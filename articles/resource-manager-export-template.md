<properties
	pageTitle="Exportar o modelo do Azure Resource Manager | Microsoft Azure"
	description="Use o Azure Resource Manager para exportar um modelo a partir de um grupo de recursos existente."
	services="azure-resource-manager"
	documentationCenter=""
	authors="tfitzmac"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="06/28/2016"
	ms.author="tomfitz"/>

# Exportar um modelo do Azure Resource Manager a partir dos recursos existentes

O Gerenciador de Recursos permite que você exporte um modelo do Gerenciador de Recursos a partir dos recursos existentes em sua assinatura. Você pode usar esse modelo gerado para saber mais sobre a sintaxe do modelo ou automatizar a reimplantação de sua solução, conforme o necessário.

É importante observar que há duas maneiras diferentes de exportar um modelo:

- Você pode exportar o modelo real que foi usado para uma implantação. O modelo exportado inclui todas as variáveis e parâmetros exatamente como foram definidos no modelo original. Essa abordagem é particularmente útil quando você implantou recursos por meio do portal e, agora, deseja ver como construir o modelo para criar esses recursos.
- Você pode exportar um modelo que representa o estado atual do grupo de recursos. O modelo exportado não é baseado em nenhum modelo usado para a implantação. Ao contrário, ele cria um modelo que é um instantâneo do grupo de recursos. O modelo exportado terá muitos valores embutidos e provavelmente menos parâmetros do que você normalmente definiria. Essa abordagem é útil quando você modificou o grupo de recursos por meio do portal ou scripts e, agora, precisa capturar o grupo de recursos como um modelo.

As duas abordagens são mostradas neste tópico. No artigo [Personalizar um modelo do Azure Resource Manager exportado](resource-manager-customize-template.md), você verá como aproveitar um modelo que foi gerado a partir do estado atual do grupo de recursos e torná-lo mais útil para reimplantar a solução.

Neste tutorial, você entrará no portal do Azure, criará uma conta de armazenamento e exportará o modelo para essa conta de armazenamento. Você adicionará uma rede virtual para modificar o grupo de recursos. Por fim, exportará um novo modelo que representa seu estado atual. Embora este artigo foque em uma infraestrutura simplificada, você poderia usar essas mesmas etapas para exportar um modelo para uma solução mais complicada.

## Criar uma conta de armazenamento

1. No [portal do Azure](https://portal.azure.com), selecione **Novo** > **Dados + Armazenamento** > **Conta de armazenamento**.

      ![criar armazenamento](./media/resource-manager-export-template/create-storage.png)

2. Crie uma conta de armazenamento com o nome **armazenamento**, suas iniciais e a data. O nome da conta de armazenamento deve ser exclusivo no Azure. Se você tentar inicialmente um nome que já está em uso, experimente uma variação. Para o grupo de recursos, use **ExportGroup**. Você pode usar valores padrão para as outras propriedades. Selecione **Criar**.

      ![fornecer valores para o armazenamento](./media/resource-manager-export-template/provide-storage-values.png)

Depois da implantação terminar, sua assinatura conterá a conta de armazenamento.

## Exportar o modelo a partir do histórico de implantações

1. Vá para a folha do grupo de recursos do novo grupo de recursos. Você notará que o resultado da última implantação está listado. Selecione este link.

      ![folha do grupo de recursos](./media/resource-manager-export-template/resource-group-blade.png)

2. Você verá um histórico das implantações do grupo. Em seu caso, provavelmente há apenas uma implantação listada. Selecione essa implantação.

     ![última implantação](./media/resource-manager-export-template/last-deployment.png)

3. Será exibido um resumo da implantação. O resumo inclui o status da implantação, suas operações e os valores que você forneceu para os parâmetros. Para ver o modelo usado para a implantação, selecione **Exibir modelo**.

     ![exibir resumo da implantação](./media/resource-manager-export-template/deployment-summary.png)

4. O Resource Manager recupera os cinco arquivos a seguir para você:

   1. **Modelo** - O modelo que define a infraestrutura de sua solução. Quando você criou a conta de armazenamento por meio do portal, o Gerenciador de Recursos usou um modelo para implantá-la e salvou esse modelo para uma futura referência.
   2. **Parâmetros** - Um arquivo de parâmetro que você pode usar para passar valores durante a implantação. Ele contém os valores fornecidos durante a primeira implantação, mas você pode alterar qualquer um desses valores ao reimplantar o modelo.
   3. **CLI** - Um arquivo de script CLI (interface da linha comando) do Azure que você pode usar para implantar o modelo.
   4. **PowerShell** - Um arquivo de script do Azure PowerShell que você pode usar para implantar o modelo.
   5. **.NET** - Uma classe .NET que você pode usar para implantar o modelo.

     Os arquivos estão disponíveis por meio de links na folha. Por padrão, o modelo é selecionado.

       ![exibir modelo](./media/resource-manager-export-template/view-template.png)

     Vamos prestar atenção particular no modelo. O modelo deve ser semelhante a:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "name": {
              "type": "String"
            },
            "accountType": {
              "type": "String"
            },
            "location": {
              "type": "String"
            },
            "encryptionEnabled": {
              "defaultValue": false,
              "type": "Bool"
            }
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "sku": {
                "name": "[parameters('accountType')]"
              },
              "kind": "Storage",
              "name": "[parameters('name')]",
              "apiVersion": "2016-01-01",
              "location": "[parameters('location')]",
              "properties": {
                "encryption": {
                  "services": {
                    "blob": {
                      "enabled": "[parameters('encryptionEnabled')]"
                    }
                  },
                  "keySource": "Microsoft.Storage"
                }
              }
            }
          ]
        }
 
Este é o modelo real usado para criar sua conta de armazenamento. Observe que ele contém parâmetros que permitem implantar diferentes tipos de contas de armazenamento. Para saber mais sobre a estrutura de um modelo, consulte [Criando modelos do Azure Resource Manager](resource-group-authoring-templates.md). Para obter a lista completa de funções que você pode usar em um modelo, consulte [Funções do modelo do Azure Resource Manager](resource-group-template-functions.md).


## Adicionar uma rede virtual

O modelo que você baixou na seção anterior representava a infraestrutura dessa implantação original, mas ele não levará em conta nenhuma alteração feita após a implantação. Para ilustrar esse problema, modificaremos o grupo de recursos adicionando uma rede virtual por meio do portal.

1. Na folha do grupo de recursos, selecione **Adicionar**.

      ![adicionar recurso](./media/resource-manager-export-template/add-resource.png)

2. Selecione **Rede virtual** nos recursos disponíveis.

      ![selecionar uma rede virtual](./media/resource-manager-export-template/select-vnet.png)

2. Nomeie sua rede virtual como **VNET** e use os valores padrão para as outras propriedades. Selecione **Criar**.

      ![definir alerta](./media/resource-manager-export-template/create-vnet.png)

3. Depois da rede virtual ser implantada com êxito para o grupo de recursos, veja novamente o histórico de implantações. Agora, você verá duas implantações. Se você não vir a segunda implantação, precisará fechar a folha do grupo de recursos e abri-la novamente. Selecione a implantação mais recente.

      ![histórico de implantações](./media/resource-manager-export-template/deployment-history.png)

4. Veja o modelo dessa implantação. Observe que ele define apenas as alterações feitas para adicionar a rede virtual.

Geralmente, é melhor trabalhar com um modelo que implanta toda a infraestrutura para sua solução em uma única operação, em vez de lembrar de vários modelos diferentes para implantar.


## Exportar o modelo do grupo de recursos

Embora cada implantação mostre apenas as alterações feitas em seu grupo de recursos, a qualquer momento você pode exportar um modelo para exibir os atributos de todo o seu grupo de recursos.

1. Para exibir o modelo de um grupo de recursos, selecione **Exportar modelo**.

      ![exportar grupo de recursos](./media/resource-manager-export-template/export-resource-group.png)

     Nem todos os tipos de recursos suportam a função para exportar o modelo. Se o grupo de recursos contiver somente a conta de armazenamento e a rede virtual mostradas neste artigo, você não verá um erro. No entanto, se você criou outros tipos de recursos, verá um erro indicando que há um problema com a exportação. Você aprenderá a lidar com esses problemas na seção [Corrigir os problemas da exportação](#fix-export-issues).

      

2. Novamente, você verá os cinco arquivos que pode usar para reimplantar a solução, mas desta vez, o modelo é um pouco diferente. Esse modelo tem somente dois parâmetros: um para o nome da conta de armazenamento e outro para o nome da rede virtual.

        "parameters": {
          "virtualNetworks_VNET_name": {
            "defaultValue": "VNET",
            "type": "String"
          },
          "storageAccounts_storagetf05092016_name": {
            "defaultValue": "storagetf05092016",
            "type": "String"
          }
        },

     O Gerenciador de Recursos não recuperou os modelos usados durante a implantação. Em vez disso, ele gerou um novo modelo com base na configuração atual dos recursos. Por exemplo, o local da conta de armazenamento e o valor da replicação são definidos para:

        "location": "northeurope",
        "tags": {},
        "properties": {
            "accountType": "Standard_RAGRS"
        },

3. Baixe o modelo para que você possa trabalhar nele localmente.

      ![baixar modelo](./media/resource-manager-export-template/download-template.png)

4. Localize o arquivo .zip que você baixou e extraia o conteúdo. Você pode usar esse modelo baixado para reimplantar sua infraestrutura.

## Corrigir os problemas da exportação

Nem todos os tipos de recursos suportam a função para exportar o modelo. Alguns tipos de recurso não são exportados especificamente para evitar a exposição de dados confidenciais. Por exemplo, se você tiver uma cadeia de conexão na configuração de seu site, provavelmente não desejará que ela seja exibida explicitamente em um modelo exportado. Você pode resolver esse problema adicionando manualmente os recursos que faltam de volta ao seu modelo.

> [AZURE.NOTE] Você só encontrará problemas de exportação ao exportar de um grupo de recursos, em vez de seu histórico de implantações. Se sua última implantação apresentar com precisão o estado atual do grupo de recursos, você deverá exportar o modelo do histórico de implantações, em vez do grupo de recursos. Exporte somente a partir de um grupo de recursos quando você fez alterações no grupo de recursos que não são definidas em um único modelo.

Por exemplo, se você exportar um modelo para um grupo de recursos que contém um aplicativo Web, Banco de Dados SQL e uma cadeia de conexão na configuração do site, verá a seguinte mensagem de erro.

![mostrar erros](./media/resource-manager-export-template/show-error.png)

Selecionar a mensagem mostrará exatamente quais tipos de recursos não foram exportados.
     
![mostrar erros](./media/resource-manager-export-template/show-error-details.png)

Algumas correções comuns são mostradas abaixo. Para implementar esses recursos, você precisará adicionar parâmetros ao modelo. Para obter mais informações, consulte [Personalizar e reimplantar o modelo exportado](resource-manager-customize-template.md).

### Cadeia de conexão

No recurso dos sites da Web, adicione uma definição da cadeia de conexão ao banco de dados:

```
{
  "type": "Microsoft.Web/sites",
  ...
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "type": "config",
      "name": "connectionstrings",
      "dependsOn": [
          "[concat('Microsoft.Web/Sites/', parameters('<site-name>'))]"
      ],
      "properties": {
          "DefaultConnection": {
            "value": "[concat('Data Source=tcp:', reference(concat('Microsoft.Sql/servers/', parameters('<database-server-name>'))).fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('<database-name>'), ';User Id=', parameters('<admin-login>'), '@', parameters('<database-server-name>'), ';Password=', parameters('<admin-password>'), ';')]",
              "type": "SQLServer"
          }
      }
    }
  ]
}
```    

### Extensão do site da Web

No recurso do site da Web, adicione uma definição para o código a instalar:

```
{
  "type": "Microsoft.Web/sites",
  ...
  "resources": [
    {
      "name": "MSDeploy",
      "type": "extensions",
      "location": "[resourceGroup().location]",
      "apiVersion": "2015-08-01",
      "dependsOn": [
        "[concat('Microsoft.Web/sites/', parameters('<site-name>'))]"
      ],
      "properties": {
        "packageUri": "[concat(parameters('<artifacts-location>'), '/', parameters('<package-folder>'), '/', parameters('<package-file-name>'), parameters('<sas-token>'))]",
        "dbType": "None",
        "connectionString": "",
        "setParameters": {
          "IIS Web Application Name": "[parameters('<site-name>')]"
        }
      }
    }
  ]
}
```

### Extensão da máquina virtual

Para obter exemplos das extensões da máquina virtual, consulte [Exemplos de Configuração da Extensão da VM do Windows do Azure](./virtual-machines/virtual-machines-windows-extensions-configuration-samples.md).

### Gateway de rede virtual

Adicione um tipo de recurso do gateway de rede virtual.

```
{
  "type": "Microsoft.Network/virtualNetworkGateways",
  "name": "[parameters('<gateway-name>')]",
  "apiVersion": "2015-06-15",
  "location": "[resourceGroup().location]",
  "properties": {
    "gatewayType": "[parameters('<gateway-type>')]",
    "ipConfigurations": [
      {
        "name": "default",
        "properties": {
          "privateIPAllocationMethod": "Dynamic",
          "subnet": {
            "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('<vnet-name>'), parameters('<new-subnet-name>'))]"
          },
          "publicIpAddress": {
            "id": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('<new-public-ip-address-Name>'))]"
          }
        }
      }
    ],
    "enableBgp": false,
    "vpnType": "[parameters('<vpn-type>')]"
  },
  "dependsOn": [
    "Microsoft.Network/virtualNetworks/codegroup4/subnets/GatewaySubnet",
    "[concat('Microsoft.Network/publicIPAddresses/', parameters('<new-public-ip-address-Name>'))]"
  ]
},
```

### Gateway de rede local

Adicione um tipo de recurso do gateway de rede local.

```
{
    "type": "Microsoft.Network/localNetworkGateways",
    "name": "[parameters('<local-network-gateway-name>')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
      "localNetworkAddressSpace": {
        "addressPrefixes": "[parameters('<address-prefixes>')]"
      }
    }
}
```

### Conexão

Adicione um tipo de recurso de conexão.

```
{
    "apiVersion": "2015-06-15",
    "name": "[parameters('<connection-name>')]",
    "type": "Microsoft.Network/connections",
    "location": "[resourceGroup().location]",
    "properties": {
        "virtualNetworkGateway1": {
        "id": "[resourceId('Microsoft.Network/virtualNetworkGateways', parameters('<gateway-name>'))]"
      },
      "localNetworkGateway2": {
        "id": "[resourceId('Microsoft.Network/localNetworkGateways', parameters('<local-gateway-name>'))]"
      },
      "connectionType": "IPsec",
      "routingWeight": 10,
      "sharedKey": "[parameters('<shared-key>')]"
    }
},
```


## Próximas etapas

Parabéns! Você aprendeu a exportar um modelo a partir dos recursos criados no portal.

- Na segunda parte deste tutorial, você irá personalizar o modelo que acabou de baixar adicionando mais parâmetros e irá reimplantá-lo por meio de um script. Consulte [Personalizar e reimplantar o modelo exportado](resource-manager-customize-template.md).
- Para ver como exportar um modelo por meio do PowerShell, consulte [Usando o Azure PowerShell com o Azure Resource Manager](powershell-azure-resource-manager.md).
- Para ver como exportar um modelo por meio da CLI do Azure, consulte [Usar a CLI do Azure para Mac, Linux e Windows com o Azure Resource Manager](xplat-cli-azure-resource-manager.md).

<!---HONumber=AcomDC_0713_2016-->