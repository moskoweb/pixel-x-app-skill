# Privacidade e aceitação

## Política operacional

Para projetos destinados ao Brasil, não criar nem exigir banner/CMP automaticamente só para instalar Pixel X. Isso não é uma declaração de dispensa legal geral: preservar política e escolhas existentes, transparência, minimização e base aplicável definida pelo responsável.
Não hardcodificar autorização se houver consentimento negado ou política que exija autorização. Não classificar publicidade/perfilização automaticamente como medição agregada.
Para UE, bloquear bootstrap e eventos não essenciais até autorização aplicável. Em mercados mistos, usar mecanismo regional confiável existente; não inventar geolocalização por idioma/domínio nem consultar serviço de IP sem necessidade/autorização.
Outros territórios/escopo incerto: confirmar política, sem extrapolar o padrão brasileiro.
Não enviar segredos/PII em URL, title, content_name, logs ou IDs técnicos; só dados de lead necessários nos campos documentados. A classificação do transporte automático importa tanto quanto send_event.

## Testes por implementação

Executar build/lint/testes pertinentes e conferir:
- Uma instalação do bootstrap e nenhuma tag nova Meta/Google/GTM.
- Domínio real válido; nenhum placeholder em produção.
- SDK lento, ausente, rejeitando e pendurado não quebra navegação.
- Revogação durante espera impede envio posterior; timeout não cria retry duplicado.
- PageView inicial e de SPA sem duplicatas; timers/observers resetam no escopo certo.
- Destinos por página: sem seleção → todos; '456' → só 456; '123,456' → só os dois, nunca 789. Conferir PageView e eventos subsequentes, inclusive automáticos.
- SPA: testar restrita → outra seleção → padrão, voltar/avançar e eventos atrasados; não assumir reset por omissão nem vazamento permitido em falha.
- Restrição inicial: comprovar que nenhum evento automático anterior à configuração atingiu pixels excluídos; sem evidência, registrar pendência.
- ViewContent principal único e Content em secundárias, inclusive seções longas/mobile.
- Formulário inválido/rejeitado não gera conversão; sucesso gera uma.
- AddToWishlist não dispara em FAQ/menu; AddToCart não dispara em falha de carrinho.
- Purchase não dispara por /obrigado aberto diretamente.
- Subscribe, Refund e SubscribeCanceled não disparam em simples cliques.
- WatchVideo não avança em pause/seek/aba oculta nem duplica por replay.
- AdsClick não transforma CTAs, origens UTM ou cliques inacessíveis em anúncios.
- Redirecionamento funciona mesmo com falha; ações de teclado/nova aba preservadas.
- Máscara monta após popup; campos preservam acessibilidade.
- Permissões/privacidade respeitadas; ausência de banner brasileiro não é falha por si só.

Usar SDK mock e dados sintéticos para testes locais; não criar compras, cancelamentos ou reembolsos reais.
Registrar Caso | Esperado | Observado | Evidência | Status, incluindo negativos.
Depois verificar eventos/conteúdo/frequência no painel Pixel X. Sem acesso, marcar "recebimento não verificado". Não confundir chamada local, resposta do SDK e confirmação de armazenamento.

## Critérios para encerrar

Não declarar pronto com domínio placeholder, tags de terceiros novas, eventos duplicados, PII indevida ou operações de negócio simuladas.
Etapas externas sem acesso devem constar como pendentes, não como entregues. Relatório de análise não prova instrumentação instalada.
