# Instalação do Super Pixel

## Índice

- Contrato do snippet
- HTML, Vite e documentos estáticos
- Next.js App Router
- Next.js Pages Router
- Outros frameworks
- CSP e diagnóstico

## Contrato do snippet

Usar exatamente o snippet fornecido pela Pixel X e substituir apenas `{DOMAIN_PROJECT}` pelo domínio real do projeto:

```html
<!-- Pixel X App - START -->
<script type='text/javascript'>
!function(){var e=window.location.href,t=document.title,n=Date.now(),o=document.createElement('script');o.src='https://{DOMAIN_PROJECT}/remote?url='+encodeURIComponent(e)+'&title='+encodeURIComponent(t)+'&time='+n,o.async=!0,document.head.appendChild(o)}()
</script>
<!-- Pixel X App - END -->
```

Não presumir que `{DOMAIN_PROJECT}` é o domínio público do site. Usar o valor exibido no snippet do painel Pixel X ou fornecido explicitamente pelo usuário.

O snippet captura URL e título no momento da execução e carrega o script remoto de forma assíncrona. Por isso:

- Inserir o bootstrap cedo e uma única vez.
- Não supor que `window.pixel_x_app` estará disponível imediatamente.
- Usar o adaptador com espera limitada para eventos programáticos.
- Não adicionar manualmente um segundo pageview inicial sem verificar **Eventos ao vivo**.

O bootstrap em si transmite URL e título ao domínio Pixel X. Aplicar o modo territorial definido abaixo.

## Política territorial e momento de execução

### Brasil — padrão

Executar o snippet no carregamento inicial, sem exigir ou criar banner de consentimento. A ausência de CMP não bloqueia a implementação Pixel X em sites destinados ao Brasil.

Manter mesmo assim:

- Política de privacidade e transparência sob responsabilidade do site.
- Coleta mínima necessária.
- Proibição de dados sensíveis e PII indevida.
- Registro do modo `Brasil` no relatório final.

Não afirmar que a ausência de banner resolve todas as obrigações legais do controlador; esta é uma configuração operacional da skill, não parecer jurídico.

### União Europeia

Quando o site ou visitante estiver sujeito às regras da União Europeia e o rastreamento não for estritamente necessário:

1. Não colocar um bootstrap executável incondicionalmente no HTML inicial.
2. Quando o consentimento estiver disponível no servidor, renderizar o `Script`/snippet condicionalmente.
3. Quando estiver disponível apenas no cliente, registrar uma única função de instalação no callback oficial da CMP.
4. Proteger a função com uma flag global ou consulta ao DOM para impedir inserção duplicada.
5. Ao revogar consentimento, seguir a documentação oficial Pixel X/CMP para cookies e storage; não inventar limpeza parcial.

Não usar polling do banner, clique genérico ou texto do botão para inferir consentimento.

### Site misto Brasil + União Europeia

Usar segmentação territorial somente quando o projeto já fornecer região confiável no servidor, CDN ou CMP. Não consultar serviços externos de IP por conta própria. Se não houver mecanismo regional, registrar a lacuna e solicitar a decisão do responsável pelo site.

## HTML, Vite e documentos estáticos

Inserir dentro do `<head>` global, antes de scripts não essenciais:

```html
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>...</title>

  <!-- Pixel X App - START -->
  <script type='text/javascript'>
  !function(){var e=window.location.href,t=document.title,n=Date.now(),o=document.createElement('script');o.src='https://pixel.example/remote?url='+encodeURIComponent(e)+'&title='+encodeURIComponent(t)+'&time='+n,o.async=!0,document.head.appendChild(o)}()
  </script>
  <!-- Pixel X App - END -->
</head>
```

`pixel.example` é apenas ilustrativo. Substituir pelo valor real, não copiar o exemplo.

Em Vite, o `index.html` é o documento global e não costuma ser sobrescrito pela SPA. Não transferir o snippet para `useEffect`.

## Next.js App Router

Usar `next/script` com `beforeInteractive` no root layout. Manter o conteúdo do bootstrap idêntico, alterando apenas o domínio:

```tsx
import Script from 'next/script';

const pixelXBootstrap = String.raw`!function(){var e=window.location.href,t=document.title,n=Date.now(),o=document.createElement('script');o.src='https://pixel.example/remote?url='+encodeURIComponent(e)+'&title='+encodeURIComponent(t)+'&time='+n,o.async=!0,document.head.appendChild(o)}()`;

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="pt-BR">
      <body>
        {children}
        <Script id="pixel-x-bootstrap" strategy="beforeInteractive">
          {pixelXBootstrap}
        </Script>
      </body>
    </html>
  );
}
```

O Next.js injeta scripts `beforeInteractive` no `<head>` do HTML inicial independentemente da posição visual dentro do root layout. Não usar `onLoad` ou `onError` com essa estratégia.

Se o projeto já possuir um componente aprovado para scripts de terceiros, seguir o padrão local desde que preserve `beforeInteractive` e a instalação global.

## Next.js Pages Router

Usar `next/script` em `pages/_document.tsx`:

```tsx
import { Head, Html, Main, NextScript } from 'next/document';
import Script from 'next/script';

const pixelXBootstrap = String.raw`!function(){var e=window.location.href,t=document.title,n=Date.now(),o=document.createElement('script');o.src='https://pixel.example/remote?url='+encodeURIComponent(e)+'&title='+encodeURIComponent(t)+'&time='+n,o.async=!0,document.head.appendChild(o)}()`;

export default function Document() {
  return (
    <Html lang="pt-BR">
      <Head />
      <body>
        <Main />
        <NextScript />
        <Script id="pixel-x-bootstrap" strategy="beforeInteractive">
          {pixelXBootstrap}
        </Script>
      </body>
    </Html>
  );
}
```

## Outros frameworks

- Vue/Vite: usar o `index.html` global.
- Nuxt: usar a API de head/script compatível com a versão instalada e garantir execução inicial no cliente.
- SvelteKit: usar o template global (`src/app.html`) ou API oficial de head do framework, evitando executar o bootstrap em SSR.
- Astro: inserir no layout global dentro de `<head>`, como script inline não processado quando necessário.

Consultar a documentação atual da versão do framework antes de aplicar sintaxe específica.

## CSP e diagnóstico

O snippet exige:

- Permissão para script inline ou nonce/hash compatível.
- `script-src` permitindo `https://{DOMAIN_PROJECT}`.
- Conectividade com o endpoint `/remote` e com os recursos que ele carregar.

Não adicionar `unsafe-inline` nem ampliar `script-src *` silenciosamente. Preferir o mecanismo de nonce/hash já adotado pelo projeto e solicitar orientação se o snippet oficial não for compatível.

Verificar no navegador:

1. O bootstrap aparece uma vez no HTML inicial.
2. Há uma única requisição para `https://{DOMAIN_PROJECT}/remote?...` no carregamento inicial.
3. Não há erro de CSP, DNS, certificado ou conteúdo misto.
4. `window.pixel_x_app` fica disponível após o carregamento remoto.
5. Uma navegação SPA não reinsere o bootstrap por acidente.
