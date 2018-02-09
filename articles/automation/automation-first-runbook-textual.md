---
title: "O meu primeiro runbook de Fluxo de Trabalho do PowerShell na Automação do Azure | Microsoft Docs"
description: "Tutorial que orienta você pela criação, teste e publicação de um runbook textual simples usando o Fluxo de Trabalho do PowerShell."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: 
keywords: fluxo de trabalho do powershell, exemplos de fluxo de trabalho do powershell, fluxo de trabalho powershell
ms.assetid: 0002d7f7-e2b5-46e3-b5eb-4596b84fd526
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 62cb96075deed6c252ae44c8a000e820cb691a90
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2018
---
# <a name="my-first-powershell-workflow-runbook"></a>Meu primeiro runbook de Fluxo de Trabalho do PowerShell

> [!div class="op_single_selector"]
> * [Gráfico](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 
> 

Este tutorial orienta você durante a criação de um [runbook de Fluxo de Trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) na Automação do Azure. Você começa com um runbook simples, que testa e publica enquanto explica como acompanhar o status do trabalho do runbook. Em seguida, você modifica o runbook para gerenciar os recursos do Azure, neste caso, iniciando uma máquina virtual do Azure. Depois, você torna o runbook mais robusto adicionando parâmetros de runbook.

## <a name="prerequisites"></a>pré-requisitos
Para concluir este tutorial, você precisará do seguinte:

* Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automação](automation-offering-get-started.md) para manter o runbook e se autenticar nos recursos do Azure.  Esta conta deve ter permissão para iniciar e parar a máquina virtual.
* Uma máquina virtual do Azure. Você para e inicia essa máquina, portanto, ele não deve ser uma VM de produção.

## <a name="step-1---create-new-runbook"></a>Etapa 1: criar o novo runbook
Você começa criando um runbook simples que exibe o texto *Olá, Mundo*.

1. No portal do Azure, abra sua conta da Automação.

   A página da conta de Automação fornece uma exibição rápida dos recursos nessa conta. Você já deve ter alguns ativos. A maioria deles são os módulos que são incluídos automaticamente em uma nova conta de Automação. Você também deve ter o ativo de credencial que é mencionado nos [pré-requisitos](#prerequisites).

1. Clique em **Runbooks** em **Automação de Processos** para abrir a lista de runbooks.
2. Crie um novo runbook clicando no botão **+ Adicionar um runbook** e em **Criar um novo runbook**.
3. Atribua o nome *MyFirstRunbook-Workflow*ao runbook.
4. Nesse caso, você criará um [runbook de Fluxo de Trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks), portanto, selecione **Fluxo de Trabalho do PowerShell** como o **Tipo de runbook**.
5. Clique em **criar** para criar o runbook e abrir o editor textual.

## <a name="step-2---add-code-to-the-runbook"></a>Etapa 2 - adicionar código ao runbook
Você pode digitar o código diretamente no runbook, ou você pode selecionar cmdlets, runbooks, e ativos do controle de biblioteca e adicioná-los ao runbook com quaisquer parâmetros relacionados. Para este passo a passo, você digita diretamente no runbook.

1. No momento, seu runbook está vazio, com apenas a palavra-chave *fluxo de trabalho* necessária, o nome do seu runbook e as chaves que encerrarão o fluxo de trabalho inteiro.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```
2. Digite *Write-Output "Hello World."* entre as chaves.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```
3. Salve o runbook clicando em **Salvar**.

## <a name="step-3---test-the-runbook"></a>Etapa 3: testar o runbook
Antes de publicar o runbook para disponibilizá-lo na produção, teste-o para verificar se ele funciona corretamente. Quando você testa um runbook, executa sua versão de **Rascunho** e vê sua saída interativamente.

1. Clique em **Painel de teste** para abrir o Painel de teste.
2. Clique em **Iniciar** para iniciar o teste. Essa deve ser a única opção habilitada.
3. Um [trabalho de runbook](automation-runbook-execution.md) é criado e seu status é exibido.

   O status do trabalho iniciará como *Na fila* , indicando que ele está aguardando um runbook worker na nuvem ficar disponível. E ele muda para *Iniciando* quando um trabalhador reivindica o trabalho e para *Executando* quando o runbook realmente começa a ser executado.  

1. Quando o trabalho do runbook é concluído, sua saída é exibida. No seu caso, você deve ver *Olá, Mundo*.<br><br> ![Olá mundo](media/automation-first-runbook-textual/test-output-hello-world.png)
2. Feche o Painel de teste para retornar à tela.

## <a name="step-4---publish-and-start-the-runbook"></a>Etapa 4: publicar e iniciar o runbook
O runbook criado ainda está em modo de Rascunho. Você precisa publicá-lo antes que possa executá-lo na produção. Quando você publica um runbook, substitui a versão Publicada existente pela versão de Rascunho. No seu caso, ainda não há uma versão publicada porque você acabou de criar o runbook.

1. Clique em **Publicar** para publicar o runbook e em **Sim** quando solicitado.
2. Se você rolar para a esquerda para exibir o runbook no painel **Runbooks**, ele mostrará o **Status de Criação** **Publicado**.
3. Role para a direita para exibir o painel de **MyFirstRunbook-Workflow**.  
   As opções na parte superior nos permitem iniciar o runbook, agendá-lo para iniciar em algum momento no futuro ou criar um [webhook](automation-webhooks.md) para que ele possa ser iniciado por meio de uma chamada de HTTP.
4. Você quer apenas iniciar o runbook, então clique em **Iniciar** e em **Sim** quando solicitado.<br><br> ![Iniciar runbook](media/automation-first-runbook-textual/automation-runbook-controls-start.png)
5. Um painel de trabalho é aberto para o trabalho de runbook criado. Você pode fechar esse painel, mas neste caso, deixe-o aberto para acompanhar o progresso do trabalho.
6. O status do trabalho é mostrado em **Resumo do trabalho** e corresponde aos status que você viu quando testou o runbook.<br><br> ![Resumo do trabalho](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)
7. Assim que o status do runbook mostrar *Concluído*, clique em **Saída**. O painel Saída é aberto e você pode ver seu *Olá, Mundo*.<br><br> ![Resumo do trabalho](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)  
8. Feche o painel Saída.
9. Clique em **Todos os Logs** para abrir o painel Fluxos do trabalho do runbook. Você só deve ver *Olá, Mundo* no fluxo de saída, mas isso pode mostrar outros fluxos de um trabalho do runbook como Detalhado e Erro se o runbook gravar neles.<br><br> ![Resumo do trabalho](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)
10. Feche o painel Fluxos e o painel Trabalho para retornar ao painel MyFirstRunbook.
11. Clique em **Trabalhos** para abrir o painel de trabalhos para este runbook. Ele lista todos os trabalhos criados por esse runbook. Você deve ver apenas um trabalho listado, já que executou o trabalho apenas uma vez.<br><br> ![Trabalhos](media/automation-first-runbook-textual/runbook-control-job-tile.png)
12. Você pode clicar nesse trabalho para abrir o mesmo painel do Trabalho exibido ao iniciar o runbook. Isso permite que você volte no tempo e veja os detalhes de qualquer trabalho que foi criado para um determinado runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Etapa 5: adicionar autenticação para gerenciar recursos do Azure
Você testou e publicou seu runbook, mas, até o momento, ele não faz nada útil. Você gostaria que ele gerenciasse recursos do Azure. No entanto, ele não será capaz de fazer isso a menos seja autenticado usando as credenciais mencionadas nos [pré-requisitos](#prerequisites). Você faz isso com o cmdlet **Add-AzureRmAccount**.

1. Abra o editor gráfico clicando em **Editar** no painel MyFirstRunbook-Workflow.
2. Você não precisa mais da linha **Write-Output**, então vamos continuar e excluí-la.
3. Posicione o cursor em uma linha em branco entre as chaves.
4. Digite ou copie e cole o seguinte código que irá manipular a autenticação com sua conta de Automação Executar Como:

   ```powershell-interactive
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   ```
5. Clique em **Painel de teste** para que você possa testar o runbook.
6. Clique em **Iniciar** para iniciar o teste. Quando ele for concluído, você deverá receber uma saída semelhante ao exemplo a seguir, que exibe informações básicas de sua conta. Isso confirma que a credencial é válida.<br><br> ![Autenticar](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Etapa 6: adicionar código para iniciar uma máquina virtual
Agora que o seu runbook está se autenticando na sua assinatura do Azure, você pode gerenciar os recursos. Você adiciona um comando para iniciar uma máquina virtual. Você pode escolher qualquer máquina virtual em sua assinatura do Azure e, por enquanto, codificará esse nome no runbook.

1. Depois de *Add-AzureRmAccount*, digite *Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'NameofResourceGroup'*, fornecendo o nome da máquina virtual a iniciar e o nome do Grupo de Recursos dessa máquina virtual.  

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   }
   ```
2. Salve o runbook e, para que possa testá-lo, clique em **Painel de teste**.
3. Clique em **Iniciar** para iniciar o teste. Quando for concluído, verifique se a máquina virtual foi iniciada.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Etapa 7: adicionar um parâmetro de entrada ao runbook
No momento, o seu runbook inicia a máquina virtual que você codificou no runbook, mas ele seria mais útil se você especificasse a máquina virtual quando o runbook fosse iniciado. Agora, você adiciona os parâmetros de entrada ao runbook para fornecer essa funcionalidade.

1. Adicione parâmetros para *VMName* e *ResourceGroupName* ao runbook e use essas variáveis com o cmdlet **Start-AzureRmVM** como no exemplo abaixo.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )  
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```
2. Salve o runbook e abra o Painel de teste. Agora você pode fornecer valores para as duas variáveis de entrada que estão no teste.
3. Feche o Painel de teste.
4. Clique em **Publicar** para publicar a nova versão do runbook.
5. Pare a máquina virtual que você iniciou na etapa anterior.
6. Clique em **Iniciar** para iniciar o runbook. Digite o **VMName** e o **ResourceGroupName** da máquina virtual que você iniciará.<br><br> ![Start Runbook](media/automation-first-runbook-textual/automation-pass-params.png)<br>  
7. Quando o runbook for concluído, verifique se a máquina virtual foi iniciada.  

## <a name="next-steps"></a>Próximas etapas
* Para começar a usar os runbooks Gráficos, consulte [Meu primeiro runbook gráfico](automation-first-runbook-graphical.md)
* Para começar a usar os runbooks do PowerShell, veja [Meu primeiro runbook do PowerShell](automation-first-runbook-textual-powershell.md)
* Para aprender mais sobre os tipos de runbook, suas vantagens e limitações, veja [Tipos de runbook da Automação do Azure](automation-runbook-types.md)
* Para saber mais sobre o recurso de suporte a scripts do PowerShell, veja [Native PowerShell script support in Azure Automation (Suporte a scripts nativos do PowerShell na Automação do Azure)](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
