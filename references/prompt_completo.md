# Prompt para Agente de Coding

Usar somente quando o usuário quiser delegar a implementação a outro agente. Preencher os campos entre colchetes antes de entregar. Não duplicar este prompt no `SKILL.md`.

```text
Implemente o rastreamento Pixel X neste projeto sem alterar o comportamento visual ou comercial existente.

Snippet oficial — substitua somente {DOMAIN_PROJECT} por [DOMÍNIO PIXEL X]:

<!-- Pixel X App - START -->
<script type='text/javascript'>
!function(){var e=window.location.href,t=document.title,n=Date.now(),o=document.createElement('script');o.src='https://{DOMAIN_PROJECT}/remote?url='+encodeURIComponent(e)+'&title='+encodeURIComponent(t)+'&time='+n,o.async=!0,document.head.appendChild(o)}()
</script>
<!-- Pixel X App - END -->

Antes de editar:
1. Descubra framework, router, entrypoint global, formulários, CTAs, checkout, seções, países atendidos e tracking já existente.
2. Produza um plano com Evento, Gatilho, Origem, Conteúdo, Escopo, Política Territorial e Deduplicação.
3. Use uma única origem por evento. Para seções, escolha painel OU JavaScript; nunca os dois.
4. Confirme a seção principal que receberá o único ViewContent.

Implementação:
- Instale o snippet uma única vez e o mais cedo possível no entrypoint global.
- Em Next.js, use next/script com strategy="beforeInteractive" no local oficial da versão instalada.
- Centralize chamadas em um adaptador seguro para SSR, SDK lento, falhas e política territorial.
- Use Brasil sem banner/CMP obrigatório como padrão. Não crie nem exija banner para sites brasileiros. Para sites ou visitantes sujeitos à União Europeia, integre com a CMP existente e bloqueie bootstrap/eventos até autorização aplicável.
- Não transforme qualquer botão em AddToWishlist. Instrumente apenas CTAs explicitamente aprovados, preferencialmente com data-pxa-event e data-pxa-content.
- Dispare Lead somente após sucesso confirmado do formulário e antes do redirect, com timeout curto e navegação em finally.
- Em SPA, reinicie seções e timers por mudança real de rota. Não duplique o pageview inicial automático.
- Use IDs de seção em inglês e kebab-case. No painel, informe o ID sem #.
- Não envie PII fora dos campos Lead autorizados nem registre payloads completos em logs.

Eventos aprovados:
[COLE A TABELA DO PLANO]

Validação obrigatória:
- build/lint/testes;
- uma instalação do snippet;
- nenhum erro com SDK bloqueado ou lento;
- no modo Brasil, tracking funciona sem banner; no modo União Europeia, nenhum evento ocorre antes da autorização aplicável;
- exatamente um evento por gatilho;
- nenhum controle de interface classificado como conversão;
- testes de rota SPA, formulário, seções, timers e popup;
- matriz para validação em Eventos ao vivo da Pixel X.

Ao final, informe arquivos modificados, plano final, tabela de seções, testes executados e pendências de painel/acesso humano. Não declare a validação no painel como concluída sem evidência.
```
