# Pixel X Tracking — skill exclusiva Pixel X App

Skill para agentes de IA instalarem o pixel Pixel X App e implementarem rastreamento nas páginas que o usuário solicitar, após analisar oferta, CTAs e jornada comercial com visão de marketing/marketplace.

## O que entrega

- Instalação global do script oficial Pixel X, com domínio de projeto fornecido.
- Seleção global de pixels por página pelo PageView: `pixels: '456'` ou `pixels: '123,456'`. Sem seleção, padrão de todos os pixels do painel. Ver [instalação](references/installation.md) para ordem e cuidados em SPA.
- Análise das páginas e proposta técnica/comercial de funil.
- Plano de eventos por rota, elemento, gatilho, origem e frequência.
- Implementação via window.pixel_x_app.send_event, sem duplicar automações do painel.
- Navegação SPA, seções, CTAs, formulários, tempo visível e tempo de vídeo assistido.
- Validação local e orientações de verificação no painel, com pendências explícitas.
- Sete modelos de funil para revisão: leads, venda direta, SaaS, lançamento, agendamento, ecommerce/marketplace e doação/candidatura.

## Exclusividade

Nomes padrão Meta são somente a taxonomia de eventos. A skill NÃO instala Meta/Facebook Pixel, CAPI, Google Analytics/Ads, GTM, gtag ou qualquer outro tracker diretamente.
Toda instrumentação implementada usa exclusivamente a Pixel X App e seu script. Eventuais destinos de anúncio são responsabilidade das configurações da própria Pixel X, fora desta instalação.
Não analisa dados internos da conta via MCP, não cria contas/campanhas e não executa pagamentos, reembolsos ou cancelamentos.

## Catálogo

**Padrão Meta (17):** PageView, Search, Schedule, AddToWishlist, Contact, CompleteRegistration, SubmitApplication, ViewContent, StartTrial, Lead, Donate, AddToCart, InitiateCheckout, AddPaymentInfo, Subscribe, Purchase.

**Personalizados Pixel X (6):** Content, Refund, SubscribeCanceled, TimeOnPage, WatchVideo, AdsClick.

Detalhes: [catálogo](references/eventos_meta.md) e [funis sugeridos](references/funis.md).
WatchVideo substitui VideoView. ScrollTo é apenas compatibilidade legada, não um sétimo personalizado recomendado.

## Como usar

Este repositório contém a skill na raiz. Copie seu conteúdo para uma pasta chamada pixel-x-tracking no diretório de skills do seu agente, preservando SKILL.md, references/ e agents/. Não copie somente SKILL.md.

Repositório: https://github.com/moskoweb/pixel-x-app-skill

Exemplo:
```text
Use $pixel-x-tracking para analisar / e /planos, sugerir o funil
e implementar exclusivamente o rastreamento Pixel X.
Meu domínio de projeto Pixel X é [host obtido no painel].
```

Para apenas análise:
```text
Use $pixel-x-tracking para propor os eventos de /produto e /checkout,
sem editar código. Quero revisar o plano primeiro.
```

## Limites e dependências

- Precisa do código/acesso às páginas para implementar e do host real Pixel X.
- Sem acesso ao checkout externo ou a confirmações, etapas posteriores ficam pendentes.
- Instalar a skill não instala automaticamente o pixel em sites.
- Não garante recebimento, receita/ROAS, identificação entre dispositivos ou deduplicação remota sem suporte documentado.
- Para Brasil, não cria banner automaticamente; isso não substitui avaliação da base legal nem autoriza ignorar CMP existente. Para UE, respeita autorização para rastreamento não essencial.
- Modelos de funil são propostas revisáveis, não métricas observadas.
