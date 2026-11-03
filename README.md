# Daikibo: downtime das máquinas em 4 fábricas

Análise dos dados de telemetria das máquinas da Daikibo, empresa com fábricas em Tóquio, Osaka, Berlim e Shenzhen.

![Dashboard no Tableau](imagens/dashboard_tableau.png)

## A pergunta

A Daikibo coletou durante um mês as mensagens das máquinas de 4 fábricas (Tóquio, Osaka, Berlim e Shenzhen) e queria responder duas perguntas:

1. Em qual fábrica as máquinas ficaram mais tempo com problema?
2. Quais máquinas dessa fábrica mais falham?

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

Ao selecionar a Seiko no painel, o gráfico de baixo mostra uma única máquina com inatividade: o LaserWelder, com 480 minutos. Todos os outros tipos ficam em 0.

## Conclusões

O que os dados mostram:

* A Seiko (Osaka) é a fábrica com mais inatividade, e todo esse tempo vem de um único tipo de máquina, o LaserWelder.
* A Shenzhen vem logo atrás (420 min) e também se concentra em um equipamento a laser: o LaserCutter (390 min).

O que pode explicar (hipótese, os dados não provam):

* Pode haver um problema comum aos equipamentos a laser, como o modelo, a manutenção ou o ambiente.

O que eu recomendaria:

* Investigar primeiro o LaserWelder da Seiko e o LaserCutter da Shenzhen.
* Perguntar à Daikibo o que `unhealthy` significa na prática: quebra ou apenas alerta.

## Cuidados com os dados

Os horários estão estranhos: cada máquina tem 4.464 mensagens, mas só 144 horários diferentes, o equivalente a um dia. Parece que a janela de um dia foi repetida na hora de unificar os dados, então não fiz nenhuma análise por dia ou hora.

Também há 5 mensagens `unhealthy` que repetem máquina e horário de outra. Mantive todas, como o exercício pede, e a Seiko continuaria na frente mesmo sem elas. E como é só um mês de dados, não dá para falar de tendência.
