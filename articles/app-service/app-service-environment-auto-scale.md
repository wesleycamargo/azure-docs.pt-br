<properties 
	pageTitle="Dimensionamento automático e Ambiente de Serviço de Aplicativo" 
	description="Dimensionamento automático e Ambiente de Serviço de Aplicativo" 
	services="app-service"
	documentationCenter=""
	authors="btardif" 
	manager="wpickett" 
	editor="" 
/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/01/2016" 
	ms.author="byvinyal"
/>
	
#Dimensionamento automático e Ambiente de Serviço de Aplicativo

##Introdução
Os **ambientes de Serviço de Aplicativo** dão suporte a dimensionamento automático. Isso é possível ao permitir dimensionar automaticamente pools de trabalho individuais com base em métricas ou agenda.
 
![][intro]
 
O dimensionamento automático permite otimizar a utilização de recursos, escalar e reduzir verticalmente um **ambiente de Serviço de Aplicativo** de forma automática para ajustar seu orçamento e/ou perfil de carregamento.

##Configurar o dimensionamento automático de pool de trabalho 
Você pode acessar a funcionalidade de dimensionamento automático na guia Configurações do **pool de trabalho**.
 
![][settings-scale]

A partir daí, a interface deve ser bastante familiar, já que é a mesma experiência que ao dimensionar um **plano de Serviço de Aplicativo**. Você pode inserir manualmente um valor de escala
 
![][scale-manual]
 
Ou configurar um perfil de dimensionamento automático:
 
![][scale-profile]
 
Os perfis de dimensionamento automático são úteis para definir limites na sua escala. Dessa forma, você pode ter tanto uma experiência de desempenho consistente, definindo um valor de escala de limite inferior (1), quanto um limite de gastos previsível, definindo um limite superior (2).
 
![][scale-profile2]
 
Após definir um perfil, as métricas com base em regras de dimensionamento automático podem ser adicionadas para aumentar ou reduzir o número de instâncias no **pool de trabalho** dentro dos limites definidos pelo perfil.

![][scale-rule]

 Qualquer métrica de **pool de trabalho** ou **front-end** pode ser usada para definir regras de dimensionamento automático. Essas são as mesmas métricas que você pode monitorar nos gráficos de folha de recursos e para as quais pode definir alertas.
 
##Exemplo de dimensionamento automático
O dimensionamento automático de um **ambiente de Serviço de Aplicativo** pode ser melhor ilustrado examinando um cenário. Neste artigo, guiarei por meio de todas as considerações necessárias ao configurar o dimensionamento automático e todas as interações que entram em jogo quando devemos fatorar **ambientes de Serviço de Aplicativo** de dimensionamento automático que são hospedados em um ASE.

###Introdução ao cenário
Matheus é um SysAdmin de uma empresa, e migrou uma parte das cargas de trabalho que ele gerencia para um **ambiente de Serviço de Aplicativo**.

O **ambiente do Serviço de Aplicativo** está configurado para a escala manual da seguinte maneira:
* Front-Ends: 3
* Pool de Trabalho 1: 10
* Pool de Trabalho 2: 5
* Pool de Trabalho 3: 5

**O pool de trabalho 1** é usado para cargas de trabalho de produção, embora o **pool de trabalho 2** e o **pool de trabalho 3** sejam usados para cargas de trabalho de desenvolvimento e controle de qualidade.

Os **planos de Serviço de Aplicativo** usados para garantia da qualidade e desenvolvimento são configuradas para **escala manual** de produção, mas o **plano de Serviço do Aplicativo** é definido como **dimensionamento automático** para lidar com variações na carga e tráfego.

Matheus está familiarizado com o aplicativo e sabe que os horários de pico de carga são entre 9:00 e 18:00, como este é um **aplicativo LOB** e é usado por funcionários enquanto estão no escritório. O uso é reduzido depois disso, quando os usuários terminam o dia de trabalho. Mas ainda há alguma carga, já que os usuários podem acessá-lo remotamente com seus dispositivos móveis ou computadores domésticos. O **plano de Serviço de Aplicativo** de produção já está configurado para **dimensionamento automático** com base no uso de CPU com as seguintes regras:

![][asp-scale]

|	**Perfil de dimensionamento automático – Dias da semana – Plano de serviço de aplicativo** |	**Perfil de dimensionamento automático – Finais de semana – Plano de serviço de aplicativo** |
|	----------------------------------------------------	|	----------------------------------------------------	|
|	**Nome:** Perfil Dia da semana |	**Nome:** Perfil Final de semana |
|	**Dimensionar por:** Regras de agendamento e desempenho |	**Dimensionar por:** Regras de agendamento e desempenho |
|	**Perfil:** Dias da semana |	**Perfil:** Final de semana |
|	**Tipo:** recorrência |	**Tipo:** recorrência |
|	**Intervalo de destino:** 5 a 20 instâncias |	**Intervalo de destino:** 3 a 10 instâncias |
|	**Dias:** segunda-feira, terça-feira, quarta-feira, quinta-feira, sexta-feira |	**Dias:** sábado e domingo |
|	**Hora de início:** 9:00 |	**Hora de início:** 9:00 |
|	**Fuso horário:** UTC-08 |	**Fuso horário:** UTC-08 |
| | |
|	**Regra de dimensionamento automático (escalar verticalmente)** |	**Regra de dimensionamento automático (escalar verticalmente)** |
|	**Recurso:** Produção (ambiente de Serviço de Aplicativo) |	**Recurso:** Produção (ambiente de Serviço de Aplicativo) |
|	**Métrica:** % da CPU |	**Métrica:** % da CPU |
|	**Operação:** Mais de 60% |	**Operação:** Mais de 80% |
|	**Duração:** 5 minutos |	**Duração:** 10 minutos |
|	**Agregação de tempo:** Média |	**Agregação de tempo:** Média |
|	**Ação:** Aumentar a contagem em 2 |	**Ação:** Aumentar a contagem em 1 |
|	**Tempo de resfriamento (minutos):** 15 |	**Tempo de resfriamento (minutos):** 20 |
| | |
|	**Regra de dimensionamento automático (reduzir verticalmente)** |	**Regra de dimensionamento automático (reduzir verticalmente)** |
|	**Recurso:** Produção (ambiente de Serviço de Aplicativo) |	**Recurso:** Produção (ambiente de Serviço de Aplicativo) |
|	**Métrica:** % da CPU |	**Métrica:** % da CPU |
|	**Operação:** Menos de 30% |	**Operação:** Menos de 20% |
|	**Duração:** 10 minutos |	**Duração:** 15 minutos |
|	**Agregação de tempo:** Média |	**Agregação de tempo:** Média |
|	**Ação:** Reduzir a contagem em 1 |	**Ação:** Reduzir a contagem em 1 |
|	**Tempo de resfriamento (minutos):** 20 |	**Tempo de resfriamento (minutos):** 10 |

###Taxa de inflação do plano de Serviço de Aplicativo
Os **planos de Serviço de Aplicativo** configurados para dimensionamento automático escalam ou reduzem a uma taxa máxima por hora. Essa taxa pode ser calculada com base nos valores fornecidos na regra de dimensionamento automático.

Compreender e calcular a **taxa de inflação do plano de Serviço de Aplicativo** é importante para o dimensionamento automático do **pool de trabalho** do **ambiente de Serviço de Aplicativo**, já que as alterações de escala em um **pool de trabalho** não são instantâneas e levam algum tempo para serem aplicadas.

A taxa de inflação do **plano do Serviço de Aplicativo** é calculada da seguinte maneira:

![][ASP-Inflation]

Com base na regra *Dimensionamento automático - escalar verticalmente* do perfil *dia da semana* perfil do **plano de Serviço de Aplicativo** de produção, seria semelhante ao seguinte:

![][Equation1]

No caso da regra *Dimensionamento automático - escalar verticalmente* do perfil *Finais de semana* do **plano de Serviço de Aplicativo** de produção, a fórmula resolveria para:

![][Equation2]

Esse valor também pode ser calculado para operações de redução:

Com base na regra *Dimensionar automaticamente - reduzir verticalmente* do perfil *Dia da semana* do **plano de Serviço de Aplicativo** de produção, seria semelhante ao seguinte:

![][Equation3]

No caso da regra *Dimensionar automaticamente - reduzir verticalmente* do perfil *Finais de semana* da produção **plano de Serviço de Aplicativo** de produção, a fórmula resolveria para:

![][Equation4]

Isso significa que o **plano de Serviço de Aplicativo** pode crescer em uma taxa máxima de **8** instâncias por hora durante a semana e **4** instâncias por hora durante o fim de semana. E ele pode liberar instâncias em uma taxa máxima de **4** instâncias por hora durante a semana e **6** instâncias por hora durante os finais de semana.

Se vários **planos do Serviço de Aplicativo** estiverem sendo hospedados em um **pool de trabalho**, a **taxa total de inflação** precisará ser calculada, e isso pode ser expresso como a *soma* da taxa de inflação de todos os **planos do Serviço de Aplicativo** hospedados nesse **pool de trabalho**.

![][ASP-Total-Inflation]

###Usar a taxa de inflação de plano de Serviço de Aplicativo para definir regras de dimensionamento automático do pool de trabalho
Os **pools de trabalho** que hospedam **planos de Serviço de Aplicativo** configurados para serem dimensionados automaticamente precisarão ter um buffer de capacidade alocado para permitir que as operações de dimensionamento automático aumentem/reduzam o **plano de Serviço de Aplicativo** conforme necessário. O buffer mínimo seria a **taxa de inflação total do plano de Serviço de Aplicativo** calculada.

Como as operações de dimensionamento do **ambiente de Serviço de Aplicativo** demoram algum tempo para serem aplicadas, qualquer alteração deve levar em consideração outras alterações sob demanda que poderiam acontecer enquanto uma operação de escala está em andamento. Para isso, recomendamos usar o **plano de Serviço de Aplicativo** como o número mínimo de instâncias adicionado a cada operação de dimensionamento automático.

Com essas informações, Matheus pode definir o seguinte perfil e regras de dimensionamento automático

![][Worker-Pool-Scale]

|	**Perfil de dimensionamento automático – Dias da semana** |	**Perfil de dimensionamento automático – Finais de semana** |
|	----------------------------------------------------	|	--------------------------------------------	|
|	**Nome:** Perfil Dia da semana |	**Nome:** Perfil Final de semana |
|	**Dimensionar por:** Regras de agendamento e desempenho |	**Dimensionar por:** Regras de agendamento e desempenho |
|	**Perfil:** Dias da semana |	**Perfil:** Final de semana |
|	**Tipo:** recorrência |	**Tipo:** recorrência |
|	**Intervalo de destino:** 13 a 25 instâncias |	**Intervalo de destino:** 6 a 15 instâncias |
|	**Dias:** segunda-feira, terça-feira, quarta-feira, quinta-feira, sexta-feira |	**Dias:** sábado e domingo |
|	**Hora de início:** 7:00 |	**Hora de início:** 9:00 |
|	**Fuso horário:** UTC-08 |	**Fuso horário:** UTC-08 |
| | |
|	**Regra de dimensionamento automático (escalar verticalmente)** |	**Regra de dimensionamento automático (escalar verticalmente)** |
|	**Recurso:** Pool de trabalho 1 |	**Recurso:** Pool de trabalho 1 |
|	**Métrica:** WorkersAvailable |	**Métrica:** WorkersAvailable |
|	**Operação:** menos de 8 |	**Operação:** menos de 3 |
|	**Duração:** 20 minutos |	**Duração:** 30 minutos |
|	**Agregação de tempo:** Média |	**Agregação de tempo:** Média |
|	**Ação:** Aumentar a contagem em 8 |	**Ação:** Aumentar a contagem em 3 |
|	**Tempo de resfriamento (minutos):** 180 |	**Tempo de resfriamento (minutos):** 180 |
| | |
|	**Regra de dimensionamento automático (reduzir verticalmente)** |	**Regra de dimensionamento automático (reduzir verticalmente)** |
|	**Recurso:** Pool de trabalho 1 |	**Recurso:** Pool de trabalho 1 |
|	**Métrica:** WorkersAvailable |	**Métrica:** WorkersAvailable |
|	**Operação:** maior que 8 |	**Operação:** maior que 3 |
|	**Duração:** 20 minutos |	**Duração:** 15 minutos |
|	**Agregação de tempo:** Média |	**Agregação de tempo:** Média |
|	**Ação:** Diminuir a contagem em 2 |	**Ação:** Diminuir a contagem em 3 |
|	**Tempo de resfriamento (minutos):** 120 |	**Tempo de resfriamento (minutos):** 120 |

O intervalo de Destino definido no perfil é calculado pelas instâncias do mínimas definidas no perfil para o **plano de Serviço de Aplicativo** + buffer.

O intervalo máximo seria a soma de todos os intervalos máximos de todos os **planos de Serviço de Aplicativo** hospedados no **pool de trabalho**.

A contagem de Aumento das regras de escala vertical deve ser definida como, pelo menos, 1X a **Taxa de Inflação do Plano do Serviço de Aplicativo** para a escala vertical.

A contagem de redução pode ser ajustada para algo entre 1/2x ou 1x a **taxa de inflação do plano de Serviço de Aplicativo** para reduzir verticalmente.

###Dimensionamento automático do pool de front-end
As regras de dimensionamento automático de **Front-end** são mais simples do que para os **pools de trabalho**. As principais coisas a observar são: garantir que a duração da medição e os temporizadores de resfriamento levem em consideração o fato de que as operações de dimensionamento em um **plano de Serviço de Aplicativo** não são instantâneas.

Para este cenário, Matheus sabe que a taxa de erro aumenta quando os front-ends atingem 80% de utilização de CPU. Para evitar isso, ele define a regra de dimensionamento automático para aumentar o número de instâncias da seguinte maneira:
 
![][Front-End-Scale]
  
|	**Perfil de dimensionamento automático – Front-ends** |
|	--------------------------------------------	|
|	**Nome:** Dimensionamento automático – Front-ends |
|	**Dimensionar por:** Regras de agendamento e desempenho |
|	**Perfil:** Todos os dias |
|	**Tipo:** recorrência |
|	**Intervalo de destino:** 3 a 10 instâncias |
|	**Dias:** Todos os dias |
|	**Hora de início:** 9:00 |
|	**Fuso horário:** UTC-08 |
| |
|	**Regra de dimensionamento automático (escalar verticalmente)** |
|	**Recurso:** Pool de front-end |
|	**Métrica:** % da CPU |
|	**Operação:** Mais de 60% |
|	**Duração:** 20 minutos |
|	**Agregação de tempo:** Média |
|	**Ação:** Aumentar a contagem em 3 |
|	**Tempo de resfriamento (minutos):** 120 |
| |
|	**Regra de dimensionamento automático (reduzir verticalmente)** |
|	**Recurso:** Pool de trabalho 1 |
|	**Métrica:** % da CPU |
|	**Operação:** Menos de 30% |
|	**Duração:** 20 minutos |
|	**Agregação de tempo:** Média |
|	**Ação:** Diminuir a contagem em 3 |
|	**Tempo de resfriamento (minutos):** 120 |

<!-- IMAGES -->
[intro]: ./media/app-service-environment-auto-scale/introduction.png
[settings-scale]: ./media/app-service-environment-auto-scale/settings-scale.png
[scale-manual]: ./media/app-service-environment-auto-scale/scale-manual.png
[scale-profile]: ./media/app-service-environment-auto-scale/scale-profile.png
[scale-profile2]: ./media/app-service-environment-auto-scale/scale-profile-2.png
[scale-rule]: ./media/app-service-environment-auto-scale/scale-rule.png
[asp-scale]: ./media/app-service-environment-auto-scale/asp-scale.png
[ASP-Inflation]: ./media/app-service-environment-auto-scale/asp-inflation-rate.png
[Equation1]: ./media/app-service-environment-auto-scale/equation1.png
[Equation2]: ./media/app-service-environment-auto-scale/equation2.png
[Equation3]: ./media/app-service-environment-auto-scale/equation3.png
[Equation4]: ./media/app-service-environment-auto-scale/equation4.png
[ASP-Total-Inflation]: ./media/app-service-environment-auto-scale/asp-total-inflation-rate.png
[Worker-Pool-Scale]: ./media/app-service-environment-auto-scale/wp-scale.png
[Front-End-Scale]: ./media/app-service-environment-auto-scale/fe-scale.png

<!------HONumber=AcomDC_0302_2016-->