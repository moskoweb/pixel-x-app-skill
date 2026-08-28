---
name: pixel-x-tracking
description: Implementa, audita e corrige o rastreamento completo da Pixel X App em sites e SPAs criados com React, Next.js, Vite, HTML, Vue, Svelte, Astro e construtores de vibe coding. Use quando o usuário pedir para instalar o Super Pixel, integrar window.pixel_x_app, mapear comportamento e navegação, rastrear páginas, rotas, seções, CTAs, formulários, leads, checkout, compras, tempo na página ou máscaras de telefone; criar conversões por seletor no painel; diagnosticar eventos ausentes ou duplicados; ou revisar uma implementação Pixel X existente. Use somente para a Pixel X App, não para pixels ou ferramentas de analytics genéricas.
---

# Pixel X Tracking

Implementar rastreamento confiável, sem duplicidade e com um plano de eventos verificável. Preservar o comportamento existente do site e nunca inventar contratos da Pixel X.

## Contrato obrigatório

1. Usar somente o snippet oficial abaixo. Substituir `{DOMAIN_PROJECT}` pelo domínio de projeto fornecido pelo usuário ou já presente no snippet do painel. Nunca adivinhar esse valor.
2. Não alterar a URL, os parâmetros ou a ordem interna do snippet, salvo documentação oficial posterior.
3. Nunca declarar sucesso apenas porque o código compila. Validar no navegador e em **Eventos ao vivo** da Pixel X.
4. Definir uma única fonte para cada evento. Não rastrear a mesma visualização simultaneamente pelo painel e por JavaScript.
5. Adotar **Brasil sem banner obrigatório** como padrão operacional: instalar o bootstrap e enviar eventos sem criar, exigir ou bloquear a implementação por um banner/CMP. Aplicar bloqueio por consentimento somente para sites ou visitantes sujeitos à União Europeia, ou quando o projeto/usuário exigir expressamente outra política.
6. Não capturar senha, documento, cartão, mensagem livre ou qualquer dado sensível. Enviar nome, e-mail e telefone somente quando necessários ao evento `Lead`, permitidos pela política do projeto e documentados no plano.
7. Não usar listeners genéricos para transformar todo botão em conversão. Mapear ações comerciais explicitamente.
8. Não inventar campos, endpoints REST, eventos ou garantias de deduplicação. Quando o contrato não estiver documentado, limitar-se a `event_name`, `content_name`, `lead_name`, `lead_email` e `lead_phone` ou pedir a fonte oficial.

## Snippet oficial

```html
<!-- Pixel X App - START -->
<script type='text/javascript'>
!function(){var e=window.location.href,t=document.title,n=Date.now(),o=document.createElement('script');o.src='https://{DOMAIN_PROJECT}/remote?url='+encodeURIComponent(e)+'&title='+encodeURIComponent(t)+'&time='+n,o.async=!0,document.head.appendChild(o)}()
</script>
<!-- Pixel X App - END -->
```

Consultar [references/installation.md](references/installation.md) para instalação por framework. Para Next.js, usar as APIs atuais do framework; não injetar o snippet tardiamente por `useEffect`.

## Recursos da skill

- Ler [references/installation.md](references/installation.md) ao instalar ou auditar o snippet.
- Ler [references/implementation_patterns.md](references/implementation_patterns.md) ao implementar chamadas, formulários, CTAs, scroll, tempo, máscaras ou rotas SPA.
- Ler [references/eventos_meta.md](references/eventos_meta.md) ao criar a taxonomia e escolher eventos.
- Ler [references/painel_conversoes.md](references/painel_conversoes.md) ao usar conversões configuradas pelo painel.
- Ler [references/privacy_validation.md](references/privacy_validation.md) antes de implementar coleta de dados e durante a validação final.
- Ler [references/prompt_completo.md](references/prompt_completo.md) somente quando o usuário pedir um prompt para outro agente/construtor em vez de implementação direta.

## Fluxo obrigatório

### 1. Inspecionar o projeto

Descobrir sem perguntar o que estiver disponível no repositório:

- Framework, versão, router e renderização SSR/CSR.
- Entrypoint global e local correto para o snippet.
- Rotas públicas e comportamento de navegação SPA.
- Formulários, popups, CTAs, links externos, checkout e página de confirmação.
- Seções relevantes e IDs existentes.
- Países atendidos, presença de visitantes da União Europeia, CMP existente e política de privacidade.
- Implementações Pixel X existentes, incluindo snippets duplicados e chamadas diretas.

Usar `rg` para localizar `pixel_x_app`, `DOMAIN_PROJECT`, `/remote?url=`, `<head>`, layouts, routers, formulários e CTAs. Não remover integrações existentes sem entender sua função.

Interromper apenas a etapa de instalação se `{DOMAIN_PROJECT}` não puder ser descoberto. Continuar produzindo o plano e as demais alterações que não dependam dele, deixando um placeholder claramente reportado.

### 2. Criar o plano de tracking

Antes de editar, produzir internamente uma tabela como esta e usá-la como contrato:

| ID | Evento | Gatilho exato | Origem | Conteúdo/dados | Escopo | Política territorial | Deduplicação |
|---|---|---|---|---|---|---|---|
| `lead-contact` | `Lead` | Resposta de sucesso do formulário | JavaScript | nome, e-mail, telefone | por sucesso | Brasil direto / UE consentimento | uma vez por submissão |
| `offer-view` | `ViewContent` | 50% da seção principal visível | painel **ou** JavaScript | `offer` | uma vez por pageview | Brasil direto / UE consentimento | fonte única |
| `checkout-primary` | `AddToCart` | CTA identificado | JavaScript | `Plano Pro` | por clique válido | Brasil direto / UE consentimento | uma vez por interação |

Definir explicitamente:

- A ação de negócio que cada evento mede.
- A seção principal que recebe o único `ViewContent` da página.
- Seções, controles de interface e links que não serão rastreados.
- Se a origem de visualização será **painel** ou **JavaScript**.
- Se o bootstrap já gera `PageView`; não adicionar outro no carregamento sem verificar.

Apresentar o plano ao usuário no relatório final. Para mudanças de alto impacto ou ambiguidade comercial, pedir confirmação antes de classificar ações como compra, lead ou checkout.

### 3. Escolher a fonte dos eventos de seção

Escolher exatamente um modo por evento:

#### Modo painel — padrão para seções

- Adicionar IDs únicos, em inglês, kebab-case e sem `#` no atributo HTML.
- Configurar a conversão no painel usando o valor do ID sem `#`, conforme [references/painel_conversoes.md](references/painel_conversoes.md).
- Não criar `IntersectionObserver` para os mesmos IDs/eventos.

#### Modo JavaScript

- Usar quando o usuário pedir implementação em código, o painel não suportar o gatilho ou for necessário controle adicional.
- Usar `IntersectionObserver`, disparar uma vez por pageview e desconectar os elementos concluídos.
- Não criar no painel uma conversão equivalente para a mesma visualização.
- Configurar explicitamente `primarySectionId`; nunca inferir usando um nome fixo como `oferta`.

### 4. Instalar uma única vez

Instalar o snippet oficial no ponto global mais antecipado suportado pelo framework. Preservar `{DOMAIN_PROJECT}` até receber o valor real. Antes de adicionar, procurar uma instalação existente por `/remote?url=` para evitar duplicidade.

Aplicar a política territorial:

- **Brasil — padrão:** instalar e executar o snippet imediatamente, sem criar ou exigir banner/CMP. Não bloquear a entrega por ausência de consentimento. Ainda preservar transparência, minimização de dados e a política de privacidade do site.
- **União Europeia:** para rastreamento não estritamente necessário, não executar o bootstrap nem enviar eventos antes da autorização fornecida pela CMP. Preferir renderização condicional server-side quando o estado estiver disponível no request; caso contrário, usar o callback oficial da CMP para executar o snippet uma única vez.
- **Brasil + União Europeia:** aplicar comportamento por região quando o projeto já possuir geolocalização/CMP regional confiável. Se isso não existir, reportar a necessidade de decisão do responsável; não inventar geolocalização ou instalar um banner genérico silenciosamente.
- **Outras jurisdições:** seguir a política expressa do projeto. Na ausência de indicação, manter o padrão Brasil solicitado pela Pixel X.

Regras:

- HTML/Vite: `<head>` do documento global.
- Next.js App Router: `next/script` com `strategy="beforeInteractive"` no root layout.
- Next.js Pages Router: `next/script` com `strategy="beforeInteractive"` em `pages/_document`.
- Nunca usar `useEffect` como instalação principal do snippet.
- Respeitar nonce/CSP do projeto. Se a política bloquear script inline ou o domínio remoto, reportar o bloqueio em vez de enfraquecer a CSP silenciosamente.

### 5. Centralizar o envio

Criar um único adaptador `trackPixelX` ou equivalente. Proibir chamadas novas espalhadas diretamente a `window.pixel_x_app.send_event`.

O adaptador deve:

- Ser seguro em SSR.
- Aguardar a disponibilidade do SDK por tempo limitado.
- Usar `Promise.resolve` porque `send_event` pode ser síncrono ou assíncrono.
- Capturar falhas sem quebrar o fluxo do usuário.
- Aplicar a política territorial: permitir diretamente no modo Brasil e consultar a CMP no modo União Europeia.
- Retornar sucesso/falha para testes.
- Não registrar PII no console.

Usar a implementação de referência em [references/implementation_patterns.md](references/implementation_patterns.md), adaptando ao stack e à política do projeto.

### 6. Instrumentar comportamento

#### Navegação e pageviews

- Verificar primeiro se o snippet gera o pageview inicial.
- Em SPA, rastrear somente mudanças reais de rota após a rota inicial.
- Remover query strings sensíveis e fragments antes de compor `content_name`.
- Usar `PageView` em mudanças de rota somente após confirmar que esse evento é aceito/esperado na conta Pixel X. Caso contrário, registrar a lacuna e solicitar o evento oficial; não inventar alternativa silenciosa.
- Reiniciar estado por pageview: seções vistas e timers.

#### Seções

- Pular Hero por padrão, salvo objetivo explícito.
- Usar um único `ViewContent` por pageview.
- Usar `Content` ou `ScrollTo` para demais seções conforme o plano.
- Usar ID real da seção como `content_name`, ou um nome legível estável definido no plano.

#### CTAs e links

- Preferir `data-pxa-event` e `data-pxa-content`, ou handlers explícitos.
- Usar `AddToCart` apenas quando a ação realmente leva um item/oferta ao checkout.
- Usar `InitiateCheckout` quando o fluxo de checkout começa, se aplicável.
- Não usar `AddToWishlist` como fallback para qualquer botão. Usá-lo somente quando a ação representar intenção comercial aprovada no plano.
- Excluir menu, FAQ, modal, carousel, cookies, acessibilidade, submit técnico e navegação por âncora.

#### Formulários

- Disparar `Lead` somente após validação e confirmação de sucesso, não no simples clique em submit.
- Garantir `name` estável; adicionar `id` quando necessário para acessibilidade ou integração de máscara.
- Usar valores do estado/FormData, não buscar indiscriminadamente o DOM.
- Em redirect, aguardar o tracking por um prazo curto e sempre navegar em `finally`.
- Impedir eventos duplicados por double-click/retry.

#### Tempo na página

- Iniciar e limpar timers por pageview.
- Disparar 30, 60 e 120 segundos apenas enquanto a página estiver visível, salvo decisão diferente no plano.
- Reiniciar em mudança de rota SPA.

#### Telefone

- Aplicar `pxa_mask_phone` ou `pxa_mask_phone_inter` no elemento pai.
- Usar `type="text"`, `inputmode="tel"`, `autocomplete="tel"`, `id` e `name`.
- Em conteúdo dinâmico/popup, chamar `mask_load()` ou `mask_load_inter()` depois que o campo estiver montado.
- Passar a chamada pelo mesmo mecanismo de prontidão do SDK.

#### Compras

- Rastrear `Purchase` apenas após confirmação confiável do pagamento.
- Preferir confirmação server-side quando houver contrato oficial da Pixel X para isso. Não confiar apenas na visita a `/obrigado` se a URL puder ser aberta diretamente.
- Não inventar campos de produto. Usar somente os confirmados pela documentação oficial fornecida ao agente.

### 7. Validar

Executar todos os testes aplicáveis de [references/privacy_validation.md](references/privacy_validation.md):

1. Compilação, lint e testes do projeto.
2. Uma única ocorrência do snippet no HTML inicial.
3. Nenhuma exceção quando o SDK está lento ou bloqueado.
4. No modo Brasil, bootstrap e eventos funcionam sem dependência de banner; no modo União Europeia, permanecem bloqueados até a CMP autorizar.
5. Um evento por gatilho e nenhuma duplicidade painel/JavaScript.
6. Payload e `content_name` corretos.
7. Navegação SPA reinicia timers e estado.
8. Formulário envia somente após sucesso e redireciona mesmo se tracking falhar.
9. Responsividade e controles não comerciais preservados.
10. Confirmação em **Eventos ao vivo** da Pixel X. Se o agente não tiver acesso, entregar passos exatos e marcar como pendente, nunca como aprovado.

### 8. Entregar relatório

Informar:

- Arquivos modificados.
- Domínio instalado ou placeholder pendente.
- Plano final de eventos.
- Tabela `Seção → ID HTML → seletor do painel → evento → origem`.
- Modo territorial adotado (`Brasil`, `União Europeia` ou regional) e integração com CMP, se aplicável.
- Evidência dos testes executados.
- Itens que exigem painel, credencial ou confirmação humana.

## Critérios de conclusão

Considerar pronta somente quando:

- Há exatamente uma instalação global do snippet.
- Cada evento tem gatilho, origem e escopo documentados.
- Não existe sobreposição painel/JavaScript.
- CTAs são explícitos; controles de interface não viram conversões.
- O adaptador lida com carregamento lento, SSR, falha e política territorial Brasil/União Europeia.
- Rotas SPA, timers e seções reiniciam corretamente.
- Lead ocorre após sucesso e Purchase após confirmação confiável.
- Não há PII indevida.
- Os testes locais passaram e a validação no painel foi concluída ou claramente deixada como pendência humana.
