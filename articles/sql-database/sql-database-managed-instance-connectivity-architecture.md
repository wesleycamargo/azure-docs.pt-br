---
title: Arquitetura de Conectividade de Instância Gerenciada do Banco de Dados SQL do Azure | Microsoft Docs
description: Este artigo fornece a visão geral da comunicação de Instância Gerenciada do Banco de Dados SQL do Azure e explica a arquitetura de conectividade, além de como os diferentes componentes funcionam para direcionar o tráfego para a Instância Gerenciada.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 12/10/2018
ms.openlocfilehash: b709bbacce23a89b8c60b77a524018b50ca1ca5e
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55245660"
---
# <a name="azure-sql-database-managed-instance-connectivity-architecture"></a>Arquitetura de Conectividade de Instância Gerenciada do Banco de Dados SQL do Azure

Este artigo fornece a visão geral da comunicação de Instância Gerenciada do Banco de Dados SQL do Azure e explica a arquitetura de conectividade, além de como os diferentes componentes funcionam para direcionar o tráfego para a Instância Gerenciada.  

A Instância Gerenciada do Banco de Dados SQL do Azure é colocada dentro da rede virtual do Azure e a sub-rede dedicada para Instâncias Gerenciadas. Essa implantação permite os cenários a seguir: 
- Proteger o endereço IP privado.
- Conectar uma Instância Gerenciada diretamente de uma rede local.
- Conectar uma Instância Gerenciada ao servidor vinculado ou a outro armazenamento de dados local.
- Conectar uma Instância Gerenciada a recursos do Azure.

## <a name="communication-overview"></a>Visão geral da comunicação

O diagrama a seguir mostra entidades que se conectam à Instância Gerenciada, bem como recursos que a Instância Gerenciada precisa atingir para funcionar adequadamente.

![entidades de arquitetura de conectividade](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

A comunicação descrita na parte inferior do diagrama representa os aplicativos e as ferramentas do cliente que se conectam à Instância Gerenciada como fonte de dados.  

Como a Instância Gerenciada é uma oferta de plataforma como um serviço (PaaS), a Microsoft gerencia esse serviço usando agentes automatizados (gerenciamento, implementação e manutenção) com base em fluxos de dados de telemetria. Como o gerenciamento de instâncias gerenciadas é de responsabilidade exclusiva da Microsoft, os clientes não podem acessar as máquinas de cluster virtual da instância gerenciada por meio do RDP.

Algumas operações do SQL Server iniciadas pelos usuários finais ou aplicativos podem exigir que a Instância Gerenciada interaja com a plataforma. Um caso é a criação de um banco de dados de Instância Gerenciada - um recurso que é exposto por meio do portal, PowerShell e CLI do Azure.

A Instância Gerenciada depende de outros Serviços do Azure para seu funcionamento adequado (como o Armazenamento do Azure para backups, o Barramento de Serviços do Azure para telemetria, o Azure AD para autenticação, o Cofre de Chaves do Azure para TDE e assim por diante) e inicia as conexões de acordo.

Todas as comunicações, mencionadas acima, são criptografadas e assinadas usando certificados. Para garantir que as partes em comunicação sejam confiáveis, a Instância Gerenciada verifica constantemente esses certificados entrando em contato com a Autoridade de Certificação. Se os certificados forem revogados ou a Instância Gerenciada não puder verificá-los, ele fechará as conexões para proteger os dados.

## <a name="high-level-connectivity-architecture"></a>Arquitetura de alto nível de conectividade

Em um nível alto, a Instância Gerenciada é um conjunto de componentes de serviço, hospedado em um conjunto dedicado de máquinas virtuais isoladas que são executadas dentro da sub-rede da rede virtual do cliente e formam um cluster virtual.

Várias instâncias gerenciadas podem ser hospedadas em um único cluster virtual. O cluster é expandido ou contratado automaticamente, se necessário, quando o cliente altera o número de instâncias aprovisionadas na sub-rede.

Os aplicativos do cliente podem se conectar à Instância Gerenciada, consultar e atualizar bancos de dados somente se forem executados dentro da rede virtual ou em rede virtual emparelhada ou rede conectada por VPN / Express Route usando endpoint com endereço IP privado.  

![diagrama de arquitetura de conectividade](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Os serviços de gerenciamento e implantação da Microsoft são executados fora da rede virtual, para que a conexão entre a Instância Gerenciada e os serviços da Microsoft ultrapasse os terminais com endereços IP públicos. Quando a Instância Gerenciada cria uma conexão de saída, ao receber o destino, parece que ela está vindo desse IP público devido à NAT (Tradução de Endereço de Rede).

O tráfego de gerenciamento flui através da rede virtual do cliente. Isso significa que os elementos da infraestrutura de rede virtual afetam e podem prejudicar o tráfego de gerenciamento, fazendo com que a instância entre no estado defeituoso e se torne indisponível.

> [!IMPORTANT]
> Para melhorar a experiência do cliente e a disponibilidade do serviço, a Microsoft aplica a Diretiva de Intenção de Rede nos elementos da infraestrutura de rede virtual do Azure que podem afetar o funcionamento da Instância Gerenciada. Este é um mecanismo de plataforma para comunicar de forma transparente os requisitos de rede aos usuários finais, com o objetivo principal de impedir a configuração incorreta da rede e garantir as operações normais da Instância Gerenciada. Na exclusão da instância gerenciada, a política de intenção de rede também é removida.

## <a name="virtual-cluster-connectivity-architecture"></a>Arquitetura de conectividade do cluster virtual

Vamos nos aprofundar na arquitetura de conectividade de instâncias gerenciadas. O diagrama a seguir mostra o layout conceitual do cluster virtual.

![cluster virtual do diagrama de arquitetura de conectividade](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

Os clientes se conectam à Instância Gerenciada usando o nome do host que possui um formulário `<mi_name>.<dns_zone>.database.windows.net`. Esse nome de host é resolvido para o endereço IP privado, embora esteja registrado na zona DNS pública e seja publicamente solucionável. A `zone-id` é gerada quando o cluster é criado. Se um cluster recém-criado estiver hospedando uma instância gerenciada secundária, ele compartilhará sua ID de zona com o cluster primário. Para obter mais informações, confira [Grupos de failover automático](sql-database-auto-failover-group.md##enabling-geo-replication-between-managed-instances-and-their-vnets)

Esse endereço IP privado pertence ao ILB (Managed Internal Load Balancer) da instância gerenciada que direciona o tráfego para o GW (Gateway de Instância Gerenciada). Como várias instâncias gerenciadas podem ser executadas no mesmo cluster, o GW usa o nome do host da instância gerenciada para redirecionar o tráfego para o serviço do SQL Engine correto.

Os serviços de gerenciamento e implantação se conectam à Instância Gerenciada usando o [endpoint de gerenciamento](#management-endpoint) que mapeia para o balanceador de carga externo. O tráfego é roteado para os nós apenas se recebido em um conjunto predefinido de portas usadas exclusivamente pelos componentes de gerenciamento da instância gerenciada. O firewall interno de nós está configurado para permitir tráfego apenas de intervalos IP específicos da Microsoft. Toda a comunicação entre componentes de gerenciamento e plano de gerenciamento é mutuamente autenticado do certificado.

## <a name="management-endpoint"></a>Ponto de extremidade de gerenciamento

O cluster virtual da Instância Gerenciada do Banco de Dados SQL do Azure contém um ponto de extremidade de gerenciamento que a Microsoft usa gerenciar a Instância Gerenciada. O ponto de extremidade de gerenciamento é protegido com um firewall interno no nível de rede e a verificação de certificado mútua no nível do aplicativo. Você pode [localizar o endereço IP do ponto de extremidade de gerenciamento](sql-database-managed-instance-find-management-endpoint-ip-address.md).

Quando as conexões são iniciadas de dentro da instância gerenciada (backup, log de auditoria) parece que o tráfego origina-se do endereço IP público de ponto de extremidade de gerenciamento. Você pode limitar o acesso para serviços públicos da instância gerenciada, definindo regras de firewall para permitir somente o endereço IP de Instância Gerenciada. Localize mais informações sobre o método que pode [verificar se o firewall interno da Instância Gerenciada](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> Isso não se aplica à configuração de regras de firewall para os serviços do Azure que estão na mesma região que a Instância Gerenciada do que a plataforma do Azure tem uma otimização para o tráfego que passa entre os serviços que são colocados.

## <a name="network-requirements"></a>Requisitos de rede

Para implantar uma Instância Gerenciada, em uma sub-rede dedicada (sub-rede da Instância Gerenciada) dentro da rede virtual em conformidade com os seguintes requisitos:
- **Sub-rede dedicada**: A sub-rede da Instância Gerenciada não deve conter nenhum outro serviço de nuvem associado a ela e não deve ser uma sub-rede de Gateway. Não será possível criar a Instância Gerenciada na sub-rede que contenha outros recursos, além da Instância Gerenciada, e não é possível adicionar outros recursos à sub-rede posteriormente.
- **Grupo de Segurança de Rede Compatível (NSG)**: Um NSG associado à sub-rede de uma Instância Gerenciada deve conter as regras mostradas nas tabelas a seguir (Regras de segurança de entrada obrigatórias e Regras de segurança de saída obrigatórias) na frente de qualquer outra regra. Você pode usar um NSG para controlar totalmente o acesso ao ponto de extremidade de dados da Instância Gerenciada filtrando o tráfego na porta 1433. 
- **Tabela de rotas definidas pelo usuário (UDR) compatível**: A sub-rede da Instância Gerenciada deve ter uma tabela de rotas do usuário com **0.0.0.0/0 da Internet de Próximo Salto** como a UDR obrigatória atribuída a ela. Além disso, você pode adicionar uma UDR que roteia o tráfego que tem intervalos IP privados locais como um destino por meio do gateway de rede virtual ou NVA (dispositivo de rede virtual). 
- **DNS personalizado opcional**: Se o DNS personalizado for especificado na rede virtual, o endereço IP do resolvedor recursivo do Azure (como 168.63.129.16) deverá ser adicionado à lista. Para obter mais informações, consulte [Configurar DNS personalizado](sql-database-managed-instance-custom-dns.md). O servidor DNS personalizado deve ser capaz de resolver nomes de host nos seguintes domínios e seus subdomínios: *microsoft.com*, *windows.net*, *windows.com*, *msocsp.com*, *digicert.com*, *live.com*, *microsoftonline.com* e *microsoftonline-p.com*. 
- **Pontos de extremidade de serviço**: A sub-rede da Instância Gerenciada não deve ter um Ponto de extremidade de serviço associado a ela. Certifique-se de que a opção de pontos de extremidade de serviço esteja desabilitada ao criar a rede virtual.
- **Endereço IP suficientes**: A sub-rede da Instância Gerenciada deve ter suporte para no mínimo 16 endereços IP (o mínimo recomendado é de 32 endereços IP). Para obter mais informações, consulte [Determinar o tamanho da sub-rede para Instâncias Gerenciadas](sql-database-managed-instance-determine-size-vnet-subnet.md). Você pode implantar instâncias gerenciadas na [rede existente](sql-database-managed-instance-configure-vnet-subnet.md) depois de você configurá-lo para satisfazer [Requisitos de Rede de Instância Gerenciada](#network-requirements), ou crie um [nova rede e sub-rede](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Não será possível implantar nova Instância Gerenciada se a sub-rede de destino não estiver compatível com todos esses requisitos. Quando uma Instância Gerenciada é criada, uma *Política de intenção de rede* é aplicada à sub-rede para evitar alterações fora de conformidade na configuração de rede. Depois que a última instância for removida da sub-rede, a *Política de intenção de rede* também será removida

### <a name="mandatory-inbound-security-rules"></a>Regras de segurança de entrada obrigatórias 

| NOME       |Porta                        |Protocolo|Fonte           |Destino|Ação|
|------------|----------------------------|--------|-----------------|-----------|------|
|gerenciamento  |9000, 9003, 1438, 1440, 1452|TCP     |Qualquer              |Qualquer        |PERMITIR |
|mi_subnet   |Qualquer                         |Qualquer     |SUB-REDE DA MI        |Qualquer        |PERMITIR |
|health_probe|Qualquer                         |Qualquer     |AzureLoadBalancer|Qualquer        |PERMITIR |

### <a name="mandatory-outbound-security-rules"></a>Regras de segurança de saída obrigatórias 

| NOME       |Porta          |Protocolo|Fonte           |Destino|Ação|
|------------|--------------|--------|-----------------|-----------|------|
|gerenciamento  |80, 443, 12000|TCP     |Qualquer              |Internet   |PERMITIR |
|mi_subnet   |Qualquer           |Qualquer     |Qualquer              |SUB-REDE DA MI  |PERMITIR |

  > [!Note]
  > MI SUBNET refere-se ao intervalo de endereços IP para a sub-rede no formato 10.x.x.x/y. Essas informações podem ser encontradas no portal do Azure (por meio de propriedades de sub-rede).
  
  > [!Note]
  > Embora as regras de segurança de entrada obrigatórias permitam o tráfego de _Qualquer_ origem nas portas 9000, 9003, 1438, 1440, 1452 essas portas são protegidas por firewall interno. Este [artigo](sql-database-managed-instance-find-management-endpoint-ip-address.md) mostra como você pode descobrir o endereço IP do ponto de extremidade de gerenciamento e verificar as regras de firewall. 
  
  > [!Note]
  > Se estiver usando uma replicação transacional na instância gerenciada e qualquer banco de dados na instância gerenciada for usado como publicador ou distribuidor, a porta 445 (TCP de saída) também precisa ser aberto nas regras de segurança da sub-rede para acessar o compartilhamento de arquivos do Azure.
  
## <a name="next-steps"></a>Próximas etapas

- Para uma visão geral, consulte  [O que é uma instância gerenciada](sql-database-managed-instance.md)
- Saiba como [configurar a nova rede virtual](sql-database-managed-instance-create-vnet-subnet.md) ou [configurarVNet](sql-database-managed-instance-configure-vnet-subnet.md) onde você pode implantar instâncias gerenciadas.
- [Calcular o tamanho da sub-rede](sql-database-managed-instance-determine-size-vnet-subnet.md) onde você irá implantar Instâncias Gerenciadas. 
- Para um início rápido, consulte como criar a instância gerenciada:
  - No [portal do Azure](sql-database-managed-instance-get-started.md)
  - Usando o [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/)
  - usando [modelo do Resource Manager](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/)
  - usando [modelo do Resource Manager (jumpbox com o SSMS incluído)](https://portal.azure.com/)
