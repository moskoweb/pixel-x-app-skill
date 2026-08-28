# Privacidade e Validação

## Índice

- Gate de privacidade
- Testes estáticos
- Testes no navegador
- Matriz de eventos
- Validação no painel
- Critérios de reprovação

## Gate de privacidade

Antes da implementação, identificar:

- Modo territorial adotado: Brasil, União Europeia ou regional.
- No Brasil, confirmar que a implementação não criou nem passou a exigir banner/CMP.
- Na União Europeia, identificar a CMP e a categoria que controla o bootstrap e os eventos programáticos.
- Dados permitidos em `Lead`.
- Política de retenção e exclusão aplicável à Pixel X.
- Ambientes onde tracking deve ficar desativado.

Não enviar:

- Senhas, tokens, documentos, cartão ou dados de saúde.
- Campos livres de mensagem.
- Query strings ou fragments que possam conter PII.
- Payloads completos em logs.

## Testes estáticos

Executar buscas equivalentes a:

```sh
rg -n "/remote\?url=|pixel_x_app|send_event|data-pxa-event|pxa_mask_phone" .
```

Confirmar:

- Uma única instalação do bootstrap.
- Nenhuma chamada direta nova fora do adaptador.
- Nenhum placeholder `{DOMAIN_PROJECT}` esquecido em produção.
- Nenhuma chave, token ou PII hardcoded.
- Nenhum elemento com `data-pxa-event` vazio.

## Testes no navegador

Testar em desktop e viewport móvel:

1. Modo Brasil: bootstrap executa e eventos funcionam sem banner/CMP.
2. Modo União Europeia, consentimento negado: bootstrap e chamadas programáticas não executam.
3. Modo União Europeia, consentimento concedido: o bootstrap executa uma vez e os eventos esperados aparecem.
4. SDK bloqueado: a página continua funcionando e redirects ocorrem.
5. SDK lento: eventos aguardam até o limite e não geram exceção.
6. CTA: exatamente um evento por clique válido.
7. Duplo clique: não duplica submissão ou compra.
8. Formulário inválido: nenhum `Lead`.
9. Backend rejeita formulário: nenhum `Lead`.
10. Backend confirma formulário: um `Lead` com dados permitidos.
11. Seção: um evento na primeira visualização e nenhum ao rolar de volta.
12. Rota SPA: estado antigo é descartado; timers e seções reiniciam.
13. Controles de menu, FAQ, carousel, modal e cookies: nenhum evento comercial.
14. Popup: máscara carrega depois que o campo aparece.

Usar mocks/spy de `window.pixel_x_app.send_event` em testes automatizados sempre que o stack permitir.

## Matriz de eventos

Registrar o resultado:

| Evento | Cenário positivo | Cenário negativo | Esperado | Observado | Resultado |
|---|---|---|---:|---:|---|
| `Lead` | envio confirmado | validação falhou | 1 / 0 | — | — |
| `AddToCart` | CTA de checkout | abrir FAQ | 1 / 0 | — | — |
| `ViewContent` | seção principal 50% | Hero | 1 / 0 | — | — |

## Validação no painel

Em **Eventos ao vivo**:

- Confirmar nome exato do evento.
- Confirmar `content_name`.
- Confirmar presença somente dos dados autorizados.
- Confirmar que cada ação gera uma ocorrência.
- Comparar modo normal e navegação SPA.
- Confirmar que uma conversão de seção não está duplicada por JavaScript.

Se o agente não tiver acesso ao painel, marcar `Validação Pixel X: pendente pelo usuário` e fornecer a matriz a preencher.

## Critérios de reprovação

Reprovar a implementação quando houver:

- Mais de um bootstrap.
- Evento duplicado.
- Exceção que interrompa navegação ou submissão.
- Evento comercial disparado por controle de interface.
- PII indevida.
- Banner/CMP criado ou exigido automaticamente no modo Brasil.
- Evento antes do consentimento no modo União Europeia.
- `ViewContent` múltiplo no mesmo pageview.
- Placeholder de domínio em build de produção.
- Afirmação de sucesso sem evidência local ou do painel.
