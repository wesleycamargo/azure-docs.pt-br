---
title: Hospedar novamente um aplicativo Contoso local migrando para VMs do Azure e Instância Gerenciada do Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba como a Contoso hospeda novamente um aplicativo local em VMs do Azure e usando a Instância Gerenciada do Banco de Dados SQL do Azure.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: 3e3f8dffbaa7109423aacdbfbaa658bada8bb84a
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39215332"
---
# <a name="contoso-migration-rehost-an-on-premises-app-on-an-azure-vm-and-sql-database-managed-instance"></a>Migração Contoso: Hospedar novamente um aplicativo local em uma Instância Gerenciada do Banco de Dados SQL e VM do Azure

Neste artigo, a Contoso migra a VM front-end do aplicativo SmartHotel para uma VM do Azure usando o serviço do Azure Site Recovery. A Contoso também migra o banco de dados de aplicativo para Instância Gerenciada do Banco de Dados SQL do Azure.

> [!NOTE]
> Atualmente, a Instância Gerenciada do Banco de Dados SQL do Azure está em versão prévia.

Este artigo é um de uma série de artigos que documenta como a empresa fictícia Contoso migra os recursos locais para a nuvem do Microsoft Azure. A série inclui informações básicas e uma série de cenários que ilustram como configurar uma infraestrutura de migração e executar diferentes tipos de migrações. Cenários aumentam em complexidade. Os artigos serão adicionados à série ao longo do tempo.


Artigo | Detalhes | Status
--- | --- | ---
[Artigo 1: Visão geral](contoso-migration-overview.md) | Visão geral da estratégia de migração da Contoso, da série de artigos e dos aplicativos de exemplo usados na série. | Disponível
[Artigo 2: Implantar uma infraestrutura do Azure](contoso-migration-infrastructure.md) | A Contoso prepara a infraestrutura local e a infraestrutura do Azure para migração. A mesma infraestrutura é usada para todos os artigos de migração da série. | Disponível
[Artigo 3: Avaliar recursos locais para migração para o Azure](contoso-migration-assessment.md) | A Contoso executa uma avaliação do aplicativo SmartHotel de duas camadas local em execução no VMware. A Contoso avalia as VMs do aplicativo, usando o serviço de [Migrações para Azure](migrate-overview.md). A Contoso avalia o banco de dados do SQL Server do aplicativo, usando o [Assistente de Migração de Dados](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Disponível
Artigo 4: Hospedar novamente um aplicativo em uma Instância Gerenciada do Banco de Dados SQL e VM do Azure | A Contoso executa uma migração lift-and-shift para Azure no aplicativo SmartHotel local. A Contoso migra a VM front-end do aplicativo, usando o [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). A Contoso migra o banco de dados do aplicativo para uma Instância Gerenciada do Banco de Dados SQL do Azure usando o [Serviço de Migração de Banco de Dados do Azure](https://docs.microsoft.com/azure/dms/dms-overview). | Este artigo
[Artigo 5: Hospedar novamente um aplicativo em VMs do Azure](contoso-migration-rehost-vm.md) | A Contoso migra as VMs do aplicativo SmartHotel para as VMs do Azure usando o serviço do Site Recovery. | Disponível
[Artigo 6: Hospedar novamente um aplicativo em VMs do Azure e em um grupo de disponibilidade AlwaysOn do SQL Server](contoso-migration-rehost-vm-sql-ag.md) | A Contoso migra o aplicativo SmartHotel. A Contoso usa o Site Recovery para migrar as VMs do aplicativo. Ele usa o Serviço de Migração de Banco de Dados para migrar o banco de dados do aplicativo para um cluster SQL Server protegido por um grupo de disponibilidade AlwaysOn. | Disponível
[Artigo 7: Hospedar novamente um aplicativo do Linux nas VMs do Azure](contoso-migration-rehost-linux-vm.md) | A Contoso conclui uma migração lift-and-shift do aplicativo osTicket do Linux para VMs do Azure usando o Site Recovery. | Disponível
[Artigo 8: Hospedar novamente um aplicativo Linux em VMs do Azure e Banco de Dados do Azure para MySQL](contoso-migration-rehost-linux-vm-mysql.md) | A Contoso migra o aplicativo osTicket do Linux para VMs do Azure, usando o Site Recovery. Ele migra o banco de dados do aplicativo para Banco de Dados do Azure para MySQL, usando MySQL Workbench. | Disponível
[Artigo 9: Refatorar um aplicativo em um aplicativo Web do Azure e Banco de Dados SQL do Azure](contoso-migration-refactor-web-app-sql.md) | A Contoso migra o aplicativo SmartHotel para um aplicativo Web do Azure e migra o banco de dados de aplicativos para uma instância do SQL Server do Azure. | Disponível
[Artigo 10: Refatorar um aplicativo do Linux em um aplicativo Web do Azure e Banco de Dados do Azure para MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | A Contoso migra o aplicativo osTicket do Linux para um aplicativo Web do Azure em vários sites. O aplicativo Web está integrado ao GitHub para entrega contínua. A Contoso migra o banco de dados do aplicativo para uma instância do Banco de Dados do Azure para MySQL. | Disponível
[Artigo 11: Refactor o Team Foundation Server no Visual Studio Team Services](contoso-migration-tfs-vsts.md) | A Contoso migra a implantação do Team Foundation Server local, migrando-a para o Visual Studio Team Services no Azure. | Disponível
[Artigo 12: Recriar a arquitetura de um aplicativo em contêineres do Azure e Banco de Dados SQL do Azure](contoso-migration-rearchitect-container-sql.md) | A Contoso migra o aplicativo SmartHotel para Azure e, em seguida, recria a arquitetura do aplicativo. A Contoso recria a arquitetura da camada do aplicativo Web como um contêiner do Windows e recria a arquitetura do banco de dados do aplicativo, usando o Banco de Dados SQL do Azure. | Disponível
[Artigo 13: Recompilar um aplicativo no Azure](contoso-migration-rebuild.md) | A Contoso recompila o aplicativo SmartHotel, usando um intervalo de serviços e recursos do Azure, incluindo o Serviço de Aplicativo do Azure, Serviço de Kubernetes do Azure, Azure Functions, Serviços Cognitivos do Azure e Azure Cosmos DB. | Disponível

É possível baixar o aplicativo SmartHotel de exemplo utilizado neste artigo no [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="on-premises-architecture"></a>Arquitetura local


Este diagrama mostra a infraestrutura local atual da Contoso:

![Arquitetura atual da Contoso](./media/contoso-migration-rehost-vm-sql-managed-instance/contoso-architecture.png)  

- A Contoso possui um datacenter principal. O datacenter está localizado na cidade de Nova York, no leste dos Estados Unidos.
- A Contoso possui três branches locais adicionais nos Estados Unidos.
- O datacenter principal é conectado à Internet com uma conexão Metro Ethernet em fibra (500 MBps).
- Cada branch é conectado localmente à Internet, usando conexões de classe empresarial com túneis VPN IPsec para o datacenter principal. A configuração permite que toda a rede da Contoso esteja permanentemente conectada e otimiza a conectividade com a Internet.
- O data center principal é totalmente virtualizado com o VMware. A Contoso tem dois hosts de virtualização ESXi 6.5 que são gerenciados pelo vCenter Server 6.5.
- A Contoso usa o Active Directory para gerenciamento de identidade. A Contoso usa servidores DNS na rede interna.
- Executam os controladores de domínio no data center em máquinas virtuais do VMware. Os controladores de domínio no locais branches executados em servidores físicos.

## <a name="business-drivers"></a>Geradores de negócios

A equipe de liderança de TI da Contoso trabalhou em estreita colaboração com os parceiros de negócios da empresa para reconhecer o que a empresa deseja alcançar com essa migração:

- **Tratar o crescimento dos negócios**: a Contoso está crescendo. Como resultado, a pressão aumentou na infraestrutura e sistemas locais da empresa.
- **Aumentar a eficiência**: a Contoso precisa remover procedimentos desnecessários e simplificar processos para desenvolvedores e usuários. A empresa precisa que a TI seja rápida e não perca tempo nem dinheiro, de modo que a empresa possa entregar os requisitos do cliente de maneira mais rápida.
- **Aumentar a agilidade**: Contoso de TI precisa atender melhor às necessidades dos negócios. Para que a empresa seja bem-sucedida em uma economia global, é necessário que seja capaz de reagir mais rápido que as mudanças do mercado. A TI da Contoso não deve atrapalhar ou tornar-se um bloqueador de negócios.
- **Escala**: à medida que os negócios da empresa crescem com êxito, a TI da Contoso deve fornecer sistemas que possam crescer no mesmo ritmo.

## <a name="migration-goals"></a>Metas de migração

A equipe de nuvem da Contoso identificou metas para essa migração. A empresa usa metas de migração para determinar o melhor método de migração.

- Após a migração, o aplicativo no Azure deverá ter os mesmos recursos de desempenho que o aplicativo tem atualmente no ambiente VMWare local da Contoso. Mover para a nuvem não significa que o desempenho do aplicativo é menos crítico.
- A Contoso não quer investir no aplicativo. O aplicativo é crítico e importante para os negócios, mas a Contoso simplesmente quer mover o aplicativo em sua forma atual para a nuvem.
- Tarefas de administração do banco de dados deverão ser minimizadas após o aplicativo ser migrado.
- A Contoso não deseja usar um Banco de Dados SQL do Azure para esse aplicativo. Ela está procurando alternativas.

## <a name="proposed-architecture"></a>Arquitetura proposta

Neste cenário:

- A Contoso quer migrar o aplicativo de viagem local de duas camadas.
- O aplicativo é em camadas em duas VMs (**WEBVM** e **SQLVM**) e está localizado em um host do VMware ESXi versão 6.5 (**contosohost1.contoso.com**). 
- O ambiente VMware é gerenciado pelo vCenter Server 6.5 (**vcenter.contoso.com**) em execução em uma VM.
- A Contoso migra o banco de dados de aplicativo (**SmartHotelDB**) para uma Instância Gerenciada do Banco de Dados SQL do Azure.
- A Contoso migra as VMs do VMware locais para uma VM do Azure.
- A Contoso tem um datacenter local (**contoso-datacenter**) e um controlador de domínio local (**contosodc1**).
- As VMs locais no datacenter da Contoso serão descomissionadas quando a migração for concluída.

![Arquitetura de cenário](media/contoso-migration-rehost-vm-sql-managed-instance/architecture.png) 

### <a name="azure-services"></a>Serviços do Azure

Serviço | DESCRIÇÃO | Custo
--- | --- | ---
[Serviço de gerenciamento do banco de dados](https://docs.microsoft.com/azure/dms/dms-overview) | O Serviço de Gerenciamento de Banco de Dados permite a migração perfeita de várias origens de banco de dados para as plataformas de dados do Azure com tempo de inatividade mínimo. | Saiba mais sobre [regiões com suporte](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability) e [preços do Serviço de Gerenciamento de Banco de Dados](https://azure.microsoft.com/pricing/details/database-migration/).
[Instância Gerenciada do Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | Instância Gerenciada é um serviço de banco de dados gerenciado que representa uma instância do SQL Server totalmente gerenciada na nuvem do Azure. Ela usa o mesmo código da última versão do Mecanismo de Banco de Dados do SQL Server e possui os recursos, melhorias de desempenho e patches de segurança mais recentes. | O uso de uma Instância Gerenciada do Banco de Dados SQL em execução no Azure incorre em encargos com base na capacidade. Saiba mais sobre [Preço de Instância Gerenciada](https://azure.microsoft.com/pricing/details/sql-database/managed/). 
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | O serviço do Site Recovery orquestra e gerencia migração e recuperação de desastre para VMs do Azure e servidores físicos e VMs locais.  | Durante a replicação para o Azure, são gerados encargos do Armazenamento do Azure.  As VMs do Azure são criadas e incorrem em encargos quando ocorre failover. Saiba mais sobre [Encargos e preços do Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).

 ## <a name="migration-process"></a>Processo de migração

A Contoso migrará as camadas de dados e Web do aplicativo SmartHotel para o Azure concluindo estas etapas:

1. A Contoso já tem a infraestrutura do Azure implantada, portanto, basta adicionar alguns componentes específicos do Azure para esse cenário.
2. A camada de dados será migrada usando o Serviço de Migração de Dados. O Serviço de Migração de Dados conecta-se à VM do SQL Server local em uma conexão VPN site a site entre o datacenter da Contoso e o Azure. Em seguida, o Serviço de Migração de Dados migra o banco de dados.
3. A camada Web será migrada usando uma migração lift-and-shift, usando Site Recovery. O processo envolve a preparação do ambiente VMware local, a configuração e habilitação da replicação e a migração das VMs, fazendo com que elas sejam reprovadas no Azure.

     ![Arquitetura de migração](media/contoso-migration-rehost-vm-sql-managed-instance/migration-architecture.png) 

## <a name="prerequisites"></a>Pré-requisitos

A Contoso e outros usuários devem atender aos pré-requisitos a seguir para este cenário:

Requisitos | Detalhes
--- | ---
**Inscrever-se na versão prévia da Instância Gerenciada** | É necessário estar inscrito na visualização pública limitada da Instância Gerenciada do Banco de Dados SQL. Adicionalmente, é necessária uma assinatura do Azure para [criar conta](https://portal.azure.com#create/Microsoft.SQLManagedInstance). A inscrição pode levar alguns dias para ser concluída, por isso, inscreva-se antes de começar a implantar esse cenário.
**Assinatura do Azure** | Ao realizar a avaliação no primeiro artigo desta série, é necessário já ter criado a assinatura. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Se você criar uma conta gratuita, será o administrador da assinatura e poderá executar todas as ações.<br/><br/> Se você usar uma assinatura existente e não for o administrador da assinatura, será necessário trabalhar com o administrador para atribuir-lhe permissões de Proprietário ou de Colaborador.<br/><br/> Se permissões mais granulares forem necessárias, consulte [Use o controle de acesso baseado em função para gerenciar o acesso à recuperação do site](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Site Recovery (locais)** | A instância do vCenter Server local deve estar executando a versão 5.5, 6.0 ou 6.5<br/><br/> Um host ESXi que executa a versão 5.5, 6.0 ou 6.5<br/><br/> Uma ou mais VMs do VMware em execução no host ESXi.<br/><br/> As VMs devem atender aos [requisitos do Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Configuração de [rede](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) e de [armazenamento](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) compatível.
**Serviço de gerenciamento do banco de dados** | Para o Serviço de Gerenciamento de Banco de Dados, é necessário um [dispositivo VPN local compatível](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).<br/><br/> É necessário poder configurar o dispositivo VPN local. Um endereço IPv4 público externo é necessário. O endereço não pode ser localizado atrás de um dispositivo NAT.<br/><br/> Certifique-se de ter acesso ao seu banco de dados local do SQL Server.<br/><br/> O Firewall do Windows deve poder acessar o mecanismo do banco de dados de origem. Saiba como [configurar o Firewall do Windows para acesso ao Mecanismo de Banco de Dados](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).<br/><br/> Se houver um firewall em frente do computador de banco de dados, adicione regras para permitir o acesso ao banco de dados e arquivos via porta SMB 445.<br/><br/> As credenciais usadas para conectar a instância de origem do SQL Server e qual Instância Gerenciada de destino deve ser membro da função de servidor sysadmin.<br/><br/> É necessário um compartilhamento de rede no banco de dados local que o Serviço de Gerenciamento de Banco de Dados possa usar para fazer backup do banco de dados de origem.<br/><br/> Certifique-se de que a conta de serviço que está executando a instância do SQL Server de origem tenha permissões de gravação no compartilhamento de rede.<br/><br/> Anote o usuário e senha do Windows que têm permissões de controle total no compartilhamento de rede. O Serviço de Gerenciamento de Banco de Dados representa essas credenciais de usuário para carregar arquivos de backup no contêiner do Armazenamento do Microsoft Azure.<br/><br/> O processo de instalação do SQL Server Express define o protocolo TCP/IP como **Desabilitado** por padrão. Certifique-se de que ele está habilitado.

## <a name="scenario-steps"></a>Etapas do cenário

Veja como a Contoso planeja configurar a implantação:

> [!div class="checklist"]
> * **Etapa 1: Configurar uma Instância Gerenciada do Banco de Dados SQL**: a Contoso precisa de uma Instância Gerenciada criada previamente para a qual o banco de dados do SQL Server local será migrado.
> * **Etapa 2: Preparar o Serviço de Gerenciamento de Banco de Dados**: a Contoso deve registrar o provedor de migração de banco de dados, criar uma instância e criar um projeto do Serviço de Gerenciamento de Banco de Dados. A Contoso também deve configurar um URI (Uniform Resource Identifier) de SAS (Assinatura de Acesso Compartilhado) para o Serviço de Gerenciamento de Banco de Dados. Um URI de SAS fornece acesso delegado a recursos na conta de armazenamento da Contoso, portanto, a Contoso pode conceder permissões limitadas a objetos de armazenamento. A Contoso configura um URI de SAS, portanto, o Serviço de Gerenciamento de Banco de Dados pode acessar o contêiner da conta de armazenamento para qual o serviço carrega os arquivos de backup do SQL Server.
> * **Etapa 3: Preparar o Azure para Site Recovery**: a Contoso deve criar uma conta de armazenamento para conter dados replicados para Site Recovery. Também é necessário criar um cofre dos Serviços de Recuperação do Azure.
> * **Etapa 4: Preparar o VMware local para Site Recovery**: a Contoso preparará as contas para a descoberta de VMs e a instalação do agente para conectar VMs do Azure após failover.
> * **Etapa 5: Replicar VMs**: para configurar a replicação, a Contoso configura os ambientes de origem e de destino do Site Recovery, configura uma política de replicação e começa a replicar as VMs para o Armazenamento do Microsoft Azure.
> * **Etapa 6: Migrar o banco de dados usando o Serviço de Gerenciamento de Banco de Dados**: a Contoso migra o banco de dados.
> * **Etapa 7: Migrar as VMs usando o Site Recovery**: a Contoso executa um failover de teste para garantir que tudo está funcionando. Em seguida, a Contoso executa um failover completo para migrar as VMs para o Azure.

## <a name="step-1-prepare-a-sql-database-managed-instance"></a>Etapa 1: Preparar uma Instância Gerenciada do Banco de Dados SQL

Para configurar uma Instância Gerenciada do Banco de Dados SQL do Azure, a Contoso precisa de uma sub-rede que atenda aos seguintes requisitos:

- A sub-rede deve ser dedicada. Deve estar vazia e não pode conter nenhum outro serviço de nuvem. A sub-rede não pode ser uma sub-rede de gateway.
- Após a criação da Instância Gerenciada, a Contoso não deve adicionar recursos à sub-rede.
- A sub-rede não pode ter um grupo de segurança de rede associado a ela.
- A sub-rede deve ter uma tabela de rotas de UDR (rota definida pelo usuário). A única rota atribuída deve ser 0.0.0.0/0 do próximo salto para a Internet. 
- DNS personalizado opcional: se o DNS personalizado for especificado na rede virtual do Azure, o endereço IP dos resolvedores recursivos do Azure (como 168.63.129.16) deverá ser adicionado à lista. Saiba como [configurar DNS personalizado para uma Instância Gerenciada](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).
- A sub-rede não deve ter um ponto de extremidade de serviço (armazenamento ou SQL) associado a ela. Os pontos de extremidade de serviço devem ser desabilitados na rede virtual.
- A sub-rede deve ter no mínimo 16 endereços IP. Saiba como [dimensionar a sub-rede da Instância Gerenciada](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#determine-the-size-of-subnet-for-managed-instances).
- No ambiente híbrido da Contoso, são necessárias configurações de DNS personalizadas. A Contoso configura as configurações de DNS para usar um ou mais dos servidores DNS do Azure da empresa. Saiba mais sobre [personalização de DNS](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).

### <a name="set-up-a-virtual-network-for-the-managed-instance"></a>Configurar uma rede virtual para a Instância Gerenciada

A Contoso configura a rede virtual conforme a seguir: 

1. A Contoso cria uma nova rede virtual (**VNET-SQLMI-EU2**) na região Leste dos EUA 2 primária. Ela adiciona a rede virtual ao grupo de recursos **ContosoNetworkingRG**.
2. A Contoso atribui um espaço de endereço de 10.235.0.0/24. A Contoso garante que o intervalo não sobreponha-se a nenhuma outra rede na empresa.
2. A Contoso adiciona duas sub-redes à rede:
    - **SQLMI-DS-EUS2** (10.235.0.0.25)
    - **SQLMI-SAW-EUS2** (10.235.0.128/29). Essa sub-rede é usada para anexar um diretório à Instância Gerenciada.

    ![Instância Gerenciada - Criar rede virtual](media/contoso-migration-rehost-vm-sql-managed-instance/mi-vnet.png)

6. Depois que a rede virtual e as sub-redes são implantadas, a Contoso emparelha redes da seguinte maneira:

    - Emparelha **VNET-SQLMI-EUS2** com **VNET-HUB-EUS2** (a rede virtual do hub para Leste dos EUA 2).
    - Emparelha **VNET-SQLMI-EUS2** com **VNET-PROD-EUS2** (a rede de produção).

    ![Emparelhamento de rede](media/contoso-migration-rehost-vm-sql-managed-instance/mi-peering.png)

7. A Contoso define as configurações de DNS personalizadas. O DNS aponta primeiro para os controladores de domínio do Azure da Contoso. DNS do Azure é secundário. Os controladores de domínio do Azure da Contoso estão localizados conforme a seguir:

    - Localizado na sub-rede **PROD-DC-EUS2**,na rede de produção Leste dos EUA 2 (**VNET-PROD-EUS2**)
    - Endereço **CONTOSODC3**: 10.245.42.4
    - Endereço **CONTOSODC4**: 10.245.42.5
    - O resolvedor de DNS do Azure: 168.63.129.16

     ![Servidores DNS de rede](media/contoso-migration-rehost-vm-sql-managed-instance/mi-dns.png)

*Precisa de mais ajuda?*

- Obtenha uma visão geral da [Instância Gerenciada do Banco de Dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance).
- Saiba como [criar uma rede virtual para uma Instância Gerenciada do Banco de Dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#create-a-new-virtual-network-for-managed-instances).
- Saiba como [configurar emparelhamento](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering).
- Saiba como [atualizar configurações de DNS do Azure Active Directory](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns).

### <a name="set-up-routing"></a>Configurar roteamento

A Instância Gerenciada é colocada em uma rede virtual privada. A Contoso precisa de uma tabela de rotas para a rede virtual comunicar-se com o Serviço de Gerenciamento do Azure. Se a rede virtual não puder comunicar-se com o serviço que a gerencia, a rede virtual ficará inacessível.

A Contoso considera estes fatores:

- A tabela de rotas contém um conjunto de regras (rotas) que especificam como os pacotes enviados da Instância Gerenciada devem ser roteados na rede virtual.
- A tabela de rotas é associada a sub-redes para instâncias gerenciadas são implantadas. Cada pacote que deixa uma sub-rede é tratado com base na tabela de rotas associada.
- Uma sub-rede pode ser associada a apenas uma tabela de rotas.
- Não há nenhuma cobrança adicional para a criação de tabelas de rotas no Microsoft Azure.

 Para configurar o roteamento:

1. A Contoso cria uma tabela de UDR. A Contoso cria a tabela de rotas no grupo de recursos **ContosoNetworkingRG**.

    ![Tabela de rotas](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table.png)

2. Para estar em conformidade com os requisitos da Instância Gerenciada, após a implantação da tabela de rotas (**MIRouteTable**) a Contoso adiciona uma rota que possui um prefixo de endereço de 0.0.0.0/0. A opção **Tipo do próximo salto** é definida para **Internet**.

    ![Prefixo de tabela de rota](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-prefix.png)
    
3. A Contoso associa a tabela de rotas à sub-rede **SQLMI-DB-EUS2** (na rede **VNET-SQLMI-EUS2**). 

    ![Sub-rede da tabela de rota](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-subnet.png)
    
*Precisa de mais ajuda?*

Saiba como [configurar rotas para uma Instância Gerenciada](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal#create-new-route-table-and-a-route).

### <a name="create-a-managed-instance"></a>Criar uma Instância Gerenciada

Agora, a Contoso pode provisionar uma Instância Gerenciada do Banco de Dados SQL:

1. Como a Instância Gerenciada atende a um aplicativo de negócios, a Contoso implanta a Instância Gerenciada na região Leste dos EUA 2 primária da empresa. A Contoso adiciona a Instância Gerenciada ao grupo de recursos **ContosoRG**.
2. A Contoso seleciona um tipo de preço, computação de tamanho e armazenamento para a instância. Saiba mais sobre [Preço de Instância Gerenciada](https://azure.microsoft.com/pricing/details/sql-database/managed/).

    ![Instância Gerenciada](media/contoso-migration-rehost-vm-sql-managed-instance/mi-create.png)

3. Depois que a Instância Gerenciada é implantada, dois novos recursos aparecem no grupo de recursos **ContosoRG**:

    - Um cluster virtual se a Contoso tiver várias Instâncias Gerenciadas.
    - A Instância Gerenciada do Banco de Dados SQL. 

    ![Instância Gerenciada](media/contoso-migration-rehost-vm-sql-managed-instance/mi-resources.png)

*Precisa de mais ajuda?*

Saiba como [provisionar uma Instância Gerenciada](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal).

## <a name="step-2-prepare-the-database-management-service"></a>Etapa 2: Preparar o serviço de Gerenciamento de Banco de Dados

Para preparar o Serviço de Gerenciamento de Banco de Dados, a Contoso precisa:

- Registrar o provedor do Serviço de Gerenciamento de Banco de Dados no Azure.
- Fornecer ao Serviço de Gerenciamento de Banco de Dados acesso ao Armazenamento do Microsoft Azure para carregar arquivos de backup usados para migrar um banco de dados. Para fornecer acesso ao Armazenamento do Microsoft Azure, a Contoso cria um contêiner de armazenamento de Blobs do Azure. A Contoso gera um URI de SAS para o contêiner de armazenamento de Blobs. 
- Crie um projeto de Serviço de Gerenciamento de Banco de Dados.

Em seguida, a Contoso conclui as seguintes etapas:

1. A Contoso registra o provedor de migração do banco de dados sob sua assinatura.
    ![Serviço de Gerenciamento de Banco de Dados - Registrar](media/contoso-migration-rehost-vm-sql-managed-instance/dms-subscription.png)

2. A Contoso cria um contêiner de armazenamento de Blobs. A Contoso gera um URI de SAS para que o Serviço de Gerenciamento de Banco de Dados possa acessá-lo.

    ![Serviço de Gerenciamento de Banco de Dados - Gerar um URI de SAS](media/contoso-migration-rehost-vm-sql-managed-instance/dms-sas.png)

3. A Contoso cria uma instância do Serviço de Gerenciamento de Banco de Dados. 

    ![Serviço de Gerenciamento de Banco de Dados - Criar instância](media/contoso-migration-rehost-vm-sql-managed-instance/dms-instance.png)

4. A Contoso coloca a instância do Serviço de Gerenciamento de Banco de Dados na sub-rede **PROD-DC-EUS2** da rede virtual **VNET-PROD-DC-EUS2**.
    - A Contoso coloca o Serviço de Gerenciamento de Banco de Dados na sub-rede porque o serviço deve estar em uma rede virtual que pode acessar a VM do SQL Server local por meio de um gateway de VPN.
    - A **VNET-PROD-EUS2** é emparelhada para **VNET-HUB-EUS2** e tem permissão para usar gateways remotos. A opção **Usar gateways remotos** garante que o Serviço de Gerenciamento de Banco de Dados possa comunicar-se conforme necessário.

        ![Serviço de Gerenciamento de Banco de Dados - Configurar rede](media/contoso-migration-rehost-vm-sql-managed-instance/dms-network.png)

*Precisa de mais ajuda?*

- Saiba como [configurar o Serviço de Gerenciamento de Banco de Dados](https://docs.microsoft.com/azure/dms/quickstart-create-data-migration-service-portal).
- Saiba como [criar e usar SAS](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2).


## <a name="step-3-prepare-azure-for-the-site-recovery-service"></a>Etapa 3: preparar o Azure para o serviço de recuperação do site

Vários elementos do Azure são necessários para que a Contoso configure o Site Recovery para migração da VM da camada Web (**WEBMV**):

- Uma rede virtual na qual os recursos de failover estão localizados.
- Uma conta de armazenamento para armazenar dados replicados. 
- Um cofre dos Serviços de Recuperação no Azure.

A Contoso configura o Site Recovery, conforme a seguir:

1. Como a VM é um front-end da Web para o aplicativo SmartHotel, a Contoso faz failover da VM na rede de produção existente (**VNET-PROD-EUS2**) e sub-rede (**PROD-FE-EUS2**). A rede e sub-rede estão localizadas na região Leste dos EUA 2 primária. A Contoso configurou a rede quando [implantou a infraestrutura do Azure](contoso-migration-infrastructure.md).
2. Contoso cria uma conta de armazenamento (**contosovmsacc20180528**). A Contoso usa uma conta de uso geral. A Contoso seleciona armazenamento padrão e replicação de armazenamento com redundância local.

    ![Site Recovery - Criar conta de armazenamento](media/contoso-migration-rehost-vm-sql-managed-instance/asr-storage.png)

3. Com a conta de rede e armazenamento implantada, a Contoso cria um cofre (**ContosoMigrationVault**). A Contoso coloca o cofre no grupo de recursos **ContosoFailoverRG**, na região Leste dos EUA 2 primária.

    ![Recovery Services - Criar cofre](media/contoso-migration-rehost-vm-sql-managed-instance/asr-vault.png)

*Precisa de mais ajuda?*

Saiba como [configurar Azure para Site Recovery](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure).


## <a name="step-4-prepare-on-premises-vmware-for-site-recovery"></a>Etapa 4: preparar o VMware local para recuperação do site

Para preparar VMware para Site Recovery, a Contoso deve concluir estas tarefas:

- Prepare uma conta na instância do vCenter Server ou no host vSphere ESXi. A conta automatiza a descoberta de VMs.
- Prepare uma conta que permita a instalação automática do Serviço de Mobilidade nas VMs do VMware que a Contoso deseja replicar.
- Prepare VMs locais para conectar VMs do Azure quando forem criadas após failover.


### <a name="prepare-an-account-for-automatic-discovery"></a>Preparar uma conta para a descoberta automática

O Site Recovery precisa de acesso aos servidores VMware para:

- Descobrir VMs automaticamente. É necessária pelo menos uma conta somente leitura.
- Orquestrar a replicação, o failover e o failback. A Contoso precisa de uma conta que possa executar operações, como criar e remover discos e ativar VMs.

A Contoso configura a conta concluindo estas tarefas:

1. Cria uma função no nível do vCenter.
2. Atribui as permissões necessárias a essa função.

*Precisa de mais ajuda?*

Saiba como [criar e atribuir uma função para descoberta automática](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparar uma conta para a instalação do Serviço de Mobilidade

O Serviço de Mobilidade deve ser instalado na VM que a Contoso deseja replicar. A Contoso considera esses fatores sobre o Serviço de Mobilidade:

- O Site Recovery pode fazer uma instalação automática por push desse componente quando a Contoso habilita a replicação para a VM.
- Para a instalação automática por push, a Contoso deve preparar uma conta que o Site Recovery usa para acessar a VM.
- A Contoso especifica essa conta quando configura a replicação no console do Azure.
- A Contoso deve ter um domínio ou conta local com permissões para instalar na VM.

*Precisa de mais ajuda*

Saiba como [criar uma conta para instalação por push do Serviço de Mobilidade](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation).

### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar para conectar VMs do Azure após o failover

Após o failover no Azure, a Contoso deseja ser capaz de se conectar às VMs replicadas no Azure. Para conectar VMs replicadas no Azure, a Contoso deve concluir algumas tarefas na VM local antes da migração: 

1. Para acesso pela Internet, a Contoso habilita o RDP na VM local antes do failover. A Contoso garante que as regras TCP e UDP sejam adicionadas ao perfil **Público** e que o RDP seja permitido no **Firewall do Windows** > **Aplicativos Permitidos** para todos os perfis.
2. Para acesso através de VPN site a site da Contoso, a Contoso habilita o RDP no computador local. A Contoso permite RDP no **Firewall do Windows** > **Aplicativos e recursos permitidos** para redes de **Domínio e Privadas**.
3. A Contoso define a diretiva SAN do sistema operacional na VM local para **OnlineAll**.

A Contoso também precisa verificar esses itens ao executar um failover:

- Não deve haver atualizações do Windows pendentes na VM quando um failover é disparado. Se as atualizações do Windows estiverem pendentes, a Contoso não poderá entrar na máquina virtual até que a atualização esteja concluída.
- Após failover, a Contoso deve verificar **Diagnóstico de inicialização** para exibir uma captura de tela da VM. Se a Contoso não puder exibir o diagnóstico de inicialização, verifique se a VM está em execução e, em seguida, revise as [dicas de soluções de problemas](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

## <a name="step-5-replicate-the-on-premises-vms-to-azure-by-using-site-recovery"></a>Etapa 5: Replicar as VMs locais para Azure usando Site Recovery

Antes de executar uma migração para Azure, a Contoso precisa configurar a replicação e habilitar a replicação para a VM local.

### <a name="set-a-replication-goal"></a>Definir uma meta de replicação

1. No cofre, no nome do cofre (**ContosoVMVault**), a Contoso define uma meta de replicação (**Introdução** > **Site Recovery** > **Preparar a infraestrutura**).
2. A Contoso especifica que as máquinas estão localizadas no local, que são VMs do VMware e que a Contoso deseja replicar no Azure.

    ![Preparar a infraestrutura - Metal de proteção](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Confirmar planejamento de implantação

Para continuar, a Contoso precisa confirmar que concluiu o planejamento de implantação. Para confirmar, a Contoso seleciona **Sim, eu fiz isso**. Nesta implantação, a Contoso está migrando apenas uma única VM, portanto, não precisa de planejamento de implantação.

### <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Agora, a Contoso precisa configurar o ambiente de origem. Para configurar o ambiente de origem, a Contoso baixa um modelo de OVF. A Contoso usa o modelo para implantar o servidor de configuração e os componentes associados como uma VM do VMware local e altamente disponível. Os componentes do servidor incluem:

- O servidor de configuração que coordena as comunicações entre a infraestrutura local e o Azure. O servidor de configuração gerencia a replicação de dados.
- Servidor em processo que age como um gateway de replicação. O servidor de processo:
    - Recebe dados de replicação.
    - Otimiza a data de replicação usando cache, compactação e criptografia.
    - Envia a data de replicação para Armazenamento do Microsoft Azure.
- O servidor de processo também instala o Serviço de Mobilidade nas VMs que a Contoso deseja replicar. O servidor de processo executa a descoberta automática de VMs do VMware locais.
- Depois que a VM do servidor de configuração é criada e iniciada, a Contoso registra o servidor no cofre.

Para configurar o ambiente de origem:

1. A Contoso baixa o modelo de OVF do portal do Azure (**Preparar a infraestrutura** > **Origem** > **Servidor de configuração**).
    
    ![Adicionar um servidor de configuração](./media/contoso-migration-rehost-vm-sql-managed-instance/add-cs.png)

2. A Contoso importa o modelo no VMware para criar e implantar a VM.

    ![Implantar o modelo de OVF](./media/contoso-migration-rehost-vm-sql-managed-instance/vcenter-wizard.png)

3.  Quando a Contoso ativa a VM pela primeira vez, a VM é iniciada em uma experiência de instalação do Windows Server 2016. A Contoso aceita o contrato de licença e insere uma senha de administrador.
4. Quando a instalação é concluída, a Contoso entra na VM como o administrador. Na primeira vez em que a Contoso entra na VM, a Ferramenta de Configuração do Azure Site Recovery é executada automaticamente.
5. Na Ferramenta de Configuração do Site Recovery, a Contoso insere um nome para usar e registrar o servidor de configuração no cofre.
6. A ferramenta verifica a conexão da VM para o Azure. Depois que a conexão for estabelecida, a Contoso seleciona **Entrar** para entrar na assinatura do Azure. As credenciais devem ter acesso ao cofre no qual o servidor de configuração está registrado. 

    ![Registrar o servidor de configuração](./media/contoso-migration-rehost-vm-sql-managed-instance/config-server-register2.png)

7. A ferramenta executa algumas tarefas de configuração e, em seguida, é reinicializada. A Contoso entra no computador novamente. O Assistente de Gerenciamento do Servidor de Configuração inicia automaticamente.
8. No assistente, a Contoso seleciona o adaptador de rede para receber o tráfego de replicação. Essa configuração não pode ser alterada depois de definida.
9. A Contoso seleciona a assinatura, o grupo de recursos e o cofre dos Serviços de Recuperação para registrar o servidor de configuração.

    ![Selecionar o cofre dos Serviços de Recuperação](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz1.png)

10. A Contoso baixa e instala o Servidor MySQL e o PowerCLI do VMware. Em seguida, a Contoso valida as configurações do servidor.
11. Após a validação, a Contoso insere o FQDN ou o endereço IP da instância do vCenter Server ou host vSphere. A Contoso deixa a porta padrão e insere um nome de exibição para a instância do vCenter Server no Azure.
12. A Contoso deve especificar a conta que criou anteriormente para que o Site Recovery possa descobrir automaticamente as VMs do VMware que estão disponíveis para replicação. 
13. A Contoso insere credenciais, portanto, o Serviço de Mobilidade é instalado automaticamente quando a replicação é habilitada. Para computadores Windows, a conta precisa de permissões de administrador local nas VMs. 

    ![Configurar vCenter Server](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz2.png)

7. Quando o registro é concluído, no portal do Azure, a Contoso verifica novamente se o servidor de configuração e o servidor VMware estão listados na página **Origem** no cofre. A descoberta pode levar 15 minutos ou mais. 
8. O Site Recovery conecta os servidores VMware usando as configurações especificadas. O Site Recovery descobre VMs.

### <a name="set-up-the-target"></a>Configurar o destino

Agora, a Contoso precisa configurar o ambiente de replicação de destino:

1. Em **Preparar a infraestrutura** > **Destino**, a Contoso seleciona as configurações de destino.
2. O Site Recovery verifica se há uma conta de armazenamento e rede no destino especificado.

### <a name="create-a-replication-policy"></a>Criar uma política de replicação

Quando a origem e o destino estão configurados, a Contoso cria uma política de replicação e associa a política ao servidor de configuração:

1. Em **Preparar a infraestrutura** > **Configurações de Replicação** > **Política de Replicação** >  **Criar e Associar**, a Contoso cria a política **ContosoMigrationPolicy**.
2. A Contoso usa as configurações padrão:
    - **Limite de RPO**: padrão de 60 minutos. Esse valor define a frequência em que são criados os pontos de recuperação. Um alerta será gerado se a replicação contínua exceder esse limite.
    - **Retenção de ponto de recuperação**: padrão de 24 horas. Esse valor especifica qual é o tempo da janela de retenção para cada ponto de recuperação. VMs replicadas podem ser recuperadas para qualquer ponto em uma janela.
    - **Frequência de instantâneo consistente com o aplicativo**: padrão de 1 hora. Esse valor especifica a frequência com que são criados instantâneos consistentes com o aplicativo.
 
    ![Política de replicação - Criar](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy.png)

3. A política é associada automaticamente ao servidor de configuração. 

    ![Política de replicação - Associar](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy2.png)

*Precisa de mais ajuda?*

- É possível ler uma explicação completa dessas etapas em [Configurar recuperação de desastre para VMs do VMware locais](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- As instruções detalhadas estão disponíveis para ajudá-lo a [configurar o ambiente da fonte](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [implantar o servidor de configuração](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), e [definir configurações de replicação](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).

### <a name="enable-replication"></a>Habilitar a replicação

Agora, a Contoso pode começar a replicar o WebVM.

1. Em **Replicar aplicativo** > **Origem** > **Replicar**, a Contoso seleciona as configurações de origem.
2. A Contoso indica que deseja habilitar máquinas virtuais, seleciona a instância do vCenter Server e define o servidor de configuração.

    ![Habilitar replicação - Origem](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication1.png)
 
3. A Contoso especifica as configurações de destino, incluindo o grupo de recursos e a rede em que a VM do Azure estará localizada após failover. A Contoso especifica a conta de armazenamento na qual os dados replicados serão armazenados.

    ![Habilitar replicação - Destino](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication2.png)

4. A Contoso seleciona **WebVM** para replicação. O Site Recovery instala o Serviço de Mobilidade em cada VM quando a replicação está habilitada. 

    ![Habilitar replicação - Selecionar a VM](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication3.png)

5. A Contoso verifica se a política de replicação correta está selecionada. Em seguida, ele habilita a replicação para **WEBVM**. A Contoso acompanha o progresso da replicação em **Trabalhos**. Após o trabalho de **Finalizar Proteção** ser executado, o computador estará pronto para failover.
6. Em **Essentials** no portal do Azure, a Contoso pode ver a estrutura das VMs que estão sendo replicadas no Azure:

    ![Modo de exibição de infraestrutura](./media/contoso-migration-rehost-vm-sql-managed-instance/essentials.png)

*Precisa de mais ajuda?*

Você pode ler um passo a passo completo dessas etapas em [Habilitar replicação](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).

## <a name="step-6-migrate-the-database-by-using-the-database-management-service"></a>Etapa 6: Migrar o banco de dados usando o Serviço de Gerenciamento de Banco de Dados

A Contoso precisa criar um projeto do Serviço de Gerenciamento de Banco de Dados e, em seguida, migrar o banco de dados.

### <a name="create-a-database-management-service-project"></a>Criar um projeto do Serviço de Gerenciamento de Banco de Dados

1. A Contoso cria um projeto do Serviço de Gerenciamento de Banco de Dados. A Contoso seleciona o tipo de servidor de origem **SQL Server**. A Contoso seleciona a **Instância Gerenciada do Banco de Dados SQL** como o destino.

     ![Serviço de Gerenciamento de Banco de Dados - Novo projeto de migração](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-project.png)

2. O Assistente de Migração é aberto.

### <a name="migrate-the-database"></a>Migrar o banco de dados 

1. No Assistente de Migração, a Contoso especifica a VM de origem na qual o banco de dados local está localizado. A Contoso insere as credenciais para acessar o banco de dados.

    ![Serviço de Gerenciamento de Banco de Dados - Detalhes da origem](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-source.png)

2. A Contoso seleciona o banco de dados a ser migrado (**SmartHotel.Registration**):

    ![Serviço de Gerenciamento de Banco de Dados - Selecionar bancos de dados de origem](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-sourcedb.png)

3. Para o destino, a Contoso insere o nome da Instância Gerenciada no Azure. A Contoso insere credenciais de acesso para a Instância Gerenciada.

    ![Serviço de Gerenciamento de Banco de Dados - Detalhes do destino](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-target-details.png)

4. Em **Nova Atividade** > **Executar Migração**, a Contoso especifica configurações para executar a migração:
    - Credenciais de origem e destino.
    - O banco de dados para migrar.
    - O compartilhamento de rede que a Contoso criou na VM local. O Serviço de Gerenciamento de Banco de Dados faz backups de origem para esse compartilhamento. 
        - A conta de serviço que executa a instância de origem do SQL Server deve ter permissões de gravação nesse compartilhamento.
        - O caminho do FQDN para o compartilhamento deve ser usado.
    - O SAS de URI que fornece ao Serviço de Gerenciamento de Banco de Dados o acesso ao contêiner da conta de armazenamento para qual o serviço carrega os arquivos de backup para migração.

        ![Serviço de Gerenciamento de Banco de Dados - Definir configurações de migração](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-migration-settings.png)

5. A Contoso salva a migração e a executa.
6. Em **Visão Geral**, a Contoso monitora o status da migração.

    ![Serviço de Gerenciamento de Banco de Dados - Monitorar](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor1.png)

7. Quando a migração é concluída, a Contoso verifica se os bancos de dados de destino existem na Instância Gerenciada.

    ![Serviço de Gerenciamento de Banco de Dados - Verificar a migração do banco de dados](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor2.png)

## <a name="step-7-migrate-the-vm-by-using-site-recovery"></a>Etapa 7: Migrar a VM usando o Site Recovery

A Contoso executa um failover de teste rápido e, em seguida, migra a VM.

### <a name="run-a-test-failover"></a>Execute um teste de failover

Antes de migrar o WEBVM, um failover de teste ajuda a garantir que tudo funcione conforme o esperado. Contoso conclui as seguintes etapas:

1. A Contoso executa um failover de teste para o ponto no tempo mais recente disponível (**Último processado**).
2. A Contoso seleciona **Desligar a máquina antes de iniciar failover** . Com essa opção selecionada, o Site Recovery tenta desligar a VM de origem antes de disparar o failover. O failover continuará, mesmo se o desligamento falhar. 
3. O failover de teste é executado: 
    1. Uma verificação de pré-requisitos é executada para garantir que todas as condições necessárias para migração foram cumpridas.
    2. O failover processa os dados para que uma VM do Azure possa ser criada. Se o ponto de recuperação mais recente for selecionado, um ponto de recuperação é criado dos dados.
    3.  A VM do Azure é criada usando os dados processados na etapa anterior.
3. Quando o failover é concluído, a VM de réplica do Azure é exibida no portal do Azure. A Contoso verifica se tudo está funcionando corretamente: a VM tem o tamanho apropriado, está conectada à rede correta e está em execução. 
4. Após verificar o failover de teste, a Contoso limpa o failover. Em seguida, registra e salva todas as observações. 

### <a name="migrate-the-vm"></a>Migração da VM

1. Depois de verificar se o failover de teste funcionou como esperado, a Contoso cria um plano de recuperação para a migração. A Contoso adiciona WEBVM ao plano:

     ![Criar plano de recuperação - Selecionar itens](./media/contoso-migration-rehost-vm-sql-managed-instance/recovery-plan.png)

2. A Contoso executa um failover no plano. A Contoso seleciona o último ponto de recuperação. A Contoso especifica se o Site Recovery deverá tentar desligar a VM local antes de disparar o failover.

    ![Failover](./media/contoso-migration-rehost-vm-sql-managed-instance/failover1.png)

3. Após o failover, a Contoso verifica se a VM do Azure aparece como esperado no portal do Azure.

   ![Detalhes do plano de recuperação](./media/contoso-migration-rehost-vm-sql-managed-instance/failover2.png)

4. Após verificar a VM no Azure, a Contoso completa a migração para concluir o processo de migração, interromper a replicação para a VM e parar o faturamento do Site Recovery para a VM.

    ![Failover - Completar migração](./media/contoso-migration-rehost-vm-sql-managed-instance/failover3.png)

### <a name="update-the-connection-string"></a>Atualizar a cadeia de conexão

Como etapa final do processo de migração, a Contoso atualiza a cadeia de conexão do aplicativo para apontar para o banco de dados migrado em execução na Instância Gerenciada da Contoso.

1. No portal do Azure, a Contoso encontra a cadeia de conexão de conexão, selecionando **Configurações** > **Cadeias de Conexão**.

    ![Cadeias de conexão](./media/contoso-migration-rehost-vm-sql-managed-instance/failover4.png)  

2. A Contoso atualiza a cadeia de caracteres com o nome de usuário e senha da Instância Gerenciada do Banco de Dados SQL.
3. Depois que a cadeia de caracteres é configurada, a Contoso substitui a cadeia de conexão atual no arquivo web.config do aplicativo.
4. Após atualizar o arquivo e salvá-lo, o Contoso reinicia o IIS no WEBVM. Para reiniciar o IIS, a Contoso executa `IISRESET /RESTART` em uma janela do Prompt de Comando.
5. Depois que o IIS for reiniciado, o aplicativo usa o banco de dados em execução na Instância Gerenciada do Banco de Dados SQL.
6. Nesse ponto, a Contoso pode desligar no local a máquina do SQLVM. A migração foi concluída.

*Precisa de mais ajuda?*

- Saiba como [executar um failover de teste](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure). 
- Saiba como [criar um plano de recuperação](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans).
- Saiba como [fazer failover no Azure](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover).

## <a name="clean-up-after-migration"></a>Limpar após a migração

Com a migração concluída, o aplicativo SmartHotel está executando em uma VM do Azure e o banco de dados SmartHotel está disponível na Instância Gerenciada do Banco de Dados SQL do Azure.  

Agora, a Contoso precisa realizar as seguintes tarefas de limpeza:  

- Remover a máquina WEBVM do inventário de vCenter Server.
- Remover a máquina SQLVM do inventário de vCenter Server.
- Remover WEBVM e SQLVM das tarefas de backup locais.
- Atualizar a documentação interna para mostrar o novo local e endereço IP para WEBVM.
- Remover SQLVM da documentação interna. Como alternativa, a Contoso pode revisar a documentação para mostrar SQLVM como excluído e não mais no inventário de VM.
- Revisar todos os recursos que interagem com as VMs descomissionadas. Atualizar qualquer configuração ou documentação relevante para refletir a nova configuração.

## <a name="review-the-deployment"></a>Revisar a implantação

Com os recursos migrados no Azure, a Contoso precisa operacionalizar e proteger totalmente a nova infraestrutura.

### <a name="security"></a>Segurança

A equipe de segurança da Contoso revisa as VMs do Azure e a Instância Gerenciada do Banco de Dados SQL para verificar se há algum problema de segurança com a implementação:

- A equipe de segurança revisa os grupos de segurança de rede usados para controlar o acesso da VM. Grupos de segurança de rede ajudam a garantir que apenas o tráfego permitido para o aplicativo possa passar.
- A equipe de segurança da Contoso também está pensando em proteger os dados no disco, usando o Azure Disk Encryption e o Azure Key Vault.
- A equipe de segurança permite a detecção de ameaças na Instância Gerenciada. A detecção de ameaças envia um alerta para o sistema da central de serviços/equipe de segurança da Contoso para abrir um tíquete se uma ameaça for detectada. Saiba mais sobre [detecção de ameaças para Instância Gerenciada ](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-threat-detection).

     ![Segurança de Instância Gerenciada - Detecção de ameaça](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-security.png)  

Para saber mais sobre práticas de segurança para VMs, consulte [Melhores práticas de segurança para cargas de trabalho de IaaS no Azure](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control).

### <a name="backups"></a>Backups

A Contoso faz backup dos dados em WEBVM usando o serviço de Backup do Azure. Saiba mais sobre [Backup do Azure](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="licensing-and-cost-optimization"></a>Licenciamento e otimização de custo

- A Contoso possui um licenciamento existente para WEBVM. Para aproveitar os preços do Benefício Híbrido do Azure, a Contoso converte a VM do Azure existente.
- A Contoso habilita o Gerenciamento de Custos do Azure licenciado pela Cloudyn, uma subsidiária da Microsoft. O Gerenciamento de Custos é uma solução de gerenciamento de custos com várias nuvens que ajuda a Contoso a usar e gerenciar o Azure e outros recursos da nuvem. Saiba mais sobre [Gerenciamento de Custos do Azure](https://docs.microsoft.com/azure/cost-management/overview). 


## <a name="conclusion"></a>Conclusão

Neste artigo, a Contoso hospeda novamente o aplicativo SmartHotel no Azure, migrando a VM front-end do aplicativo para Azure usando o serviço do Site Recovery. A Contoso migra o banco de dados local para uma Instância Gerenciada do Banco de Dados SQL do Azure usando o Serviço de Gerenciamento de Banco de Dados do Azure.

## <a name="next-steps"></a>Próximas etapas

No próximo artigo da série, o Contoso [hospeda novamente o aplicativo SmartHotel nas VMs do Azure](contoso-migration-rehost-vm.md), usando o serviço do Azure Site Recovery.

