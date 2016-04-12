<properties
	pageTitle="Azure Insights: Exemplos de início rápido da CLI do Azure Insights. | Microsoft Azure"
	description="O exemplo de comando da CLI pode ajudar você a acessar os recursos de monitoramento do Azure Insights. O Azure Insights é um serviço do Microsoft Azure que permite dimensionar automaticamente Serviços de Nuvem, Máquinas Virtuais e Aplicativos Web, bem como enviar notificações de alerta ou chamar URLs da Web baseadas em valores dos dados de telemetria configurados."
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

# Exemplos de início rápido da CLI entre plataformas do Azure Insights

Este artigo mostra um exemplo de CLI (interface de linha de comando) que ajudará você a acessar os recursos de monitoramento do Azure Insights. O Azure Insights permite que você dimensione automaticamente os Serviços de Nuvem, as Máquinas Virtuais e os Aplicativos Web e envie notificações de alerta ou chame URLs da Web com base em valores de dados de telemetria configurados.

## Pré-requisitos

Se você ainda não tiver instalado a CLI do Azure, confira [Instalar a CLI do Azure](../xplat-cli-install.md). Se não estiver familiarizado com a CLI do Azure, você poderá ler mais sobre ela em [Usar a CLI do Azure para Mac, Linux e Windows com o Azure Resource Manager](../xplat-cli-azure-resource-manager.md).


No Windows, instale o npm do [site do Node.js](https://nodejs.org/). Após concluir a instalação, usando o CMD.exe com os privilégios de Executar como Administrador, execute o seguinte na pasta em que o npm está instalado:

```
npm install azure-cli --global
```

Em seguida, navegue até qualquer pasta/local desejado e digite na linha de comando:

```
azure help
```

## Fazer logon no Azure

A primeira etapa é fazer logon na conta do Azure.

```
azure login
```

Isso exige que você se conecte. Posteriormente, você verá sua Conta, sua TenantId e sua ID da Assinatura padrão. Todos os comandos funcionarão no contexto de sua assinatura padrão.

Para listar os detalhes da sua assinatura atual, use o comando a seguir.

```
azure account show
```

Para alterar o contexto de trabalho para uma assinatura diferente, use o comando a seguir.

```
azure account set "subscription ID or subscription name"
```

Para usar comandos do Azure Resource Manager e do Azure Insights, você precisará estar no modo ARM.

```
azure config mode arm
```

Para exibir uma lista de todos os comandos permitidos do Azure Insights, execute o seguinte:

```
azure insights
```

## Exibir logs de auditoria de uma assinatura

Para exibir uma lista de logs de auditoria, execute o seguinte:

```
azure insights logs list [options]
```

Tente o seguinte para exibir todas as opções disponíveis:

```
azure insights logs list -help
```

Veja um exemplo para listar logs por resourceGroup

```
azure insights logs list --resourceGroup "myrg1"
```

Exemplo para listar logs por chamador

```
azure insights logs list --caller "myname@company.com"
```

Exemplo para listar logs por chamador em um tipo de recurso, entre datas de início e de término

```
azure insights logs list --resourceProvider "Microsoft.Web" --caller "myname@company.com" --startTime 2016-03-08T00:00:00Z --endTime 2016-03-16T00:00:00Z
```

## Trabalhar com alertas
Você pode usar as informações na seção para trabalhar com alertas.

### Obter regras de alerta em um grupo de recursos

```
node bin\azure insights alerts rule list abhingrgtest123
node bin\azure insights alerts rule list abhingrgtest123 --ruleName andy0323
```

### Criar uma regra de alerta de métrica

```
node bin\azure insights alerts actions email create --customEmails foo@microsoft.com
node bin\azure insights alerts actions webhook create https://someuri.com
node bin\azure insights alerts rule metric set andy0323 eastus abhingrgtest123 PT5M GreaterThan 2 /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Web-EastUS/providers/Microsoft.Web/serverfarms/Default1 BytesReceived Total
```

### Criar uma regra de alerta de log

```
insights alerts rule log set ruleName eastus resourceGroupName someOperationName
```

### Criar uma regra de alerta de teste na Web

```
node bin\azure insights alerts rule webtest set leowebtestr1-webtestr1 eastus Default-Web-WestUS PT5M 1 GSMT_AvRaw /subscriptions/b67f7fec-69fc-4974-9099-a26bd6ffeda3/resourcegroups/Default-Web-WestUS/providers/microsoft.insights/webtests/leowebtestr1-webtestr1
```

### Excluir uma regra de alerta

```
node bin\azure insights alerts rule delete abhingrgtest123 andy0323
```

## Perfis de log
Use as informações desta seção para trabalhar com perfis de log.

### Obter um perfil de log

```
node bin\azure insights logprofile list
node bin\azure insights logprofile get -n default
```


### Adicionar um perfil de log sem retenção

```
node bin\azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### Remover um perfil de log

```
node bin\azure insights logprofile delete --name default
```

### Adicionar um perfil de log com retenção

```
node bin\azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```

### Adicionar um perfil de log com retenção e Hub de Eventos

```
node bin\azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --serviceBusRuleId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/testshoeboxeastus/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```


## Diagnostics
Use as informações desta seção para trabalhar com configurações de diagnóstico.

### Obter uma configuração de diagnóstico

```
node bin\azure insights diagnostic get --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp
```

### Desabilitar uma configuração de diagnóstico

```
node bin\azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled false
```

### Habilitar uma configuração de diagnóstico sem retenção

```
node bin\azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled true
```


## Autoscale
Use as informações desta seção para trabalhar com configurações de dimensionamento automático. Você precisará modificar estes exemplos.

### Obter configurações de dimensionamento automático para um grupo de recursos

```
node bin\azure insights autoscale setting list montest2
```

### Obter configurações de dimensionamento automático por nome em um grupo de recursos

```
node bin\azure insights autoscale setting list montest2 -n setting2
```


### Definir as configurações de dimensionamento automático

```
node bin\azure insights autoscale setting set montest2 -n setting2 --settingSpec
```

<!-----------HONumber=AcomDC_0330_2016-->