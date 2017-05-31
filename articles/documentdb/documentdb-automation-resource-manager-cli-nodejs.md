---
title: "Automação do Azure Cosmos DB – Resource Manager – CLI 1.0 do Azure | Microsoft Docs"
description: "Use modelos do Azure Resource Manager ou a CLI para implantar uma conta de banco de dados do Azure Cosmos DB. O Azure Cosmos DB é um banco de dados baseado em nuvem distribuído globalmente."
services: cosmosdb
author: mimig1
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: eae5eec6-0e27-442c-abfc-ef6b7fd3f8d2
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: mimig
redirect_url: https://aka.ms/acdbclisamples
ROBOTS: NOINDEX, NOFOLLOW
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 80902661eb811bdd8f6e00bcd5f61a8939a4e47c
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---
# <a name="automate-azure-cosmos-db-account-creation-using-azure-cli-10-and-azure-resource-manager-templates"></a>Automatizar a criação de conta do Azure Cosmos DB usando a CLI 1.0 do Azure e modelos do Azure Resource Manager
> [!div class="op_single_selector"]
> * [Portal do Azure](documentdb-create-account.md)
> * [CLI 1.0 do Azure](documentdb-automation-resource-manager-cli-nodejs.md)
> * [CLI 2.0 do Azure](documentdb-automation-resource-manager-cli.md)
> * [PowerShell do Azure](documentdb-manage-account-with-powershell.md)

Este artigo mostra como criar uma conta da API do DocumentDB no Azure Cosmos DB usando modelos do Azure Resource Manager ou diretamente com a CLI (Interface de Linha de Comando) 1.0 do Azure. Para criar uma conta do Azure Cosmos DB usando o portal do Azure, consulte [Criar uma conta de banco de dados do Azure Cosmos DB usando o portal do Azure](documentdb-create-account.md).

Atualmente, as contas de banco de dados da API do DocumentDB e da API do MongoDB no Azure Cosmos DB são os únicos recursos do Azure Cosmos DB que podem ser criados usando os modelos do Azure Resource Manager e a CLI 1.0 do Azure.

Para criar a API do DocumentDB do Azure Cosmos DB, a API de Tabela, a API do Graph ou uma conta do MongoDB usando a CLI 2.0, consulte [Criar uma conta do Azure DocumentDB usando a CLI do Azure](documentdb-automation-resource-manager-cli.md).

## <a name="getting-ready"></a>Preparando-se
Para usar a CLI do Azure 1.0 com grupos de recursos do Azure, você precisará ter a versão correta e uma conta do Azure. Se você não tiver a CLI do Azure 1.0, [instale-a](../cli-install-nodejs.md).

### <a name="update-your-azure-cli-10-version"></a>Atualizar sua versão da CLI do Azure 1.0
No prompt de comando, digite `azure --version` para ver se você já tem a versão 0.10.4 ou posterior instalada. Poderá ser solicitado que você participe da coleta de dados da CLI do Microsoft Azure nesta etapa; você poderá selecionar S ou N para aceitar ou recusar.

    azure --version
    0.10.4 (node: 4.2.4)

Se a sua versão não for a 0.10.4 ou posterior, será necessário [instalar a CLI do Azure 1.0](../cli-install-nodejs.md) ou atualizá-la usando um dos instaladores nativos, ou por meio do **npm** digitando `npm update -g azure-cli` para atualizar ou `npm install -g azure-cli` para instalar.

### <a name="set-your-azure-account-and-subscription"></a>Definir sua conta e assinatura do Azure
Se você ainda não tiver uma assinatura do Azure, mas tiver uma assinatura do Visual Studio, poderá ativar seus [benefícios de assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Ou então, você poderá se inscrever para uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

Você precisa ter uma conta corporativa ou de estudante, ou uma identidade de conta da Microsoft, para usar os modelos de gerenciamento de recursos do Azure. Se você tiver uma dessas contas, digite o comando a seguir:

    azure login

Que produz esta saída:

    info:    Executing command login
    |info:    To sign in, use a web browser to open the page https://aka.ms/devicelogin.
    Enter the code E1A2B3C4D to authenticate.

> [!NOTE]
> Se você não tiver uma conta do Azure, verá uma mensagem de erro indicando que você precisa de um tipo diferente de conta. Para criar uma com base em sua atual conta do Azure, veja [Criando uma identidade corporativa ou de estudante no Active Directory do Azure](../virtual-machines/windows/create-aad-work-id.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

Abra [https://aka.ms/devicelogin](https://aka.ms/devicelogin) em um navegador e insira o código fornecido na saída do comando.

![Captura de tela mostrando a tela de logon do dispositivo para a CLI do Microsoft Azure 1.0](media/documentdb-automation-resource-manager-cli/azure-cli-login-code.png)

Depois de inserir o código, selecione a identidade que você deseja usar no navegador e forneça seu nome de usuário e senha, se for necessário.

![Captura de tela mostrando onde selecionar a conta de identidade da Microsoft associada à assinatura do Azure que você deseja usar](media/documentdb-automation-resource-manager-cli/identity-cli-login.png)

Você receberá a seguinte tela de confirmação quando estiver conectado e poderá fechar a janela do navegador.

![Captura de tela mostrando a confirmação de logon para a Interface de linha de comando de plataforma cruzada do Microsoft Azure](media/documentdb-automation-resource-manager-cli/login-confirmation.png)

O shell de comando também fornece a saída a seguir:

    /info:    Added subscription Visual Studio Ultimate with MSDN
    info:    Setting subscription "Visual Studio Ultimate with MSDN" as default
    +
    info:    login command OK

Além do método de logon interativo descrito aqui, há métodos adicionais de logon disponíveis na CLI do Azure 1.0. Para saber mais sobre os outros métodos e informações sobre como lidar com várias assinaturas, confira [Conectar uma assinatura do Azure da CLI (Interface de Linha de Comando) do Azure 1.0](../xplat-cli-connect.md).

### <a name="switch-to-azure-cli-10-resource-group-mode"></a>Alternar para o modo de grupo de recursos da CLI do Azure 1.0
Por padrão, a CLI do Azure 1.0 é iniciada no modo de gerenciamento de serviços (modo**asm** ). Digite o seguinte para alternar para o modo de grupo de recursos.

    azure config mode arm

Que fornece esta saída:

    info:    Executing command config mode
    info:    New mode is arm
    info:    config mode command OK

Se necessário, você poderá voltar para o conjunto de comandos padrão digitando `azure config mode asm`.

### <a name="create-or-retrieve-your-resource-group"></a>Criar ou recuperar o grupo de recursos
Para criar uma conta do Azure Cosmos DB, primeiro você precisa de um grupo de recursos. Se você já souber o nome do grupo de recursos que deseja usar, pule para a [Etapa 2](#create-documentdb-account-cli).

Para examinar uma lista de todos os grupos de recursos atuais, execute o seguinte comando e anote o nome do grupo de recursos que você deseja usar:

    azure group list

Para criar um grupo de recursos, execute o seguinte comando, especifique o nome do novo grupo de recursos a ser criado e a região na qual você quer criar o grupo de recursos:

    azure group create <resourcegroupname> <resourcegrouplocation>

* `<resourcegroupname>` pode usar apenas caracteres alfanuméricos, pontos, sublinhados, o caractere '-' e parênteses, e não pode terminar em um ponto.
* `<resourcegrouplocation>` deve ser uma das regiões na qual o Azure Cosmos DB está disponível. Confira a lista atual de regiões na [página Regiões do Azure](https://azure.microsoft.com/regions/#services).

Entrada de exemplo:

    azure group create new_res_group westus

Que produz esta saída:

    info:    Executing command group create
    + Getting resource group new_res_group
    + Creating resource group new_res_group
    info:    Created resource group new_res_group
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group
    data:    Name:                new_res_group
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

Se você encontrar erros, confira a [Solução de problemas](#troubleshooting).

## <a name="understanding-resource-manager-templates-and-resource-groups"></a>Noções básicas sobre os grupos de recursos e os modelos do Resource Manager
A maioria dos aplicativos é criada com uma combinação de diferentes tipos de recursos (como uma ou mais contas do Azure Cosmos DB, contas de armazenamento, uma rede virtual ou uma rede de distribuição de conteúdo). A API de gerenciamento de serviço do Azure padrão e o portal do Azure representava esses itens usando uma abordagem de acordo com o serviço. Essa abordagem requer que você implante e gerencie individualmente os serviços individuais (ou localize outras ferramentas fazê-lo) e não como uma única unidade lógica de implantação.

*modelos do Gerenciador de Recursos do Azure* , é possível implantar e gerenciar esses diferentes recursos como uma unidade lógica de implantação de forma declarativa. Em vez de informar imperativamente ao Azure o que implantar, em um comando após o outro, você descreve a implantação inteira em um arquivo JSON (todos os recursos e configurações e parâmetros de implantação associados) e instrui o Azure a implantar esses recursos como um grupo.

Você pode saber muito mais sobre os grupos de recursos do Azure e o que eles podem fazer por você na [Visão geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Se você estiver interessado na criação de modelos, confira [Criando modelos do Gerenciador de Recursos do Azure](../azure-resource-manager/resource-group-authoring-templates.md).

## <a id="quick-create-documentdb-account"></a>Tarefa: Criar uma conta do Azure Cosmos DB em uma única região
Use as instruções desta seção para criar uma conta do Azure Cosmos DB em uma Única Região. Isso pode ser feito usando a CLI do Azure 1.0 com ou sem os modelos do Resource Manager.

### <a id="create-single-documentdb-account-cli-arm"></a> Criar uma conta do Azure Cosmos DB em uma única região usando a CLI 1.0 do Azure sem modelos do Resource Manager
Crie uma conta do Azure Cosmos DB no grupo de recursos novo ou existente inserindo o seguinte comando no prompt de comando:

> [!TIP]
> Se você executar este comando no Azure PowerShell ou no Windows PowerShell, receberá um erro sobre um token inesperado. Em vez disso, execute este comando no Prompt de Comando do Windows.
>
>

    azure resource create -g <resourcegroupname> -n <databaseaccountname> -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l <resourcegrouplocation> -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"<ip-range-filter>\",\"locations\":["{\"locationName\":\"<databaseaccountlocation>\",\"failoverPriority\":\"<failoverPriority>\"}"]}"

* `<resourcegroupname>` pode usar apenas caracteres alfanuméricos, pontos, sublinhados, o caractere '-' e parênteses, e não pode terminar em um ponto.
* `<resourcegrouplocation>` é a região do grupo de recursos atual.
* `<ip-range-filter>` Especifica o conjunto de endereços IP ou os intervalos de endereços IP no formato CIDR a serem incluídos como a lista de permissões de IPs de cliente para uma determinada conta de banco de dados. Os intervalos/endereços IP devem ser separados por vírgula e não devem conter espaços. Para obter mais informações, consulte [Suporte ao firewall do Azure Cosmos DB](documentdb-firewall-support.md)
* `<databaseaccountname>` pode usar apenas letras minúsculas, números, o caractere '-' e deve ter entre três e 50 caracteres.
* `<databaseaccountlocation>` deve ser uma das regiões na qual o Azure Cosmos DB está disponível. Confira a lista atual de regiões na [página Regiões do Azure](https://azure.microsoft.com/regions/#services).

Entrada de exemplo:

    azure resource create -g new_res_group -n samplecliacct -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l westus -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"\",\"locations\":["{\"locationName\":\"westus\",\"failoverPriority\":\"0\"}"]}"

Que produz a seguinte saída à medida que sua nova conta é provisionada:

    info:    Executing command resource create
    + Getting resource samplecliacct
    + Creating resource samplecliacct
    info:    Resource samplecliacct is updated
    data:
    data:    Id:        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group/providers/Microsoft.DocumentDB/databaseAccounts/samplecliacct
    data:    Name:      samplecliacct
    data:    Type:      Microsoft.DocumentDB/databaseAccounts
    data:    Parent:
    data:    Location:  West US
    data:    Tags:
    data:
    info:    resource create command OK

Se você encontrar erros, confira a [Solução de problemas](#troubleshooting).

Após o retorno do comando, a conta ficará no estado **Criando** por alguns minutos antes de mudar para o estado **Online**, no qual estará pronta para uso. Verifique o status da conta no [portal do Azure](https://portal.azure.com), na folha **Contas do Azure Cosmos DB**.

### <a id="create-single-documentdb-account-cli-arm"></a> Criar uma conta do Azure Cosmos DB em uma única região usando a CLI 1.0 do Azure com modelos do Resource Manager
As instruções desta seção descrevem como criar uma conta do Azure Cosmos DB com um modelo do Azure Resource Manager e um arquivo de parâmetros opcional, ambos arquivos JSON. O uso de um modelo permite a descrição exata do que você deseja e também a repetição sem erros.

Crie um arquivo de modelo local com o seguinte conteúdo. Nomeie o arquivo como azuredeploy.json.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "type": "string"
            },
            "locationName1": {
                "type": "string"
            }
        },
        "variables": {},
        "resources": [
            {
                "apiVersion": "2015-04-08",
                "type": "Microsoft.DocumentDb/databaseAccounts",
                "name": "[parameters('databaseAccountName')]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "databaseAccountOfferType": "Standard",
                    "ipRangeFilter": "",
                    "locations": [
                        {
                            "failoverPriority": 0,
                            "locationName": "[parameters('locationName1')]"
                        }
                    ]
                }
            }
        ]
    }

O failoverPriority deve ser definido como 0, pois esta é uma conta de região única. Uma failoverPriority igual a 0 indica que essa região deve ser mantida como a [região de gravação para a conta do Azure Cosmos DB][scaling-globally].
Você pode inserir o valor na linha de comando ou criar um arquivo de parâmetro para especificar o valor.

Para criar um arquivo de parâmetro, copie o seguinte conteúdo em um novo arquivo e nomeie esse arquivo como azuredeploy.parameters.json. Se você planeja especificar o nome da conta de banco de dados no prompt de comando, continue sem criar esse arquivo.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "value": "samplearmacct"
            },
            "locationName1": {
                "value": "westus"
            }
        }
    }

No arquivo azuredeploy.parameters.json, atualize o campo `"samplearmacct"` com o nome do banco de dados que você deseja usar e salve o arquivo. `"databaseAccountName"` pode usar apenas letras minúsculas, números, o caractere '-' e deve ter entre três e 50 caracteres. Atualize o campo de valor `"locationName1"` para a região na qual você deseja criar a conta do Azure Cosmos DB.

Para criar uma conta do Azure Cosmos DB em seu grupo de recursos, execute o comando a seguir e forneça o caminho para o arquivo de modelo, o caminho para o arquivo de parâmetro ou o valor do parâmetro, o nome do grupo de recursos no qual você deseja implantar e um nome de implantação (-n é opcional).

Para usar um arquivo de parâmetro:

    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g <resourcegroupname> -n <deploymentname>

* `<PathToTemplate>` é o caminho até o arquivo azuredeploy.json criado na Etapa 1. Se o nome do caminho tiver espaços, delimite o parâmetro com aspas duplas.
* `<PathToParameterFile>` é o caminho até o arquivo azuredeploy.parameters.json criado na Etapa 1. Se o nome do caminho tiver espaços, delimite o parâmetro com aspas duplas.
* `<resourcegroupname>` é o nome do grupo de recursos existente no qual uma conta de banco de dados do Azure Cosmos DB será adicionada.
* `<deploymentname>` é o nome opcional da implantação.

Entrada de exemplo:

    azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json -g new_res_group -n azuredeploy

OU para especificar o parâmetro de nome de conta do banco de dados sem um arquivo de parâmetro e, em vez disso, receber uma solicitação pelo valor, execute o seguinte comando:

    azure group deployment create -f <PathToTemplate> -g <resourcegroupname> -n <deploymentname>

Exemplo de entrada que exibe o prompt e a entrada de uma conta de banco de dados denominada samplearmacct:

    azure group deployment create -f azuredeploy.json -g new_res_group -n azuredeploy
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    databaseAccountName: samplearmacct

À medida que a conta for provisionada, você receberá as seguintes informações:

    info:    Executing command group deployment create
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "azuredeploy"
    + Waiting for deployment to complete
    +
    +
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Running
    +
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Succeeded
    data:    DeploymentName     : azuredeploy
    data:    ResourceGroupName  : new_res_group
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-11-30T18:50:23.6300288Z
    data:    Mode               : Incremental
    data:    CorrelationId      : 4a5d4049-c494-4053-bad4-cc804d454700
    data:    DeploymentParameters :
    data:    Name                 Type    Value
    data:    -------------------  ------  ------------------
    data:    databaseAccountName  String  samplearmacct
    data:    locationName1        String  westus
    info:    group deployment create command OK

Se você encontrar erros, confira a [Solução de problemas](#troubleshooting).  

Após o retorno do comando, a conta ficará no estado **Criando** por alguns minutos antes de mudar para o estado **Online**, no qual estará pronta para uso. Verifique o status da conta no [portal do Azure](https://portal.azure.com), na folha **Contas do Azure Cosmos DB**.

## <a id="quick-create-documentdb-with-mongodb-api-account"></a>Tarefa: Criar uma conta do Azure Cosmos DB em uma única região: conta da API para MongoDB
Use as instruções nesta seção para criar uma conta do MongoDB de região única. Isso pode ser feito usando a CLI do Azure 1.0 com os modelos do Resource Manager.

### <a id="create-single-documentdb-with-mongodb-api-account-cli-arm"></a> Criar uma conta do MongoDB de região única usando a CLI do Azure 1.0 com modelos do Resource Manager
As instruções nesta seção descrevem como criar uma API para a conta do MongoDB com um modelo do Azure Resource Manager e um arquivo de parâmetros opcional, ambos arquivos JSON. O uso de um modelo permite a descrição exata do que você deseja e também a repetição sem erros.

Crie um arquivo de modelo local com o seguinte conteúdo. Nomeie o arquivo como azuredeploy.json.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "type": "string"
            },
            "locationName1": {
                "type": "string"
            }
        },
        "variables": {},
        "resources": [
            {
                "apiVersion": "2015-04-08",
                "type": "Microsoft.DocumentDb/databaseAccounts",
                "name": "[parameters('databaseAccountName')]",
                "location": "[resourceGroup().location]",
                "kind": "MongoDB",
                "properties": {
                    "databaseAccountOfferType": "Standard",
                    "ipRangeFilter": "",
                    "locations": [
                        {
                            "failoverPriority": 0,
                            "locationName": "[parameters('locationName1')]"
                        }
                    ]
                }
            }
        ]
    }

O tipo deve ser definido como MongoDB para especificar que essa conta dará suporte às APIs do MongoDB. Se nenhuma propriedade de tipo for especificada, o padrão será uma conta da API do DocumentDB no Azure Cosmos DB.

O failoverPriority deve ser definido como 0, pois esta é uma conta de região única. Uma failoverPriority igual a 0 indica que essa região deve ser mantida como a [região de gravação para a conta do Azure Cosmos DB][scaling-globally].
Você pode inserir o valor na linha de comando ou criar um arquivo de parâmetro para especificar o valor.

Para criar um arquivo de parâmetro, copie o seguinte conteúdo em um novo arquivo e nomeie esse arquivo como azuredeploy.parameters.json. Se você planeja especificar o nome da conta de banco de dados no prompt de comando, continue sem criar esse arquivo.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "value": "samplearmacct"
            },
            "locationName1": {
                "value": "westus"
            }
        }
    }

No arquivo azuredeploy.parameters.json, atualize o campo `"samplearmacct"` com o nome do banco de dados que você deseja usar e salve o arquivo. `"databaseAccountName"` pode usar apenas letras minúsculas, números, o caractere '-' e deve ter entre três e 50 caracteres. Atualize o campo de valor `"locationName1"` para a região na qual você deseja criar a conta do Azure Cosmos DB.

Para criar uma conta do Azure Cosmos DB em seu grupo de recursos, execute o comando a seguir e forneça o caminho para o arquivo de modelo, o caminho para o arquivo de parâmetro ou o valor do parâmetro, o nome do grupo de recursos no qual você deseja implantar e um nome de implantação (-n é opcional).

Para usar um arquivo de parâmetro:

    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g <resourcegroupname> -n <deploymentname>

* `<PathToTemplate>` é o caminho até o arquivo azuredeploy.json criado na Etapa 1. Se o nome do caminho tiver espaços, delimite o parâmetro com aspas duplas.
* `<PathToParameterFile>` é o caminho até o arquivo azuredeploy.parameters.json criado na Etapa 1. Se o nome do caminho tiver espaços, delimite o parâmetro com aspas duplas.
* `<resourcegroupname>` é o nome do grupo de recursos existente no qual uma conta de banco de dados do Azure Cosmos DB será adicionada.
* `<deploymentname>` é o nome opcional da implantação.

Entrada de exemplo:

    azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json -g new_res_group -n azuredeploy

OU para especificar o parâmetro de nome de conta do banco de dados sem um arquivo de parâmetro e, em vez disso, receber uma solicitação pelo valor, execute o seguinte comando:

    azure group deployment create -f <PathToTemplate> -g <resourcegroupname> -n <deploymentname>

Exemplo de entrada que exibe o prompt e a entrada de uma conta de banco de dados denominada samplearmacct:

    azure group deployment create -f azuredeploy.json -g new_res_group -n azuredeploy
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    databaseAccountName: samplearmacct

À medida que a conta for provisionada, você receberá as seguintes informações:

    info:    Executing command group deployment create
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "azuredeploy"
    + Waiting for deployment to complete
    +
    +
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Running
    +
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Succeeded
    data:    DeploymentName     : azuredeploy
    data:    ResourceGroupName  : new_res_group
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-11-30T18:50:23.6300288Z
    data:    Mode               : Incremental
    data:    CorrelationId      : 4a5d4049-c494-4053-bad4-cc804d454700
    data:    DeploymentParameters :
    data:    Name                 Type    Value
    data:    -------------------  ------  ------------------
    data:    databaseAccountName  String  samplearmacct
    data:    locationName1        String  westus
    info:    group deployment create command OK

Se você encontrar erros, confira a [Solução de problemas](#troubleshooting).  

Após o retorno do comando, a conta ficará no estado **Criando** por alguns minutos antes de mudar para o estado **Online**, no qual estará pronta para uso. Verifique o status da conta no [portal do Azure](https://portal.azure.com), na folha **Contas do Azure Cosmos DB**.

## <a id="create-multi-documentdb-account"></a>Tarefa: Criar uma conta do Azure Cosmos DB em várias regiões
O Azure Cosmos DB tem a capacidade de [distribuir os dados globalmente][distribute-globally] entre várias [regiões do Azure](https://azure.microsoft.com/regions/#services). Ao criar uma conta do Azure Cosmos DB, é possível especificar as regiões nas quais você deseja que o serviço esteja presente. Use as instruções desta seção para criar uma conta do Azure Cosmos DB em várias regiões. Isso pode ser feito usando a CLI do Azure 1.0 com ou sem os modelos do Resource Manager.

### <a id="create-multi-documentdb-account-cli"></a> Criar uma conta do Azure Cosmos DB em várias regiões usando a CLI 1.0 do Azure sem modelos do Resource Manager
Crie uma conta do Azure Cosmos DB no grupo de recursos novo ou existente inserindo o seguinte comando no prompt de comando:

> [!TIP]
> Se você executar este comando no Azure PowerShell ou no Windows PowerShell, receberá um erro sobre um token inesperado. Em vez disso, execute este comando no Prompt de Comando do Windows.
>
>

    azure resource create -g <resourcegroupname> -n <databaseaccountname> -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l <resourcegrouplocation> -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"<ip-range-filter>\",\"locations\":["{\"locationName\":\"<databaseaccountlocation1>\",\"failoverPriority\":\"<failoverPriority1>\"},{\"locationName\":\"<databaseaccountlocation2>\",\"failoverPriority\":\"<failoverPriority2>\"}"]}"

* `<resourcegroupname>` pode usar apenas caracteres alfanuméricos, pontos, sublinhados, o caractere '-' e parênteses, e não pode terminar em um ponto.
* `<resourcegrouplocation>` é a região do grupo de recursos atual.
* `<ip-range-filter>` Especifica o conjunto de endereços IP ou os intervalos de endereços IP no formato CIDR a serem incluídos como a lista de permissões de IPs de cliente para uma determinada conta de banco de dados. Os intervalos/endereços IP devem ser separados por vírgula e não devem conter espaços. Para obter mais informações, consulte [Suporte ao firewall do Azure Cosmos DB](documentdb-firewall-support.md)
* `<databaseaccountname>` pode usar apenas letras minúsculas, números, o caractere '-' e deve ter entre três e 50 caracteres.
* `<databaseaccountlocation1>` e `<databaseaccountlocation2>` devem ser regiões nas quais o Azure Cosmos DB está disponível. Confira a lista atual de regiões na [página Regiões do Azure](https://azure.microsoft.com/regions/#services).

Entrada de exemplo:

    azure resource create -g new_res_group -n samplecliacct -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l westus -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"\",\"locations\":["{\"locationName\":\"westus\",\"failoverPriority\":\"0\"},{\"locationName\":\"eastus\",\"failoverPriority\":\"1\"}"]}"

Que produz a seguinte saída à medida que sua nova conta é provisionada:

    info:    Executing command resource create
    + Getting resource samplecliacct
    + Creating resource samplecliacct
    info:    Resource samplecliacct is updated
    data:
    data:    Id:        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group/providers/Microsoft.DocumentDB/databaseAccounts/samplecliacct
    data:    Name:      samplecliacct
    data:    Type:      Microsoft.DocumentDB/databaseAccounts
    data:    Parent:
    data:    Location:  West US
    data:    Tags:
    data:
    info:    resource create command OK

Se você encontrar erros, confira a [Solução de problemas](#troubleshooting).

Após o retorno do comando, a conta ficará no estado **Criando** por alguns minutos antes de mudar para o estado **Online**, no qual estará pronta para uso. Verifique o status da conta no [portal do Azure](https://portal.azure.com), na folha **Contas do Azure Cosmos DB**.

### <a id="create-multi-documentdb-account-cli-arm"></a> Criar uma conta do Azure Cosmos DB em várias regiões usando a CLI 1.0 do Azure com modelos do Resource Manager
As instruções desta seção descrevem como criar uma conta do Azure Cosmos DB com um modelo do Azure Resource Manager e um arquivo de parâmetros opcional, ambos arquivos JSON. O uso de um modelo permite a descrição exata do que você deseja e também a repetição sem erros.

Crie um arquivo de modelo local com o seguinte conteúdo. Nomeie o arquivo como azuredeploy.json.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "type": "string"
            },
            "locationName1": {
                "type": "string"
            },
            "locationName2": {
                "type": "string"
            }
        },
        "variables": {},
        "resources": [
            {
                "apiVersion": "2015-04-08",
                "type": "Microsoft.DocumentDb/databaseAccounts",
                "name": "[parameters('databaseAccountName')]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "databaseAccountOfferType": "Standard",
                    "ipRangeFilter": "",
                    "locations": [
                        {
                            "failoverPriority": 0,
                            "locationName": "[parameters('locationName1')]"
                        },
                        {
                            "failoverPriority": 1,
                            "locationName": "[parameters('locationName2')]"
                        }
                    ]
                }
            }
        ]
    }

O arquivo de modelo anterior pode ser usado para criar uma conta do Azure Cosmos DB com duas regiões. Para criar a conta com mais regiões, adicione-a à matriz "locais" e adicione os parâmetros correspondentes.

Uma das regiões deve ter um valor de failoverPriority igual a 0 para indicar que essa região deve ser mantida como a [região de gravação para a conta do Azure Cosmos DB][scaling-globally]. Os valores de prioridade de failover devem ser exclusivos entre os locais, e o valor de prioridade de failover mais alto deve ser inferior ao número total de regiões. Você pode inserir o valor na linha de comando ou criar um arquivo de parâmetro para especificar o valor.

Para criar um arquivo de parâmetro, copie o seguinte conteúdo em um novo arquivo e nomeie esse arquivo como azuredeploy.parameters.json. Se você planeja especificar o nome da conta de banco de dados no prompt de comando, continue sem criar esse arquivo.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "value": "samplearmacct"
            },
            "locationName1": {
                "value": "westus"
            },
            "locationName2": {
                "value": "eastus"
            }
        }
    }

No arquivo azuredeploy.parameters.json, atualize o campo `"samplearmacct"` com o nome do banco de dados que você deseja usar e salve o arquivo. `"databaseAccountName"` pode usar apenas letras minúsculas, números, o caractere '-' e deve ter entre três e 50 caracteres. Atualize o campo de valor de `"locationName1"` e `"locationName2"` para a região na qual você deseja criar a conta do Azure Cosmos DB.

Para criar uma conta do Azure Cosmos DB em seu grupo de recursos, execute o comando a seguir e forneça o caminho para o arquivo de modelo, o caminho para o arquivo de parâmetro ou o valor do parâmetro, o nome do grupo de recursos no qual você deseja implantar e um nome de implantação (-n é opcional).

Para usar um arquivo de parâmetro:

    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g <resourcegroupname> -n <deploymentname>

* `<PathToTemplate>` é o caminho até o arquivo azuredeploy.json criado na Etapa 1. Se o nome do caminho tiver espaços, delimite o parâmetro com aspas duplas.
* `<PathToParameterFile>` é o caminho até o arquivo azuredeploy.parameters.json criado na Etapa 1. Se o nome do caminho tiver espaços, delimite o parâmetro com aspas duplas.
* `<resourcegroupname>` é o nome do grupo de recursos existente no qual uma conta de banco de dados do Azure Cosmos DB será adicionada.
* `<deploymentname>` é o nome opcional da implantação.

Entrada de exemplo:

    azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json -g new_res_group -n azuredeploy

OU para especificar o parâmetro de nome de conta do banco de dados sem um arquivo de parâmetro e, em vez disso, receber uma solicitação pelo valor, execute o seguinte comando:

    azure group deployment create -f <PathToTemplate> -g <resourcegroupname> -n <deploymentname>

Exemplo de entrada que exibe o prompt e a entrada de uma conta de banco de dados denominada samplearmacct:

    azure group deployment create -f azuredeploy.json -g new_res_group -n azuredeploy
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    databaseAccountName: samplearmacct

À medida que a conta for provisionada, você receberá as seguintes informações:

    info:    Executing command group deployment create
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "azuredeploy"
    + Waiting for deployment to complete
    +
    +
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Running
    +
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Succeeded
    data:    DeploymentName     : azuredeploy
    data:    ResourceGroupName  : new_res_group
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-11-30T18:50:23.6300288Z
    data:    Mode               : Incremental
    data:    CorrelationId      : 4a5d4049-c494-4053-bad4-cc804d454700
    data:    DeploymentParameters :
    data:    Name                 Type    Value
    data:    -------------------  ------  ------------------
    data:    databaseAccountName  String  samplearmacct
    data:    locationName1        String  westus
    data:    locationName2        String  eastus
    info:    group deployment create command OK

Se você encontrar erros, confira a [Solução de problemas](#troubleshooting).  

Após o retorno do comando, a conta ficará no estado **Criando** por alguns minutos antes de mudar para o estado **Online**, no qual estará pronta para uso. Verifique o status da conta no [portal do Azure](https://portal.azure.com), na folha **Contas do Azure Cosmos DB**.

## <a name="troubleshooting"></a>Solucionar problemas
Se você receber erros como `Deployment provisioning state was not successful` ao criar sua conta de banco de dados ou grupo de recursos, poderá usar algumas opções para a solução do problema.

> [!NOTE]
> O fornecimento de caracteres incorretos no nome da conta de banco de dados ou de uma localização na qual o Azure Cosmos DB não está disponível causará erros de implantação. Os nomes de conta de banco de dados podem usar apenas letras minúsculas, números, o caractere '-' e devem ter entre três e 50 caracteres. Todos os locais de conta do banco de dados válidos são listados na [página Regiões do Azure](https://azure.microsoft.com/regions/#services).
>
>

* Se a saída apresentar o seguinte `Error information has been recorded to C:\Users\wendy\.azure\azure.err`, examine as informações de erro no arquivo azure.err.
* Você pode encontrar informações úteis no arquivo de log do grupo de recursos. Para exibir o arquivo de log, execute o seguinte comando:

        azure group log show <resourcegroupname> --last-deployment

    Entrada de exemplo:

        azure group log show new_res_group --last-deployment

    Em seguida, confira [Solucionando problemas de implantações do grupo de recursos no Azure](../azure-resource-manager/resource-manager-common-deployment-errors.md) para saber mais.
* As informações sobre o erro também estão disponíveis no Portal do Azure, conforme mostra a seguinte captura de tela. Para navegar até as informações sobre o erro: clique em Grupos de Recursos na Jumpbar, selecione o Grupo de Recursos que apresentou o erro e, na área Essentials da folha Grupo de Recursos, clique na data da Última Implantação. Na folha Histórico de implantação escolha a implantação com falha e, na folha Implantação, clique no Detalhe da operação com o ponto de exclamação vermelho. A Mensagem de Status da implantação com falha é exibida na folha Detalhes da operação.

    ![Captura de tela do portal do Azure mostrando como navegar até a mensagem de erro de implantação](media/documentdb-automation-resource-manager-cli/portal-troubleshooting-deploy.png)

## <a name="next-steps"></a>Próximas etapas
Agora que você tem uma conta do Azure Cosmos DB, a próxima etapa é criar um banco de dados do Azure Cosmos DB. Você pode criar um banco de dados usando:

* O portal do Azure, conforme descrito em [Criar uma coleção e um banco de dados do Azure Cosmos DB usando o portal do Azure](documentdb-create-collection.md).
* As amostras do C# .NET no projeto [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) do repositório [azure-documentdb-dotnet](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) no GitHub.
* Os [SDKs da API do DocumentDB no Azure Cosmos DB](https://msdn.microsoft.com/library/azure/dn781482.aspx). As APIs do DocumentDB no Azure Cosmos DB têm SDKs da API do .NET, Java, Python, Node.js e JavaScript.

Depois de criar seu banco de dados, você precisará [adicionar uma ou mais coleções](documentdb-create-collection.md) ao banco de dados e [adicionar documentos](documentdb-view-json-document-explorer.md) às coleções.

Depois que os documentos estiverem em uma coleção, você poderá usar o [SQL](documentdb-sql-query.md) para [executar consultas](documentdb-sql-query.md#ExecutingSqlQueries) nos documentos usando o [Gerenciador de Consultas](documentdb-query-collections-query-explorer.md) no portal, a [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou um dos [SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx).

Para saber mais sobre o Azure Cosmos DB, consulte a [Introdução ao multimodelo do Azure Cosmos DB](../cosmos-db/introduction.md).

Para obter mais modelos que você possa usar, confira [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/).

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[distribute-globally]: https://azure.microsoft.com/documentation/articles/documentdb-distribute-data-globally
[scaling-globally]: https://azure.microsoft.com/documentation/articles/documentdb-distribute-data-globally/#scaling-across-the-planet

