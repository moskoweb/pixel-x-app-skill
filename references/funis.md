# Modelos de funil — propostas para revisão

## Como selecionar

Inspecionar as páginas pedidas e escolher pelo objetivo real. Etapas podem ser puladas, repetidas ou acontecer fora do site. Não forçar uma sequência nem simular eventos faltantes. Instrumentar só rotas acessíveis e autorizadas.

Legenda:
- Núcleo: necessário para medir o objetivo, quando existe gatilho verificável.
- Opcional: implementar somente se a ação existir.
- Diagnóstico: Content, TimeOnPage e WatchVideo são sinais de engajamento, não etapas obrigatórias para converter.
- Pós-conversão: Refund e SubscribeCanceled são ramos posteriores, não substituem Purchase/Subscribe.

## 1. Geração de leads

Núcleo: PageView → ViewContent → Lead.
Opcionais: Contact para contato direto para o WhatsApp ou similar; CompleteRegistration apenas se houver cadastro; SubmitApplication para aplicação formal em vez de um Lead genérico.
Diagnóstico: Content em benefícios/prova social; TimeOnPage; WatchVideo quando houver vídeo.
Não contar clique no WhatsApp como lead confirmado. Se só há contato externo, o objetivo observável termina em Contact. Sugerir uso de link rastreado para redirecionamento para WhatsApp.

## 2. Venda direta

Núcleo: PageView → AddToWishlist (links âncoras) → ViewContent → AddToCart (seleção da oferta) → InitiateCheckout → Purchase.
Opcionais: AddPaymentInfo se etapa observável; Lead se houver captura independente ou pré-checkout.
Diagnóstico: Content em módulo/prova/garantia/FAQ; TimeOnPage e WatchVideo.
Pós-conversão: Refund confirmado.
Sem acesso ao checkout externo, registrar seleção/saída no site e marcar etapas posteriores como pendentes. Não enviar InitiateCheckout/Purchase somente pelo clique de saída. Sugerir uso de link rastreado para redirecionamento para o checkout.

## 3. Assinatura / SaaS

Núcleo: PageView → ViewContent → Subscribe/Purchase.
Ramo trial: Lead → StartTrial → Subscribe/Purchase.
Ramo pago: InitiateCheckout → AddPaymentInfo (se observável) → Subscribe; Purchase só com pagamento confirmado e finalidade distinta registrada.
Diagnóstico: Content para funcionalidades/comparação/depoimentos/FAQ; WatchVideo para demo.
Pós-conversão: SubscribeCanceled; Refund quando realmente houver reembolso.
Não somar Subscribe e Purchase como duas vendas. Separar coortes de trial e assinatura paga.

## 4. Lançamento

Pré-lançamento: PageView → ViewContent → Lead.
Aquecimento: novas PageViews; Content/WatchVideo/TimeOnPage nas páginas de conteúdo.
Oferta: PageView → ViewContent → AddToCart → InitiateCheckout → Purchase.
Opcionais: Contact, Schedule em vendas assistidas.
Não exigir que compradores tenham assistido todo conteúdo; analisar jornadas com e sem aquecimento.

## 5. Agendamento

Núcleo: PageView → ViewContent → Schedule.
Opcionais: Contact antes do agendamento; Lead se houver captação anterior independente; SubmitApplication para qualificação formal.
Diagnóstico: Content em credenciais e depoimentos.
Calendário externo sem callback verificável: registrar Contact quando apropriado e deixar Schedule pendente. Não inventar evento de comparecimento ou venda.

## 6. Ecommerce / marketplace

Núcleo: PageView → ViewContent (produto/oferta) → AddToCart → InitiateCheckout → Purchase.
Opcionais: Search nos resultados; AddToWishlist nos favoritos; AddPaymentInfo no checkout; Contact para vendedor; CompleteRegistration para conta.
Ramo vendedor: SubmitApplication → CompleteRegistration, somente nos gatilhos reais correspondentes.
Diagnóstico: Content em detalhes/prova; AdsClick apenas em espaços publicitários identificados.
Pós-conversão: Refund.
Não confundir GMV, receita do vendedor e comissão da plataforma; a skill não calcula receita sem contrato e dados próprios.

## 7. Doação / candidatura

Doação: PageView → ViewContent → Donate confirmado. InitiateCheckout/AddPaymentInfo apenas se houver fluxo correspondente.
Candidatura: PageView → ViewContent → SubmitApplication confirmado.
CompleteRegistration é opcional se existir cadastro separado. Não emitir Lead e SubmitApplication automaticamente para o mesmo formulário.

## Medidas sugeridas

| Pergunta | Medida | Condição |
|---|---|---|
| Visitantes chegaram à oferta? | visitantes com ViewContent / visitantes com PageView | Mesmo período e escopo |
| Oferta gera leads? | visitantes com Lead / visitantes com ViewContent | Mesma coorte; resultado não é contagem bruta de eventos |
| Oferta gera seleção? | visitantes com AddToCart / visitantes com ViewContent | Separar carrinho de seleção de oferta |
| Checkout converte? | checkouts com Purchase / checkouts iniciados | Identificação e janela de conversão compatíveis |
| Trial vira assinatura? | usuários em trial que assinaram / trials da coorte | Aguardar janela de maturação |
| Qual abandono entre etapas? | 1 − taxa de passagem ordenada | Só se houver correlação/jornada observável |
| Reembolso e cancelamento? | operações/pessoas afetadas sobre base elegível | Definir período, coorte, parcial/total; não misturar pedidos e pessoas |

São definições para o plano e futura análise; não inventar resultados. Não calcular funil sequencial confiável com totais agregados sem identidade/janela. Documentar unidade (visitante, sessão, pedido), timezone, período, janela, filtros e deduplicação. Denominador zero → não disponível.
