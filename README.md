# Pixel X Tracking Skill

Uma Skill para agentes de desenvolvimento instalarem, auditarem e validarem o rastreamento avançado da **Pixel X App** em sites e aplicações web.

Ela orienta a instalação única do pixel no ponto global correto, o desenho de um funil de eventos confiável e a validação prática no navegador e no painel **Live Events** da Pixel X.

> Esta Skill é operacional e técnica; não substitui orientação jurídica, de privacidade ou de consentimento.

## Para que serve

Use esta Skill quando for preciso:

- instalar ou corrigir o pixel da Pixel X em HTML, React, Next.js, Vite, Vue, Nuxt, SvelteKit ou Astro;
- criar um plano de rastreamento para CTAs, formulários, páginas comerciais, carrinho e checkout;
- implementar eventos sem duplicidade entre o painel da Pixel X e JavaScript;
- tratar navegação SPA, carregamento lento do SDK, CSP, máscaras de telefone e consentimento regional;
- validar se os eventos chegam à Pixel X com o nome, conteúdo e quantidade esperados.

## Garantias e princípios

A Skill estabelece estes guardrails:

1. **Um único bootstrap:** o snippet oficial é instalado uma vez, cedo no carregamento e nunca reinjetado a cada rota SPA.
2. **Fonte única por evento:** cada evento vem do painel **ou** do código; nunca dos dois para o mesmo gatilho.
3. **Funil baseado em ação real:** conversões só são enviadas após sucesso confirmado. Um clique em botão não vira `Purchase`.
4. **Adapter seguro:** envios diretos passam por um adapter centralizado, compatível com SSR, que não quebra o fluxo caso o SDK esteja indisponível.
5. **Privacidade por padrão:** não envia credenciais, tokens, documentos, cartão, dados de saúde, texto livre, URL completa com query/fragmento ou logs de payload.
6. **Validação obrigatória:** não declara sucesso apenas pela alteração no código; valida navegador e, quando houver acesso, Live Events.

## Requisitos antes de usar

- O domínio real do projeto Pixel X (`{DOMAIN_PROJECT}`) fornecido pelo usuário.
- Acesso ao código do site e, idealmente, ao painel da Pixel X para confirmar eventos e usar Live Events.
- Definição de quais territórios recebem o site e qual mecanismo de consentimento já existe.
- Confirmação no painel/documentação atual da Pixel X antes de usar eventos que não estejam explicitamente documentados pela Skill, especialmente `PageView`, `InitiateCheckout`, `Purchase`, `Contact`, `VideoView` e `Download`.

Não invente domínio, chaves, payloads ou nomes de eventos.

## Estrutura

```text
pixel-x-tracking/
├── SKILL.md
├── README.md
└── references/
    ├── installation.md
    ├── implementation_patterns.md
    ├── eventos_meta.md
    ├── painel_conversoes.md
    ├── privacy_validation.md
    └── prompt_completo.md
```

| Arquivo | Quando consultar |
| --- | --- |
| `SKILL.md` | Contrato principal, processo, escopo e critérios de conclusão. |
| `references/installation.md` | Ponto correto de instalação por framework, CSP e consentimento. |
| `references/implementation_patterns.md` | Adapter, SPA, CTA, formulário, seções e máscara de telefone. |
| `references/eventos_meta.md` | Catálogo de eventos, gatilhos e limites de confirmação. |
| `references/painel_conversoes.md` | Eventos de visualização configurados exclusivamente no painel. |
| `references/privacy_validation.md` | Privacidade, testes e checklist de Live Events. |
| `references/prompt_completo.md` | Prompt-base para delegar a implementação a outro agente. |

## Como instalar a Skill

1. Copie ou clone esta pasta para o diretório de Skills configurado no seu agente/ambiente.
2. Garanta que `SKILL.md` e a pasta `references/` permaneçam juntos.
3. Recarregue ou reinicie o agente para que ele detecte a nova Skill.
4. Acione a Skill pelo nome `pixel-x-tracking` ou descreva uma tarefa de instalação/auditoria da Pixel X.

Se o seu ambiente não tiver descoberta automática de Skills, forneça o conteúdo de `SKILL.md` ao agente e mantenha os arquivos de `references/` acessíveis durante a execução.

## Como a implementação funciona

### 1. Diagnóstico do projeto

Antes de editar, a Skill identifica framework, roteador, entrada global, páginas comerciais, CTAs, formulários, checkout, consentimento/CMP, CSP e qualquer rastreamento existente.

### 2. Plano de eventos

O agente deve entregar uma tabela antes de instrumentar:

| ID/área | Evento | Gatilho exato | Fonte | Dados permitidos | Deduplicação |
| --- | --- | --- | --- | --- | --- |
| `pricing` | `ViewContent` | 50% visível | Painel ou JS | `content_name` estável | Uma vez por rota |
| CTA de plano | `Content` | Clique explícito | JavaScript | `content_name` | Delegação única |
| Formulário | `Lead` | API confirmou sucesso | JavaScript | campos permitidos | ID imutável da submissão |
| Pedido pago | `Purchase` | Confirmação confiável | Contrato confirmado | Confirmar antes | ID do pedido, se suportado |

### 3. Escolha da fonte

- **Painel Pixel X:** use para visualização de seção por ID. O elemento deve ter ID único, em inglês e `kebab-case`; configure um único `ViewContent` principal por página.
- **JavaScript:** use para cliques, formulário confirmado, carrinho, checkout, permanência visível e lógica dinâmica.
- **Backend:** somente se houver contrato oficial da Pixel X para isso.

Não implemente um `IntersectionObserver` em JavaScript para uma seção já configurada no painel.

### 4. Instalação global do pixel

O snippet oficial entra uma vez e de modo global:

| Plataforma | Local esperado |
| --- | --- |
| HTML / Vite | `<head>` global em `index.html` |
| Next.js App Router | `app/layout.*` com `next/script` e estratégia `beforeInteractive` |
| Next.js Pages Router | `pages/_document.*` |
| Vue / Vite | `index.html` global |
| Nuxt | API de head global da versão em uso |
| SvelteKit | `src/app.html` ou head oficial |
| Astro | Layout/head global |

Para sintaxe específica da versão do framework, consulte a documentação oficial atual antes de alterar o projeto.

### 5. Adapter e eventos diretos

Eventos JavaScript devem passar por uma função central, SSR-safe, que:

- aguarda o SDK por período limitado;
- respeita o consentimento aplicável;
- captura falhas sem impedir navegação, envio de formulário ou checkout;
- retorna sucesso/falha em booleano;
- não registra payloads ou PII nos logs.

### 6. Regras por território

- **Brasil:** a Skill não cria nem exige CMP/banner apenas para instalar esse rastreamento. Isso não é parecer jurídico.
- **UE/territórios equivalentes:** eventos não essenciais só iniciam após a autorização de uma CMP já existente e confiável.
- **Site multirregional:** use a regra regional já presente no produto; não invente geolocalização ou fluxo de revogação.

### 7. Validação

Valide, nesta ordem:

1. busca estática: somente um snippet/URL `remote`, sem placeholders de produção;
2. navegador: request do pixel, SDK disponível, navegação SPA sem reinjeção;
3. fluxo: CTA, formulário inválido/rejeitado/sucesso, clique duplo, carrinho e checkout;
4. consentimento: negar/conceder na UE, quando aplicável;
5. Live Events: evento, `content_name`, dados permitidos, quantidade e ausência de duplicidade.

Se o painel não estiver acessível, o resultado deve ficar marcado como **pendente de validação humana no Live Events**, e não como concluído.

## Eventos do funil

### Eventos já tratados pela Skill

| Evento | Uso recomendado |
| --- | --- |
| `ViewContent` | Uma visualização de conteúdo comercial primário por page view. |
| `Content` | Seções ou CTAs comerciais não equivalentes ao conteúdo principal. |
| `ScrollTo` | Ação de rolagem aprovada e configurada. |
| `AddToCart` | Item efetivamente adicionado ao carrinho. |
| `AddToWishlist` | Item efetivamente adicionado à lista de desejo. |
| `Lead` | Formulário confirmado pelo backend. |
| `TimeOnPage` | Marcos de 30/60/120s, após confirmar aceitação. |

### Eventos que exigem confirmação atual

Antes de implementar, confirme a disponibilidade, payload e semântica no painel/documentação atual da Pixel X:

`PageView`, `InitiateCheckout`, `Purchase`, `Contact`, `VideoView` e `Download`.

`Purchase` exige confirmação de pagamento ou de pedido concluído por uma fonte confiável — nunca apenas o clique em “comprar”.

## Exemplos de prompts

### Instalar em um projeto Next.js

```text
Use a Skill pixel-x-tracking para instalar a Pixel X neste projeto Next.js App Router.
O domínio Pixel X é [DOMÍNIO]. Inspecione o layout global, CSP, consentimento e eventos existentes.
Antes de editar, entregue um plano de eventos. Instale o snippet uma vez com beforeInteractive,
crie um adapter SSR-safe e valide no navegador. Não declare Live Events como validado sem acesso ao painel.
```

### Criar funil para landing page

```text
Use pixel-x-tracking para mapear o funil desta landing page.
Rastreie a seção de preços, cliques nos CTAs, envio confirmado do formulário e início do checkout.
Escolha painel ou JavaScript para cada evento sem duplicidade, use nomes de conteúdo estáveis e
entregue a tabela de rastreamento, os arquivos alterados e o checklist de validação.
```

### Auditar implementação existente

```text
Audite o rastreamento Pixel X existente com a Skill pixel-x-tracking.
Verifique bootstrap duplicado, reinjeção em SPA, eventos duplicados entre painel e código,
formulários que disparam Lead antes do sucesso, PII, CSP e fluxo de consentimento para Brasil e UE.
Corrija apenas problemas confirmados e liste o que depende de validação no Live Events.
```

### Implementar e-commerce com segurança

```text
Use pixel-x-tracking neste e-commerce. O domínio é [DOMÍNIO].
Mapeie ViewContent, AddToCart, AddToWishlist, Lead e o fluxo de checkout.
Não implemente InitiateCheckout ou Purchase sem confirmar o contrato atual da Pixel X.
Purchase só pode ser disparado depois da confirmação confiável de pagamento/pedido.
```

## Problemas comuns

| Sintoma | Verificação |
| --- | --- |
| Evento duplicado | Procure a mesma seção no painel e em observers/listeners JavaScript. Mantenha uma fonte. |
| Pixel não carrega | Confirme domínio, CSP (`script-src`), posição global e request `remote` no navegador. |
| Formulário trava se o SDK falha | Garanta timeout limitado e que a navegação/sucesso não dependa do retorno do SDK. |
| Eventos extras em SPA | Não reinjete o snippet; reinicie apenas o estado de deduplicação por page view. |
| Dados sensíveis aparecem | Remova payload/logs; envie apenas campos autorizados e necessários. |

## Limites conhecidos

- A Skill não substitui a documentação oficial ou o painel da Pixel X para contratos de eventos não confirmados.
- A Skill não decide requisitos jurídicos de consentimento, retenção ou base legal.
- O Live Events pode exigir validação humana quando não houver acesso autenticado ao painel.

## Avaliação da Skill

A Skill está dentro de um padrão sólido para agentes: possui frontmatter válido, escopo específico, instruções progressivas, referências separadas por assunto, critérios de conclusão e mecanismos explícitos de privacidade, deduplicação e validação. A descrição inicial é detalhada, mas adequadamente restritiva para reduzir implementações inseguras.

As principais dependências externas são a confirmação do contrato atual de eventos na Pixel X e a validação final no painel. Essas limitações são deliberadas para evitar que o agente invente eventos, campos ou um falso sucesso de rastreamento.
