<properties
	pageTitle="Azure Insights: exemplos de início rápido do Azure Insights PowerShell. | Microsoft Azure"
	description="Os comandos do PowerShell do início rápido do Azure Insights podem ajudá-lo a acessar rapidamente os recursos de monitoramento do Azure Insights."
	authors="kamathashwin"
	manager=""
	editor=""
	services="azure-portal"
	documentationCenter="na"/>

<tags
	ms.service="azure-portal"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2016"
	ms.author="ashwink"/>

# Exemplos de início rápido do PowerShell do Azure Insights

Este artigo mostra exemplos de comandos do PowerShell para ajudá-lo a acessar rapidamente os recursos de monitoramento do Azure Insights. O Azure Insights permite que você dimensione automaticamente Serviços de Nuvem, Máquinas Virtuais e Aplicativos Web e envie notificações de alerta ou chame URLs da Web com base em valores de dados de telemetria configurados.

## Configurar o PowerShell
Se ainda não tiver feito isso, configure o PowerShell para ser executado no seu computador. Para saber mais, consulte [Como instalar e configurar o PowerShell](../powershell-install-configure.md).

## Exemplos neste artigo

Os exemplos neste artigo ilustram como você pode usar os cmdlets do Azure Insights. Você também pode ver a lista completa de cmdlets (de monitoramento) do Azure Insights em [Cmdlets do Azure Insights](https://msdn.microsoft.com/library/mt282452.aspx).


## Conectar-se e usar assinaturas

Primeiro, faça logon na sua assinatura do Azure.

```
Login-AzureRmAccount
```

Isso exige que você se conecte. Quando fizer isso, você verá sua Conta, sua TenantId e ID da Assinatura padrão. Todos os cmdlets do Azure funcionam no contexto de sua assinatura padrão. Para ver a lista de assinaturas a que você tem acesso, use o seguinte comando.

```
Get-AzureRmSubscription
```

Para alterar o contexto de trabalho para uma assinatura diferente, use o comando a seguir.

```
Set-AzureRmContext -SubscriptionId <subscriptionid>
```


## Recuperar logs de auditoria de uma assinatura
Use o cmdlet `Get-AzureRmLog`. A seguir, temos alguns exemplos comuns.

Obter entradas de log dessa data/hora até o momento presente:

```
Get-AzureRmLog -StartTime 2016-03-01T10:30
```

Obter entradas de log em um intervalo de data/hora:

```
Get-AzureRmLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obter entradas de log de um grupo de recursos específico:

```
Get-AzureRmLog -ResourceGroup 'myrg1'
```

Obter entradas de log de um provedor de recursos específico um intervalo de data/hora:

```
Get-AzureRmLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obter todas as entradas de log com um autor de chamadas específico:

```
Get-AzureRmLog -Caller 'myname@company.com'
```

O comando a seguir recupera os últimos 1000 eventos do log de auditoria:

```
Get-AzureRmLog -MaxEvents 1000
```

`Get-AzureRmLog` dá suporte a muitos outros parâmetros. Para saber mais, consulte a referência `Get-AzureRmLog`.

>[AZURE.NOTE] `Get-AzureRmLog` fornece apenas 15 dias de histórico. Usar o parâmetro **-MaxEvents** permite consultar os últimos N eventos, além de 15 dias. Para eventos de acesso que ocorreram há mais 15 dias, use a API REST ou o SDK (exemplo em C# usando o SDK). Se você não incluir **StartTime**, o valor padrão será **EndTime** menos uma hora. Se você não incluir **EndTime**, o valor padrão será a hora atual. Todas as horas estão no padrão UTC.

## Recuperar o histórico de alertas
Para ver todos os eventos de alerta, você pode consultar os logs do ARM (Azure Resource Manager) usando os exemplos a seguir.

```
Get-AzureRmLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Para ver o histórico de uma regra de alerta específica, você pode usar o cmdlet `Get-AzureRmAlertHistory`, passando a ID de recurso da regra de alerta.

```
Get-AzureRmAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

O cmdlet `Get-AzureRmAlertHistory` dá suporte a vários parâmetros. Para saber mais, consulte [Get-AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx).


## Recuperar informações sobre regras de alerta
Todos os comandos a seguir atuam em um grupo de recursos denominado "montest".

Exibir todas as propriedades da regra de alerta:

```
Get-AzureRmAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Recuperar todos os alertas de um grupo de recursos:

```
Get-AzureRmAlertRule -ResourceGroup montest
```

Recuperar todas as regras de alerta definidas para um recurso de destino. Por exemplo, todas as regras de alerta definidas em uma VM.

```
Get-AzureRmAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzureRmAlertRule` dá suporte a outros parâmetros. Consulte [Get-AlertRule](https://msdn.microsoft.com/library/mt282459.aspx) para obter mais informações.

## Criar regras de alerta
Você pode usar o cmdlet `Add-AlertRule` para criar, atualizar ou desabilitar uma regra de alerta.

Você pode criar propriedades de email e webhook usando `New-AzureRmAlertRuleEmail` e `New-AzureRmAlertRuleWebhook`, respectivamente. No cmdlet da regra de alerta, atribua como ações para a propriedade **Actions** da regra de alerta.

A próxima seção contém um exemplo que mostra como criar uma regra de alerta com vários parâmetros.

### Regra de alerta com uma métrica
A tabela a seguir descreve os parâmetros e valores usados para criar um alerta usando uma métrica.


|parâmetro|value|
|---|---|
|Nome|	simpletestdiskwrite|
|Local desta regra de alerta|	Leste dos EUA|
|ResourceGroup|	montest|
|TargetResourceId|	/subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig|
|MetricName do alerta que é criado|	\\PhysicalDisk(\_Total)\\Disk Writes/sec. Consulte o cmdlet `Get-MetricDefinitions` abaixo para ver como recuperar os nomes exatos das métricas|
|operator| GreaterThan|
|Threshold value (count/sec para esta métrica)| 1|
|WindowSize (formato hh:mm:ss)| 00:05:00|
|aggregator (estatística da métrica, que usa a contagem média neste caso)| Average|
|custom emails (matriz da cadeia de caracteres)|'foo@example.com','bar@example.com'|
|enviar email para proprietários, colaboradores e leitores| -SendToServiceOwners|

Ação Criar um email

```
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

Ação Criar um webhook

```
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Criar a regra de alerta na métrica de % de CPU em uma VM clássica

```
Add-AzureRmMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Recuperar a regra de alerta

```
Get-AzureRmAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

O cmdlet de alerta Add também atualizará a regra se já existir uma regra de alerta para as propriedades determinadas. Para desabilitar uma regra de alerta, inclua o parâmetro **- DisableRule**.

### Alerta de evento de log de auditoria

>[AZURE.NOTE] Este recurso ainda está em Preview.

Nesse cenário, você vai enviar um email quando um site for iniciado com êxito em minha assinatura no grupo de recursos *abhingrgtest123*.

Configurar uma regra de email

```
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

Configurar uma regra de webhook

```
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Criar a regra no evento

```
Add-AzureRmLogAlertRule -Name superalert1 -Location "East US" -ResourceGroup myrg1 -OperationName microsoft.web/sites/start/action -Status Succeeded -TargetResourceGroup abhingrgtest123 -Actions $actionEmail, $actionWebhook
```

Recuperar a regra de alerta

```
Get-AzureRmAlertRule -Name superalert1 -ResourceGroup myrg1 -DetailedOutput
```

O cmdlet `Add-AlertRule` permite vários outros parâmetros. Para obter mais informações, consulte [Add-AlertRule](https://msdn.microsoft.com/library/mt282468.aspx).

## Obter uma lista das métricas disponíveis para alertas
Você pode usar o cmdlet `Get-AzureRmMetricDefinition` para exibir a lista de todas as métricas para um recurso específico.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id>
```

O exemplo a seguir gera uma tabela com o nome de métrica e sua unidade.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Uma lista completa das opções disponíveis para `Get-AzureRmMetricDefinition` está disponível em [Get-MetricDefinitions](https://msdn.microsoft.com/library/mt282458.aspx).


## Criar e gerenciar configurações de Autoescala
Um recurso, como um aplicativo Web, VM, serviço de nuvem ou conjunto de escalas de VM pode ter apenas uma configuração de autoescala definida. No entanto, cada configuração de autoescala pode ter vários perfis. Por exemplo, um para um perfil de escala baseada em desempenho e outro para um perfil baseado em agendamento. Cada perfil pode ter várias regras configuradas nele. Para obter mais informações sobre Dimensionamento Automático, confira [Como fazer o dimensionamento automático de um aplicativo](../cloud-services/cloud-services-how-to-scale.md).

Estas são as etapas que usaremos:

1. Criar regra(s).
2. Crie perfis mapeando as regras que você criou anteriormente para os perfis.
3. Opcional: criar notificações de autoescala configurando propriedades de webhook e email.
4. Crie uma configuração de autoescala com um nome do recurso de destino mapeando os perfis e notificações que você criou nas etapas anteriores.

Os exemplos a seguir mostram como você pode criar uma configuração de Autoescala para um conjunto de escalas de VM definido para um sistema operacional Windows usando a métrica de utilização da CPU.

Primeiro, crie uma regra para expansão, com um aumento de contagem de instâncias.

```
$rule1 = New-AzureRmAutoscaleRule -MetricName "\Processor(_Total)\% Processor Time" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 0.01 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue 1
```		

Em seguida, crie uma regra para expansão, com uma redução da contagem de instâncias.

```
$rule2 = New-AzureRmAutoscaleRule -MetricName "\Processor(_Total)\% Processor Time" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 2 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionScaleType ChangeCount -ScaleActionValue 1
```

Depois, crie um perfil para as regras.

```
$profile1 = New-AzureRmAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Crie uma propriedade de webhook.

```
$webhook_scale = New-AzureRmAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Crie a propriedade de notificação para a configuração de autoescala, incluindo o email e o webhook que você criou anteriormente.

```
$notification1= New-AzureRmAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Por fim, crie a configuração de autoescala para adicionar o perfil que você criou acima.

```
Add-AzureRmAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Para obter mais informações sobre como gerenciar configurações de Dimensionamento Automático, confira [Get-AutoscaleSetting](https://msdn.microsoft.com/library/mt282461.aspx).

## Histórico de Autoescala
O exemplo a seguir mostra como você pode exibir eventos recentes de autoescala e alertas. Use a pesquisa de log de auditoria para exibir o histórico de Autoescala.

```
Get-AzureRmLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

Você pode usar o cmdlet `Get-AzureRmAutoScaleHistory` para recuperar o histórico de Dimensionamento Automático.

```
Get-AzureRmAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

Para obter mais informações, confira [Get-AutoscaleHistory](https://msdn.microsoft.com/library/mt282464.aspx).

### Exibir os detalhes de uma configuração de autoescala
Você pode usar o cmdlet `Get-Autoscalesetting` para recuperar mais informações sobre a configuração de dimensionamento automático.

O exemplo a seguir mostra detalhes de todas as configurações de autoescala no grupo de recursos "myrg1".

```
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

O exemplo a seguir mostra detalhes sobre todas as configurações de autoescala do grupo de recursos "myrg1" e, especificamente, a configuração de autoescala chamada "MyScaleVMSSSetting".

```
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### Remover uma configuração de autoescala
Você pode usar o cmdlet `Remove-Autoscalesetting` para excluir uma configuração de dimensionamento automático.

```
Remove-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## Gerenciar perfis de log para logs de auditoria

Você pode criar um *perfil de log* e exportar dados de logs de auditoria para uma conta de armazenamento e pode configurar retenção de dados para ele. Você também pode transmitir os dados para seu Hub de eventos. Observe que no momento esse recurso está em Preview e você só pode criar um perfil de log por assinatura. Você pode usar os cmdlets a seguir com sua assinatura atual para criar e gerenciar perfis de log. Você também pode escolher uma assinatura específica. Embora o PowerShell selecione a assinatura atual por padrão, você pode alterar isso usando `Set-AzureRmContext`. Você pode configurar logs de auditoria para encaminhar dados para qualquer conta de armazenamento ou Hub de eventos dentro dessa assinatura. Os dados são gravados como arquivos de blob no formato JSON.

### Obter um perfil de log
Para buscar os perfis de log existentes, use o cmdlet `Get-AzureRmLogProfile`.

### Adicionar um perfil de log sem retenção de dados

```
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### Remover um perfil de log

```
Remove-AzureRmLogProfile -name my_log_profile_s1
```

### Adicionar um perfil de log com retenção de dados

Você pode especificar a propriedade **-RetentionInDays** com o número de dias, como um inteiro positivo, em que os dados são mantidos.

```
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### Adicionar perfil de log com retenção e Hub de eventos
Além de encaminhar seus dados para a conta de armazenamento, você também pode transmiti-los para um Hub de eventos. Observe que nessa versão de teste a configuração da conta de armazenamento é obrigatória, mas a configuração do Hub de eventos é opcional.

```
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## Configurar logs de diagnóstico
Muitos serviços do Azure fornecem logs e telemetria adicionais, incluindo Grupos de Segurança de Rede do Azure, Balanceadores de Carga de Software, o Cofre de Chaves, os Serviços de Pesquisa do Azure e os Aplicativos Lógicos, e eles podem ser configurados para salvar os dados em sua conta de armazenamento do Azure. Essa operação só pode ser executada no nível dos recursos e a conta de armazenamento deve estar na mesma região que o recurso de destino em que a configuração de diagnóstico está definida.

### Obter configuração de diagnóstico

```
Get-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Desabilitar configuração de diagnóstico

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Habilitar configuração de diagnóstico sem retenção

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Habilitar configuração de diagnóstico com retenção

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Habilitar configuração de diagnóstico com retenção para uma categoria de log específica

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

<!---HONumber=AcomDC_0406_2016-->