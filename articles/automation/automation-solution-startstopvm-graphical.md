---
title: "Iniciando e parando máquinas virtuais — Graph | Microsoft Docs"
description: "Versão do fluxo de trabalho do PowerShell do cenário de Automação do Azure, incluindo runbooks para iniciar e parar máquinas virtuais clássicas."
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
ms.assetid: 62d93170-ca3d-42ef-ac1d-6d50b61ac87d
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/06/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 00b217a4cddac0a893564db27ffb4f460973c246
ms.openlocfilehash: ba67183cf0518bee102579a8ab16a9be92ed7b73


---
# <a name="azure-automation-scenario---starting-and-stopping-virtual-machines"></a>Cenário de Automação do Azure — iniciando e parando máquinas virtuais
Esse cenário de Automação do Azure inclui runbooks para iniciar e parar máquinas virtuais clássicas.  Você pode usar esse cenário para qualquer um destes objetivos:  

* Usar os runbooks sem modificação em seu próprio ambiente.
* Modificar os runbooks para executar funcionalidade personalizada.  
* Chamar os runbooks de outro runbook como parte de uma solução geral.
* Usar os runbooks como tutoriais para saber sobre os conceitos de criação do runbook.

> [!div class="op_single_selector"]
> * [Gráfico](automation-solution-startstopvm-graphical.md)
> * [Fluxo de Trabalho do PowerShell](automation-solution-startstopvm-psworkflow.md)
>
>

Esta é a versão gráfica do runbook desse cenário. Ela também pode ser disponibilizada usando os [runbooks de Fluxo de Trabalho do PowerShell](automation-solution-startstopvm-psworkflow.md).

## <a name="getting-the-scenario"></a>Obtendo o cenário
Esse cenário é formado por dois runbooks gráficos que você pode baixar dos links a seguir.  Confira a [versão do Fluxo de Trabalho do PowerShell](automation-solution-startstopvm-psworkflow.md) desse cenário para obter links para os runbooks de Fluxo de Trabalho do PowerShell.

| Runbook | Link | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| StartAzureClassicVM |[Runbook gráfico Iniciar VM clássica do Azure](https://gallery.technet.microsoft.com/scriptcenter/Start-Azure-Classic-VM-c6067b3d) |Gráfico |Inicia todas as máquinas virtuais clássicas em uma assinatura do Azure ou todas as máquinas virtuais com um nome de serviço específico. |
| StopAzureClassicVM |[Runbook gráfico Parar VM clássica do Azure](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-Classic-VM-397819bd) |Gráfico |Para todas as máquinas virtuais em uma conta de automação ou todas as máquinas virtuais com um nome de serviço específico. |

## <a name="installing-and-configuring-the-scenario"></a>Instalando e configurando o cenário
### <a name="1-install-the-runbooks"></a>1. Instalar os runbooks
Depois de baixar os runbooks, você poderá importá-los usando o procedimento em [Procedimentos de runbook gráfico](automation-graphical-authoring-intro.md#graphical-runbook-procedures).

### <a name="2-review-the-description-and-requirements"></a>2. Analisar a descrição e os requisitos
Os runbooks incluem uma atividade chamada **Leia-me** , que apresenta uma descrição e ativos necessários.  Você pode exibir essas informações selecionando a atividade **Leia-me** e, em seguida, o parâmetro **Script do Fluxo de Trabalho**.  Também é possível obter as mesmas informações neste artigo.

### <a name="3-configure-assets"></a>3. Configurar ativos
Os runbooks exigem os ativos a seguir, que você deverá criar e preencher com os valores apropriados.  Os nomes são padrão.  Será possível usar ativos com nomes diferentes se você especificar esses nomes nos [parâmetros de entrada](#using-the-runbooks) quando inicia o runbook.

| Tipo de Ativo | Nome padrão | Descrição |
|:--- |:--- |:--- |:--- |
| [Credencial](automation-credentials.md) |AzureCredential |Contém as credenciais de uma conta com autoridade para iniciar e parar máquinas virtuais na assinatura do Azure. |
| [Variável](automation-variables.md) |AzureSubscriptionId |Contém a ID da sua assinatura do Azure. |

## <a name="using-the-scenario"></a>Usando o cenário
### <a name="parameters"></a>Parâmetros
Cada um dos runbooks tem os [parâmetros de entrada](automation-starting-a-runbook.md#runbook-parameters)a seguir.  Você deve fornecer valores para todos os parâmetros obrigatórios e pode, se desejar, fornecer valores para outros parâmetros de acordo com os próprios requisitos.

| Parâmetro | Tipo | Obrigatório | Descrição |
|:--- |:--- |:--- |:--- |
| ServiceName |string |Não |Se um valor for fornecido, todas as máquinas virtuais com esse nome de serviço serão iniciadas ou paradas.  Se nenhum valor for fornecido, todas as máquinas virtuais clássicas na assinatura do Azure serão iniciadas ou paradas. |
| AzureSubscriptionIdAssetName |string |Não |Contém o nome do [ativo variável](#installing-and-configuring-the-scenario) que contém a ID da sua assinatura do Azure.  Se você não especificar um valor, *AzureSubscriptionId* será usado. |
| AzureCredentialAssetName |string |Não |Contém o nome do [ativo de credencial](#installing-and-configuring-the-scenario) com as credenciais para o runbook a ser usado.  Se você não especificar um valor, *AzureCredential* será usado. |

### <a name="starting-the-runbooks"></a>Iniciando os runbooks
Você pode usar qualquer um dos métodos em [Iniciando um runbook na Automação do Azure](automation-starting-a-runbook.md) para iniciar qualquer um dos runbooks neste artigo.

O comando de exemplo a seguir usa o Windows PowerShell para executar **StartAzureClassicVM** e iniciar todas as máquinas virtuais com o nome do serviço *MyVMService*.

    $params = @{"ServiceName"="MyVMService"}
    Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "StartAzureClassicVM" –Parameters $params

### <a name="output"></a>Saída
Os runbooks [emitirão uma mensagem](automation-runbook-output-and-messages.md) para cada máquina virtual, indicando se a instrução de inicialização ou interrupção foi enviada com êxito.  Você pode procurar uma cadeia de caracteres específica na saída para determinar o resultado de cada runbook.  As cadeias de caracteres de saída possíveis estão listadas na tabela a seguir.

| Runbook | Condição | Mensagem |
|:--- |:--- |:--- |
| StartAzureClassicVM |A máquina virtual já está em execução |MyVM já está em execução |
| StartAzureClassicVM |Solicitação de inicialização para máquina virtual enviada com êxito |MyVM foi iniciada |
| StartAzureClassicVM |Falha na solicitação de inicialização para máquina virtual |Falha ao iniciar a MyVM |
| StopAzureClassicVM |A máquina virtual já está em execução |MyVM já foi parada |
| StopAzureClassicVM |Solicitação de inicialização para máquina virtual enviada com êxito |MyVM foi iniciada |
| StopAzureClassicVM |Falha na solicitação de inicialização para máquina virtual |Falha ao iniciar a MyVM |

A seguir, uma imagem de como usar **StartAzureClassicVM** com um [runbook filho](automation-child-runbooks.md) em um runbook gráfico de exemplo.  Ela usa os links condicionais da tabela a seguir.

| Link | Critérios |
|:--- |:--- |
| Link de êxito |$ActivityOutput['StartAzureClassicVM'] -like "\* foi iniciada" |
| Link de erro |$ActivityOutput['StartAzureClassicVM'] -notlike "\* foi iniciada" |

![Exemplo de runbook filho](media/automation-solution-startstopvm/graphical-childrunbook-example.png)

## <a name="detailed-breakdown"></a>Divisão detalhada
Veja a seguir uma divisão detalhada dos runbooks nesse cenário.  Você pode usar essas informações para personalizar os runbooks ou apenas para aprender a criar seus próprios cenários de automação.

### <a name="authentication"></a>Autenticação
![Autenticação](media/automation-solution-startstopvm/graphical-authentication.png)

O runbook começa com atividades para definir as [credenciais](automation-credentials.md) e a assinatura do Azure que serão usadas para o restante do runbook.

As primeiras duas atividades, **Obter ID de Assinatura** e **Obter Credenciais do Azure**, recuperam os [ativos](#installing-the-runbook) que serão usados pelas duas atividades.  Essas atividades podem especificar diretamente os ativos, mas elas precisam dos nomes do ativo.  Uma vez que estamos permitindo ao usuário especificar esses nomes nos [parâmetros de entrada](#using-the-runbooks), precisamos dessas atividades para recuperar os ativos com um nome especificado por um parâmetro de entrada.

**Add-AzureAccount** define as credenciais que serão usadas no restante do runbook.  O ativo credencial recuperado de **Obter Credencial do Azure** deve ter acesso para iniciar e parar máquinas virtuais na assinatura do Azure.  A assinatura usada é selecionada por **Select-AzureSubscription**, que usa a ID de assinatura de **Obter ID de Assinatura**.

### <a name="get-virtual-machines"></a>Obter máquinas virtuais
![Obter VMs](media/automation-solution-startstopvm/graphical-getvms.png)

O runbook precisa determinar com quais máquinas virtuais ele trabalhará e se elas já tiverem sido iniciadas ou paradas (dependendo do runbook).   Uma das duas atividades vai recuperar as VMs.  **Obter VMs em Serviço** será executado se o parâmetro de entrada *ServiceName* para o runbook contiver um valor.  **Obter Todas as VMs** será executado se o parâmetro de entrada *ServiceName* para o runbook não contiver um valor.  Essa lógica é executada pelos links condicionais que precedem cada atividade.

Ambas as atividades usam o cmdlet **Get-AzureVM** .  **Obter Todas as VMs** usa o conjunto de parâmetros **ListAllVMs** para retornar todas as máquinas virtuais.  **Obter VMs em Serviço** usa o conjunto de parâmetros **GetVMByServiceAndVMName** e fornece o parâmetro de entrada **ServiceName** para o parâmetro **ServiceName**.  

### <a name="merge-vms"></a>Mesclar VMs
![Mesclar VMs](media/automation-solution-startstopvm/graphical-mergevms.png)

A atividade **Mesclar VMs** é exigida para fornecer entrada para **Start-AzureVM**, que precisa do nome e do nome do serviço das VMs a serem iniciadas.  Essa entrada pode ser originada em **Obter Todas as VMs** ou **Obter VMs em Serviço**, mas **Start-AzureVM** pode apenas especificar uma atividade para sua entrada.   

A função do cenário é criar a opção **Mesclar VMs**, que executa o cmdlet **Write-Output**.  O parâmetro **InputObject** para esse cmdlet é uma Expressão do PowerShell que combina a entrada das duas atividades anteriores.  Apenas uma dessas atividades será executada, de modo que apenas um conjunto de saída é esperado.  **Start-AzureVM** pode usar essa saída para seus parâmetros de entrada.

### <a name="startstop-virtual-machines"></a>Iniciar/parar máquinas virtuais
![Iniciar VMs](media/automation-solution-startstopvm/graphical-startvm.png) ![Parar VMs](media/automation-solution-startstopvm/graphical-stopvm.png)

Dependendo do runbook, as próximas atividades tentam iniciar ou parar o runbook usando **Start-AzureVM** ou **Stop-AzureVM**.  Uma vez que a atividade é precedida por um link de pipeline, ela será executada uma vez para cada objeto retornado de **Mesclar VMs**.  O link é condicional, de modo que a atividade será executada somente se *RunningState* da máquina virtual for *Parado* para **Start-AzureVM** e *Iniciado* para **Stop-AzureVM**. Se essa condição não for atendida, **Notificar Já Iniciadas** ou **Notificar Já Interrompidas** será executada para enviar uma mensagem usando **Write-Output**.

### <a name="send-output"></a>Enviar saída
![Notificar Iniciar VMs](media/automation-solution-startstopvm/graphical-notifystart.png) ![Notificar Parar VMs](media/automation-solution-startstopvm/graphical-notifystop.png)

A etapa final no runbook é enviar a saída, se a solicitação de inicialização ou de interrupção de cada máquina virtual tiver sido enviada com êxito. Há uma atividade **Write-Output** separada para cada uma e determinamos qual delas executar com links condicionais.  **Notificar VM Iniciada** ou **Notificar VM Parada** será executada se *OperationStatus* for *Êxito*.  Se *OperationStatus* tiver qualquer outro valor, **Notificar Falhou ao Iniciar** ou **Notificar Falhou ao Parar** será executada.

## <a name="next-steps"></a>Próximas etapas
* [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md)
* [Runbooks filhos na Automação do Azure](automation-child-runbooks.md)
* [Saída de runbook e mensagens na Automação do Azure](automation-runbook-output-and-messages.md)



<!--HONumber=Nov16_HO3-->


