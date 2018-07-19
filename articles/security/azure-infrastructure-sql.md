---
title: Recursos de segurança do Banco de Dados SQL do Azure
description: Este artigo fornece uma descrição geral do Banco de Dados SQL que protege os dados do cliente no Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: cca8febb004029b13b0df09a047da701c4528e8e
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101288"
---
# <a name="microsoft-azure-sql-database-security-features"></a>Recursos de segurança do Banco de Dados SQL do Microsoft Azure    
O Banco de Dados SQL do Microsoft Azure oferece um serviço de banco de dados relacional no Azure. Para proteger os dados do cliente e fornecer recursos de segurança fortes que eles esperam de um serviço de banco de dados relacional, o Banco de Dados SQL tem seus próprios conjuntos de recursos de segurança. Esses recursos se baseiam nos controles que são herdados do Azure.

## <a name="security-capabilities"></a>Funcionalidades de segurança

### <a name="usage-of-tabular-data-stream-tds-protocol"></a>Uso do protocolo TDS (Tabular Data Stream)
O Banco de Dados SQL do Microsoft Azure oferece suporte apenas ao protocolo TDS, que exige que o banco de dados fique acessível apenas para a porta padrão de TCP/1433.

### <a name="microsoft-azure-sql-database-firewall"></a>Firewall do Banco de Dados SQL do Microsoft Azure
Para ajudar a proteger os dados dos clientes, o Banco de Dados SQL do Microsoft Azure inclui uma funcionalidade de firewall, que, por padrão, impede todo acesso ao servidor de Banco de Dados SQL, conforme mostrado abaixo.

![Firewall do Banco de Dados SQL do Azure][1]

O firewall do gateway fornece o recurso de limitar os endereços, permitindo um controle granular para que os clientes especifiquem os intervalos de endereços IP aceitáveis. O firewall concede acesso com base no endereço IP de origem de cada solicitação.

A configuração de firewall pode ser realizada usando um Portal de Gerenciamento ou programaticamente usando a API REST de Gerenciamento de Banco de Dados SQL do Microsoft Azure. O firewall de Gateway de Banco de Dados SQL do Microsoft Azure por padrão impede todo acesso a protocolo TDS de clientes para Bancos de Dados SQL do Microsoft Azure. O acesso deve ser configurado usando ACLs para permitir conexões do Banco de Dados SQL do Microsoft Azure pelo código-fonte e endereços de Internet de destino, protocolos e números de porta.

### <a name="dosguard"></a>DoSGuard
Ataques de DoS (negação de serviço) são reduzidos por um serviço de Gateway de Banco de Dados SQL chamado DoSGuard. O DoSGuard controla ativamente os logons com falha de endereços IP. Se houver vários logons com falha de um endereço IP específico dentro de um período de tempo, o endereço IP é impedido de acessar todos os recursos no serviço por um período de tempo predefinido.

Além disso, também realiza gateway do Banco de Dados SQL do Microsoft Azure também realiza:

- Negociações de recurso de canal seguro para implementar conexões criptografadas validadas TDS FIPS 140-2 ao se conectar a servidores de banco de dados.
- Inspeção de pacotes TDS com monitoração de estado ao aceitar conexões de clientes. O gateway valida as informações de conexão e passa os pacotes TDS para o servidor físico apropriado com base no nome do banco de dados especificado na cadeia de conexão.

O princípio geral de segurança de rede da oferta do Banco de Dados SQL do Microsoft Azure é permitir somente a conexão e comunicação necessária para permitir que o serviço opere. Todas as outras portas, protocolos e conexões são bloqueados por padrão. VLANs e as ACLs são usadas para restringir a comunicação de rede por redes de origem e destino, protocolos e números de porta.

Mecanismos aprovados para implementar ACLs com base em rede incluem: ACLs em roteadores e balanceadores de carga. Esses são gerenciados pela Rede do Azure, o firewall de VM Convidada e regras de firewall do gateway do Banco de Dados SQL do Microsoft Azure, que são configurados pelo cliente.

## <a name="data-segregation-and-customer-isolation"></a>Segregação de dados e isolamento do cliente
A rede de Produção do Azure é estruturada de forma que os componentes do sistema publicamente acessíveis são segregados de recursos internos. Existem limites de físicos e lógicos entre os servidores Web fornecendo acesso ao Portal do Azure voltado ao público e a infraestrutura virtual subjacente do Azure, onde residem as instâncias do aplicativo de cliente e os dados do cliente.

Todas as informações publicamente acessíveis são gerenciadas dentro da rede de Produção do Azure. A rede de Produção está sujeita a autenticação de dois fatores e mecanismos de proteção de limite, usa o conjunto de recursos de firewall e segurança descrito na seção anterior e funções de isolamento de dados, conforme indicado abaixo.

### <a name="unauthorized-systems-and-isolation-of-fc"></a>Sistemas de não autorizados e isolamento de FC
Como o FC é o orquestrador central do Microsoft Azure Fabric, controles significativos estão em vigor para atenuar as ameaças a ele, especialmente de FAs potencialmente comprometidos em aplicativos do cliente. O FC não reconhece nenhum hardware cujas informações de dispositivo (por exemplo, endereço MAC) não tenham sido carregadas previamente no FC. Os servidores DHCP no FC configuraram as listas de endereços MAC de nós que estão devem ser reinicializados. Mesmo que sistemas não autorizados estejam conectados, eles não incorporados ao inventário do Fabric e, portanto, não estão conectados ou autorizados a se comunicar com nenhum sistema no inventário do Fabric. Isso reduz o risco de sistemas não autorizados se comunicarem com o FC e terem acesso à VLAN e ao Azure.

### <a name="vlan-isolation"></a>Isolamento de VLAN
A rede de produção do Azure é separada logicamente em três VLANs primárias:

- A VLAN principal – interconecta nós de clientes não confiáveis
- A VLAN de FC – contém FCs confiáveis e sistemas de suporte
- A VLAN do dispositivo – contém a rede confiável e outros dispositivos de infraestrutura

### <a name="packet-filtering"></a>Filtragem de pacotes
O IPFilter e os firewalls de software implementados SO Raiz e no SO Convidado dos nós impõem restrições de conectividade e impedem tráfego não autorizado entre as VMs.

### <a name="hypervisor-root-os-and-guest-vms"></a>Hipervisor, Root OS e VMs Convidadas
O isolamento do SO Raiz das VMs Convidadas, e entre as VMs Convidadas, gerenciado pelo Hipervisor e pelo SO Raiz.

### <a name="types-of-rules-on-firewalls"></a>Tipos de regras em firewalls
Uma regra é definida como:

{IP do Security Response Center (Src), Porta do Src, IP de destino, Porta de destino, Protocolo de destino, Entrada/Saída, Com monitoração de estado/sem monitoração de estado, Tempo limite de fluxo com monitoração de estado}.

A entrada ou saída de pacotes SYN é permitida somente se alguma das regras permitir. Para TCP, o Microsoft Azure usa regras sem monitoração de estado em que o princípio é que ele só permita que todos os pacotes não SYN para entrem ou saiam da VM. A premissa de segurança é que qualquer pilha de host seja resiliente de ignorar um não SYN se ele não tiver sido visto como um pacote SYN anteriormente. O protocolo TCP em si tem monitoração de estado e, em combinação com a regra SYNbased sem monitoração de estado, alcança um comportamento geral de uma implementação com monitoração de estado.

Para o protocolo UDP (User Datagram), o Microsoft Azure usa uma regra com monitoração de estado. Sempre que um pacote UDP corresponde a uma regra, um fluxo inverso é criado na outra direção. Esse fluxo tem um tempo limite interno.

Os clientes são responsáveis por configurar seus próprios firewalls além do que é fornecido pelo Microsoft Azure. Aqui, os clientes são capazes de definir as regras de tráfego de entrada e saída.

### <a name="production-configuration-management"></a>Gerenciamento de configuração de produção
As configurações padrão de seguras são mantidas por equipes de operações respectivas no Azure e no Banco de Dados SQL do Microsoft Azure. Todas as alterações de configuração em sistemas de produção são documentadas e controladas por meio de um sistema de controle central. Alterações de software e hardware são controladas por meio do sistema central de rastreamento. Alterações de rede relacionadas à ACL são controladas usando o AMS (Serviço de Gerenciamento de ACL).

Todas as alterações de configuração no Microsoft Azure são desenvolvidas e testadas no ambiente de preparo; e daí em diante, implantadas no ambiente de produção. Builds de software são examinados como parte dos testes. Verificações de segurança e privacidade são analisadas como parte dos critérios de lista de verificação de entrada. As alterações são implantadas em intervalos programados pela respectiva equipe de implantação. Versões são analisadas e aprovados pela respectiva equipe de implantação antes de serem implantadas em produção.

As alterações são monitoradas para o sucesso. Em um cenário de falha, a alteração é revertida ao estado anterior ou um hotfix é implantado para resolver a falha com a aprovação da equipe designada. Depósito de origem, Git, TFS, MDS, executores, monitoramento de segurança do Azure (ASM), o FC e a plataforma do WinFabric são usados para gerenciar, aplicar e verificar centralmente as definições de configuração no ambiente virtual do Azure.

Da mesma forma, as alterações de hardware e de rede têm estabelecido etapas de validação para avaliar sua aderência aos requisitos de build. As versões são analisadas e autorizadas por meio de um Comitê de Aconselhamento sobre Mudanças (CAB) coordenado dos respectivos grupos na pilha.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o que a Microsoft faz para proteger a infraestrutura do Azure, confira:

- [Recursos, local e segurança física do Azure](azure-physical-security.md)
- [Disponibilidade da infraestrutura do Azure](azure-infrastructure-availability.md)
- [Componentes e limites do sistema de informações do Azure](azure-infrastructure-components.md)
- [Arquitetura de rede do Azure](azure-infrastructure-network.md)
- [Rede de produção do Azure](azure-production-network.md)
- [Gerenciamento e operações de produção do Azure](azure-infrastructure-operations.md)
- [Monitoramento da infraestrutura do Azure](azure-infrastructure-monitoring.md)
- [Monitoramento da integridade do Azure](azure-infrastructure-integrity.md)
- [Proteção de dados do cliente no Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-infrastructure-sql/sql-database-firewall.png
