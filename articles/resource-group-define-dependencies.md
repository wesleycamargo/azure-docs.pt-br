<properties
   pageTitle="Dependências nos modelos do Gerenciador de Recursos | Microsoft Azure"
   description="Descreve como definir um recurso como dependente de outro recurso durante a implantação para garantir que os recursos sejam implantados na ordem correta."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/12/2016"
   ms.author="tomfitz"/>

# Definindo dependências em modelos do Gerenciador de Recursos do Azure

Para um determinado recurso, pode ser necessário que existam outros recursos antes que o recurso em questão seja implantado. Por exemplo, um SQL Server deve existir antes que você tente implantar um Banco de Dados SQL. Você define essa relação marcando um recurso como dependente do outro. Normalmente, você define uma dependência com o elemento **dependsOn**, mas também pode defini-la por meio da função **reference**.

O Gerenciador de Recursos avalia as dependências entre os recursos e os implanta na ordem de dependência. Quando os recursos não dependem uns dos outros, o Gerenciador de Recursos os implanta paralelamente.

## dependsOn

No seu modelo, o elemento dependsOn permite definir um recurso como um dependente em um ou mais recursos. Seu valor pode ser uma lista de nomes de recurso separados por vírgula.

O exemplo a seguir mostra um conjunto de escala de máquina virtual que depende de um balanceador de carga, de uma rede virtual e de um loop que cria várias contas de armazenamento. Esses outros recursos não são mostrados no exemplo a seguir, mas precisariam existir em outro lugar no modelo.

    {
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "name": "[variables('namingInfix')]",
      "location": "[variables('location')]",
      "apiVersion": "2016-03-30",
      "tags": {
        "displayName": "VMScaleSet"
      },
      "dependsOn": [
        "storageLoop",
        "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
      ],
      ...
    }

Para definir uma dependência entre um recurso e os recursos criados por meio de um loop de cópia, defina o elemento dependsOn como o nome do loop. Por exemplo, consulte [Criar várias instâncias de recursos no Gerenciador de Recursos do Azure](resource-group-create-multiple.md)

Embora você talvez queira usar o dependsOn para mapear as relações entre os seus recursos, é importante entender por que você está fazendo isso, uma vez que o desempenho de sua implantação pode ser afetado. Por exemplo, para documentar como os recursos são interconectados, o dependsOn não é a abordagem correta. Você não pode consultar quais recursos foram definidos no elemento dependsOn após a implantação. Ao usar o dependsOn, você potencialmente afeta o tempo de implantação, pois o Resource Manager não implanta paralelamente dois recursos que têm uma dependência. Para documentar relações entre recursos, use a [vinculação de recursos](resource-group-link-resources.md).

## Recursos filho

A propriedade resources permite especificar os recursos filho relacionados ao recurso que está sendo definido. Os recursos filho só podem ser definidos em cinco níveis de profundidade. É importante observar que não é criada uma dependência implícita entre um recurso filho e o pai. Se precisar que o recurso filho seja implantado após o recurso pai, você deve declarar explicitamente essa dependência com a propriedade dependsOn.

Cada recurso pai aceita somente determinados tipos de recurso como recursos filho. Os tipos de recurso aceitos são especificados no [esquema do modelo](https://github.com/Azure/azure-resource-manager-schemas) do recurso pai. O nome do tipo de recurso de filho inclui o nome do tipo de recurso pai, assim como **Microsoft.Web/sites/config** e **Microsoft.Web/sites/extensions** são ambos recursos filho do **Microsoft.Web/sites**.

O exemplo a seguir mostra um SQL Server e um Banco de Dados SQL. Observe que uma dependência explícita é definida entre o Banco de Dados SQL e o SQL Server, ainda que o banco de dados seja um filho do servidor.

    "resources": [
      {
        "name": "[variables('sqlserverName')]",
        "type": "Microsoft.Sql/servers",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "SqlServer"
        },
        "apiVersion": "2014-04-01-preview",
        "properties": {
          "administratorLogin": "[parameters('administratorLogin')]",
          "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
        },
        "resources": [
          {
            "name": "[parameters('databaseName')]",
            "type": "databases",
            "location": "[resourceGroup().location]",
            "tags": {
              "displayName": "Database"
            },
            "apiVersion": "2014-04-01-preview",
            "dependsOn": [
              "[variables('sqlserverName')]"
            ],
            "properties": {
              "edition": "[parameters('edition')]",
              "collation": "[parameters('collation')]",
              "maxSizeBytes": "[parameters('maxSizeBytes')]",
              "requestedServiceObjectiveName": "[parameters('requestedServiceObjectiveName')]"
            }
          }
        ]
      }
    ]


## Função reference

A [função de referência](resource-group-template-functions.md#reference) permite que uma expressão derive seu valor de outro nome JSON e de pares de valor ou de recursos de tempo de execução. Expressões de referência declaram de maneira implícita que um recurso depende de outro.

    reference('resourceName').propertyPath

Você pode usar esse elemento ou o elemento dependsOn para especificar dependências, mas não é necessário usar ambos para o mesmo recurso dependente. Sempre que possível, use uma referência implícita para evitar adicionar inadvertidamente uma dependência desnecessária.

Para saber mais, consulte [Função de referência](resource-group-template-functions.md#reference).

## Próximas etapas

- Para saber mais sobre a criação de modelos do Gerenciador de Recursos do Azure, consulte [Criando modelos](resource-group-authoring-templates.md).
- Para obter uma lista das funções disponíveis em um modelo, consulte [Funções de modelo](resource-group-template-functions.md).

<!---HONumber=AcomDC_0914_2016-->