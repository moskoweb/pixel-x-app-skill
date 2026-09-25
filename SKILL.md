---
name: pixel-x-tracking
description: Instala e implementa exclusivamente o rastreamento Pixel X App. Analisa as páginas solicitadas, propõe funis de marketing e marketplace, mapeia eventos padrão Meta e personalizados Pixel X e valida a instrumentação via script Pixel X. Use para instalar, planejar ou auditar Pixel X; não para integrar pixels de terceiros diretamente nem analisar dados internos de contas.
---

# Pixel X App — Advanced Paid Traffic Tracking

Analisar as páginas solicitadas sob uma visão comercial de marketing/marketplace e instrumentar os pontos-chave do funil exclusivamente pela Pixel X App.

## Limites e contratos

- Usar somente o bootstrap oficial Pixel X e `window.pixel_x_app.send_event` para envio no site.
- A nomenclatura padrão Meta é uma taxonomia, não autorização para instalar Meta Pixel, Facebook SDK/CAPI, Google Analytics/Ads, GTM, gtag ou qualquer outro rastreador.
- Não alterar integrações de terceiros existentes fora do escopo. Se interferirem, reportar a sobreposição e propor uma mudança específica.
- Não confundir análise técnica/comercial das páginas com análise dos dados internos da conta por MCP. Esta skill cobre implementação; não requer MCP.
- Utilizar os nomes confirmados pelo responsável da Pixel X em [eventos_meta.md](references/eventos_meta.md). Eles não dependem de nova confirmação de nomenclatura, mas campos adicionais e garantias do SDK exigem documentação.
- Não inventar domínio, campos de receita/produto, REST, retries ou deduplicação do servidor.
- Preservar funcionalidades, estilos, IDs públicos, submissões, navegação e acessibilidade.
- Não implementar todos os eventos em todas as páginas: selecionar apenas gatilhos reais e relevantes.

## Fluxo

### 1. Descobrir o escopo

Identificar páginas/rotas pedidas, framework e versão, router, ponto global de instalação e tracking existente. Inspecionar oferta, audiência, CTAs, seções, formulários, vídeos, busca, checkout e confirmações disponíveis. Ler documentação atual do framework quando precisar de sintaxe específica.

Descobrir o domínio Pixel X no snippet existente ou solicitá-lo; não usar automaticamente o domínio do site. Continuar o plano quando o domínio estiver pendente, sem declarar instalação concluída.

Identificar restrições de pixel por página: sem seleção, usar o padrão de todos os pixels do painel; com seleção, configurar `pixels` no PageView (`'456'` ou `'123,456'`). IDs de pixel não são o domínio do projeto. Ver detalhes de instalação antes de implementar.

### 2. Analisar e propor o funil

Ler [funis.md](references/funis.md) e [eventos_meta.md](references/eventos_meta.md). Escolher ou combinar os modelos conforme o negócio real. Tratar os modelos como propostas revisáveis, não jornadas obrigatoriamente lineares.

Entregar o plano antes da implementação:
| Página/rota | Etapa | Elemento/ID | Evento e tipo | Gatilho de sucesso | content_name | Origem | Contagem | Status |
|---|---|---|---|---|---|---|---|---|
| / | Consideração | offer | ViewContent / padrão | Oferta visível | offer | JavaScript | uma vez/pageview | proposto |

Registrar também o destino de pixels de cada página/rota: padrão (todos) ou IDs específicos, herdado pelos eventos da página.

Explicar a decisão comercial que cada evento permite medir. Identificar etapas fora do domínio, sem confirmação ou sem acesso. Diferenciar proposto, implementado, testado localmente, verificado na Pixel X e pendente.
Se o pedido for somente análise/sugestão, parar no plano; não editar o site.
Se pedir implementação, executar as partes claras e perguntar somente sobre ambiguidade comercial relevante.

### 3. Instalar e escolher uma fonte por evento

Ler [installation.md](references/installation.md).
Instalar uma única vez no entrypoint global, respeitando a política de privacidade aplicável. Na SPA, não reinstalar por rota.

Para visualização de seção:
- Painel: adicionar IDs e entregar instruções de conversão; não implementar observer equivalente.
- JavaScript: observer chama o adaptador; não criar conversão equivalente no painel.
Ler [painel_conversoes.md](references/painel_conversoes.md) quando escolher painel.
Não inferir deduplicação automática entre fontes.

### 4. Implementar

Ler [implementation_patterns.md](references/implementation_patterns.md).
Centralizar envio, espera limitada pelo SDK, erros, cancelamento e permissão de tracking.
- PageView inicial: verificar o que o bootstrap envia antes de adicionar outro; quando houver seleção de pixels, configurar o PageView conforme installation.md, coordenando ordem e duplicidade.
- SPA: mudança real de rota cria novo pageview lógico e reinicia seções/timers.
- ViewContent: escolher explicitamente a oferta principal; uma vez por pageview como convenção Pixel X, não alegar limitação universal Meta.
- Content: seções secundárias; pular Hero por padrão e evitar contagem dupla da principal.
- CTAs: anotação explícita; não mapear qualquer botão para AddToWishlist.
- Lead/cadastro/agendamento: evento depois do sucesso real, preservando a operação original.
- Purchase/Refund/SubscribeCanceled: confirmação confiável da operação, não URL ou clique.
- WatchVideo: medir tempo realmente reproduzido; não usar simples permanência na página.
- AdsClick: apenas anúncio efetivamente clicado e observável no site, não todo CTA.
- Máscaras: classe no pai, input text com inputmode tel; inicializar após montagem do popup.
Só enviar dados de lead necessários e permitidos; não capturar senha, cartão, documento ou texto livre.

### 5. Validar e entregar

Ler [privacy_validation.md](references/privacy_validation.md). Executar testes locais existentes e cenários positivos/negativos por evento. Não emitir transações reais para testar.

Entregar:
- Arquivos e domínio configurado.
- Plano final e modelo de funil escolhido.
- Tabela seção → ID → evento → origem.
- Eventos não implementados e motivo.
- Evidências locais separadas de confirmação no painel.
- Pendências de domínio, CMP, página externa, confirmação de negócio ou acesso ao painel.

Não declarar aprovação em Eventos ao vivo sem acesso e evidência. Instalar a skill não instala o pixel em nenhum site.

## Recursos sob demanda

- [eventos_meta.md](references/eventos_meta.md): catálogo completo confirmado pelo produto.
- [funis.md](references/funis.md): modelos revisáveis e métricas.
- [installation.md](references/installation.md): bootstrap e decisões por stack.
- [implementation_patterns.md](references/implementation_patterns.md): regras técnicas de instrumentação.
- [painel_conversoes.md](references/painel_conversoes.md): alternativa de configuração no painel.
- [privacy_validation.md](references/privacy_validation.md): privacidade e aceitação.
- [prompt_completo.md](references/prompt_completo.md): somente para delegar a outro construtor.
