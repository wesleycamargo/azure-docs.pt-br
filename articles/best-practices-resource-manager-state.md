<properties
	pageTitle="Tratando do estado em modelos do Resource Manager | Microsoft Azure"
	description="Mostra abordagens recomendadas para usar objetos complexos para compartilhar dados de estado com modelos e modelos vinculados do Gerenciador de Recursos do Azure"
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
	ms.topic="article"
	ms.date="04/06/2016"
	ms.author="tomfitz"/>

# Compartilhando estado em modelos do Gerenciador de Recursos do Azure

Este tópico mostra as melhores práticas para gerenciar e compartilhar o estado em modelos. Os parâmetros e variáveis mostrados neste tópico são exemplos dos tipos de objetos que você pode definir para organizar seus requisitos de implantação convenientemente. A partir desses exemplos, você pode implementar seus próprios objetos com valores de propriedade que façam sentido para o seu ambiente.

Este tópico faz parte de um whitepaper mais amplo. Para ler o documento completo, baixe [Considerações e práticas comprovadas de modelos ARM de nível mundial](http://download.microsoft.com/download/8/E/1/8E1DBEFA-CECE-4DC9-A813-93520A5D7CFE/World Class ARM Templates - Considerations and Proven Practices.pdf).


## Fornecer configurações padrão

Em vez de oferecer um modelo com total flexibilidade e inúmeras variações, um padrão comum é fornecer a capacidade de selecionar configurações conhecidas; na verdade, tamanhos de camiseta padrão como tamanho único, pequeno, médio e grande. Outros exemplos de tamanhos de camiseta são ofertas de produtos, como community edition ou enterprise edition. Em outros casos, podem se tratar de configurações de uma tecnologia específicas para uma determinada carga de trabalho - como mapear/reduzir ou no SQL.

Com objetos complexos, você pode criar variáveis que contenham coleções de dados, às vezes conhecidos como "conjunto de propriedades", e usar esses dados para orientar a declaração de recurso em seu modelo. Essa abordagem fornece boas configurações conhecidas, de tamanhos variados que são pré-configurados para os clientes. Sem configurações conhecidas, os clientes finais devem determinar o tamanho do cluster por conta própria, levar em consideração restrições de recursos de plataforma e fazer cálculos para identificar o particionamento resultante de contas de armazenamento e outros recursos (devido a restrições de recursos e de tamanho do cluster). É mais fácil dar suporte a um pequeno número de configurações conhecidas; além disso, o uso de um pequeno número delas resulta em uma melhor experiência para o cliente e pode ajudá-lo a oferecer um maior nível de densidade.

O exemplo a seguir mostra como definir variáveis que contêm objetos complexos para representar coleções de dados. As coleções definem os valores que são usados para o tamanho da máquina virtual, as configurações de rede, as configurações de sistema operacional e as configurações de disponibilidade.

    "variables": {
      "tshirtSize": "[variables(concat('tshirtSize', parameters('tshirtSize')))]",
      "tshirtSizeSmall": {
        "vmSize": "Standard_A1",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
        "vmCount": 2,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 1,
          "pool": "db",
          "map": [0,0],
          "jumpbox": 0
        }
      },
      "tshirtSizeMedium": {
        "vmSize": "Standard_A3",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-8disk-resources.json')]",
        "vmCount": 2,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 2,
          "pool": "db",
          "map": [0,1],
          "jumpbox": 0
        }
      },
      "tshirtSizeLarge": {
        "vmSize": "Standard_A4",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-16disk-resources.json')]",
        "vmCount": 3,
        "slaveCount": 2,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 2,
          "pool": "db",
          "map": [0,1,1],
          "jumpbox": 0
        }
      },
      "osSettings": {
        "scripts": [
          "[concat(variables('templateBaseUrl'), 'install_postgresql.sh')]",
          "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
        ],
        "imageReference": {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "14.04.2-LTS",
          "version": "latest"
        }
      },
      "networkSettings": {
        "vnetName": "[parameters('virtualNetworkName')]",
        "addressPrefix": "10.0.0.0/16",
        "subnets": {
          "dmz": {
            "name": "dmz",
            "prefix": "10.0.0.0/24",
            "vnet": "[parameters('virtualNetworkName')]"
          },
          "data": {
            "name": "data",
            "prefix": "10.0.1.0/24",
            "vnet": "[parameters('virtualNetworkName')]"
          }
        }
      },
      "availabilitySetSettings": {
        "name": "pgsqlAvailabilitySet",
        "fdCount": 3,
        "udCount": 5
      }
    }

Observe que a variável **tshirtSize** concatena o tamanho de camiseta que você forneceu através de um parâmetro (**Small**, **Medium**, **Large**) ao texto **tshirtSize**. Você usará essa variável para recuperar a variável de objeto complexo associada para o tamanho de camiseta.

Você pode fazer referência a essas variáveis posteriormente no modelo. A capacidade de fazer referência a variáveis nomeadas e às suas propriedades simplifica a sintaxe de modelo e facilita o entendimento do contexto. O exemplo a seguir define um recurso para implantar usando os objetos mostrados acima para definir valores. Por exemplo, observe que o tamanho da VM é definido pela recuperação do valor de `variables('tshirtSize').vmSize`, enquanto o valor do tamanho do disco é recuperado de `variables('tshirtSize').diskSize`. Além disso, o URI de um modelo vinculado é definido com o valor de `variables('tshirtSize').vmTemplate`.

    "name": "master-node",
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2015-01-01",
    "dependsOn": [
        "[concat('Microsoft.Resources/deployments/', 'shared')]"
    ],
    "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[variables('tshirtSize').vmTemplate]",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "value": "[parameters('adminPassword')]"
          },
          "replicatorPassword": {
            "value": "[parameters('replicatorPassword')]"
          },
          "osSettings": {
            "value": "[variables('osSettings')]"
          },
          "subnet": {
            "value": "[variables('networkSettings').subnets.data]"
          },
          "commonSettings": {
            "value": {
              "region": "[parameters('region')]",
              "adminUsername": "[parameters('adminUsername')]",
              "namespace": "ms"
            }
          },
          "storageSettings": {
            "value":"[variables('tshirtSize').storage]"
          },
          "machineSettings": {
            "value": {
              "vmSize": "[variables('tshirtSize').vmSize]",
              "diskSize": "[variables('tshirtSize').diskSize]",
              "vmCount": 1,
              "availabilitySet": "[variables('availabilitySetSettings').name]"
            }
          },
          "masterIpAddress": {
            "value": "0"
          },
          "dbType": {
            "value": "MASTER"
          }
        }
      }
    }

## Passar o estado para um modelo

Você pode compartilhar o estado em um modelo por meio de parâmetros fornecidos diretamente durante a implantação.

A tabela a seguir lista os parâmetros normalmente usados em modelos.

Nome | Valor | Descrição
---- | ----- | -----------
location | Cadeia de caracteres de uma lista restrita de regiões do Azure | O local onde os recursos serão implantados.
storageAccountNamePrefix | Cadeia de caracteres | Nome DNS exclusivo da conta de armazenamento na qual serão colocados os discos da VM
domainName | Cadeia de caracteres | Nome de domínio da VM jumpbox publicamente acessível no formato: **{domainName}.{local}.cloudapp.com** Por exemplo: **nomedomeudomínio.westus.cloudapp.azure.com**
adminUsername | Cadeia de caracteres | Nome de usuário das VMs
adminPassword | Cadeia de caracteres | A senha das VMs
tshirtSize | Cadeia de caracteres de uma lista restrita de tamanhos de camiseta oferecidos | O tamanho da unidade de escala nomeada para provisionamento. Por exemplo, "Pequeno", "Médio", "Grande"
virtualNetworkName | Cadeia de caracteres | Nome da rede virtual que o consumidor deseja usar.
enableJumpbox | Cadeia de caracteres de uma lista restrita (habilitada/desabilitada) | Parâmetro que identifica se um Jumpbox será habilitado para o ambiente. Valores: "habilitado", "desabilitado"

O parâmetro **tshirtSize** usado na seção anterior é definido como:

    "parameters": {
      "tshirtSize": {
        "type": "string",
        "defaultValue": "Small",
        "allowedValues": [
          "Small",
          "Medium",
          "Large"
        ],
        "metadata": {
          "Description": "T-shirt size of the MongoDB deployment"
        }
      }
    }


## Passar o estado para modelos vinculados

Ao conectar-se aos modelos vinculados, geralmente você utilizará uma combinação de variáveis estáticas e geradas.

### Variáveis estáticas

Variáveis estáticas geralmente são usadas para fornecer valores de base, como URLs, que são usados em um modelo.

No trecho do modelo abaixo, `templateBaseUrl` especifica o local raiz do modelo no GitHub. A próxima linha cria uma nova variável `sharedTemplateUrl`, que concatena o URL base com o nome conhecido do modelo de recursos compartilhados. Abaixo dela, uma variável de objeto complexo é usada para armazenar um tamanho de camiseta, em que o URL base é concatenado ao local do modelo de configuração conhecido e armazenado na propriedade `vmTemplate`.

O benefício desta abordagem é que, se o local do modelo for alterado, você só precisará alterar a variável estática em um único lugar, que a passará para os modelos vinculados.

    "variables": {
      "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
      "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
      "tshirtSizeSmall": {
        "vmSize": "Standard_A1",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
        "vmCount": 2,
        "slaveCount": 1,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 1,
          "pool": "db",
          "map": [0,0],
          "jumpbox": 0
        }
      }
    }

### Variáveis geradas

Além de variáveis estáticas, diversas variáveis são geradas dinamicamente. Esta seção identifica alguns dos tipos comuns de variáveis geradas.

#### tshirtSize

Você está familiarizado com essa variável gerada dos exemplos acima.

#### networkSettings

Em um modelo de capacidade, recurso ou solução de escopo completa, os modelos vinculados normalmente criam recursos que existem em uma rede. Uma abordagem simples é usar um objeto complexo para armazenar as configurações de rede e passá-las para modelos vinculados.

Um exemplo de configurações de rede de comunicação pode ser visto abaixo.

    "networkSettings": {
      "vnetName": "[parameters('virtualNetworkName')]",
      "addressPrefix": "10.0.0.0/16",
      "subnets": {
        "dmz": {
          "name": "dmz",
          "prefix": "10.0.0.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        },
        "data": {
          "name": "data",
          "prefix": "10.0.1.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        }
      }
    }

#### availabilitySettings

Recursos criados em modelos vinculados geralmente são colocados em um conjunto de disponibilidade. No exemplo a seguir, o nome do conjunto de disponibilidade é especificado e também a contagem de domínios de falha e de atualização para uso.

    "availabilitySetSettings": {
      "name": "pgsqlAvailabilitySet",
      "fdCount": 3,
      "udCount": 5
    }

Se precisar de vários conjuntos de disponibilidade (por exemplo, um para nós mestres e outro para nós de dados), você pode usar um nome como um prefixo, especificar vários conjuntos de disponibilidade ou seguir o modelo mostrado anteriormente para a criação de uma variável para um tamanho de camiseta específico.

#### storageSettings

Detalhes de armazenamento geralmente são compartilhados com modelos vinculados. No exemplo abaixo, um objeto *storageSettings* fornece detalhes sobre os nomes de contêiner e da conta de armazenamento.

    "storageSettings": {
        "vhdStorageAccountName": "[parameters('storageAccountName')]",
        "vhdContainerName": "[variables('vmStorageAccountContainerName')]",
        "destinationVhdsContainer": "[concat('https://', parameters('storageAccountName'), variables('vmStorageAccountDomain'), '/', variables('vmStorageAccountContainerName'), '/')]"
    }

#### osSettings

Com modelos vinculados, você precisa passar configurações do sistema operacional para vários tipos de nós em todos os tipos de configuração diferentes conhecidos. Um objeto complexo é uma maneira fácil de armazenar e compartilhar informações de sistema operacional e também torna mais fácil dar suporte a várias opções de sistema operacional para implantação.

O exemplo a seguir mostra um objeto para *osSettings*:

    "osSettings": {
      "imageReference": {
        "publisher": "Canonical",
        "offer": "UbuntuServer",
        "sku": "14.04.2-LTS",
        "version": "latest"
      }
    }

#### machineSettings

Uma variável gerada, *machineSettings*, é um objeto complexo que contém uma mistura de variáveis principais para a criação de uma nova VM: o nome de usuário e a senha de administrador, um prefixo para os nomes de VM e uma referência de imagem de sistema operacional, como mostrado abaixo:

    "machineSettings": {
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "machineNamePrefix": "mongodb-",
        "osImageReference": {
            "publisher": "[variables('osFamilySpec').imagePublisher]",
            "offer": "[variables('osFamilySpec').imageOffer]",
            "sku": "[variables('osFamilySpec').imageSKU]",
            "version": "latest"
        }
    },

Observe que *osImageReference* recupera os valores da variável *osSettings* definida no modelo principal. Isso significa que você pode alterar facilmente o sistema operacional de uma VM — completamente ou com base na preferência de um consumidor de modelo.

#### vmScripts

O objeto *vmScripts* contém detalhes sobre os scripts a serem baixados e executados em uma instância de VM, incluindo referências externas e internas. Referências externas incluem a infraestrutura. Referências internas incluem o software instalado e a configuração.

Você usa a propriedade *scriptsToDownload* para listar os scripts a serem baixados na VM.

Como o exemplo a seguir mostra, esse objeto também contém referências aos argumentos de linha de comando de diferentes tipos de ações. Essas ações incluem a execução da instalação padrão para cada nó individual, uma instalação que é executada depois que todos os nós são implantados e quaisquer scripts adicionais que possam ser específicos de um determinado modelo.

Este exemplo é de um modelo usado para implantar MongoDB, que requer um arbitrador para oferecer alta disponibilidade. O *arbiterNodeInstallCommand* foi adicionado a *vmScripts* para instalar o arbitrador.

A seção de variáveis é onde você encontrará as variáveis que definem o texto específico para executar o script com os valores adequados.

    "vmScripts": {
        "scriptsToDownload": [
            "[concat(variables('scriptUrl'), 'mongodb-', variables('osFamilySpec').osName, '-install.sh')]",
            "[concat(variables('sharedScriptUrl'), 'vm-disk-utils-0.1.sh')]"
        ],
        "regularNodeInstallCommand": "[variables('installCommand')]",
        "lastNodeInstallCommand": "[concat(variables('installCommand'), ' -l')]",
        "arbiterNodeInstallCommand": "[concat(variables('installCommand'), ' -a')]"
    },


## Retornar o estado de um modelo

Você pode não só passar dados para um modelo, mas também pode compartilhar dados de volta para o modelo de chamada. Na seção **outputs** de um modelo vinculado, você pode fornecer os pares de chave-valor que podem ser consumidos pelo modelo de origem.

O exemplo a seguir mostra como passar o endereço IP privado gerado em um modelo vinculado.

    "outputs": {
        "masterip": {
            "value": "[reference(concat(variables('nicName'),0)).ipConfigurations[0].properties.privateIPAddress]",
            "type": "string"
         }
    }

No modelo principal, você pode usar esses dados com a seguinte sintaxe:

    "[reference('master-node').outputs.masterip.value]"

Você pode usar essa expressão na seção de saídas ou na seção de recursos do modelo principal. Não é possível usar a expressão na seção de variáveis porque ela se baseia no estado de tempo de execução. Para retornar esse valor do modelo principal, use:

    "outputs": { 
      "masterIpAddress": {
        "value": "[reference('master-node').outputs.masterip.value]",
        "type": "string"
      }
     
Para obter um exemplo de como usar a seção de saídas de um modelo vinculado para retornar discos de dados para uma máquina virtual, consulte [Criando vários discos de dados para uma Máquina Virtual](./resource-group-create-multiple/#creating-multiple-data-disks-for-a-virtual-machine).

## Definir configurações de autenticação para uma máquina virtual

Você pode usar o mesmo padrão mostrado acima para definições de configuração, com o fim de especificar as configurações de autenticação para uma máquina virtual. Você cria um parâmetro para passar o tipo de autenticação.

    "parameters": {
      "authenticationType": {
        "allowedValues": [
          "password",
          "sshPublicKey"
        ],
        "defaultValue": "password",
        "metadata": {
          "description": "Authentication type"
        },
        "type": "string"
      }
    }

Adicione variáveis para os tipos de autenticação diferentes, bem como uma variável para armazenar qual tipo é usado para essa implantação com base no valor do parâmetro.

    "variables": {
      "osProfile": "[variables(concat('osProfile', parameters('authenticationType')))]",
      "osProfilepassword": {
        "adminPassword": "[parameters('adminPassword')]",
        "adminUsername": "notused",
        "computerName": "[parameters('vmName')]",
        "customData": "[base64(variables('customData'))]"
      },
      "osProfilesshPublicKey": {
        "adminUsername": "notused",
        "computerName": "[parameters('vmName')]",
        "customData": "[base64(variables('customData'))]",
        "linuxConfiguration": {
          "disablePasswordAuthentication": "true",
          "ssh": {
            "publicKeys": [
              {
                "keyData": "[parameters('sshPublicKey')]",
                "path": "/home/notused/.ssh/authorized_keys"
              }
            ]
          }
        }
      }
    }

Ao definir a máquina virtual, você define o **osProfile** para a variável que criou.

    {
      "type": "Microsoft.Compute/virtualMachines",
      ...
      "osProfile": "[variables('osProfile')]"
    }


## Próximas etapas
- Para saber mais sobre as seções do modelo, confira [Criando modelos do Azure Resource Manager](resource-group-authoring-templates.md)
- Para ver as funções que estão disponíveis em um modelo, confira [Funções do modelo do Azure Resource Manager](resource-group-template-functions.md).

<!---HONumber=AcomDC_0413_2016-->