<properties 
   pageTitle="Conceitos de runbook de Automação do Azure"
   description="Descreve os conceitos básicos que você deve compreender para criar runbooks na Automação do Azure."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/13/2015"
   ms.author="bwren" />

# Conceitos de runbook da Automação do Azure

Os runbooks na Automação do Azure são implementados como Fluxos de Trabalho do Windows PowerShell. Esta seção fornece uma visão geral dos recursos essenciais de fluxos de trabalho que são comuns aos runbooks de automação. Os detalhes completos sobre fluxos de trabalho estão disponíveis em [Introdução ao Fluxo de Trabalho do Windows PowerShell](http://technet.microsoft.com/library/jj134242.aspx).


## Fluxos de Trabalho do Windows PowerShell

Um fluxo de trabalho é uma sequência de etapas programadas e conectadas que executam tarefas de longa duração ou exigem a coordenação de várias etapas em vários dispositivos ou nós gerenciados. Os benefícios de um fluxo de trabalho comparado com um script normal incluem a capacidade de realizar simultaneamente uma ação em vários dispositivos e a capacidade de se recuperar automaticamente de falhas. Um Fluxo de Trabalho do Windows PowerShell é um script do Windows PowerShell que utiliza o Windows Workflow Foundation. Embora o fluxo de trabalho seja escrito com a sintaxe do Windows PowerShell e inicializado pelo Windows PowerShell, ele é processado pelo Windows Workflow Foundation.

### Estrutura básica

Um Fluxo de Trabalho do Windows PowerShell começa com a palavra-chave **Workflow** seguida do corpo do script entre chaves. O nome do fluxo de trabalho segue a palavra-chave **Workflow**, como mostra a sintaxe a seguir. O nome do fluxo de trabalho corresponde ao nome do runbook de automação.

    Workflow Test-Runbook
    {
       <Commands>
    }

Para adicionar parâmetros ao fluxo de trabalho, use a palavra-chave **Param**, como mostra a sintaxe a seguir. O Portal de gerenciamento solicitará ao usuário que forneça valores para esses parâmetros quando eles iniciarem o runbook. Este exemplo usa o atributo de parâmetro opcional que especifica se o parâmetro é obrigatório ou não.

    Workflow Test-Runbook
    {
      Param
      (
       [Parameter(Mandatory=<$True | $False>]
       [Type]$<ParameterName>,

       [Parameter(Mandatory=<$True | $False>]
       [Type]$<ParameterName>
      )
      <Commands>
    }

### Nomenclatura

O nome do fluxo de trabalho deve estar de acordo com o formato verbo-substantivo que é padrão no Windows PowerShell. Veja [Verbos aprovados para comandos do Windows PowerShell](http://msdn.microsoft.com/library/windows/desktop/ms714428(v=vs.85).aspx) para obter uma lista de verbos com uso aprovado. O nome do fluxo de trabalho deve corresponder ao nome do runbook de automação. Se o runbook está sendo importado, o nome do arquivo deve corresponder ao nome do fluxo de trabalho e deve terminar em. ps1.

### Limitações

Para obter uma lista completa de limitações e diferenças de sintaxe entre Fluxos de Trabalho do Windows PowerShell e o Windows PowerShell, confira [Diferenças de sintaxe entre Fluxos de Trabalho de script e scripts](http://technet.microsoft.com/library/jj574140.aspx).

## Atividades

Uma atividade é uma tarefa específica em um fluxo de trabalho. Assim como um script é composto de um ou mais comandos, um fluxo de trabalho é composto de uma ou mais atividades que são executadas em uma sequência. O fluxo de trabalho do Windows PowerShell converte automaticamente muitos dos cmdlets do Windows PowerShell para atividades ao executar um fluxo de trabalho. Quando você especifica um desses cmdlets em seu runbook, a atividade correspondente é efetivamente executada pelo Windows Workflow Foundation. Para esses cmdlets sem uma atividade correspondente, o Fluxo de Trabalho do Windows PowerShell executa o cmdlet automaticamente dentro de uma atividade de [InlineScript](#inlinescript). Há um conjunto de cmdlets que são excluídos e não podem ser usados em um fluxo de trabalho, a menos que você o inclua explicitamente em um bloco de InlineScript. Para obter mais detalhes sobre esses conceitos, confira [Usando atividades em fluxos de trabalho de script](http://technet.microsoft.com/library/jj574194.aspx).

As atividades de fluxo de trabalho compartilham um conjunto de parâmetros comuns para configurar suas operações. Para obter detalhes sobre os parâmetros comuns de fluxo de trabalho, confira [about_WorkflowCommonParameters](http://technet.microsoft.com/library/jj129719.aspx).

## Módulos de integração

Um *Módulo de Integração* é um pacote que contém um módulo do Windows PowerShell e pode ser importado para a Automação do Azure. Os cmdlets nos módulos de integração que são importados para a Automação do Azure ficam automaticamente disponíveis para todos os runbooks na mesma conta de automação. Uma vez que a Automação do Azure baseia-se no Windows PowerShell 4.0, ela dá suporte ao carregamento automático de módulos, o que significa que os cmdlets dos módulos instalados podem ser usados sem ser importados para o script com o [Import-Module](http://technet.microsoft.com/library/hh849725.aspx).

## Execução paralela

Uma vantagem dos Fluxos de Trabalho do Windows PowerShell é a capacidade de executar um conjunto de comandos em paralelo em vez de sequencialmente como com um script típico. Isso é particularmente útil em runbooks, uma vez que eles podem executar várias ações que levam bastante tempo para concluir. Por exemplo, um runbook pode provisionar um conjunto de máquinas virtuais. Em vez de executar cada processo de provisionamento em sequência uns com os outros, as ações podem ser efetuadas simultaneamente, aumentando a eficiência geral. O runbook continua somente quando todas forem concluídas.

Você pode usar a palavra-chave **Parallel** para criar um bloco de script com vários comandos que serão executados simultaneamente. Isso é feito através da sintaxe mostrada abaixo. Nesse caso, a Atividade1 e a Atividade2 vão começar ao mesmo tempo. A Atividade3 somente começará após conclusão da Atividade1 e da Atividade2.

    Parallel
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

Você pode usar o construct **ForEach-Parallel** para processar comandos de cada item em uma coleção simultaneamente. Os itens na coleção são processados em paralelo, enquanto os comandos no bloco de script são executados em sequência. Isso é feito através da sintaxe mostrada abaixo. Nesse caso, a Atividade1 será iniciada ao mesmo tempo para todos os itens na coleção. Para cada item, a Atividade2 será iniciada após a conclusão da Atividade1. A Atividade3 somente começará depois que a Atividade1 e a Atividade2 forem concluídas para todos os itens.

    ForEach -Parallel ($<item> in $<collection>)
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

A palavra-chave **Sequence** é usada para executar comandos em sequência em um bloco de script **Parallel**. O bloco de script **Sequence** é executado em paralelo com outros comandos, mas os comandos dentro do bloco são executados em sequência. Isso é feito através da sintaxe mostrada abaixo. Nesse caso, a Atividade1, a Atividade2 e a Atividade3 serão iniciadas ao mesmo tempo. A Atividade4 será iniciada somente após a conclusão da Atividade3. A Atividade5 será iniciada depois que todas as outras atividades tiverem sido concluídas.

    Parallel
    {
      <Activity1>
      <Activity2>

      Sequence 
      { 
        <Activity3>  
        <Activity4>
      }

    }
    <Activity5>

## Pontos de verificação

Um *ponto de verificação* é um instantâneo do estado atual do fluxo de trabalho que inclui o valor atual de variáveis e as saídas geradas para aquele ponto. O último ponto de verificação a ser concluído em um runbook é salvo no banco de dados de automação para que o fluxo de trabalho possa continuar se houver um problema, como uma interrupção de máquina durante o tempo de execução. Sem um ponto de verificação, o fluxo de trabalho começaria desde o início. Os dados do ponto de verificação são removidos quando o trabalho de runbook é concluído.

Você pode definir um ponto de verificação em um fluxo de trabalho com a atividade **Checkpoint-Workflow**. Quando essa atividade é incluída em um runbook, um ponto de verificação é executado imediatamente. Se o runbook for suspenso por uma exceção, quando o trabalho for reiniciado, ele continuará do último ponto de verificação definido.

No código de exemplo a seguir, uma exceção ocorre após a Atividade2, fazendo com que o runbook seja suspenso. Quando o trabalho é retomado, ele começa pela execução da Atividade2, já que isso foi logo após o último ponto de verificação definido.

    <Activity1>
    Checkpoint-Workflow
    <Activity2>
    <Exception>
    <Activity3>

Você deve definir pontos de verificação em um runbook para depois de atividades que possam estar sujeitas a exceção e não devam ser repetidas se o runbook for retomado. Por exemplo, o runbook pode criar uma máquina virtual. Você pode definir um ponto de verificação antes e depois dos comandos para criar a máquina virtual. Se a criação falhar, os comandos são repetidos quando o runbook é retomado. Se a criação for bem-sucedida, mas o runbook falhar posteriormente, a máquina virtual não é criada novamente quando o runbook é retomado.

Para saber mais sobre pontos de verificação, confira [Adicionando pontos de verificação a um Fluxo de Trabalho de script](http://technet.microsoft.com/library/jj574114.aspx).

## Suspendendo um fluxo de trabalho

Você pode forçar um runbook para se suspender com a atividade **Suspend-Workflow**. Essa atividade definirá um ponto de verificação e fará com que o fluxo de trabalho seja suspenso imediatamente. A suspensão de um fluxo de trabalho é útil para runbooks que podem exigir a execução de uma etapa manual antes da execução de outro conjunto de atividades.

Para saber mais sobre a suspensão de um fluxo de trabalho, confira [Fazendo um Fluxo de Trabalho se suspender](http://technet.microsoft.com/library/jj574175.aspx).

## InlineScript

A atividade **InlineScript** executa um bloco de comandos em script tradicional do PowerShell em vez do fluxo de trabalho do PowerShell, e retorna a sua saída ao fluxo de trabalho. Enquanto os comandos em um fluxo de trabalho são enviados ao Windows Workflow Foundation para processamento, os comandos em um bloco de InlineScript são processados pelo Windows PowerShell. A atividade usa os parâmetros comuns de fluxo de trabalho padrão, incluindo **PSCredential**, que permite que você especifique a execução do bloco de código com credenciais alternativas.

O InlineScript usa a sintaxe mostrada abaixo.

    InlineScript
    {
      <Script Block>
    } <Common Parameters>

Embora as atividades de InlineScript atividades possam ser essenciais em determinados runbooks, elas não dão suporte a constructs de fluxo de trabalho e devem ser usadas somente quando necessário pelos seguintes motivos:

- Você não pode usar pontos de verificação de dentro de um bloco de InlineScript. Se uma falha ocorre dentro do bloco, ele deve ser retomado desde o início do bloco.
- O InlineScript afeta a escalabilidade do runbook, uma vez que retém a sessão do Windows PowerShell por toda a duração do bloco de InlineScript.
- Atividades como Get-AutomationVariable e Get-AutomationPSCredential não estão disponíveis em um bloco de InlineScript.  

Se você precisar usar um InlineScript, deve minimizar seu escopo. Por exemplo, se o runbook itera em uma coleção enquanto aplica a mesma operação em cada item, o loop deve ocorrer fora do InlineScript. Isso fornece as seguintes vantagens:

- Você pode ter [ponto de verificação](#checkpoints) no fluxo de trabalho depois de cada iteração. Se o trabalho é suspenso ou interrompido e retomado, o loop será capaz de continuar.
- Você pode usar **ForEach–Parallel** para processar itens de coleção simultaneamente.

Lembre-se das seguintes recomendações se você usar um InlineScript no runbook:

- Você pode passar valores para o script com o modificador de escopo **$Using**. Por exemplo, se uma variável chamada $abc foi definida fora do InlineScript, ela se tornaria $using:abc dentro de um InlineScript.

- Para retornar a saída de um InlineScript, atribua a saída a uma variável e coloque a saída de dados para ser retornada ao fluxo de saída. O exemplo a seguir atribui a cadeia de caracteres "hi" a uma variável chamada $output.

	<pre><code>$output = InlineScript {Write-Output "hi"}</code></pre>

- Evite definir fluxos de trabalho dentro do escopo do InlineScript. Embora alguns fluxos de trabalho possam parecer funcionar corretamente,esse não é um cenário testado. Assim, você pode encontrar mensagens de erro confusas ou comportamento inesperado.

Para obter mais detalhes sobre como usar o InlineScript, confira [Executando comandos do Windows PowerShell em um Fluxo de Trabalho](http://technet.microsoft.com/library/jj574197.aspx) e [about_inlinescript](http://technet.microsoft.com/library/jj649082.aspx).


## Artigos relacionados

- [Criando ou importando um Runbook](http://technet.microsoft.com/library/dn919921.aspx)

<!---HONumber=58--> 