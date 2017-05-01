## <a name="overview-of-azure-resource-manager-templates"></a>Visão geral dos modelos do Gerenciador de Recursos do Azure
O modelo do Azure Resource Manager permite especificar de forma declarativa a infraestrutura IaaS do Azure na linguagem JSON definindo as dependências entre recursos. Para obter uma visão geral detalhada dos Modelos do Azure Resource Manager, consulte os artigos abaixo:

[Visão geral do Grupo de Recursos](../articles/azure-resource-manager/resource-group-overview.md)

## <a name="sample-template-snippet-for-vm-extensions"></a>Trecho de código do modelo de exemplo para extensões de VM
Implantar as extensões de VM como parte de um modelo do Azure Resource Manager exige uma especificação declarativa e a configuração da extensão no modelo.
Veja abaixo o formato para especificar a configuração da extensão.

      {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "MyExtension",
      "apiVersion": "2015-05-01-preview",
      "location": "[parameters('location')]",
      "dependsOn": ["[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"],
      "properties":
      {
      "publisher": "Publisher Namespace",
      "type": "extension Name",
      "typeHandlerVersion": "extension version",
      "settings": {
      // Extension specific configuration goes in here.
      }
      }
      }

Como você pode ver no exemplo acima, o modelo de extensão contém duas partes principais:

1. Nome da extensão, editor e versão
2. Configuração da Extensão.

## <a name="identifying-the-publisher-type-and-typehandlerversion-for-any-extension"></a>Identificando o editor, o tipo e a typeHandlerVersion de qualquer extensão
As extensões de VM do Azure são publicadas pela Microsoft e por editores terceiros confiáveis, e cada extensão é identificada exclusivamente por seu editor, tipo e typeHandlerVersion. Elas podem ser determinadas da seguinte maneira:  

