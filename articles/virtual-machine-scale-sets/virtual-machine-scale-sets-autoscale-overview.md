<properties
	pageTitle="Dimensionamento automático e conjuntos de dimensionamento da máquina virtual | Microsoft Azure"
	description="Saiba como usar os recursos de diagnóstico e dimensionamento automático para dimensionar automaticamente as máquinas virtuais em um conjunto de dimensionamento."
    services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/10/2016"
	ms.author="davidmu"/>

# Dimensionamento automático e conjuntos de dimensionamento da máquina virtual

Dimensionamento automático de máquinas virtuais em um conjunto de escala é a criação ou exclusão de máquinas no conjunto conforme necessário por um aplicativo para atender às necessidades de desempenho e satisfazer SLAs (Contratos de Nível de Serviço). À medida que o volume de trabalho cresce, um aplicativo pode exigir recursos adicionais para poder realizar suas tarefas no tempo adequado.

O dimensionamento automático é um processo automatizado que ajuda a facilitar o gerenciamento da sobrecarga. Reduzindo a sobrecarga, não é necessário realizar o monitoramento contínuo do desempenho do sistema nem tomar decisões sobre adicionar ou remover recursos. O dimensionamento é um processo elástico; mais recursos podem ser provisionados à medida que a carga no sistema aumenta, porém conforme a demanda diminui os recursos podem ser desalocados para minimizar os custos, mantendo ainda o desempenho adequado e atendendo os SLAs.

Configure o dimensionamento automático em um conjunto de escala usando um modelo do Azure Resource Manager, o Azure PowerShell ou a CLI do Azure.

## Configure o dimensionamento usando modelos do Resource Manager

Em vez de implantar e gerenciar cada recurso do seu aplicativo separadamente, use um modelo que implanta e provisiona todos os recursos em uma única operação coordenada. No modelo, os recursos do aplicativo são definidos e os parâmetros de implantação são especificados para ambientes diferentes. O modelo consiste em JSON e expressões que podem ser usados na construção de valores para sua implantação. Para saber mais, confira a [Criação de modelos do Azure Resource Manager](../resource-group-authoring-templates.md).

No modelo, você deve especificar o elemento de capacidade:

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 3
    },

A capacidade identifica o número de máquinas virtuais no conjunto. Você pode alterar manualmente a capacidade implantando um modelo com um valor diferente. Se você implantar um modelo para alterar apenas a capacidade, poderá incluir apenas o elemento SKU com a capacidade atualizada.

Altere automaticamente a capacidade do seu conjunto de escala usando uma combinação do recurso autoscaleSettings fornecido pelo Azure Resource Manager e a extensão de Diagnóstico do Azure instalados nas máquinas no conjunto de escala.

### Configurar a extensão do Diagnóstico do Azure

O dimensionamento automático só poderá ser feito se a coleta de métricas for bem-sucedida em cada máquina virtual do conjunto de escala. A Extensão de Diagnóstico do Azure fornece os recursos de monitoramento e diagnóstico para atender às necessidades de coleta de métricas do recurso de dimensionamento automático. Você pode instalar a extensão como parte do modelo do Resource Manager.

Este exemplo mostra as variáveis que são usadas no modelo para configurar a extensão de diagnóstico:

	"diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'saa')]",
	"accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/', 'Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
	"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB="4096" xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/> <WindowsEventLog scheduledTransferPeriod="PT1M" > <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]" /> <DataSource name="Security!*[System[(Level = 1 or Level = 2)]]" /> <DataSource name="System!*[System[(Level = 1 or Level = 2)]]" /></WindowsEventLog>",
	"wadperfcounter": "<PerformanceCounters scheduledTransferPeriod="PT1M"><PerformanceCounterConfiguration counterSpecifier="\\Processor(_Total)\\Thread Count" sampleRate="PT15S" unit="Percent"><annotation displayName="Thread Count" locale="pt-BR"/></PerformanceCounterConfiguration></PerformanceCounters>",
	"wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId="')]",
	"wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
	"wadcfgxend": "[concat('"><MetricAggregation scheduledTransferPeriod="PT1H"/><MetricAggregation scheduledTransferPeriod="PT1M"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"

São fornecidos parâmetros quando o modelo é implantado para valores como o nome da conta de armazenamento, na qual os dados são armazenados e o nome do conjunto de escala do qual os dados são coletados. Ainda neste exemplo do Windows Server, apenas o contador de desempenho Contagem de Threads é coletado, mas todos os contadores de desempenho disponíveis no Windows ou Linux podem ser usados para coletar informações de diagnóstico e podem ser incluídos na configuração da extensão.

Este exemplo mostra a definição da extensão do modelo:

    "extensionProfile": {
      "extensions": [
        {
          "name": "Microsoft.Insights.VMDiagnosticsSettings",
          "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.5",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "xmlCfg": "[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
              "storageAccount": "[variables('diagnosticsStorageAccountName')]"
            },
            "protectedSettings": {
              "storageAccountName": "[variables('diagnosticsStorageAccountName')]",
              "storageAccountKey": "[listkeys(variables('accountid'), variables('apiVersion')).key1]",
              "storageAccountEndPoint": "https://core.windows.net"
            }
          }
        }
      ]
    }

Quando a extensão de diagnóstico é executada, os dados são coletados em uma tabela que está localizada na conta de armazenamento que você especificar. Você encontrará os dados coletados na tabela WADPerformanceCounters. Por exemplo, essa é a contagem de threads coletada quando as máquinas virtuais foram criadas primeiro em um conjunto de escala:

![](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountBefore2.png)

### Configurar o recurso autoScaleSettings

O recurso autoscaleSettings usa as informações que a extensão de diagnóstico coletou para tomar decisões sobre como aumentar ou diminuir o número de máquinas virtuais no conjunto de escala.

Este exemplo mostra a configuração do recurso no modelo:

    {
      "type": "Microsoft.Insights/autoscaleSettings",
      "apiVersion": "2015-04-01",
      "name": "[concat(parameters('resourcePrefix'),'as1')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
      ],
      "properties": {
        "enabled": true,
        "name": "[concat(parameters('resourcePrefix'),'as1')]",
        "profiles": [
          {
            "name": "Profile1",
            "capacity": {
              "minimum": "1",
              "maximum": "10",
              "default": "1"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "\\Process(_Total)\\Thread Count",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 650
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "\\Process(_Total)\\Thread Count",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 550
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              }
            ]
          }
        ],
        "targetResourceUri": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
      }
    }

No exemplo acima, são criadas duas regras para definir as ações de dimensionamento automático. A primeira regra define a ação de escala horizontal e a segunda regra define a de redução horizontal. Esses valores são fornecidos nas regras:

- **metricName** – é igual ao contador de desempenho que você definiu na variável wadperfcounter para a extensão de diagnóstico. No exemplo acima, usamos o contador Contagem de Threads.
- **metricResourceUri** - Este é o identificador de recurso do conjunto de dimensionamento de máquina virtual. Esse identificador contém o nome do grupo de recursos, o nome do provedor de recursos e o nome do conjunto de escala a ser dimensionado.
- **timeGrain** – Esta é a granularidade das métricas que são coletadas. No exemplo acima, os dados são coletados em um intervalo de um minuto. Esse valor é usado junto com timeWindow.
- **statistic** – Isso determina como as métricas são combinadas para acomodar a ação de dimensionamento automático. Os valores possíveis são: Média, Mín, Máx.
- **timeWindow** – é o intervalo de tempo em que os dados da instância são coletados. Deve estar entre 5 minutos e 12 horas.
- **timeAggregation** – Determina como os dados coletados devem ser combinados ao longo do tempo. O valor padrão é Average. Os valores possíveis são: Média, Mínimo, Máximo, Último, Total, Contagem.
- **operator** – Este é o operador usado para comparar os dados de métrica e o limite. Os valores possíveis são: Equals, NotEquals, GreaterThan, GreaterThanOrEqual, LessThan, LessThanOrEqual.
- **threshold** – Este é o valor que dispara a ação de dimensionamento. Você deve fornecer uma diferença suficiente entre os limites definidos para as ações de escala e redução horizontal. Se você definir valores iguais, por exemplo, definindo os dois para 600 threads no exemplo acima, o sistema preverá o aumento e redução constantes do tamanho do conjunto e não implementará uma ação de dimensionamento.
- **direction** – Determina a ação que é executada quando o valor de limite for atingido. Os valores possíveis são Aumentar ou Diminuir.
- **type** – Este é o tipo de ação que deve ocorrer e deve ser definido como ChangeCount.
- **value** – é o número de máquinas virtuais que são adicionadas ou removidas do conjunto de dimensionamento. Este valor deve ser 1 ou maior.
- **cooldown** – Esta é a quantidade de tempo de espera desde a última ação de dimensionamento antes que ocorra a próxima ação. Ele deve estar entre um minuto e uma semana.

Dependendo do contador de desempenho que você está usando, alguns dos elementos na configuração de modelo são usados de forma diferente. No exemplo mostrado, o contador de desempenho usado é Contagem de Threads e o valor de limite é definido para 650 para uma ação de escala horizontal e 550 para redução horizontal. Se você usar um contador como %Processor Time, o valor do limite será definido para o percentual de utilização de CPU que determinaria uma ação de dimensionamento.

Quando uma carga é criada nas máquinas virtuais no conjunto que dispara uma ação de dimensionamento, o número de máquinas no conjunto é aumentado com base no elemento de valor no modelo. Por exemplo, em um conjunto de escala em que a capacidade é definida para 3 e o valor da ação de escala é definido para 1:

![](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerBefore.png)

Quando a carga é criada fazendo com que a contagem de threads médio fique acima do limite de 650:

![](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountAfter.png)

Uma ação de escala horizontal é disparada, fazendo com que a capacidade do conjunto seja aumentada em um:

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 4
    },

E uma máquina virtual é adicionada ao conjunto de escala:

![](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerAfter.png)

Após um período de resfriamento de cinco minutos, se o número médio de threads nas máquinas permanecer acima de 600, outra máquina será adicionada ao conjunto. Se a contagem de threads média permanecer abaixo 550, a capacidade do conjunto de escala será reduzida em um e uma máquina será removida do conjunto.

## Configurar o dimensionamento usando o Azure PowerShell

O Azure PowerShell pode ser usado para configurar o dimensionamento automático para um conjunto de dimensionamento. Aqui está um exemplo usando o PowerShell para criar as regras que foram descritas anteriormente neste artigo:

    $rule1 = New-AutoscaleRule -MetricName "\Processor(_Total)\Thread Count" -MetricResourceId "/subscriptions/{subscription-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1" -Operator GreaterThan -MetricStatistic Average -Threshold 650 -TimeGrain 00:01:00 -ScaleActionCooldown 00:05:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue "1"  
 
    $rule2 = New-AutoscaleRule -MetricName "\Processor(_Total)\% Processor Time" -MetricResourceId "/subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/rainvmss/providers/Microsoft.Compute/virtualMachineScaleSets/rainvmss1" -Operator LessThan -MetricStatistic Average -Threshold 10 -TimeGrain 00:01:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionScaleType ChangeCount -ScaleActionValue "2" 
 
    $profile1 = New-AutoscaleProfile -DefaultCapacity "2" -MaximumCapacity "10" -MinimumCapacity "2" -Rules $rule1, $rule2 -Name "ScalePuppy" 
 
    Add-AutoscaleSetting -Location "East US" -Name 'MyScaleVMSSSetting' -ResourceGroup rainvmss -TargetResourceId "/subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/rainvmss/providers/Microsoft.Compute/virtualMachineScaleSets/rainvmss1" -AutoscaleProfiles $profile1 

## Configurar o dimensionamento usando a CLI do Azure

(Informações não disponíveis ainda)

## Investigar ações de dimensionamento

- [Portal do Azure]() - Atualmente, você pode obter uma quantidade limitada de informações usando o portal.
- [Azure Resource Manager]() - é a melhor ferramenta para explorar o estado atual de seu conjunto de dimensionamento. Siga este caminho e você deverá ver a exibição da instância do conjunto de dimensionamento criado: assinaturas > {sua assinatura} > resourceGroups > {seu grupo de recursos} > provedores > Microsoft.Compute > virtualMachineScaleSets > {seu conjunto de dimensionamento} > virtualMachines
- Azure PowerShell- Use este comando para obter algumas informações:

        Get-AzureRmResource -name vmsstest1 -ResourceGroupName vmsstestrg1 -ResourceType Microsoft.Compute/virtualMachineScaleSets -ApiVersion 2015-06-15
        Get-Autoscalesetting -ResourceGroup rainvmss -DetailedOutput
        
- Conecte-se à máquina virtual de jumpbox exatamente como faria com qualquer outra máquina e, em seguida, você pode acessar remotamente as máquinas virtuais no conjunto de dimensionamento para monitorar os processos individuais.

## Próximas etapas

- Veja [Dimensionar automaticamente as máquinas em um Conjunto de Dimensionamento da Máquina Virtual](virtual-machine-scale-sets-windows-autoscale.md) para ter um exemplo de como criar um conjunto de dimensionamento com o dimensionamento automático configurado.
- Encontre exemplos de recursos de monitoramento do Azure Insights nos [exemplos de início rápido do PowerShell do Azure Insights](../azure-portal/insights-powershell-samples.md)
- Saiba mais sobre os recursos de notificação em [Usar ações de dimensionamento automático para enviar notificações de alerta de email e webhook no Azure Insights](../azure-portal/insights-autoscale-to-webhook-email.md) e [Usar logs de auditoria para enviar notificações de alerta de email e webhook no Azure Insights](../azure-portal/insights-auditlog-to-webhook-email.md)
- Saiba mais sobre [Cenários de dimensionamento automático avançado](./virtual-machine-scale-sets-advanced-autoscale.md).

<!---HONumber=AcomDC_0907_2016-->