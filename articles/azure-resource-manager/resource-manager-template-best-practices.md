---
title: "Práticas recomendadas para o modelo do Resource Manager | Microsoft Docs"
description: Diretrizes para simplificar seus modelos do Azure Resource Manager.
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 31b10deb-0183-47ce-a5ba-6d0ff2ae8ab3
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 311eeee882e64bc9fb2e7c1ecb4706de351e7753
ms.openlocfilehash: c774a249d37c489ad53665ad6432122c8fdd1c32


---
# <a name="best-practices-for-creating-azure-resource-manager-templates"></a>Práticas recomendadas para criação de modelos do Azure Resource Manager
As diretrizes a seguir ajudarão você a criar modelos do Resource Manager confiáveis e fáceis de usar. Estas diretrizes são apenas sugestões, não requisitos absolutos. Seu cenário pode exigir variações dessas diretrizes.

## <a name="resource-names"></a>Nomes de recurso
Normalmente, há três tipos de nomes de recurso com os quais você trabalha:

1. Os nomes de recurso devem ser exclusivos.
2. Nomes de recurso que não precisam ser exclusivos, mas você deseja fornecer um nome que ajude a identificar o contexto.
3. Nomes de recursos que podem ser genéricos.

Para obter ajuda com o estabelecimento de uma convenção de nomenclatura, confira [Diretrizes de nomenclatura de infraestrutura](../virtual-machines/virtual-machines-windows-infrastructure-naming-guidelines.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Para obter informações sobre restrições de nome de recurso, confira [Recursos recomendados de convenções de nomenclatura do Azure](../guidance/guidance-naming-conventions.md).

### <a name="unique-resource-names"></a>Nomes de recurso exclusivos
Você deve fornecer um nome de recurso exclusivo para qualquer tipo de recurso que tenha um ponto de extremidade de acesso de dados. Entre os tipos comuns que exigem um nome exclusivo estão:

* Conta de armazenamento
* Site da Web
* SQL Server
* Cofre de chaves
* Cache Redis
* Conta do Batch
* Gerenciador de tráfego
* Serviço de pesquisa
* Cluster HDInsight

Além disso, os nomes de conta de armazenamento devem estar em letras minúsculas, ter 24 caracteres ou menos e não incluir hífens.

Se você fornecer um parâmetro para esses nomes de recursos, você deverá imaginar um nome exclusivo durante a implantação. Em vez disso, você pode criar uma variável que use a função [uniqueString()](resource-group-template-functions.md#uniquestring) para gerar um nome. Frequentemente, convém também adicionar um prefixo ou sufixo ao resultado de **uniqueString** para que você possa determinar mais facilmente o tipo de recurso apenas observando o nome. Por exemplo, você gera um nome exclusivo para uma conta de armazenamento com a variável a seguir:

    "variables": {
        "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
    }

### <a name="resource-names-for-identification"></a>Nomes de recurso para identificação
Para os tipos de recursos que você deseja nomear, mas para os quais não precisa garantir exclusividade, basta fornecer um nome que identifique o contexto e o tipo de recurso. Convém fornecer um nome descritivo que ajude você a reconhecê-lo em uma lista de nomes de recursos. Se você precisa variar o nome do recurso durante as implantações, use um parâmetro para o nome:

    "parameters": {
        "vmName": { 
            "type": "string",
            "defaultValue": "demoLinuxVM",
            "metadata": {
                "description": "The name of the VM to create."
            }
        }
    }

Se não é necessário passar um nome durante a implantação, use uma variável: 

    "variables": {
        "vmName": "demoLinuxVM"
    }

Ou use um valor codificado:

    {
      "type": "Microsoft.Compute/virtualMachines",
      "name": "demoLinuxVM",
      ...
    }

### <a name="generic-resource-names"></a>Nomes de recurso genéricos
Para os tipos de recursos que são acessados principalmente por meio de outro recurso, você pode usar um nome genérico codificado no modelo. Por exemplo, provavelmente não convém fornecer um nome personalizável para regras de firewall em um SQL Server.

    {
        "type": "firewallrules",
        "name": "AllowAllWindowsAzureIps",
        ...
    }

## <a name="parameters"></a>Parâmetros
1. Minimize os parâmetros sempre que possível. Se você pode usar uma variável ou um literal, faça-o. Forneça parâmetros apenas para:
   
   * Configurações que você deseja variar de acordo com o ambiente (como sku, tamanho ou capacidade).
   * Nomes de recursos que você deseja especificar para facilitar a identificação.
   * Valores usados com frequência para realizar outras tarefas (como nome de usuário de administrador).
   * Segredos (por exemplo, senhas)
   * O número ou matriz de valores a serem usados durante a criação de várias instâncias de um tipo de recurso.
2. Os nomes de parâmetro devem seguir **camelCasing**.
3. Forneça uma descrição nos metadados para cada parâmetro.
   
        "parameters": {
            "storageAccountType": {
                "type": "string",
                "metadata": {
                    "description": "The type of the new storage account created to store the VM disks"
                }
            }
        }
4. Defina valores padrão para parâmetros (exceto senhas e chaves SSH).
   
        "parameters": {
            "storageAccountType": {
                "type": "string",
                "defaultValue": "Standard_GRS",
                "metadata": {
                    "description": "The type of the new storage account created to store the VM disks"
                }
            }
        }
5. Use **securestring** para todas as senhas e segredos. 
   
        "parameters": {
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        }
6. Quando for possível, evite usar um parâmetro para especificar o **local**. Em vez disso, use a propriedade location do grupo de recursos. Ao usar a expressão **resourceGroup().location** para todos os seus recursos, os recursos no modelo são implantados na mesma localização que o grupo de recursos.
   
        "resources": [
          {
              "name": "[variables('storageAccountName')]",
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2016-01-01",
              "location": "[resourceGroup().location]",
              ...
          }
        ]
   
     Se um tipo de recurso tem suporte em apenas um número limitado de locais, considere a especificação de um local válido diretamente no modelo. Se você precisa usar um parâmetro location, compartilhe esse valor de parâmetro o máximo possível com os recursos que provavelmente estarão no mesmo local. Essa abordagem minimiza a necessidade de fornecer locais para cada tipo de recurso.
7. Evite usar um parâmetro ou variável para a versão de API de um tipo de recurso. Os valores e propriedades do recurso podem variar de acordo com o número de versão. O IntelliSense nos editores de código não é capaz de determinar o esquema correto quando a versão de API for definida como um parâmetro ou uma variável. Em vez disso, codifique a versão da API no modelo.

## <a name="variables"></a>Variáveis
1. Use variáveis para valores que você precisa usar mais de uma vez em um modelo. Se um valor for usado apenas uma vez, um valor embutido em código facilita a leitura do modelo.
2. Não é possível usar a função [reference](resource-group-template-functions.md#reference) na seção de variáveis. A função de referência obtém seu valor do estado de tempo de execução do recurso, mas as variáveis são resolvidas durante a análise inicial do modelo. Em vez disso, construa valores que precisem da função **referência** diretamente na seção **recursos** ou **saídas** do modelo.
3. Inclua variáveis para nomes de recursos que precisam ser exclusivos, conforme mostra os [Nomes de recurso](#resource-names).
4. Você pode agrupar variáveis em objetos complexos. Você pode fazer referência a um valor de um objeto complexo no formato **variable.subentry**. O agrupamento de variáveis ajuda você a controlar variáveis relacionadas e a melhorar a legibilidade do modelo.
   
        "variables": {
            "storage": {
                "name": "[concat(uniqueString(resourceGroup().id),'storage')]",
                "type": "Standard_LRS"
            }
        },
        "resources": [
          {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[variables('storage').name]",
              "apiVersion": "2016-01-01",
              "location": "[resourceGroup().location]",
              "sku": {
                  "name": "[variables('storage').type]"
              },
              ...
          }
        ]
   
   > [!NOTE]
   > Um objeto complexo não pode conter uma expressão que faça referência a um valor de um objeto complexo. Defina uma variável separada para essa finalidade.
   > 
   > 
   
     Para obter exemplos mais avançados de como usar objetos complexos como variáveis, consulte [Compartilhamento do estado em modelos do Azure Resource Manager](best-practices-resource-manager-state.md).

## <a name="resources"></a>Recursos
1. Especifique **comentários** para cada recurso no modelo a fim de ajudar outros colaboradores a entenderem a finalidade do recurso.
   
        "resources": [
          {
              "name": "[variables('storageAccountName')]",
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2016-01-01",
              "location": "[resourceGroup().location]",
              "comments": "This storage account is used to store the VM disks",
              ...
          }
        ]
2. Use marcas para adicionar metadados aos recursos que permitam a adição de outras informações sobre os recursos. Por exemplo, você pode adicionar metadados a um recurso para detalhamento da cobrança. Para obter mais informações, veja [Usando marcas para organizar os recursos do Azure](resource-group-using-tags.md).
3. Se você usa um **ponto de extremidade público** em seu modelo (como um ponto de extremidade público de armazenamento de blobs), **não codifique** o namespace. Use a função **reference** para recuperar o namespace dinamicamente. Essa abordagem permite que você implante o modelo em ambientes de namespace públicos diferentes, sem alterar manualmente o ponto de extremidade no modelo. Defina o apiVersion como a mesma versão que você está usando para a storageAccount em seu modelo.
   
        "osDisk": {
            "name": "osdisk",
            "vhd": {
                "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
            }
        }
   
     Se a conta de armazenamento for implantada no mesmo modelo, você não precisará especificar o namespace do provedor ao fazer referencia ao recurso. A sintaxe simplificada é:
   
        "osDisk": {
            "name": "osdisk",
            "vhd": {
                "uri": "[concat(reference(variables('storageAccountName'), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
            }
        }
   
     Se há outros valores em seu modelo configurados com um namespace público, altere esses valores para refletir a mesma função de referência. Por exemplo, a propriedade storageUri de diagnosticsProfile da máquina virtual.
   
        "diagnosticsProfile": {
            "bootDiagnostics": {
                "enabled": "true",
                "storageUri": "[reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
            }
        }
   
     Você também pode fazer **referência** a uma conta de armazenamento existente em um grupo de recursos diferente.

        "osDisk": {
            "name": "osdisk", 
            "vhd": {
                "uri":"[concat(reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts/', parameters('existingStorageAccountName')), '2016-01-01').primaryEndpoints.blob,  variables('vmStorageAccountContainerName'), '/', variables('OSDiskName'),'.vhd')]"
            }
        }

1. Atribua publicIPAddresses a uma máquina virtual somente quando for necessário para um aplicativo. Para conectar-se com finalidade administrativa, de gerenciamento ou de depuração, use inboundNatRules, virtualNetworkGateways ou um jumpbox.
   
     Para saber mais sobre como se conectar às máquinas virtuais, confira:
   
   * [VMs em execução para uma arquitetura de N camadas no Azure](../guidance/guidance-compute-n-tier-vm.md)
   * [Configurando o acesso do WinRM para as máquinas virtuais no Azure Resource Manager](../virtual-machines/virtual-machines-windows-winrm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
   * [Permitir o acesso externo à sua VM usando o Portal do Azure](../virtual-machines/virtual-machines-windows-nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
   * [Permitir o acesso externo à sua VM usando o PowerShell](../virtual-machines/virtual-machines-windows-nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
   * [Abertura de pontos de extremidade e de portas](../virtual-machines/virtual-machines-linux-nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
2. A propriedade **domainNameLabel** para publicIPAddresses deve ser exclusiva. domainNameLabel deve ter entre três e 63 caracteres e seguir as regras especificadas por essa expressão regular `^[a-z][a-z0-9-]{1,61}[a-z0-9]$`. Como a função uniqueString gera uma cadeia de caracteres com tamanho de 13 caracteres, o parâmetro dnsPrefixString é limitado a não mais de 50 caracteres.
   
        "parameters": {
            "dnsPrefixString": {
                "type": "string",
                "maxLength": 50,
                "metadata": {
                    "description": "DNS Label for the Public IP. Must be lowercase. It should match with the following regular expression: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$ or it will raise an error."
                }
            }
        },
        "variables": {
            "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
        }
3. Ao adicionar uma senha a um **customScriptExtension**, use a propriedade **commandToExecute** em protectedSettings.
   
        "properties": {
            "publisher": "Microsoft.Azure.Extensions",
            "type": "CustomScript",
            "typeHandlerVersion": "2.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "fileUris": [
                    "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
                ]
            },
            "protectedSettings": {
                "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
            }
        }
   
   > [!NOTE]
   > Para garantir que os segredos estejam criptografados quando passados como parâmetros para virtualMachines/extensions, use a propriedade protectedSettings das extensões relevantes.
   > 
   > 

## <a name="outputs"></a>Saídas
Se um modelo cria um **publicIPAddresses**, ele deve ter uma seção de **saídas** que retorna os detalhes do endereço IP e o nome de domínio totalmente qualificado. Esses valores de saída habilitam a fácil recuperação desses detalhes após a implantação. Ao fazer referência ao recurso, use a versão de API que foi usada para criá-lo. 

```
"outputs": {
    "fqdn": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName')), '2016-07-01').dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName')), '2016-07-01').ipAddress]",
        "type": "string"
    }
}
```

## <a name="single-template-or-nested-templates"></a>Modelo único ou modelos aninhados
Para implantar sua solução, você pode usar um único modelo, ou um modelo principal com vários modelos aninhados. Os modelos aninhados são comuns em cenários mais avançados. Modelos aninhados apresentam as seguintes vantagens:

1. Podem decompor a solução em componentes de destino
2. Podem reutilizar os modelos aninhados com diferentes modelos principais

Quando você decidir decompor o design do modelo em vários modelos aninhados, as diretrizes a seguir ajudam a padronizar o design. Essas diretrizes têm base na documentação [padrões para criação de modelos do Azure Resource Manager](best-practices-resource-manager-design-templates.md) . O design recomendado é composto pelos modelos a seguir:

* **Modelo principal** (azuredeploy.json). Usado para os parâmetros de entrada.
* **Modelo de recursos compartilhados**. Implanta os recursos compartilhados usados por todos os outros recursos (por exemplo, rede virtual, conjuntos de disponibilidade). A expressão dependsOn impõe que esse modelo seja implantado antes dos outros modelos.
* **Modelo de recursos opcionais**. Implanta condicionalmente os recursos com base em um parâmetro (por exemplo, um jumpbox)
* **Modelo de recursos de membro**. Cada tipo de instância dentro de uma camada de aplicativo tem sua própria configuração. Dentro de uma camada, diferentes tipos de instância podem ser definidos (por exemplo, a primeira instância cria um cluster, outras instâncias são adicionadas ao cluster existente). Cada tipo de instância tem seu próprio modelo de implantação.
* **Scripts**. Scripts amplamente reutilizáveis são aplicáveis a cada tipo de instância (por exemplo, inicializar e formatar discos adicionais). Scripts personalizados criados para fins de personalização específicos são diferentes de acordo com o tipo de instância.

![modelo aninhado](./media/resource-manager-template-best-practices/nestedTemplateDesign.png)

Para saber mais, confira [Usando modelos vinculados com o Gerenciador de Recursos do Azure](resource-group-linked-templates.md).

## <a name="conditionally-link-to-nested-template"></a>Link condicional para modelo aninhado
Você pode criar links condicionais para modelos aninhados usando um parâmetro que se torna parte do URI do modelo.

    "parameters": {
        "newOrExisting": {
            "type": "String",
            "allowedValues": [
                "new",
                "existing"
            ]
        }
    },
    "variables": {
        "templatelink": "[concat('https://raw.githubusercontent.com/Contoso/Templates/master/',parameters('newOrExisting'),'StorageAccount.json')]"
    },
    "resources": [
        {
            "apiVersion": "2015-01-01",
            "name": "nestedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "incremental",
                "templateLink": {
                    "uri": "[variables('templatelink')]",
                    "contentVersion": "1.0.0.0"
                },
                "parameters": {
                }
            }
        }
    ]

## <a name="template-format"></a>Formato de modelo
1. É uma prática recomendada passar o modelo por um validador de JSON a fim de remover vírgulas, parênteses e colchetes estranhos que podem causar um erro durante a implantação. Experimente o [JSONlint](http://jsonlint.com/) ou um pacote linter para seu ambiente de edição favorito (Visual Studio Code, Atom, Sublime Text, Visual Studio etc.)
2. Também convém formatar o JSON para melhorar a legibilidade. Você pode usar um pacote de formatador JSON para seu editor local. No Visual Studio, formate o documento com **Ctrl+K, Ctrl+D**. No VS Code, use **Alt+Shift+F**. Se o seu editor local não formata o documento, use um [formatador online](https://www.bing.com/search?q=json+formatter).

## <a name="next-steps"></a>Próximas etapas
* Para obter diretrizes sobre a arquitetura de sua solução para máquinas virtuais, consulte [Execução de uma VM do Windows no Azure](../guidance/guidance-compute-single-vm.md) e [Execução de uma VM do Linux no Azure](../guidance/guidance-compute-single-vm-linux.md).
* Para obter orientação sobre a configuração de uma conta de armazenamento, confira [Lista de verificação de escalabilidade e desempenho do Armazenamento do Microsoft Azure](../storage/storage-performance-checklist.md).
* Para obter ajuda com redes virtuais, confira [Diretrizes de infraestrutura de rede](../virtual-machines/virtual-machines-windows-infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Para obter orientação sobre como as empresas podem usar o Resource Manager para gerenciar assinaturas de forma eficaz, consulte [Azure enterprise scaffold – controle de assinatura prescritivas](resource-manager-subscription-governance.md).




<!--HONumber=Dec16_HO2-->


