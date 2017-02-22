---
title: "Aprender sobre o Fluxo de Trabalho do PowerShell para Automação do Azure | Microsoft Docs"
description: "Este artigo é concebido como uma lição rápida para autores familiarizados com o PowerShell para entender as diferenças entre o PowerShell e o fluxo de trabalho do PowerShell e conceitos aplicáveis aos runbooks de Automação."
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
ms.assetid: 84bf133e-5343-4e0e-8d6c-bb14304a70db
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: magoedte;bwren
translationtype: Human Translation
ms.sourcegitcommit: 480a40bd5ecd58f11b10c27e7e0d2828bcae1f17
ms.openlocfilehash: 50966ed518b79f2033680790432e29b0c9e7b289


---
# <a name="learning-key-windows-powershell-workflow-concepts-for-automation-runbooks"></a>Aprender sobre os principais conceitos de Fluxo de Trabalho do Windows PowerShell para runbooks de Automação 
Os runbooks na Automação do Azure são implementados como Fluxos de Trabalho do Windows PowerShell.  Um fluxo de trabalho do Windows PowerShell é semelhante a um script do Windows PowerShell, mas tem algumas diferenças significativas que podem ser confusas para um novo usuário.  Embora este artigo sirva para ajudar você a escrever runbooks usando o Fluxo de Trabalho do PowerShell, recomendamos que você escreva os runbooks usando o PowerShell, a menos que você precise de pontos de verificação.  Há algumas diferenças de sintaxe quando se cria runbooks de Fluxo de trabalho do PowerShell, e essas diferenças exigem um pouco mais de trabalho para escrever fluxos de trabalho efetivos.  

Um fluxo de trabalho é uma sequência de etapas programadas e conectadas que executam tarefas de longa duração ou exigem a coordenação de várias etapas em vários dispositivos ou nós gerenciados. Os benefícios de um fluxo de trabalho comparado com um script normal incluem a capacidade de realizar simultaneamente uma ação em vários dispositivos e a capacidade de se recuperar automaticamente de falhas. Um Fluxo de Trabalho do Windows PowerShell é um script do Windows PowerShell que utiliza o Windows Workflow Foundation. Embora o fluxo de trabalho seja escrito com a sintaxe do Windows PowerShell e inicializado pelo Windows PowerShell, ele é processado pelo Windows Workflow Foundation.

Para obter detalhes completos sobre os tópicos nesse artigo, consulte [Introdução ao fluxo de trabalho do Windows PowerShell](http://technet.microsoft.com/library/jj134242.aspx).

## <a name="basic-structure-of-a-workflow"></a>Estrutura básica de um fluxo de trabalho
A primeira etapa para converter um script do PowerShell para um fluxo de trabalho do PowerShell é circunscrevê-lo com a palavra-chave **Workflow** .  Um fluxo de trabalho começa com a palavra-chave **Workflow** seguida do corpo do script entre chaves. O nome do fluxo de trabalho segue a palavra-chave **Workflow** , como mostra a sintaxe a seguir.

    Workflow Test-Workflow
    {
       <Commands>
    }

O nome do fluxo de trabalho deve corresponder ao nome do runbook de automação. Se o runbook está sendo importado, o nome do arquivo deve corresponder ao nome do fluxo de trabalho e deve terminar em. ps1.

Para adicionar parâmetros ao fluxo de trabalho, use a palavra-chave **Param** , exatamente como faria para um script.

## <a name="code-changes"></a>Alterações de código
O código de fluxo de trabalho do PowerShell é praticamente idêntico ao código de script do PowerShell, exceto por algumas alterações significativas.  As seções a seguir descrevem as alterações que você precisará fazer em um script do PowerShell para ele para funcionar em um fluxo de trabalho.

### <a name="activities"></a>Atividades
Uma atividade é uma tarefa específica em um fluxo de trabalho. Assim como um script é composto de um ou mais comandos, um fluxo de trabalho é composto de uma ou mais atividades que são executadas em uma sequência. O fluxo de trabalho do Windows PowerShell converte automaticamente muitos dos cmdlets do Windows PowerShell para atividades ao executar um fluxo de trabalho. Quando você especifica um desses cmdlets em seu runbook, a atividade correspondente é efetivamente executada pelo Windows Workflow Foundation. Para esses cmdlets sem uma atividade correspondente, o Fluxo de Trabalho do Windows PowerShell executa o cmdlet automaticamente dentro de uma atividade de [InlineScript](#inlinescript) . Há um conjunto de cmdlets que são excluídos e não podem ser usados em um fluxo de trabalho, a menos que você o inclua explicitamente em um bloco de InlineScript. Para obter mais detalhes sobre esses conceitos, confira [Usando atividades em fluxos de trabalho de script](http://technet.microsoft.com/library/jj574194.aspx).

As atividades de fluxo de trabalho compartilham um conjunto de parâmetros comuns para configurar suas operações. Para obter detalhes sobre os parâmetros comuns do fluxo de trabalho, consulte [about_WorkflowCommonParameters](http://technet.microsoft.com/library/jj129719.aspx).

### <a name="positional-parameters"></a>Parâmetros posicionais
Você não pode usar parâmetros posicionais com atividades e cmdlets em um fluxo de trabalho.  Tudo o que isso significa é que você deve usar nomes de parâmetro.

Por exemplo, considere o código a seguir que obtém todos os serviços em execução.

     Get-Service | Where-Object {$_.Status -eq "Running"}

Se você tentar executar esse mesmo código em um fluxo de trabalho, receberá uma mensagem como “O conjunto de parâmetros não pode ser resolvido usando os parâmetros nomeados especificados”.  Para corrigir isso, basta fornece o nome do parâmetro como a seguir.

    Workflow Get-RunningServices
    {
        Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
    }

### <a name="deserialized-objects"></a>Objetos desserializados
Os objetos em fluxos de trabalho são desserializados.  Isso significa que suas propriedades ainda estão disponíveis, mas não seus métodos.  Por exemplo, considere código do PowerShell a seguir que para um serviço usando o método Stop do objeto Service.

    $Service = Get-Service -Name MyService
    $Service.Stop()

Se você tentar executá-lo em um fluxo de trabalho, obterá um erro dizendo “Não há suporte para a invocação de métodos em um Fluxo de Trabalho do Windows PowerShell”.  

Uma opção é encapsular essas duas linhas de código em um bloco [InlineScript](#inlinescript); neste caso, $Service seria um objeto de serviço dentro do bloco.

    Workflow Stop-Service
    {
        InlineScript {
            $Service = Get-Service -Name MyService
            $Service.Stop()
        }
    }

Outra opção é usar outro cmdlet que executa a mesma funcionalidade que o método, se houver um disponível.  No nosso exemplo, o cmdlet Stop-Service fornece a mesma funcionalidade que o método Stop e você pode usar o seguinte para um fluxo de trabalho.

    Workflow Stop-MyService
    {
        $Service = Get-Service -Name MyService
        Stop-Service -Name $Service.Name
    }


## <a name="inlinescript"></a>InlineScript
A atividade **InlineScript** é útil quando você precisa executar um ou mais comandos como um script do PowerShell tradicional em vez do fluxo de trabalho do PowerShell.  Enquanto os comandos em um fluxo de trabalho são enviados ao Windows Workflow Foundation para processamento, os comandos em um bloco de InlineScript são processados pelo Windows PowerShell.

O InlineScript usa a sintaxe mostrada abaixo.

    InlineScript
    {
      <Script Block>
    } <Common Parameters>

Você pode retornar a saída de um InlineScript atribuindo a saída a uma variável. O exemplo a seguir para um serviço e, em seguida, gera o nome do serviço.

    Workflow Stop-MyService
    {
        $Output = InlineScript {
            $Service = Get-Service -Name MyService
            $Service.Stop()
            $Service
        }

        $Output.Name
    }


Você pode passar valores para um bloco InlineScript, mas deve usar o modificador de escopo **$Using** .  O exemplo a seguir é idêntico ao exemplo anterior, exceto que o nome do serviço é fornecido por uma variável.

    Workflow Stop-MyService
    {
        $ServiceName = "MyService"

        $Output = InlineScript {
            $Service = Get-Service -Name $Using:ServiceName
            $Service.Stop()
            $Service
        }

        $Output.Name
    }


Embora as atividades InlineScript possam ser essenciais em determinados fluxos de trabalho, elas não dão suporte a construtores de fluxo de trabalho e devem ser usadas somente quando necessário pelos seguintes motivos:

* Não é possível usar [pontos de verificação](#checkpoints) dentro de um bloco InlineScript. Se uma falha ocorre dentro do bloco, ele deve ser retomado desde o início do bloco.
* Não é possível usar a [execução paralela](#parallel-processing) dentro de um bloco InlineScript.
* O InlineScript afeta a escalabilidade do fluxo de trabalho, uma vez que retém a sessão do Windows PowerShell por toda a duração do bloco de InlineScript.

Para obter mais detalhes sobre como usar o InlineScript, consulte [Executando Comandos do Windows PowerShell em um Fluxo de Trabalho](http://technet.microsoft.com/library/jj574197.aspx) e [about_InlineScript](http://technet.microsoft.com/library/jj649082.aspx).

## <a name="parallel-processing"></a>Processamento paralelo
Uma vantagem dos Fluxos de Trabalho do Windows PowerShell é a capacidade de executar um conjunto de comandos em paralelo em vez de sequencialmente como com um script típico.

Você pode usar a palavra-chave **Parallel** para criar um bloco de script com vários comandos que serão executados simultaneamente. Isso é feito através da sintaxe mostrada abaixo. Nesse caso, a Atividade1 e a Atividade2 vão começar ao mesmo tempo. A Atividade3 somente começará após conclusão da Atividade1 e da Atividade2.

    Parallel
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>


Por exemplo, considere os seguintes comandos do PowerShell que copiam vários arquivos para um destino de rede.  Esses comandos são executados em sequência, de forma que a cópia de um arquivo deve terminar antes que a do próximo seja iniciada.     

    $Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
    $Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
    $Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt

O fluxo de trabalho a seguir executa esses mesmos comandos em paralelo para que todos eles comecem a copiar ao mesmo tempo.  Somente depois que todos estiverem completamente copiados a mensagem de conclusão é exibida.

    Workflow Copy-Files
    {
        Parallel
        {
            $Copy-Item -Path "C:\LocalPath\File1.txt" -Destination "\\NetworkPath"
            $Copy-Item -Path "C:\LocalPath\File2.txt" -Destination "\\NetworkPath"
            $Copy-Item -Path "C:\LocalPath\File3.txt" -Destination "\\NetworkPath"
        }

        Write-Output "Files copied."
    }


Você pode usar o construct **ForEach-Parallel** para processar comandos de cada item em uma coleção simultaneamente. Os itens na coleção são processados em paralelo, enquanto os comandos no bloco de script são executados em sequência. Isso é feito através da sintaxe mostrada abaixo. Nesse caso, a Atividade1 será iniciada ao mesmo tempo para todos os itens na coleção. Para cada item, a Atividade2 será iniciada após a conclusão da Atividade1. A Atividade3 somente começará depois que a Atividade1 e a Atividade2 forem concluídas para todos os itens.

    ForEach -Parallel ($<item> in $<collection>)
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

O exemplo a seguir é semelhante ao exemplo anterior copiando os arquivos em paralelo.  Nesse caso, uma mensagem é exibida para cada arquivo depois de copiar.  Somente depois que todos estiverem completamente copiados a mensagem de conclusão final é exibida.

    Workflow Copy-Files
    {
        $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

        ForEach -Parallel ($File in $Files)
        {
            $Copy-Item -Path $File -Destination \\NetworkPath
            Write-Output "$File copied."
        }

        Write-Output "All files copied."
    }

> [!NOTE]
> Não recomendamos a execução de runbooks filho em paralelo, uma vez que isso demonstrou fornecer resultados não confiáveis.  Às vezes, a saída do runbook filho não será exibida e as configurações em um runbook filho podem afetar os outros runbooks filho paralelos
>

## <a name="checkpoints"></a>pontos de verificação
Um *ponto de verificação* é um instantâneo do estado atual do fluxo de trabalho que inclui o valor atual de variáveis e as saídas geradas para aquele ponto. Se um fluxo de trabalho terminar em erro ou se for suspenso, na próxima vez que ele for executado, iniciará no seu último ponto de verificação e não no início do fluxo de trabalho.  Você pode definir um ponto de verificação em um fluxo de trabalho com a atividade **Checkpoint-Workflow** .

No código de exemplo a seguir, uma exceção ocorre após Activity2, fazendo com que o fluxo de trabalho seja encerrado. Quando o fluxo de trabalho é executado novamente, ele começa pela execução de Activity2, já que isso foi logo após o último ponto de verificação definido.

    <Activity1>
    Checkpoint-Workflow
    <Activity2>
    <Exception>
    <Activity3>

Você deve definir pontos de verificação em um fluxo de trabalho para depois de atividades que possam estar sujeitas a exceção e não devam ser repetidas se o fluxo de trabalho for retomado. Por exemplo, o fluxo de trabalho pode criar uma máquina virtual. Você pode definir um ponto de verificação antes e depois dos comandos para criar a máquina virtual. Se a criação falhar, os comandos serão repetidos se o fluxo de trabalho for iniciado novamente. Se o fluxo de trabalho falhar após a criação bem-sucedida, a máquina virtual não será ser criada novamente quando o fluxo de trabalho for retomado.

O exemplo a seguir copia vários arquivos para um local de rede e define um ponto de verificação depois de cada arquivo.  Se o local de rede for perdido, o fluxo de trabalho terminará em erro.  Quando ele for iniciado novamente, ele continuará no último ponto de verificação, o que significa que somente os arquivos que já foram copiados serão ignorados.

    Workflow Copy-Files
    {
        $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

        ForEach ($File in $Files)
        {
            $Copy-Item -Path $File -Destination \\NetworkPath
            Write-Output "$File copied."
            Checkpoint-Workflow
        }

        Write-Output "All files copied."
    }

Como as credenciais do nome de usuário não são mantidas depois de chamar a atividade [Suspend-Workflow](https://technet.microsoft.com/library/jj733586.aspx) ou após o último ponto de verificação, você precisa definir as credenciais para null e recuperá-las novamente no armazenamento de ativos após **Suspend-Workflow** ou o ponto de verificação ser chamado.  Caso contrário, você pode receber a seguinte mensagem de erro: *não é possível continuar o fluxo de trabalho, ou porque não foi possível salvar completamente os dados de persistência, ou porque os dados de persistência salvos estão corrompidos. Você deve reiniciar o fluxo de trabalho.*

O mesmo código a seguir demonstra como lidar com isso em seus runbooks do Fluxo de Trabalho do PowerShell.

    workflow CreateTestVms
    {
       $Cred = Get-AzureAutomationCredential -Name "MyCredential"
       $null = Add-AzureRmAccount -Credential $Cred

       $VmsToCreate = Get-AzureAutomationVariable -Name "VmsToCreate"

       foreach ($VmName in $VmsToCreate)
         {
          # Do work first to create the VM (code not shown)

          # Now add the VM
          New-AzureRmVm -VM $Vm -Location "WestUs" -ResourceGroupName "ResourceGroup01"

          # Checkpoint so that VM creation is not repeated if workflow suspends
          $Cred = $null
          Checkpoint-Workflow
          $Cred = Get-AzureAutomationCredential -Name "MyCredential"
          $null = Add-AzureRmAccount -Credential $Cred
         }
     }


Isso não é necessário se você estiver autenticando usando uma conta Executar como configurada com uma entidade de serviço.  

Para saber mais sobre pontos de verificação, confira [Adicionando pontos de verificação a um Fluxo de Trabalho de script](http://technet.microsoft.com/library/jj574114.aspx).

## <a name="next-steps"></a>Próximas etapas
* Para começar a usar runbooks de fluxo de trabalho do PowerShell, veja [Meu primeiro runbook de Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md)



<!--HONumber=Jan17_HO4-->


