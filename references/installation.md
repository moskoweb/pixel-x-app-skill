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

## Privacidade

Aplicar a decisão do projeto descrita em privacy_validation.md. Não criar banner automaticamente em projetos brasileiros apenas porque esta skill foi usada. Não remover CMP existente ou substituir uma negativa por autorização. Política/região desconhecida não é sinônimo de permissão de coleta.
