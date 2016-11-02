<properties
    pageTitle="Usando o armazenamento de blobs para IIS e o armazenamento de tabelas para eventos | Microsoft Azure"
    description="O Log Analytics pode ler os logs para os serviços do Azure que gravam o diagnóstico no armazenamento de tabelas, ou então logs do IIS gravados no Armazenamento de Blobs."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>



# <a name="using-blob-storage-for-iis-and-table-storage-for-events"></a>Usando o armazenamento de blobs para IIS e o armazenamento de tabelas para eventos

O Log Analytics pode ler os logs para os serviços a seguir, que gravam o diagnóstico no armazenamento de tabelas, ou então logs do IIS gravados no Armazenamento de Blobs:

+ Clusters do Service Fabric (visualização)
+ Máquinas Virtuais
+ Funções Web/de Trabalho

Antes que o Log Analytics possa coletar dados para esses recursos, o diagnóstico do Azure deve ser habilitado.

Depois que os diagnósticos são habilitados, você pode usar a configuração do Log Analytics pelo Portal do Azure PowerShell para coletar os logs.

O Diagnóstico do Azure é uma extensão do Azure que permite coletar dados de diagnóstico de uma função de trabalho, função web ou máquina virtual em execução no Azure. Os dados são armazenados em uma conta de armazenamento do Azure e, em seguida, podem ser coletados pelo Log Analytics.

Para que o Log Analytics colete esses logs de diagnóstico do Azure, os logs devem estar nos seguintes locais:

| Tipo de Log | Tipo de recurso | Local |
| -------- | ------------- | -------- |
| Logs IIS | Máquinas Virtuais <br> Funções da Web <br> Funções de trabalho | wad-iis-logfiles (Armazenamento de Blobs) |
| syslog | Máquinas Virtuais | LinuxsyslogVer2v0 (Armazenamento de Tabelas) |
| Eventos operacionais do Service Fabric | Nós do Service Fabric | WADServiceFabricSystemEventTable |
| Eventos dos Reliable Actors do Service Fabric | Nós do Service Fabric | WADServiceFabricReliableActorEventTable |
| Arquitetura de Serviços Confiáveis do Service Fabric | Nós do Service Fabric | WADServiceFabricReliableServiceEventTable |
| Log de eventos do Windows | Nós do Service Fabric <br> Máquinas Virtuais <br> Funções da Web <br> Funções de trabalho | WADWindowsEventLogsTable (Armazenamento de Tabelas) |
| Logs do ETW do Windows | Nós do Service Fabric <br> Máquinas Virtuais <br> Funções da Web <br> Funções de trabalho | WADETWEventTable (Armazenamento de Tabelas) |

>[AZURE.NOTE] Atualmente, não há suporte para logs do IIS nos sites do Azure.

Para máquinas virtuais, você também tem a opção de instalar o [Microsoft Monitoring Agent](http://go.microsoft.com/fwlink/?LinkId=517269) na sua máquina virtual para habilitar insights adicionais. Além de ser capaz de analisar os logs do IIS e Logs de Eventos, você também poderá executar análises adicionais, inclusive controle de alterações de configuração, avaliação de SQL e avaliação de atualização.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection"></a>Habilitar os diagnósticos do Azure em uma máquina virtual para coleta de log de eventos e log do IIS

Use o procedimento a seguir para habilitar os diagnósticos do Azure em uma máquina virtual para coleta de log de eventos e log do IIS usando o Portal de Gerenciamento do Microsoft Azure.

### <a name="to-enable-azure-diagnostics-in-a-virtual-machine-with-the-azure-management-portal"></a>Para habilitar os diagnósticos do Azure em uma máquina virtual com o Portal de Gerenciamento do Azure

1. Instale o Agente de VM quando criar uma máquina virtual. Se a máquina virtual já existir, verifique se o Agente de VM já está instalado.
    - Se você usar o novo portal de gerenciamento do Azure para criar uma máquina virtual, selecione **Configuração Opcional**, depois selecione **Diagnóstico** e defina o **Status** como **Ativo**.

    Após a conclusão, a VM terá automaticamente a extensão de diagnóstico do Azure instalada e em execução, que será responsável por coletar os dados de diagnóstico.

2. Habilite o monitoramento e configure o log de eventos em uma VM existente. Você pode habilitar o diagnóstico no nível da VM. Para habilitar o diagnóstico e, em seguida, configurar o log de eventos, execute as seguintes etapas:
    1. Selecione a VM.
    2. Clique em **Monitoramento**.
    3. Clique em **Diagnóstico**.
    4. Defina o **Status** como **ATIVO**.
    5. Selecione cada log de diagnóstico que deseja coletar.
    7. Clique em **OK**.

Usando o PowerShell do Azure, você pode especificar mais precisamente os eventos gravados no armazenamento do Azure. Veja [Coletar dados usando diagnósticos do Azure gravados no armazenamento de tabelas ou então Logs do IIS gravados no blob](log-analytics-azure-storage-json.md).


## <a name="enable-azure-diagnostics-in-a-web-role-for-iis-log-and-event-collection"></a>Habilitar diagnóstico do Azure em uma função web para coleta de eventos e log do IIS

Consulte [Como habilitar o diagnóstico em um Serviço de Nuvem](../cloud-services/cloud-services-dotnet-diagnostics.md). Você usará as informações básicas contidas na página e personalizará as etapas descritas aqui para uso com o Log Analytics.

Com o diagnóstico do Azure habilitado:

- Logs do IIS são armazenados por padrão, e os dados de log são transferidos ao intervalo de transferência de scheduledTransferPeriod.
- Logs de eventos do Windows não são transferidos por padrão.

### <a name="to-enable-diagnostics"></a>Para habilitar o diagnóstico

Para habilitar Logs de eventos do Windows ou alterar o scheduledTransferPeriod, configure o diagnóstico do Azure usando o arquivo de configuração XML (wadcfg), conforme mostra a [Etapa 4: Criar seu arquivo de configuração do Diagnostics e instalar a extensão](../cloud-services/cloud-services-dotnet-diagnostics.md)

O arquivo de configuração de exemplo a seguir coleta os Logs do IIS e todos os Eventos dos logs do Aplicativo e do Sistema:

```
    <?xml version="1.0" encoding="utf-8" ?>
    <DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"
          configurationChangePollInterval="PT1M"
          overallQuotaInMB="4096">

      <Directories bufferQuotaInMB="0"
         scheduledTransferPeriod="PT10M">  
        <!-- IISLogs are only relevant to Web roles -->
        <IISLogs container="wad-iis" directoryQuotaInMB="0" />
      </Directories>

      <WindowsEventLog bufferQuotaInMB="0"
         scheduledTransferLogLevelFilter="Verbose"
         scheduledTransferPeriod="PT10M">
        <DataSource name="Application!*" />
        <DataSource name="System!*" />
      </WindowsEventLog>

    </DiagnosticMonitorConfiguration>
```

Certifique-se de que ConfigurationSettings especifique uma conta de armazenamento assim como no exemplo a seguir:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

Os valores de **AccountName** e **AccountKey** são encontrados no Portal de Gerenciamento do Azure, no painel da conta de armazenamento, em Gerenciar Chaves de Acesso. O protocolo para a cadeia de conexão deve ser **https**.

Depois que a configuração de diagnóstico atualizada for aplicada ao serviço de nuvem e estiver gravando o diagnóstico no Armazenamento do Azure, você estará pronto para configurar o Log Analytics.


## <a name="use-the-azure-portal-to-collect-logs-from-azure-storage"></a>Usar o Portal do Azure para coletar logs do Armazenamento do Azure

Você pode usar o Portal do Azure para configurar o Log Analytics para coletar os logs dos seguintes serviços do Azure:

+ Clusters do Service Fabric
+ Máquinas Virtuais
+ Funções Web/de Trabalho

No Portal do Azure, navegue até o espaço de trabalho do Log Analytics e execute as seguintes tarefas:

1. Clique em *Logs das contas de armazenamento*
2. Clique na tarefa *Adicionar*
3. Selecione a conta de armazenamento que contém os logs de diagnóstico
  - Ela pode ser uma conta de armazenamento clássica ou uma conta de armazenamento do Azure Resource Manager
4. Selecione o tipo de dados que você deseja coletar logs para
  - Esse será um entre Logs do IIS, Eventos, Syslog (Linux), Logs do ETW e Eventos do Service Fabric
5. O valor de origem será preenchido automaticamente com base no tipo de dados e não pode ser alterado
6. Clique em OK para salvar a configuração

Repita as etapas 2 a 6 para contas de armazenamento adicionais e os tipos de dados que você deseja que o Log Analytics colete.

Você poderá ver os dados da conta de armazenamento no Log Analytics em cerca de 30 minutos. Você só verá os dados gravados no armazenamento após a configuração ser aplicada. O Log Analytics não lê os dados pré-existentes da conta de armazenamento.

>[AZURE.NOTE] O portal não valida se a origem existe na conta de armazenamento ou se novos dados estão sendo gravados.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection-using-powershell"></a>Habilitar o diagnóstico do Azure em uma máquina virtual para coleta de log de eventos e log do IIS usando o PowerShell

Usando o PowerShell do Azure, você pode especificar mais precisamente os eventos gravados no armazenamento do Azure.
Consulte [Habilitando o diagnóstico em Máquinas Virtuais do Azure](../virtual-machines-dotnet-diagnostics.md) para obter mais detalhes.

Você pode habilitar e atualizar o agente usando o seguinte script do PowerShell.
Você também pode usar esse script com uma configuração de log personalizada.
Você precisará modificar o script para definir a conta de armazenamento, o nome do serviço e o nome da máquina virtual.
O script usando os cmdlets para máquinas virtuais clássicas.

Revise o exemplo de script a seguir, copie-o, modifique-o conforme necessário, salve o exemplo como um arquivo de script do PowerShell e execute o script.

```
    #Connect to Azure
    Add-AzureAccount

    # settings to change:
    $wad_storage_account_name = "myStorageAccount"
    $service_name = "myService"
    $vm_name = "myVM"

    #Construct Azure Diagnostics public config and convert to config format

    # Collect just system error events:
    $wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"

    $wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
    $wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)

    #Construct Azure diagnostics private config

    $wad_storage_account_key = (Get-AzureStorageKey $wad_storage_account_name).Primary
    $wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)

    #Enable Diagnostics Extension for Virtual Machine

    $wad_extension_name = "IaaSDiagnostics"
    $wad_publisher = "Microsoft.Azure.Diagnostics"
    $wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension

    (Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM
```

## <a name="next-steps"></a>Próximas etapas

- [Use arquivos JSON no Armazenamento de Blobs](log-analytics-azure-storage-json.md) para ler os logs de serviços do Azure que gravam diagnósticos no armazenamento de blobs no formato JSON.
- [Habilitar Soluções](log-analytics-add-solutions.md) para fornecer informações sobre os dados.
- [Usar consultas de pesquisa](log-analytics-log-searches.md) para analisar os dados.



<!--HONumber=Oct16_HO2-->


