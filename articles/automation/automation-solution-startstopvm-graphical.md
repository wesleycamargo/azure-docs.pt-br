<properties 
	pageTitle="Iniciando e parando máquinas virtuais — Graph | Microsoft Azure"
	description="Versão da solução de Automação do Azure do Fluxo de Trabalho do PowerShell, incluindo runbooks para iniciar e parar máquinas virtuais clássicas."
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
	ms.date="09/25/2015"
	ms.author="bwren" />

# Solução de Automação do Azure — iniciando e parando máquinas virtuais

Essa solução de Automação do Azure inclui runbooks para iniciar e parar máquinas virtuais clássicas. Você pode usar essa solução para qualquer um destes objetivos:

- Usar os runbooks sem modificação em seu próprio ambiente. 
- Modificar os runbooks para executar funcionalidade personalizada.  
- Chamar os runbooks de outro runbook como parte de uma solução geral. 
- Usar os runbooks como tutoriais para saber sobre os conceitos de criação do runbook. 

> [AZURE.SELECTOR]
- [Graphical](automation-solution-startstopvm-graphical.md)
- [PowerShell Workflow](automation-solution-startstopvm-psworkflow.md)

Esta é a versão gráfica do runbook dessa solução. Ela também pode ser disponibilizada usando os [runbooks de Fluxo de Trabalho do PowerShell](automation-solutions-startstopvm-psworkflow.md).

## Obtendo a solução

Essa solução consiste em dois runbooks gráficos que você pode baixar dos links a seguir. Consulte a [versão do Fluxo de Trabalho do PowerShell](automation-solutions-startstopvm-psworkflow.md) dessa solução para obter links para os runbooks de Fluxo de Trabalho do PowerShell.


| Runbook | Link | Tipo | Descrição |
|:---|:---|:---|:---|
| StartAzureClassicVM | [Runbook gráfico Iniciar VM clássica do Azure](https://gallery.technet.microsoft.com/scriptcenter/Start-Azure-Classic-VM-c6067b3d) | Gráfico | Inicia todas as máquinas virtuais clássicas em uma assinatura do Azure ou todas as máquinas virtuais com um nome de serviço específico. |
| StopAzureClassicVM | [Runbook gráfico Parar VM clássica do Azure](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-Classic-VM-397819bd) | Gráfico | Para todas as máquinas virtuais em uma conta de automação ou todas as máquinas virtuais com um nome de serviço específico. |


## Instalando a solução

### 1\. Instalar os runbooks

Depois de baixar os runbooks, você poderá importá-los usando o procedimento em [Procedimentos de runbook gráfico](automation-graphical-authoring-intro.md#graphical-runbook-procedures).

### 2\. Analisar a descrição e os requisitos
Os runbooks incluem uma atividade chamada **Leia-me**, que apresenta uma descrição e ativos necessários. Você pode exibir essas informações selecionando a atividade **Leia-me** e, em seguida, o parâmetro **Script do Fluxo de Trabalho**. Também é possível obter as mesmas informações neste artigo.

### 3\. Configurar ativos
Os runbooks exigem os ativos a seguir, que você deverá criar e preencher com os valores apropriados. Os nomes são padrão. Será possível usar ativos com nomes diferentes se você especificar esses nomes nos [parâmetros de entrada](#using-the-solution) quando inicia o runbook.

| Tipo de Ativo | Nome padrão | Descrição |
|:---|:---|:---|:---|
| [Credencial](automation-credentials.md) | AzureCredential | Contém as credenciais de uma conta com autoridade para iniciar e parar máquinas virtuais na assinatura do Azure. |
| [Variável](automation-variables.md) | AzureSubscriptionId | Contém a ID da sua assinatura do Azure. |

## Usando a solução

### Parâmetros

Cada um dos runbooks tem os [parâmetros de entrada](automation-starting-a-runbook#runbook-parameters) a seguir. Você deve fornecer valores para todos os parâmetros obrigatórios e pode, se desejar, fornecer valores para outros parâmetros de acordo com os próprios requisitos.

| Parâmetro | Tipo | Obrigatório | Descrição |
|:---|:---|:---|:---|
| ServiceName | string | Não | Se um valor for fornecido, todas as máquinas virtuais com esse nome de serviço serão iniciadas ou paradas. Se nenhum valor for fornecido, todas as máquinas virtuais clássicas na assinatura do Azure serão iniciadas ou paradas. |
| AzureSubscriptionIdAssetName | string | Não | Contém o nome do [ativo variável](#installing-the-solution) que contém a ID da sua assinatura do Azure. Se você não especificar um valor, *AzureSubscriptionId* será usado. |
| AzureCredentialAssetName | string | Não | Contém o nome do [ativo de credencial](#installing-the-solution) com as credenciais para o runbook a ser usado. Se você não especificar um valor, *AzureCredential* será usado. |

### Iniciando os runbooks

Você pode usar qualquer um dos métodos em [Iniciando um runbook na Automação do Azure](automation-starting-a-runbook.md) para iniciar qualquer um dos runbooks nesta solução.

O comando de exemplo a seguir usa o Windows PowerShell para executar **StartAzureClassicVM** e iniciar todas as máquinas virtuais com o nome do serviço *MyVMService*.

	$params = @{"ServiceName"="MyVMService"}
	Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "StartAzureClassicVM" –Parameters $params

### Saída

Os runbooks [emitirão uma mensagem](automation-runbook-output-and-messages.md) para cada máquina virtual, indicando se a instrução de inicialização ou interrupção foi enviada com êxito. Você pode procurar uma cadeia de caracteres específica na saída para determinar o resultado de cada runbook. As cadeias de caracteres de saída possíveis estão listadas na tabela a seguir.

| Runbook | Condição | Mensagem |
|:---|:---|:---|
| StartAzureClassicVM | A máquina virtual já está em execução | MyVM já está em execução |
| StartAzureClassicVM | Solicitação de inicialização para máquina virtual enviada com êxito | MyVM foi iniciada |
| StartAzureClassicVM | Falha na solicitação de inicialização para máquina virtual | Falha ao iniciar a MyVM |
| StopAzureClassicVM | A máquina virtual já está em execução | MyVM já foi parada |
| StopAzureClassicVM | Solicitação de inicialização para máquina virtual enviada com êxito | MyVM foi iniciada |
| StopAzureClassicVM | Falha na solicitação de inicialização para máquina virtual | Falha ao iniciar a MyVM |


A seguir, uma imagem de como usar **StartAzureClassicVM** com um [runbook filho](automation-child-runbooks.md) em um runbook gráfico de exemplo. Ela usa os links condicionais da tabela a seguir.

| Link | Critérios |
|:---|:---|
| Link de êxito | $ActivityOutput['StartAzureClassicVM'] -like "* foi iniciada" |
| Link de erro | $ActivityOutput['StartAzureClassicVM'] -notlike "* foi iniciada" |

![Exemplo de runbook filho](media/automation-solution-startstopvm/graphical-childrunbook-example.png)


## Divisão detalhada

Veja a seguir uma divisão detalhada dos runbooks nessa solução. Você pode usar essas informações para personalizar os runbooks ou apenas para aprender a criar suas próprias soluções.
 

### Autenticação

![Autenticação](media/automation-solution-startstopvm/graphical-authentication.png)

O runbook começa com atividades para definir as [credenciais](automation-configuring.md#configuring-authentication-to-azure-resources) e a assinatura do Azure que serão usadas para o restante do runbook.

As duas primeiras atividades, **Obter ID da Assinatura** e **Obter Credencial do Azure**, recuperam os [ativos](#installing-the-solution) usados pelas próximas duas atividades. Essas atividades podem especificar diretamente os ativos, mas elas precisam dos nomes do ativo. Uma vez que estamos permitindo ao usuário especificar esses nomes nos [parâmetros de entrada](#using-the-solution), precisamos dessas atividades para recuperar os ativos com um nome especificado por um parâmetro de entrada.

**Add-AzureAccount** define as credenciais que serão usadas no restante do runbook. O ativo credencial recuperado de **Obter Credencial do Azure** deve ter acesso para iniciar e parar máquinas virtuais na assinatura do Azure. A assinatura usada é selecionada por **Select-AzureSubscription** que usa a Id da assinatura de **Obter a Id da assinatura**.

### Obter máquinas virtuais

![Obter VMs](media/automation-solution-startstopvm/graphical-getvms.png)

O runbook precisa determinar com quais máquinas virtuais ele trabalhará e se elas já tiverem sido iniciadas ou paradas (dependendo do runbook). Uma das duas atividades vai recuperar as VMs. **Obter VMs em Serviço** será executado se o parâmetro de entrada *ServiceName* para o runbook contiver um valor. **Obter Todas as VMs** será executado se o parâmetro de entrada *ServiceName* para o runbook não contiver um valor. Essa lógica é executada pelos links condicionais que precedem cada atividade.

Ambas as atividades usam o cmdlet **Get-AzureVM**. **Obter Todas as VMs** usa o conjunto de parâmetros **ListAllVMs** para retornar todas as máquinas virtuais. **Obter VMs em Serviço** usa o conjunto de parâmetros **GetVMByServiceAndVMName** e fornece o parâmetro de entrada **ServiceName** para o parâmetro **ServiceName**.

### Mesclar VMs

![Mesclar VMs](media/automation-solution-startstopvm/graphical-mergevms.png)

A atividade **Mesclar VMs** é exigida para fornecer entrada para **Start-AzureVM**, que precisa do nome e do nome do serviço das VMs a serem iniciadas. Essa entrada pode ser originada em **Obter Todas as VMs** ou **Obter VMs em Serviço**, mas **Start-AzureVM** pode apenas especificar uma atividade para sua entrada.

A solução é criar **Mesclar VMs** que executa o cmdlet **Write-Output**. O parâmetro **InputObject** para esse cmdlet é uma Expressão do PowerShell que combina a entrada das duas atividades anteriores. Apenas uma dessas atividades será executada, de modo que apenas um conjunto de saída é esperado. **Start-AzureVM** pode usar essa saída para seus parâmetros de entrada.

### Iniciar/parar máquinas virtuais

![Iniciar VMs](media/automation-solution-startstopvm/graphical-startvm.png) ![Parar VMs](media/automation-solution-startstopvm/graphical-stopvm.png)

Dependendo do runbook, as próximas atividades tentam iniciar ou parar o runbook usando **Start-AzureVM** ou **Stop-AzureVM**. Uma vez que a atividade é precedida por um link de pipeline, ela será executada uma vez para cada objeto retornado de **Mesclar VMs**. O link é condicional, de modo que a atividade será executada somente se *RunningState* da máquina virtual for *Parado* para **Start-AzureVM** e *Iniciado* para **Stop-AzureVM**. Se essa condição não for atendida, **Notificar Já Iniciadas** ou **Notificar Já Interrompidas** será executada para enviar uma mensagem usando **Write-Output**.

### Enviar saída

![Notificar Iniciar VMs](media/automation-solution-startstopvm/graphical-notifystart.png) ![Notificar Parar VMs](media/automation-solution-startstopvm/graphical-notifystop.png)

A etapa final no runbook é enviar a saída, se a solicitação de inicialização ou de interrupção de cada máquina virtual tiver sido enviada com êxito. Há uma atividade **Write-Output** separada para cada uma e determinamos qual delas executar com links condicionais. **Notificar VM Iniciada** ou **Notificar VM Parada** será executada se *OperationStatus* for *Êxito*. Se *OperationStatus* tiver qualquer outro valor, **Notificar Falhou ao Iniciar** ou **Notificar Falhou ao Parar** será executada.


## Artigos relacionados

- [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md)
- [Runbooks filhos na Automação do Azure](automation-child-runbooks.md) 
- [Saída de runbook e mensagens na Automação do Azure](automation-runbook-output-and-messages.md)

<!---HONumber=Oct15_HO3-->