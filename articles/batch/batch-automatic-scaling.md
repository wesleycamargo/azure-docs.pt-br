
<properties
	pageTitle="Dimensionar automaticamente nós de computação em um pool do Lote do Azure | Microsoft Azure"
	description="Habilite o dimensionamento automático em um pool de nuvem para ajustar dinamicamente o número de nós de computação no pool."
	services="batch"
	documentationCenter=""
	authors="mmacy"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="batch"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="multiple"
	ms.date="01/08/2015"
	ms.author="marsma"/>

# Dimensionar automaticamente nós de computação em um pool do Lote do Azure

O dimensionamento automático no Lote do Azure é o ajuste automático do poder de processamento usado pelo seu aplicativo, que adiciona ou remove dinamicamente nós de computação durante a execução do trabalho. Esse ajuste automático pode economizar tempo e dinheiro.

O dimensionamento automático é habilitado em um pool de nós de computação associando uma *fórmula de dimensionamento automático* ao pool, como acontece com o método [PoolOperations.EnableAutoScale][net_enableautoscale] na biblioteca [Batch .NET](batch-dotnet-get-started.md). O serviço Lote usa então essa fórmula para determinar o número de nós de computação que são necessários para executar a carga de trabalho. Atuando em amostras de dados de métricas de serviços que são coletadas periodicamente, o número de nós de computação no pool é ajustado em um intervalo configurável com base na fórmula associada.

O dimensionamento automático pode ser habilitado quando um pool é criado ou em um pool existente, e você pode alterar uma fórmula existente em um pool já habilitado para dimensionamento automático. O Lote permite avaliar as fórmulas antes de atribuí-las aos pools, bem como monitorar o status das execuções de dimensionamento automático.

## Fórmulas de dimensionamento automático

Uma fórmula de dimensionamento automático é um valor de cadeia de caracteres que contém uma ou mais instruções, atribuído a um elemento [autoScaleFormula][rest_autoscaleformula] (API REST do Lote) do pool ou à propriedade [CloudPool.AutoScaleFormula][net_cloudpool_autoscaleformula] (API .NET do Lote). Você define essas fórmulas e, quando atribuídas a um pool, elas determinam o número de nós de computação disponíveis em um pool para o próximo intervalo de processamento (você verá mais sobre intervalos mais adiante). A cadeia de caracteres da fórmula não pode ter mais de 8 KB, pode incluir até 100 instruções separadas por ponto e vírgula e pode incluir quebras de linha e comentários.

Você pode pensar nas fórmulas de dimensionamento automático como se estivesse usando uma "linguagem" de dimensionamento automático do Lote. As instruções da fórmula são expressões formadas livremente, podem incluir variáveis definidas pelo usuário e pelo sistema, bem como constantes, e podem fornecer várias operações nesses valores usando funções, tipos e operadores internos. Por exemplo, uma instrução pode ter a seguinte forma:

`VAR = Expression(system-defined variables, user-defined variables);`

Geralmente, as fórmulas contêm várias instruções que executam operações em valores obtidos em instruções anteriores:

```
VAR₀ = Expression₀(system-defined variables);
VAR₁ = Expression₁(system-defined variables, VAR₀);
```

Usando as instruções em sua fórmula, sua meta é atingir um número de nós de computação para o qual o pool deve ser dimensionado, o número **alvo** de **nós dedicados**. Esse número "alvo dedicado" pode ser maior, menor ou igual ao número atual de nós no pool. O Lote avalia a fórmula de dimensionamento automático de um pool em um intervalo específico ([intervalos de dimensionamento automático](#interval) são abordados abaixo) e ajusta o número alvo de nós no pool para o número especificado pela fórmula de dimensionamento automático no momento da avaliação.

Como um exemplo rápido, essa fórmula de dimensionamento automático de duas linhas especifica que o número de nós deve ser ajustado de acordo com o número de tarefas ativas, até um máximo de 10 nós de computação:

```
$averageActiveTaskCount = avg($ActiveTasks.GetSample(TimeInterval_Minute * 15));
$TargetDedicated = min(10, $averageActiveTaskCount);
```

As próximas seções do artigo abordam as diversas entidades que vão compor as fórmulas de dimensionamento automático, incluindo variáveis, operadores, operações e funções. Você descobrirá como obter várias métricas de tarefa e recurso de computação no Lote para que seja possível ajustar de modo inteligente a contagem de nós do pool com base no uso do recurso e no status da tarefa. Em seguida, você aprenderá a criar uma fórmula e a habilitar o dimensionamento automático em um pool usando as APIs REST e .NET do Lote, e finalizaremos com alguns exemplos de fórmula.

> [AZURE.NOTE]Cada conta do Lote do Azure fica limitada a um número máximo de nós de computação que podem ser usados para processamento. O serviço de Lote criará os nós somente até esse limite e, portanto, talvez ele não atinja o número alvo especificado por uma fórmula. Confira [Cotas e limites para o serviço do Lote do Azure](batch-quota-limit.md) para obter informações sobre como exibir e aumentar as cotas da conta.

## <a name="variables"></a>Variáveis

As variáveis definidas pelo usuário e pelo sistema podem ser usadas nas fórmulas de dimensionamento automático. Na fórmula de exemplo de duas linhas acima, `$TargetDedicated` é uma variável definida pelo sistema, enquanto `$averageActiveTaskCount` é definida pelo usuário. As tabelas a seguir mostram as variáveis de leitura/gravação e somente leitura que são definidas pelo serviço de Lote.

*Obtenha* e *defina* o valor dessas **variáveis definidas pelo sistema** para gerenciar o número de nós de computação em um pool:

<table>
  <tr>
    <th>Variáveis (leitura/gravação)</th>
    <th>Descrição</th>
  </tr>
  <tr>
    <td>$TargetDedicated</td>
    <td>O número <b>alvo</b> de <b>nós de computação dedicados</b> para o pool. Esse é o número de nós de computação para o qual o pool deve ser dimensionado. Trata-se de um número "alvo", uma vez que é possível que um pool não atinja o número alvo de nós. Isso poderá acontecer se o número alvo de nós for modificado novamente por uma avaliação de dimensionamento automático subsequente antes que o pool tenha atingido o alvo inicial, ou porque um nó da conta ou uma cota principal do Lote foi atingido antes de o número alvo de nós ter sido atingido.</td>
  </tr>
  <tr>
    <td>$NodeDeallocationOption</td>
    <td>A ação que ocorre quando nós de computação são removidos de um pool. Os valores possíveis são:
      <br/>
      <ul>
        <li><p><b>requeue</b> – finalizar tarefas imediatamente e colocá-las novamente na fila de trabalhos para que elas são reagendadas.</p></li>
        <li><p><b>terminate</b> – finalizar tarefas imediatamente e removê-las da fila de trabalhos.</p></li>
        <li><p><b>taskcompletion</b> – aguardar que as tarefas em execução sejam concluídas e remover o nó do pool.</p></li>
        <li><p><b>retaineddata</b> - aguardar que todos os dados de tarefas locais retidos no nó sejam limpos antes de remover o nó do pool.</p></li>
      </ul></td>
   </tr>
</table>

*Obtenha* o valor dessas **variáveis definidas pelo sistema** para fazer ajustes com base nas métricas do serviço de Lote:

<table>
  <tr>
    <th>Variáveis (somente leitura)</th>
    <th>Descrição</th>
  </tr>
  <tr>
    <td>$CPUPercent</td>
    <td>O percentual médio de utilização da CPU</td>
  </tr>
  <tr>
    <td>$WallClockSeconds</td>
    <td>O número de segundos consumidos</td>
  </tr>
  <tr>
    <td>$MemoryBytes</td>
    <td>O número médio de megabytes usados</td>
  <tr>
    <td>$DiskBytes</td>
    <td>O número médio de gigabytes usado nos discos locais</td>
  </tr>
  <tr>
    <td>$DiskReadBytes</td>
    <td>O número de bytes lidos</td>
  </tr>
  <tr>
    <td>$DiskWriteBytes</td>
    <td>O número de bytes gravados</td>
  </tr>
  <tr>
    <td>$DiskReadOps</td>
    <td>A contagem de operações de leitura de disco executadas</td>
  </tr>
  <tr>
    <td>$DiskWriteOps</td>
    <td>A contagem de operações de gravação em disco executadas</td>
  </tr>
  <tr>
    <td>$NetworkInBytes</td>
    <td>O número de bytes de entrada</td>
  </tr>
  <tr>
    <td>$NetworkOutBytes</td>
    <td>O número de bytes de saída</td>
  </tr>
  <tr>
    <td>$SampleNodeCount</td>
    <td>A contagem de nós de computação</td>
  </tr>
  <tr>
    <td>$ActiveTasks</td>
    <td>O número de tarefas que estão em estado ativo</td>
  </tr>
  <tr>
    <td>$RunningTasks</td>
    <td>O número de tarefas em estado de execução</td>
  </tr>
  <tr>
    <td>$SucceededTasks</td>
    <td>O número de tarefas que foram concluídas com êxito</td>
  </tr>
  <tr>
    <td>$FailedTasks</td>
    <td>O número de tarefas que falharam</td>
  </tr>
  <tr>
    <td>$CurrentDedicated</td>
    <td>O número atual de nós de computação dedicados</td>
  </tr>
</table>

> [AZURE.TIP]As variáveis definidas pelo sistema somente leitura mostradas acima são *objetos* que fornecem vários métodos para acessar dados associados a cada um. Confira abaixo [Obter dados de exemplo](#getsampledata) para obter mais informações.

## Tipos

Esses **tipos** têm suporte em uma fórmula.

- double
- doubleVec
- doubleVecList
- cadeia de caracteres
- timestamp - timestamp é uma estrutura composta que contém os elementos a seguir:
	- year
	- month (1-12)
	- day (1-31)
	- weekday (no formato numérico, por exemplo, 1 para segunda-feira)
	- hour (no formato numérico de 24 horas, por exemplo, 13 significa 1h da tarde)
	- minute (00-59)
	- second (00-59)
- timeinterval
	- TimeInterval\_Zero
	- TimeInterval\_100ns
	- TimeInterval\_Microsecond
	- TimeInterval\_Millisecond
	- TimeInterval\_Second
	- TimeInterval\_Minute
	- TimeInterval\_Hour
	- TimeInterval\_Day
	- TimeInterval\_Week
	- TimeInterval\_Year

## Operações

Estas **operações** são permitidas nos tipos listados acima.

<table>
  <tr>
    <th>Operação</th>
    <th>Operadores permitidos</th>
  </tr>
  <tr>
    <td>double &lt;operador> double => double</td>
    <td>+, -, *, /</td>
  </tr>
  <tr>
    <td>double &lt;operador> timeinterval => timeinterval</td>
    <td>*</td>
  </tr>
  <tr>
    <td>doubleVec &lt;operador> double => doubleVec</td>
    <td>+, -, *, /</td>
  </tr>
  <tr>
    <td>doubleVec &lt;operador> doubleVec => doubleVec</td>
    <td>+, -, *, /</td>
  </tr>
  <tr>
    <td>timeinterval &lt;operador> double => timeinterval</td>
    <td>*, /</td>
  </tr>
  <tr>
    <td>timeinterval &lt;operador> timeinterval => timeinterval</td>
    <td>+, -</td>
  </tr>
  <tr>
    <td>timeinterval &lt;operador> timestamp => timestamp</td>
    <td>+</td>
  </tr>
  <tr>
    <td>timestamp &lt;operador> timeinterval => timestamp</td>
    <td>+</td>
  </tr>
  <tr>
    <td>timestamp &lt;operador> timestamp => timeinterval</td>
    <td>-</td>
  </tr>
  <tr>
    <td>&lt;operador>double => double</td>
    <td>-, !</td>
  </tr>
  <tr>
    <td>&lt;operador>timeinterval => timeinterval</td>
    <td>-</td>
  </tr>
  <tr>
    <td>double &lt;operador> double => double</td>
    <td>&lt;, &lt;=, ==, >=, >, !=</td>
  </tr>
  <tr>
    <td>string &lt;operador> string => double</td>
    <td>&lt;, &lt;=, ==, >=, >, !=</td>
  </tr>
  <tr>
    <td>timestamp &lt;operador> timestamp => double</td>
    <td>&lt;, &lt;=, ==, >=, >, !=</td>
  </tr>
  <tr>
    <td>timeinterval &lt;operador> timeinterval => double</td>
    <td>&lt;, &lt;=, ==, >=, >, !=</td>
  </tr>
  <tr>
    <td>double &lt;operador> double => double</td>
    <td>&amp;&amp;, ||</td>
  </tr>
  <tr>
    <td>testar apenas double (diferente de zero é true, zero é false)</td>
    <td>? :</td>
  </tr>
</table>

## Funções

Estas **funções** predefinidas estão disponíveis para definir uma fórmula de dimensionamento automático.

<table>
  <tr>
    <th>Função</th>
    <th>Descrição</th>
  </tr>
  <tr>
    <td>double <b>avg</b>(doubleVecList)</td>
    <td>O valor médio de todos os valores em doubleVecList.</td>
  </tr>
  <tr>
    <td>double <b>len</b>(doubleVecList)</td>
    <td>O comprimento do vetor criado por meio de doubleVecList.</td>
  <tr>
    <td>double <b>lg</b>(double)</td>
    <td>Base de log 2.</td>
  </tr>
  <tr>
    <td>doubleVec <b>lg</b>(doubleVecList)</td>
    <td>Base de log 2 componentwise. Um vec(double) deve ser passado explicitamente para o único parâmetro double. Caso contrário, a versão double lg(double) é assumida.</td>
  </tr>
  <tr>
    <td>double <b>ln</b>(double)</td>
    <td>Log natural.</td>
  </tr>
  <tr>
    <td>doubleVec <b>ln</b>(doubleVecList)</td>
    <td>Base de log 2 componentwise. Um vec(double) deve ser passado explicitamente para o único parâmetro double. Caso contrário, a versão double lg(double) é assumida.</td>
  </tr>
  <tr>
    <td>double <b>log</b>(double)</td>
    <td>Base de log 10.</td>
  </tr>
  <tr>
    <td>doubleVec <b>log</b>(doubleVecList)</td>
    <td>Base de log 10 componentwise. Um vec(double) deve ser passado explicitamente para o único parâmetro double. Caso contrário, a versão double log(double) é assumida.</td>
  </tr>
  <tr>
    <td>double <b>max</b>(doubleVecList)</td>
    <td>O valor máximo em doubleVecList.</td>
  </tr>
  <tr>
    <td>double <b>min</b>(doubleVecList)</td>
    <td>O valor mínimo em doubleVecList.</td>
  </tr>
  <tr>
    <td>double <b>norm</b>(doubleVecList)</td>
    <td>A norma dupla do vetor criado por meio de doubleVecList.
  </tr>
  <tr>
    <td>double <b>percentile</b>(doubleVec v, double p)</td>
    <td>O elemento percentil do vetor v.</td>
  </tr>
  <tr>
    <td>double <b>rand</b>()</td>
    <td>Um valor aleatório entre 0,0 e 1,0.</td>
  </tr>
  <tr>
    <td>double <b>range</b>(doubleVecList)</td>
    <td>A diferença entre os valores mínimo e máximo em doubleVecList.</td>
  </tr>
  <tr>
    <td>double <b>std</b>(doubleVecList)</td>
    <td>O desvio padrão da amostra dos valores em doubleVecList.</td>
  </tr>
  <tr>
    <td><b>stop</b>()</td>
    <td>Para a avaliação da expressão de dimensionamento automático.</td>
  </tr>
  <tr>
    <td>double <b>sum</b>(doubleVecList)</td>
    <td>A soma de todos os componentes em doubleVecList.</td>
  </tr>
  <tr>
    <td>timestamp <b>time</b>(string dateTime="")</td>
    <td>O carimbo de data/hora do horário atual se nenhum parâmetro foi passado ou o carimbo de data/hora da cadeia de caracteres dateTime, se tiver sido passada. Os formatos de dateTime com suporte são W3CDTF e RFC1123.</td>
  </tr>
  <tr>
    <td>double <b>val</b>(doubleVec v, double i)</td>
    <td>O valor do elemento no local i do vetor v com um índice inicial de zero.</td>
  </tr>
</table>

Algumas das funções descritas na tabela acima podem aceitar uma lista como argumento. A lista separada por vírgulas é qualquer combinação de *double* e *doubleVec*. Por exemplo:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

O valor de *doubleVecList* é convertido em um único *doubleVec* antes da avaliação. Por exemplo, se `v = [1,2,3]`, chamar `avg(v)` é equivalente a chamar `avg(1,2,3)`, e chamar `avg(v, 7)` é equivalente a chamar `avg(1,2,3,7)`.

## <a name="getsampledata"></a>Obter dados de exemplo

As fórmulas de dimensionamento automático atuam em dados de métricas (amostras) fornecidos pelo serviço de Lote, aumentando ou diminuindo o tamanho do pool com base nos valores que sua fórmula obtém do serviço. As variáveis definidas pelo sistema mostradas acima são objetos que fornecem vários métodos para acessar dados associados ao objeto. Por exemplo, a expressão a seguir mostra uma solicitação para obter os últimos cinco minutos de uso da CPU:

`$CPUPercent.GetSample(TimeInterval_Minute * 5)`

<table>
  <tr>
    <th>Método</th>
    <th>Descrição</th>
  </tr>
  <tr>
    <td>GetSample()</td>
    <td><p>O método <b>GetSample()</b> retorna um vetor de exemplos de dados.
	<p>Um exemplo vale 30 segundos de dados de métrica. Em outras palavras, os exemplos são obtidos a cada 30 segundos, mas conforme indicado abaixo, há um atraso entre o momento em que um exemplos é coletado e quando ele é disponibilizado para uma fórmula. Como tal, nem todos os exemplos para um determinado período de tempo podem estar disponíveis para avaliação por uma fórmula.
        <ul>
          <li><p><b>doubleVec GetSample(double count)</b> - Especifica o número de exemplos a serem obtidos a partir dos exemplos mais recentes coletados.</p>
				  <p>GetSample(1) retorna o último exemplo disponível. No entanto, para métricas como $CPUPercent, isso não deve ser usado, pois é impossível saber <em>quando</em> o exemplo foi coletado. Ele pode ser recente ou, devido a problemas de sistema, podem ser muito mais antigo. Nesses casos, é melhor usar um intervalo de tempo, conforme mostrado abaixo.</p></li>
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime [, double samplePercent])</b> – Especifica um intervalo de tempo para a coleta de dados de amostra e, opcionalmente, especifica o percentual de amostras que deve estar disponível no intervalo solicitado.</p>
          <p><em>$CPUPercent.GetSample(TimeInterval_Minute * 10)</em> deve retornar 20 amostras se todas as amostras dos últimos dez minutos estiverem presentes no histórico de CPUPercent. No entanto, se o último minuto do histórico não estiver disponível, apenas 18 exemplos retornariam, e nesse caso:<br/>
		  &#160;&#160;&#160;&#160;<em>$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)</em> falharia, pois somente 90% dos exemplos estão disponíveis, e<br/>
		  &#160;&#160;&#160;&#160;<em>$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)</em> teria êxito.</p></li>
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime, (timestamp | timeinterval) endTime [, double samplePercent])</b> – Especifica um intervalo de tempo para coleta de dados, com uma hora de início e uma hora de término.</p></li></ul>
		  <p>Conforme mencionado acima, há um atraso entre quando uma amostra é coletada e quando ela fica disponível para uma fórmula. Isso deve ser considerado ao usar o método <em>GetSample</em>. Consulte <em>GetSamplePercent</em> abaixo.</td>
  </tr>
  <tr>
    <td>GetSamplePeriod()</td>
    <td>Retorna o período das amostras deitas de um conjunto de dados históricos de exemplo.</td>
  </tr>
	<tr>
		<td>Count()</td>
		<td>Retorna o número total de amostras no histórico da métrica.</td>
	</tr>
  <tr>
    <td>HistoryBeginTime()</td>
    <td>Retorna o carimbo de data/hora da amostra de dados mais antiga disponível para a métrica.</td>
  </tr>
  <tr>
    <td>GetSamplePercent()</td>
    <td><p>Retorna a porcentagem de exemplos disponíveis para um determinado intervalo de tempo. Por exemplo:</p>
    <p><b>doubleVec GetSamplePercent( (timestamp | timeinterval) startTime [, (timestamp | timeinterval) endTime] )</b>
	<p>Como o método GetSample falhará se a porcentagem de exemplos retornados for menor que a especificada para samplePercent, você pode usar o método GetSamplePercent para primeiro verificar e depois executar uma ação alternativa se exemplos insuficientes estiverem presentes, sem interromper a avaliação do dimensionamento automático.</p></td>
  </tr>
</table>

### Exemplos, porcentagem de exemplo e o método *GetSample()*

Obter dados de métrica de tarefa e recurso e ajustar o tamanho do pool com base nesses dados forma a operação principal de uma fórmula de dimensionamento automático. Assim, é importante ter um claro entendimento de como as fórmulas de dimensionamento automático interagem com dados de métricas, ou "exemplos".

**Exemplos**

O serviço de Lote coleta periodicamente *exemplos* de métricas de tarefa e recurso e os disponibiliza para suas fórmulas de dimensionamento automático. Esses exemplos são gravados a cada 30 segundos pelo serviço de Lote. No entanto, normalmente há alguma latência que causa um atraso entre quando esses exemplos foram gravados e quando eles são disponibilizados para as fórmulas de dimensionamento automático (e podem ser lidos por elas). Além disso, devido a vários fatores, como rede ou outros problemas de infraestrutura, os exemplos podem não ter sido gravados em um intervalo específico, resultando em exemplos "ausentes".

**Exemplo de porcentagem**

Ao passar um `samplePercent` para o método `GetSample()` ou chamar o método `GetSamplePercent()`, "porcentagem" se refere a uma comparação entre o número total *possível* de exemplos gravados pelo serviço de Lote e o número de exemplos que estão realmente *disponíveis* para sua fórmula de dimensionamento automático.

Vamos examinar um período de 10 minutos como um exemplo. Como os exemplos são gravados a cada 30 segundos, em um período de 10 minutos, o número total máximo de exemplos gravados pelo Lote é de 20 exemplos (2 por minuto). No entanto, devido à latência inerente do mecanismo de relatório ou a algum outro problema na infraestrutura do Azure, pode haver apenas 15 exemplos disponíveis para a fórmula de dimensionamento automático para leitura. Isso significa que, para esse período de 10 minutos, apenas **75%** do número total de exemplos gravados estão de fato disponíveis para a fórmula.

**GetSample() e intervalos de exemplo**

As fórmulas de dimensionamento automático vão aumentar e diminuir seus pools (adicionando ou removendo nós), e como os nós não são de graça, você quer garantir que as fórmulas estejam tomando decisões inteligentes com base em dados suficientes. Desse modo, é recomendável usar uma análise de tipo de tendência nas fórmulas, aumentando e diminuindo os pools com base em um *intervalo* de exemplos coletados.

Para isso, use `GetSample(interval look-back start, interval look-back end)` para retornar um **vetor** de exemplos:

`runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);`

Quando a linha acima é avaliada pelo Lote, ela retorna um intervalo de exemplos como um vetor de valores, por exemplo:

`runningTasksSample=[1,1,1,1,1,1,1,1,1,1];`

Depois de coletar o vetor de exemplos, você poderá usar funções como `min()`, `max()` e `avg()` para derivar valores significativos do intervalo coletado.

Para obter mais segurança, você pode forçar uma avaliação de fórmula a *falhar* se menos de uma determinada porcentagem de exemplos estiver disponível para um período específico. Forçar uma avaliação de fórmula a falhar orientará o Lote a interromper outras avaliações da fórmula se a porcentagem especificada de exemplos não estiver disponível, e nenhuma mudança será feita no tamanho do pool. Para especificar uma porcentagem necessária de exemplos para que a avaliação seja bem-sucedida, especifique-a como o terceiro parâmetro para `GetSample()`. Aqui, é especificado um requisito de 75% de exemplos:

`runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);`

Também é importante, devido ao atraso mencionado anteriormente na disponibilidade do exemplo, sempre especificar um intervalo de tempo com uma hora de início retrógrada de mais de um minuto. Isso porque leva aproximadamente um minuto para que os exemplos se propaguem pelo sistema, de modo que os exemplos no intervalo `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` muitas vezes não estarão disponíveis. Repetindo, você pode usar o parâmetro de porcentagem de `GetSample()` para forçar um requisito de porcentagem de exemplo específico.

> [AZURE.IMPORTANT]É **altamente recomendável** **evitar depender *apenas* de um `GetSample(1)` nas fórmulas de dimensionamento automático**. Isso porque `GetSample(1)` basicamente informa ao serviço de Lote: "Passe-me o último exemplo que você tem, não importa há quanto tempo você o tem". Como se trata apenas de único exemplo, e pode ser um exemplo antigo, ele pode não representar o retrato mais amplo do estado da tarefa ou do recurso. Se for mesmo usar `GetSample(1)`, certifique-se de que ele faz parte de uma instrução mais ampla e não apenas do ponto de dados do qual sua fórmula depende.

## Métricas

Você pode usar métricas de **recurso** e **tarefa** ao definir uma fórmula, ajustando o número alvo de nós dedicados no pool com base nos dados de métrica que obtém e avalia. Confira a seção acima [Variáveis](#variables) para obter mais informações sobre cada métrica.

<table>
  <tr>
    <th>Métrica</th>
    <th>Descrição</th>
  </tr>
  <tr>
    <td><b>Recurso</b></td>
    <td><p>As <b>métricas de recurso</b> são aquelas que se baseiam no uso de CPU, largura de banda e memória dos nós de computação, bem como no número de nós.</p>
		<p> Estas variáveis definidas pelo sistema são úteis para fazer ajustes com base na contagem de  nós:</p>
    <p><ul>
      <li>$TargetDedicated</li>
			<li>$CurrentDedicated</li>
			<li>$SampleNodeCount</li>
    </ul></p>
    <p>Estas variáveis definidas pelo sistema são úteis para fazer ajustes com base no uso de recursos do nó:</p>
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
    <td><p>As <b>métricas de tarefa</b> são aquelas que se baseiam no status das tarefas, como Ativa, Pendente e Concluída. Estas variáveis definidas pelo sistema são úteis para fazer ajustes no tamanho do pool com base nas métricas de tarefa:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$SucceededTasks</li>
			<li>$FailedTasks</li></ul></p>
		</td>
  </tr>
</table>

## Criar uma fórmula de dimensionamento automático

A construção de uma fórmula de dimensionamento automático é feita formando instruções pelo uso dos componentes acima e combinando essas instruções em uma fórmula completa. Por exemplo, aqui construímos uma fórmula primeiro definindo os requisitos para uma fórmula que vai:

1. Aumentar o número de destino de nós de computação em um pool se o uso de CPU estiver alto
2. Reduzir o número de destino de nós de computação em um pool quando o uso de CPU estiver baixo
3. Restringir sempre o número máximo de nós a 400

Para o *aumento* de nós quando há uso elevado de CPU, definimos a instrução que popula uma variável definida pelo usuário ($TotalNodes) com um valor que é 110% do número de destino de nós atual, desde que o valor mínimo para a utilização média de CPU durante os últimos 10 minutos tenha estado acima de 70%:

`$TotalNodes = (min($CPUPercent.GetSample(TimeInterval_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;`

A instrução a seguir define a mesma variável como 90% do número de destino de nós atual se o uso da CPU médio dos últimos 60 minutos esteve *abaixo* de 20%, reduzindo o número de destino durante períodos de baixa utilização da CPU. Observe que essa instrução também faz referência à variável *$TotalNodes* definida pelo usuário, a qual consta na instrução acima.

`$TotalNodes = (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ? ($CurrentDedicated * 0.9) : $TotalNodes;`

Agora, limite o número de destino de nós de computação dedicados para um **máximo** de 400:

`$TargetDedicated = min(400, $TotalNodes)`

Aqui está a fórmula completa:

```
$TotalNodes = (min($CPUPercent.GetSample(TimeInterval_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;
$TotalNodes = (avg($CPUPercent.GetSample(TimeInterval_Minute*60)) < 0.2) ? ($CurrentDedicated * 0.9) : $TotalNodes;
$TargetDedicated = min(400, $TotalNodes)
```

> [AZURE.NOTE]Uma fórmula de dimensionamento automático é composta de tipos, operações, funções e variáveis de API [REST do Lote][rest_api]. Estes são usados em cadeias de caracteres de fórmulas mesmo ao trabalhar com a biblioteca [.NET do Lote][net_api].

## Criar um pool com o dimensionamento automático habilitado

Para habilitar o dimensionamento automático durante a criação de um pool, use uma das técnicas a seguir:

- [New-AzureBatchPool](https://msdn.microsoft.com/library/azure/mt125936.aspx)– esse cmdlet do Azure PowerShell usa o parâmetro AutoScaleFormula para especificar a fórmula de dimensionamento automático.
- [BatchClient.PoolOperations.CreatePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx) – após esse método .NET ser chamado para criar um pool, você define as propriedades [CloudPool.AutoScaleEnabled](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleenabled.aspx) e [CloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx) para habilitar o dimensionamento automático.
- [Adicionar um pool a uma conta](https://msdn.microsoft.com/library/azure/dn820174.aspx) – os elementos enableAutoScale e autoScaleFormula são usados nesta solicitação API REST para configurar o dimensionamento automático para o pool quando ele é criado.

> [AZURE.IMPORTANT]Se você criar um pool habilitado para dimensionamento automático usando uma das técnicas acima, o parâmetro *targetDedicated* para o pool **não** deve ser especificado. Observe também que, se você desejar redimensionar manualmente um pool habilitado para dimensionamento automático (por exemplo, com [BatchClient.PoolOperations.ResizePool][net_poolops_resizepool]), primeiramente é preciso **desabilitar** o dimensionamento automático no pool e depois redimensioná-lo.

O trecho de código a seguir mostra a criação de um [CloudPool][net_cloudpool] com dimensionamento automático habilitado usando a biblioteca [.NET do Lote][net_api], cuja fórmula define o número de destino de nós como 5 às segundas-feiras e como 1 em todos os outros dias da semana. Além disso, o intervalo de dimensionamento automático é definido para 30 minutos (confira [Intervalo de dimensionamento automático](#interval) abaixo). Neste e nos outros trechos de C# neste artigo, "myBatchClient" é uma instância corretamente inicializada de [BatchClient][net_batchclient].

```
CloudPool pool = myBatchClient.PoolOperations.CreatePool("mypool", "3", "small");
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicated = (time().weekday==1?5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
pool.Commit();
```

### <a name="interval"></a>Intervalo de dimensionamento automático

Por padrão, o serviço de Lote ajusta o tamanho do pool de acordo com a sua fórmula de dimensionamento automático a cada **15 minutos**. Esse intervalo é configurável, no entanto, usando as seguintes propriedades do pool:

- API REST - [autoScaleEvaluationInterval][rest_autoscaleinterval]
- API .NET - [CloudPool.AutoScaleEvaluationInterval][net_cloudpool_autoscaleevalinterval]

O intervalo mínimo é de 5 minutos e o máximo é de 168 horas. Se um intervalo fora desse intervalo for especificado, o serviço de Lote retornará um erro de solicitação incorreta (400).

> [AZURE.NOTE]Atualmente, o dimensionamento automático não atua como uma resposta a mudanças de menos de um minuto, mas sim como um ajuste gradual do tamanho do pool à medida que você executa uma carga de trabalho.

## Habilitar o dimensionamento automático após um pool ser criado

Se já tiver configurado um pool com um número especificado de nós de computação usando o parâmetro *targetDedicated*, você pode atualizar o pool existente em um momento posterior para dimensionar automaticamente. Faça isso de uma das seguintes maneiras:

- [BatchClient.PoolOperations.EnableAutoScale][net_enableautoscale] – este método .NET requer a identificação de um pool existente e a fórmula de dimensionamento automático a aplicar ao pool.
- [Habilitar dimensionamento automático em um pool][rest_enableautoscale] – esta solicitação de API REST requer a ID do pool existente no URI e a fórmula de dimensionamento automático no corpo da solicitação.

> [AZURE.NOTE]Se um valor foi especificado para o parâmetro *targetDedicated* quando o pool foi criado, tal valor é ignorado quando a fórmula de dimensionamento automático é avaliada.

Este trecho de código demonstra a habilitação do dimensionamento automático em um pool existente usando a biblioteca [.NET do Lote][net_api]. Observe que o mesmo método é usado tanto para habilitar quanto para atualizar a fórmula em um pool existente. Assim, essa técnica *atualizaria* a fórmula no pool especificado se o dimensionamento automático já tivesse sido habilitado. O trecho supõe que "mypool" seja a ID de um [CloudPool][net_cloudpool] existente.

		 // Define the autoscaling formula. In this snippet, the  formula sets the target number of nodes to 5 on
		 // Mondays, and 1 on every other day of the week
		 string myAutoScaleFormula = "$TargetDedicated = (time().weekday==1?5:1);";

		 // Update the existing pool's autoscaling formula by calling the BatchClient.PoolOperations.EnableAutoScale
		 // method, passing in both the pool's ID and the new formula.
		 myBatchClient.PoolOperations.EnableAutoScale("mypool", myAutoScaleFormula);

## Avaliar a fórmula de dimensionamento automático

É sempre uma boa prática avaliar uma fórmula antes de usá-la em seu aplicativo. Cada fórmula é avaliada por meio de uma “execução de teste” da fórmula em um pool existente. Faça isso usando:

- [BatchClient.PoolOperations.EvaluateAutoScale](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscale.aspx) ou [BatchClient.PoolOperations.EvaluateAutoScaleAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscaleasync.aspx) – esses métodos .NET exigem a ID de um pool existente e a cadeia de caracteres que contém a fórmula de dimensionamento automático. Os resultados da chamada estão contidos em uma instância da classe [AutoScaleEvaluation](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscaleevaluation.aspx).
- [Avaliar uma fórmula de dimensionamento automático](https://msdn.microsoft.com/library/azure/dn820183.aspx) – nesta solicitação de API REST, a ID do pool é especificada no URI e a fórmula de dimensionamento automático é especificada no elemento *autoScaleFormula* do corpo da solicitação. A resposta da operação contém quaisquer informações de erro que possam estar relacionadas à fórmula.

> [AZURE.NOTE]Para avaliar uma fórmula de dimensionamento automático, você deve primeiro ter habilitado o dimensionamento automático no pool usando uma fórmula válida.

Nesse trecho de código usando a biblioteca [.NET do Lote][net_api], podemos avaliar uma fórmula antes de aplicá-la ao [CloudPool][net_cloudpool].

```
// First obtain a reference to the existing pool
CloudPool pool = myBatchClient.PoolOperations.GetPool("mypool");

// We must ensure that autoscaling is enabled on the pool prior to evaluating a formula
if (pool.AutoScaleEnabled.HasValue && pool.AutoScaleEnabled.Value)
{
	// The formula to evaluate - adjusts target number of nodes based on day of week and time of day
	string myFormula = @"
		$CurTime=time();
		$WorkHours=$CurTime.hour>=8 && $CurTime.hour<18;
		$IsWeekday=$CurTime.weekday>=1 && $CurTime.weekday<=5;
		$IsWorkingWeekdayHour=$WorkHours && $IsWeekday;
		$TargetDedicated=$IsWorkingWeekdayHour?20:10;
	";

	// Perform the autoscale formula evaluation. Note that this does not actually apply the formula to
	// the pool.
	AutoScaleEvaluation eval = client.PoolOperations.EvaluateAutoScale(pool.Id, myFormula);

	if (eval.AutoScaleRun.Error == null)
	{
		// Evaluation success - print the results of the AutoScaleRun. This will display the values of each
		// variable as evaluated by the the autoscaling formula.
		Console.WriteLine("AutoScaleRun.Results: " + eval.AutoScaleRun.Results);

		// Apply the formula to the pool since it evaluated successfully
		client.PoolOperations.EnableAutoScale(pool.Id, myFormula);
	}
	else
	{
		// Evaluation failed, output the message associated with the error
		Console.WriteLine("AutoScaleRun.Error.Message: " + eval.AutoScaleRun.Error.Message);
	}
}
```

Uma avaliação bem-sucedida da fórmula neste trecho de código resultará em uma saída semelhante à seguinte:

`AutoScaleRun.Results: $TargetDedicated = 10;$NodeDeallocationOption = requeue;$CurTime = 2015 - 08 - 25T20: 08:42.271Z;$IsWeekday = 1;$IsWorkingWeekdayHour = 0;$WorkHours = 0`

## Obter informações sobre execuções automáticas de dimensionamento

Verifique periodicamente os resultados das execuções de dimensionamento automático para garantir que uma fórmula esteja sendo executada conforme o esperado.

- [CloudPool.AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscalerun.aspx) – ao usar a biblioteca .NET, essa propriedade de um pool fornece uma instância da classe [AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.aspx) que fornece as seguintes propriedades da mais recente execução de dimensionamento automático:
  - [AutoScaleRun.Error](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.error.aspx)
  - [AutoScaleRun.Results](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.results.aspx)
  - [AutoScaleRun.Timestamp](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.timestamp.aspx)
- [Obter informações sobre um pool](https://msdn.microsoft.com/library/dn820165.aspx) – esta solicitação de API REST retorna informações sobre o pool, que incluem a execução mais recente de dimensionamento automático.

## <a name="examples"></a>Exemplos de fórmulas

Vamos dar uma olhada em alguns exemplos que mostram apenas algumas maneiras pelas quais as fórmulas podem ser usadas para dimensionar automaticamente os recursos de computação em um pool.

### Exemplo 1: ajuste baseado no tempo

Talvez você queira ajustar o tamanho do pool com base no dia da semana e hora do dia, aumentando ou diminuindo correspondentemente o número de nós no pool:

```
$CurTime=time();
$WorkHours=$CurTime.hour>=8 && $CurTime.hour<18;
$IsWeekday=$CurTime.weekday>=1 && $CurTime.weekday<=5;
$IsWorkingWeekdayHour=$WorkHours && $IsWeekday;
$TargetDedicated=$IsWorkingWeekdayHour?20:10;
```

Esta fórmula obtém primeiramente a hora atual. Se trata-se de um dia da semana (1-5) e durante o horário de trabalho (8-18h), o tamanho do pool de destino é definido como 20 nós. Caso contrário, o tamanho de destino do pool será definido como 10 nós.

### Exemplo 2: ajuste baseado na tarefa

Neste exemplo, o tamanho do pool é ajustado com base no número de tarefas na fila. Observe que os comentários e quebras de linha são aceitáveis em cadeias de caracteres de fórmulas.

```
// Get pending tasks for the past 15 minutes.
$Samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have less than 70% data points, we use the last sample point, otherwise we use the maximum of
// last sample point and the history average.
$Tasks = $Samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1), avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise half of current dedicated.
$TargetVMs = $Tasks > 0? $Tasks:max(0, $TargetDedicated/2);
// The pool size is capped at 20, if target VM value is more than that, set it to 20. This value
// should be adjusted according to your use case.
$TargetDedicated = max(0,min($TargetVMs,20));
// Set node deallocation mode - keep nodes active only until tasks finish
$NodeDeallocationOption = taskcompletion;
```

### Exemplo 3: contagem de tarefas paralelas

Outro exemplo que ajusta o tamanho do pool com base no número de tarefas, esta fórmula também leva em conta o valor [MaxTasksPerComputeNode][net_maxtasks] que foi definido para o pool. Isso é especialmente útil em situações em que a [execução de tarefas paralelas](batch-parallel-node-tasks.md) foi habilitada no seu pool.

```
// Determine whether 70% of the samples have been recorded in the past 15 minutes; if not, use last sample
$Samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$Tasks = $Samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks (the MaxTasksPerComputeNode
// property on this pool is set to 4, adjust this number for your use case)
$Cores = $TargetDedicated * 4;
$ExtraVMs = (($Tasks - $Cores) + 3) / 4;
$TargetVMs = ($TargetDedicated+$ExtraVMs);
// Attempt to grow the number of compute nodes to match the number of active tasks, with a maximum of 3
$TargetDedicated = max(0,min($TargetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### Exemplo 4: definindo um tamanho de pool inicial

Este exemplo mostra um trecho de código de C# com uma fórmula de dimensionamento automático que define o tamanho do pool para um determinado número de nós por um período inicial e, em seguida, ajusta o tamanho do pool com base no número de tarefas ativas e em execução após o período inicial.

```
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

A fórmula no trecho de código acima tem as seguintes características:

- Define o tamanho do pool inicial como 4 nós
- Não ajusta o tamanho do pool nos primeiros 10 minutos do ciclo de vida do pool
- Após 10 minutos, obtém o valor máximo do número de tarefas ativas e em execução nos últimos 60 minutos
  - Se os dois valores forem 0 (indicando que nenhuma tarefa estava em execução ou ativa nos últimos 60 minutos) o tamanho do pool será definido como 0
  - Se um dos valores for maior do que zero, nenhuma alteração será feita

## Próximas etapas

1. Para avaliar completamente a eficiência de seu aplicativo, talvez seja necessário acessar um nó de computação. Para se beneficiar do acesso remoto, uma conta de usuário deve ser adicionada ao nó que você deseja acessar e um arquivo RDP deve ser recuperado para esse nó.
    - Adicione a conta de usuário de uma das seguintes maneiras:
        * [New-AzureBatchVMUser](https://msdn.microsoft.com/library/mt149846.aspx) – esse cmdlet do PowerShell obtém o nome do pool, o nome do nó de computação, o nome da conta e a senha como parâmetros.
        * [BatchClient.PoolOperations.CreateComputeNodeUser](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createcomputenodeuser.aspx) – este método .NET cria uma instância da classe [ComputeNodeUser](https://msdn.microsoft.com/library/microsoft.azure.batch.computenodeuser.aspx) na qual o nome da conta e a senha podem ser definidos para o nó de computação, então [ComputeNodeUser.Commit](https://msdn.microsoft.com/library/microsoft.azure.batch.computenodeuser.commit.aspx) é chamado na instância para criar o usuário nesse nó.
        * [Adicionar uma conta de usuário a um nó](https://msdn.microsoft.com/library/dn820137.aspx) – o nome do pool e o nó de computação são especificados no URI e o nome da conta e a senha são enviados para o nó no corpo desta solicitação API REST.
    - Obtenha o arquivo RDP:
        * [BatchClient.PoolOperations.GetRDPFile](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getrdpfile.aspx) – este método .NET requer a ID do pool, a ID do nó e o nome do arquivo RDP a criar.
        * [Obter um arquivo de protocolo RDP de um nó](https://msdn.microsoft.com/library/dn820120.aspx) – esta solicitação API REST requer o nome do pool e o nome do nó de computação. A resposta tem o conteúdo do arquivo RDP.
        * [Get-AzureBatchRDPFile](https://msdn.microsoft.com/library/mt149851.aspx) – esse cmdlet do PowerShell obtém o arquivo RDP do nó de computação especificado e o salva no local de arquivo especificado ou em um fluxo.
2.	Alguns aplicativos geram grandes quantidades de dados que podem ser difíceis de processar. Um modo de resolver isso é por meio da [consulta de lista eficiente](batch-efficient-list-queries.md).

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
[rest_autoscaleinterval]: https://msdn.microsoft.com/pt-BR/library/azure/dn820173.aspx
[rest_enableautoscale]: https://msdn.microsoft.com/library/azure/dn820173.aspx

<!---HONumber=AcomDC_0114_2016-->