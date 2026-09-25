# Padrões técnicos

## Adaptador único

Criar trackPixelX(payload, options) integrado ao stack real, seguro em SSR.
- Contrato mínimo: event_name, content_name, lead_name, lead_email, lead_phone.
- Verificar window e permissão de tracking antes da espera e imediatamente antes do envio, inclusive após revogação.
- Aguardar typeof window.pixel_x_app?.send_event === 'function' com polling curto e timeout limitado (ex.: 4s). Cancelar polling no término/abort.
- Usar Promise.resolve para retorno síncrono/assíncrono. Limitar também o tempo total da chamada; SDK carregado não implica promise resolvida.
- Capturar erro sem quebrar ação do usuário. Não logar payload/PII.
- Não afirmar entrega só por ausência de exceção: retorno local indica chamada tentada/resolvida, não recebimento remoto.
- Não fazer retry após timeout: envio pode ter ocorrido e a API não garante idempotência.
- Não usar filas persistentes com PII. Eventos pendentes de rota devem carregar token da rota; descartá-los se mudarem de escopo antes do envio.
- Uma chave local de contagem não é event_id remoto. Usar estados pendente/tentado por gatilho; documentar falhas sem reenvio cego.

## Navegação

Verificar PageView automático no load e navegação SPA antes de adicionar envio manual.
Usar hook/evento do router real; não monkey-patch history indiscriminadamente.
No commit da rota, descartar observers/timers e criar novo escopo. Diferenciar pathname de filtro, hash e parâmetros conforme plano. Sanitizar conteúdo; não transmitir queries sensíveis.
Verificar como SDK captura contexto de rota; não inventar page_url ou função de atualização.
Se a atualização de contexto não puder ser confirmada, reportar a limitação da instrumentação SPA.
Tratar voltar/avançar, rotas repetidas e remount/StrictMode sem duplicidade.

## Seções

Escolher primarySectionId explicitamente (ex.: offer). Pular hero e controles irrelevantes.
Observar IDs selecionados no plano, não apenas todos os section[id].
Verificar intersectionRatio >= limiar configurado, não só isIntersecting. Para seção maior que viewport, escolher alvo/sentinel visível representativo: 50% da seção pode ser impossível.
Contar uma vez por seção/pageview; desconectar observer no cleanup.
Não misturar conversão de painel com observer do mesmo gatilho.

## Ações comerciais

Usar handlers explícitos ou data-pxa-event/data-pxa-content estáticos em elementos aprovados.
Não obter nome do evento de texto do botão/URL por regex.
Adicionar ao carrinho/favoritos só após sucesso; link direto pode ser seleção de oferta conforme plano.
Preservar Ctrl/Cmd-clique, abrir nova aba, botões disabled, teclado e navegação.
Links externos podem descarregar o documento antes do envio; usar somente transporte documentado Pixel X e espera curta limitada quando apropriado. Não prometer entrega nem criar beacon para endpoint inventado.

## Formulários e confirmações

Preservar handler e validação existentes. Enviar após resposta bem-sucedida.
Lead, CompleteRegistration, SubmitApplication e Schedule têm significados distintos.
Obter dados do estado/FormData; names estáveis e IDs únicos por formulário.
Usar identidade real da submissão quando disponível para impedir dupla contagem; não usar PII como chave.
Antes de redirect, limitar espera (ex.: 1200ms); cancelar espera pendente no adaptador e navegar em finally. Promise.race sozinha não cancela o trabalho perdedor.
Tracking falho não impede sucesso do formulário.

## Tempo e vídeo

TimeOnPage: 30/60/120 segundos de tempo visível por pageview. Contar deltas monotônicos reais, não número de ticks. Pausar oculto e limpar no cleanup.
WatchVideo: marcos adequados à duração (ex.: 30/60/120s) e IDs de vídeo estáveis no content_name. Só acumular durante reprodução observável; pausar em pause, buffering, ended e página oculta. Seek não conta o trecho pulado; replay não repete marco já enviado naquele pageview.
Em players externos, usar eventos públicos do player se disponíveis, sem instalar pixels de publicidade externos. Iframe opaco sem API → pendência, não simular vídeo assistido com timer.
AdsClick: somente anúncio clicado e observável; não inferir clique de iframe cross-origin ou de visita com UTM.

## Assinatura, compra e pós-venda

Purchase/Subscribe/Donate/Refund/SubscribeCanceled exigem estado confirmado verificável.
Visita direta/reload de página de sucesso não basta. Associar tentativa local a operação real e respeitar limites do SDK: dedupe em memória não protege entre dispositivos.
Reembolso parcial precisa identidade de operação própria; não contar todo pedido como reembolsado total.
Sem confirmação acessível pelo site/script Pixel X, deixar pendente. Não expandir para REST/webhooks/Meta CAPI. Handoff para integração oficial Pixel X separado, se necessário.
Nunca enviar cartão ou dados sensíveis em AddPaymentInfo.

## Máscaras

Classe pxa_mask_phone ou pxa_mask_phone_inter no pai do input, não no campo.
Usar type="text", inputmode="tel", autocomplete="tel", id e name.
Após montar popup/campo, aguardar SDK e invocar mask_load() ou mask_load_inter() preservando this, com timeout e erro isolado.
Não instalar outra biblioteca de tracking para obter máscara.
