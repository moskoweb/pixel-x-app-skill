# Instalação exclusiva Pixel X

## Bootstrap oficial

Substituir somente {DOMAIN_PROJECT} pelo host de projeto fornecido pelo usuário/painel, sem protocolo, caminho, credenciais ou caracteres de código. Não inferir host a partir do domínio público do site.

```html
<!-- Pixel X App - START -->
<script type='text/javascript'>
!function(){var e=window.location.href,t=document.title,n=Date.now(),o=document.createElement('script');o.src='https://{DOMAIN_PROJECT}/remote?url='+encodeURIComponent(e)+'&title='+encodeURIComponent(t)+'&time='+n,o.async=!0,document.head.appendChild(o)}()
</script>
<!-- Pixel X App - END -->
```

O bootstrap envia URL/título e carrega o SDK assincronamente. Evitar dados pessoais/segredos nas URLs e títulos. Nunca substituir por URL px.js ou Meta/Google.

## Local de instalação

- HTML/Vite: head do documento global antes dos scripts não essenciais.
- Next App Router: conteúdo inline do bootstrap via next/script no root layout, estratégia beforeInteractive quando a política permite carregamento inicial.
- Next Pages Router: estratégia equivalente em pages/_document.
- Outros stacks: usar entrypoint global e consultar documentação da versão antes de aplicar APIs específicas.
- Consentimento exigido: executar uma vez somente após autorização; prioridade não justifica contornar CMP.
- CSP: seguir nonce/hash existente; não introduzir unsafe-inline ou domínios irrestritos silenciosamente.
- Procurar /remote?url= antes de instalar e confirmar uma só requisição inicial. Não reinjetar em rotas SPA nem em remounts.
- Nunca tratar posição no head como prova de prontidão do SDK.

## Pixel(s) específico(s) por página

O host `{DOMAIN_PROJECT}` identifica o projeto; não é o ID do pixel. Manter o bootstrap oficial inalterado: a seleção global da página é feita pelo campo opcional `pixels` no evento **PageView** dessa página, via Pixel X.

| Configuração da página | PageView | Destino |
|---|---|---|
| Sem seleção específica | Omitir `pixels` | Todos os pixels cadastrados no painel Pixel X (padrão informado pelo produto) |
| Apenas um pixel | `pixels: '456'` | Somente 456 |
| Vários pixels | `pixels: '123,456'` | Somente 123 e 456 |

Exemplo: há 123, 456 e 789 no painel, mas `/xpto` deve usar apenas 456. Após prontidão do SDK e autorização de tracking aplicável, enviar pelo adaptador da implementação:

```js
await window.pixel_x_app.send_event({
  event_name: 'PageView',
  pixels: '456',
});
```

Para selecionar vários pixels, substituir o payload do mesmo PageView (não enviar os dois exemplos):

```js
await window.pixel_x_app.send_event({
  event_name: 'PageView',
  pixels: '123,456',
});
```

- `pixels` é uma string de IDs separados por vírgula, sem espaços nos exemplos; não usar array ou número. Obter IDs reais do usuário/painel, sem confundir com IDs de elementos HTML.
- Essa configuração no PageView define a seleção global da página, não apenas um Lead isolado. Registrar o destino por rota no plano e estabelecer esse PageView antes dos eventos subsequentes implementados pela skill.
- Sem restrição solicitada, manter o padrão e omitir o campo; não exigir IDs nem inventar seleção. String vazia não é um contrato de reset documentado.
- Coordenar com PageView automático: verificar ordem e duplicidade antes de implementar. Não presumir que um PageView manual posterior restringe retroativamente eventos já enviados pelo bootstrap. Se não for possível garantir a restrição inicial, reportar a pendência em vez de afirmar isolamento completo.
- Em SPA, não reinjetar o bootstrap. Validar a seleção na entrada e troca de rota, especialmente restrita → padrão e restrita → outra seleção. Não presumir que omitir `pixels` limpa uma seleção anterior no mesmo documento; confirmar o comportamento suportado pelo SDK, sem inventar método de reset.
- Falha na configuração restrita não autoriza reenviar para todos: suspender os eventos dependentes dessa configuração e reportar, sem bloquear a navegação do visitante.

Fonte: [documentação oficial — Rastreamento de Pixel Específico em Página](https://app.notion.com/p/mosko-digital/Documenta-o-de-Fun-es-por-JavaScript-e7c7e40e5ab643f7b74ed3ad90f23967#3e6d005117c880b6b548c3382bda7f7a). Formato múltiplo `123,456` e padrão sem seleção confirmados pelo responsável da Pixel X.

## Privacidade

Aplicar a decisão do projeto descrita em privacy_validation.md. Não criar banner automaticamente em projetos brasileiros apenas porque esta skill foi usada. Não remover CMP existente ou substituir uma negativa por autorização. Política/região desconhecida não é sinônimo de permissão de coleta.
