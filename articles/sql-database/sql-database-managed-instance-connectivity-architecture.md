---
title: Arquitetura de conectividade para uma instância gerenciada SQL do Azure | Microsoft Docs
description: Aprenda sobre comunicação de instância de banco de dados do SQL Azure gerenciado e arquitetura de conectividade, bem como os componentes de direcionam o tráfego para a instância gerenciada.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 04/16/2019
ms.openlocfilehash: 399e2585f541f28b3880e69b508cfd643b2f2263
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64686285"
---
# <a name="connectivity-architecture-for-a-managed-instance-in-azure-sql-database"></a>Arquitetura de conectividade para uma instância gerenciada SQL do Azure

Este artigo explica a comunicação em uma instância gerenciada do banco de dados SQL. Ele também descreve a arquitetura de conectividade e como os componentes de direcionam o tráfego para a instância gerenciada.  

A instância gerenciada do banco de dados SQL é colocada dentro da rede virtual do Azure e a sub-rede dedicada para instâncias gerenciadas. Essa implantação fornece:

- Um endereço IP privado seguro.
- A capacidade de se conectar a uma rede local a uma instância gerenciada.
- A capacidade de se conectar a uma instância gerenciada para um servidor vinculado ou outro armazenamento de dados no local.
- A capacidade de se conectar a uma instância gerenciada a recursos do Azure.

## <a name="communication-overview"></a>Visão geral da comunicação

O diagrama a seguir mostra as entidades que se conectam a uma instância gerenciada. Ele também mostra os recursos que precisam se comunicar com a instância gerenciada. O processo de comunicação na parte inferior do diagrama representa aplicativos de cliente e ferramentas que se conectar à instância gerenciada como fontes de dados.  

![Entidades na arquitetura de conectividade](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

Uma instância gerenciada é uma plataforma como uma oferta de serviço (PaaS). A Microsoft usa agentes automatizados (gerenciamento, implantação e manutenção) para gerenciar esse serviço com base em fluxos de dados de telemetria. Como a Microsoft é responsável pelo gerenciamento, os clientes não podem acessar as máquinas virtuais de cluster de instância gerenciada por meio do protocolo RDP (Remote Desktop).

Algumas operações iniciadas por aplicativos ou usuários finais podem exigir do SQL Server gerenciado de instâncias para interagir com a plataforma. Um caso é a criação de um banco de dados de instância gerenciada. Esse recurso é exposto por meio do portal do Azure, PowerShell, CLI do Azure e a API REST.

Instâncias gerenciadas dependem de serviços do Azure como armazenamento do Azure para backups, Hubs de eventos de telemetria, Azure Active Directory para autenticação, Azure Key Vault para criptografia de dados transparente (TDE) e alguns serviços da plataforma Azure que fornecem recursos de segurança e capacidade de suporte. As instâncias gerenciadas faz conexões com esses serviços.

Todas as comunicações são criptografadas e assinados usando certificados. Para verificar a confiabilidade de comunicação partes, gerenciadas instâncias constantemente Verifique se esses certificados por meio de listas de revogação de certificado. Se os certificados são revogados, a instância gerenciada fecha as conexões para proteger os dados.

## <a name="high-level-connectivity-architecture"></a>Arquitetura de alto nível de conectividade

Em um alto nível, uma instância gerenciada é um conjunto de componentes de serviço. Esses componentes são hospedados em um conjunto dedicado de máquinas virtuais isoladas que são executados dentro da sub-rede de rede virtual do cliente. Essas máquinas formam um cluster virtual.

Um cluster virtual pode hospedar várias instâncias gerenciadas. Se necessário, o cluster se expande automaticamente ou contratos quando o cliente alterar o número de instâncias provisionados na sub-rede.

Aplicativos cliente podem se conectar a instâncias gerenciadas e podem consultar e atualizar bancos de dados dentro da rede virtual, rede virtual emparelhada, ou de rede conectados por VPN ou ExpressRoute do Azure. Essa rede deve usar um ponto de extremidade e um endereço IP privado.  

![Diagrama de arquitetura de conectividade](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Serviços de implantação e gerenciamento do Microsoft executar fora da rede virtual. Uma instância gerenciada e serviços da Microsoft conectam através de pontos de extremidade que tem endereços IP públicos. Quando uma instância gerenciada cria uma conexão de saída, na extremidade de recebimento torna de tradução de endereço de rede (NAT), a aparência de conexão, como ele é proveniente de endereço IP público.

Fluxos de tráfego de gerenciamento por meio da rede virtual do cliente. Isso significa que elementos da infraestrutura da rede virtual podem prejudicar o tráfego de gerenciamento, tornando a instância falhar e se tornar indisponível.

> [!IMPORTANT]
> Para melhorar a experiência do cliente e a disponibilidade do serviço, a Microsoft aplica uma política de intenção de rede em elementos de infraestrutura de rede virtual do Azure. A política pode afetar o funcionamento da instância gerenciada. Esse mecanismo de plataforma transparentemente comunica-se os requisitos de rede para os usuários. Meta principal da política é para evitar erros de configuração de rede e para garantir operações de instância gerenciada normal. Quando você exclui uma instância gerenciada, a política de intenção de rede também é removida.

## <a name="virtual-cluster-connectivity-architecture"></a>Arquitetura de conectividade do cluster virtual

Vamos dar um aprofundamento na arquitetura de conectividade para instâncias gerenciadas. O diagrama a seguir mostra o layout conceitual do cluster virtual.

![Arquitetura de conectividade do cluster virtual](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

Os clientes se conectam a uma instância gerenciada usando um nome de host que tem a forma `<mi_name>.<dns_zone>.database.windows.net`. Esse nome de host resolve para um endereço IP privado, embora ele é registrado em uma zona de nome DNS (sistema) de domínio público e pode ser resolvido publicamente. O `zone-id` é gerado automaticamente quando você cria o cluster. Se um cluster recém-criado hospeda uma instância gerenciada secundária, ele compartilha sua ID de zona com o cluster primário. Para obter mais informações, consulte [usar grupos de failover automático para habilitar o failover transparente e coordenado de vários bancos de dados](sql-database-auto-failover-group.md##enabling-geo-replication-between-managed-instances-and-their-vnets).

Esse endereço IP pertence ao balanceador de carga interno da instância gerenciada. O balanceador de carga direciona o tráfego para gateway a instância gerenciada. Como várias instâncias gerenciadas podem ser executado dentro do mesmo cluster, o gateway usa o nome do host da instância gerenciada para redirecionar o tráfego para o serviço de mecanismo SQL correto.

Serviços de gerenciamento e implantação de se conectar a uma instância gerenciada usando um [ponto de extremidade de gerenciamento](#management-endpoint) balanceador de carga é mapeado para externo. Tráfego é roteado para os nós somente se ela é recebida em um conjunto predefinido de portas que usam somente os componentes de gerenciamento da instância gerenciada. Configurar um firewall interno em nós para permitir o tráfego apenas de intervalos de IP da Microsoft. Certificados de autenticam mutuamente toda a comunicação entre componentes de gerenciamento e o plano de gerenciamento.

## <a name="management-endpoint"></a>Ponto de extremidade de gerenciamento

A Microsoft gerencia a instância gerenciada usando um ponto de extremidade de gerenciamento. Esse ponto de extremidade está dentro do cluster virtual da instância. O ponto de extremidade de gerenciamento é protegido por um firewall interno no nível da rede. No nível do aplicativo, ele é protegido pela verificação de certificado mútuo. Para localizar o endereço IP do ponto de extremidade, consulte [determinar o endereço IP do ponto de extremidade de gerenciamento](sql-database-managed-instance-find-management-endpoint-ip-address.md).

Quando conexões iniciar dentro da instância gerenciada (assim como acontece com backups e logs de auditoria), o tráfego é exibido iniciar a partir do endereço IP público de um ponto de extremidade de gerenciamento. Você pode limitar o acesso a serviços públicos de uma instância gerenciada, definindo regras de firewall para permitir que somente o endereço IP da instância gerenciada. Para obter mais informações, consulte [Verifique se o firewall interno da instância gerenciada](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> O tráfego que vai para os serviços do Azure que estão dentro da região da instância gerenciada é otimizado e por esse motivo não NATed para gerenciado gerenciamento ponto de extremidade público endereço IP de instância. Por esse motivo se você precisar usar regras de firewall baseadas em IP, mais comumente para o armazenamento, o serviço precisa estar em uma região diferente da instância gerenciada.

## <a name="network-requirements"></a>Requisitos de rede

Implante uma instância gerenciada em uma sub-rede dedicada dentro da rede virtual. A sub-rede deve ter as seguintes características:

- **Sub-rede dedicada:** Sub-rede da instância gerenciada não pode conter qualquer outro serviço de nuvem que está associada com ele, e não pode ser uma sub-rede de gateway. A sub-rede não pode conter qualquer recurso, mas a instância gerenciada e, posteriormente, você não pode adicionar recursos na sub-rede.
- **NSG (Grupo de Segurança de Rede):** Um NSG que está associada com a rede virtual deve definir [regras de segurança de entrada](#mandatory-inbound-security-rules) e [regras de segurança de saída](#mandatory-outbound-security-rules) antes de qualquer outra regra. Você pode usar um NSG para controlar o acesso ao ponto de extremidade da instância gerenciada dados filtrando o tráfego na porta 1433 e portas de 11000-11999 quando a instância gerenciada está configurada para redirecionar conexões.
- **Tabela de rotas definida (UDR) de usuário:** Uma tabela UDR que está associada com a rede virtual deve incluir específico [entradas](#user-defined-routes).
- **Nenhum ponto de extremidade de serviço:** Nenhum ponto de extremidade de serviço deve ser associado com a sub-rede da instância gerenciada. Certifique-se de que a opção de pontos de extremidade de serviço é desabilitada quando você cria a rede virtual.
- **Endereços IP suficientes:** A sub-rede da instância gerenciada deve ter pelo menos 16 endereços IP. O mínimo recomendado é 32 endereços IP. Para obter mais informações, consulte [determinar o tamanho da sub-rede para instâncias gerenciadas](sql-database-managed-instance-determine-size-vnet-subnet.md). Você pode implantar instâncias gerenciadas na [a rede existente](sql-database-managed-instance-configure-vnet-subnet.md) depois de você configurá-lo para satisfazer [os requisitos de rede para instâncias gerenciadas](#network-requirements). Caso contrário, crie uma [nova rede e sub-rede](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> É possível implantar uma nova instância gerenciada se a sub-rede de destino não tem as seguintes características. Quando você cria uma instância gerenciada, uma política de intenção de rede é aplicada na sub-rede para evitar alterações fora de conformidade para a configuração de rede. Após a última instância é removida da sub-rede, a política de intenção de rede também será removida.

### <a name="mandatory-inbound-security-rules"></a>Regras de segurança de entrada obrigatórias

| NOME       |Porta                        |Protocol|Fonte           |Destino|Ação|
|------------|----------------------------|--------|-----------------|-----------|------|
|gerenciamento  |9000, 9003, 1438, 1440, 1452|TCP     |Qualquer              |SUB-REDE DA MI  |PERMITIR |
|mi_subnet   |Qualquer                         |Qualquer     |SUB-REDE DA MI        |SUB-REDE DA MI  |PERMITIR |
|health_probe|Qualquer                         |Qualquer     |AzureLoadBalancer|SUB-REDE DA MI  |PERMITIR |

### <a name="mandatory-outbound-security-rules"></a>Regras de segurança de saída obrigatórias

| NOME       |Porta          |Protocol|Fonte           |Destino|Ação|
|------------|--------------|--------|-----------------|-----------|------|
|gerenciamento  |80, 443, 12000|TCP     |SUB-REDE DA MI        |AzureCloud |PERMITIR |
|mi_subnet   |Qualquer           |Qualquer     |SUB-REDE DA MI        |SUB-REDE DA MI  |PERMITIR |

> [!IMPORTANT]
> Verifique se há apenas uma regra de entrada para portas 9000, 9003, 1438, 1440, 1452 e uma regra de saída para as portas 80, 443, 12000. Gerenciado por meio do Azure Resource Manager as implantações falharão se as regras de entrada e saídas são configuradas separadamente para cada porta de provisionamento de instância. Se essas portas estiverem em regras separadas, a implantação falhará com o código de erro `VnetSubnetConflictWithIntendedPolicy`

\* Subrede de MI refere-se ao intervalo de endereços IP para a sub-rede na 10.x.x.x/y formulário. Você pode encontrar essas informações no portal do Azure, nas propriedades de sub-rede.

> [!IMPORTANT]
> Embora as regras de segurança de entrada necessário permitirem o tráfego de _qualquer_ 9000 de origem nas portas, 9003, 1438, 1440 e 1452, essas portas são protegidas por um firewall interno. Para obter mais informações, consulte [determinar o endereço do ponto de extremidade de gerenciamento](sql-database-managed-instance-find-management-endpoint-ip-address.md).
> [!NOTE]
> Se você usa a replicação transacional em uma instância gerenciada e, se você usar qualquer instância de banco de dados como um publicador ou um distribuidor, abre a porta 445 (TCP de saída) nas regras de segurança da sub-rede. Essa porta permitirá acesso ao compartilhamento de arquivos do Azure.

### <a name="user-defined-routes"></a>rotas definidas pelo usuário

|NOME|Prefixo de endereço|Próximo salto|
|----|--------------|-------|
|subnet_to_vnetlocal|SUB-REDE DA MI|Rede virtual|
|mi-13-64-11-nexthop-internet|13.64.0.0/11|Internet|
|mi-13-96-13-nexthop-internet|13.96.0.0/13|Internet|
|mi-13-104-14-nexthop-internet|13.104.0.0/14|Internet|
|mi-20-8-nexthop-internet|20.0.0.0/8|Internet|
|mi-23-96-13-nexthop-internet|23.96.0.0/13|Internet|
|mi-40-64-10-nexthop-internet|40.64.0.0/10|Internet|
|mi-42-159-16-nexthop-internet|42.159.0.0/16|Internet|
|mi-51-8-nexthop-internet|51.0.0.0/8|Internet|
|mi-52-8-nexthop-internet|52.0.0.0/8|Internet|
|mi-64-4-18-nexthop-internet|64.4.0.0/18|Internet|
|mi-65-52-14-nexthop-internet|65.52.0.0/14|Internet|
|mi-66-119-144-20-nexthop-internet|66.119.144.0/20|Internet|
|mi-70-37-17-nexthop-internet|70.37.0.0/17|Internet|
|mi-70-37-128-18-nexthop-internet|70.37.128.0/18|Internet|
|mi-91-190-216-21-nexthop-internet|91.190.216.0/21|Internet|
|mi-94-245-64-18-nexthop-internet|94.245.64.0/18|Internet|
|mi-103-9-8-22-nexthop-internet|103.9.8.0/22|Internet|
|mi-103-25-156-22-nexthop-internet|103.25.156.0/22|Internet|
|mi-103-36-96-22-nexthop-internet|103.36.96.0/22|Internet|
|mi-103-255-140-22-nexthop-internet|103.255.140.0/22|Internet|
|mi-104-40-13-nexthop-internet|104.40.0.0/13|Internet|
|mi-104-146-15-nexthop-internet|104.146.0.0/15|Internet|
|mi-104-208-13-nexthop-internet|104.208.0.0/13|Internet|
|mi-111-221-16-20-nexthop-internet|111.221.16.0/20|Internet|
|mi-111-221-64-18-nexthop-internet|111.221.64.0/18|Internet|
|mi-129-75-16-nexthop-internet|129.75.0.0/16|Internet|
|mi-131-253-16-nexthop-internet|131.253.0.0/16|Internet|
|mi-132-245-16-nexthop-internet|132.245.0.0/16|Internet|
|mi-134-170-16-nexthop-internet|134.170.0.0/16|Internet|
|mi-134-177-16-nexthop-internet|134.177.0.0/16|Internet|
|mi-137-116-15-nexthop-internet|137.116.0.0/15|Internet|
|mi-137-135-16-nexthop-internet|137.135.0.0/16|Internet|
|mi-138-91-16-nexthop-internet|138.91.0.0/16|Internet|
|mi-138-196-16-nexthop-internet|138.196.0.0/16|Internet|
|mi-139-217-16-nexthop-internet|139.217.0.0/16|Internet|
|mi-139-219-16-nexthop-internet|139.219.0.0/16|Internet|
|mi-141-251-16-nexthop-internet|141.251.0.0/16|Internet|
|mi-146-147-16-nexthop-internet|146.147.0.0/16|Internet|
|mi-147-243-16-nexthop-internet|147.243.0.0/16|Internet|
|mi-150-171-16-nexthop-internet|150.171.0.0/16|Internet|
|mi-150-242-48-22-nexthop-internet|150.242.48.0/22|Internet|
|mi-157-54-15-nexthop-internet|157.54.0.0/15|Internet|
|mi-157-56-14-nexthop-internet|157.56.0.0/14|Internet|
|mi-157-60-16-nexthop-internet|157.60.0.0/16|Internet|
|mi-167-220-16-nexthop-internet|167.220.0.0/16|Internet|
|mi-168-61-16-nexthop-internet|168.61.0.0/16|Internet|
|mi-168-62-15-nexthop-internet|168.62.0.0/15|Internet|
|mi-191-232-13-nexthop-internet|191.232.0.0/13|Internet|
|mi-192-32-16-nexthop-internet|192.32.0.0/16|Internet|
|mi-192-48-225-24-nexthop-internet|192.48.225.0/24|Internet|
|mi-192-84-159-24-nexthop-internet|192.84.159.0/24|Internet|
|mi-192-84-160-23-nexthop-internet|192.84.160.0/23|Internet|
|mi-192-100-102-24-nexthop-internet|192.100.102.0/24|Internet|
|mi-192-100-103-24-nexthop-internet|192.100.103.0/24|Internet|
|mi-192-197-157-24-nexthop-internet|192.197.157.0/24|Internet|
|mi-193-149-64-19-nexthop-internet|193.149.64.0/19|Internet|
|mi-193-221-113-24-nexthop-internet|193.221.113.0/24|Internet|
|mi-194-69-96-19-nexthop-internet|194.69.96.0/19|Internet|
|mi-194-110-197-24-nexthop-internet|194.110.197.0/24|Internet|
|mi-198-105-232-22-nexthop-internet|198.105.232.0/22|Internet|
|mi-198-200-130-24-nexthop-internet|198.200.130.0/24|Internet|
|mi-198-206-164-24-nexthop-internet|198.206.164.0/24|Internet|
|mi-199-60-28-24-nexthop-internet|199.60.28.0/24|Internet|
|mi-199-74-210-24-nexthop-internet|199.74.210.0/24|Internet|
|mi-199-103-90-23-nexthop-internet|199.103.90.0/23|Internet|
|mi-199-103-122-24-nexthop-internet|199.103.122.0/24|Internet|
|mi-199-242-32-20-nexthop-internet|199.242.32.0/20|Internet|
|mi-199-242-48-21-nexthop-internet|199.242.48.0/21|Internet|
|mi-202-89-224-20-nexthop-internet|202.89.224.0/20|Internet|
|mi-204-13-120-21-nexthop-internet|204.13.120.0/21|Internet|
|mi-204-14-180-22-nexthop-internet|204.14.180.0/22|Internet|
|mi-204-79-135-24-nexthop-internet|204.79.135.0/24|Internet|
|mi-204-79-179-24-nexthop-internet|204.79.179.0/24|Internet|
|mi-204-79-181-24-nexthop-internet|204.79.181.0/24|Internet|
|mi-204-79-188-24-nexthop-internet|204.79.188.0/24|Internet|
|mi-204-79-195-24-nexthop-internet|204.79.195.0/24|Internet|
|mi-204-79-196-23-nexthop-internet|204.79.196.0/23|Internet|
|mi-204-79-252-24-nexthop-internet|204.79.252.0/24|Internet|
|mi-204-152-18-23-nexthop-internet|204.152.18.0/23|Internet|
|mi-204-152-140-23-nexthop-internet|204.152.140.0/23|Internet|
|mi-204-231-192-24-nexthop-internet|204.231.192.0/24|Internet|
|mi-204-231-194-23-nexthop-internet|204.231.194.0/23|Internet|
|mi-204-231-197-24-nexthop-internet|204.231.197.0/24|Internet|
|mi-204-231-198-23-nexthop-internet|204.231.198.0/23|Internet|
|mi-204-231-200-21-nexthop-internet|204.231.200.0/21|Internet|
|mi-204-231-208-20-nexthop-internet|204.231.208.0/20|Internet|
|mi-204-231-236-24-nexthop-internet|204.231.236.0/24|Internet|
|mi-205-174-224-20-nexthop-internet|205.174.224.0/20|Internet|
|mi-206-138-168-21-nexthop-internet|206.138.168.0/21|Internet|
|mi-206-191-224-19-nexthop-internet|206.191.224.0/19|Internet|
|mi-207-46-16-nexthop-internet|207.46.0.0/16|Internet|
|mi-207-68-128-18-nexthop-internet|207.68.128.0/18|Internet|
|mi-208-68-136-21-nexthop-internet|208.68.136.0/21|Internet|
|mi-208-76-44-22-nexthop-internet|208.76.44.0/22|Internet|
|mi-208-84-21-nexthop-internet|208.84.0.0/21|Internet|
|mi-209-240-192-19-nexthop-internet|209.240.192.0/19|Internet|
|mi-213-199-128-18-nexthop-internet|213.199.128.0/18|Internet|
|mi-216-32-180-22-nexthop-internet|216.32.180.0/22|Internet|
|mi-216-220-208-20-nexthop-internet|216.220.208.0/20|Internet|
||||

Além disso, você pode adicionar entradas à tabela de rotas para rotear o tráfego que tem intervalos IP privados em locais como um destino por meio do gateway de rede virtual ou um dispositivo de rede virtual (NVA).

Se a rede virtual inclui um DNS personalizado, o servidor DNS personalizado deve ser capaz de resolver nomes de host em \*. core.windows.net zona. Usando os recursos adicionais, como autenticação do Azure AD pode exigir resolver FQDNs adicionais. Para obter mais informações, consulte [configurar um DNS personalizado](sql-database-managed-instance-custom-dns.md).

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral, consulte [avançadas de segurança de dados do banco de dados do SQL](sql-database-managed-instance.md).
- Saiba como [configurar uma nova rede virtual do Azure](sql-database-managed-instance-create-vnet-subnet.md) ou um [rede virtual do Azure existente](sql-database-managed-instance-configure-vnet-subnet.md) onde você pode implantar instâncias gerenciadas.
- [Calcular o tamanho da sub-rede](sql-database-managed-instance-determine-size-vnet-subnet.md) onde você deseja implantar as instâncias gerenciadas.
- Saiba como criar uma instância gerenciada:
  - No [portal do Azure](sql-database-managed-instance-get-started.md).
  - Usando [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md).
  - Usando [um modelo do Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).
  - Usando [um modelo do Resource Manager (usando o JumpBox, com o SSMS incluído)](https://portal.azure.com/). 
