# Conversões no Painel Pixel X

Usar este fluxo quando o plano escolher `panel` como origem da visualização de seção.

## Preparar os elementos

Adicionar um ID HTML único por seção:

```html
<section id="plans">...</section>
<section id="testimonials">...</section>
```

Convenções:

- Inglês, kebab-case e sem caracteres especiais.
- Valor do atributo HTML sem `#`.
- Não renomear IDs públicos existentes sem verificar links e CSS.
- Uma única seção principal recebe `ViewContent`.

## Configurar a conversão

No painel, criar uma conversão para cada seção aprovada:

1. Selecionar o gatilho **Visualização de Elemento / Sessão**.
2. Em **Classe CSS / ID do Elemento / Seletor CSS**, informar o ID sem `#`, por exemplo `plans`.
3. Escolher o evento:
   - `ViewContent` para a única seção principal.
   - `Content` para seções secundárias.
   - `Custom` com `ScrollTo` quando esse for o evento aprovado.
4. Preencher **Nome do Conteúdo** com um nome estável, por exemplo `PLANS`.
5. Salvar e validar em **Eventos ao vivo**.

As capturas fornecidas na documentação mostram seletores `plans` e `demo`, ambos sem `#`. Se a interface do painel mudar, seguir o formato exibido no painel atual e atualizar esta referência.

## Regra contra duplicidade

Ao usar esta conversão:

- Não observar a mesma seção com `IntersectionObserver` para enviar o mesmo evento.
- Não anexar outro gatilho de visualização ao mesmo seletor.
- Verificar se uma conversão anterior já usa o elemento.

## Tabela de entrega

| Seção | ID HTML | Seletor no painel | Evento | Origem |
|---|---|---|---|---|
| Planos | `plans` | `plans` | `ViewContent` | painel |
| Depoimentos | `testimonials` | `testimonials` | `Content` | painel |

## Validação

Abrir a página em sessão de teste, rolar uma vez até cada seção e confirmar:

- Um único evento.
- Evento e conteúdo corretos.
- Nenhum evento ao rolar novamente para a mesma seção, caso a conversão deva contar uma vez por pageview.
- Nenhuma chamada JavaScript equivalente.
