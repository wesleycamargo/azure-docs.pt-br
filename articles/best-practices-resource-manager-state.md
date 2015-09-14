<properties
	pageTitle="Práticas recomendadas para manipulação de estado nos modelos do Gerenciador de Recursos do Azure"
	description="Mostra abordagens recomendadas para usar objetos complexos para compartilhar dados de estado com modelos e modelos vinculados do Gerenciador de Recursos do Azure"
	services="azure-resource-manager"
	documentationCenter=""
	authors="mmercuri"
	manager="georgem"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/02/2015"
	ms.author="mmercuri"/>

# Compartilhando estado em modelos do Gerenciador de Recursos do Azure

Este tópico mostra as práticas recomendadas para gerenciar e compartilhar estado dentro de um modelo do Gerenciador de Recursos do Azure entre modelos vinculados. Os parâmetros e variáveis mostrados neste tópico são exemplos dos tipos de objetos que você pode definir para organizar seus requisitos de implantação convenientemente. A partir desses exemplos, você pode implementar seus próprios objetos com valores de propriedade que façam sentido para o seu ambiente.

## Usando objetos complexos para compartilhar o estado

Além dos parâmetros de valor único, você pode usar objetos complexos como parâmetros em um modelo do Gerenciador de Recursos do Azure. Com objetos complexos, você pode implementar e fazer referência a coleções de dados para uma área específica, como o tamanho de camiseta (para descrever uma máquina virtual), as configurações de rede, as configurações de sistema operacional e as configurações de disponibilidade.

O exemplo a seguir mostra como definir variáveis que contêm objetos complexos para representar coleções de dados. As coleções definem os valores que são usados para o tamanho da máquina virtual, as configurações de rede, as configurações de sistema operacional e as configurações de disponibilidade.

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

Você pode fazer referência a essas variáveis posteriormente no modelo. A capacidade de fazer referência a variáveis nomeadas e às suas propriedades simplifica a sintaxe de modelo e facilita o entendimento do contexto. O exemplo a seguir define um recurso para implantar usando os objetos mostrados acima para definir valores. Por exemplo, observe que o tamanho da VM é definido pela recuperação do valor de `variables('tshirtSize').vmSize`, enquanto o valor para o tamanho do disco é recuperado de `variables('tshirtSize').diskSize`. Além disso, o URI de um modelo vinculado é definido com o valor de `variables('tshirtSize').vmTemplate`.

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

## Passando estado para um modelo e seus modelos vinculados

Você pode compartilhar informações de estado em um modelo e em seus modelos vinculados por meio de:

- parâmetros fornecidos diretamente para o modelo principal durante a implantação
- parâmetros, variáveis estáticas e variáveis geradas que o modelo principal compartilha com seus modelos vinculados

### Parâmetros comuns fornecidos ao modelo principal

A tabela a seguir lista os parâmetros normalmente usados em modelos.

**Parâmetros normalmente passados para o modelo principal**

Nome | Valor | Descrição
---- | ----- | -----------
location | Cadeia de caracteres de uma lista restrita de regiões do Azure | O local onde os recursos serão implantados.
storageAccountNamePrefix | Cadeia de caracteres | Nome DNS exclusivo da conta de armazenamento na qual serão colocados os discos da VM
domainName | Cadeia de caracteres | Nome de domínio da VM Jumpbox publicamente acessível no formato:**{domainName}.{local}.cloudapp.com** Por exemplo: **mydomainname.westus.cloudapp.azure.com**
adminUsername | Cadeia de caracteres | Nome de usuário das VMs
adminPassword | Cadeia de caracteres | A senha das VMs
tshirtSize | Cadeia de caracteres de uma lista restrita de tamanhos de camiseta oferecidos | O tamanho da unidade de escala nomeada para provisionamento. Por exemplo, "Pequeno", "Médio", "Grande"
virtualNetworkName | Cadeia de caracteres | Nome da rede virtual que o consumidor deseja usar.
enableJumpbox | Cadeia de caracteres de uma lista restrita (habilitada/desabilitada) | Parâmetro que identifica se um Jumpbox será habilitado para o ambiente. Valores: "habilitado", "desabilitado"

### Parâmetros enviados para modelos vinculados

Ao conectar-se aos modelos vinculados, geralmente você utilizará uma combinação de variáveis estáticas e geradas.

#### Variáveis estáticas

Variáveis estáticas geralmente são usadas para fornecer valores de base, como URLs, que são usados em um modelo ou como valores que são usados para compor valores para variáveis dinâmicas.

No trecho de modelo abaixo, *templateBaseUrl* especifica o local raiz do modelo no GitHub. A próxima linha cria uma nova variável *sharedTemplateUrl* que concatena o valor de *templateBaseUrl* com o nome conhecido do modelo de recursos compartilhados. Abaixo, uma variável de objeto complexo é usada para armazenar um tamanho de camiseta, em que *templateBaseUrl* é concatenado para especificar o local do modelo de configuração conhecido armazenado na propriedade *vmTemplate*.

O benefício dessa abordagem é que você pode facilmente mover, bifurcar ou usar o modelo como base para um novo. Se o local do modelo for alterado, você só precisa alterar a variável estática em um único lugar — o modelo principal — que o passa para os modelos.

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

#### Variáveis geradas

Além de variáveis estáticas, diversas variáveis são geradas dinamicamente. Esta seção identifica alguns dos tipos comuns de variáveis geradas.

##### tshirtSize

Ao chamar o modelo principal, você pode selecionar um tamanho de camiseta entre um número fixo de opções, que geralmente incluem valores como *Pequeno*, *Médio* e *Grande*.

No modelo principal, essa opção aparece como um parâmetro, como *tshirtSize*:

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

No modelo principal, as variáveis correspondem a cada um dos tamanhos. Por exemplo, se os tamanhos disponíveis são pequeno, médio e grande, a seção de variáveis incluiria variáveis chamadas *tshirtSizeSmall*, *tshirtSizeMedium* e *tshirtSizeLarge*.

Como mostra o exemplo a seguir, essas variáveis definem as propriedades de um determinado tamanho de camiseta. Cada uma identifica o tipo de VM, o tamanho do disco, o modelo de recursos de unidade de escala associado ao qual vincular, o número de instâncias, os detalhes da conta de armazenamento e o status de Jumpbox.

O prefixo de nome de conta de armazenamento é extraído de um parâmetro fornecido por um usuário e o modelo vinculado é a concatenação da URL base do modelo e do nome do arquivo de um modelo de recursos de unidade de escala específico.

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
      "storage": {
        "name": "[parameters('storageAccountNamePrefix')]",
        "count": 2,
        "pool": "db",
        "map": [0,1,1],
        "jumpbox": 0
      }
    }

A variável *tshirtSize* aparece mais adiante na seção das variáveis. O final do tamanho de camiseta fornecido (*Pequeno*, *Médio*, *Grande*) é concatenado com o texto *tshirtSize* para recuperar a variável de objeto complexo associada ao tamanho de camiseta:

    "tshirtSize": "[variables(concat('tshirtSize', parameters('tshirtSize')))]",

Essa variável é passada para o modelo de recursos de unidade de escala vinculado.

##### networkSettings

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

##### availabilitySettings

Recursos criados em modelos vinculados geralmente são colocados em um conjunto de disponibilidade. No exemplo a seguir, o nome do conjunto de disponibilidade é especificado e também a contagem de domínios de falha e de atualização para uso.

    "availabilitySetSettings": {
      "name": "pgsqlAvailabilitySet",
      "fdCount": 3,
      "udCount": 5
    }

Se precisar de vários conjuntos de disponibilidade (por exemplo, um para nós mestres e outro para nós de dados), você pode usar um nome como um prefixo, especificar vários conjuntos de disponibilidade ou seguir o modelo mostrado anteriormente para a criação de uma variável para um tamanho de camiseta específico.

##### storageSettings

Detalhes de armazenamento geralmente são compartilhados com modelos vinculados. No exemplo abaixo, um objeto *storageSettings* fornece detalhes sobre os nomes de contêiner e da conta de armazenamento.

    "storageSettings": {
        "vhdStorageAccountName": "[parameters('storageAccountName')]",
        "vhdContainerName": "[variables('vmStorageAccountContainerName')]",
        "destinationVhdsContainer": "[concat('https://', parameters('storageAccountName'), variables('vmStorageAccountDomain'), '/', variables('vmStorageAccountContainerName'), '/')]"
    }

##### osSettings

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

##### machineSettings

Uma variável gerada, *machineSettings* é um objeto complexo que contém uma mistura de variáveis de núcleo para criar uma nova VM: nome de usuário e senha de administrador, um prefixo para os nomes de VM e uma referência de imagem de sistema operacional conforme mostrado abaixo:

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

##### vmScripts

O objeto *vmScripts* contém detalhes sobre os scripts a serem baixados e executados em uma instância VM, incluindo referências externas e internas. Referências externas incluem a infraestrutura. Referências internas incluem o software instalado e a configuração.

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


## Retornando o estado de um modelo

Você pode não só passar dados para um modelo, mas também pode compartilhar dados de volta para o modelo de chamada. Na seção de **outputs** de um modelo vinculado, você pode fornecer os pares chave-valor que podem ser consumidos pelo modelo de origem.

O exemplo a seguir mostra como passar o endereço IP privado gerado em um modelo vinculado.

    "outputs": {
        "masterip": {
            "value": "[reference(concat(variables('nicName'),0)).ipConfigurations[0].privateIPAddress]",
            "type": "string"
         }
    }

No modelo principal, você pode usar esses dados com a seguinte sintaxe:

    "masterIpAddress": {
        "value": "[reference('master-node').outputs.masterip.value]"
    }

## Próximas etapas
- [Criação de modelos do Gerenciador de Recursos do Azure](resource-group-authoring-templates.md)
- [Funções de modelo do Gerenciador de Recursos do Azure](resource-group-template-functions.md)

<!---HONumber=September15_HO1-->