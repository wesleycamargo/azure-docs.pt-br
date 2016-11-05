---
title: Considerações de segurança para o Gerenciador de Recursos | Microsoft Docs
description: Mostra abordagens recomendadas no Gerenciador de Recursos do Azure para proteger recursos com chaves e segredos, controle de acesso baseado em função e grupos de segurança de rede.
services: azure-resource-manager
documentationcenter: ''
author: george-moore
manager: georgem
editor: tysonn

ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2016
ms.author: georgem;tomfitz

---
# Considerações de segurança do Gerenciador de Recursos do Azure
Ao examinar os aspectos de segurança dos modelos do Gerenciador de Recursos do Azure, existem várias áreas a considerar: chaves e segredos, controle de acesso baseado em função e grupos de segurança de rede.

Este tópico pressupõe que você esteja familiarizado com o RBAC (controle de acesso baseado em função) no Gerenciador de Recursos do Azure. Para saber mais, consulte [Controle de acesso baseado em função no Azure](active-directory/role-based-access-control-configure.md).

Este tópico faz parte de um whitepaper mais amplo. Para ler o documento completo, baixe [Considerações e práticas comprovadas de modelos ARM de nível mundial](http://download.microsoft.com/download/8/E/1/8E1DBEFA-CECE-4DC9-A813-93520A5D7CFE/World Class ARM Templates - Considerations and Proven Practices.pdf).

## Segredos e certificados
As Máquinas Virtuais do Azure, o Gerenciador de Recursos do Azure e o Cofre da Chave do Azure são totalmente integrados para dar suporte ao processamento seguro dos certificados que devem ser implantados na VM. Usar o Cofre da Chave do Azure com o Gerenciador de Recursos para orquestrar e armazenar certificados e segredos de VM é uma prática recomendada e fornece as seguintes vantagens:

* Os modelos contêm somente as referências de URI aos segredos, o que significa que os segredos reais não estão nos repositórios de códigos, de configurações ou de códigos-fonte. Isso impede grandes ataques de phishing em repositórios internos ou externos, como de harvest-bots no GitHub.
* Os segredos armazenados no Cofre da Chave estão sob controle RBAC total de um operador confiável. Se o operador confiável deixa a empresa ou é transferido para um novo grupo na empresa, ele deixa de ter acesso às chaves que criou no Cofre.
* Compartimentalização completa de todos os ativos:
  * os modelos para implantar as chaves
  * os modelos para implantar uma VM com referências às chaves
  * os materiais de chave reais no Cofre. Cada modelo (e ação) pode estar em diferentes funções RBAC para separação total das tarefas.
* O carregamento de segredos em uma VM no momento da implantação ocorre por meio do canal direto entre a Malha do Azure e o Cofre da Chave nos limites do datacenter da Microsoft. Depois que as chaves estiverem no Cofre da Chave, elas nunca mais verão a "luz do sol" em um canal não confiável fora do datacenter.
* Os Cofres das Chaves são sempre regionais, de modo que os segredos mantêm sempre a localidade (e a soberania) em relação às máquinas virtuais. Não há nenhum Cofre da Chave global.

### Separação das chaves de implantações
Uma prática recomendada é manter modelos separados para:

1. Criação de cofres (que conterão o material de chave)
2. Implantação de VMs (com referências de URI às chaves contidas nos cofres)

Um cenário corporativo típico é ter um pequeno grupo de operadores confiáveis que têm acesso aos segredos críticos nas cargas de trabalho implantadas, juntamente com um grupo mais amplo de desenvolvedores/operadores que podem criar ou atualizar implantações de VM. Veja abaixo um modelo de exemplo do ARM que cria e configura um novo cofre no contexto da identidade do usuário atualmente autenticada no Active Directory do Azure. Esse usuário teria permissão padrão para criar, excluir, listar, atualizar, fazer backup, restaurar e obter a metade pública das chaves no novo cofre da chave.

Embora a maioria dos campos desse modelo seja autoexplicativa, a configuração **enableVaultForDeployment** merece mais explicações: os cofres não têm acesso permanente padrão por meio de nenhum outro componente da infraestrutura do Azure. Ao definir esse valor, ele permite que os componentes da infraestrutura de Computação do Azure tenham acesso somente leitura a esse cofre nomeado específico. Portanto, uma prática recomendada adicional é não misturar dados corporativos confidenciais no mesmo cofre como segredos de máquina virtual.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the Vault"
                }
            },
            "location": {
                "type": "string",
                "allowedValues": ["East US", "West US", "West Europe", "East Asia", "South East Asia"],
                "metadata": {
                    "description": "Location of the Vault"
                }
            },
            "tenantId": {
                "type": "string",
                "metadata": {
                    "description": "Tenant Id of the subscription. Get using Get-AzureSubscription cmdlet or Get Subscription API"
                }
            },
            "objectId": {
                "type": "string",
                "metadata": {
                    "description": "Object Id of the AD user. Get using Get-AzureADUser cmdlet"
                }
            },
            "skuName": {
                "type": "string",
                "allowedValues": ["Standard", "Premium"],
                "metadata": {
                    "description": "SKU for the vault"
                }
            },
            "enableVaultForDeployment": {
                "type": "bool",
                "allowedValues": [true, false],
                "metadata": {
                    "description": "Specifies if the vault is enabled for a VM deployment"
                }
            }
        },
        "resources": [{
            "type": "Microsoft.KeyVault/vaults",
            "name": "[parameters('keyVaultName')]",
            "apiVersion": "2014-12-19-preview",
            "location": "[parameters('location')]",
            "properties": {
                "enabledForDeployment": "[parameters('enableVaultForDeployment')]",
                "tenantid": "[parameters('tenantId')]",
                "accessPolicies": [{
                    "tenantId": "[parameters('tenantId')]",
                    "objectId": "[parameters('objectId')]",
                    "permissions": {
                        "secrets": ["all"],
                        "keys": ["all"]
                    }
                }],
                "sku": {
                    "name": "[parameters('skuName')]",
                    "family": "A"
                }
            }
        }]
    }

Depois de criar o cofre, a próxima etapa é referenciar esse cofre no modelo de implantação de uma nova VM. Conforme mencionado acima, uma prática recomendada é fazer com que um grupo diferente de desenvolvedores/operadores gerencie implantações de VM, sendo que esse grupo não terá acesso direto às chaves como armazenadas no cofre.

O fragmento do modelo abaixo deveria ser composto em construções de implantação de ordem superior, cada uma das quais referenciando com segurança segredos altamente confidenciais que não estão sob o controle direto do operador.

    "vaultName": {
        "type": "string",
        "metadata": {
            "description": "Name of Key Vault that has a secret"
        }
    },
    {
        "apiVersion": "2015-05-01-preview",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "[parameters('vmName')]",
        "location": "[parameters('location')]",
        "properties": {
            "osProfile": {
                "secrets": [{
                    "sourceVault": {
                        "id": "[resourceId('vaultrg', 'Microsoft.KeyVault/vaults', 'kayvault')]"
                    },
                    "vaultCertificates": [{
                        "certificateUrl": "[parameters('secretUrlWithVersion')]",
                        "certificateStore": "My"
                    }]
                }]
            }
        }
    }

Para passar um valor de um cofre de chave como um parâmetro durante a implantação de um modelo, consulte [Passar valores seguros durante a implantação](resource-manager-keyvault-parameter.md).

## Entidades de serviço para interações entre assinaturas
As identidades de serviço são representadas por entidades de serviço no Active Directory. As entidades de serviço estarão no centro de cenários de habilitação de chave para organizações de TI corporativas, SI (integradores de sistemas) e CSV (fornecedores de serviços de nuvem). Especificamente, haverá casos de uso em que uma dessas organizações precisará interagir com a assinatura de um de seus clientes.

Sua organização pode fornecer uma oferta que monitorará uma solução implantada no ambiente e na assinatura de seus clientes. Nesse caso, você precisará obter acesso aos logs e a outros dados em uma conta de clientes para que você possa usá-la em sua solução de monitoramento. Se você for uma organização de TI corporativa ou um integrador de sistemas, poderá fornecer uma oferta a um cliente na qual implantará e gerenciará uma funcionalidade, como uma plataforma de análise de dados, com a oferta residindo na própria assinatura dos clientes.

Nesses casos de uso, sua organização requer uma identidade que pode permitir acesso para executar essas ações no contexto de uma assinatura de cliente.

Estes cenários apresentam um determinado conjunto de considerações para o cliente:

* Por motivos de segurança, o acesso talvez precise ter um escopo para determinados tipos de ações, por exemplo, acesso somente leitura.
* Como os recursos implantados são fornecidos a um custo, pode haver restrições semelhantes no acesso necessário por razões financeiras.
* Por motivos de segurança, o acesso talvez precise ter um escopo somente para um recurso específico (contas de armazenamento) ou recursos (grupo de recursos que contém um ambiente ou uma solução)
* Uma vez que a relação com um fornecedor pode mudar, o cliente desejará poder habilitar/desabilitar o acesso para o integrador de sistemas ou para o fornecedor de serviços de nuvem
* Como ações contra essa conta podem ter implicações de cobrança, o cliente deseja ter suporte para auditoria e responsabilidade para cobrança.
* De uma perspectiva de conformidade, o cliente desejará poder auditar seu comportamento no ambiente dele

Uma combinação de uma entidade de serviço e o RBAC pode ser usada para atender a esses requisitos.

## Grupos de segurança de rede
Muitos cenários terão requisitos que especificam como o tráfego para uma ou mais instâncias de VM em sua rede virtual é controlado. Você pode usar um NSG (grupo de segurança de rede) para fazer isso como parte de uma implantação de modelo do ARM.

Um grupo de segurança de rede é um objeto de nível superior associado à sua assinatura. Um NSG contém regras de controle de acesso que permitem ou negam o tráfego para as instâncias de VM. As regras de um NSG podem ser alteradas a qualquer momento e as alterações se aplicam a todas as instâncias associadas. Para usar um NSG, você deve ter uma rede virtual associada a uma região (local). Os NSGs não são compatíveis com as redes virtuais associadas a um grupo de afinidades. Se você não tiver uma rede virtual regional e quiser controlar o tráfego dos pontos de extremidade, consulte [Sobre as ACLs (Listas de Controle de Acesso) de rede](virtual-network/virtual-networks-acl.md).

Você pode associar um NSG a uma VM ou a uma sub-rede em uma rede virtual. Quando associado a uma máquina virtual, o NSG se aplica a todo o tráfego que é enviado e recebido pela instância de VM. Quando aplicado a uma sub-rede na rede virtual, ele se aplica a todo o tráfego que é enviado e recebido por todas as instâncias de VM na sub-rede. Uma VM ou sub-rede pode ser associada a apenas um NSG, no entanto, cada NSG pode conter até 200 regras. Você pode ter 100 NSGs por assinatura.

> [!NOTE]
> Não há suporte para grupos de segurança de rede e ACLs baseadas em ponto de extremidade na mesma instância de VM. Se você quiser usar um NSG e já tiver uma ACL de ponto de extremidade à disposição, primeiro remova a ACL de ponto de extremidade. Para saber mais sobre como fazer isso, confira [Gerenciando listas de controle de acesso (ACLs) para pontos de extremidade usando o PowerShell](virtual-network/virtual-networks-acl-powershell.md).
> 
> 

### Como funcionam os grupos de segurança de rede
Os grupos de segurança de rede são diferentes das ACLs baseadas em ponto de extremidade. As ACLs de ponto de extremidade funcionam apenas na porta pública que fica exposta por meio do ponto de extremidade de entrada. Um NSG funciona em uma ou mais instâncias de VM e controla todo o tráfego de entrada e saída na VM.

Um grupo de segurança de rede tem um *Nome*, está associado a uma *Região* (um dos locais do Azure com suporte) e tem um rótulo descritivo. Ele contém dois tipos de regras: Entrada e Saída. As Regras de entrada são aplicadas aos pacotes de entrada de uma máquina virtual e as Regras de saída são aplicadas a pacotes de saída de uma VM. As regras são aplicadas no computador do servidor no qual a VM está localizada. Um pacote de entrada ou de saída deve corresponder a uma regra de permissão a ser permitida; caso contrário, ele é descartado.

As regras são processadas na ordem de prioridade. Por exemplo, uma regra com um número de prioridade mais baixo, como 100, é processada antes das regras com números de prioridade mais altos, como 200. Quando uma correspondência é encontrada, nenhuma outra regra é processada.

Uma regra especifica o seguinte:

* Nome: um identificador exclusivo da regra
* Tipo: Entrada/Saída
* Prioridade: Um inteiro entre 100 e 4096 (regras processadas do mais baixo para o mais alto)
* Endereço IP de origem: CIDR do intervalo de IP de origem
* Intervalo de porta de origem: um inteiro ou um intervalo entre 0 e 65536
* Intervalo de IP de destino: CIDR do intervalo de IP de destino
* Intervalo de porta de destino: um inteiro ou um intervalo entre 0 e 65536
* Protocolo: TCP, UDP ou ‘*’
* Acesso: Permitir/Negar

### Regras padrão
Um NSG contém regras padrão. As regras padrão não podem ser excluídas, mas como recebem a prioridade mais baixa, elas podem ser substituídas pelas regras que você criar. As regras padrão descrevem as configurações padrão recomendadas pela plataforma. Como ilustrado pelas regras padrão abaixo, o tráfego que começa e termina em uma rede virtual é permitido tanto na Entrada quanto na Saída.

Enquanto a conectividade com a Internet é permitida na saída, ela é por padrão bloqueada na entrada. Uma regra padrão permite que o balanceador de carga do Azure investigue a integridade de uma VM. Você pode substituir essa regra se a VM ou o conjunto de VMs no NSG não participar do conjunto de balanceamento de carga.

As regras padrão são mostradas nas tabelas abaixo.

**Regras de entrada padrão**

| Nome | Prioridade | IP de origem | Porta de origem | IP de destino | Porta de destino | Protocolo | Access |
| --- | --- | --- | --- | --- | --- | --- | --- |
| PERMITIR A ENTRADA DA VNET |65000 |REDE\_VIRTUAL |* |REDE\_VIRTUAL |* |* |PERMITIR |
| PERMITIR A ENTRADA DO BALANCEADOR DE CARGA DO AZURE |65001 |BALANCEADORDECARGA\_AZURE |* |* |* |* |PERMITIR |
| NEGAR TODAS AS ENTRADAS |65500 |* |* |* |* |* |NEGAR |

**Regras de saída padrão**

| Nome | Prioridade | IP de origem | Porta de origem | IP de destino | Porta de destino | Protocolo | Access |
| --- | --- | --- | --- | --- | --- | --- | --- |
| PERMITIR SAÍDA DA VNET |65000 |REDE\_VIRTUAL |* |REDE\_VIRTUAL |* |* |PERMITIR |
| PERMITIR SAÍDA DA INTERNET |65001 |* |* |INTERNET |* |* |PERMITIR |
| NEGAR TODAS AS SAÍDAS |65500 |* |* |* |* |* |NEGAR |

### Regras especiais de infraestrutura
As regras NSG são explícitas. Nenhum tráfego é permitido ou negado além do que é especificado nas regras de NSG. No entanto, dois tipos de tráfego são sempre permitidos, independentemente da especificação do grupo de segurança de rede. Estes provisionamentos são feitos para dar suporte à infraestrutura:

* **IP virtual do nó do host**: serviços básicos de infraestrutura, como DHCP, DNS e integridade de monitoramento, são fornecidos pelo endereço IP virtualizado host 168.63.129.16. Este endereço IP público pertence à Microsoft e será o único endereço IP virtualizado usado em todas as regiões para essa finalidade. Esse endereço IP é mapeado para o endereço IP físico do computador do servidor (nó do host) que hospeda a VM. O nó do host atua como a retransmissão DHCP, o solucionador de DNS recursivo e a fonte de sonda para a investigação de integridade do balanceador de carga e a investigação de integridade da máquina. A comunicação com esse endereço IP não deve ser considerada como um ataque.
* **Licenciamento (Serviço de Gerenciamento de Chaves)**: as imagens do Windows em execução nas VMs devem ser licenciadas. Para fazer isso, uma solicitação de licenciamento é enviada para os servidores de host do serviço de gerenciamento de chaves que lidar com essas consultas. Isso sempre será na porta de saída 1688.

### Marcas padrão
Marcas padrão são identificadores fornecidos pelo sistema para atender a uma categoria de endereços IP. As marcas padrão podem ser especificadas nas regras definidas pelo cliente.

**Marcas padrão para NSGs**

| Marca | Descrição |
| --- | --- |
| REDE\_VIRTUAL |Indica todo o espaço de endereço de rede. Ela inclui o espaço de endereço da rede virtual (CIDR de IP no Azure), bem como todo o espaço de endereço local conectado (redes locais). Isso também inclui espaços de endereço de rede virtual a rede virtual. |
| BALANCEADORDECARGA\_AZURE |Indica o balanceador de carga da infraestrutura do Azure e será convertido em um IP de datacenter do Azure do qual se originarão as investigações de integridade do Azure. Ele somente é necessário se a VM ou um conjunto de máquinas virtuais associado ao NSG estiver participando de um conjunto de balanceamento de carga. |
| INTERNET |Indica o espaço de endereço IP que está fora da rede virtual e que pode ser acessado por Internet pública. Esse intervalo também inclui o espaço de IP público de propriedade do Azure. |

### Portas e intervalos de portas
As regras de NSG podem ser especificadas em uma porta de origem ou de destino única ou em um intervalo de portas. Essa abordagem é especialmente útil quando você deseja abrir uma grande variedade de portas para um aplicativo, como FTP. O intervalo deve ser sequencial e não pode ser combinado com especificações de portas individuais. Para especificar um intervalo de portas, use o caractere hífen (–). Por exemplo, **100-500**.

### Tráfego ICMP
Com as regras NSG atuais, você pode especificar TCP ou UDP como protocolos, mas não o ICMP. No entanto, o tráfego com ICMP é permitido em uma rede virtual por padrão por meio das regras de entrada que dão suporte ao tráfego de e para qualquer porta e protocolo (*) na rede virtual.

### Associando um NSG a uma VM
Quando um NSG está diretamente associado a uma VM, as regras de acesso de rede no NSG são aplicadas diretamente a todo o tráfego destinado à VM. Sempre que o NSG é atualizado por conta de alterações de regra, a manipulação do tráfego reflete as atualizações em questão de minutos. Quando o NSG é desassociado da VM, o estado é revertido à sua condição pré-NSG, isto é, os padrões do sistema antes do NSG ser introduzido.

### Associando um NSG a uma sub-rede
Quando um NSG é associado a uma sub-rede, as regras de acesso de rede no NSG são aplicadas a todas as VMs na sub-rede. Sempre que as regras de acesso no NSG são atualizadas, as alterações são aplicadas a todas as VMs na sub-rede em questão de minutos.

### Associando um NSG a uma sub-rede e a uma VM
Você pode associar um NSG a uma VM e a outro NSG com a sub-rede na qual a VM reside. Há suporte nesse cenário para fornecer duas camadas de proteção à VM. No tráfego de entrada, o pacote segue as regras de acesso especificadas na sub-rede, seguido pelas regras na VM. Na saída, o pacote segue as regras especificadas na VM primeiro e, em seguida, as regras especificadas na sub-rede conforme mostrado abaixo.

![Associando um NSG a uma sub-rede e a uma VM](./media/best-practices-resource-manager-security/nsg-subnet-vm.png)

Quando um NSG está associado a uma VM ou sub-rede, as regras de controle de acesso de rede se tornam bastante explícitas. A plataforma não irá inserir regras implícitas para permitir o tráfego em uma porta específica. Nesse caso, se criar um ponto de extremidade na VM, você também deverá criar uma regra para permitir o tráfego da Internet. Se você não fizer isso, *VIP:{Port}* não poderá ser acessado de fora.

Por exemplo, você cria uma nova VM e um novo NSG. Você associa o NSG à VM. A VM pode se comunicar com outras VMs na rede virtual por meio da regra PERMITIR A ENTRADA DA VNET. A VM também pode fazer conexões de saída com a Internet usando a regra PERMITIR SAÍDA DA INTERNET. Posteriormente, você cria um ponto de extremidade na porta 80 para receber o tráfego para o site em execução na VM. Os pacotes destinados à porta 80 no VIP (endereço IP virtual público) da Internet não chegarão à VM até que você adicione uma regra semelhante à tabela a seguir para o NSG.

**Regra explícita que permite o tráfego para uma porta específica**

| Nome | Prioridade | IP de origem | Porta de origem | IP de destino | Porta de destino | Protocolo | Access |
| --- | --- | --- | --- | --- | --- | --- | --- |
| WEB |100 |INTERNET |* |* |80 |TCP |PERMITIR |

## Rotas definidas pelo usuário
O Azure usa uma tabela de rotas para decidir como encaminhar o tráfego IP com base no destino de cada pacote. Embora o Azure forneça uma tabela de rotas padrão com base em suas configurações de rede virtual, talvez seja necessário adicionar rotas personalizadas a essa tabela.

A necessidade mais comum para uma entrada personalizada na tabela de rotas é o uso de um dispositivo virtual em seu ambiente do Azure. Leve em consideração o cenário mostrado na figura abaixo. Suponha que você queira garantir que todo o tráfego direcionado para a camada intermediária e sub-redes com suporte iniciado da sub-rede de front-end passe por um dispositivo de firewall virtual. Apenas adicionar o dispositivo à sua rede virtual e conectá-lo a diferentes sub-redes não fornecerá essa funcionalidade. Você também deve alterar a tabela de roteamento aplicada à sua sub-rede para garantir que os pacotes sejam encaminhados ao dispositivo de firewall virtual.

O mesmo ocorreria se você implementasse um dispositivo NAT virtual para controlar o tráfego entre sua rede virtual do Azure e a Internet. Para garantir que o dispositivo virtual seja usado, você precisa criar uma rota especificando que todo o tráfego destinado à Internet seja encaminhado ao dispositivo virtual.

### Roteamento
Os pacotes são roteados através de uma rede TCP/IP com base em uma tabela de rotas definida em cada nó na rede física. Uma tabela de rotas é uma coleção de rotas individuais usadas para decidir para onde encaminhar pacotes com base no endereço IP de destino. Uma rota consiste no seguinte:

* Prefixo de endereço. O CIDR de destino ao qual a rota se aplica, como 10.1.0.0/16.
* Tipo do próximo salto. O tipo de salto do Azure ao qual o pacote deve ser enviado. Os valores possíveis são:
  * Local. Representa a rede virtual local. Por exemplo, se você tiver duas sub-redes, 10.1.0.0/16 e 10.2.0.0/16, na mesma rede virtual, a rota para cada sub-rede na tabela de rotas terá um valor de próximo salto de Local.
  * Gateway de VPN. Representa um Gateway de VPN S2S do Azure.
  * Internet. Representa o gateway de Internet padrão fornecido pela Infraestrutura do Azure
  * Dispositivo virtual. Representa um dispositivo virtual que você adicionou à sua rede virtual do Azure.
  * NULO. Representa um buraco negro. Pacotes encaminhados a um buraco negro não serão encaminhados.
* Valor de próximo salto. O valor de próximo salto contém o endereço IP para o qual os pacotes devem ser encaminhados. Os valores de próximas salto são permitidos apenas em rotas em que o próximo salto é um *Dispositivo Virtual*. O próximo salto deve estar na sub-rede (a interface local do dispositivo virtual de acordo com a identificação de rede), não em uma sub-rede remota.

![Roteamento](./media/best-practices-resource-manager-security/routing.png)

### Rotas padrão
Cada sub-rede criada em uma rede virtual é associada automaticamente a uma tabela de rota que contém as seguintes regras de rota padrão:

* Regra VNet local: essa regra é criada automaticamente para todas as sub-redes em uma rede virtual. Ela especifica que há um link direto entre as VMs na VNet e não há nenhum próximo salto intermediário. Isso permite que as VMs na mesma sub-rede, independentemente da identificação de rede na qual as máquinas virtuais existem, se comuniquem umas com as outras sem a necessidade de um endereço de gateway padrão.
* Regra local: essa regra se aplica a todo o tráfego destinado ao intervalo de endereços local e usa o gateway de VPN como o destino do próximo salto.
* Regra de Internet: essa regra processa todo o tráfego destinado à Internet pública e usa o gateway de Internet de infraestrutura como o próximo salto para todo o tráfego destinado à Internet.

### Rotas BGP
No momento em que este artigo foi escrito, a [Rota Expressa](expressroute/expressroute-introduction.md) ainda não tinha suporte no [Provedor de Recursos de Rede](virtual-network/resource-groups-networking.md) para o Gerenciador de Recursos do Azure. Se houver uma conexão de Rota Expressa entre sua rede local e o Azure, você poderá habilitar o BGP para propagar rotas da rede local para o Azure assim que o provedor de recursos de rede der suporte à Rota Expressa. Essas rotas BGP são usadas da mesma maneira que as rotas padrão e as rotas definidas pelo usuário em cada sub-rede do Azure. Para obter mais informações, consulte [Introdução ao ExpressRoute](expressroute/expressroute-introduction.md).

> [!NOTE]
> Quando o NRP der suporte à Rota Expressa, você poderá configurar seu ambiente do Azure para usar um túnel à força por meio de sua rede local, criando uma rota definida pelo usuário para a sub-rede 0.0.0.0/0 que usa o gateway de VPN como o próximo salto. No entanto, isso só funcionará se você estiver usando um gateway de VPN, não o ExpressRoute. Para o ExpressRoute, o túnel à força é configurado por meio do BGP.
> 
> 

### Rotas definidas pelo usuário
Não é possível exibir as rotas padrão especificadas acima em seu ambiente do Azure e, para a maioria dos ambientes, essas são as únicas rotas de que você precisará. No entanto, talvez seja necessário criar uma tabela de rotas e adicionar uma ou mais rotas em casos específicos, como:

* Túnel à força para a Internet através de sua rede local.
* Uso de dispositivos virtuais em seu ambiente do Azure.

Nos cenários acima, você precisará criar uma tabela de rotas e adicionar rotas definidas pelo usuário a ela. Você pode ter várias tabelas de rotas, e a mesma tabela de rotas pode ser associada a uma ou mais sub-redes. Cada sub-rede só pode ser associada a uma única tabela de rotas. Todas as VMs e serviços em nuvem em uma sub-rede usam a tabela de rotas associada a essa sub-rede.

As sub-redes contam com rotas padrão até que uma tabela de rotas seja associada à sub-rede. Quando houver uma associação, o roteamento será feito com base em [LPM (Correspondência de Prefixo mais Longo)](https://en.wikipedia.org/wiki/Longest_prefix_match) entre as rotas definidas pelo usuário e as rotas padrão. Se houver mais de uma rota com a mesma correspondência LPM, uma rota será selecionada com base em sua origem na seguinte ordem:

1. Rota definida pelo usuário
2. Rota BGP (quando o ExpressRoute é usado)
3. Rota padrão

> [!NOTE]
> As rotas definidas pelo usuário são aplicadas apenas a VMs do Azure e a serviços de nuvem. Por exemplo, se desejar adicionar um dispositivo virtual de firewall entre sua rede local e o Azure, você terá que criar uma rota definida pelo usuário para as tabelas de rotas do Azure que encaminham todo o tráfego direcionado ao espaço de endereço local para o dispositivo virtual. No entanto, o tráfego de entrada do espaço de endereço local fluirá através de seu gateway de VPN ou circuito do ExpressRoute diretamente para o ambiente do Azure, ignorando o dispositivo virtual.
> 
> 

### Encaminhamento IP
Conforme descrito acima, uma das principais razões para criar uma rota definida pelo usuário é encaminhar o tráfego para um dispositivo virtual. Um dispositivo virtual é nada mais do que uma VM que executa um aplicativo usado para lidar com o tráfego de rede de alguma forma, como um firewall ou um dispositivo NAT.

Essa VM de dispositivo virtual deve ser capaz de receber o tráfego de entrada não endereçado a si mesma. Para permitir que uma VM receba o tráfego endereçado a outros destinos, você deve habilitar o Encaminhamento IP na VM.

## Próximas etapas
* Para entender como configurar as entidades de segurança com o acesso correto para funcionar com os recursos em sua organização, consulte [Autenticação de uma entidade de serviço com o Gerenciador de Recursos do Azure](resource-group-authenticate-service-principal.md)
* Se precisar bloquear o acesso a um recurso, você pode usar bloqueios de gerenciamento. Consulte [Bloquear recursos com o Gerenciador de Recursos do Azure](resource-group-lock-resources.md)
* Para configurar o roteamento e o encaminhamento de IP, consulte [Criar UDRs (Rotas Definidas pelo Usuário) no Resource Manager usando um modelo](virtual-network/virtual-network-create-udr-arm-template.md)
* Para obter uma visão geral do controle de acesso baseado em função, consulte [Controle de acesso baseado em função no portal do Microsoft Azure](active-directory/role-based-access-control-configure.md)

<!---HONumber=AcomDC_0803_2016-->