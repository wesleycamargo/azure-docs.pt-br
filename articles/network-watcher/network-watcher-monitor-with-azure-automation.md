---

title: "Monitorar os gateways de VPN com a solução de problemas do Observador de Rede do Azure | Microsoft Docs"
description: "Este artigo descreve como diagnosticar a conectividade local com a Automação e o Observador de Rede do Azure"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 9a6f42e9b7b737e9316dcc1ff39ea532c4b923c5
ms.lasthandoff: 03/29/2017


---

# <a name="monitor-vpn-gateways-with-network-watcher-troubleshooting"></a>Monitorar os gateways de VPN com a solução de problemas do Observador de Rede

Obter informações detalhadas sobre o desempenho da rede é essencial para fornecer serviços confiáveis aos clientes. Portanto, é fundamental detectar as condições de interrupção da rede rapidamente e tomar medidas corretivas para reduzir a condição de interrupção. A Automação do Azure permite implementar e executar uma tarefa de forma programática por meio de runbooks. Usar a Automação do Azure cria uma receita perfeita para realizar um monitoramento contínuo e proativo da rede e alertas.

## <a name="scenario"></a>Cenário

O cenário na imagem a seguir é um aplicativo de várias camadas, com uma conectividade local estabelecida usando um Gateway de VPN e túnel. Garantir que o Gateway de VPN esteja ativo e em execução é fundamental para o desempenho dos aplicativos.

Um runbook é criado com um script para verificar o status de conexão do túnel VPN, usando a API de Solução de Problemas do Recurso para verificar o status de conexão do túnel. Se o status não for íntegro, um gatilho de email será enviado para os administradores.

![Cenário de exemplo][scenario]

Este cenário:

- Criar um runbook chamando o cmdlet `Start-AzureRmNetworkWatcherResourceTroubleshooting` para solucionar os problemas de status da conexão
- Vincular uma agenda ao runbook

## <a name="before-you-begin"></a>Antes de começar

Antes de começar este tutorial, você deve ter os seguintes pré-requisitos:

- uma conta de automação no Azure.
- deve ter um conjunto de credenciais configurado na Automação do Azure. Saiba mais em [Segurança da Automação do Azure](../automation/automation-security-overview.md)
- Um servidor SMTP válido (Office 365, seu email local ou outro) e credenciais definidas na Automação do Azure
- Um Gateway de Rede Virtual configurado no Azure.

> [!NOTE]
> A infraestrutura representada na imagem anterior é para ilustrar e não é criada com as etapas contidas neste artigo.

### <a name="create-the-runbook"></a>Criar o runbook

A primeira etapa para configurar o exemplo é criar o runbook. Este exemplo usa uma conta do tipo executar como. Para saber mais sobre as contas executar como, visite [Autenticar Runbooks com conta Executar Como do Azure](../automation/automation-sec-configure-azure-runas-account.md#create-an-automation-account-from-the-azure-portal)

### <a name="step-1"></a>Etapa 1

Navegue para a Automação do Azure no [portal do Azure](https://portal.azure.com) e clique em **Runbooks**

![visão geral da conta de automação][1]

### <a name="step-2"></a>Etapa 2

Clique em **Adicionar um runbook** para iniciar o processo de criação do runbook.

![folha de runbooks][2]

### <a name="step-3"></a>Etapa 3

Em **Criação Rápida**, clique em **Criar um novo runbook** para criar o runbook.

![adicionar uma folha de runbook][3]

### <a name="step-4"></a>Etapa 4

Nesta etapa, fornecemos ao runbook um nome, no exemplo ele é denominado **Get-VPNGatewayStatus**. É importante dar um nome descritivo ao runbook e recomendável dar um nome que segue os padrões de nomenclatura do PowerShell. O tipo de runbook para este exemplo é **PowerShell**, as outras opções são Gráfico, fluxo de trabalho do PowerShell e fluxo de trabalho Gráfico do PowerShell.

![folha de runbook][4]

### <a name="step-5"></a>Etapa 5

Nesta etapa, o runbook é criado e o exemplo de código a seguir fornece todo o código necessário. Os itens no código que contêm um \<valor\> precisam ser substituídos pelos valores de sua assinatura.

Use o seguinte código e clique em **Salvar**

```PowerShell
# Get credentials for Office 365 account
$MyCredential = "Office 365 account"
$Cred = Get-AutomationPSCredential -Name $MyCredential

# Get the connection "AzureRunAsConnection "
$connectionName = "AzureRunAsConnection"
$servicePrincipalConnection=Get-AutomationConnection -Name $connectionName
$subscriptionId = "<subscription id>"
"Logging in to Azure..."
Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $servicePrincipalConnection.TenantId `
    -ApplicationId $servicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
"Setting context to a specific subscription"
Set-AzureRmContext -SubscriptionId $subscriptionId

$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name "2to3" -ResourceGroupName "testrg"
$sa = New-AzureRmStorageAccount -Name "contosoexamplesa" -SKU "Standard_LRS" -ResourceGroupName "testrg" -Location "WestCentralUS"
$result = Start-AzureRmNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath "$($sa.PrimaryEndpoints.Blob)logs"


if($result.code -ne "Healthy")
    {
        $Body = "Connection for ${vpnconnectionName} is: $($result.code). View the logs at $($sa.PrimaryEndpoints.Blob)logs to learn more."
        $subject = "${connectionname} Status"
        Send-MailMessage `
        -To 'admin@contoso.com' `
        -Subject $subject `
        -Body $Body `
        -UseSsl `
        -Port 587 `
        -SmtpServer 'smtp.office365.com' `
        -From "${$username}" `
        -BodyAsHtml `
        -Credential $Cred
    }
else
    {
    Write-Output ("Connection Status is: $($result.connectionStatus)")
    }
```

![Etapa 5][5]

### <a name="step-6"></a>Etapa 6

Depois que o runbook é salvo, uma agenda deve ser vinculada a ele para automatizar o início do runbook. Para iniciar o processo, clique em **Agendar**.

![Etapa 6][6]

## <a name="link-a-schedule-to-the-runbook"></a>Vincular uma agenda ao runbook

Deve ser criada uma nova agenda. Clique em **Vincular uma agenda ao runbook**.

![Etapa 7][7]

### <a name="step-1"></a>Etapa 1

Na folha **Agenda**, clique em **Criar uma nova agenda**

![Etapa 8][8]

### <a name="step-2"></a>Etapa 2

Na folha **Nova Agenda**, preencha as informações da agenda. Os valores que podem ser definidos estão na lista a seguir:

- **Nome** - um nome amigável da agenda.
- **Descrição** - uma descrição da agenda.
- **Inícios** - esse valor é uma combinação de data, hora e fuso horário que compõem o momento quando a agenda dispara.
- **Recorrência** - esse valor determina a repetição da agenda.  Os valores válidos são **Uma vez** ou **Recorrente**.
- **Repetir a cada** - o intervalo de recorrência da agenda em horas, dias, semanas ou meses.
- **Definir Expiração** - o valor determina se a agenda deve expirar ou não. Pode ser definido para **Sim** ou **Não**. Uma data e hora válidas devem ser fornecidas se sim for escolhido.

> [!NOTE]
> Se você precisar ter um runbook executado com mais frequência que a cada hora, várias agendas devem ser criadas em intervalos diferentes (ou seja, 15, 30, 45 minutos após a hora)

![Etapa 9][9]

### <a name="step-3"></a>Etapa 3

Clique em Salvar para salvar a agenda para o runbook.

![Etapa 10][10]

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma compreensão de como integrar a solução de problemas do Observador de Rede na Automação do Azure, saiba como a disparar capturas de pacotes nos alertas da VM visitando [Criar uma captura de pacotes disparada por alertas com o Observador de Rede do Azure](network-watcher-alert-triggered-packet-capture.md).

<!-- images -->
[scenario]: ./media/network-watcher-monitor-with-azure-automation/scenario.png
[1]: ./media/network-watcher-monitor-with-azure-automation/figure1.png
[2]: ./media/network-watcher-monitor-with-azure-automation/figure2.png
[3]: ./media/network-watcher-monitor-with-azure-automation/figure3.png
[4]: ./media/network-watcher-monitor-with-azure-automation/figure4.png
[5]: ./media/network-watcher-monitor-with-azure-automation/figure5.png
[6]: ./media/network-watcher-monitor-with-azure-automation/figure6.png
[7]: ./media/network-watcher-monitor-with-azure-automation/figure7.png
[8]: ./media/network-watcher-monitor-with-azure-automation/figure8.png
[9]: ./media/network-watcher-monitor-with-azure-automation/figure9.png
[10]: ./media/network-watcher-monitor-with-azure-automation/figure10.png

