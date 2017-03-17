---
title: "Automação do DocumentDB - Gerenciar regiões | Microsoft Docs"
description: "Use a CLI do Azure 1.0 e o Azure Resource Manager para gerenciar regiões em uma conta de banco de dados do DocumentDB. O Banco de Dados de Documentos é um banco de dados NoSQL baseado em nuvem para dados JSON."
services: documentdb
author: dmakwana
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: 7f765c17-8549-4108-9475-46394fc3a218
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: dimakwan
translationtype: Human Translation
ms.sourcegitcommit: 655f501f920e3169450831f501f7183ae46a4a60
ms.openlocfilehash: 70614f7d97466fb7e8a2f325d744f5e1632640a6
ms.lasthandoff: 02/27/2017


---
# <a name="automate-documentdb-account-region-management-using-azure-cli-10-and-azure-resource-manager-templates"></a>Automatizar o gerenciamento de região da conta do DocumentDB usando a CLI do Azure 1.0 e os modelos do Azure Resource Manager

Este artigo mostra como adicionar/remover uma região em sua conta do DocumentDB do Azure usando comandos da CLI do Azure 1.0 e modelos do Azure Resource Manager. O gerenciamento de região também pode ser feito no [Portal do Azure](documentdb-portal-global-replication.md). Observe que os comandos no tutorial a seguir não permitem a alteração das prioridades de failover das várias regiões. É possível adicionar ou remover regiões somente leitura. A região de gravação de uma conta de banco de dados (prioridade de failover 0) não pode ser adicionada/removida.

No momento, as contas de banco de dados do DocumentDB são o único recurso do DocumentDB que pode ser criado/modificado usando os [modelos do Azure Resource Manager e a CLI do Azure 1.0](documentdb-automation-resource-manager-cli.md).

## <a name="getting-ready"></a>Preparando-se

Para usar a CLI do Azure 1.0 com grupos de recursos do Azure, você precisará ter a versão correta da CLI do Azure 1.0 e uma conta do Azure. Se você não tiver a CLI do Azure 1.0, [instale-a](../xplat-cli-install.md).

### <a name="update-your-azure-cli-10-version"></a>Atualizar sua versão da CLI do Azure 1.0

No prompt de comando, digite `azure --version` para ver se você já tem a versão 0.10.4 ou posterior instalada. Poderá ser solicitado que você participe da coleta de dados da CLI 1.0 do Microsoft Azure nesta etapa; você poderá selecionar S ou N para aceitar ou recusar.

    azure --version
    0.10.4 (node: 4.2.4)

Se a sua versão não for a 0.10.4 ou posterior, será necessário [instalar a CLI do Azure 1.0](../xplat-cli-install.md) ou atualizá-la usando um dos instaladores nativos, ou por meio do **npm** digitando `npm update -g azure-cli` para atualizar ou `npm install -g azure-cli` para instalar.

### <a name="set-your-azure-account-and-subscription"></a>Definir sua conta e assinatura do Azure

Se você ainda não tiver uma assinatura do Azure, mas tiver uma assinatura do Visual Studio, poderá ativar seus [benefícios de assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Ou então, você poderá se inscrever para uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

Você precisa ter uma conta corporativa ou de estudante, ou uma identidade de conta da Microsoft, para usar os modelos de gerenciamento de recursos do Azure. Se você tiver uma dessas contas, digite o comando a seguir.

    azure login

Que produz esta saída: 

    info:    Executing command login
    |info:    To sign in, use a web browser to open the page https://aka.ms/devicelogin. 
    Enter the code E1A2B3C4D to authenticate.

> [!NOTE]
> Se você não tiver uma conta do Azure, verá uma mensagem de erro indicando que você precisa de um tipo diferente de conta. Para criar uma com base em sua atual conta do Azure, veja [Criando uma identidade corporativa ou de estudante no Active Directory do Azure](../virtual-machines/virtual-machines-windows-create-aad-work-id.md).

Abra [https://aka.ms/devicelogin](https://aka.ms/devicelogin) em um navegador e insira o código fornecido na saída do comando.

![Captura de tela mostrando a tela de logon do dispositivo para a CLI do Microsoft Azure 1.0](media/documentdb-automation-resource-manager-cli/azure-cli-login-code.png)

Depois de inserir o código, selecione a identidade que você deseja usar no navegador e forneça seu nome de usuário e senha, se for necessário.

![Captura de tela mostrando onde selecionar a conta de identidade da Microsoft associada à assinatura do Azure que você deseja usar](media/documentdb-automation-resource-manager-cli/identity-cli-login.png)

Você receberá a seguinte tela de confirmação quando estiver conectado e poderá fechar a janela do navegador.

![Captura de tela mostrando a confirmação de logon para a Interface de linha de comando de plataforma cruzada do Microsoft Azure](media/documentdb-automation-resource-manager-cli/login-confirmation.png)

O shell de comando também fornece a saída a seguir.

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

Para criar uma conta do Banco de Dados de Documentos, primeiro você precisará de um grupo de recursos. Se você já souber o nome do grupo de recursos que deseja usar, pule para a [Etapa 2](#create-documentdb-account-cli). 

Para examinar uma lista de todos os grupos de recursos atuais, execute o seguinte comando e anote o nome do grupo de recursos que você deseja usar: 

    azure group list

Para criar um novo grupo de recursos, execute o seguinte comando, especifique o nome do novo grupo de recursos a ser criado e a região na qual você quer criar o grupo de recursos: 

    azure group create <resourcegroupname> <resourcegrouplocation>

 - `<resourcegroupname>` pode usar apenas caracteres alfanuméricos, pontos, sublinhados, o caractere '-' e parênteses, e não pode terminar em um ponto. 
 - `<resourcegrouplocation>` deve ser uma das regiões na qual o Banco de Dados de Documentos normalmente está disponível. Confira a lista atual de regiões na [página Regiões do Azure](https://azure.microsoft.com/regions/#services).

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

## <a name="understanding-azure-resource-manager-templates-and-resource-groups"></a>Noções básicas sobre os grupos de recursos e os modelos do Azure Resource Manager

A maioria dos aplicativos é criada a partir de uma combinação de diferentes tipos de recursos (como uma ou mais contas do Banco de Dados de Documentos, contas de Armazenamento, uma rede virtual ou uma rede de distribuição de conteúdo). A API de gerenciamento de serviço do Azure padrão e o portal do Azure representava esses itens usando uma abordagem de acordo com o serviço. Essa abordagem requer que você implante e gerencie individualmente os serviços individuais (ou localize outras ferramentas fazê-lo) e não como uma única unidade lógica de implantação.

*modelos do Gerenciador de Recursos do Azure* , é possível implantar e gerenciar esses diferentes recursos como uma unidade lógica de implantação de forma declarativa. Em vez de informar imperativamente ao Azure o que implantar, em um comando após o outro, você descreve a implantação inteira em um arquivo JSON (todos os recursos e configurações e parâmetros de implantação associados) e instrui o Azure a implantar esses recursos como um grupo.

Você pode saber muito mais sobre os grupos de recursos do Azure e o que eles podem fazer por você na [Visão geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Se você estiver interessado na criação de modelos, confira [Criando modelos do Gerenciador de Recursos do Azure](../azure-resource-manager/resource-group-authoring-templates.md).


## <a id="add-region-documentdb-account"></a>Tarefa: Adicionar uma região a uma conta do DocumentDB

O DocumentDB tem a capacidade de [distribuir os dados globalmente][distribute-globally] entre várias [regiões do Azure](https://azure.microsoft.com/regions/#services). As instruções nesta seção descrevem como adicionar uma região de leitura a uma conta existente do DocumentDB com a CLI do Azure 1.0 e os modelos do Resource Manager. Isso pode ser feito usando a CLI do Azure 1.0 com ou sem os modelos do Resource Manager.

### <a id="add-region-documentdb-account-cli"></a> Adicionar uma região a uma conta do DocumentDB usando a CLI do Azure 1.0 sem modelos do Resource Manager

Adicione uma região a uma conta do DocumentDB no grupo de recursos novo ou existente inserindo o seguinte comando no prompt de comando. Observe que a matriz "locais" deve refletir a configuração de região atual dentro da conta do DocumentDB com a exceção da nova região a ser adicionada. O exemplo a seguir mostra um comando para adicionar uma segunda região à conta.

Combine os valores de prioridade de failover com a configuração existente. Uma das regiões deve ter um failoverPriority de 0 para indicar que essa região deve ser mantida como a [região de gravação para a conta do DocumentDB][scaling-globally]. Os valores de prioridade de failover devem ser exclusivos entre os locais, e o valor de prioridade de failover mais alto deve ser inferior ao número total de regiões. A nova região será uma região de "Leitura" e deve ter um valor de prioridade de failover maior do que 0.

> [!TIP]
> Se você executar este comando no Azure PowerShell ou no Windows PowerShell, receberá um erro sobre um token inesperado. Em vez disso, execute este comando no Prompt de Comando do Windows.

    azure resource create -g <resourcegroupname> -n <databaseaccountname> -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l <resourcegrouplocation> -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"<ip-range-filter>\",\"locations\":["{\"locationName\":\"<databaseaccountlocation>\",\"failoverPriority\":\"<failoverPriority1>\"},{\"locationName\":\"<newdatabaseaccountlocation>\",\"failoverPriority\":\"<failoverPriority2>\"}"]}"

 - `<resourcegroupname>` pode usar apenas caracteres alfanuméricos, pontos, sublinhados, o caractere '-' e parênteses, e não pode terminar em um ponto.
 - `<resourcegrouplocation>` é a região do grupo de recursos atual.
 - `<ip-range-filter>` Especifica o conjunto de endereços IP ou os intervalos de endereços IP no formato CIDR a serem incluídos como a lista de permissões de IPs de cliente para uma determinada conta de banco de dados. Os intervalos/endereços IP devem ser separados por vírgula e não devem conter espaços. Para saber mais, confira [DocumentDB Firewall Support](documentdb-firewall-support.md) (Suporte ao firewall do DocumentDB)
 - `<databaseaccountname>` pode usar apenas letras minúsculas, números, o caractere '-' e deve ter entre três e 50 caracteres.
 - `<databaseaccountlocation>` deve ser uma das regiões na qual o Banco de Dados de Documentos normalmente está disponível. Confira a lista atual de regiões na [página Regiões do Azure](https://azure.microsoft.com/regions/#services).
 - `<newdatabaseaccountlocation>` é a nova região a ser adicionada e deve ser uma das regiões na qual o DocumentDB está amplamente disponível. Confira a lista atual de regiões na [página Regiões do Azure](https://azure.microsoft.com/regions/#services).


Exemplo de entrada para adicionar a região "Leste dos EUA" como uma região de leitura na conta do DocumentDB: 

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

Após o retorno do comando, a conta ficará no estado **Criando** por alguns minutos antes de mudar para o estado **Online**, no qual estará pronta para uso. Você pode verificar o status da conta no [portal do Azure](https://portal.azure.com), na folha **Contas do Banco de Dados de Documentos** .

### <a id="add-region-documentdb-account-cli-arm"></a> Adicionar uma região a uma conta do DocumentDB usando a CLI do Azure 1.0 com modelos do Resource Manager

As instruções nesta seção descrevem como adicionar uma região a uma conta existente do DocumentDB com um modelo do Azure Resource Manager e um arquivo de parâmetros opcional, ambos arquivos JSON. O uso de um modelo permite a descrição exata do que você deseja e também a repetição sem erros.

Combine os valores de prioridade de failover com a configuração existente. Uma das regiões deve ter um failoverPriority de 0 para indicar que essa região deve ser mantida como a [região de gravação para a conta do DocumentDB][scaling-globally]. Os valores de prioridade de failover devem ser exclusivos entre os locais, e o valor de prioridade de failover mais alto deve ser inferior ao número total de regiões. A nova região será uma região de "Leitura" e deve ter um valor de prioridade de failover maior do que 0.

Crie um arquivo de modelo local semelhante ao mostrado a seguir, que combine com sua configuração de região atual do DocumentDB. A matriz "Locais" deve conter todas as regiões existentes na conta do banco de dados, juntamente com a nova região a ser adicionada. Nomeie o arquivo como azuredeploy.json.

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
            },
            "newLocationName": {
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
                        },
                        {
                            "failoverPriority": 2,
                            "locationName": "[parameters('newLocationName')]"
                        }
                    ]
                }
            }
        ]
    }

O arquivo de modelo acima demonstra um exemplo no qual uma nova região está sendo adicionada a uma conta do DocumentDB que já possui duas regiões.

Você pode inserir os valores de parâmetro na linha de comando ou criar um arquivo de parâmetro para especificar o valor.

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
            },
            "newLocationName": {
                "value": "northeurope"
            }
        }
    }

No arquivo azuredeploy.parameters.json, atualize o campo `"databaseAccountName"` com o nome do banco de dados que você deseja usar e salve o arquivo. `"databaseAccountName"` pode usar apenas letras minúsculas, números, o caractere '-' e deve ter entre três e 50 caracteres. Atualizar os campos de valor de `"locationName1"` e `"locationName2"` para as regiões nas quais sua conta do DocumentDB existe. Atualizar o campo de valor de `"newLocationName"` para a região que você deseja adicionar.

Para criar uma conta do Banco de Dados de Documentos em seu grupo de recursos, execute o seguinte comando e forneça o caminho até o arquivo de modelo, o caminho até o arquivo de parâmetro ou o valor do parâmetro, o nome do grupo de recursos no qual deseja implantar e um nome de implantação (-n é opcional). 

Para usar um arquivo de parâmetro:

    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g <resourcegroupname> -n <deploymentname>

 - `<PathToTemplate>` é o caminho até o arquivo azuredeploy.json criado na Etapa 1. Se o nome do caminho tiver espaços, delimite o parâmetro com aspas duplas.
 - `<PathToParameterFile>` é o caminho até o arquivo azuredeploy.parameters.json criado na Etapa 1. Se o nome do caminho tiver espaços, delimite o parâmetro com aspas duplas.
 - `<resourcegroupname>` é o nome do grupo de recursos existente no qual você quer adicionar uma conta de banco de dados do Banco de Dados de Documentos. 
 - `<deploymentname>` é o nome opcional da implantação.

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
    data:    locationName1        String  westus
    data:    locationName2        String  eastus
    data:    newLocationName      String  eastus
    data:    databaseAccountName  String  samplearmacct
    info:    group deployment create command OK

Se você encontrar erros, confira a [Solução de problemas](#troubleshooting).  

Após o retorno do comando, a conta ficará no estado **Criando** por alguns minutos antes de mudar para o estado **Online**, no qual estará pronta para uso. Você pode verificar o status da conta no [portal do Azure](https://portal.azure.com), na folha **Contas do Banco de Dados de Documentos** .

## <a id="remove-region-documentdb-account"></a>Tarefa: Remover a região de uma conta do DocumentDB

O DocumentDB tem a capacidade de [distribuir os dados globalmente][distribute-globally] entre várias [regiões do Azure](https://azure.microsoft.com/regions/#services). As instruções nesta seção descrevem como remover uma região de uma conta existente do DocumentDB com a CLI do Azure 1.0 e os modelos do Resource Manager. Isso pode ser feito usando a CLI do Azure 1.0 com ou sem os modelos do Resource Manager.

### <a id="remove-region-documentdb-account-cli"></a> Remover uma região a uma conta do DocumentDB usando a CLI do Azure 1.0 sem modelos do Resource Manager

Para remover uma região de uma conta existente do DocumentDB, o comando a seguir pode ser executado com a CLI do Azure 1.0. A matriz "Locais" deve conter apenas as regiões que devem permanecer após a remoção da região. **O local omitido será removido da conta do DocumentDB**. Insira o seguinte comando no prompt de comando.

Uma das regiões deve ter um failoverPriority de 0 para indicar que essa região deve ser mantida como a [região de gravação para a conta do DocumentDB][scaling-globally]. Os valores de prioridade de failover devem ser exclusivos entre os locais, e o valor de prioridade de failover mais alto deve ser inferior ao número total de regiões. 

> [!TIP]
> Se você executar este comando no Azure PowerShell ou no Windows PowerShell, receberá um erro sobre um token inesperado. Em vez disso, execute este comando no Prompt de Comando do Windows.

    azure resource create -g <resourcegroupname> -n <databaseaccountname> -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l <resourcegrouplocation> -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"<ip-range-filter>\",\"locations\":["{\"locationName\":\"<databaseaccountlocation>\",\"failoverPriority\":\"<failoverPriority>\"}"]}"

 - `<resourcegroupname>` pode usar apenas caracteres alfanuméricos, pontos, sublinhados, o caractere '-' e parênteses, e não pode terminar em um ponto.
 - `<resourcegrouplocation>` é a região do grupo de recursos atual.
 - `<ip-range-filter>` Especifica o conjunto de endereços IP ou os intervalos de endereços IP no formato CIDR a serem incluídos como a lista de permissões de IPs de cliente para uma determinada conta de banco de dados. Os intervalos/endereços IP devem ser separados por vírgula e não devem conter espaços. Para saber mais, confira [DocumentDB Firewall Support](documentdb-firewall-support.md) (Suporte ao firewall do DocumentDB)
 - `<databaseaccountname>` pode usar apenas letras minúsculas, números, o caractere '-' e deve ter entre três e 50 caracteres.
 - `<databaseaccountlocation>` deve ser uma das regiões na qual o Banco de Dados de Documentos normalmente está disponível. Confira a lista atual de regiões na [página Regiões do Azure](https://azure.microsoft.com/regions/#services).

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

Após o retorno do comando, a conta ficará no estado **Atualizando** por alguns minutos antes de mudar para o estado **Online**, no qual estará pronta para uso. Você pode verificar o status da conta no [portal do Azure](https://portal.azure.com), na folha **Contas do Banco de Dados de Documentos** .

### <a id="remove-region-documentdb-account-cli-arm"></a> Remover uma região de uma conta do DocumentDB usando a CLI do Azure 1.0 com modelos do Resource Manager

As instruções nesta seção descrevem como remover uma região de uma conta existente do DocumentDB com um modelo do Azure Resource Manager e um arquivo de parâmetros opcional, ambos arquivos JSON. O uso de um modelo permite a descrição exata do que você deseja e também a repetição sem erros.

Uma das regiões deve ter um failoverPriority de 0 para indicar que essa região deve ser mantida como a [região de gravação para a conta do DocumentDB][scaling-globally]. Os valores de prioridade de failover devem ser exclusivos entre os locais, e o valor de prioridade de failover mais alto deve ser inferior ao número total de regiões. 

Crie um arquivo de modelo local semelhante ao mostrado a seguir, que combine com sua configuração de região atual do DocumentDB. A matriz "Locais" deve conter apenas as regiões que devem permanecer após a remoção da região. **O local omitido será removido da conta do DocumentDB**. Nomeie o arquivo como azuredeploy.json.

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

Você pode inserir os valores de parâmetro na linha de comando ou criar um arquivo de parâmetro para especificar o valor.

Para criar um arquivo de parâmetro, copie o seguinte conteúdo em um novo arquivo e nomeie esse arquivo como azuredeploy.parameters.json. Se você planeja especificar o nome da conta de banco de dados no prompt de comando, continue sem criar esse arquivo. Adicione os parâmetros necessários definidos em seu modelo do Azure Resource Manager.

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

No arquivo azuredeploy.parameters.json, atualize o campo `"databaseAccountName"` com o nome do banco de dados que você deseja usar e salve o arquivo. `"databaseAccountName"` pode usar apenas letras minúsculas, números, o caractere '-' e deve ter entre três e 50 caracteres. Atualize o campo de valor de `"locationName1"` para as regiões nas quais você deseja que a conta do DocumentDB exista após a remoção da região.

Para criar uma conta do Banco de Dados de Documentos em seu grupo de recursos, execute o seguinte comando e forneça o caminho até o arquivo de modelo, o caminho até o arquivo de parâmetro ou o valor do parâmetro, o nome do grupo de recursos no qual deseja implantar e um nome de implantação (-n é opcional). 

Para usar um arquivo de parâmetro:

    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g <resourcegroupname> -n <deploymentname>

 - `<PathToTemplate>` é o caminho até o arquivo azuredeploy.json criado na Etapa 1. Se o nome do caminho tiver espaços, delimite o parâmetro com aspas duplas.
 - `<PathToParameterFile>` é o caminho até o arquivo azuredeploy.parameters.json criado na Etapa 1. Se o nome do caminho tiver espaços, delimite o parâmetro com aspas duplas.
 - `<resourcegroupname>` é o nome do grupo de recursos existente no qual você quer adicionar uma conta de banco de dados do Banco de Dados de Documentos. 
 - `<deploymentname>` é o nome opcional da implantação.

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

Após o retorno do comando, a conta ficará no estado **Atualizando** por alguns minutos antes de mudar para o estado **Online**, no qual estará pronta para uso. Você pode verificar o status da conta no [portal do Azure](https://portal.azure.com), na folha **Contas do Banco de Dados de Documentos** .

## <a name="troubleshooting"></a>Solução de problemas

Se você receber erros como `Deployment provisioning state was not successful` ao criar sua conta de banco de dados ou grupo de recursos, poderá usar algumas opções para a solução do problema. 

> [!NOTE]
> O fornecimento de caracteres incorretos no nome da conta de banco de dados ou o fornecimento de um local no qual o Banco de Dados de Documentos não está disponível causará erros de implantação. Os nomes de conta de banco de dados podem usar apenas letras minúsculas, números, o caractere '-' e devem ter entre três e 50 caracteres. Todos os locais de conta do banco de dados válidos são listados na [página Regiões do Azure](https://azure.microsoft.com/regions/#services).

- Se a saída apresentar o seguinte `Error information has been recorded to C:\Users\wendy\.azure\azure.err`, examine as informações de erro no arquivo azure.err.

- Você pode encontrar informações úteis no arquivo de log do grupo de recursos. Para exibir o arquivo de log, execute o seguinte comando:

        azure group log show <resourcegroupname> --last-deployment

    Exemplo de entrada:       azure group log show new_res_group --last-deployment

    Em seguida, confira [Solucionando problemas de implantações do grupo de recursos no Azure](../azure-resource-manager/resource-manager-common-deployment-errors.md) para saber mais.

- As informações sobre o erro também estão disponíveis no Portal do Azure, conforme mostra a seguinte captura de tela. Para navegar até as informações sobre o erro: clique em Grupos de Recursos na Jumpbar, selecione o Grupo de Recursos que apresentou o erro e, na área Essentials da folha Grupo de Recursos, clique na data da Última Implantação. Na folha Histórico de implantação escolha a implantação com falha e, na folha Implantação, clique no Detalhe da operação com o ponto de exclamação vermelho. A Mensagem de Status da implantação com falha é exibida na folha Detalhes da operação. 

    ![Captura de tela do portal do Azure mostrando como navegar até a mensagem de erro de implantação](media/documentdb-automation-resource-manager-cli/portal-troubleshooting-deploy.png) 

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma conta do Banco de Dados de Documentos, a próxima etapa é criar um banco de dados do Banco de Dados de Documentos. Você pode criar um banco de dados usando:

- O Portal do Azure, como descrito em [Como criar uma coleção e um banco de dados do DocumentDB usando o Portal do Azure](documentdb-create-collection.md).
- As amostras do C# .NET no projeto [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) do repositório [azure-documentdb-dotnet](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) no GitHub.
- As [SDKs do Banco de Dados de Documentos](https://msdn.microsoft.com/library/azure/dn781482.aspx). O Banco de Dados de Documentos tem SDKs de API de JavaScript, .NET, Java, Python e Node.js. 

Depois de criar seu banco de dados, você precisará [adicionar uma ou mais coleções](documentdb-create-collection.md) ao banco de dados e [adicionar documentos](documentdb-view-json-document-explorer.md) às coleções. 

Depois que os documentos estiverem em uma coleção, você poderá usar o [SQL do DocumentDB](documentdb-sql-query.md) para [executar consultas](documentdb-sql-query.md#ExecutingSqlQueries) nos documentos usando o [Gerenciador de Consultas](documentdb-query-collections-query-explorer.md) no portal, a [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou um dos [SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx).

Para saber mais sobre o DocumentDB, explore estes recursos:

-    [Roteiro de aprendizagem para o Banco de Dados de Documentos](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
-    [Conceitos e modelo de recursos do Banco de Dados de Documentos](documentdb-resources.md)

Para obter mais modelos que você possa usar, confira [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/).

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[distribute-globally]: https://azure.microsoft.com/en-us/documentation/articles/documentdb-distribute-data-globally
[scaling-globally]: https://azure.microsoft.com/en-us/documentation/articles/documentdb-distribute-data-globally/#scaling-across-the-planet

