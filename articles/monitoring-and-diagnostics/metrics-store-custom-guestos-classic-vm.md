---
title: Enviar métricas do SO convidado para o armazenamento de dados do Azure Monitor para uma Máquina Virtual do Windows (clássico)
description: Enviar métricas do SO convidado para o armazenamento de dados do Azure Monitor para uma Máquina Virtual do Windows (clássico)
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.component: ''
ms.openlocfilehash: 235eda231dfb0f936bf55c7c8d93a8f709fdf9bc
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49954818"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-data-store-for-a-windows-virtual-machine-classic"></a>Enviar métricas do SO convidado para o armazenamento de dados do Azure Monitor para uma Máquina Virtual do Windows (clássico)

A [extensão WAD (Diagnóstico do Azure do Windows)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) do Azure Monitor permite que você colete logs e métricas do SO convidado (sistema operacional convidado) executado como parte de uma Máquina Virtual, Serviço de Nuvem ou Service Fabric. A extensão pode enviar telemetria para vários locais diferentes listados no artigo vinculado anteriormente.

Este artigo descreve o processo para enviar métricas de desempenho do SO convidado de uma Máquina Virtual do Windows (clássica) para o repositório de métricas do Azure Monitor. Começando no WAD versão 1.11, você pode gravar as métricas diretamente no repositório de métricas do Azure Monitor em que as métricas da plataforma padrão já são coletadas. Armazená-las nesse local permite que você acesse as mesmas ações disponíveis para as métricas da plataforma.  As ações incluem alertas quase em tempo real, criação de gráficos, roteamento, acesso a partir da API REST e muito mais.  Anteriormente, a extensão do WAD gravava no Armazenamento do Azure, mas não no armazenamento de dados do Azure Monitor. 

O processo descrito neste artigo funciona somente em Máquinas Virtuais clássicas que estão executando o sistema operacional Windows.

## <a name="pre-requisites"></a>Pré-requisitos

- Você deve ser um [Administrador ou coadministrador de serviços](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator.md) em sua assinatura do Azure 

- Sua assinatura precisará ser registrada com o [Microsoft.Insights](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) 

- Você precisará ter o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) instalado ou poderá usar o [Azure CloudShell](https://docs.microsoft.com/azure/cloud-shell/overview.md) 

## <a name="create-a-classic-virtual-machine-and-storage-account"></a>Criar uma Máquina Virtual Clássica e uma Conta de Armazenamento

1. Criar uma VM Clássica usando o portal do Azure ![Criar uma VM Clássica](./media/metrics-store-custom-guestos-classic-vm/create-classic-vm.png)

1. Ao criar essa VM, opte por criar uma conta de armazenamento clássica. Usaremos essa conta de armazenamento em etapas posteriores.

1. No portal do Azure, navegue até a folha de recursos Conta de Armazenamento e escolha as **Chaves**, então anote o nome da conta de armazenamento e a chave de conta de armazenamento. Você precisará dessas chaves em etapas posteriores ![Chaves de acesso de armazenamento](./media/metrics-store-custom-guestos-classic-vm/storage-access-keys.png)

## <a name="create-a-service-principal"></a>Criar uma Entidade de Serviço

Crie uma entidade de serviço no seu locatário do Azure Active Directory usando as instruções em [Criar uma entidade de serviço](../active-directory/develop/howto-create-service-principal-portal.md). Observe o seguinte ao percorrer este processo: 
- Criar novo segredo do cliente para este aplicativo  
- Salve a Chave e a ID do cliente para serem usadas em etapas posteriores.

Conceda a este aplicativo permissões de "Editor de Métricas de Monitoramento" para o recurso para o qual você deseja emitir métricas contra. Você pode usar um grupo de recursos ou uma assinatura inteira.  

> [!NOTE]
> A Extensão de Diagnóstico usará a entidade de serviço para autenticação no Azure Monitor e emitir métricas para sua VM clássica.

## <a name="author-diagnostics-extension-configuration"></a>Criar Configuração de Extensão de Diagnóstico

1. Prepare o arquivo de configuração de extensão de diagnóstico WAD. Esse arquivo determina quais logs e contadores de desempenho a extensão de diagnóstico deve coletar para sua VM Clássica. Veja abaixo um exemplo.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
            <Directories scheduledTransferPeriod="PT1M">
                <IISLogs containerName="wad-iis-logfiles" />
                <FailedRequestLogs containerName="wad-failedrequestlogs" />
            </Directories>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
                <PerformanceCounterConfiguration counterSpecifier="\Processor(*)\% Processor Time" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(*)\Disk Read Bytes/sec" sampleRate="PT15S" />
            </PerformanceCounters>
            <WindowsEventLog scheduledTransferPeriod="PT1M">
                <DataSource name="Application!*[System[(Level=1 or Level=2 or Level=3)]]" />
                <DataSource name="Windows Azure!*[System[(Level=1 or Level=2 or Level=3 or Level=4)]]" />
            </WindowsEventLog>
            <CrashDumps>
                <CrashDumpConfiguration processName="WaIISHost.exe" />
                <CrashDumpConfiguration processName="WaWorkerHost.exe" />
                <CrashDumpConfiguration processName="w3wp.exe" />
            </CrashDumps>
            <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Error" />
            <Metrics resourceId="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicCompute/virtualMachines/MyClassicVM">
                <MetricAggregation scheduledTransferPeriod="PT1M" />
                <MetricAggregation scheduledTransferPeriod="PT1H" />
            </Metrics>
        </DiagnosticMonitorConfiguration>
        <SinksConfig>
        </SinksConfig>
        </WadCfg>
        <StorageAccount />
    </PublicConfig>
    <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <StorageAccount name="" endpoint="" />
    </PrivateConfig>
    <IsEnabled>true</IsEnabled>
    </DiagnosticsConfiguration>
    ```
1. Na seção "SinksConfig" do seu arquivo de diagnóstico, defina um novo coletor do Azure Monitor:

    ```xml
    <SinksConfig>
        <Sink name="AzMonSink">
            <AzureMonitor>
                <ResourceId>Provide your Classic VM’s Resource ID </ResourceId>
                <Region>Region your VM is deployed in</Region>
            </AzureMonitor>
        </Sink>
    </SinksConfig>
    ```

1. Na seção de seu arquivo de configuração em que a lista de contadores de desempenho a serem coletados é apresentada, encaminhe os contadores de desempenho para o coletor do Azure Monitor "AzMonSink".

    ```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
    ```

1. Na configuração Privada, defina a conta do Azure Monitor e adicione as informações da entidade de serviço a serem usadas para emitir as métricas.

    ```xml
    <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="" endpoint="" />
        <AzureMonitorAccount>
            <ServicePrincipalMeta>
                <PrincipalId>clientId for your service principal</PrincipalId>
                <Secret>client secret of your service principal</Secret>
            </ServicePrincipalMeta>
        </AzureMonitorAccount>
    </PrivateConfig>
    ```

1. Salve esse arquivo localmente.

## <a name="deploy-diagnostics-extension-to-your-cloud-service"></a>Implantar a extensão de diagnóstico para seu Serviço de Nuvem

1. Inicie o PowerShell e entre

    ```powershell
    Login-AzureRmAccount
    ```

1. Comece configurando o contexto para a VM clássica

    ```powershell
    $VM = Get-AzureVM -ServiceName <VM’s Service_Name> -Name <VM Name>
    ```

1. Defina o contexto da conta de armazenamento clássico criada com a VM.

    ```powershell
    $StorageContext = New-AzureStorageContext -StorageAccountName <name of your storage account from earlier steps> -storageaccountkey "<storage account key from earlier steps>"
    ```

1.  Defina o caminho do arquivo de diagnóstico para uma variável usando o comando abaixo.

    ```powershell
    $diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>”
    ```

1.  Preparar a atualização para VM clássica com o arquivo de diagnóstico com o coletor do Azure Monitor configurado

    ```powershell
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $diagconfig -VM $VM -StorageContext $Storage_Context
    ```

1.  Implante a atualização em sua VM executando o comando a seguir

    ```powershell
    Update-AzureVM -ServiceName "ClassicVMWAD7216" -Name "ClassicVMWAD" -VM $VM_Update.VM
    ```

> [!NOTE]
> É obrigatório ainda fornecer uma Conta de Armazenamento como parte da instalação da extensão de diagnóstico. Todos os logs e/ou contadores de desempenho especificados no arquivo de configuração de diagnóstico serão gravados na conta de armazenamento especificada.

## <a name="plot-the-metrics-in-the-azure-portal"></a>Plotar as métricas no portal do Azure

1.  Navegue até o portal do Azure

1.  No menu à esquerda, clique em Monitorar

1.  Na folha Monitor, clique em **Métricas**
   ![Métricas de navegação](./media/metrics-store-custom-guestos-classic-vm/navigate-metrics.png)

1. Na lista suspensa do recurso, selecione sua VM clássica

1. Na lista suspensa de namespaces, selecione **azure.vm.windows.guest**

1. Na lista suspensa de métricas, selecione **Memória\Bytes Confirmados em Uso**
   ![Plotar Métricas](./media/metrics-store-custom-guestos-classic-vm/plot-metrics.png)


## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [métricas personalizadas](metrics-custom-overview.md).
