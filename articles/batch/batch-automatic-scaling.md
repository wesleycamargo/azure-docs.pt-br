
<properties
	pageTitle="Dimensionar Automaticamente Nós de Computação em um Pool do Lote do Azure"
	description="O dimensionamento automático é realizado habilitando um pool e associando uma fórmula ao pool, que é usada para calcular o número de nós de computação que são necessários para processar o aplicativo."
	services="batch"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="batch"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="multiple"
	ms.date="08/05/2015"
	ms.author="davidmu"/>

# Dimensionar automaticamente nós de computação em um pool do Lote do Azure

O dimensionamento automático de nós de computação em um pool do Lote do Azure é um ajuste dinâmico da potência de processamento que é usada pelo seu aplicativo. Essa facilidade de ajuste economiza tempo e dinheiro. Para saber mais sobre pools e nós de computação, consulte a [Visão geral técnica do Lote do Azure](batch-technical-overview.md).

O dimensionamento automático ocorre quando ele está habilitado em um pool e uma fórmula é associada ao pool. A fórmula é usada para determinar o número de nós de computação que são necessários para processar o aplicativo. O dimensionamento automático pode ser configurado quando o pool é criado, ou você pode fazer isso posteriormente em um pool existente. A fórmula também pode ser atualizada em um pool no qual o dimensionamento automático foi habilitado.

Quando o dimensionamento automático está habilitado, o número de nós de computação disponíveis é ajustado a cada 15 minutos com base na fórmula. A fórmula atua em amostras que são coletados periodicamente, mas há um atraso entre o momento que a amostra é coletada e quando ela é disponibilizada para a fórmula. Isso deve ser considerado ao usar o método GetSample descrito abaixo.

É sempre uma boa prática avaliar a fórmula antes de atribuí-la a um pool, e é importante monitorar o status das execuções de dimensionamento automático.

> [AZURE.NOTE]Cada conta do Lote do Azure fica limitada a um número máximo de nós de computação que podem ser usados para processamento. O sistema cria os nós até esse limite e talvez ele não alcance os números de destino especificados por uma fórmula.

## Definir a fórmula

A fórmula que você define é usada para decidir o número de nós de computação disponíveis no pool para o próximo intervalo de processamento. A fórmula é uma cadeia de caracteres que não pode exceder 8 KB e pode incluir até 100 instruções separadas por ponto e vírgula.

As instruções em uma fórmula são expressões de formatação livre. Elas podem incluir quaisquer variáveis definidas pelo sistema, variáveis definidas pelo usuário, valores constantes e operações com suporte nessas variáveis ou constantes:

	VAR = Function(System defined variables, user-defined variables);

É possível combinar variáveis para fazer fórmulas complexas:

	VAR₀ = Function₀(system-defined variables);
	VAR₁ = Function₁(system-defined variables, VAR₀);

### Variáveis

Variáveis definidas pelo sistema e variáveis definidas pelo usuário podem ser usadas em uma fórmula. Você pode configurar o valor dessas variáveis definidas pelo sistema para gerenciar os nós de computação em um pool.

<table>
  <tr>
    <th>Variável</th>
    <th>Descrição</th>
  </tr>
  <tr>
    <td>$TargetDedicated</td>
    <td>O número de destino de nós de computação dedicados para o pool. O valor pode ser alterado com base no uso real para as tarefas.</td>
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

Você só pode ler os valores dessas variáveis definidas pelo sistema para fazer ajustes com base nas métricas dos nós de computação na amostra.

<table>
  <tr>
    <th>Variável</th>
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

### Tipos

Esses tipos têm suporte em uma fórmula:

- double
- doubleVec
- string
- timestamp
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

### Operações

Estas operações são permitidas nos tipos listados acima.

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

### Funções

Estas funções predefinidas estão disponíveis para definir uma fórmula de dimensionamento automático.

<table>
  <tr>
    <th>Função</th>
    <th>Descrição</th>
  </tr>
  <tr>
    <td>double avg(doubleVecList)</td>
    <td>O valor médio de todos os valores em doubleVecList.</td>
  </tr>
  <tr>
    <td>double len(doubleVecList)</td>
    <td>O comprimento do vetor criado por meio de doubleVecList.</td>
  <tr>
    <td>double lg(double)</td>
    <td>Base de log 2.</td>
  </tr>
  <tr>
    <td>doubleVec lg(doubleVecList)</td>
    <td>Base de log 2 componentwise. Um vec(double) deve ser passado explicitamente para o único parâmetro double. Caso contrário, a versão double lg(double) é assumida.</td>
  </tr>
  <tr>
    <td>double ln(double)</td>
    <td>Log natural.</td>
  </tr>
  <tr>
    <td>doubleVec ln(doubleVecList)</td>
    <td>Base de log 2 componentwise. Um vec(double) deve ser passado explicitamente para o único parâmetro double. Caso contrário, a versão double lg(double) é assumida.</td>
  </tr>
  <tr>
    <td>double log(double)</td>
    <td>Base de log 10.</td>
  </tr>
  <tr>
    <td>doubleVec log(doubleVecList)</td>
    <td>Base de log 10 componentwise. Um vec(double) deve ser passado explicitamente para o único parâmetro double. Caso contrário, a versão double log(double) é assumida.</td>
  </tr>
  <tr>
    <td>double max(doubleVecList)</td>
    <td>O valor máximo em doubleVecList.</td>
  </tr>
  <tr>
    <td>double min(doubleVecList)</td>
    <td>O valor mínimo em doubleVecList.</td>
  </tr>
  <tr>
    <td>double norm(doubleVecList)</td>
    <td>A norma dupla do vetor criado por meio de doubleVecList.
  </tr>
  <tr>
    <td>double percentile(doubleVec v, double p)</td>
    <td>O elemento percentil do vetor v.</td>
  </tr>
  <tr>
    <td>double rand()</td>
    <td>Um valor aleatório entre 0,0 e 1,0.</td>
  </tr>
  <tr>
    <td>double range(doubleVecList)</td>
    <td>A diferença entre os valores mínimo e máximo em doubleVecList.</td>
  </tr>
  <tr>
    <td>double std(doubleVecList)</td>
    <td>O desvio padrão da amostra dos valores em doubleVecList.</td>
  </tr>
  <tr>
    <td>stop()</td>
    <td>Para a avaliação da expressão de dimensionamento automático.</td>
  </tr>
  <tr>
    <td>double sum(doubleVecList)</td>
    <td>A soma de todos os componentes em doubleVecList.</td>
  </tr>
  <tr>
    <td>timestamp time(string dateTime="")</td>
    <td>O carimbo de data/hora do horário atual se nenhum parâmetro foi passado ou o carimbo de data/hora da cadeia de caracteres dateTime, se tiver sido passada. Os formatos de dateTime com suporte são W3CDTF e RFC1123.</td>
  </tr>
  <tr>
    <td>double val(doubleVec v, double i)</td>
    <td>O valor do elemento no local i do vetor v com um índice inicial de zero.</td>
  </tr>
</table>

Algumas das funções descritas na tabela podem aceitar uma lista como argumento. A lista separada por vírgulas é qualquer combinação de double e doubleVec. Por exemplo:

	doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?

O valor de doubleVecList é convertido em um único doubleVec antes da avaliação. Por exemplo, se v = [1,2, 3], chamar avg(v) é equivalente a chamar avg(1,2,3) e chamar avg (v, 7) é equivalente a chamar avg(1,2,3,7).

### Dados de amostra

As variáveis definidas pelo sistema são objetos que fornecem métodos para acessar os dados associados. Por exemplo, a expressão a seguir mostra uma solicitação para obter os últimos cinco minutos de uso da CPU:

	$CPUPercent.GetSample(TimeInterval_Minute*5)

Esses métodos podem ser usados para obter dados de amostra.

<table>
  <tr>
    <th>Método</th>
    <th>Descrição</th>
  </tr>
  <tr>
    <td>Count()</td>
    <td>Retorna o número total de amostras no histórico da métrica.</td>
  </tr>
  <tr>
    <td>GetSample()</td>
    <td><p>Retorna um vetor de exemplos de dados. Por exemplo:</p>
        <ul>
          <li><p><b>doubleVec GetSample(double count)</b> - Especifica o número de amostras que são necessárias para os exemplos mais recentes.</p>
				  <p>Um exemplo é 5 segundos de dados de métrica. GetSample(1) retorna a última amostra disponível, mas para métricas como $CPUPercent você não deve usá-lo porque não é possível saber quando a amostra foi coletada. Pode ser recente ou, devido a problemas do sistema, pode ser muito mais antigo. É melhor usar um intervalo de tempo, conforme mostrado abaixo.</p></li>
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime [, double samplePercent])</b> – Especifica um intervalo de tempo para a coleta de dados de amostra e, opcionalmente, especifica o percentual de amostras que deve haver no intervalo solicitado.</p>
          <p>$CPUPercent.GetSample(TimeInterval\_Minute*10) deve retornar 200 amostras se todas as amostras dos últimos dez minutos estiverem presentes no histórico de CPUPercent. Se o último minuto do histórico ainda não estiver presente, somente 180 exemplos são retornados.</p>
					<p>$CPUPercent.GetSample(TimeInterval\_Minute*10, 80) é bem-sucedido e $CPUPercent.GetSample(TimeInterval_Minute*10,95) falha.</p></li>
          <li><p><b>doubleVec GetSample((timestamp | timeinterval) startTime, (timestamp | timeinterval) endTime [, double samplePercent])</b> – Especifica um intervalo de tempo para coleta de dados, com uma hora de início e uma hora de término.</p></li></ul></td>
  </tr>
  <tr>
    <td>GetSamplePeriod()</td>
    <td>Retorna o período das amostras deitas de um conjunto de dados históricos de exemplo.</td>
  </tr>
  <tr>
    <td>HistoryBeginTime()</td>
    <td>Retorna o carimbo de data/hora da amostra de dados mais antiga disponível para a métrica.</td>
  </tr>
  <tr>
    <td>GetSamplePercent()</td>
    <td><p>Retorna a porcentagem de amostras que um histórico tem atualmente para um determinado intervalo de tempo. Por exemplo:</p>
    <p><b>doubleVec GetSamplePercent( (timestamp | timeinterval) startTime [, (timestamp | timeinterval) endTime] )</b> - como o método GetSample falha se a porcentagem de amostras retornadas for menor que a especificada para samplePercent, você pode usar os métodos GetSamplePercent para, primeiro, verificar e depois realizar uma ação alternativa quando não houver amostras suficientes, sem interromper a avaliação do dimensionamento automático.</p></td>
  </tr>
</table>

### Métricas

Essas métricas podem ser definidas em uma fórmula.

<table>
  <tr>
    <th>Métrica</th>
    <th>Descrição</th>
  </tr>
  <tr>
    <td>Recurso</td>
    <td><p>Com base no uso da CPU, uso de largura de banda, uso de memória e número de nós de computação. As variáveis de sistema descritas acima são usadas em fórmulas para gerenciar os nós de computação em um pool:</p>
    <p><ul>
      <li>$TargetDedicated</li>
      <li>$NodeDeallocationOption</li>
    </ul></p>
    <p>Essas variáveis de sistema são usados para fazer ajustes com base nas métricas de nó:</p>
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
    <p>Este exemplo mostra uma fórmula que é usada para definir o número de nós de computação no pool para 110% do número de destino de nós atual se o uso médio mínimo da CPU nos últimos 10 minutos estiver acima de 70%:</p>
    <p><b>totalTVMs = (min($CPUPercent.GetSample(TimeInterval\_Minute*10)) > 0.7) ? ($CurrentDedicated * 1.1) : $CurrentDedicated;</b></p>
    <p>Este exemplo mostra uma fórmula que é usada para definir o número de nós de computação no pool para 90% do número de destino de nós atual se o uso médio da CPU nos últimos 60 minutos ficar abaixo de 20%:</p>
    <p><b>totalTVMs = (avg($CPUPercent.GetSample(TimeInterval\_Minute*60)) &lt; 0.2) ? ($CurrentDedicated * 0.9) : totalTVMs;</b></p>
    <p>Este exemplo define o número de destino de nós de computação dedicados para um máximo de 400:</p>
    <p><b>$TargetDedicated = min(400, totalTVMs);</b></p></td>
  </tr>
  <tr>
    <td>Tarefa</td>
    <td><p>Com base no status das tarefas, como Ativa, Pendente e Concluída.</p>
    <p>Essas variáveis de sistema são usadas para fazer ajustes com base nas métricas da tarefa:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$SucceededTasks</li>
      <li>$FailedTasks</li>
      <li>$CurrentDedicated</li></ul></p>
    <p>Este exemplo mostra uma fórmula que detecta se 70% das amostras foram registradas nos últimos 15 minutos. Caso contrário, ele usa a última amostra. Ele tenta aumentar o número de nós de computação para corresponder ao número de tarefas ativas, com um máximo de 3. Ele define o número de nós para um quarto do número de tarefas ativas porque a propriedade MaxTasksPerVM do pool está definida como 4. Ele também define a opção de Desalocação como "taskcompletion" para manter o computador até a conclusão das tarefas.</p>
    <p><b>$Samples = $ActiveTasks.GetSamplePercent(TimeInterval\_Minute * 15); $Tasks = $Samples &lt; 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval\_Minute * 15))); $Cores = $TargetDedicated * 4; $ExtraVMs = ($Tasks - $Cores) / 4; $TargetVMs = ($TargetDedicated+$ExtraVMs);$TargetDedicated = max(0,min($TargetVMs,3)); $NodeDeallocationOption = taskcompletion;</b></p></td>
  </tr>
</table>

## Avaliar a fórmula de dimensionamento automático

É sempre uma boa prática avaliar uma fórmula antes de usá-la em seu aplicativo. A fórmula é avaliada por meio de uma execução de teste em um pool existente. Faça isso usando um destes métodos:

- [Método IPoolManager.EvaluateAutoScale](https://msdn.microsoft.com/library/azure/dn931617.aspx) ou [Método IPoolManager.EvaluateAutoScaleAsync](https://msdn.microsoft.com/library/azure/dn931545.aspx) – estes métodos .NET requerem o nome de um pool existente e a cadeia de caracteres que contém a fórmula de dimensionamento automático. Os resultados da chamada são contidos em uma instância da [Classe AutoScaleEvaluation](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscaleevaluation.aspx).
- [Avaliar uma fórmula de dimensionamento automático](https://msdn.microsoft.com/library/azure/dn820183.aspx) – nesta operação REST, o nome do pool é especificado no URI e a fórmula de dimensionamento automático é especificada no elemento autoScaleFormula do corpo da solicitação. A resposta da operação contém quaisquer informações de erro que possam estar relacionadas à fórmula.

## Criar um pool com o dimensionamento automático habilitado

Crie um pool de uma das seguintes maneiras:

- [New-AzureBatchPool](https://msdn.microsoft.com/library/azure/mt125936.aspx)– esse cmdlet usa o parâmetro AutoScaleFormula para especificar a fórmula de dimensionamento automático.
- [Método IPoolManager.CreatePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.ipoolmanager.createpool.aspx) – após esse método .NET ser chamado para criar um pool, a [Propriedade ICloudPool.AutoScaleEnabled](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.icloudpool.autoscaleenabled.aspx) e a [Propriedade ICloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.icloudpool.autoscaleformula.aspx) são definidas no pool para habilitar o dimensionamento automático.
- [Adicionar um grupo a uma conta](https://msdn.microsoft.com/library/azure/dn820174.aspx) – os elementos enableAutoScale e autoScaleFormula são usados nesta API REST para configurar o dimensionamento automático para o pool quando ele é criado.

> [AZURE.NOTE]Se você definir o dimensionamento automático quando o pool for criado usando os recursos mencionados acima, o parâmetro targetDedicated do pool não será usado.

## Habilitar o dimensionamento automático após um pool ser criado

Se já tiver configurado um pool com um número especificado de nós de computação usando o parâmetro targetDedicated, você pode atualizar o pool existente em um momento posterior para dimensionar automaticamente. Você faz isso de uma das seguintes maneiras:

- [Método IPoolManager.EnableAutoScale](https://msdn.microsoft.com/library/azure/dn931709.aspx) – este método .NET requer o nome de um pool existente e a fórmula de dimensionamento automático.
- [Habilitar/desabilitar dimensionamento automático](https://msdn.microsoft.com/library/azure/dn820173.aspx) – esta API REST requer o nome de pool existente no URI e a fórmula de dimensionamento automático no corpo da solicitação.

> [AZURE.NOTE]O valor especificado para o parâmetro targetDedicated quando o pool foi criado é ignorado quando a fórmula de dimensionamento automático é avaliada.

## Obter informações sobre execuções automáticas de dimensionamento

Você deve verificar periodicamente os resultados das execuções automáticas de dimensionamento. Faça isso de uma das seguintes maneiras:

- [Propriedade ICloudPool.AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.icloudpool.autoscalerun.aspx) – ao usar a biblioteca .NET, esta propriedade de um pool fornece uma instância da [Classe AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.aspx), que fornece uma [Propriedade AutoScaleRun.Error](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.error.aspx), uma [Propriedade AutoScaleRun.Results](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.results.aspx) e uma [Propriedade AutoScaleRun.Timestamp](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.timestamp.aspx).
- [Obter informações sobre um pool](https://msdn.microsoft.com/library/dn820165.aspx) – esta API REST retorna informações sobre o pool, que incluem a execução mais recente de dimensionamento automático.

## Próximas etapas

1.	Talvez seja necessário acessar o nó de computação para avaliar completamente a eficiência de seu aplicativo. Para se beneficiar do acesso remoto, uma conta de usuário deve ser adicionada ao nó de computação que você deseja acessar e um arquivo RDP deve ser recuperado desse nó. Adicione a conta de usuário de uma das seguintes maneiras:

	- [New-AzureBatchVMUser](https://msdn.microsoft.com/library/mt149846.aspx) – este cmdlet obtém o nome do pool, o nome do nó de computação, o nome da conta e a senha como parâmetros.
	- [Método IVM.CreateUser](https://msdn.microsoft.com/library/microsoft.azure.batch.ivm.createuser.aspx) – este método .NET cria uma instância da [Interface do Usuário](https://msdn.microsoft.com/library/microsoft.azure.batch.iuser.aspx) na qual é possível definir o nome da conta e a senha para o nó de computação.
	- [Adicionar uma conta de usuário a um nó](https://msdn.microsoft.com/library/dn820137.aspx) – o nome do pool e o nó de computação são especificados no URI e o nome da conta e a senha são enviados para o nó no corpo da solicitação dessa API REST.

		Obtenha o arquivo RDP:

	- [Método IVM.GetRDPFile](https://msdn.microsoft.com/library/microsoft.azure.batch.ivm.getrdpfile.aspx) – este método .NET requer o nome do arquivo RDP a ser criado.
	- [Obter um arquivo de protocolo RDP de um nó](https://msdn.microsoft.com/library/dn820120.aspx) – esta API REST requer o nome do pool e o nome do nó de computação. A resposta tem o conteúdo do arquivo RDP.
	- [Get-AzureBatchRDPFile](https://msdn.microsoft.com/library/mt149851.aspx) – este cmdlet obtém o arquivo RDP do nó de computação especificado e o salva no local de arquivo especificado ou em um fluxo.
2.	Alguns aplicativos geram grandes quantidades de dados que podem ser difíceis de processar. Uma forma de resolver isso é por meio da [consulta de lista eficiente](batch-efficient-list-queries.md).

<!---HONumber=August15_HO7-->