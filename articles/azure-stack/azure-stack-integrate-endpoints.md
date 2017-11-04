---
title: "Azure pilha datacenter integração - publicar pontos de extremidade"
description: Saiba como publicar pontos de extremidade de pilha do Azure no seu datacenter
services: azure-stack
author: troettinger
ms.service: azure-stack
ms.topic: article
ms.date: 10/18/2017
ms.author: victorh
keywords: 
ms.openlocfilehash: 0d15252079b62f6a74a1279309fb9b1b3ed5711e
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2017
---
# <a name="azure-stack-datacenter-integration---publish-endpoints"></a>Azure pilha datacenter integração - publicar pontos de extremidade

*Aplica-se a: sistemas integrados de pilha do Azure*

A pilha do Azure define vários pontos de extremidade (VIPs - endereços IP virtuais) para suas funções de infraestrutura. Esses VIPs são alocados do pool de endereços IP público. Cada VIP é protegida com uma lista de controle de acesso (ACL) na camada de rede definida pelo software. As ACLs também são usadas entre os comutadores físicos (tes e BMC) para proteger ainda mais a solução. Uma entrada de DNS é criada para cada ponto de extremidade na zona DNS externo que foi especificado no momento da implantação.


O seguinte diagrama de arquitetura mostra as camadas de rede diferente e ACLs:

![Diagrama de arquitetura](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

## <a name="ports-and-protocols-inbound"></a>Portas e protocolos (entrada)

VIPs de infraestrutura que são necessários para publicação pontos de extremidade de pilha do Azure para redes externas são listados na tabela a seguir. A lista mostra cada ponto de extremidade, a porta necessária e o protocolo. Pontos de extremidade necessários para provedores de recursos adicionais, como o provedor de recursos do SQL e outros, são abordados a documentação de implantação do provedor de recurso específico.

Infraestrutura interna que VIPs não são listadas porque eles não são necessários para a pilha de publicação do Azure.

> [!NOTE]
> VIPs de usuário são dinâmicos, definidos pelos usuários com nenhum controle pelo operador pilha do Azure.


|Ponto de extremidade (VIP)|Um registro de host DNS|Protocolo|Portas|
|---------|---------|---------|---------|
|AD FS|`Adfs.[Region].[External FQDN]`|HTTPS|443|
|Portal (administrador)|`Adminportal.[Region].[External FQDN]`|HTTPS|443<br>12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13020<br>13021<br>13026<br>30015|
|Gerenciador de recursos do Azure (administrador)|`Adminmanagement.[Region].[External FQDN]`|HTTPS|443<br>30024|
|Portal (usuário)|`Portal. [Region].[External FQDN]`|HTTPS|443<br>12495<br>12649<br>13001<br>13010<br>13011<br>13020<br>13021<br>30015<br>13003|
|Gerenciador de recursos do Azure (usuário)|`Management.[Region].[External FQDN]`|HTTPS|443<br>30024|
|Gráfico|`Graph.[Region].[External FQDN]`|HTTPS|443|
|Lista de certificados revogados|`Crl.[Region].[External FQDN]`|HTTP|80|
|DNS|`*.[Region].[External FQDN]`|TCP E UDP|53|
|Cofre de chaves (usuário)|`*.vault.[Region].[External FQDN]`|TCP|443|
|Cofre de chaves (administrador)|`*.adminvault.[Region].[External FQDN]`|TCP|443|
|Fila de Armazenamento|`*.queue.[Region].[External FQDN]`|HTTP<br>HTTPS|80<br>443|
|Tabela de Armazenamento|`*.table.[Region].[External FQDN]`|HTTP<br>HTTPS|80<br>443|
|Blob de Armazenamento|`*.blob.[Region].[External FQDN]`|HTTP<br>HTTPS|80<br>443|

## <a name="ports-and-urls-outbound"></a>Portas e URLs (saídas)

Pilha do Azure oferece suporte somente a servidores de proxy transparente. Em uma implantação onde uplinks um proxy transparente em um servidor de proxy tradicional, você deve permitir que as URLs e portas a seguir para comunicação de saída:


|Finalidade|URL|Protocolo|Portas|
|---------|---------|---------|---------|
|Identidade|`login.windows.net`<br>`login.microsoftonline.com`<br>`graph.windows.net`|HTTP<br>HTTPS|80<br>443|
|Distribuição do Marketplace|`https://management.azure.com`<br>`https://*.blob.core.windows.net`<br>`https://*.azureedge.net`<br>`https://*.microsoftazurestack.com`|HTTPS|443|
|Patch e atualização|`https://*.azureedge.net`|HTTPS|443|
|Registro|`https://management.azure.com`|HTTPS|443|
|Uso|`https://*.microsoftazurestack.com`<br>`https://*.trafficmanager.com`|HTTPS|443|

## <a name="firewall-publishing"></a>Publicação de firewall

As portas listadas na seção anterior, se aplicam a comunicação de entrada ao publicar serviços de pilha do Azure por meio de um firewall.

É recomendável que você use um dispositivo de firewall para ajudar a proteger pilha do Azure. No entanto, não é um requisito restrito. Embora firewalls podem ajudar com coisas como ataques (DDOS) de negação de serviço distribuídos e inspeção de conteúdo, eles também podem se tornar um afunilamento de taxa de transferência para serviços de armazenamento do Azure como blobs, tabelas e filas.

Com base no modelo de identidade (Azure AD ou AD FS), ele pode ou não pode ser necessárias para publicar o ponto de extremidade do AD FS. Se for usado um modo de implantação desconectado, você deve publicar o ponto de extremidade do AD FS. (Para obter mais informações, consulte o tópico de identidade de integração de Datacenter).

O Gerenciador de recursos do Azure (administrador), portal de administrador e pontos de extremidade do Cofre de chaves (administrador) não requer necessariamente publicação externa. Dependendo do cenário. Por exemplo, como um provedor de serviço, você talvez queira limitar a superfície de ataque e administrar somente a pilha do Azure de dentro de sua rede e não da Internet.

Para uma organização, a rede externa pode ser a rede corporativa existente. Nesse cenário, você deve publicar esses pontos de extremidade para operar Azure pilha da rede corporativa.

## <a name="edge-firewall-scenario"></a>Cenário de firewall de borda

Em uma implantação de borda, a pilha do Azure é implantada diretamente atrás do roteador de borda (fornecido pelo ISP) com ou sem um firewall na frente dele.

![Diagrama da arquitetura de uma implantação de borda da pilha do Azure](media/azure-stack-integrate-endpoints/Integrate-Endpoints-02.png)

Normalmente, os endereços IP roteáveis públicos são especificados para o pool de VIP público no momento da implantação em uma implantação de borda. Este cenário permite que um usuário tiver a experiência completa de nuvem automaticamente controlado como um pública em nuvem como o Azure.

### <a name="using-nat"></a>Usando NAT

Embora não seja recomendado devido à sobrecarga, você pode usar a conversão de endereço de rede (NAT) para pontos de extremidade de publicação. Para a publicação de ponto de extremidade totalmente controlado por usuários, isso requer uma regra NAT por usuário VIP que contém todas as portas que um usuário pode usar.

Outra consideração é que o Azure não suporta a configuração de um túnel VPN para um ponto de extremidade usando o NAT em um cenário de nuvem híbrida com o Azure.

## <a name="enterpriseintranetperimeter-network-firewall-scenario"></a>Cenário de firewall de rede de perímetro/de intranet de empresa

Em uma implantação de intranet/enterprise/perímetro, a pilha do Azure é implantada além de um segundo firewall, que normalmente é parte de uma rede de perímetro (também conhecida como rede de Perímetro).

![Cenário de firewall de pilha do Azure](media/azure-stack-integrate-endpoints/Integrate-Endpoints-03.png)

Se os endereços IP roteáveis públicos tiverem sido especificados para o pool de VIP público da pilha do Azure, esses endereços logicamente pertencem à rede de perímetro e exigem regras de publicação no primário firewall.

### <a name="using-nat"></a>Usando NAT

Se os endereços IP roteáveis não públicos são usados para o pool de VIP público da pilha do Azure, NAT é usado no firewall secundário para publicar pontos de extremidade de pilha do Azure. Nesse cenário, você precisa configurar as regras de publicação no primário firewall além da borda e o Firewall do secundário. Se você quiser usar NAT, considere os seguintes pontos:

- NAT adiciona sobrecarga ao gerenciar regras de firewall, como os usuários controlar seus próprios pontos de extremidade e suas próprias regras de publicação da pilha de (SDN) rede definida pelo software. Os usuários devem entrar em contato com o operador de pilha do Azure para obter seus VIPs publicados e para atualizar a lista de portas.
- Enquanto o uso do NAT limita a experiência do usuário, ele dá controle total para o operador sobre solicitações de publicação.
- Para cenários de nuvem híbrida com o Azure, considere a possibilidade de que o Azure não oferece suporte a configuração de um túnel VPN para um ponto de extremidade usando NAT.


## <a name="next-steps"></a>Próximas etapas

[Integração do data center do Azure pilha - segurança](azure-stack-integrate-security.md)