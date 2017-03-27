---
title: "Dimensionar automaticamente os nós de computação em um pool de Lote do Azure | Microsoft Docs"
description: "Habilite o dimensionamento automático em um pool de nuvem para ajustar dinamicamente o número de nós de computação no pool."
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: c624cdfc-c5f2-4d13-a7d7-ae080833b779
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: multiple
ms.date: 02/27/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 2c9877f84873c825f96b62b492f49d1733e6c64e
ms.openlocfilehash: 9dbfa813ea64666779f1f85b3ccda2b4fa1a755b
ms.lasthandoff: 03/15/2017


---
# <a name="create-an-automatic-scaling-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Criar uma fórmula de dimensionamento automático para expandir nós de computação em um pool do Lote

Com o dimensionamento automático, o serviço de Lote do Azure pode adicionar ou remover dinamicamente os nós do computador em um pool baseado nos parâmetros definidos. Você pode economizar potencialmente tempo e dinheiro ajustando automaticamente a quantidade de energia do computador usada por seu aplicativo - adicione nós quando as demandas de tarefa do seu trabalho aumentarem e remova-os quando diminuírem.

Você pode habilitar o dimensionamento automático em um pool de nós de computação associando-o a uma *fórmula de dimensionamento automático* que você definir, como com o método [PoolOperations.EnableAutoScale][net_enableautoscale] na biblioteca [.NET do Lote](batch-dotnet-get-started.md). O serviço Lote usa então essa fórmula para determinar o número de nós de computação que são necessários para executar a carga de trabalho. O Lote responde aos exemplos de dados de métrica de serviço coletados periodicamente e ajusta o número de nós de computação em um pool em um intervalo configurável baseado na fórmula associada.

Você pode habilitar o dimensionamento automático quando um pool é criado ou em um pool existente. Você também pode alterar uma fórmula existente em um pool que tenha o "dimensionamento automático" habilitado. O Lote fornece a capacidade de avaliar as fórmulas antes de atribuí-las aos pools, bem como monitorar o status das execuções de dimensionamento automático.

## <a name="automatic-scaling-formulas"></a>Fórmulas de dimensionamento automático
Uma fórmula de dimensionamento automático é um valor de cadeia de caracteres que você define, que contém uma ou mais instruções que são atribuídas ao elemento [autoScaleFormula][rest_autoscaleformula] (REST do Lote) do pool ou à propriedade [CloudPool.AutoScaleFormula][net_cloudpool_autoscaleformula] (.NET do Lote). Ao ser atribuído a um pool, o serviço do Lote usa sua fórmula para determinar o número de destino dos nós de computação no pool para o próximo intervalo de processamento (você verá mais sobre intervalos mais adiante). A cadeia de caracteres da fórmula não pode ter mais de 8 KB, pode incluir até cem instruções que são separadas por ponto e vírgula e pode incluir quebras de linha e comentários.

Você pode pensar nas fórmulas de dimensionamento automático como se estivesse usando uma "linguagem" de dimensionamento automático do Lote. As instruções da fórmula são expressões de forma livre que podem incluir as variáveis definidas pelo serviço (variáveis definidas pelo serviço de Lote) e as variáveis definidas pelo usuário (variáveis que você define). Eles podem executar várias operações com esses valores usando tipos, operadores e funções internas. Por exemplo, uma instrução pode ter a seguinte forma:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Geralmente, as fórmulas contêm várias instruções que executam operações nos valores que são obtidos nas instruções anteriores. Por exemplo, primeiro, obtemos um valor para `variable1`, em seguida, passamos para uma função para preencher `variable2`:

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Com as instruções em sua fórmula, seu objetivo é atingir um número de nós de computação para o qual o pool deve ser dimensionado -- o número de **destino** dos **nós dedicados**. Esse número pode ser maior, menor ou igual ao número atual de nós no pool. O lote avalia a fórmula de dimensionamento automático do pool em um intervalo específico ([os intervalos de dimensionamento automático](#automatic-scaling-interval) são analisados abaixo). Em seguida, ele ajustará o número alvo de nós no pool para o número especificado pela fórmula de dimensionamento no momento da avaliação.

Como um exemplo rápido, essa fórmula de dimensionamento automático de duas linhas especifica que o número de nós deve ser ajustado de acordo com o número de tarefas ativas, até um máximo de 10 nós de computação:

```
$averageActiveTaskCount = avg($ActiveTasks.GetSample(TimeInterval_Minute * 15));
$TargetDedicated = min(10, $averageActiveTaskCount);
```

As próximas seções deste artigo abordam as diversas entidades que vão compor as fórmulas de dimensionamento automático, incluindo variáveis, operadores, operações e funções. Você descobrirá como obter vários recursos de computação e métricas de tarefa no Lote. Você pode usar essas métricas para ajustar a contagem de nós do seu pool baseada no uso de recursos e no status da tarefa. Você aprenderá como construir uma fórmula e habilitar o dimensionamento automático em um pool usando APIs de Lote .NET e REST. Vamos concluir usando algumas fórmulas de exemplo.

> [!IMPORTANT]
> Cada conta do Lote do Azure fica limitada a um número máximo de nós (portanto, os nós de computação) que podem ser usados para o processamento. O serviço de Lote criará nós apenas até esse limite básico. Por isso, ele não pode atingir o número de destino dos nós de computação que é especificado por uma fórmula. Consulte [Cotas e limites para o serviço do Lote do Azure](batch-quota-limit.md) para obter informações sobre como exibir e aumentar as cotas da conta.
> 
> 

## <a name="variables"></a>Variáveis
Você pode usar as variáveis **definidas pelo serviço** e **definidas pelo usuário** em suas fórmulas de dimensionamento automático. As variáveis definidas pelo serviço são criadas no serviço de Lote – algumas são de leitura-gravação e outras são de somente leitura. As variáveis definidas pelo usuário são as variáveis que *você* define. Na fórmula de exemplo com duas linhas acima, `$TargetDedicated` é uma variável definida pelo sistema, enquanto `$averageActiveTaskCount` é definida pelo usuário.

As tabelas a seguir mostram as variáveis de leitura/gravação e somente leitura que são definidas pelo serviço de Lote.

Você pode **obter** e **definir** os valores dessas variáveis definidas pelo serviço para gerenciar o número de nós de computação em um pool:

| Variáveis definidas pelo serviço de leitura/gravação | Descrição |
| --- | --- |
| $TargetDedicated |O número de **destino** de **nós de computação dedicados** para o pool. Esse é o número de nós de computação para o qual o pool deve ser dimensionado. Trata-se de um número "alvo", uma vez que é possível que um pool não atinja o número alvo de nós. Isso pode ocorrer se o número alvo de nós for modificado novamente por uma avaliação de dimensionamento automático subsequente antes do pool atingir a meta inicial. Isso também acontece ao atingir uma cota principal ou de nó da conta do Lote antes de atingir o número alvo de nós. |
| $NodeDeallocationOption |A ação que ocorre quando nós de computação são removidos de um pool. Os valores possíveis são:<ul><li>**requeue** – finaliza tarefas imediatamente e as coloca novamente na fila de trabalhos para que elas sejam reagendadas.<li>**terminate** – finaliza tarefas imediatamente e as remove da fila de trabalhos.<li>**taskcompletion** – aguarda que as tarefas em execução sejam concluídas e remove o nó do pool.<li>**retaineddata** - aguarda que todos os dados de tarefas locais retidos no nó sejam limpos antes de remover o nó do pool.</ul> |

Você pode **obter** o valor dessas variáveis definidas pelo serviço para fazer ajustes com base nas métricas do serviço de Lote:

| Variáveis somente leitura definidas pelo serviço | Descrição |
| --- | --- |
| $CPUPercent |O percentual médio de utilização da CPU. |
| $WallClockSeconds |O número de segundos consumidos. |
| $MemoryBytes |O número médio de megabytes usados. |
| $DiskBytes |O número médio de gigabytes usado nos discos locais. |
| $DiskReadBytes |O número de bytes lidos. |
| $DiskWriteBytes |O número de bytes gravados. |
| $DiskReadOps |A contagem de operações de leitura de disco executadas. |
| $DiskWriteOps |A contagem de operações de gravação em disco executadas. |
| $NetworkInBytes |O número de bytes de entrada. |
| $NetworkOutBytes |O número de bytes de saída. |
| $SampleNodeCount |A contagem de nós de computação. |
| $ActiveTasks |O número de tarefas que estão em estado ativo. |
| $RunningTasks |O número de tarefas em estado de execução. |
| $PendingTasks |A soma de $ActiveTasks e $RunningTasks. |
| $SucceededTasks |O número de tarefas que foram concluídas com êxito. |
| $FailedTasks |O número de tarefas que falharam. |
| $CurrentDedicated |O número atual de nós de computação dedicados. |

> [!TIP]
> As variáveis definidas pelo serviço e de somente leitura mostradas acima são *objetos* que fornecem vários métodos para acessar os dados associados a cada uma. Consulte abaixo [Obter dados de exemplo](#getsampledata) para obter mais informações.
> 
> 

## <a name="types"></a>Tipos
Esses **tipos** têm suporte em uma fórmula.

* double
* doubleVec
* doubleVecList
* string
* timestamp - timestamp é uma estrutura composta que contém os seguintes elementos:
  
  * year
  * month (1-12)
  * day (1-31)
  * weekday (no formato numérico, por exemplo, 1 para segunda-feira)
  * hour (no formato numérico de 24 horas, por exemplo, 13h significa 1h da tarde)
  * minute (00-59)
  * second (00-59)
* timeinterval
  
  * TimeInterval_Zero
  * TimeInterval_100ns
  * TimeInterval_Microsecond
  * TimeInterval_Millisecond
  * TimeInterval_Second
  * TimeInterval_Minute
  * TimeInterval_Hour
  * TimeInterval_Day
  * TimeInterval_Week
  * TimeInterval_Year

## <a name="operations"></a>Operações
Essas **operações** são permitidas nos tipos listados acima.

| Operação | Operadores com suporte | Tipo de resultado |
| --- | --- | --- |
| double *operador* double |+, -, *, / |double |
| double *operador* timeinterval |* |timeinterval |
| doubleVec *operador* double |+, -, *, / |doubleVec |
| doubleVec *operador* doubleVec |+, -, *, / |doubleVec |
| timeinterval *operador* double |*, / |timeinterval |
| timeinterval *operador* timeinterval |+, - |timeinterval |
| timeinterval *operador* timestamp |+ |timestamp |
| timestamp *operador* timeinterval |+ |timestamp |
| timestamp *operador* timestamp |- |timeinterval |
| *operador*double |-, ! |double |
| *operador*timeinterval |- |timeinterval |
| double *operador* double |<, <=, ==, >=, >, != |double |
| string *operador* string |<, <=, ==, >=, >, != |double |
| timestamp *operador* timestamp |<, <=, ==, >=, >, != |double |
| timeinterval *operador* timeinterval |<, <=, ==, >=, >, != |double |
| double *operador* double |&&, &#124;&#124; |double |

Ao testar um double com um operador ternário (`double ? statement1 : statement2`), um item diferente de zero é **true** e zero é **false**.

## <a name="functions"></a>Funções
Essas **funções** predefinidas estão disponíveis para usar na definição de uma fórmula de dimensionamento automático.

| Função | Tipo de retorno | Descrição |
| --- | --- | --- |
| avg(doubleVecList) |double |Retorna o valor médio de todos os valores em doubleVecList. |
| len(doubleVecList) |double |Retorna o comprimento do vetor criado por meio de doubleVecList. |
| lg(double) |double |Retorna o logaritmo na base 2 do double. |
| lg(doubleVecList) |doubleVec |Retorna o logaritmo de componentwise na base 2 de doubleVecList. Um vec(double) deve ser passado explicitamente para o parâmetro. Caso contrário, a versão double lg(double) será assumida. |
| ln(double) |double |Retorna o logaritmo natural do double. |
| ln(doubleVecList) |doubleVec |Retorna o logaritmo de componentwise na base 2 de doubleVecList. Um vec(double) deve ser passado explicitamente para o parâmetro. Caso contrário, a versão double lg(double) será assumida. |
| log(double) |double |Retorna o logaritmo na base 10 do double. |
| log(doubleVecList) |doubleVec |Retorna o logaritmo de componentwise na base 10 de doubleVecList. Um vec (double) deve ser passado explicitamente para o parâmetro double único. Caso contrário, a versão double log(double) será assumida. |
| max(doubleVecList) |double |Retorna o valor máximo em doubleVecList. |
| min(doubleVecList) |double |Retorna o valor mínimo em doubleVecList. |
| norm(doubleVecList) |double |Retorna a norma dupla do vetor criado por meio de doubleVecList. |
| percentile(doubleVec v, double p) |double |Retorna o elemento percentil do vetor v. |
| rand() |double |Retorna um valor aleatório entre 0,0 e 1,0. |
| range(doubleVecList) |double |Retorna a diferença entre os valores mínimo e máximo em doubleVecList. |
| std(doubleVecList) |double |Retorna o desvio padrão da amostra dos valores em doubleVecList. |
| stop() | |Interrompe a avaliação da expressão de dimensionamento automático. |
| sum(doubleVecList) |double |Retorna a soma de todos os componentes em doubleVecList. |
| time(string dateTime="") |timestamp |Retorna o carimbo de data/hora do horário atual se nenhum parâmetro for passado, ou o carimbo de data/hora da cadeia de caracteres dateTime se algum parâmetro passar. Os formatos de dateTime com suporte são W3C-DTF e RFC1123. |
| val(doubleVec v, double i) |double |Retorna o valor do elemento que está no local i do vetor v com um índice inicial de zero. |

Algumas das funções descritas na tabela acima podem aceitar uma lista como argumento. A lista separada por vírgulas é qualquer combinação de *double* e *doubleVec*. Por exemplo:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

O valor de *doubleVecList* é convertido em um único *doubleVec* antes da avaliação. Por exemplo, se `v = [1,2,3]`, então chamar `avg(v)` equivale a chamar `avg(1,2,3)`. Chamar `avg(v, 7)` é equivalente a chamar `avg(1,2,3,7)`.

## <a name="getsampledata"></a>Obter dados de exemplo
As fórmulas de dimensionamento automático atuam em dados de métricas (exemplos) que são fornecidos pelo serviço de Lote. Uma fórmula aumenta ou diminui o tamanho do pool com base nos valores obtidos do serviço. As variáveis definidas pelo serviço descritas acima são objetos que fornecem vários métodos para acessar os dados associados ao objeto. Por exemplo, a expressão a seguir mostra uma solicitação para obter os últimos cinco minutos de uso da CPU:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

| Método | Descrição |
| --- | --- |
| GetSample() |O método `GetSample()` retorna um vetor de exemplos de dados.<br/><br/>Uma amostra é de 30 segundos de dados de métrica. Em outras palavras, os exemplos são obtidos a cada 30 segundos. Mas, conforme mencionado abaixo, há um atraso entre o momento em que uma amostra é coletada e o momento em que ela fica disponível para uma fórmula. Como tal, é possível que nem todas as amostras para um determinado período de tempo estejam disponíveis para avaliação por uma fórmula.<ul><li>`doubleVec GetSample(double count)`<br/>Especifica o número de amostras a serem obtidas dos exemplos mais recentes que foram coletados.<br/><br/>`GetSample(1)` retorna a última amostra disponível. No entanto, para métricas como `$CPUPercent`, isso não deve ser usado porque é impossível saber *quando* a amostra foi coletada. Pode ser recente ou, devido a problemas do sistema, muito mais antigo. É melhor nesses casos usar um intervalo de tempo, conforme mostrado abaixo.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>Especifica um intervalo de tempo para coleta de dados de exemplo. Opcionalmente, também especifica a porcentagem de amostras que devem estar disponíveis no período de tempo solicitado.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10)` retornaria 20 amostras caso todas as amostras dos últimos dez minutos estivessem presentes no histórico de CPUPercent. No entanto, se o último minuto do histórico não estivesse disponível, apenas 18 exemplos seriam retornados. Nesse caso:<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` falhará porque apenas 90% dos exemplos estão disponíveis.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` teria êxito.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/>Especifica um intervalo de tempo para coleta de dados, com uma hora de início e uma hora de término.<br/><br/>Conforme mencionado acima, há um atraso entre quando uma amostra é coletada e quando ela está disponível para uma fórmula. Isso deve ser considerado quando você usar o método `GetSample` . Veja `GetSamplePercent` abaixo. |
| GetSamplePeriod() |Retorna o período das amostras que foram colhidas de um conjunto de dados históricos de exemplo. |
| Count() |Retorna o número total de amostras no histórico da métrica. |
| HistoryBeginTime() |Retorna o carimbo de data/hora da amostra de dados mais antiga disponível para a métrica. |
| GetSamplePercent() |Retorna a porcentagem de exemplos disponíveis para um determinado intervalo de tempo. Por exemplo:<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>Como o método `GetSample` falhará se o percentual de amostras retornadas for menor do que o `samplePercent` especificado, você poderá usar o método `GetSamplePercent` para verificar primeiro. Em seguida, você pode executar uma ação alternativa se não houver exemplos suficientes, sem interromper a avaliação do dimensionamento automático. |

### <a name="samples-sample-percentage-and-the-getsample-method"></a>Exemplos, porcentagem de exemplo e método *GetSample()*
A operação principal de uma fórmula de dimensionamento automático é obter os dados de métrica de tarefa e recurso e ajustar o tamanho do pool baseado nesses dados. Assim, é importante ter um claro entendimento de como as fórmulas de dimensionamento automático interagem com dados de métricas, ou "exemplos".

**Exemplos**

O serviço de Lote obtém periodicamente *exemplos* de métricas das tarefas e recursos, e disponibiliza-os para suas fórmulas de dimensionamento automático. Esses exemplos são gravados a cada 30 segundos pelo serviço de Lote. No entanto, normalmente há alguma latência que causa um atraso entre quando esses exemplos foram gravados e quando eles são disponibilizados para as fórmulas de dimensionamento automático (e podem ser lidos por elas). Além disso, devido a vários fatores, como rede ou outros problemas de infraestrutura, os exemplos podem não ter sido gravados em um intervalo específico. Isso resulta em exemplos "ausentes".

**Exemplo de porcentagem**

Quando um `samplePercent` é passado para o método `GetSample()` ou o método `GetSamplePercent()` é chamado, "porcentagem" se refere a uma comparação entre o número total *possível* de exemplos gravados pelo serviço de Lote e o número de exemplos que estão realmente *disponíveis* para sua fórmula de dimensionamento automático.

Vamos examinar um período de 10 minutos como exemplo. Como os exemplos são gravados a cada 30 segundos, em um período de 10 minutos, o número total máximo de exemplos gravados pelo Lote é de 20 exemplos (2 por minuto). No entanto, devido à latência inerente do mecanismo de relatório ou a algum outro problema na infraestrutura do Azure, pode haver apenas 15 exemplos disponíveis para a fórmula de dimensionamento automático para leitura. Isso significa que, para esse período de 10 minutos, apenas **75%** do número total de exemplos gravados estão, de fato, disponíveis para a fórmula.

**GetSample() e intervalos de exemplo**

As fórmulas de dimensionamento automático ampliarão ou reduzirão seus pools, adicionando ou removendo nós. Já que nós custam dinheiro, você deve fazer com que suas fórmulas usem um método inteligente de análise baseado em dados suficientes. Portanto, recomendamos que você use uma análise de tipo de tendência em suas fórmulas. Esse tipo aumentará ou reduzirá os pools com base em um *intervalo* de exemplos coletados.

Para tanto, use `GetSample(interval look-back start, interval look-back end)` para retornar um **vetor** de exemplos:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Quando a linha acima é avaliada pelo Lote, ela retorna um intervalo de exemplos como um vetor de valores. Por exemplo:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

Depois de coletar o vetor de exemplos, você poderá usar funções como `min()`, `max()` e `avg()` para derivar valores significativos do intervalo coletado.

Para ter mais segurança, você pode forçar uma avaliação de fórmula a *falhar* se menos de uma determinada porcentagem de exemplo estiver disponível por um período de tempo específico. Ao forçar uma avaliação de fórmula a falhar, você orienta o Lote a interromper outras avaliações da fórmula se a porcentagem especificada de exemplos não está disponível, e nenhuma mudança é feita no tamanho do pool. Para especificar uma porcentagem requerida de exemplos para que a avaliação seja bem-sucedida, especifique-a como o terceiro parâmetro para `GetSample()`. Aqui, é especificado um requisito de 75% de exemplos:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Também é importante, devido ao atraso mencionado anteriormente na disponibilidade do exemplo, sempre especificar um intervalo de tempo com uma hora de início anterior a um minuto. Isso porque leva aproximadamente um minuto para que os exemplos se propaguem no sistema, portanto, os exemplos no intervalo `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` muitas vezes não estarão disponíveis. Repetindo, você pode usar o parâmetro de porcentagem de `GetSample()` para forçar um requisito de porcentagem de exemplo específico.

> [!IMPORTANT]
> **Recomendamos expressamente** que você **evite contar*somente* com `GetSample(1)` em suas fórmulas de dimensionamento automático**. Isso porque `GetSample(1)` basicamente informa ao serviço de Lote: "Passe-me o último exemplo que você tem, não importa há quanto tempo o tem". Como se trata apenas de único exemplo, e pode ser um exemplo antigo, ele pode não representar o retrato mais amplo do estado da tarefa ou do recurso. Se você for mesmo usar `GetSample(1)`, verifique se faz parte de uma instrução mais ampla e não apenas do ponto de dado do qual sua fórmula depende.
> 
> 

## <a name="metrics"></a>Métricas
Você pode usar as métricas do **recurso** e da **tarefa** quando estiver definindo uma fórmula. Você ajustará o número alvo de nós dedicados no pool com base nos dados de métrica que você obtiver e avaliar. Consulte a seção acima [Variáveis](#variables) para obter mais informações sobre cada métrica.

<table>
  <tr>
    <th>Métrica</th>
    <th>Descrição</th>
  </tr>
  <tr>
    <td><b>Recurso</b></td>
    <td><p>As <b>Métricas de recurso</b> se baseiam no uso de CPU, largura de banda e memória dos nós de computação, bem como no número de nós.</p>
        <p> Essas variáveis definidas pelo serviço são úteis para fazer ajustes com base na contagem de nós:</p>
    <p><ul>
      <li>$TargetDedicated</li>
            <li>$CurrentDedicated</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>Estas variáveis definidas pelo serviço são úteis para fazer ajustes com base no uso de recursos do nó:</p>
    <p><ul>
      <li>$CPUPercent</li>
      <li>$WallClockSeconds</li>
      <li>$MemoryBytes</li>
      <li>$DiskBytes</li>
      <li>$DiskReadBytes</li>
      <li>$DiskWriteBytes</li>
      <li>$DiskReadOps</li>
      <li>$DiskWriteOps</li>
      <li>$NetworkInBytes</li>
      <li>$NetworkOutBytes</li></ul></p>
  </tr>
  <tr>
    <td><b>Tarefa</b></td>
    <td><p>As <b>Métricas de tarefa</b> se baseiam no status das tarefas, como Ativa, Pendente e Concluída. As variáveis definidas pelo serviço a seguir são úteis para fazer ajustes no tamanho do pool com base nas métricas da tarefa:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="write-an-autoscale-formula"></a>Criar uma fórmula de autoescala
Crie uma fórmula de autoescala formando instruções que usam os componentes acima e combine essas instruções em uma fórmula completa. Nesta seção, vamos criar uma fórmula de autoescala de exemplo que pode executar algumas decisões de dimensionamento do mundo real.

Primeiro, vamos definir os requisitos para nossa nova fórmula de autoescala. A fórmula deve:

1. **Aumente** o número alvo de nós de computação em um pool se o uso de CPU estiver alto.
2. **Reduza** o número alvo de nós de computação em um pool quando o uso de CPU estiver baixo.
3. Restringir sempre o número **máximo** de nós a 400.

Para *aumentar* o número de nós durante o uso elevado da CPU, definimos uma instrução que preenche uma variável definida pelo usuário (`$totalNodes`) com um valor que é 110% do número de destino de nós atual, ,mas somente caso a utilização mínima média da CPU durante os últimos 10 minutos tenha estado acima de 70%: Caso contrário, usamos o valor dedicado atual.

```
$totalNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicated * 1.1) : $CurrentDedicated;
```

Para *diminuir* o número de nós durante a baixa utilização da CPU, a próxima instrução em nossa fórmula define a mesma variável `$totalNodes` para 90% do número de destino atual de nós, se o uso de CPU médio nos últimos 60 minutos foi inferior a 20%. Caso contrário, use o valor atual de `$totalNodes` que é populado na instrução acima.

```
$totalNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicated * 0.9) : $totalNodes;
```

Agora, limite o número de destino de nós de computação dedicados para um **máximo** de 400:

```
$TargetDedicated = min(400, $totalNodes)
```

Aqui está a fórmula completa:

```
$totalNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicated * 1.1) : $CurrentDedicated;
$totalNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicated * 0.9) : $totalNodes;
$TargetDedicated = min(400, $totalNodes)
```

## <a name="create-an-autoscale-enabled-pool"></a>Criar um pool habilitado para autoescala
Para criar um novo pool com a autoescala habilitada, você pode usar uma das seguintes técnicas:

**.NET do Lote**

1. Crie o pool com [BatchClient.PoolOperations.CreatePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx).
2. Defina a propriedade [CloudPool.AutoScaleEnabled](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleenabled.aspx) como `true`.
3. Defina a propriedade [CloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx) com a fórmula de autoescala.
4. (Opcional) Defina a propriedade [CloudPool.AutoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoScaleevaluationinterval.aspx) (o padrão é de 15 minutos).
5. Confirmar o pool com [CloudPool.Commit](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.commit.aspx) ou [CommitAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.commitasync.aspx).

**API REST do Lote**

* [Adicionar um pool a uma conta](https://msdn.microsoft.com/library/azure/dn820174.aspx): especifique os elementos `enableAutoScale` e `autoScaleFormula` na solicitação de API REST para configurar a autoescala para um pool quando você criá-lo.

O trecho de código a seguir cria um pool de habilitado para dimensionamento automático usando a biblioteca [.NET do Lote][net_api]. A fórmula de dimensionamento automático do pool define o número alvo de nós para cinco às segundas-feiras e um em todos os outros dias da semana. O [intervalo de autoescala](#automatic-scaling-interval) é definido como 30 minutos. Neste e nos outros trechos do C# neste artigo, "myBatchClient" é uma instância corretamente inicializada de [BatchClient][net_batchclient].

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool("mypool", "3", "small");
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicated = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
pool.Commit();
```

Além da API REST do Lote e do SDK do .NET, você pode usar qualquer um dos outros [SDKs do Lote](batch-apis-tools.md#batch-development-apis), [cmdlets do Lote do PowerShell](batch-powershell-cmdlets-get-started.md) e a [CLI do Lote](batch-cli-get-started.md) para trabalhar com a autoescala.

> [!IMPORTANT]
> Ao criar um pool habilitado para autoescala, você **não** deve especificar o parâmetro `targetDedicated`. Além disso, se você desejar redimensionar manualmente um pool habilitado para dimensionamento automático (por exemplo, com [BatchClient.PoolOperations.ResizePool][net_poolops_resizepool]), primeiramente será preciso **desabilitar** o dimensionamento automático no pool e, depois, redimensioná-lo.
> 
> 

### <a name="automatic-scaling-interval"></a>Intervalo de dimensionamento automático
Por padrão, o serviço de Lote ajusta o tamanho do pool de acordo com sua fórmula de dimensionamento automático a cada **15 minutos**. Esse intervalo é configurável, no entanto, usando as seguintes propriedades do pool:

* [CloudPool.AutoScaleEvaluationInterval][net_cloudpool_autoscaleevalinterval] (.NET do Lote)
* [autoScaleEvaluationInterval][rest_autoscaleinterval] (API REST)

O intervalo mínimo é de cinco minutos e o máximo é de 168 horas. Se um intervalo fora desse intervalo for especificado, o serviço de Lote retornará um erro de solicitação incorreta (400).

> [!NOTE]
> Atualmente, o dimensionamento automático não está projetado para responder a alterações em menos de um minuto, mas para ajustar o tamanho de seu pool gradativamente conforme você executa uma carga de trabalho.
> 
> 

## <a name="enable-autoscaling-on-an-existing-pool"></a>Habilitar autoescala em um pool existente
Se já tiver criado um pool com um número definido de nós de computação usando o parâmetro *targetDedicated*, você ainda poderá habilitar a autoescala no pool. Cada SDK do Lote fornece uma operação "habilitar autoescala", por exemplo:

* [BatchClient.PoolOperations.EnableAutoScale][net_enableautoscale] (.NET do Lote)
* [Habilitar o dimensionamento automático em um pool][rest_enableautoscale] (API REST)

Quando você habilita a autoescala em um pool existente, o seguinte se aplica:

* Se a autoescala estiver atualmente **desabilitada** no pool quando você emitir a solicitação "habilitar autoescala", você *deverá* especificar uma fórmula de autoescala válida ao emitir a solicitação. *Opcionalmente*, você pode especificar um intervalo de avaliação do autoescala. Se você não especificar um intervalo, será usado o valor padrão de 15 minutos.
* Se a autoescala estiver atualmente **habilitada** no pool, você poderá especificar uma fórmula de autoescala, um intervalo de avaliação ou ambos. Não é possível omitir ambas as propriedades.
  
  * Se você especificar um novo intervalo de avaliação de autoescala, a agenda de avaliação existente será interrompida e uma nova agenda será iniciada. A hora de início do novo agendamento é a hora em que a solicitação "habilitar autoescala" foi emitida.
  * Se você omitir a fórmula de autoescala ou o intervalo de avaliação, o serviço de Lote continuará a usar o valor atual da configuração.

> [!NOTE]
> Se um valor foi especificado para o parâmetro *targetDedicated* quando o pool foi criado, tal valor é ignorado quando a fórmula de dimensionamento automático é avaliada.
> 
> 

Este trecho de código em C# usa a biblioteca [.NET do Lote][net_api] para habilitar o dimensionamento automático em um pool existente:

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicated = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>Atualizar uma fórmula de autoescala
Use a mesma solicitação "habilitar dimensionamento automático" para *atualizar* a fórmula em um pool existente com habilitado para dimensionamento automático (por exemplo, com [EnableAutoScale][net_enableautoscale] no .NET do Lote). Não há uma operação especial "atualizar autoescala". Por exemplo, se a autoescala já estiver habilitada em "myexistingpool" quando o código a seguir for executado, sua fórmula de autoescala será substituída pelo conteúdo de `myNewFormula`.

```csharp
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Atualizar o intervalo de autoescala
Assim como na atualização de uma fórmula de dimensionamento automático, use o mesmo método [EnableAutoScale][net_enableautoscale] para alterar o intervalo de avaliação de dimensionamento automático de um pool existente habilitado para dimensionamento automático. Por exemplo, para definir o intervalo de avaliação de autoescala como 60 minutos para um pool que já está habilitado para autoescala:

```csharp
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Avaliar uma fórmula de autoescala
Você pode avaliar uma fórmula antes de aplicá-la a um pool. Dessa forma, você pode fazer uma "execução de teste" da fórmula para ver como suas declarações são avaliadas antes de colocar a fórmula em produção.

Para avaliar uma fórmula de autoescala, você deve primeiro **habilitar a autoescala** no pool com uma **fórmula válida**. Se quiser testar uma fórmula em um pool que ainda não tem a autoescala habilitada, você poderá usar a fórmula de uma linha `$TargetDedicated = 0` ao habilitar a autoescala pela primeira vez. Depois, use um dos seguintes itens para avaliar a fórmula que você deseja testar:

* [BatchClient.PoolOperations.EvaluateAutoScale](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscale.aspx) ou [EvaluateAutoScaleAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscaleasync.aspx)
  
    Estes métodos .NET do Lote exigem a identificação de um pool existente e uma cadeia de caracteres que contém a fórmula de autoescala a ser avaliada. Os resultados da avaliação estão contidos na instância [AutoScaleEvaluation](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscaleevaluation.aspx) retornada.
* [Avaliar uma fórmula de autoescala](https://msdn.microsoft.com/library/azure/dn820183.aspx)
  
    Nessa solicitação da API REST, especifique a ID do pool no URI e a fórmula de autoescala no elemento *autoScaleFormula* do corpo da solicitação. A resposta da operação contém quaisquer informações de erro que possam estar relacionadas à fórmula.

Neste trecho de código do [.NET do Lote][net_api], avaliamos uma fórmula antes de aplicá-la a [CloudPool][net_cloudpool]. Se o pool não tiver a autoescala habilitada, poderemos habilitá-la primeiro.

```csharp
// First obtain a reference to an existing pool
CloudPool pool = batchClient.PoolOperations.GetPool("myExistingPool");

// If autoscaling isn't already enabled on the pool, enable it.
// You can't evaluate an autoscale formula on non-autoscale-enabled pool.
if (pool.AutoScaleEnabled == false)
{
    // We need a valid autoscale formula to enable autoscaling on the
    // pool. This formula is valid, but won't resize the pool:
    pool.EnableAutoScale(
        autoscaleFormula: $"$TargetDedicated = {pool.CurrentDedicated};",
        autoscaleEvaluationInterval: TimeSpan.FromMinutes(5));

    // Batch limits EnableAutoScale calls to once every 30 seconds.
    // Because we want to apply our new autoscale formula below if it
    // evaluates successfully, and we *just* enabled autoscaling on
    // this pool, we pause here to ensure we pass that threshold.
    Thread.Sleep(TimeSpan.FromSeconds(31));

    // Refresh the properties of the pool so that we've got the
    // latest value for AutoScaleEnabled
    pool.Refresh();
}

// We must ensure that autoscaling is enabled on the pool prior to
// evaluating a formula
if (pool.AutoScaleEnabled == true)
{
    // The formula to evaluate - adjusts target number of nodes based on
    // day of week and time of day
    string myFormula = @"
        $curTime = time();
        $workHours = $curTime.hour >= 8 && $curTime.hour < 18;
        $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
        $isWorkingWeekdayHour = $workHours && $isWeekday;
        $TargetDedicated = $isWorkingWeekdayHour ? 20:10;
    ";

    // Perform the autoscale formula evaluation. Note that this does not
    // actually apply the formula to the pool.
    AutoScaleRun eval =
        batchClient.PoolOperations.EvaluateAutoScale(pool.Id, myFormula);

    if (eval.Error == null)
    {
        // Evaluation success - print the results of the AutoScaleRun.
        // This will display the values of each variable as evaluated by the
        // autoscale formula.
        Console.WriteLine("AutoScaleRun.Results: " +
            eval.Results.Replace("$", "\n    $"));

        // Apply the formula to the pool since it evaluated successfully
        batchClient.PoolOperations.EnableAutoScale(pool.Id, myFormula);
    }
    else
    {
        // Evaluation failed, output the message associated with the error
        Console.WriteLine("AutoScaleRun.Error.Message: " +
            eval.Error.Message);
    }
}
```

Uma avaliação bem-sucedida da fórmula neste trecho de código resultará em uma saída semelhante à seguinte:

```
AutoScaleRun.Results:
    $TargetDedicated=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Obter informações sobre execuções de autoescala
Para garantir que a fórmula está sendo executada conforme o esperado, é recomendável que você verifique periodicamente os resultados de "execuções" de autoescala que o Lote executa em seu pool. Para isso, obtenha (ou atualize) uma referência para o pool e examine as propriedades de sua última execução de autoescala.

No .NET do Lote, a propriedade [CloudPool.AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscalerun.aspx) tem várias propriedades que fornecem informações sobre a mais recente execução de autoescala automática executada no pool pelo serviço de Lote.

* [AutoScaleRun.Timestamp](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.timestamp.aspx)
* [AutoScaleRun.Results](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.results.aspx)
* [AutoScaleRun.Error](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.error.aspx)

Na API REST, a solicitação [Obter informações sobre um pool](https://msdn.microsoft.com/library/dn820165.aspx) retorna informações sobre o pool, o que inclui as mais recentes informações de execução de autoescala em [autoScaleRun](https://msdn.microsoft.com/library/dn820165.aspx#bk_autrun).

O seguinte trecho de código em C# usa a biblioteca .NET do Lote para imprimir informações sobre a última autoescala executada no pool "myPool":

```csharp
Cloud pool = myBatchClient.PoolOperations.GetPool("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Exemplo de saída do trecho de código anterior:

```
Last execution: 10/14/2016 18:36:43
Result:
  $TargetDedicated=10;
  $NodeDeallocationOption=requeue;
  $curTime=2016-10-14T18:36:43.282Z;
  $isWeekday=1;
  $isWorkingWeekdayHour=0;
  $workHours=0
Error:
```

## <a name="example-autoscale-formulas"></a>Fórmulas de autoescala de exemplo
Vamos conferir algumas fórmulas que mostram diferentes maneiras de ajustar a quantidade de recursos de computação em um pool.

### <a name="example-1-time-based-adjustment"></a>Exemplo 1: ajuste baseado no tempo
Talvez você queira ajustar o tamanho do pool com base no dia da semana e hora do dia, para aumentar ou diminuir correspondentemente o número de nós no pool.

Esta fórmula obtém primeiramente a hora atual. Se trata-se de um dia da semana (1-5) e durante o horário de trabalho (8h-18h), o tamanho do pool de destino é definido como 20 nós. Caso contrário, ele é definido como 10 nós.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicated = $isWorkingWeekdayHour ? 20:10;
```

### <a name="example-2-task-based-adjustment"></a>Exemplo 2: ajuste baseado na tarefa
Neste exemplo, o tamanho do pool é ajustado com base no número de tarefas na fila. Observe que os comentários e quebras de linha são aceitáveis em cadeias de caracteres de fórmulas.

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1), avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicated/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicated = max(0, min($targetVMs, 20));
// Set node deallocation mode - keep nodes active only until tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>Exemplo 3: contagem de tarefas paralelas
Esse é outro exemplo que ajusta o tamanho do pool com base no número de tarefas. Essa fórmula também leva em conta o valor [MaxTasksPerComputeNode][net_maxtasks] que foi definido para o pool. Isso é especialmente útil nas situações em que a [execução de tarefas paralelas](batch-parallel-node-tasks.md) foi habilitada em seu pool.

```csharp
// Determine whether 70 percent of the samples have been recorded in the past
// 15 minutes; if not, use last sample
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks (the
// MaxTasksPerComputeNode property on this pool is set to 4, adjust this number
// for your use case)
$cores = $TargetDedicated * 4;
$extraVMs = (($tasks - $cores) + 3) / 4;
$targetVMs = ($TargetDedicated + $extraVMs);
// Attempt to grow the number of compute nodes to match the number of active
// tasks, with a maximum of 3
$TargetDedicated = max(0,min($targetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-4-setting-an-initial-pool-size"></a>Exemplo 4: definindo um tamanho de pool inicial
Esse exemplo mostra um trecho do código em C# com uma fórmula de dimensionamento automático que define o tamanho do pool para um determinado número de nós por um período de tempo inicial. Em seguida, ele ajusta o tamanho do pool com base no número de execução e tarefas ativas depois de decorrido o período de tempo inicial.

A fórmula no seguinte trecho de código:

* Define o tamanho do pool inicial como quatro nós
* Não ajusta o tamanho do pool nos primeiros 10 minutos do ciclo de vida do pool
* Após 10 minutos, obtém o valor máximo do número de tarefas ativas e em execução nos últimos 60 minutos
  * Se os dois valores forem 0 (indicando que nenhuma tarefa estava em execução ou ativa nos últimos 60 minutos) o tamanho do pool será definido como 0.
  * Se um dos valores for maior do que zero, nenhuma alteração será feita

```csharp
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"
    $TargetDedicated = {1};
    lifespan         = time() - time(""{0}"");
    span             = TimeInterval_Minute * 60;
    startup          = TimeInterval_Minute * 10;
    ratio            = 50;

    $TargetDedicated = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicated) : {1});
    ", now, 4);
```

## <a name="next-steps"></a>Próximas etapas
* [Maximizar o uso dos recursos de computação do Lote do Azure com tarefas simultâneas do nó](batch-parallel-node-tasks.md) contém detalhes sobre como executar várias tarefas simultaneamente nos nós de computação em seu pool. Além de dimensionamento automático, esse recurso pode ajudar a reduzir a duração do trabalho para algumas cargas de trabalho, economizando dinheiro.
* Para outro auxiliar de eficiência, certifique-se de que o aplicativo do Lote consulte o serviço de Lote da maneira ideal. Em [Consultar o serviço do Lote do Azure com eficiência](batch-efficient-list-queries.md), você aprenderá a limitar a quantidade de dados que passam por transmissão ao consultar o status de potencialmente milhares de nós de computação ou tarefas.

[net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_batchclient]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_autoscaleformula]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx
[net_cloudpool_autoscaleevalinterval]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval.aspx
[net_enableautoscale]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.enableautoscale.aspx
[net_maxtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[net_poolops_resizepool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.resizepool.aspx

[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_autoscaleformula]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[rest_autoscaleinterval]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[rest_enableautoscale]: https://msdn.microsoft.com/library/azure/dn820173.aspx

