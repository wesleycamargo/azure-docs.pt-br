---
title: Enviar as métricas do SO convidado ao Serviço de Nuvem clássico de armazenamento de métricas do Azure Monitor
description: Enviar as métricas do SO convidado ao Serviço de Nuvem clássico de armazenamento de métricas do Azure Monitor
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: be27ff3f8dda3209a011c3ad79d1a7a1f1d259fe
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46986907"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-service"></a>Enviar as métricas do SO convidado ao Serviço de Nuvem clássico de armazenamento de métricas do Azure Monitor

A [extensão WAD (Diagnóstico do Azure do Windows)](azure-diagnostics.md) do Azure Monitor permite que você colete logs e métricas do SO convidado (sistema operacional convidado) executado como parte de uma Máquina Virtual, um Serviço de Nuvem ou um Service Fabric.  A extensão pode enviar telemetria para vários locais diferentes listados no artigo vinculado anteriormente.  

Este artigo descreve o processo para enviar métricas de desempenho do SO convidado dos Serviços de Nuvem clássicos do Azure para o armazenamento de métricas do Azure Monitor. Começando com o WAD versão 1.11, você pode gravar as métricas diretamente no armazenamento de métricas do Azure Monitor, no qual as métricas da plataforma padrão já são coletadas. Armazená-las neste local permite que você acesse as mesmas ações disponíveis para as métricas da plataforma.  As ações incluem alertas quase em tempo real, criação de gráficos, roteamento, acesso da API REST e muito mais.  Anteriormente, a extensão do WAD gravava no Armazenamento do Azure, mas não no armazenamento de dados do Azure Monitor.  

O processo mostrado neste artigo só funciona para contadores de desempenho nos Serviços de Nuvem do Azure. Ele não funciona para outras métricas personalizadas. 
   

## <a name="pre-requisites"></a>Pré-requisitos

- Você deve ser um [Administrador ou coadministrador de serviços](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator.md) em sua assinatura do Azure 

- Sua assinatura precisará ser registrada com o [Microsoft.Insights](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) 

- Você precisará ter o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) instalado ou poderá usar o [Azure CloudShell](https://docs.microsoft.com/azure/cloud-shell/overview.md) 


## <a name="provision-cloud-service-and-storage-account"></a>Provisionar Serviço de Nuvem e Conta de Armazenamento 

1. Crie e implante um Serviço de Nuvem clássico (um aplicativo de serviço de nuvem clássico de exemplo e sua implantação podem ser encontradas [aqui](../cloud-services/cloud-services-dotnet-get-started.md)). 

2. É possível usar uma conta de armazenamento existente ou implantar uma nova. É melhor se a conta de armazenamento estiver na mesma região que o Serviço de Nuvem clássico que você acabou de criar. No portal do Azure, navegue até a folha de recursos da Conta de Armazenamento e escolha **Chaves**. Anote o nome da conta de armazenamento e a chave da conta de armazenamento, pois você precisará dessas informações em etapas futuras.

   ![Chaves da conta de armazenamento](./media/metrics-store-custom-guestos-classic-cloud-service/storage-keys.png)



## <a name="create-a-service-principal"></a>Criar uma entidade de serviço 

Crie uma entidade de serviço no seu locatário do Azure Active Directory usando instruções encontradas em ../azure/azure-resource-manager/resource-group-create-service-principal-portal. Observe o seguinte ao percorrer este processo: 
  - Você pode colocar qualquer URL na URL de logon.  
  - Criar novo segredo do cliente para esse aplicativo  
  - Salve a Chave e a ID do cliente para serem usadas em etapas posteriores.  

Ao aplicativo criado na etapa anterior *Editor de métricas de monitoramento*, dê as permissões ao recurso do qual deseja emitir métricas. Se você planeja usar o aplicativo para emitir métricas personalizadas de vários recursos, conceda essas permissões no nível da assinatura ou do grupo de recursos.  

> [!NOTE]
> A Extensão de Diagnóstico usa a entidade de serviço para autenticação no Azure Monitor e emissão de métricas para seu serviço de nuvem 

## <a name="author-diagnostics-extension-configuration"></a>Criar Configuração de extensão de diagnóstico 

Prepare o arquivo de configuração de extensão de diagnóstico do WAD. Esse arquivo determina quais logs e contadores de desempenho a extensão de diagnóstico deve coletar para seu serviço de nuvem. A seguir, há um arquivo de configuração de diagnóstico como exemplo.  

```XML
<?xml version="1.0" encoding="utf-8"?> 
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
    <WadCfg> 
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096"> 
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" /> 
        <Directories scheduledTransferPeriod="PT1M"> 
          <IISLogs containerName="wad-iis-logfiles" /> 
          <FailedRequestLogs containerName="wad-failedrequestlogs" /> 
        </Directories> 
        <PerformanceCounters scheduledTransferPeriod="PT1M"> 
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Page Faults/sec" sampleRate="PT15S" /> 
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

Na seção "SinksConfig" do seu arquivo de diagnóstico, defina um novo coletor do Azure Monitor: 

```XML
  <SinksConfig> 
    <Sink name="AzMonSink"> 
    <AzureMonitor> 
      <ResourceId>-Provide ClassicCloudService’s Resource ID-</ResourceId> 
      <Region>-Azure Region your Cloud Service is deployed in-</Region> 
    </AzureMonitor> 
    </Sink> 
  </SinksConfig> 
```

Na seção de seu arquivo de configuração na qual você lista os contadores de desempenho a serem coletados, adicione o coletor do Azure Monitor. Essa entrada garante que todos os contadores de desempenho especificados sejam roteados para o Azure Monitor como métricas. Fique à vontade para adicionar/remover contadores de desempenho conforme suas necessidades. 

```XML
<PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink"> 
 <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" /> 
  … 
</PerformanceCounters> 
```
Por fim, na configuração privada, adicione uma seção *Conta do Azure Monitor*. Insira a ID do cliente da entidade de serviço e o segredo que foram criados na etapa anterior. 

```XML
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <StorageAccount name="" endpoint="" /> 
    <AzureMonitorAccount> 
      <ServicePrincipalMeta> 
        <PrincipalId>clientId from step 3</PrincipalId> 
        <Secret>client secret from step 3</Secret> 
      </ServicePrincipalMeta> 
    </AzureMonitorAccount> 
</PrivateConfig> 
```
 
Salve esse arquivo de diagnóstico localmente.  

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>Implantar a extensão de diagnóstico para seu Serviço de Nuvem 

Inicie o PowerShell e faça logon no Azure 

```PowerShell
Login-AzureRmAccount 
```

Usando os comandos a seguir, armazene em variáveis os detalhes sobre a conta de armazenamento criados na etapa anterior. 

```PowerShell
$storage_account = <name of your storage account from step 3> 
$storage_keys = <storage account key from step 3> 
```
 
De modo semelhante, defina o caminho do arquivo de diagnóstico para uma variável usando o comando abaixo. 

```PowerShell
$diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>” 
```
 
Usando o comando a seguir, implante a extensão de diagnóstico para seu serviço de nuvem com o arquivo de diagnóstico com o coletor do Azure Monitor configurado 

```PowerShell
Set-AzureServiceDiagnosticsExtension -ServiceName <classicCloudServiceName> -StorageAccountName $storage_account -StorageAccountKey $storage_keys -DiagnosticsConfigurationPath $diagconfig 
```
 
> [!NOTE] 
> Ainda é obrigatório fornecer uma conta de armazenamento como parte da instalação da extensão de diagnóstico. Todos os logs e/ou contadores de desempenho especificados no arquivo de configuração de diagnóstico serão gravados na conta de armazenamento especificada.  

## <a name="plot-metrics-in-the-azure-portal"></a>Plotar métricas no portal do Azure 

Navegue até o portal do Azure 

 ![Métricas do portal do Azure](./media/metrics-store-custom-guestos-classic-cloud-service/navigate-metrics.png)

1. No menu à esquerda, clique em Monitor 

1. Na folha Monitor, clique na guia Métricas (versão prévia) 

1. Na lista suspensa do recurso, selecione seu serviço de nuvem clássico 

1. Na lista suspensa de namespaces, selecione **azure.vm.windows.guest** 

1. Na lista suspensa de métricas, selecione *Memória\Bytes Confirmados em Uso* 

Você pode optar por exibir a memória total usada por uma função específica e cada instância de função usando a filtragem de dimensão e dividindo recursos. 

 ![Métricas do portal do Azure](./media/metrics-store-custom-guestos-classic-cloud-service/metrics-graph.png)

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [métricas personalizadas](metrics-custom-overview.md).



