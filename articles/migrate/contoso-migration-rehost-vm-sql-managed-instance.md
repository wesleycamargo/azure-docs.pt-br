---
title: Re-hospeda um aplicativo Contoso no Azure migrando para uma Instância Gerenciada do Azure VM e do Azure SQL | Microsoft Docs
description: Saiba como a Contoso hospeda novamente um aplicativo local em VMs do Azure e na Instância Gerenciada do Azure SQL
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: raynew
ms.openlocfilehash: c7dc9e8406494739aa5d8f21397a606e0b74a617
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35301245"
---
# <a name="contoso-migration-rehost-an-on-premises-app-to-azure-vms-and-azure-sql-managed-instance"></a>Migração da Contoso: Re-hospeda um aplicativo local para VMs do Azure e a Instância Gerenciada do Azure SQL

Este artigo mostra como a Contoso migra sua VM front-end de aplicativo do SmartHotel para VMs do Azure usando o serviço Azure Site Recovery e o banco de dados do aplicativo para uma Instância Gerenciada do Azure SQL.

> [!NOTE]
> A Instância Gerenciada do Azure SQL está atualmente em visualização.

Este documento é o quarto de uma série de artigos que documentam como a empresa fictícia Contoso migra seus recursos locais para a nuvem do Microsoft Azure. A série inclui informações básicas e uma série de cenários que ilustram como configurar uma infraestrutura de migração e executar diferentes tipos de migrações. Cenários de crescem em complexidade e incluiremos artigos adicionais ao longo do tempo.


**Artigo** | **Detalhes** | **Status**
--- | --- | ---
[O artigo 1: Visão geral](contoso-migration-overview.md) | Fornece uma visão geral de estratégia de migração da Contoso, a série de artigos e os aplicativos de exemplo que usamos. | Disponível
[Artigo 2: Implantar uma infraestrutura do Azure](contoso-migration-infrastructure.md) | Descreve como Contoso prepara seu local e a infraestrutura do Azure para a migração. A mesma infraestrutura é usada para todos os cenários de migração da Contoso. | Disponível
[Artigo 3: Avaliar recursos locais](contoso-migration-assessment.md)  | Mostra como a Contoso é executado em uma avaliação de seu aplicativo de SmartHotel de duas camadas do local em execução no VMware. Avaliar a VMs de aplicativo com o [migrar do Azure](migrate-overview.md) service e o banco de dados do SQL Server de aplicativo com o [Assistente de migração de banco de dados do Azure](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Disponível
Artigo 4: Rehost para VMs do Azure e uma Instância Gerenciada do SQL (este artigo) | Demonstra como Contoso migra o aplicativo SmartHotel no Azure. Elas migram a VM de front-end do aplicativo usando o [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)e o banco de dados de aplicativo usando o serviço de [Migração de Banco de Dados do Azure](https://docs.microsoft.com/azure/dms/dms-overview), para migrar para uma Instância Gerenciada do SQL. | Disponível
[Artigo 5: Novo host para máquinas virtuais do Azure](contoso-migration-rehost-vm.md) | Mostra como a Contoso migra as VMs do aplicativo SmartHotel usando apenas o Site Recovery.
[Artigo 6: Novo host para máquinas virtuais do Azure e grupos de disponibilidade do SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Mostra como a Contoso migra o aplicativo SmartHotel. Eles usam o Site Recovery para migrar as máquinas virtuais do aplicativo e o serviço de Migração de Banco de Dados para migrar o banco de dados de aplicativo para um Grupo de Disponibilidade do SQL Server. | Disponível
[ Artigo 7: Re-hospede um aplicativo Linux para VMs do Azure ](contoso-migration-rehost-linux-vm.md) | Demonstra como Contoso migra o aplicativo osTicket do Linux para VMs do Azure usando o Site Recovery. | Disponível
[Artigo 8: Novo host a um aplicativo do Linux para as VMs do Azure e o Azure MySQL Server](contoso-migration-rehost-linux-vm-mysql.md) | Demonstra como a Contoso migra o aplicativo osTicket do Linux para VMs do Azure usando o Site Recovery e para uma instância do Azure MySQL Server usando o MySQL Workbench. | Disponível

Se você quiser usar o aplicativo SmartHotel de amostra usado neste artigo, faça o download dele no [ github ](https://github.com/Microsoft/SmartHotel360).

## <a name="on-premises-architecture"></a>Arquitetura local

Aqui está um diagrama que mostra a atual infraestrutura local Contoso.

![Arquitetura da Contoso](./media/contoso-migration-rehost-vm-sql-managed-instance/contoso-architecture.png)  

- A Contoso tem um data center principal localizado na cidade de Nova York do Leste dos Estados Unidos.
- Eles têm três ramificações locais adicionais nos Estados Unidos.
- O data center principal está conectado à internet com uma conexão de ethernet metro fibra (500 mbps).
- Cada ramificação está conectada localmente à internet usando conexões da classe de negócios, com túneis VPN IPSec para o data center principal. Isso permite que seus toda a rede a ser conectado permanentemente e otimiza a conectividade com a internet.
- O data center principal é totalmente virtualizado com o VMware. Eles têm dois hosts de virtualização ESXi 6.5, gerenciados pelo vCenter Server 6.5.
- A Contoso usa o Active Directory para gerenciamento de identidade e os servidores DNS na rede interna.
- Executam os controladores de domínio no data center em máquinas virtuais do VMware. Os controladores de domínio no locais branches executados em servidores físicos.



## <a name="business-drivers"></a>Geradores de negócios

A equipe de liderança de TI trabalhou em conjunto com seus parceiros comerciais para entender o que a empresa deseja atingir com essa migração:

- **Endereço de crescimento comercial**: Contoso está crescendo e como resultado, há pressão de seus sistemas locais e da infraestrutura.
- **Aumentar a eficiência**: Contoso precisa remover procedimentos desnecessários e simplificar processos para seus desenvolvedores e usuários.  Necessidades de negócios IT rápidas e tempo não resíduos ou money, fornecendo, assim, mais rápido nas necessidades do cliente.
- **Aumentar a agilidade**: Contoso de TI precisa atender melhor às necessidades dos negócios. Ele deve ser capaz de reagir mais rápido do que as alterações no marketplace, para habilitar o sucesso em uma economia global.  Ele não deve obter da forma ou se tornar um bloqueador de negócios.
- **Escala**: que a empresa cresce com êxito, TI da Contoso deve fornecer sistemas que são capazes de crescer no mesmo ritmo.

## <a name="migration-goals"></a>Metas de migração

A equipe de nuvem da Contoso fixou metas para esta migração. Essas metas são usadas para determinar o melhor método de migração:

- Após a migração, o aplicativo no Azure deve ter os mesmos recursos de desempenho de hoje em seu ambiente de VMWare no local.  Mover para a nuvem não significa que o desempenho do aplicativo é menos crítico.
- A Contoso não quiser investir nesse aplicativo.  É fundamental e importante para os negócios, mas em sua forma atual eles simplesmente querem movê-lo como está para a nuvem.
- Tarefas administrativas do banco de dados devem ser minimizadas depois que o aplicativo é migrado.
- A Contoso não deseja usar um banco de dados do SQL Azure para este aplicativo e está procurando alternativas.

## <a name="proposed-architecture"></a>Arquitetura proposta

Neste cenário:

- A Contoso quer migrar seu aplicativo de viagens local de dois níveis.
- O aplicativo é dividido em duas VMs (WEBVM e SQLVM) e localizado no host VMware ESXi **contosohost1.contoso.com** (versão 6.5)
- O ambiente VMware é gerenciado pelo vCenter Server 6.5 (**vcenter.contoso.com**), em execução em uma VM.
- Eles migram o banco de dados do aplicativo (SmartHotelDB) para uma instância do Azure SQL Managed.
- Eles migrar as máquinas virtuais VMware do local para uma VM do Azure.
- A Contoso tem um datacenter local (contoso-datacenter), com um controlador de domínio local (**contosodc1**).
- As VMs locais no datacenter Contoso, serão descomissionadas após a migração.

![Arquitetura de cenário](media/contoso-migration-rehost-vm-sql-managed-instance/architecture.png) 

### <a name="azure-services"></a>Serviços do Azure

**Serviço** | **Descrição** | **Custo**
--- | --- | ---
[Serviço de gerenciamento do banco de dados](https://docs.microsoft.com/azure/dms/dms-overview) | O DMS habilita migrações perfeitas de várias fontes de banco de dados para plataformas de dados do Azure, com o tempo de inatividade mínimo. | Saiba mais sobre [suporte para regiões](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability) para get e DMS [detalhes de preços](https://azure.microsoft.com/pricing/details/database-migration/).
[Instância Gerenciada do Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | A Instância Gerenciada é um serviço de banco de dados gerenciado que representa uma Instância do SQL Server totalmente gerenciada na nuvem do Azure. Ela compartilha o mesmo código com a versão mais recente do Mecanismo de Banco de Dados do SQL Server e tem os recursos, as melhorias de desempenho e os patches de segurança mais recentes. | O uso de Instâncias Gerenciadas do Banco de Dados SQL do Azure em execução no Azure incorre em encargos com base na capacidade. [Saiba mais](https://azure.microsoft.com/pricing/details/sql-database/managed/). 
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | O serviço coordena e gerencia a migração e a recuperação de desastre para VMs do Azure e servidores físicos e de VMs locais.  | Durante a replicação para o Azure, são gerados encargos do Armazenamento do Azure.  As VMs do Azure são criadas e incorrem em encargos quando ocorre failover. [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre encargos e preços.

 

## <a name="migration-process"></a>Processo de migração

A Contoso migrará as camadas da Web e de dados do aplicativo SmartHotel para o Azure.

1. A Contoso já tem sua infraestrutura do Azure em vigor, portanto, basta adicionar alguns componentes específicos do Azure para esse cenário.
2. A camada de dados será migrada usando o Serviço de migração de dados (DMS).  O DMS se conecta à VM do SQL Server no local através de uma conexão VPN site a site entre o datacenter da Contoso e o Azure e, em seguida, migra o banco de dados.
3. A camada da web será migrada usando uma migração de elevação e troca com o Azure Site Recovery. Isso implicará a preparação do ambiente VMware local, a configuração e a ativação da replicação e a migração das VMs, fazendo com que elas sejam reprovadas no Azure.

     ![Arquitetura de migração](media/contoso-migration-rehost-vm-sql-managed-instance/migration-architecture.png) 


## <a name="prerequisites"></a>pré-requisitos

Veja o que a Contoso (e você) precisa para esse cenário.

**Requisitos** | **Detalhes**
--- | ---
**Inscreva-se na pré-visualização** | É necessário estar registrado na Versão Prévia Pública Limitada da Instância Gerenciada do SQL. É necessária uma assinatura do Azure para [criar uma conta](https://portal.azure.com#create/Microsoft.SQLManagedInstance). A criação da conta pode levar alguns dias para ser concluída, então faça isso antes de começar a implantar esse cenário.
**Assinatura do Azure** | Você já deve ter criado uma assinatura quando realizou a avaliação no primeiro artigo desta série. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Se você criar uma conta gratuita, será o administrador da assinatura e poderá executar todas as ações.<br/><br/> Se você usar uma assinatura existente e não for o administrador, será necessário trabalhar com o administrador para receber permissões de Proprietário ou de Colaborador.<br/><br/> Se forem necessárias permissões mais granulares, leia [este artigo](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Recuperação de site (local)** | O servidor do vCenter local deve estar executando a versão 5.5, 6.0 ou 6.5<br/><br/> Um host ESXi que executa a versão 5.5, 6.0 ou 6.5<br/><br/> Uma ou mais VMs do VMware em execução no host ESXi.<br/><br/> As VMs devem atender aos [requisitos do Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Configuração de [rede](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) e de [armazenamento](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) compatível.
**DMS** | Para o DMS, é necessário um [dispositivo VPN local compatível](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).<br/><br/> É necessário poder configurar o dispositivo VPN local. Ele deve ter um endereço IPv4 público voltado para o exterior, e o endereço não pode ser localizado atrás de um dispositivo NAT.<br/><br/> Certifique-se de ter acesso ao seu banco de dados local do SQL Server.<br/><br/> O Firewall do Windows deve poder acessar o mecanismo de banco de dados de origem. [Saiba mais](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).<br/><br/> Se houver um firewall em frente do seu computador de banco de dados, adicione regras para permitir o acesso ao banco de dados e a arquivos por meio da porta SMB 445.<br/><br/> As credenciais usadas para conectar-se ao SQL Server de origem e à Instância Gerenciada de destino devem ser membros da função de servidor sysadmin.<br/><br/> É necessário um compartilhamento de rede em seu banco de dados local que o DMS possa usar para fazer backup do banco de dados de origem.<br/><br/> Certifique-se de que a conta de serviço que executa a instância do SQL Server de origem tem privilégios de gravação no compartilhamento de rede.<br/><br/> Anote um usuário do Windows (e a senha) que tem privilégios de controle total no compartilhamento de rede. O Serviço de Migração de Banco de Dados do Azure representa essas credenciais de usuário para carregar arquivos de backup para o contêiner de armazenamento do Azure.<br/><br/> O processo de instalação do SQL Server Express define o protocolo TCP/IP como **Desabilitado** por padrão. Certifique-se de que ele está habilitado.


## <a name="scenario-steps"></a>Etapas do cenário

Veja como a Contoso configurará a implantação:

> [!div class="checklist"]
> * **Etapa 1: configurar uma Instância Gerenciada do SQL Azure**: eles precisam de uma instância gerenciada pré-criada para a qual o banco de dados local do SQL Server será migrado.
> * **Etapa 2: preparar o DMS**: eles precisam registrar o provedor de migração de banco de dados, criar uma instância e criar um projeto de DMS. Eles também precisam configurar o SA URI para o DMS. Um Uniform Resource Identifier (URI) de assinatura de acesso compartilhado (SAS) fornece acesso delegado a recursos em sua conta de armazenamento, para que você possa conceder permissões limitadas a objetos de armazenamento. Eles configuram um URI do SAS para que o DMS possa acessar o contêiner da conta de armazenamento para o qual o serviço faz o upload dos arquivos de backup do SQL Server.
> * **Etapa 3: preparar o Azure para a recuperação do site**: para a recuperação do site, eles devem criar uma conta de armazenamento do Azure para armazenar dados replicados e criar um cofre do Recovery Services.
> * **Etapa 4: preparar o VMware local para recuperação do site**: a Contoso preparará contas para a descoberta de VMs e a instalação de agentes e se preparará para se conectar a VMs do Azure após o failover.
> * **Etapa 5: replique as VMs**: para configurar a replicação, elas configuram o ambiente de origem e de destino da Recuperação do Site, configuram uma política de replicação e começam a replicar as VMs para o armazenamento do Azure.
> * **Etapa 6: Migre o banco de dados com o DMS**: agora eles podem migrar o banco de dados.
> * **Etapa 7: migrar as VMs com o Site Recovery**: elas executam um failover de teste para garantir que tudo esteja funcionando e, em seguida, executam um failover completo para migrar as VMs para o Azure.


## <a name="step-1-prepare-an-azure-sql-managed-instance"></a>Etapa 1: Preparar uma instância gerenciada do SQL Azure

Para configurar uma instância gerenciada do Azure SQL, a Contoso precisa de uma sub-rede:

- A sub-rede deve ser dedicada. A sub-rede deve estar vazia, não conter nenhum outro serviço de nuvem e não deve ser uma sub-rede do gateway.
- Depois que a instância gerenciada é criada, você não deve adicionar recursos a ele.
- A sub-rede não deve ter um NSG associado a ela.
- A sub-rede deve ter uma UDR (Tabela de Rotas do Usuário) com a Internet de Próximo Salto 0.0.0.0/0 como a única rota atribuída a ela. 
- DNS personalizado opcional: se o DNS personalizado for especificado na VNet, o endereço IP do resolvedor recursivo do Azure (como 168.63.129.16) deverá ser adicionado à lista. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).
- A sub-rede não deve ter um ponto de extremidade de serviço (armazenamento ou SQL) associado a ela. Os pontos de extremidade de serviço devem ser desabilitados na rede virtual.
- A sub-rede deve ter no mínimo 16 endereços IP. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#determine-the-size-of-subnet-for-managed-instances) sobre o dimensionamento da sub-rede de instância gerenciada.
- Em seu ambiente híbrido, as configurações personalizadas de DNS são necessárias. A Contoso irá configurar as configurações de DNS para usar um ou mais dos seus servidores DNS do Azure. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns) sobre a personalização de DNS.

### <a name="set-up-a-virtual-network-for-the-managed-instance"></a>Configurar uma rede virtual para a instância gerenciada

A Contoso configura a rede virtual da seguinte maneira: 

1. A Contoso cria uma nova rede virtual (VNET-SQLMI-EU2) na região primária do Leste dos EUA 2, no grupo de recursos ContosoNetworkingRG.
2. A Contoso atribui um espaço de endereço de 10.235.0.0/24, garantindo que o intervalo não coincide com outras redes da empresa Contoso.
2. Eles adicionar duas sub-redes na rede:
    - SQLMI-DS-EUS2 (10.235.0.0.25)
    - SQLMI-SAW-EUS2 (10.235.0.128/29). Essa sub-rede será usada para anexar o diretório para a instância gerenciada (SQLMI).

    ![Rede da Instância Gerenciada](media/contoso-migration-rehost-vm-sql-managed-instance/mi-vnet.png)

6. Depois que a rede virtual e as sub-redes são implantados, Contoso correspondentes de redes da seguinte maneira:

    - Pares VNET-SQLMI-EUS2 com rede virtual-HUB-EUS2 (hub de rede virtual para o Leste dos EUA 2).
    - Pares VNET-SQLMI-EUS2 com VNET-PROD-EUS2 (rede de produção).

    ![Emparelhamento de rede](media/contoso-migration-rehost-vm-sql-managed-instance/mi-peering.png)

7. A Contoso define as configurações de DNS personalizadas. DNS apontará primeiro seus controladores de domínio do Azure. DNS do Azure será secundário. Os controladores de domínio do Contoso do Azure estão localizados da seguinte maneira:

    - Localizado na sub-rede EUS2 de controlador de domínio de produção, na rede de produção 2 Leste dos EUA (VNET-PROD-EUS2).
    - Endereço CONTOSODC3: 10.245.42.4
    - Endereço CONTOSODC4: 10.245.42.5
    - O resolvedor de DNS do Azure: 168.63.129.16

     ![DNS de rede](media/contoso-migration-rehost-vm-sql-managed-instance/mi-dns.png)

**Precisa de mais ajuda?**

- [Obtenha uma visão geral](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) de instâncias gerenciadas do SQL do Azure.
- [Saiba mais sobre](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#create-a-new-virtual-network-for-managed-instances) como criar uma rede virtual para a instância gerenciada do SQL.
- [Saiba mais sobre](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering) Configurando emparelhamento.
- [Saiba mais sobre](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns) Atualizando as configurações de DNS do AD do Azure.



### <a name="set-up-routing"></a>Configurar roteamento

A instância gerenciada é colocada em uma rede virtual privada para a Contoso precisa de uma tabela de rotas para se comunicar com o serviço de gerenciamento do Azure. Se ele não pode se comunicar com o serviço que gerencia, ele se torna inacessível.

- A tabela de rota contém um conjunto de regras (rotas) que especifica como os pacotes enviados de instância gerenciada devem ser roteados na rede virtual.
- A tabela de rotas é associada a sub-redes para instâncias gerenciadas são implantadas. Cada pacote deixando uma sub-rede é tratado com base na tabela de rotas associada.
- Uma sub-rede só pode ser associada uma tabela única rota.
- Não há nenhuma cobrança adicional para a criação de tabelas de rotas no Microsoft Azure.

1. A Contoso cria uma tabela de rotas definidas pelo usuário. A tabela de rotas é criada no grupo de recursos ContosoNetworkingRG.

    ![Tabela de rotas](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table.png)

2. Para atender aos requisitos de instância gerenciada, depois que a tabela de rota (MIRouteTable) é implantada, a Contoso adicionar uma rota com um prefixo de endereço de 0.0.0.0/0, e **tipo do próximo salto** definida como **Internet**.

    ![Prefixo de tabela de rota](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-prefix.png)
    
3. Contoso associar a tabela de rotas de sub-rede SQLMI-DB-EUS2 (na rede de rede virtual-SQLMI-EUS2). 

    ![Sub-rede da tabela de rota](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-subnet.png)
    
**Precisa de mais ajuda?**

[Saiba mais sobre](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal#create-new-route-table-and-a-route) configurar rotas para uma instância gerenciada.

### <a name="create-a-managed-instance"></a>Criar uma instância gerenciada

Agora, a Contoso pode provisionar uma instância gerenciada do SQL banco de dados.

1. Desde que a instância gerenciada atua como um aplicativo de negócios, a Contoso implantá-lo em sua região primária do Leste dos EUA 2, no grupo de recursos de ContosoRG, 
2. Eles selecionam uma camada, preços e computação de tamanho e armazenamento para a instância. [ Saiba mais ](https://azure.microsoft.com/pricing/details/sql-database/managed/) sobre preços.

    ![Instância gerenciada](media/contoso-migration-rehost-vm-sql-managed-instance/mi-create.png)

3. Após a implantação de instância gerenciada, dois novos recursos aparecem no grupo de recursos ContosoRG:

    - Uma máquina virtual de cluster caso você tenha várias instâncias gerenciadas,
    - O SQL Server gerenciado instância. 

    ![Instância gerenciada](media/contoso-migration-rehost-vm-sql-managed-instance/mi-resources.png)

**Precisa de mais ajuda?**

[Saiba mais sobre](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal) provisionamento de uma instância gerenciada.

## <a name="step-2-prepare-dms"></a>Etapa 2: Preparar DMS

Para preparar DMS Contoso precisa fazer algumas coisas:

- Registrar o provedor DMS no Azure
- Fornece acesso ao armazenamento do Azure para carregar os arquivos de backup usados para migrar um banco de dados DMS. Eles fazer isso criando um contêiner de armazenamento de blob e geram um URI acesso compartilhado assinatura (SAS) para ele. 
- Criar projeto do DMS.


Conclua as etapas da seguinte maneira:

1. A Contoso registrar o provedor de migração de banco de dados em sua assinatura.
    ![Registrar DMS](media/contoso-migration-rehost-vm-sql-managed-instance/dms-subscription.png)

2. Eles criar um contêiner de blob de armazenamento e geram um URI de SAS para que DMS pode acessá-lo.

    ![URI DE SAS](media/contoso-migration-rehost-vm-sql-managed-instance/dms-sas.png)

3. Por fim, ele criarem uma instância DMS. 

    ![Instância DMS](media/contoso-migration-rehost-vm-sql-managed-instance/dms-instance.png)

4. A Contoso coloca a instância do DMS na sub-rede PROD-DC-EUS2 da VNET-PROD-DC-EUS2.
    - Eles o colocam lá porque ele deve estar em uma VNet que possa acessar a VM do SQL Server no local por meio de um gateway de VPN.
    - O VNET-PROD-EUS2 é direcionado para o VNET-HUB-EUS2 e tem permissão para usar gateways remotos.  Isso garante que o DMS possa se comunicar conforme necessário.

        ![Rede DMS](media/contoso-migration-rehost-vm-sql-managed-instance/dms-network.png)

**Precisa de mais ajuda?**
- [ Aprenda sobre a ](https://docs.microsoft.com/azure/dms/quickstart-create-data-migration-service-portal) configuração do DMS.
- [ Saiba mais ](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-2) sobre como criar e usar o SAS.


## <a name="step-3-prepare-azure-for-the-site-recovery-service"></a>Etapa 3: preparar o Azure para o serviço de recuperação do site

Há vários elementos do Azure que a Contoso precisa para configurar o Site Recovery para a migração de sua VM da camada da Web (WEBMV)

- Uma VNet na qual os recursos com failover estão localizados.
- Uma conta de armazenamento do Azure para armazenar dados replicados. 
- Um cofre do Recovery Services no Azure.

Eles configuram o Site Recovery da seguinte maneira:

1. Como a VM é uma interface Web para o aplicativo SmartHotel, eles farão o failover da VM na rede de produção existente (VNET-PROD-EUS2) e na sub-rede (PROD-FE-EUS2), na região primária do leste dos EUA 2. Eles configuraram essa rede quando [ implantaram a infraestrutura do Azure ](contoso-migration-infrastructure.md)
2. Eles criam uma conta de armazenamento do Azure (contosovmsacc20180528). Eles está usando uma conta de finalidade geral, com padrão de armazenamento e replicação de LRS.

    ![Armazenamento de recuperação de site](media/contoso-migration-rehost-vm-sql-managed-instance/asr-storage.png)

3. Com a conta de rede e armazenamento em vigor, a Contoso agora pode criar um cofre (ContosoMigrationVault) e colocá-lo no grupo de recursos ContosoFailoverRG, na região primária do Leste dos EUA 2.

    ![Cofre dos Serviços de Recuperação](media/contoso-migration-rehost-vm-sql-managed-instance/asr-vault.png)

**Precisa de mais ajuda?**

[Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) a configuração o Azure para o Site Recovery.


## <a name="step-4-prepare-on-premises-vmware-for-site-recovery"></a>Etapa 4: preparar o VMware local para recuperação do site

Para preparar o VMware para o Site Recovery, veja o que a Contoso precisa fazer:

- Prepare uma conta no servidor vCenter ou no host vSphere ESXi para automatizar a descoberta de VMs.
- Prepare uma conta que permita a instalação automática do serviço Mobility nas VMs do VMware que você deseja replicar.
- Prepare VMs locais para que elas possam se conectar às VMs do Azure quando forem criadas após o failover.


### <a name="prepare-an-account-for-automatic-discovery"></a>Preparar uma conta para a descoberta automática

O Site Recovery precisa de acesso aos servidores VMware para:

- Descobrir VMs automaticamente. Pelo menos uma conta somente leitura é necessária.
- Orquestrar a replicação, o failover e o failback. É necessária uma conta que possa executar operações como criação e remoção de discos e ativação de VMs.

A Contoso configura a conta da seguinte maneira:

1. Cria uma função no nível do vCenter.
2. Atribui as permissões necessárias a essa função.

**Precisa de mais ajuda?**

[Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) criar e atribuir uma função para a descoberta automática.

### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparar uma conta para a instalação do serviço de Mobilidade

O serviço de Mobilidade deve ser instalado na VM que você deseja replicar.

- O Site Recovery poderá fazer uma instalação automática por push desse componente quando você habilitar a replicação da VM.
- Para instalação automática por push, é necessário preparar uma conta que o Site Recovery usará para acessar a VM.
- Especifique essa conta ao configurar a replicação no console do Azure.
- É necessária uma conta local ou de domínio com permissões para instalação no VM.

**Precisa de mais ajuda**

[Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) como criar uma conta para a instalação por push do serviço de Mobilidade.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar para conectar VMs do Azure após o failover

Após o failover no Azure, a Contoso deseja ser capaz de se conectar às VMs replicadas no Azure. Para fazer isso, há algumas coisas que eles precisam fazer na VM local antes de executar a migração: 

1. Para acesso pela Internet, eles ativam o RDP na VM local antes do failover e asseguram que as regras TCP e UDP sejam adicionadas ao perfil **Público** e que o RDP seja permitido no **Firewall do Windows** >  **Aplicativos permitidos** para todos os perfis.
2. Para acessar a VPN de site para site, eles habilitam o RDP na máquina local e permitem o RDP no **Firewall do Windows**  ->  **Aplicativos e recursos permitidos** para **} Domínio e redes privadas**.
3. Eles definem a política de SAN do sistema operacional na VM local para **OnlineAll**.

Além disso, quando eles executam um failover, precisam verificar o seguinte:

- Não deve haver atualizações do Windows pendentes na VM ao acionar um failover. Se houver, eles não poderão efetuar login na máquina virtual até que a atualização seja concluída.
- Após o failover, eles devem verificar **Diagnósticos de inicialização** para exibir uma captura de tela da VM. Se isso não funcionar, eles devem verificar se a VM está em execução e revisar essas [ dicas de solução de problemas ](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


## <a name="step-5-replicate-the-on-premises-vms-to-azure-with-site-recovery"></a>Etapa 5: Replicar máquinas virtuais locais para o Azure com a recuperação de Site

Antes de executar uma migração para o Azure, a Contoso deve configurar a replicação e habilitar a replicação para sua VM local.

### <a name="set-a-replication-goal"></a>Definir uma meta de replicação

1. No cofre, sob o nome do cofre (ContosoVMVault) definir uma meta de replicação (**Introdução** > **recuperação de Site** > **preparar infraestrutura**.
2. Eles especificam que suas máquinas estão localizados no local, eles são VMs VMware e que deseja replicar no Azure.

    ![Meta de replicação](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Confirmar planejamento de implantação

Para continuar, eles precisam confirmar que eles tenham concluído o planejamento da implantação, selecionando **Sim, eu tenho feito isso**. Nesta implantação, a Contoso está apenas migrando uma única VM e não precisa de planejamento de implantação.

### <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Agora eles precisam configurar seu ambiente de origem. Para fazer isso, eles fazem download de um modelo OVF e o usam para implementar o servidor de configuração e seus componentes associados como uma VM VMware local e altamente disponível. Os componentes do servidor incluem:

- O servidor de configuração que coordena as comunicações entre local e Azure e gerencia a replicação de dados.
- Servidor em processo que age como um gateway de replicação. Ele recebe dados de replicação, otimiza-os com caching, compactação e criptografia e os envia para o Armazenamento do Azure.
- O servidor de processo também instala o Serviço de Mobilidade nas VMs que você deseja replicar e executa a descoberta automática de VMs VMware locais.
- Depois que a VM do servidor de configuração é criada e iniciada, a Contoso pode registrá-la no cofre.


A Contoso executar essas etapas, da seguinte maneira:

1. Eles baixam o modelo OVF do portal do Azure (**Preparar Infraestrutura**  >  **Fonte**  >  **Servidor de Configuração**).
    
    ![Baixe o OVF](./media/contoso-migration-rehost-vm-sql-managed-instance/add-cs.png)

2. Eles importam o modelo para o VMware para criar e implantar a VM.

    ![Modelo de OVF](./media/contoso-migration-rehost-vm-sql-managed-instance/vcenter-wizard.png)

3.  Ao ligar a máquina virtual pela primeira vez, ela é inicializado com uma experiência de instalação do Windows Server 2016. Eles aceitam o contrato de licença e inserir uma senha de administrador.
4. Após a conclusão da instalação, eles entram na VM como administrador. No primeiro login, a Ferramenta de Configuração do Azure Site Recovery é executada por padrão.
5. Na ferramenta, eles especificam um nome para usar ao registrar o servidor de configuração no cofre.
6. A ferramenta verifica se a VM pode se conectar ao Azure. Depois que a conexão é estabelecida, eles selecionam **Login** para entrar na assinatura do Azure. As credenciais devem ter acesso ao cofre no qual o servidor de configuração será registrado. 

    [ Registrar servidor de configuração ](./media/contoso-migration-rehost-vm-sql-managed-instance/config-server-register2.png)

7. A ferramenta executa algumas tarefas de configuração e, em seguida, é reinicializada. Eles entram na máquina novamente e o Assistente de Gerenciamento do Servidor de Configuração é iniciado automaticamente.
8. No assistente, eles selecionam o NIC para receber o tráfego de replicação. Essa configuração não pode ser alterada depois de ter sido definida.
9. Eles selecionam a assinatura, o grupo de recursos e o cofre no qual registrar o servidor de configuração.
        ![cofre](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz1.png) 

10. Eles Baixe e instale o MySQL Server e VMWare PowerCLI. Em seguida, eles validar as configurações do servidor.
11. Após a validação, eles especificar o FQDN ou endereço IP do host de servidor ou vSphere do vCenter. Eles deixam a porta padrão e especifique um nome amigável para o servidor do vCenter.
12. Eles precisam especificar a conta criada anteriormente, para que a recuperação de Site pode descobrir automaticamente as máquinas virtuais do VMware que estão disponíveis para replicação. 
13. Eles especificam as credenciais para instalar o serviço de mobilidade automaticamente quando a replicação está habilitada. Para computadores do Windows, a conta precisa de privilégios de administrador local nas VMs. 

    ![vCenter](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz2.png)

7. Após a conclusão do registro, no portal do Azure, A Contoso verifica novamente se o servidor de configuração e o VMware Server estão listados na página **Fonte** no cofre. Descoberta pode levar 15 minutos ou mais. 
8. O Site Recovery então conecta-se aos servidores VMware usando as configurações especificadas e descobre VMs.

### <a name="set-up-the-target"></a>Configurar o destino

A Contoso deve configurar o ambiente de replicação de destino.

1. Em **preparar infraestrutura** > **destino**, eles selecionam as configurações de destino.
2. Recuperação de site verifica se há uma conta de armazenamento do Azure e da rede de destino especificado.

### <a name="create-a-replication-policy"></a>Criar uma política de replicação

Depois que a origem e o destino estiverem configurados, a Contoso estará pronta para criar uma política de replicação e associá-la ao servidor de configuração.

1. Em **preparar infraestrutura** > **as configurações de replicação** > **política de replicação** >  **criar e Associar**, eles criam uma política **ContosoMigrationPolicy**.
2. Eles usam as configurações padrão:
    - **Limite de RPO**: Padrão de 60 minutos. Esse valor define a frequência em que são criados os pontos de recuperação. Um alerta será gerado se a replicação contínua exceder esse limite.
    - **Retenção de ponto de recuperação**. Padrão de 24 horas. Esse valor Especifica quanto tempo a janela de retenção para cada ponto de recuperação. VMs replicadas podem ser recuperadas para qualquer ponto em uma janela.
    - **Frequência de instantâneos consistentes com o aplicativo**. Padrão de uma hora. Esse valor especifica a frequência com a qual os instantâneos consistentes com o aplicativo são criados.
 
        ![Criar política de replicação](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy.png)

5. A política é associada automaticamente ao servidor de configuração. 

    ![Associar política de replicação](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy2.png)


**Precisa de mais ajuda?**

- Você pode ler uma explicação completa de todas essas etapas em [configurar a recuperação de desastres para máquinas virtuais do VMware no local](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- As instruções detalhadas estão disponíveis para ajudá-lo a [configurar o ambiente de origem](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [implantar o servidor de configuração](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), e [definir configurações de replicação](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).

### <a name="enable-replication"></a>Habilitar a replicação

Agora, a Contoso pode começar a replicar o WebVM.

1. Em **Replicar aplicativo**  >  **Fonte**  >  **+ Replicar** eles selecionam as configurações de origem.
2. Eles indicam que desejam ativar máquinas virtuais, selecionar o servidor vCenter e o servidor de configuração.

 ![Habilitar a replicação](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication1.png)
 
3. Agora, eles especificam as configurações de destino, incluindo o grupo de recursos e a rede em que a VM do Azure estará localizada após o failover, e a conta de armazenamento na qual os dados replicados serão armazenados.

     ![Habilitar a replicação](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication2.png)

4. A Contoso seleciona WebVM para replicação. O Site Recovery instalará o Serviço de Mobilidade em cada VM quando você habilitar a replicação para ele. 

    ![Habilitar a replicação](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication3.png)

5. A Contoso verifica que a política de replicação correta está selecionada e permite a replicação para WEBVM. Eles rastreiam o progresso da replicação em **Trabalhos**. Após o trabalho de **Finalizar Proteção** ser executado, o computador estará pronto para failover.
6. Em **Essentials** no portal do Azure, a Contoso pode ver a estrutura para as VMs sendo replicadas para o Azure.

    ![Modo de exibição de infraestrutura](./media/contoso-migration-rehost-vm-sql-managed-instance/essentials.png)


**Precisa de mais ajuda?**

Você pode ler uma explicação completa de todas essas etapas em [habilitar replicação](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).

## <a name="step-6-migrate-the-database-with-dms"></a>Etapa 6: Migrar o banco de dados com o DMS

A Contoso deve criar um projeto do DMS e migrar o banco de dados.

### <a name="create-a-dms-project"></a>Criar projeto do DMS
1. Eles criam um projeto do DMS. Eles especificam o tipo de servidor de origem como SQL Server e o destino como Instância Gerenciada do Banco de Dados SQL do Azure.

     ![Projeto do DMS](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-project.png)

2. Depois de criar o projeto, o Assistente de Migração é aberto.

### <a name="migrate-the-database"></a>Migrar o banco de dados 

1. No Assistente de Migração, a Contoso especifica a VM de origem na qual o banco de dados local está localizado e as credenciais para acessá-lo.

    ![Origem do DMS](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-source.png)

2. Eles selecionam o banco de dados para migrar (SmartHotel.Registration).

    ![Banco de dados de origem do DMS](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-sourcedb.png)

3. Como destino, eles especificam o nome da Instância Gerenciada no Azure e acessam as credenciais.

    ![Alvo DMS](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-target-details.png)

4. Então, em **+ Nova Atividade**  >  **Executar Migração**, eles especificam configurações para executar a migração:
    - Credenciais de origem e destino.
    - Banco de dados para migrar.
    - O compartilhamento de rede que eles criaram na VM local. O DMS faz backups de origem para esse compartilhamento.
        - A conta de serviço que está executando a instância de origem do SQL Server deve ter privilégios de gravação nesse compartilhamento.
        - Especifique o caminho do FQDN para o compartilhamento.
    - O URI do SAS que fornece ao DMS acesso ao contêiner de conta de armazenamento para o qual o serviço faz o upload dos arquivos de backup para migração.

        ![Configurações DMS](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-migration-settings.png)

5. Eles salvam a migração e a executam.
6. Na **Visão geral**, eles monitoram o status da migração.

    ![Monitor DMS](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor1.png)

7. Após a conclusão da migração, verifique se os bancos de dados de destino existem na instância gerenciada.

    ![Monitor DMS](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor2.png)

## <a name="step-7-migrate-the-vm-with-site-recovery"></a>Etapa 7: migrar a VM com a recuperação do site

A Contoso executa um failover de teste rápido e, em seguida, migra a VM.

### <a name="run-a-test-failover"></a>Execute um teste de failover

Antes de migrar o WEBVM, um failover de teste ajuda a garantir que tudo esteja funcionando conforme o esperado. 

1. Eles executam um failover de teste, até o último ponto disponível no tempo (**Latest processed**).
2. Eles selecionam **Desligar o computador antes do início do failover**, para que o Site Recovery tente desligar a VM da fonte antes de disparar o failover. O failover continuará mesmo o desligamento falhar. 
3. O failover de Teste executa: 

    - Uma verificação de pré-requisitos será executada para conferir se todas as condições exigidas para a migração foram cumpridas.
    - O failover processa os dados para que uma VM do Azure possa ser criada. Se o ponto de recuperação mais recente for selecionado, um ponto de recuperação será criado nos dados.
    - Uma VM do Azure é criada usando os dados processados na etapa anterior.
3. Após o failover ser concluído, a réplica da VM do Azure aparecerá no portal do Azure. Eles verificam se tudo está funcionando corretamente. A VM tem o tamanho apropriado, está conectada à rede correta e está em execução. 
4. Depois de verificar o failover de teste, a Contoso limpa o failover e registra e salva as observações. 

### <a name="migrate-the-vm"></a>Migração da VM

1. Depois de verificar se o failover de teste funcionou como esperado, a Contoso cria um plano de recuperação para a migração. Eles adicionam o WEBVM ao plano.

     ![Plano de recuperação](./media/contoso-migration-rehost-vm-sql-managed-instance/recovery-plan.png)

2. Em seguida, eles executarem um failover no plano. Eles adicionam o WEBVM ao plano. Eles selecionam o ponto de recuperação mais recente e especificam que o Site Recovery deve tentar encerrar a VM local antes de acionar o failover.

    ![Failover](./media/contoso-migration-rehost-vm-sql-managed-instance/failover1.png)

3. Depois do failover, a Contoso verifica se a VM do Azure aparece conforme o esperado no portal do Azure.

   ![Plano de recuperação](./media/contoso-migration-rehost-vm-sql-managed-instance/failover2.png)

4. Depois de verificar a VM no Azure, eles concluem a migração para concluir o processo de migração, interromper a replicação para a VM e interromper o faturamento do Site Recovery para a VM.

    ![Failover](./media/contoso-migration-rehost-vm-sql-managed-instance/failover3.png)

### <a name="update-the-connection-string"></a>Atualizar a cadeia de conexão

Como etapa final do processo de migração, a Contoso atualiza a cadeia de conexão do aplicativo para apontar para o banco de dados migrado em execução no seu MI do SQL.

1. No portal do Azure, eles encontram a cadeia de conexão clicando em **Configurações**  >  **Sequências de conexão**.

    ![Failover](./media/contoso-migration-rehost-vm-sql-managed-instance/failover4.png)  

2. Eles atualizam a cadeia com o nome de usuário e a senha da Instância Gerenciada do SQL.
3. Depois que a string é configurada, eles substituem a string de conexão atual no arquivo web.config de seu aplicativo.
4. Depois de atualizar o arquivo e salvá-lo, eles reiniciam o IIS no WEBVM. Eles fazem isso com **IISRESET / RESTART** a partir de um prompt cmd.
5. Depois que o IIS for reiniciado, o aplicativo usará o banco de dados em execução na instância gerenciada do SQL.
6. Nesse ponto, a Contoso pode encerrar a máquina SQLVM no local e a migração está concluída.

**Precisa de mais ajuda?**

- [Saiba mais](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) sobre a execução de failovers de teste. 
- [Saiba](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) como criar um plano de recuperação.
- [Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) failover para o Azure.

## <a name="clean-up-after-migration"></a>Limpeza após a migração

Com a migração concluída, o aplicativo SmartHotel está sendo executado em uma VM do Azure, e o banco de dados SmartHotel está disponível na Instância Gerenciada do SQL do Azure.  

Agora, a Contoso precisa fazer alguma limpeza, da seguinte maneira:  

- Remova a máquina WEBVM do inventário do vCenter.
- Remova a máquina do SQLVM do inventário do vCenter.
- Remova o WEBVM e o SQLVM das tarefas de backup locais.
- Atualizar documentação interna mostra o novo local, endereço IP para WEBVM.
- Remova o SQLVM da documentação. Como alternativa, eles poderiam marcá-lo para mostrar como excluído e não mais no inventário da VM.
- Revise todos os recursos que interagem com as VMs desatribuídas e atualize quaisquer configurações ou documentação relevantes para refletir a nova configuração.

## <a name="review-the-deployment"></a>Revisar a implantação

Com os recursos migrados no Azure, a Contoso precisa operacionalizar e proteger totalmente sua nova infraestrutura.

### <a name="security"></a>Segurança

A equipe de segurança da Contoso revisa as VMs do Azure e a Instância Gerenciada do SQL para determinar quaisquer problemas de segurança com sua implementação.

- Eles revisam os Network Security Groups (NSGs) para a VM, para controlar o acesso. Os NSGs são usados para garantir que apenas o tráfego permitido para o aplicativo possa ser transmitido.
- Eles também estão pensando em proteger os dados no disco usando o Azure Disk Encryption e o Azure KeyVault.
- Eles permitem a detecção de ameaças na SQL Managed Instance (SQLMI). Eles enviarão alertas para seu sistema de equipe de segurança / service desk para abrir um ticket se uma ameaça for detectada. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-threat-detection).

     ![Segurança de instância gerenciada](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-security.png)  

[Leia mais](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) sobre as práticas recomendadas de segurança para máquinas virtuais.

### <a name="backups"></a>Backups
A Contoso fará o backup dos dados no WEBVM usando o serviço Backup do Azure. [Saiba mais](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="licensing-and-cost-optimization"></a>Licenciamento e otimização de custo

1. A Contoso possui licenciamento existente para o WEBVM e aproveitará o Benefício Híbrido do Azure.  Eles converterão a VM do Azure existente para aproveitar esse preço.
2. A Contoso permitirá o gerenciamento de custos do Azure licenciado por Cloudyn, uma subsidiária Microsoft. É uma solução de gerenciamento de custos com várias nuvens que ajuda a utilizar e gerenciar o Azure e outros recursos da nuvem.  [Saiba mais](https://docs.microsoft.com/azure/cost-management/overview) sobre o Gerenciamento de Custos do Azure. 


## <a name="conclusion"></a>Conclusão

Neste artigo, a Contoso hospedou novamente o aplicativo SmartHotel no Azure, migrando a VM front-end do aplicativo para o Azure usando o serviço de recuperação do site. Eles migraram o banco de dados local para uma Instância Gerenciada do Azure SQL usando o DMS.

## <a name="next-steps"></a>Próximas etapas

No próximo artigo da série, mostraremos como a Contoso hospeda novamente o aplicativo SmartHotel nas VMs do Azure usando apenas o serviço Azure Site Recovery.

