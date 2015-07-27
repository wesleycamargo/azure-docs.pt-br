<properties 
   pageTitle="Provedor de recursos de rede"
   description="Provedor de recursos de rede"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/22/2015"
   ms.author="telmos" />

# Provedor de recursos de rede
Uma necessidade básica para sucesso nos negócios de hoje é a capacidade de compilar e gerenciar aplicativos com reconhecimento de rede de grande escala de uma maneira ágil, flexível, segura e repetível. O ARM (Gerenciador de Recursos do Azure) permite que você crie aplicativos como um único conjunto de recursos em grupos de recursos. Esses recursos são gerenciados por meio de vários provedores de recursos no ARM.

O ARM (Gerenciador de Recursos do Azure) permite que você crie esses aplicativos e a coleção associada de recursos de rede como uma única coleção de recursos em um grupo de recursos. O aplicativo e os recursos de rede são executados como uma única unidade em um grupo de recursos do ARM.

Você pode gerenciar recursos de rede usando qualquer uma das seguintes interfaces de gerenciamento:

- API baseada em REST
- PowerShell
- SDK .NET
- SDK do Node.JS
- Java SDK
- CLI do Azure
- Portal do Azure
- Idioma do modelo ARM

Com a introdução de provedores de recursos de rede, você pode aproveitar os seguintes benefícios:

- **Metadados** – você pode adicionar informações aos recursos usando marcas. Essas marcas podem ser usadas para controlar a utilização de recursos em grupos de recursos e assinaturas.
- **Maior controle da sua rede** - recursos de rede são flexíveis e você pode controlá-los de forma mais granular. Isso significa que você tem mais flexibilidade no gerenciamento de recursos de rede.
- **Configuração mais rápida** - já que os recursos de rede são agrupados de modo menos rígido, você pode criar e organizar os recursos de rede em paralelo. Isso reduziu drasticamente o tempo de configuração.
- **Controle de acesso com base em função** - o RBAC fornece funções padrão, com escopo de segurança específico, além de permitir a criação de funções personalizadas para gerenciamento seguro. 
- **Gerenciamento e implantação simplificados** - é mais fácil implantar e gerenciar aplicativos, já que você pode criar uma pilha com todo o aplicativo como uma única coleção de recursos em um grupo de recursos. E isso agiliza a implantação, pois você pode implantar simplesmente fornecendo uma carga JSON de modelo.
- **Personalização rápida** - você pode usar modelos de estilo declarativo para habilitar a personalização repetível e rápida de implantações. 
- **Personalização repetível** - você pode usar modelos de estilo declarativo para habilitar a personalização repetível e rápida de implantações.

## Recursos de rede 
Agora você pode gerenciar recursos de rede de modo independente, em vez de ter todos eles gerenciados por meio de um recurso de computação único (uma máquina virtual). Isso garante um maior grau de flexibilidade e agilidade em redigir uma infraestrutura complexa e em grande escala em um grupo de recursos.
 
O diagrama a seguir ilustra uma visão geral do modelo de recursos de rede e suas associações. Recursos de nível superior são coloridos com um contorno azul. Além dos recursos de nível superior, você pode ver os recursos filho coloridos com um contorno cinza. Você pode gerenciar cada recurso individualmente.

![Modelo de recursos de rede](./media/resource-groups-networking/Figure1.png)

Uma exibição conceitual de uma implantação de exemplo envolvendo um aplicativo de várias camadas é apresentado a seguir. Todos os recursos de rede são coloridos com um contorno azul.

![Modelo de recursos de rede](./media/resource-groups-networking/Figure2.png)

## API REST 
Como mencionado anteriormente, os recursos de rede podem ser gerenciados por meio de uma variedade de interfaces, inclusive API REST, o SDK do .NET, SDK do Node.JS, Java SDK, PowerShell, CLI, Portal do Azure e modelos.

A API Rest está de acordo com a especificação do protocolo HTTP 1.1. A estrutura geral do URI da API é apresentada abaixo:

	https://management.azure.com/subscriptions/{subscription-id}/providers/{resource-provider-namespace}/locations/{region-location}/register?api-version={api-version}

E os parâmetros entre chaves representam os elementos a seguir:

- **subscription-id** - a ID de sua assinatura do Azure.
- **resource-provider-namespace** - namespace para o provedor que está sendo usado. O valor para o provedor de recursos de rede é *Microsoft.Network*.
- **region-name** - o nome da região do Azure

Os métodos HTTP a seguir têm suporte ao fazer chamadas para a API REST:

- **PUT** - usado para criar um recurso de um determinado tipo, modificar uma propriedade de recurso ou alterar uma associação entre os recursos. 
- **GET** - usado para recuperar informações de um recurso provisionado.
- **DELETE** - usado para excluir um recurso existente.

Ambas a solicitação e a resposta têm conformidade com um formato de carga JSON. Para obter mais detalhes, consulte [APIs de Gerenciamento de Recursos do Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx).

## Idioma do modelo ARM
Além de gerenciar recursos de modo imperativo (via APIs ou SDK), você também pode usar um estilo de programação declarativo para criar e gerenciar recursos de rede usando o idioma do modelo de ARM.

Uma representação de um modelo de exemplo é fornecida abaixo -

	{
	  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
	  "contentVersion": "<version-number-of-template>",
	  "parameters": { <parameter-definitions-of-template> },
	  "variables": { <variable-definitions-of-template> },
	  "resources": [ { <definition-of-resource-to-deploy> } ],
	  "outputs": { <output-of-template> }    
	}

O modelo é basicamente uma descrição de JSON os recursos e os valores de instância injetados por meio de parâmetros. O exemplo a seguir pode ser usado para criar uma rede virtual com 2 sub-redes.

	{
	    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VNET.json",
	    "contentVersion": "1.0.0.0",
	    "parameters" : {
	      "location": {
	        "type": "String",
	        "allowedValues": ["East US", "West US", "West Europe", "East Asia", "South East Asia"],
	        "metadata" : {
	          "Description" : "Deployment location"
	        }
	      },
	      "virtualNetworkName":{
	        "type" : "string",
	        "defaultValue":"myVNET",
	        "metadata" : {
	          "Description" : "VNET name"
	        }
	      },
	      "addressPrefix":{
	        "type" : "string",
	        "defaultValue" : "10.0.0.0/16",
	        "metadata" : {
	          "Description" : "Address prefix"
	        }
	
	      },
	      "subnet1Name": {
	        "type" : "string",
	        "defaultValue" : "Subnet-1",
	        "metadata" : {
	          "Description" : "Subnet 1 Name"
	        }
	      },
	      "subnet2Name": {
	        "type" : "string",
	        "defaultValue" : "Subnet-2",
	        "metadata" : {
	          "Description" : "Subnet 2 name"
	        }
	      },
	      "subnet1Prefix" : {
	        "type" : "string",
	        "defaultValue" : "10.0.0.0/24",
	        "metadata" : {
	          "Description" : "Subnet 1 Prefix"
	        }
	      },
	      "subnet2Prefix" : {
	        "type" : "string",
	        "defaultValue" : "10.0.1.0/24",
	        "metadata" : {
	          "Description" : "Subnet 2 Prefix"
	        }
	      }
	    },
	    "resources": [
	    {
	      "apiVersion": "2015-05-01-preview",
	      "type": "Microsoft.Network/virtualNetworks",
	      "name": "[parameters('virtualNetworkName')]",
	      "location": "[parameters('location')]",
	      "properties": {
	        "addressSpace": {
	          "addressPrefixes": [
	            "[parameters('addressPrefix')]"
	          ]
	        },
	        "subnets": [
	          {
	            "name": "[parameters('subnet1Name')]",
	            "properties" : {
	              "addressPrefix": "[parameters('subnet1Prefix')]"
	            }
	          },
	          {
	            "name": "[parameters('subnet2Name')]",
	            "properties" : {
	              "addressPrefix": "[parameters('subnet2Prefix')]"
	            }
	          }
	        ]
	      }
	    }
	    ]
	}

Você tem a opção de fornecer os valores de parâmetro manualmente ao usar um modelo, ou então pode usar um arquivo de parâmetro. O exemplo a seguir mostra um conjunto possível de valores de parâmetro a ser usado com o modelo acima:

	{
	  "location": {
	      "value": "East US"
	  },
	  "virtualNetworkName": {
	      "value": "VNET1"
	  },
	  "subnet1Name": {
	      "value": "Subnet1"
	  },
	  "subnet2Name": {
	      "value": "Subnet2"
	  },
	  "addressPrefix": {
	      "value": "192.168.0.0/16"
	  },
	  "subnet1Prefix": {
	      "value": "192.168.1.0/24"
	  },
	  "subnet2Prefix": {
	      "value": "192.168.2.0/24"
	  }
	}


As principais vantagens de usar modelos são:

- Você pode criar uma infraestrutura complexa em um grupo de recursos em um estilo declarativo. A orquestração de criação de recursos, incluindo gerenciamento de dependência é manipulada pela ARM. 
- A infraestrutura pode ser criada de forma repetida por várias regiões e dentro de uma região, simplesmente alterando os parâmetros. 
- O estilo declarativo resulta em menor tempo de avanço na criação de modelos e implantação da infraestrutura. 

Para exemplos de modelo, consulte [Modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates).

Para obter mais informações sobre a linguagem de modelo do ARM, consulte [Linguagem de Modelo do Gerenciador de Recursos do Azure](https://msdn.microsoft.com/library/azure/dn835138.aspx).

O modelo de exemplo acima usa a rede virtual e recursos de sub-rede. Há outros recursos de rede, que você pode usar, conforme listado abaixo:

## NIC
Placa de Interface de rede ou NIC representa uma interface de rede que pode ser associada a uma VM (máquina virtual). Uma VM pode ter uma ou mais NICs.

![NICs em uma única VM](./media/resource-groups-networking/Figure3.png)

As propriedades principais de um recurso NIC incluem:

- Configurações de IP

Uma NIC também pode ser associada aos seguintes recursos de rede:

- NSG (grupo de segurança de rede) 
- Balanceador de carga

## Sub-rede e rede virtual
VNETs (Redes virtuais) e sub-redes ajudam a definir um limite de segurança para cargas de trabalho em execução no Azure. Uma VNET é caracterizada por um espaço de endereço, também conhecido como bloco CIDR.

Uma sub-rede é um recurso filho de uma VNET e ajuda a definir segmentos de espaços de endereço em um bloco CIDR, usando prefixos de endereço IP. VMs que executam diversas cargas de trabalho essencialmente operam em um limite de sub-rede.

![NICs em uma única VM](./media/resource-groups-networking/Figure4.png)

As propriedades principais de um recurso VNET incluem:

- Espaço de endereço IP (bloco CIDR) 
- Nome da VNET
- sub-redes

Uma VNET também pode ser associada aos seguintes recursos de rede:

- Gateway de VPN

As propriedades principais de uma sub-rede incluem:

- Prefixo de endereço IP
- Nome da sub-rede

Uma sub-rede também pode ser associada aos seguintes recursos de rede:

- NSG

## Balanceador de carga
Um balanceador de carga é usado quando você deseja dimensionar seus aplicativos. Cenários comuns de implantação envolvem aplicativos executados em várias instâncias de VM. As instâncias de VM são apoiadas por um balanceador de carga, que ajuda a distribuir o tráfego de rede para as várias instâncias.

![NICs em uma única VM](./media/resource-groups-networking/Figure5.png)

Os balanceadores de carga contêm os seguintes recursos filho:

- **Configuração de IP front-end** – um balanceador de carga pode incluir um ou mais endereços IP de front-end, também conhecidos como VIPs (IPs virtuais). Esses endereços IP servem como entrada para o tráfego. 
- **Pool de endereços back-end** – estes são os endereços IP associados a NICs de VM aos quais a carga será distribuída.
- **Regras de balanceamento de carga** – uma propriedade de regra mapeia determinados IP de front-end e combinação de portas para um conjunto de endereços IP de back-end e combinação de portas. Com uma única definição de um recurso de balanceador de carga, você pode definir várias regras de balanceamento de carga, cada regra refletindo uma combinação de um IP de front-end e porta e outra combinação de IP de back-end e porta, ambas associadas a VMs. 
- **Sondas** – sondas permitem que você controle a integridade das instâncias de VMs. Se um teste de integridade falhar, a instância VM será retirada automaticamente do rodízio.
- **Regras de NAT de entrada** – regras de NAT definindo o tráfego de entrada fluindo pelo IP front-end e distribuído para o IP de back-end.

## IP público
Um recurso de endereço IP público pode fornecer tanto um endereço IP público dinâmico quanto um reservado. Um endereço IP público pode ser atribuído a um balanceador de carga, NAT ou associado a um endereço IP privado em uma NIC de uma VM.

As propriedades principais de um recurso de endereço IP público incluem:

- **Método de alocação de IP** – reservado ou dinâmico. 

## NSG (grupo de segurança de rede)
Um recurso NSG habilita a criação de limite de segurança para cargas de trabalho, por meio de regras de permissão e recusa. Essas regras podem ser aplicadas no nível do NIC (nível de instância VM) ou no nível de sub-rede (grupo de VMs).

As propriedades principais de um recurso NSG incluem:

- **Regra de segurança** - um NSG um pode ter várias regras de segurança definidas. Cada regra pode permitir ou negar diferentes tipos de tráfego.

## Regra de segurança
Uma regra de segurança é um recurso de filho de um NSG.

As propriedades principais de uma regra de segurança incluem:

- **Protocolo** – protocolo de rede ao qual essa regra se aplica.
- **Intervalo de portas de origem** - porta de origem ou o intervalo de portas, de 0 a 65535. Um caractere curinga pode ser usado para corresponder a todas as portas. 
- **Intervalo de portas de destino** - porta de destino ou intervalo de portas, de 0 a 65535. Um caractere curinga pode ser usado para corresponder a todas as portas.
- **Prefixo de endereço de origem** – intervalo de endereços IP de origem. 
- **Prefixo de endereço de destino** – intervalo de endereços IP de destino.
- **Acesso** – *Permitir* ou *Bloquear* tráfego.
- **Prioridade** – um valor entre 100 e 4096. O número de prioridade deve ser exclusivo para cada regra na coleção de regras de segurança. Quanto menor o número da prioridade, maior será a prioridade da regra.
- **Direção** – especifica se a regra será aplicada ao tráfego na direção de *entrada* ou *saída*. 

## Gateway de VPN 
Um recurso de gateway VPN permite que você crie uma conexão segura entre seu data center local e o Azure. Um recurso de gateway VPN pode ser configurado de três maneiras diferentes:
 
- **Ponto a Site** – você pode acessar com segurança os recursos do Azure hospedados em uma VNET com um cliente VPN, em qualquer computador. 
- **Conexão multissite** – você pode conectar-se com segurança por meio de seus data centers locais para recursos em execução em uma VNET. 
- **VNET para VNET** – você pode conectar-se com segurança entre VNETS do Azure na mesma região ou em regiões diferentes, para criar cargas de trabalho com redundância geográfica.

Propriedades principais de um gateway VPN incluem:
 
- **Tipo de gateway** - gateway roteado dinamicamente ou estaticamente. 
- **Prefixo do Pool de endereços do cliente de VPN** – endereços IP a serem atribuídos a clientes conectando-se em configuração de ponto a site.

## Perfil de Gerenciador de Tráfego
O gerenciador de tráfego e seu recurso de ponto de extremidade filho permitem a distribuição de tráfego para pontos de extremidade no Azure e fora do Azure. Essa distribuição de tráfego é controlada por políticas. O Gerenciador de Tráfego também permite que a integridade do ponto de extremidade seja monitorada e que o tráfego seja desviado adequadamente, com base na integridade de um ponto de extremidade.

Propriedades principais de um perfil de gerenciador de tráfego incluem:

- **Método de roteamento de tráfego** - os valores possíveis são *Desempenho*, *Ponderado* e *Prioridade*.
- **Configuração DNS** -FQDN para o perfil.
- **Protocolo** - protocolo de monitoramento, os valores possíveis são *HTTP* e *HTTPS*.
- **Porta** - porta de monitoramento. 
- **Caminho** - caminho de monitoramento.
- **Pontos de extremidade** -contêiner para recursos de ponto de extremidade.

## Ponto de extremidade 
Um ponto de extremidade é um recurso de filho de um perfil de Gerenciador de Tráfego. Representa um serviço ou ponto de extremidade Web para o qual o tráfego de usuário é distribuído com base na política configurada no recurso de Perfil de Gerenciador de Tráfego.

As propriedades principais de um ponto de extremidade incluem:
 
- **Tipo** - o tipo do ponto de extremidade, os valores possíveis são *Ponto de extremidade do Azure*, *Ponto de extremidade externo* e *Ponto de extremidade aninhado*. 
- **ID do recurso de destino** - endereço IP público de um ponto de extremidade de serviço ou da Web. Isso pode ser um ponto de extremidade do Azure ou externo.
- **Peso** - peso de ponto de extremidade usado no gerenciamento de tráfego. 
- **Prioridade** - prioridade do ponto de extremidade, usada para definir uma ação de failover. 

## Criação de um modelo

Você pode implantar serviços no Azure de um modelo usando o PowerShell, AzureCLI ou apenas clicando para implantar no GitHub. Para implantar os serviços de um modelo no GitHub, execute as etapas a seguir:

1. Abra o arquivo template3 no GitHub. Por exemplo, abra a rede Virtual do [ com duas sub-redes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-virtual-network).
2. Clique em **implantar no Azure** e entre no portal do Azure com suas credenciais.
3. Verifique o modelo e, em seguida, clique em **Salvar**.
4. Clique em **Editar parâmetros** e selecione um local, como *Oeste dos EUA*, para o vnet e sub-redes.
5. Se necessário, altere os parâmetros **ADDRESSPREFIX** e **SUBNETPREFIX** e clique em **OK**.
6. Clique em **Selecionar um grupo de recursos** e, em seguida, clique no grupo de recursos ao qual você deseja adicionar o vnet e as sub-redes. Como alternativa, você pode criar um novo grupo de recursos clicando **Ou criar novos**.
3. Clique em **Criar**. Observe a exibição lado a lado **Implantação de modelo de provisionamento**. Quando a implantação estiver concluída, você verá uma tela semelhante à tela abaixo.

![Implantação do modelo de exemplo](./media/resource-groups-networking/Figure6.png)

## Consulte também

[Referência de API de rede do Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx)

[Referência do Azure PowerShell para rede](https://msdn.microsoft.com/library/azure/mt163510.aspx)

[Idioma de modelo do Gerenciador de Recursos do Azure](https://msdn.microsoft.com/library/azure/dn835138.aspx)

[Rede do Azure - modelos usados frequentemente](https://github.com/Azure/azure-quickstart-templates)

[Provedor de recursos de computação](../virtual-machines-azurerm-versus-azuresm)

[Visão Geral do Gerenciador de Recursos do Azure](../resource-group-overview)

[Controle de acesso com base em função no Gerenciador de Recursos do Azure](https://msdn.microsoft.com/library/azure/dn906885.aspx)

[Usando marcas no Gerenciador de Recursos do Azure](https://msdn.microsoft.com/library/azure/dn848368.aspx)

[Implantações de modelo](https://msdn.microsoft.com/library/azure/dn790549.aspx)

<!---HONumber=July15_HO3-->