<properties
   pageTitle="Criação de modelos do Azure Resource Manager | Microsoft Azure"
   description="Crie modelos do Gerenciador de Recursos do Azure usando sintaxe JSON declarativa para implantar aplicativos no Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/19/2016"
   ms.author="tomfitz"/>

# Criando modelos do Gerenciador de Recursos do Azure

Este tópico descreve a estrutura de um modelo do Azure Resource Manager. Ele apresenta as diferentes seções de um modelo e as propriedades que estão disponíveis nessas seções. O modelo consiste em JSON e expressões que podem ser usados na construção de valores para sua implantação.

Para obter orientações sobre como criar um modelo, consulte [Passo a Passo do Modelo do Resource Manager](resource-manager-template-walkthrough.md). Para ver recomendações sobre como criar modelos, consulte [Práticas recomendadas para criar modelos do Azure Resource Manager](resource-manager-template-best-practices.md).

Um bom editor JSON pode simplificar a tarefa de criação de modelos. Para obter informações sobre como usar o Visual Studio com seus modelos, consulte [Criar e implantar grupos de recursos do Azure com o Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md). Para obter informações sobre como usar o VS Code, consulte [Trabalhando com Modelos do Azure Resource Manager no Visual Studio Code](resource-manager-vs-code.md).

Você deve limitar o tamanho de seu modelo para 1 MB, e cada arquivo de parâmetro para 64 KB. O limite de 1 MB se aplica para o estado final do modelo depois que ele foi expandido com definições de recurso iterativo e valores para variáveis e parâmetros.

## Formato de modelo

Em sua estrutura mais simples, um modelo contém os seguintes elementos.

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

| Nome do elemento | Obrigatório | Descrição
| :------------: | :------: | :----------
| $schema | Sim | Local do arquivo de esquema JSON que descreve a versão da linguagem do modelo. Você deve usar a URL mostrada acima.
| contentVersion | Sim | Versão do modelo (como 1.0.0.0). Você pode fornecer qualquer valor para esse elemento. Ao implantar recursos com o modelo, esse valor pode ser usado para garantir que o modelo certo esteja sendo usado.
| parameters | Não | Valores que são fornecidos quando a implantação é executada para personalizar a implantação dos recursos.
| variáveis | Não | Valores que são usados como fragmentos JSON no modelo para simplificar expressões de linguagem do modelo.
| recursos | Sim | Tipos de recursos que são implantados ou atualizados em um grupo de recursos.
| outputs | Não | Valores que são retornados após a implantação.

Examinaremos as seções do modelo em detalhes mais adiante neste tópico. Por enquanto, analisaremos parte da sintaxe que compõe o modelo.

## Expressões e funções

A sintaxe básica do modelo é JSON. No entanto, expressões e funções estendem o JSON que está disponível no modelo e permitem criar valores que não são valores literais rígidos. As expressões são colocadas entre colchetes, [ e ], e avaliadas quando o modelo é implantado. As expressões podem aparecer em qualquer lugar em um valor de cadeia de caracteres JSON e sempre retornam outro valor JSON. Se precisar usar uma cadeia de caracteres literal que comece com um colchete [, você deverá usar dois colchetes [[.

Normalmente, você usa expressões com funções para executar operações e configurar a implantação. Assim como no JavaScript, as chamadas de função são formatadas como **functionName(arg1,arg2,arg3)**. Você faz referência às propriedades usando os operadores dot e [index].

O seguinte exemplo mostra como usar várias das funções ao construir valores:
 
    "variables": {
       "location": "[resourceGroup().location]",
       "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
       "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

Para obter a lista completa das funções de modelo, veja [Funções de modelo do Gerenciador de Recursos do Azure](resource-group-template-functions.md).


## Parâmetros

Na seção de parâmetros do modelo, você deve especificar os valores que você pode inserir ao implantar os recursos. Esses valores de parâmetro permitem personalizar a implantação fornecendo valores que são personalizados para um determinado ambiente (como desenvolvimento, teste e produção). Você não precisa fornecer parâmetros em seu modelo, mas sem parâmetros o modelo sempre implantaria os mesmos recursos com o mesmo nomes, locais e propriedades.

Você pode usar esses valores de parâmetro em todo o modelo para definir valores para os recursos implantados. Somente os parâmetros que são declarados na seção de parâmetros podem ser usados em outras seções do modelo.

Você define parâmetros com a seguinte estrutura:

    "parameters": {
       "<parameterName>" : {
         "type" : "<type-of-parameter-value>",
         "defaultValue": "<optional-default-value-of-parameter>",
         "allowedValues": [ "<optional-array-of-allowed-values>" ],
         "minValue": <optional-minimum-value-for-int-parameters>,
         "maxValue": <optional-maximum-value-for-int-parameters>,
         "minLength": <optional-minimum-length-for-string-secureString-array-parameters>,
         "maxLength": <optional-maximum-length-for-string-secureString-array-parameters>,
         "metadata": {
             "description": "<optional-description-of-the parameter>" 
         }
       }
    }

| Nome do elemento | Obrigatório | Descrição
| :------------: | :------: | :----------
| parameterName | Sim | Nome do parâmetro. Deve ser um identificador JavaScript válido.
| type | Sim | Tipo do valor do parâmetro. Consulte a lista de tipos permitidos abaixo.
| defaultValue | Não | Valor padrão do parâmetro, se nenhum valor for fornecido para o parâmetro.
| allowedValues | Não | Matriz de valores permitidos para o parâmetro para garantir que o valor correto seja fornecido.
| minValue | Não | O valor mínimo para parâmetros de tipo int, esse valor é inclusivo.
| maxValue | Não | O valor máximo para parâmetros de tipo int, esse valor é inclusivo.
| minLength | Não | O comprimento mínimo para cadeia, secureString e parâmetros do tipo de matriz, esse valor é inclusivo.
| maxLength | Não | O comprimento máximo para cadeia, secureString e parâmetros do tipo de matriz, esse valor é inclusivo.
| description | Não | Descrição do parâmetro que será exibida aos usuários do modelo por meio da interface do modelo personalizado do portal.

Os valores e tipos permitidos são:

- string ou secureString - qualquer cadeia de caracteres JSON válida
- int - qualquer inteiro JSON válido
- bool - qualquer booliano JSON válido
- object ou secureObject - qualquer objeto JSON válido
- array - qualquer matriz JSON válida

Para especificar um parâmetro como opcional, forneça um defaultValue (pode ser uma cadeia de caracteres vazia).

Se você especificar um nome de parâmetro que corresponde a um dos parâmetros no comando para implantar o modelo (como incluir um parâmetro denominado **ResourceGroupName** em seu modelo que é igual ao parâmetro **ResourceGroupName** no cmdlet [New-AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx)), será solicitado a fornecer um valor para um parâmetro com o sufixo **FromTemplate** (como **ResourceGroupNameFromTemplate**). Em geral, você deve evitar essa confusão não dando aos parâmetros o mesmo nome dos parâmetros usados para operações de implantação.

>[AZURE.NOTE] Todas as senhas, chaves e outros segredos devem usar o tipo **secureString**. Os parâmetros do modelo com o tipo secureString não podem ser lidos após a implantação de recursos.

O seguinte exemplo mostra como definir parâmetros:

    "parameters": {
      "siteName": {
        "type": "string",
        "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]"
      },
      "hostingPlanName": {
        "type": "string",
        "defaultValue": "[concat(parameters('siteName'),'-plan')]"
      },
      "skuName": {
        "type": "string",
        "defaultValue": "F1",
        "allowedValues": [
          "F1",
          "D1",
          "B1",
          "B2",
          "B3",
          "S1",
          "S2",
          "S3",
          "P1",
          "P2",
          "P3",
          "P4"
        ]
      },
      "skuCapacity": {
        "type": "int",
        "defaultValue": 1,
        "minValue": 1
      }
    }

Para saber como inserir os valores do parâmetro durante a implantação, consulte [Implantar um aplicativo com o modelo do Azure Resource Manager](resource-group-template-deploy.md#parameter-file).

## Variáveis

Na seção de variáveis, você constrói valores que podem ser usados em todo o seu modelo. Normalmente, essas variáveis serão baseadas em valores fornecidos pelos parâmetros. Você não precisa definir variáveis, mas normalmente elas simplificam seu modelo reduzindo expressões complexas.

Você define variáveis com a seguinte estrutura:

    "variables": {
       "<variable-name>": "<variable-value>",
       "<variable-name>": { 
           <variable-complex-type-value> 
       }
    }

O seguinte exemplo mostra como definir uma variável que é construída com base em dois valores de parâmetro:

     "variables": {
       "connectionString": "[concat('Name=', parameters('username'), ';Password=', parameters('password'))]"
    }

O próximo exemplo mostra uma variável que é um tipo JSON complexo e variáveis que são construídas com base em outras variáveis:

    "parameters": {
       "environmentName": {
         "type": "string",
         "allowedValues": [
           "test",
           "prod"
         ]
       }
    },
    "variables": {
       "environmentSettings": {
         "test": {
           "instancesSize": "Small",
           "instancesCount": 1
         },
         "prod": {
           "instancesSize": "Large",
           "instancesCount": 4
         }
       },
       "currentEnvironmentSettings": "[variables('environmentSettings')[parameters('environmentName')]]",
       "instancesSize": "[variables('currentEnvironmentSettings').instancesSize]",
       "instancesCount": "[variables('currentEnvironmentSettings').instancesCount]"
    }

## Recursos

Na seção de recursos, você define os recursos que são implantados ou atualizados. É aqui que o modelo pode ficar mais complicado, porque você precisa entender os tipos que está implantando para fornecer os valores corretos. Para aprender a maior parte do que você precisa saber sobre os provedores de recursos, consulte [Provedores, regiões, versões de API e esquemas do Gerenciador de Recursos](resource-manager-supported-services.md).

Você define recursos com a seguinte estrutura:

    "resources": [
       {
         "apiVersion": "<api-version-of-resource>",
         "type": "<resource-provider-namespace/resource-type-name>",
         "name": "<name-of-the-resource>",
         "location": "<location-of-resource>",
         "tags": "<name-value-pairs-for-resource-tagging>",
         "comments": "<your-reference-notes>",
         "dependsOn": [
           "<array-of-related-resource-names>"
         ],
         "properties": "<settings-for-the-resource>",
         "resources": [
           "<array-of-child-resources>"
         ]
       }
    ]

| Nome do elemento | Obrigatório | Descrição
| :----------------------: | :------: | :----------
| apiVersion | Sim | Versão da API REST a ser usada para criar o recurso. Para determinar os números de versão disponíveis para um tipo de recurso específico, confira [Versões da API com suporte](resource-manager-supported-services.md#supported-api-versions).
| type | Sim | Tipo do recurso. Esse valor é uma combinação do namespace do provedor de recursos e do tipo de recurso que tem suporte do provedor de recursos.
| name | Sim | Nome do recurso. O nome deve seguir as restrições de componente URI definidas em RFC3986. Além disso, os serviços do Azure que expõem o nome do recurso a terceiros validam o nome para garantir que ele não é uma tentativa de falsificar outra identidade. Consulte [Verificar o nome do recurso](https://msdn.microsoft.com/library/azure/mt219035.aspx).
| location | Varia | Locais geográficos com suporte do recurso fornecido. Para determinar os locais disponíveis, consulte [Regiões com suporte](resource-manager-supported-services.md#supported-regions). A maioria dos tipos de recursos exige um local, mas alguns deles (como uma atribuição de função) não.
| marcas | Não | Marcas que são associadas ao recurso.
| comentários | Não | Suas anotações para documentar os recursos em seu modelo
| dependsOn | Não | Recursos dos quais o recurso que está sendo definido depende. As dependências entre recursos são avaliadas e os recursos são implantados na ordem de dependência. Quando os recursos não dependem uns dos outros, é possível que tentem ser implantados paralelamente. O valor pode ser uma lista separada por vírgulas de nomes de recursos ou identificadores exclusivos de recursos.
| propriedades | Não | Definições de configuração específicas do recurso. Os valores para as propriedades são exatamente iguais aos valores que você fornece no corpo da solicitação para a operação da API REST (método PUT) para criar o recurso. Para obter links para a documentação do esquema de recursos ou a API REST, consulte [Provedores, regiões, versões de API e esquemas do Gerenciador de Recursos](resource-manager-supported-services.md).
| recursos | Não | Recursos filho que dependem do recurso que está sendo definido. Você pode fornecer apenas os tipos de recurso permitidos pelo esquema do recurso pai. O nome totalmente qualificado do tipo de recurso filho inclui o tipo de recurso pai, como **Microsoft.Web/sites/extensions**. A dependência no recurso pai não é implícita; você deve definir explicitamente essa dependência. 


Se o nome do recurso não for exclusivo, você poderá usar a função auxiliar **resourceId** (descrita abaixo) para obter o identificador exclusivo de qualquer recurso.

A seção de recursos contém uma matriz dos recursos a serem implantados. Dentro de cada recurso, você também pode definir uma matriz de recursos filhos para esses recursos. Portanto, a seção de recursos pode ter uma estrutura como:

    "resources": [
       {
           "name": "resourceA",
           ...
       },
       {
           "name": "resourceB",
           ...
           "resources": [
               {
                   "name": "firstChildResourceB",
                   ...
               },
               {   
                   "name": "secondChildResourceB",
                   ...
               }
           ]
       },
       {
           "name": "resourceC",
           ...
       }
    ]



O exemplo a seguir mostra um recurso **Microsoft.Web/serverfarms** e um recurso **Microsoft.Web/sites** com um recurso-filho **Extensions**. Observe que o site está marcado como dependente no farm de servidores, uma vez que o farm de servidores deve existir antes que o site possa ser implantado. Observe também que o recurso **Extensions** é filho do site.

    "resources": [
      {
        "apiVersion": "2015-08-01",
        "name": "[parameters('hostingPlanName')]",
        "type": "Microsoft.Web/serverfarms",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "HostingPlan"
        },
        "sku": {
          "name": "[parameters('skuName')]",
          "capacity": "[parameters('skuCapacity')]"
        },
        "properties": {
          "name": "[parameters('hostingPlanName')]",
          "numberOfWorkers": 1
        }
      },
      {
        "apiVersion": "2015-08-01",
        "type": "Microsoft.Web/sites",
        "name": "[parameters('siteName')]",
        "location": "[resourceGroup().location]",
        "tags": {
          "environment": "test",
          "team": "ARM"
        },
        "dependsOn": [
          "[concat('Microsoft.Web/serverFarms/', parameters('hostingPlanName'))]"
        ],
        "properties": {
          "name": "[parameters('siteName')]",
          "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
        },
        "resources": [
          {
            "apiVersion": "2015-08-01",
            "type": "extensions",
            "name": "MSDeploy",
            "dependsOn": [
              "[concat('Microsoft.Web/sites/', parameters('siteName'))]"
            ],
            "properties": {
              "packageUri": "https://auxmktplceprod.blob.core.windows.net/packages/StarterSite-modified.zip",
              "dbType": "None",
              "connectionString": "",
              "setParameters": {
                "Application Path": "[parameters('siteName')]"
              }
            }
          }
        ]
      }
    ]


## Saídas

Na seção de saídas, você especifica valores que são retornados da implantação. Por exemplo, é possível retornar o URI para acessar um recurso implantado.

O exemplo a seguir mostra a estrutura de uma definição de saída:

    "outputs": {
       "<outputName>" : {
         "type" : "<type-of-output-value>",
         "value": "<output-value-expression>"
       }
    }

| Nome do elemento | Obrigatório | Descrição
| :------------: | :------: | :----------
| outputName | Sim | Nome do valor de saída. Deve ser um identificador JavaScript válido.
| type | Sim | Tipo do valor de saída. Valores de saída oferecem suporte aos mesmos tipos que os parâmetros de entrada do modelo.
| value | Sim | Expressão de linguagem do modelo que será avaliada e retornada como valor de saída.


O exemplo a seguir mostra um valor que é retornado na seção de saídas.

    "outputs": {
       "siteUri" : {
         "type" : "string",
         "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
       }
    }

Para obter mais informações sobre como trabalhar com a saída, consulte [Compartilhando o estado nos modelos do Azure Resource Manager](best-practices-resource-manager-state.md).

## Próximas etapas
- Para exibir modelos completos para muitos tipos diferentes de soluções, consulte os [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/).
- Para obter detalhes sobre as funções que podem ser usadas em um modelo, consulte [Funções do Modelo do Azure Resource Manager](resource-group-template-functions.md).
- Para combinar vários modelos durante a implantação, consulte [Usando modelos vinculados com o Azure Resource Manager](resource-group-linked-templates.md).
- Para iterar um número de vezes especificado ao criar um tipo de recurso, consulte [Criar várias instâncias de recursos no Gerenciador de Recursos do Azure](resource-group-create-multiple.md).
- Talvez seja necessário usar recursos que existam em um grupo de recursos diferente. Isso é comum ao trabalhar com contas de armazenamento ou redes virtuais que são compartilhadas entre vários grupos de recursos. Para obter mais informações, consulte a [função resourceId](resource-group-template-functions.md#resourceid).

<!---HONumber=AcomDC_0803_2016-->