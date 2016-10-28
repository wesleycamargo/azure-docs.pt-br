<properties
	pageTitle="Automação do Banco de Dados de Documentos - Gerenciador de Recursos - CLI | Microsoft Azure"
	description="Use modelos do Gerenciador de Recursos do Azure ou a CLI para implantar uma conta de banco de dados do Banco de Dados de Documentos. O Banco de Dados de Documentos é um banco de dados NoSQL baseado em nuvem para dados JSON."
	services="documentdb"
	authors="mimig1"
	manager="jhubbard"
	editor=""
    tags="azure-resource-manager"
	documentationCenter=""/>


<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/03/2016" 
	ms.author="mimig"/>

# Automatizar a criação de conta do Banco de Dados de Documentos usando modelos do Gerenciador de Recursos do Azure e a CLI do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](documentdb-create-account.md)
- [CLI do Azure e ARM](documentdb-automation-resource-manager-cli.md)

Este artigo mostra como criar uma conta de Banco de Dados de Documentos do Azure usando modelos do Azure Resource Manager ou a CLI (Interface de Linha de Comando) do Azure. Para criar uma conta do Banco de Dados de Documentos usando o Portal do Azure, confira [Criar uma conta de banco de dados do Banco de Dados de Documentos usando o portal do Azure](documentdb-create-account.md).

- [Criar uma conta do Banco de Dados de Documentos usando a CLI](#quick-create-documentdb-account)
- [Criar uma conta do Banco de Dados de Documentos usando um modelo ARM](#deploy-documentdb-from-a-template)

No momento, as contas de banco de dados do Banco de Dados de Documentos são o único recurso do Banco de Dados de Documentos que pode ser criado usando modelos ARM e a CLI do Azure.

## Preparando-se

Para usar a CLI do Azure com grupos de recursos do Azure, você precisará ter a versão correta da CLI do Azure e uma conta do Azure. Se você não tiver a CLI do Azure, [instale-a](../xplat-cli-install.md).

### Atualizar sua versão da CLI do Azure

No prompt de comando, digite `azure --version` para ver se você já tem a versão 0.9.11 ou posterior instalada. Poderá ser solicitado que você participe da coleta de dados da CLI do Microsoft Azure nesta etapa; você poderá selecionar S ou N para aceitar ou recusar.

	azure --version
    0.9.11 (node: 0.12.7)

Se a sua versão não for a 0.9.11 ou posterior, será necessário [instalar a CLI do Azure](../xplat-cli-install.md) ou atualizá-la usando um dos instaladores nativos, ou por meio do **npm** digitando `npm update -g azure-cli` para atualizar ou `npm install -g azure-cli` para instalar.

### Definir sua conta e assinatura do Azure

Se você ainda não tiver uma assinatura do Azure, mas tiver uma assinatura do Visual Studio, poderá ativar seus [benefícios de assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Ou então, você poderá se inscrever para uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

Você precisa ter uma conta corporativa ou de estudante, ou uma identidade de conta da Microsoft, para usar os modelos de gerenciamento de recursos do Azure. Se você tiver uma dessas contas, digite o comando a seguir.

	azure login

Que produz esta saída:

    info:    Executing command login
    |info:    To sign in, use a web browser to open the page https://aka.ms/devicelogin. 
    Enter the code E1A2B3C4D to authenticate.

> [AZURE.NOTE] Se você não tiver uma conta do Azure, verá uma mensagem de erro indicando que você precisa de um tipo diferente de conta. Para criar uma com base em sua atual conta do Azure, veja [Criando uma identidade corporativa ou de estudante no Active Directory do Azure](../virtual-machines/virtual-machines-windows-create-aad-work-id.md).

Abra [https://aka.ms/devicelogin](https://aka.ms/devicelogin) em um navegador e insira o código fornecido na saída do comando.

![Captura de tela mostrando a tela de logon do dispositivo para a CLI do Microsoft Azure](media/documentdb-automation-resource-manager-cli/azure-cli-login-code.png)

Depois de inserir o código, selecione a identidade que você deseja usar no navegador e forneça seu nome de usuário e senha, se for necessário.

![Captura de tela mostrando onde selecionar a conta de identidade da Microsoft associada à assinatura do Azure que você deseja usar](media/documentdb-automation-resource-manager-cli/identity-cli-login.png)

Você receberá a seguinte tela de confirmação quando estiver conectado e poderá fechar a janela do navegador.

![Captura de tela mostrando a confirmação de logon para a Interface de linha de comando de plataforma cruzada do Microsoft Azure](media/documentdb-automation-resource-manager-cli/login-confirmation.png)

O shell de comando também fornece a saída a seguir.

    /info:    Added subscription Visual Studio Ultimate with MSDN
    info:    Setting subscription "Visual Studio Ultimate with MSDN" as default
    +
    info:    login command OK

Além do método de logon interativo descrito aqui, há métodos adicionais de logon disponíveis na CLI do Azure. Para saber mais sobre os outros métodos e informações sobre como lidar com várias assinaturas, confira [Conectar uma assinatura do Azure da CLI (Interface de Linha de Comando) do Azure](../xplat-cli-connect.md).

### Alternar para o modo de grupo de recursos da CLI do Azure

Por padrão, a CLI do Azure é iniciada no modo de gerenciamento de serviços (modo **asm**). Digite o seguinte para alternar para o modo de grupo de recursos.

	azure config mode arm

Que fornece esta saída:

    info:    Executing command config mode
    info:    New mode is arm
    info:    config mode command OK

Se necessário, você poderá voltar para o conjunto de comandos padrão digitando `azure config mode asm`.

## <a id="quick-create-documentdb-account"></a>Tarefa: criar uma conta do Banco de Dados de Documentos usando a CLI do Azure

Use as instruções nesta seção para criar uma conta do Banco de Dados de Documentos com a CLI do Azure.

### Etapa 1: criar ou recuperar o grupo de recursos

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

### <a id="create-documentdb-account-cli"></a>Etapa 2: criar uma conta do Banco de Dados de Documentos usando a CLI

Crie uma conta do Banco de Dados de Documentos no grupo de recursos novo ou existente inserindo o seguinte comando no prompt de comando:

> [AZURE.TIP] Se você executar este comando no Azure PowerShell ou no Windows PowerShell, receberá um erro sobre um token inesperado. Em vez disso, execute este comando no Prompt de Comando do Windows.

    azure resource create -g <resourcegroupname> -n <databaseaccountname> -r "Microsoft.DocumentDB/databaseAccounts" -o "2015-04-08" -l <databaseaccountlocation> -p "{"databaseAccountOfferType":"Standard"}" 

 - `<resourcegroupname>` pode usar apenas caracteres alfanuméricos, pontos, sublinhados, o caractere '-' e parênteses, e não pode terminar em um ponto.
 - `<databaseaccountname>` pode usar apenas letras minúsculas, números, o caractere '-' e deve ter entre três e 50 caracteres.
 - `<databaseaccountlocation>` deve ser uma das regiões na qual o Banco de Dados de Documentos normalmente está disponível. Confira a lista atual de regiões na [página Regiões do Azure](https://azure.microsoft.com/regions/#services).

Entrada de exemplo:

    azure resource create -g new_res_group -n samplecliacct -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08  -l westus -p "{"databaseAccountOfferType":"Standard"}"

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

Após o retorno do comando, a conta ficará no estado **Criando** por alguns minutos antes de mudar para o estado **Online**, no qual estará pronta para uso. Você pode verificar o status da conta no [portal do Azure](https://portal.azure.com), na folha **Contas do Banco de Dados de Documentos**.

## <a id="deploy-documentdb-from-a-template"></a>Tarefa: criar uma conta do Banco de Dados de Documentos usando um modelo ARM

Use as instruções nesta seção para criar uma conta do Banco de Dados de Documentos com um modelo ARM (Gerenciador de Recursos do Azure) e um arquivo de parâmetros opcionais, ambos arquivos JSON. O uso de um modelo permite a descrição exata do que você deseja e também a repetição sem erros.

### Noções básicas sobre modelos ARM e grupos de recursos

A maioria dos aplicativos é criada a partir de uma combinação de diferentes tipos de recursos (como uma ou mais contas do Banco de Dados de Documentos, contas de Armazenamento, uma rede virtual ou uma rede de distribuição de conteúdo). A API de gerenciamento de serviço do Azure padrão e o portal do Azure representava esses itens usando uma abordagem de acordo com o serviço. Essa abordagem requer que você implante e gerencie individualmente os serviços individuais (ou localize outras ferramentas fazê-lo) e não como uma única unidade lógica de implantação.

Com os *modelos do Gerenciador de Recursos do Azure*, é possível implantar e gerenciar esses diferentes recursos como uma unidade lógica de implantação de forma declarativa. Em vez de informar imperativamente ao Azure o que implantar, em um comando após o outro, você descreve a implantação inteira em um arquivo JSON (todos os recursos e configurações e parâmetros de implantação associados) e instrui o Azure a implantar esses recursos como um grupo.

Você pode saber muito mais sobre os grupos de recursos do Azure e o que eles podem fazer por você na [Visão geral do Gerenciador de Recursos do Azure](../resource-group-overview.md). Se você estiver interessado na criação de modelos, confira [Criando modelos do Gerenciador de Recursos do Azure](../resource-group-authoring-templates.md).

### Etapa 1: criar um modelo e um arquivo de parâmetro

Crie um arquivo de modelo local com o seguinte conteúdo. Nomeie o arquivo como azuredeploy.json.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "type": "string"
            }
        },
        "variables": { },
        "resources": [
            {
                "apiVersion": "2015-04-08",
                "type": "Microsoft.DocumentDb/databaseAccounts",
                "name": "[parameters('databaseAccountName')]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "name": "[parameters('databaseAccountName')]",
                    "databaseAccountOfferType": "Standard"
                }
            }
        ]
    }

Este modelo exige apenas um parâmetro, o nome da conta de banco de dados a ser criada. O local da nova conta de banco de dados é definido como o mesmo local do grupo de recursos.

Como o modelo usa apenas um parâmetro, você pode inserir o valor na linha de comando ou criar um arquivo de parâmetro para especificar o valor.

Para criar um arquivo de parâmetro, copie o seguinte conteúdo em um novo arquivo e nomeie esse arquivo como azuredeploy.parameters.json. Se você planeja especificar o nome da conta de banco de dados no prompt de comando, continue sem criar esse arquivo.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "value": "samplearmacct"
            }
        }
    }

No arquivo azuredeploy.parameters.json, atualize o valor "samplearmacct" com o nome do banco de dados que você deseja usar e salve o arquivo. O `"databaseAccountName"` pode usar apenas letras minúsculas, números, o caractere '-' e deve conter entre três e 50 caracteres.

### Etapa 2: criar ou recuperar o grupo de recursos

Para criar uma conta do Banco de Dados de Documentos, primeiro você precisará de um grupo de recursos. Se você já souber o nome do grupo de recursos que deseja usar, verifique se o local é uma [região na qual o Banco de Dados de Documentos normalmente está disponível](https://azure.microsoft.com/regions/#services) e pule para a [Etapa 3](#create-account-from-template). No modelo, o local da conta é criado na mesma região do grupo de recursos, portanto, tentar criar uma conta em uma região na qual o Banco de Dados de Documentos não está disponível resultará em um erro de implantação.

Para examinar uma lista de todos os grupos de recursos atuais, execute o seguinte comando e anote o nome do grupo de recursos que você deseja usar:

    azure group list

Para criar um novo grupo de recursos, execute o seguinte comando, especifique o nome do novo grupo de recursos a ser criado e a região na qual você quer criar o grupo de recursos:

	azure group create <resourcegroupname> <databaseaccountlocation>

 - `<resourcegroupname>` pode usar apenas caracteres alfanuméricos, pontos, sublinhados, o caractere '-' e parênteses, e não pode terminar em um ponto.
 - `<databaseaccountlocation>` deve ser uma das regiões na qual o Banco de Dados de Documentos normalmente está disponível. Confira a lista atual de regiões na [página Regiões do Azure](https://azure.microsoft.com/regions/#services).

Entrada de exemplo:

	azure group create new_res_group westus

Que produz esta saída:

    info:    Executing command group create
    + Getting resource group new_res_group
    + Creating resource group new_res_group
    info:    Created resource group new_res_group
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group
    data:    Name:                new_res_group
    data:    Location:            West US
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

Se você encontrar erros, confira a [Solução de problemas](#troubleshooting).

### <a id="create-account-from-template"></a>Etapa 3: criar a conta do Banco de Dados de Documentos usando um modelo ARM

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

Exemplo de entrada que exibe o prompt e a entrada de uma conta de banco de dados denominada new\_db\_acct:

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
    info:    group deployment create command OK

Se você encontrar erros, confira a [Solução de problemas](#troubleshooting).

Após o retorno do comando, a conta ficará no estado **Criando** por alguns minutos antes de mudar para o estado **Online**, no qual estará pronta para uso. Você pode verificar o status da conta no [portal do Azure](https://portal.azure.com), na folha **Contas do Banco de Dados de Documentos**.

## Solucionar problemas

Se você receber erros como `Deployment provisioning state was not successful` ao criar sua conta de banco de dados ou grupo de recursos, poderá usar algumas opções para a solução do problema.

> [AZURE.NOTE] O fornecimento de caracteres incorretos no nome da conta de banco de dados ou o fornecimento de um local no qual o Banco de Dados de Documentos não está disponível causará erros de implantação. Os nomes de conta de banco de dados podem usar apenas letras minúsculas, números, o caractere '-' e devem ter entre três e 50 caracteres. Todos os locais de conta do banco de dados válidos são listados na [página Regiões do Azure](https://azure.microsoft.com/regions/#services).

- Se a saída apresentar o seguinte `Error information has been recorded to C:\Users\wendy\.azure\azure.err`, examine as informações de erro no arquivo azure.err.

- Você pode encontrar informações úteis no arquivo de log do grupo de recursos. Para exibir o arquivo de log, execute o seguinte comando:

    	azure group log show <resourcegroupname> --last-deployment

    Entrada de exemplo:

    	azure group log show new_res_group --last-deployment

    Em seguida, confira [Solucionando problemas de implantações do grupo de recursos no Azure](../resource-manager-troubleshoot-deployments-cli.md) para saber mais.

- As informações sobre o erro também estão disponíveis no Portal do Azure, conforme mostra a seguinte captura de tela. Para navegar até as informações sobre o erro: clique em Grupos de Recursos na Jumpbar, selecione o Grupo de Recursos que apresentou o erro e, na área Essentials da folha Grupo de Recursos, clique na data da Última Implantação. Na folha Histórico de implantação escolha a implantação com falha e, na folha Implantação, clique no Detalhe da operação com o ponto de exclamação vermelho. A Mensagem de Status da implantação com falha é exibida na folha Detalhes da operação.

    ![Captura de tela do portal do Azure mostrando como navegar até a mensagem de erro de implantação](media/documentdb-automation-resource-manager-cli/portal-troubleshooting-deploy.png)

## Próximas etapas

Agora que você tem uma conta do Banco de Dados de Documentos, a próxima etapa é criar um banco de dados do Banco de Dados de Documentos. Você pode criar um banco de dados usando:

- O portal do Azure, como descrito em [Criar um banco de dados do Banco de Dados de Documentos usando o portal do Azure](documentdb-create-database.md).
- As amostras do C# .NET no projeto [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) do repositório [azure-documentdb-dotnet](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) no GitHub.
- As [SDKs do Banco de Dados de Documentos](https://msdn.microsoft.com/library/azure/dn781482.aspx). O Banco de Dados de Documentos tem SDKs de API de JavaScript, .NET, Java, Python e Node.js.

Depois de criar seu banco de dados, você precisa [adicionar uma ou mais coleções](documentdb-create-collection.md) ao banco de dados e [adicionar documentos](documentdb-view-json-document-explorer.md) às coleções.

Depois que os documentos estiverem em uma coleção, você poderá usar o [SQL do Banco de Dados de Documentos](documentdb-sql-query.md) para [executar consultas](documentdb-sql-query.md#executing-queries) nos documentos usando o [Gerenciador de Consultas](documentdb-query-collections-query-explorer.md) no portal, a [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou um dos [SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx).

Para saber mais sobre o Banco de Dados de Documentos, explore estes recursos:

-	[Roteiro de aprendizagem para o Banco de Dados de Documentos](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
-	[Conceitos e modelo de recursos do Banco de Dados de Documentos](documentdb-resources.md)

Para obter mais modelos que você possa usar, confira [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/).

<!---HONumber=AcomDC_0720_2016-->