# Catálogo de eventos Pixel X

Fonte: lista confirmada pelo responsável da Pixel X nesta revisão. Os 17 nomes abaixo seguem a taxonomia padrão Meta solicitada; o transporte é exclusivamente Pixel X, nunca SDK de terceiros. Nome suportado não significa gatilho disponível na página nem documentação de campos adicionais.

## Eventos padrão

| Evento | Etapa / gatilho | Evitar |
|---|---|---|
| PageView | Página carregada ou rota SPA concluída | Duplicar o evento automático do bootstrap |
| Search | Busca efetivamente executada | Cada tecla digitada; termos com dados pessoais |
| Schedule | Agendamento confirmado | Simples abertura do calendário |
| AddToWishlist | Item salvo/favoritado com sucesso | Qualquer clique ou CTA genérico |
| Contact | Ação explícita de contato: WhatsApp, telefone, e-mail | Alegar conversa/atendimento concluído por causa do clique |
| CompleteRegistration | Cadastro concluído com sucesso | Abrir cadastro ou validação falhar |
| SubmitApplication | Candidatura/solicitação formal enviada com sucesso | Confundir toda candidatura com Lead |
| ViewContent | Visualização da oferta/conteúdo principal | Várias seções principais no mesmo pageview |
| StartTrial | Período de teste efetivamente ativado | Apenas clicar em experimentar |
| Lead | Captação de lead confirmada | Submit inválido ou rejeitado |
| Donate | Doação confirmada | Apenas clicar em doar |
| AddToCart | Item adicionado; em venda direta, seleção explícita da oferta que leva ao checkout | Qualquer link externo ou botão comprar sem verificar comportamento |
| InitiateCheckout | Fluxo de checkout realmente iniciado | Inferir abertura de checkout externo inacessível |
| AddPaymentInfo | Informações de pagamento aceitas pela etapa | Capturar conteúdo do cartão ou cada edição |
| Subscribe | Assinatura efetivamente ativada | Clicar no plano ou iniciar trial sem assinatura |
| Purchase | Compra/pagamento confirmado | Visitar obrigado, boleto/Pix criado ou pedido pendente |

Usar a convenção Pixel X de um ViewContent principal por pageview. Documentar AddToCart como seleção da oferta quando o site de venda direta não tiver carrinho, para não confundir métricas de ecommerce.

## Personalizados sugeridos pela Pixel X

| Evento | Gatilho | Conteúdo e contagem sugeridos |
|---|---|---|
| Content | Visualização de seção secundária, complementar a ViewContent | ID estável; uma vez por seção/pageview |
| Refund | Reembolso efetivamente realizado | Referência não sensível; uma vez por operação de reembolso |
| SubscribeCanceled | Cancelamento confirmado | Uma vez por operação; distinguir cancelamento solicitado de acesso encerrado no plano |
| TimeOnPage | Marcos de tempo visível na página | 30, 60, 120 segundos; uma vez por marco/pageview |
| WatchVideo | Marcos de tempo efetivamente assistido | Ex.: demo-video:30s, 60s, 120s conforme duração; uma vez por marco/vídeo/pageview |
| AdsClick | Clique explícito em anúncio exibido na página | ID estável do anúncio/posição; uma vez por interação |

Não usar VideoView. WatchVideo é o nome canônico. ScrollTo é uma alternativa legada do material anterior: preservar se já existir e for pedida, mas sugerir Content para novas seções; não emitir ambos para a mesma visualização. Não acrescentar ScrollTo aos seis personalizados recomendados.

## Payload e significado

Contrato mínimo conhecido:
```js
await window.pixel_x_app.send_event({
  event_name: 'Lead',
  content_name: 'contact-form',
  lead_name: name,
  lead_email: email,
  lead_phone: phone,
});
```

Usar somente event_name/content_name em eventos sem dados de lead necessários. Strings estáveis em inglês para IDs e conteúdo técnico; nomes de eventos são case-sensitive. Não incluir e-mail, telefone, parâmetros secretos ou termos livres no content_name.

Não deduzir price, currency, order_id, event_id, métricas de receita ou deduplicação remota a partir do nome Purchase. Consultar contrato específico antes de enviar campos extras.

Contact não prova Lead; Lead não prova Purchase; Subscribe não é automaticamente Purchase. Se a mesma operação justificar dois eventos diferentes, explicar finalidades e não somar os dois como duas conversões/vendas.

Refund e SubscribeCanceled não são inferíveis pelo navegador quando ocorrem fora dele. Sem confirmação confiável, deixar como planejados/pendentes; não criar webhook, REST ou integração de terceiros sob esta skill.
