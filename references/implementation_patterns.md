# Padrões de Implementação

## Índice

- Adaptador seguro
- Política territorial
- Formulário e redirect
- CTAs explícitos
- Seções por JavaScript
- Tempo visível
- Rotas SPA
- Máscaras de telefone

Adaptar os exemplos ao stack existente. Não copiar tipos ou infraestrutura que conflitem com padrões do projeto.

## Adaptador seguro

Centralizar as chamadas e limitar a espera pelo SDK:

```ts
type PixelXPayload = {
  event_name: string;
  content_name?: string;
  lead_name?: string;
  lead_email?: string;
  lead_phone?: string;
};

type PixelXSDK = {
  send_event(payload: PixelXPayload): unknown | Promise<unknown>;
  mask_load?(): unknown | Promise<unknown>;
  mask_load_inter?(): unknown | Promise<unknown>;
};

declare global {
  interface Window {
    pixel_x_app?: PixelXSDK;
  }
}

const SDK_TIMEOUT_MS = 4_000;
const SDK_POLL_MS = 50;

function waitForPixelX(timeoutMs = SDK_TIMEOUT_MS): Promise<PixelXSDK> {
  if (typeof window === 'undefined') {
    return Promise.reject(new Error('Pixel X is client-only'));
  }

  if (window.pixel_x_app?.send_event) {
    return Promise.resolve(window.pixel_x_app);
  }

  return new Promise((resolve, reject) => {
    const startedAt = Date.now();
    const timer = window.setInterval(() => {
      if (window.pixel_x_app?.send_event) {
        window.clearInterval(timer);
        resolve(window.pixel_x_app);
        return;
      }

      if (Date.now() - startedAt >= timeoutMs) {
        window.clearInterval(timer);
        reject(new Error('Pixel X SDK timeout'));
      }
    }, SDK_POLL_MS);
  });
}

export async function trackPixelX(
  payload: PixelXPayload,
  isTrackingAllowed: () => boolean,
): Promise<boolean> {
  if (typeof window === 'undefined' || !isTrackingAllowed()) return false;

  try {
    const sdk = await waitForPixelX();
    await Promise.resolve(sdk.send_event(payload));
    return true;
  } catch (error) {
    if (process.env.NODE_ENV === 'development') {
      console.warn('[Pixel X] event not sent', {
        event_name: payload.event_name,
        error,
      });
    }
    return false;
  }
}
```

Não registrar o payload inteiro porque ele pode conter PII. Se o projeto não expuser `process.env.NODE_ENV`, usar o mecanismo de ambiente local.

## Política territorial

Usar `isTrackingAllowed` conforme o modo do projeto:

- **Brasil — padrão:** retornar `true`; não criar banner ou CMP apenas para satisfazer a integração.
- **União Europeia:** consultar a CMP/store real e retornar `true` somente após autorização aplicável.
- **Regional:** usar o sinal territorial confiável já fornecido pelo servidor, CDN ou CMP.

Exemplo Brasil:

```ts
const isPixelXTrackingAllowed = () => true;
```

Exemplo conceitual União Europeia — adaptar à CMP instalada:

```ts
const isPixelXTrackingAllowed = () => consentStore.allows('analytics');
```

Não inventar APIs de CMP nem instalar um banner artesanal automaticamente.

No modo União Europeia, se o consentimento for revogado:

- Parar novos eventos.
- Cancelar timers e observers ligados ao tracking quando aplicável.
- Seguir o processo oficial da Pixel X para revogação/remoção de cookies, se houver documentação disponível.

## Formulário e redirect

Disparar após a operação de negócio confirmar sucesso:

```ts
const sentSubmissions = new Set<string>();

async function handleSuccessfulLead(
  submissionId: string,
  lead: { name?: string; email?: string; phone?: string },
  navigate: () => void,
) {
  if (sentSubmissions.has(submissionId)) {
    navigate();
    return;
  }
  sentSubmissions.add(submissionId);

  try {
    await Promise.race([
      trackPixelX(
        {
          event_name: 'Lead',
          lead_name: lead.name,
          lead_email: lead.email,
          lead_phone: lead.phone,
        },
        isPixelXTrackingAllowed,
      ),
      new Promise((resolve) => window.setTimeout(resolve, 1_200)),
    ]);
  } finally {
    navigate();
  }
}
```

Usar o ID imutável da submissão retornado pelo backend quando disponível. Não gerar deduplicação usando e-mail ou telefone.

## CTAs explícitos

Preferir atributos declarativos:

```html
<a
  href="/checkout"
  data-pxa-event="AddToCart"
  data-pxa-content="Plano Pro"
>
  Comprar Plano Pro
</a>
```

Um listener delegado pode ler apenas elementos anotados:

```ts
function onTrackedClick(event: MouseEvent) {
  const element = (event.target as Element | null)?.closest<HTMLElement>(
    '[data-pxa-event][data-pxa-content]',
  );
  if (!element) return;

  void trackPixelX(
    {
      event_name: element.dataset.pxaEvent!,
      content_name: element.dataset.pxaContent!,
    },
    isPixelXTrackingAllowed,
  );
}
```

Adicionar e remover o listener no ciclo de vida do componente. Não anotar controles de interface sem valor comercial.

## Seções por JavaScript

Usar somente quando o plano definir origem JavaScript:

```ts
const primarySectionId = 'offer';
const excludedSectionIds = new Set(['hero', 'footer']);
const viewed = new Set<string>();

const observer = new IntersectionObserver(
  (entries) => {
    for (const entry of entries) {
      const id = (entry.target as HTMLElement).id;
      if (!entry.isIntersecting || !id || viewed.has(id)) continue;
      viewed.add(id);
      observer.unobserve(entry.target);

      void trackPixelX(
        {
          event_name: id === primarySectionId ? 'ViewContent' : 'Content',
          content_name: id,
        },
        isPixelXTrackingAllowed,
      );
    }
  },
  { threshold: 0.5 },
);

document.querySelectorAll<HTMLElement>('section[id]').forEach((section) => {
  if (!excludedSectionIds.has(section.id)) observer.observe(section);
});
```

Criar `viewed` novamente a cada pageview SPA. Não configurar os mesmos elementos no painel.

## Tempo visível

Contar tempo somente com `document.visibilityState === 'visible'`. Uma estratégia robusta usa um intervalo de 1 segundo, acumula tempo visível e dispara cada marco uma vez. Limpar o intervalo e o conjunto de marcos em mudança de rota.

Marcos padrão: 30, 60 e 120 segundos. Usar `TimeOnPage` apenas se confirmado na conta/documentação Pixel X.

## Rotas SPA

Reagir à API oficial do router instalado, não sobrescrever `history.pushState` se o framework expuser eventos ou hooks próprios.

Regras:

1. Guardar a rota inicial e não emitir um `PageView` manual para ela sem verificar o evento automático do snippet.
2. Normalizar a rota removendo fragment e parâmetros sensíveis.
3. Em mudança real de rota, cancelar observers/timers antigos e criar novo estado de pageview.
4. Emitir `PageView` somente se confirmado como evento suportado e necessário.
5. Não reinjetar o bootstrap remoto em cada rota.

## Máscaras de telefone

```html
<div class="pxa_mask_phone">
  <label for="phone">Telefone</label>
  <input
    id="phone"
    name="phone"
    type="text"
    inputmode="tel"
    autocomplete="tel"
  />
</div>
```

Depois que um popup montar o campo:

```ts
async function loadPhoneMask(international = false): Promise<boolean> {
  try {
    const sdk = await waitForPixelX();
    const load = international ? sdk.mask_load_inter : sdk.mask_load;
    if (!load) return false;
    await Promise.resolve(load.call(sdk));
    return true;
  } catch {
    return false;
  }
}
```

Não chamar a máscara antes que o elemento esteja no DOM.
