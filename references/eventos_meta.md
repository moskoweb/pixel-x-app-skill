# Taxonomia de Eventos Pixel X

Esta referência consolida eventos presentes na documentação de Vibe Coding fornecida para a Pixel X. Tratar nomes adicionais como não confirmados até obter documentação oficial ou verificar sua disponibilidade no painel.

## Eventos documentados

| Evento | Gatilho recomendado | Observações |
|---|---|---|
| `ViewContent` | Visualização da seção/oferta principal | No máximo um por pageview. |
| `Content` | Visualização de seção secundária | Usar painel ou JavaScript, nunca ambos para o mesmo elemento. |
| `ScrollTo` | Visualização/chegada a uma seção | Pode ser configurado como evento personalizado no painel. |
| `AddToCart` | CTA que leva a oferta/item ao checkout | Não usar em navegação comum. |
| `AddToWishlist` | Ação real de intenção comercial equivalente | Não usar como fallback para todo botão. |
| `Lead` | Formulário validado e confirmado com sucesso | Enviar apenas dados permitidos. |
| `TimeOnPage` | 30, 60 e 120 segundos visíveis | Confirmar aceitação do evento na conta. |

## Eventos que exigem confirmação

`PageView`, `InitiateCheckout`, `Purchase`, `Contact`, `VideoView`, `Download` e outros eventos podem ser úteis, mas não estão integralmente especificados na documentação de Vibe Coding anexada. Antes de implementar:

1. Confirmar que aparecem como evento padrão ou personalizado no painel Pixel X.
2. Confirmar o payload aceito.
3. Registrar o gatilho e a fonte no plano.
4. Definir como evitar duplicidade com eventos automáticos.

Para `Purchase`, exigir uma confirmação confiável da compra. A simples visita à página de obrigado não prova pagamento.

## Regras de nomeação e conteúdo

- Preservar case e grafia exibidos no painel.
- Usar `content_name` estável e legível.
- Não incluir PII em `content_name`.
- Não colocar rota completa com query string em `content_name`.
- Usar propriedades para contexto somente quando documentadas pela Pixel X.

## Fonte única

Cada linha do plano deve possuir uma origem:

- `bootstrap`: evento automático do snippet.
- `panel`: conversão configurada no painel por seletor.
- `javascript`: chamada via adaptador no site.
- `backend`: somente quando houver contrato oficial server-side.

Se duas origens puderem emitir o mesmo evento para o mesmo gatilho, interromper e escolher uma antes de implementar.
