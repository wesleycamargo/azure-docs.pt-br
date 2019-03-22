---
title: Arquitetura de conectividade para uma instância gerenciada SQL do Azure | Microsoft Docs
description: Aprenda sobre comunicação de instância de banco de dados do SQL Azure gerenciado e arquitetura de conectividade, bem como os componentes de direcionam o tráfego para a instância gerenciada.
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
ms.date: 02/26/2019
ms.openlocfilehash: 6ef020ff1054416e2b9af5af824b9aa27f0b1e64
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2019
ms.locfileid: "57247232"
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

Instâncias gerenciadas dependem de serviços do Azure como o armazenamento do Azure para backups, o barramento de serviço do Azure para a telemetria, Azure Active Directory para autenticação e o Azure Key Vault para criptografia de dados transparente (TDE). As instâncias gerenciadas fazem conexões com esses serviços.

Todas as comunicações usam certificados de criptografia e assinatura. Para verificar a confiabilidade de comunicação partes, gerenciadas instâncias constantemente verificar esses certificados entrando em uma autoridade de certificação. Se os certificados são revogados ou não podem ser verificados, a instância gerenciada fecha as conexões para proteger os dados.

## <a name="high-level-connectivity-architecture"></a>Arquitetura de alto nível de conectividade

Em um alto nível, uma instância gerenciada é um conjunto de componentes de serviço. Esses componentes são hospedados em um conjunto dedicado de máquinas virtuais isoladas que são executados dentro da sub-rede de rede virtual do cliente. Essas máquinas formam um cluster virtual.

Um cluster virtual pode hospedar várias instâncias gerenciadas. Se necessário, o cluster se expande automaticamente ou contratos quando o cliente alterar o número de instâncias provisionados na sub-rede.

Aplicativos cliente podem se conectar a instâncias gerenciadas e podem consultar e atualizar bancos de dados somente se eles são executados dentro da rede virtual, virtual emparelhada rede, ou conectados por VPN ou ExpressRoute do Azure. Essa rede deve usar um ponto de extremidade e um endereço IP privado.  

![Diagrama de arquitetura de conectividade](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Serviços de implantação e gerenciamento do Microsoft executar fora da rede virtual. Uma instância gerenciada e serviços da Microsoft conectam através de pontos de extremidade que tem endereços IP públicos. Quando uma instância gerenciada cria uma conexão de saída, na extremidade de recebimento torna de tradução de endereço de rede (NAT), a aparência de conexão, como ele é proveniente de endereço IP público.

Fluxos de tráfego de gerenciamento por meio da rede virtual do cliente. Isso significa que elementos da infraestrutura da rede virtual podem prejudicar o tráfego de gerenciamento, tornando a instância falhar e se tornar indisponível.

> [!IMPORTANT]
> Para melhorar a experiência do cliente e a disponibilidade do serviço, a Microsoft aplica uma política de intenção de rede em elementos de infraestrutura de rede virtual do Azure. A política pode afetar o funcionamento da instância gerenciada. Esse mecanismo de plataforma transparentemente comunica-se os requisitos de rede para os usuários. Meta principal da política é para evitar erros de configuração de rede e para garantir operações de instância gerenciada normal. Quando você exclui uma instância gerenciada, a política de intenção de rede também é removida.

## <a name="virtual-cluster-connectivity-architecture"></a>Arquitetura de conectividade do cluster virtual

Vamos dar um aprofundamento na arquitetura de conectividade para instâncias gerenciadas. O diagrama a seguir mostra o layout conceitual do cluster virtual.

![Arquitetura de conectividade do cluster virtual](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

Os clientes se conectam a uma instância gerenciada usando um nome de host que tem a forma `<mi_name>.<dns_zone>.database.windows.net`. Esse nome de host resolve para um endereço IP privado, embora ele é registrado em uma zona de nome DNS (sistema) de domínio público e pode ser resolvido publicamente. O `zone-id` é gerado automaticamente quando você cria o cluster. Se um cluster recém-criado hospeda uma instância gerenciada secundária, ele compartilha sua ID de zona com o cluster primário. Para obter mais informações, consulte [usar grupos de autofailover para habilitar o failover transparente e coordenado de vários bancos de dados](sql-database-auto-failover-group.md##enabling-geo-replication-between-managed-instances-and-their-vnets).

Esse endereço IP pertence ao balanceador de carga interno da instância gerenciada. O balanceador de carga direciona o tráfego para gateway a instância gerenciada. Como várias instâncias gerenciadas podem ser executado dentro do mesmo cluster, o gateway usa o nome do host da instância gerenciada para redirecionar o tráfego para o serviço de mecanismo SQL correto.

Serviços de gerenciamento e implantação de se conectar a uma instância gerenciada usando um [ponto de extremidade de gerenciamento](#management-endpoint) balanceador de carga é mapeado para externo. Tráfego é roteado para os nós somente se ela é recebida em um conjunto predefinido de portas que usam somente os componentes de gerenciamento da instância gerenciada. Configurar um firewall interno em nós para permitir o tráfego apenas de intervalos de IP da Microsoft. Certificados de autenticam mutuamente toda a comunicação entre componentes de gerenciamento e o plano de gerenciamento.

## <a name="management-endpoint"></a>Ponto de extremidade de gerenciamento

A Microsoft gerencia a instância gerenciada usando um ponto de extremidade de gerenciamento. Esse ponto de extremidade está dentro do cluster virtual da instância. O ponto de extremidade de gerenciamento é protegido por um firewall interno no nível da rede. No nível do aplicativo, ele é protegido pela verificação de certificado mútuo. Para localizar o endereço IP do ponto de extremidade, consulte [determinar o endereço IP do ponto de extremidade de gerenciamento](sql-database-managed-instance-find-management-endpoint-ip-address.md).

Quando conexões iniciar dentro da instância gerenciada (assim como acontece com backups e logs de auditoria), o tráfego é exibido iniciar a partir do endereço IP público de um ponto de extremidade de gerenciamento. Você pode limitar o acesso a serviços públicos de uma instância gerenciada, definindo regras de firewall para permitir que somente o endereço IP da instância gerenciada. Para obter mais informações, consulte [Verifique se o firewall interno da instância gerenciada](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> Ao contrário do firewall para conexões que iniciar dentro da instância gerenciada, os serviços do Azure que estão dentro região da instância gerenciada tem um firewall que é otimizado para o tráfego que passa entre esses serviços.

## <a name="network-requirements"></a>Requisitos de rede

Implante uma instância gerenciada em uma sub-rede dedicada dentro da rede virtual. A sub-rede deve ter as seguintes características:

- **Sub-rede dedicada:** Sub-rede da instância gerenciada não pode conter qualquer outro serviço de nuvem que está associada com ele, e não pode ser uma sub-rede de gateway. A sub-rede não pode conter qualquer recurso, mas a instância gerenciada e, posteriormente, você não pode adicionar recursos na sub-rede.
- **NSG (Grupo de Segurança de Rede):** Um NSG que está associada com a rede virtual deve definir [regras de segurança de entrada](#mandatory-inbound-security-rules) e [regras de segurança de saída](#mandatory-outbound-security-rules) antes de qualquer outra regra. Você pode usar um NSG para controlar o acesso ao ponto de extremidade da instância gerenciada dados filtrando o tráfego na porta 1433.
- **Tabela de rotas definida (UDR) de usuário:** Uma tabela UDR que está associada com a rede virtual deve incluir específico [entradas](#user-defined-routes).
- **Nenhum ponto de extremidade de serviço:** Nenhum ponto de extremidade de serviço deve ser associado com a sub-rede da instância gerenciada. Certifique-se de que a opção de pontos de extremidade de serviço é desabilitada quando você cria a rede virtual.
- **Endereços IP suficientes:** A sub-rede da instância gerenciada deve ter pelo menos 16 endereços IP. O mínimo recomendado é 32 endereços IP. Para obter mais informações, consulte [determinar o tamanho da sub-rede para instâncias gerenciadas](sql-database-managed-instance-determine-size-vnet-subnet.md). Você pode implantar instâncias gerenciadas na [a rede existente](sql-database-managed-instance-configure-vnet-subnet.md) depois de você configurá-lo para satisfazer [os requisitos de rede para instâncias gerenciadas](#network-requirements). Caso contrário, crie uma [nova rede e sub-rede](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> É possível implantar uma nova instância gerenciada se a sub-rede de destino não tem as seguintes características. Quando você cria uma instância gerenciada, uma política de intenção de rede é aplicada na sub-rede para evitar alterações fora de conformidade para a configuração de rede. Após a última instância é removida da sub-rede, a política de intenção de rede também será removida.

### <a name="mandatory-inbound-security-rules"></a>Regras de segurança de entrada obrigatórias

| Nome       |Porta                        |Protocolo|Fonte           |Destino|Ação|
|------------|----------------------------|--------|-----------------|-----------|------|
|gerenciamento  |9000, 9003, 1438, 1440, 1452|TCP     |Qualquer              |Qualquer        |Permitir |
|mi_subnet   |Qualquer                         |Qualquer     |SUB-REDE DA MI        |Qualquer        |Permitir |
|health_probe|Qualquer                         |Qualquer     |AzureLoadBalancer|Qualquer        |Permitir |

### <a name="mandatory-outbound-security-rules"></a>Regras de segurança de saída obrigatórias

| Nome       |Porta          |Protocolo|Fonte           |Destino|Ação|
|------------|--------------|--------|-----------------|-----------|------|
|gerenciamento  |80, 443, 12000|TCP     |Qualquer              |Internet   |Permitir |
|mi_subnet   |Qualquer           |Qualquer     |Qualquer              |SUB-REDE DE MI *  |Permitir |

\* Subrede de MI refere-se ao intervalo de endereços IP para a sub-rede na 10.x.x.x/y formulário. Você pode encontrar essas informações no portal do Azure, nas propriedades de sub-rede.

> [!IMPORTANT]
> Embora as regras de segurança de entrada necessário permitirem o tráfego de _qualquer_ 9000 de origem nas portas, 9003, 1438, 1440 e 1452, essas portas são protegidas por um firewall interno. Para obter mais informações, consulte [determinar o endereço do ponto de extremidade de gerenciamento](sql-database-managed-instance-find-management-endpoint-ip-address.md).

> [!NOTE]
> Se você usa a replicação transacional em uma instância gerenciada e, se você usar qualquer instância de banco de dados como um publicador ou um distribuidor, abre a porta 445 (TCP de saída) nas regras de segurança da sub-rede. Essa porta permitirá acesso ao compartilhamento de arquivos do Azure.

### <a name="user-defined-routes"></a>rotas definidas pelo usuário

|Nome|Prefixo de endereço|Próximo salto|
|----|--------------|-------|
|subnet_to_vnetlocal|[mi_subnet]|Rede virtual|
|mi-0-5-next-hop-internet|0.0.0.0/5|Internet|
|mi-11-8-nexthop-internet|11.0.0.0/8|Internet|
|mi-12-6-nexthop-internet|12.0.0.0/6|Internet|
|mi-128-3-nexthop-internet|128.0.0.0/3|Internet|
|mi-16-4-nexthop-internet|16.0.0.0/4|Internet|
|mi-160-5-nexthop-internet|160.0.0.0/5|Internet|
|mi-168-6-nexthop-internet|168.0.0.0/6|Internet|
|mi-172-12-nexthop-internet|172.0.0.0/12|Internet|
|mi-172-128-9-nexthop-internet|172.128.0.0/9|Internet|
|mi-172-32-11-nexthop-internet|172.32.0.0/11|Internet|
|mi-172-64-10-nexthop-internet|172.64.0.0/10|Internet|
|mi-173-8-nexthop-internet|173.0.0.0/8|Internet|
|mi-174-7-nexthop-internet|174.0.0.0/7|Internet|
|mi-176-4-nexthop-internet|176.0.0.0/4|Internet|
|mi-192-128-11-nexthop-internet|192.128.0.0/11|Internet|
|mi-192-160-13-nexthop-internet|192.160.0.0/13|Internet|
|mi-192-169-16-nexthop-internet|192.169.0.0/16|Internet|
|mi-192-170-15-nexthop-internet|192.170.0.0/15|Internet|
|mi-192-172-14-nexthop-internet|192.172.0.0/14|Internet|
|mi-192-176-12-nexthop-internet|192.176.0.0/12|Internet|
|mi-192-192-10-nexthop-internet|192.192.0.0/10|Internet|
|mi-192-9-nexthop-internet|192.0.0.0/9|Internet|
|mi-193-8-nexthop-internet|193.0.0.0/8|Internet|
|mi-194-7-nexthop-internet|194.0.0.0/7|Internet|
|mi-196-6-nexthop-internet|196.0.0.0/6|Internet|
|mi-200-5-nexthop-internet|200.0.0.0/5|Internet|
|mi-208-4-nexthop-internet|208.0.0.0/4|Internet|
|mi-224-3-nexthop-internet|224.0.0.0/3|Internet|
|mi-32-3-nexthop-internet|32.0.0.0/3|Internet|
|mi-64-2-nexthop-internet|64.0.0.0/2|Internet|
|mi-8-7-nexthop-internet|8.0.0.0/7|Internet|
||||

Além disso, você pode adicionar entradas à tabela de rotas para rotear o tráfego que tem intervalos IP privados em locais como um destino por meio do gateway de rede virtual ou um dispositivo de rede virtual (NVA).

Se a rede virtual inclui um DNS personalizado, adicione uma entrada para o endereço IP do resolvedor recursivo do Azure (como 168.63.129.16). Para obter mais informações, consulte [configurar um DNS personalizado](sql-database-managed-instance-custom-dns.md). O servidor DNS personalizado deve ser capaz de resolver nomes de host nesses domínios e seus subdomínios: *microsoft.com*, *windows.net*, *windows.com*,  *msocsp.com*, *digicert.com*, *live.com*, *microsoftonline.com*, e *Secure.aadcdn.microsoftonline-p.com microsoftonline*.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral, consulte [avançadas de segurança de dados do banco de dados do SQL](sql-database-managed-instance.md).
- Saiba como [configurar uma nova rede virtual do Azure](sql-database-managed-instance-create-vnet-subnet.md) ou um [rede virtual do Azure existente](sql-database-managed-instance-configure-vnet-subnet.md) onde você pode implantar instâncias gerenciadas.
- [Calcular o tamanho da sub-rede](sql-database-managed-instance-determine-size-vnet-subnet.md) onde você deseja implantar as instâncias gerenciadas.
- Saiba como criar uma instância gerenciada:
  - No [portal do Azure](sql-database-managed-instance-get-started.md).
  - Usando [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/).
  - Usando [um modelo do Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).
  - Usando [um modelo do Resource Manager (usando o JumpBox, com o SSMS incluído)](https://portal.azure.com/).
