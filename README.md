# Daikibo: downtime das máquinas em 4 fábricas

Exercício de análise de dados de uma escola internacional. A Daikibo é a empresa do cenário, e os dados vieram prontos com o exercício.

![Dashboard no Tableau](imagens/dashboard_tableau.png)

## A pergunta

A Daikibo coletou durante um mês as mensagens das máquinas de 4 fábricas (Tóquio, Osaka, Berlim e Shenzhen) e queria saber duas coisas: em qual fábrica as máquinas ficaram mais tempo com problema e, dentro dela, quais máquinas falham mais.

## Os dados

É um arquivo JSON com 160.704 mensagens. São 36 máquinas (9 tipos em cada fábrica), e cada uma manda uma mensagem a cada 10 minutos dizendo se está `healthy` ou `unhealthy`. Só 103 mensagens são `unhealthy`.

## O que eu fiz

Importei o JSON no Tableau marcando todos os níveis do esquema, porque fábrica e status ficam em níveis internos do arquivo e não aparecem se eu deixar algum de fora.

Cada mensagem cobre 10 minutos, então criei um campo calculado chamado `Unhealthy`:

```
IF [Status]="unhealthy" THEN 10 ELSE 0 END
```

Somando esse campo eu tenho os minutos de inatividade. As mensagens `healthy` valem 0 e não mudam a soma.

Depois fiz dois gráficos de barras, "Downtime by plant" (por fábrica) e "Downtime by Device Type" (por tipo de máquina), e coloquei os dois em um painel. O de cima funciona como filtro: ao clicar numa fábrica, o de baixo mostra só as máquinas dela.

## Resultado

| Fábrica | Minutos de inatividade |
|---|---|
| Seiko (Osaka) | 480 |
| Shenzhen | 420 |
| Meiyo (Tóquio) | 110 |
| Berlim | 20 |

A Seiko é a fábrica com mais inatividade, e todos os 480 minutos vêm de um único tipo de máquina, o LaserWelder. Na Shenzhen, que vem em segundo, quase tudo é do LaserCutter (390 dos 420 minutos).

As duas primeiras têm o problema concentrado em equipamentos a laser. Os dados não explicam o motivo, mas é por aí que eu começaria a investigar. Também perguntaria à Daikibo o que `unhealthy` significa na prática (quebra ou só alerta), porque isso muda como o resultado deve ser lido. Por isso falo em inatividade "potencial".

## Cuidados com os dados

Os horários estão estranhos: cada máquina tem 4.464 mensagens, mas só 144 horários diferentes, o equivalente a um dia. Parece que a janela de um dia foi repetida na hora de unificar os dados, então não fiz nenhuma análise por dia ou hora.

Também há 5 mensagens `unhealthy` que repetem máquina e horário de outra. Mantive todas, como o exercício pede, e a Seiko continuaria na frente mesmo sem elas. E como é só um mês de dados, não dá para falar de tendência.
