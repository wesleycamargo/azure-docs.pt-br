---
title: Migrar cargas de trabalho locais para o Azure com o Azure DMS e o Site Recovery | Microsoft Docs
description: Saiba como preparar o Azure para a migração de computadores locais usando o Serviço de Migração de Banco de Dados do Azure e o serviço do Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/21/2018
ms.author: raynew
ms.openlocfilehash: d6fc1af3c5a587ab62b86dcd4189165d96e6e3bc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32182236"
---
# <a name="scenario-2-lift-and-shift-migration-to-azure"></a>Cenário 2: deslocar e comparar a migração para o Azure

A empresa Contoso está considerando migrar para o Azure. Para testar isso, eles desejam avaliar e migrar um pequeno aplicativo local de viagens para o Azure. É um aplicativo de viagens de duas camadas, com um aplicativo Web em execução em uma VM e um banco de dados do SQL Server em uma segunda VM. O aplicativo é implantado no VMware e o ambiente é gerenciado por um vCenter Server. 

No [Cenário 1: avaliar a migração para o Azure](migrate-scenarios-assessment.md), eles usavam o AMD (Assistente de Migração de Dados) para avaliar o banco de dados do SQL Server para o aplicativo. Além disso, eles usavam o serviço Migrações para Azure para avaliar as VMs do aplicativo. Agora, nesse cenário, após a conclusão bem-sucedida da avaliação, eles desejam migrar o banco de dados para uma Instância Gerenciada do Azure SQL usando o DMS (Serviço de Migração de Banco de Dados do Azure) e computadores locais para VMs do Azure usando o serviço do Azure Site Recovery.

Se desejar experimentar o [Cenário 1](migrate-scenarios-assessment.md) e esse cenário usando esse aplicativo de viagens de exemplo, será possível baixá-lo no [GitHub](https://github.com/Microsoft/SmartHotel360).



**Serviço** | **Descrição** | **Custo**
--- | --- | ---
[Serviço de gerenciamento do banco de dados](https://docs.microsoft.com/azure/dms/dms-overview) | O DMS habilita migrações perfeitas de várias fontes de banco de dados para plataformas de dados do Azure, com o tempo de inatividade mínimo. | No momento, o serviço está em versão prévia pública (abril de 2018), e pode ser usado sem custos durante a versão prévia.<br/><br/> Observe que, para a versão prévia pública, o DMS é limitado a um [número de regiões](https://docs.microsoft.com/azure/dms/dms-overview).
[Instância Gerenciada do Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | A Instância Gerenciada é um serviço de banco de dados gerenciado que representa uma Instância do SQL Server totalmente gerenciada na nuvem do Azure. Ela compartilha o mesmo código com a versão mais recente do Mecanismo de Banco de Dados do SQL Server e tem os recursos, as melhorias de desempenho e os patches de segurança mais recentes. | O uso de Instâncias Gerenciadas do Banco de Dados SQL do Azure em execução no Azure incorre em encargos com base na capacidade. [Saiba mais](https://azure.microsoft.com/pricing/details/sql-database/managed/). 
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | O serviço coordena e gerencia a migração e a recuperação de desastre para VMs do Azure e servidores físicos e de VMs locais.  | Durante a replicação para o Azure, são gerados encargos do Armazenamento do Azure.  As VMs do Azure são criadas e incorrem em encargos quando ocorre failover. [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre encargos e preços.

Nesse cenário, configuraremos uma VPN site a site para que o DMS possa se conectar ao banco de dados local, criar uma Instância Gerenciada do Azure SQL no Azure e migrar o banco de dados. Para o Site Recovery, prepararemos o ambiente local do VMware, configuraremos a replicação e migraremos as VMs para o Azure.  


> [!IMPORTANT]
> É necessário estar registrado na Versão Prévia Pública Limitada da Instância Gerenciada do SQL. É necessária uma assinatura do Azure para [criar uma conta](https://portal.azure.com#create/Microsoft.SQLManagedInstance). A criação da conta pode levar alguns dias para ser concluída, então faça isso antes de começar a implantar esse cenário.

## <a name="architecture"></a>Arquitetura

### <a name="site-recovery"></a>Site Recovery

![Site Recovery](media/migrate-scenarios-lift-and-shift/asr-architecture.png)  

### <a name="dms"></a>DMS
![DMS](media/migrate-scenarios-lift-and-shift/dms-architecture.png)  

Neste cenário:

- A Contoso é um nome fictício que representa uma empresa comum. A Contoso deseja avaliar e migrar seu aplicativo local de viagens de duas camadas.
- A Contoso tem um datacenter local (contoso-datacenter), com um controlador de domínio local (**contosodc1**).
- O aplicativo de viagem interno tem camadas em duas VMs, WEBVM e SQLVM e está localizado no host VMware ESXi (**contosohost1.contoso.com**).
- O ambiente VMware é gerenciado pelo vCenter Server (**vcenter.contoso.com**) em execução em uma VM.

## <a name="prerequisites"></a>pré-requisitos

Se desejar executar esse cenário, veja o que você deve ter.

Requisitos | Detalhes
--- | ---
**Assinatura do Azure** | Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Se você criar uma conta gratuita, será o administrador da assinatura e poderá executar todas as ações.<br/><br/> Se você usar uma assinatura existente e não for o administrador, será necessário trabalhar com o administrador para receber permissões de Proprietário ou de Colaborador.<br/><br/> Se forem necessárias permissões mais granulares, leia [este artigo](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Recuperação de site (local)** | Um vCenter Server local que executa a versão 5.5, 6.0 ou 6.5<br/><br/> Um host ESXi que executa a versão 5.5, 6.0 ou 6.5<br/><br/> Uma ou mais VMs do VMware em execução no host ESXi.<br/><br/> As VMs devem atender aos [requisitos do Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).<br/><br/> Configuração de [rede](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) e de [armazenamento](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) compatível.
**DMS** | Para o DMS, é necessário um [dispositivo VPN local compatível](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).<br/><br/> É necessário poder configurar o dispositivo VPN local. Ele deve ter um endereço IPv4 público voltado para o exterior, e o endereço não pode ser localizado atrás de um dispositivo NAT.<br/><br/> Certifique-se de ter acesso ao seu banco de dados local do SQL Server.<br/><br/> O Firewall do Windows deve poder acessar o mecanismo de banco de dados de origem. [Saiba mais](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).<br/><br/> Se houver um firewall em frente do seu computador de banco de dados, adicione regras para permitir o acesso ao banco de dados e a arquivos por meio da porta SMB 445.<br/><br/> As credenciais usadas para conectar-se ao SQL Server de origem e à Instância Gerenciada de destino devem ser membros da função de servidor sysadmin.<br/><br/> É necessário um compartilhamento de rede em seu banco de dados local que o DMS possa usar para fazer backup do banco de dados de origem.<br/><br/> Certifique-se de que a conta de serviço que executa a instância do SQL Server de origem tem privilégios de gravação no compartilhamento de rede.<br/><br/> Anote um usuário do Windows (e a senha) que tem privilégios de controle total no compartilhamento de rede. O Serviço de Migração de Banco de Dados do Azure representa essas credenciais de usuário para carregar arquivos de backup para o contêiner de armazenamento do Azure.<br/><br/> O processo de instalação do SQL Server Express define o protocolo TCP/IP como **Desabilitado** por padrão. Certifique-se de que ele está habilitado.


# <a name="scenario-overview"></a>Visão geral do cenário

Veja o que vamos fazer:

> [!div class="checklist"]
> * **Etapa 1: Configurar uma conexão VPN site a site**: o DMS conecta-se ao seu banco de dados local por uma conexão VPN site a site. Criamos uma rede virtual para a conexão VPN e posteriormente reutilizaremos essa rede para o Site Recovery. A rede criada deve estar localizada na região na qual você criará um cofre dos Serviços de Recuperação.
> * **Etapa 2: Configurar uma Instância Gerenciada do SQL Azure**: é necessária uma instância gerenciada pré-criada para a qual o banco de dados do SQL Server local será migrado.
> * **Etapa 3: Configurar o URI de SAS para o DMS**: um URI (Uniform Resource Identifier) de SAS (assinatura de acesso compartilhado) fornece acesso delegado aos recursos em sua conta de armazenamento para que você possa conceder permissões limitadas a objetos de armazenamento. Configure um URI de SAS para que o DMS possa acessar o contêiner da conta de armazenamento para o qual o serviço carrega os arquivos de backup do SQL Server.
> * **Etapa 4: Preparar DMS**: registre o provedor de Migração de Banco de Dados, crie uma instância e, em seguida, crie um projeto do DMS.
> * **Etapa 5: Preparar o Azure para o Site Recovery**: crie uma conta de armazenamento do Azure para armazenar os dados replicados.
> * **Etapa 6: Preparar VMware local para o Site Recovery**: prepare contas para a instalação de descoberta e agente VM e se preparar para conectar a máquinas virtuais do Azure após o failover. 
> * **Etapa 7: Replicar VMs**: configure o ambiente de origem e de destino do Site Recovery, defina uma política de replicação e comece a replicar VMs para o armazenamento do Azure.
> * **Etapa 8: Migrar o banco de dados com o DMS**: execute uma migração de banco de dados.
> * **Etapa 9: Migrar as VMs com o Site Recovery**: execute um failover para migrar as VMs para o Azure.


## <a name="step-1-set-up-site-to-site-vpn-connection"></a>Etapa 1: Configurar conexão VPN site a site

Para preparar a conexão VPN site a site, configure uma rede virtual e sub-redes, crie um gateway VPN para a rede virtual e configure um gateway de rede local para que o Azure saiba como se conectar à sua VPN local. Em seguida, crie e verifique a conexão site a site.

### <a name="set-up-a-virtual-network"></a>Configurar uma rede virtual

Crie uma Rede Virtual do Azure para fornecer ao DMS conectividade site a site com seu SQL Server local.


1. No [portal do Azure](https://portal.azure.com), clique em **Criar um recurso**. Selecione **Rede** > **Rede virtual**.
2. Em **Rede Virtual** > **Selecionar um modelo de implantação**, selecione **Gerenciador de Recursos** > **Criar**
3. Em **Criar rede virtual** > **Nome**, insira um nome de rede exclusivo. Para o nosso cenário, **ContosoVNET**.
4. Em **Espaço de Endereço**, adicione o intervalo de endereços IP. O intervalo não deve se sobrepor ao seu espaço de endereço local.
5. No **Grupo de recursos**, crie um novo grupo ou selecione um existente. Nesse cenário, estamos usando **ContosoRG**.
6. Em **Local**, selecione a região na qual deseja criar a rede virtual. Estamos usando **Leste dos EUA 2**.
7. Em **Sub-rede**, adicione o nome da primeira sub-rede e o intervalo de endereços. Criamos a sub-rede **Aplicativos**.
8. Desabilitar pontos de extremidade de serviço.

    ![Criar uma rede virtual](media/migrate-scenarios-lift-and-shift/vnet-create-network.png)

9. Selecione **Fixar no painel** para poder localizar facilmente a sua rede no futuro e, em seguida, clique em **Criar**.
10. A rede virtual leva alguns segundos para ser criada. Após a criação, verifique-a no painel do portal do Azure.
11. Certifique-se de que essas portas de comunicação são permitidas em sua rede virtual: 443, 53, 9354, 445, 12000.


### <a name="set-up-subnets"></a>Configurar sub-redes

Teremos quatro sub-redes na rede do Azure:

![Lista de sub-redes](media/migrate-scenarios-lift-and-shift/vnet-subnet-list.png)

Será possível configurar a primeira sub-rede (Aplicativos) quando você tiver criado a rede. Agora crie o restante das sub-redes. A sub-rede do gateway é usada pela conexão do gateway VPN para que o Azure envie tráfego por meio de uma conexão VPN com seu site local.

1. Na rede virtual, em **Configurações**, clique em **Sub-rede** > **+Sub-rede**.
2. Configure uma sub-rede **Datamigration**, com esse intervalo de endereços e, em seguida, clique em **OK**.

    ![Sub-rede de dados](media/migrate-scenarios-lift-and-shift/vnet-subnet-data.png)  


3. Em **+Sub-rede**, configure uma sub-rede **Identidade**, com esse intervalo de endereços e, em seguida, clique em **OK**.
    ![Sub-rede de Identidade](media/migrate-scenarios-lift-and-shift/vnet-subnet-identity.png)

4. Em **Sub-rede**, clique em **Sub-rede de gateway** e, em seguida, clique em **OK**.
    - Essa sub-rede contém os endereços IP que serão usados pelo gateway VPN para conexões VPN.
    - O nome dessa sub-rede é definido automaticamente para o Azure poder reconhecê-lo.
    - Ajuste o intervalo de endereços preenchido automaticamente para coincidir com sua sub-rede local. 

    ![gateway de sub-rede](media/migrate-scenarios-lift-and-shift/vnet-subnet-gateway.png)
    
### <a name="set-up-a-virtual-network-gateway"></a>Configurar um gateway de rede virtual

1. No lado esquerdo da página do portal, clique em **+** e digite 'Gateway de Rede Virtual' na pesquisa. Em **Resultados**, clique no **Gateway de rede virtual**.
2. Na parte inferior da página ‘Gateway de rede virtual’, clique em **Criar**.
3. Em **Criar gateway de rede virtual** >  **Nome**, especifique um nome para o objeto de gateway.
4. Em **Tipo de gateway**, selecione **VPN**.
5. Em **Tipo de VPN**, selecione **Baseada em rota**.
6. Em **SKU**, selecione o SKU do gateway no menu suspenso. As SKUs listadas na lista suspensa dependem do tipo de VPN selecionado. Não habilite o modo ativo-ativo. [Saiba mais](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#gwsku) sobre SKUs.
7. Em **Rede virtual** > **Escolha uma rede virtual**, adicione a rede criada. O gateway será adicionado a essa rede.
8. Em **Primeira configuração de IP**, especifique um endereço IP público.  Um gateway de VPN deve ter um endereço IP público. O endereço é atribuído dinamicamente ao recurso quando o gateway de VPN é criado. No momento, apenas endereços dinâmicos são compatíveis, mas o endereço IP não é alterado após sua atribuição.
    - Clique em **Criar configuração de IP do gateway**. Em **Escolher o endereço IP público**, clique em **+Criar novo**.
    - Em **Criar endereço IP público**, especifique um nome para seu endereço IP público (Contoso-Gateway). Deixe o SKU como **Básico** e clique em **OK** para salvar as alterações.
        
    ![Gateway de rede virtual](media/migrate-scenarios-lift-and-shift/vnet-create-vpn-gateway2.png) 

9. Clique em **Criar** para começar a criar o gateway de VPN. As configurações são validadas e "Implantando gateway de rede virtual" é exibido no painel.

    ![Validar gateway de rede virtual](media/migrate-scenarios-lift-and-shift/vnet-vpn-gateway-validate.png)
    
A criação de um gateway pode levar até 45 minutos. Talvez seja necessário atualizar a página do portal para ver o status concluído.

### <a name="set-up-a-local-network-gateway"></a>Configurar um gateway de rede local

Configure um gateway de rede local no Azure para representar seu site local.

1. No portal, clique em **+Criar um recurso**.
2. Na caixa de pesquisa da página, insira **Gateway de rede local**, depois pressione **Enter** para pesquisar. Nos resultados, clique em **Gateway de rede local** > **Criar**.
3. Em **Criar gateway de rede local** > **Nome**, especifique um nome usado pelo Azure para seu objeto de gateway de rede local.
4. **Endereço IP**, especifique o endereço IP público do dispositivo VPN local com o qual o Azure se conectará. O endereço IP não deve usar o NAT, e o Azure deve ser capaz de acessá-lo.
5. Em **Espaço de endereço**, digite os intervalos de endereços de rede local que serão encaminhados por meio do gateway VPN ao dispositivo local. Certifique-se de que eles não se sobreponham aos intervalos na Rede Virtual do Azure.
6. **Configurar o BGP** não é relevante para esse cenário.
7. Em **Assinatura**, verifique se a assinatura está correta.
8. Em **Grupo de recursos**: selecione o grupo de recursos usado para criar a rede (ContosoRG).
9. Em **Local**, selecione a região na qual você criou a rede virtual.

    ![Gateway local](media/migrate-scenarios-lift-and-shift/vnet-create-local-gateway.png)

4. Clique em **Criar** para criar o gateway local.

### <a name="configure-your-on-premises-vpn-device"></a>Configurar seu dispositivo VPN local

Veja o que é necessário para configurar em seu dispositivo VPN local:

- O endereço IPv4 público do gateway de Rede Virtual do Azure recém-criado.
- A chave pré-compartilhada (a chave usada ao criar a conexão VPN site a site).  

Para ajudá-lo a configurar seu dispositivo VPN local:

- Dependendo do dispositivo VPN local que você tem, talvez seja possível baixar um script de configuração do dispositivo VPN. [Saiba mais](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-download-vpndevicescript).
- Examine [mais recursos úteis](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#VPNDevice).

### <a name="create-the-vpn-connection"></a>Criar a conexão VPN

Agora, crie a conexão VPN site a site entre o gateway de rede virtual e o dispositivo VPN local.

1. Abra a página de rede virtual em **Redes virtuais** > **Visão geral** > **Dispositivos conectados**. 
2. Clique em **Conexões** > **+Adicionar**.
3. Em **Adicionar conexão** > **Nome**, especifique um nome para a conexão.
4. Em **Tipo de conexão**, selecione **Site a site (IPsec)**.
5. O valor do **Gateway de rede virtual** é fixo porque você está se conectando desse gateway.
6. Em **Gateway de rede Local**, especifique o gateway de rede local criado.
7. Em **Chave compartilhada**, especifique a chave que corresponde ao valor que você está usando para seu dispositivo VPN local. O exemplo usa “abc123”, mas você pode (e deve) usar algo mais complexo. O importante é que o valor especificado aqui deve ser o mesmo valor especificado ao configurar seu dispositivo VPN.
8. Os valores para **Assinatura**, **Grupo de recursos** e **Local** são fixos.

    ![Conexão VPN](media/migrate-scenarios-lift-and-shift/vpn-connection.png) 

4. Clique em **OK** para criar a conexão. Você verá "Criando conexão" piscar na tela.
5. Após a criação da conexão, exiba-a na página **Conexões** do gateway de rede virtual. O status será alterado de Desconhecido > Conectando > Êxito.

### <a name="verify-the-vpn-connection"></a>Verificar a conexão VPN

No portal do Azure, você pode exibir o status da conexão de um Gateway de VPN do Resource Manager navegando até a conexão. 

1. Clique em **Todos os recursos** e navegue até seu gateway de rede virtual.
2. Na página do gateway de rede virtual, clique em **Conexões**. Você pode ver o status de cada conexão.
3. Clique no nome da conexão e exiba mais informações em **Conceitos básicos**. O status será 'Êxito' e 'Conectado' quando você tiver realizado uma conexão bem-sucedida.

Agora verifique se é possível se conectar à VM do SQL Server pela VPN. Use uma conexão de Área de Trabalho Remota e conecte-se ao endereço IP da VM.



## <a name="step-2-prepare-an-azure-sql-managed-instance"></a>Etapa 2: Preparar uma Instância Gerenciada do Azure SQL

### <a name="set-up-a-virtual-network"></a>Configurar uma rede virtual

Para esse cenário, será necessário criar outra rede virtual dedicada para a Instância Gerenciada do Azure SQL.  Observe os seguintes requisitos:

- Para criar a instância gerenciada, é necessária uma sub-rede dedicada.
- A sub-rede deve estar vazia, não conter nenhum outro serviço de nuvem e não deve ser uma sub-rede do gateway. Após a criação da instância gerenciada, não será necessário adicionar recursos à sub-rede.
- A sub-rede não deve ter um NSG associado a ela.
- A sub-rede deve ter uma UDR (Tabela de Rotas do Usuário) com a Internet de Próximo Salto 0.0.0.0/0 como a única rota atribuída a ela. 
- DNS personalizado opcional: se o DNS personalizado for especificado na VNet, o endereço IP do resolvedor recursivo do Azure (como 168.63.129.16) deverá ser adicionado à lista. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns).
- A sub-rede não deve ter um ponto de extremidade de serviço (armazenamento ou SQL) associado a ela. Os pontos de extremidade de serviço devem ser desabilitados na rede virtual.
- A sub-rede deve ter no mínimo 16 endereços IP. [Saiba mais](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#determine-the-size-of-subnet-for-managed-instances) sobre o dimensionamento da sub-rede de instância gerenciada.

Configure a rede virtual da seguinte maneira:

1.  No portal do Azure, clique em **Criar um recurso**. 
2. Selecione **Rede** > **Rede virtual**.
3. Em **Rede Virtual** > **Selecionar um modelo de implantação**, selecione **Gerenciador de Recursos** > **Criar**.
4. Em **Criar rede virtual** > **Nome**, insira um nome de rede exclusivo. Para nosso cenário, **ContosoVNETSQLMI**.
5. No **Espaço de endereço**, adicione o intervalo de endereços IP mostrado abaixo. O intervalo não deve se sobrepor ao espaço de endereço local e da ContosoVNET.
6. No **Grupo de recursos**, crie um novo grupo ou selecione um existente. Nesse cenário, estamos usando **ContosoRG**.
7. Em **Local**, selecione a região na qual deseja criar a rede virtual. Estamos usando **Leste dos EUA 2**.
8. Em **Sub-rede**, adicione o nome da primeira sub-rede e o intervalo de endereços. Criamos a sub-rede **ManagedInstances**.
9. Desabilitar pontos de extremidade de serviço.

    ![Rede da Instância Gerenciada](media/migrate-scenarios-lift-and-shift/network-mi.png)

10. Após a implantação da rede, é necessário alterar as configurações de DNS. Nesse cenário, primeiro a DNS aponta para um controlador de domínio na sub-rede de identidade usando um endereço IP privado estático (172.16.3.4) e, em seguida, o resolvedor de DNS do Azure 168.63.129.16.

    ![Rede da Instância Gerenciada](media/migrate-scenarios-lift-and-shift/network-mi2.png)


### <a name="set-up-routing"></a>Configurar roteamento

1. Clique em **Criar um recurso** no canto superior esquerdo do Portal do Azure. Clique na página **Tabela de rotas** > **Criar na tabela de rotas**.
2. Em **Criar tabela de rotas** > **Nome**, especifique um nome para a tabela.
3. Selecione sua assinatura, grupo de recursos e local. Deixe o BGP desabilitado e clique em **Criar**.
    ![Tabela de rotas](media/migrate-scenarios-lift-and-shift/route-table.png)

4. Após a criação da tabela de rotas, abra-a e clique em **Rotas** > **+Adicionar**.
5. Em **Adicionar rota** > **Nome**, especifique um nome para a rota.
6. Em **Prefixo de endereço**, especifique 0.0.0.0/0.
7. Em **Tipo do próximo salto**, selecione **Internet**. Em seguida, clique em **OK**.

     ![Tabela de rotas](media/migrate-scenarios-lift-and-shift/route-table2.png)


### <a name="apply-the-route-to-the-managed-instance-subnet"></a>Aplicar a rota à sub-rede de instância gerenciada

1. Abra a rede virtual criada. Clique em **Sub-redes** > nome-da-sub-rede.
2. Clique em **Tabela de rotas** > nome-da-tabela. Em seguida, clique em **Salvar**.

     ![Tabela de rotas](media/migrate-scenarios-lift-and-shift/route-table3.png)

### <a name="create-a-managed-instance"></a>Criar uma instância gerenciada

1. Clique em **Criar um recurso**, localize **Instância Gerenciada** e selecione **Instância Gerenciada do Banco de Dados SQL do Azure (versão prévia)**.
2. Clique em **Criar**.
3. Em **Instância Gerenciada do SQL**, selecione sua assinatura e verifique que os **Termos de versão prévia** mostram **Aceito**.
4. Em **Nome da Instância Gerenciada**, especifique um nome. 
5. Especifique um nome de usuário e senha como administrador para a instância.
6. Selecione seu grupo de recursos, local e a rede virtual da instância.
7. Clique em **Tipo de preço** para dimensionar a computação e o armazenamento. Por padrão, a instância obtém 32 GB de espaço de armazenamento sem custo (abril de 2018).
8. Especifique o armazenamento e o número de núcleos virtuais.
9. Clique em **Aplicar** para salvar as configurações e em **Criar** para implantar a instância gerenciada. Para exibir o status da implantação, clique em **Notificações** e em **Implantação em andamento**.

    ![Instância gerenciada](media/migrate-scenarios-lift-and-shift/mi-1.png)

Após a conclusão da implantação da Instância Gerenciada, dois novos recursos serão exibidos no grupo de recursos ContosoRG: cluster virtual das Instâncias gerenciadas e a Instância Gerenciada do SQL. Ambos estão no local Leste dos EUA 2.

![Instância gerenciada](media/migrate-scenarios-lift-and-shift/managed-instance2.png)


## <a name="step-3-get-a-sas-uri-for-dms"></a>Etapa 3: Obter um URI de SAS para o DMS

Obtenha um URI de SAS para que o DMS possa acessar o contêiner de conta de armazenamento, para carregar os arquivos de backup usados para migrar bancos de dados para a Instância Gerenciada do Banco de Dados SQL do Azure. 

1. Abra o Gerenciador de Armazenamento (Preview).
2. No painel esquerdo, expanda a conta de armazenamento que contém o contêiner de blobs para o qual você deseja obter uma SAS. Expanda os **Contêineres de Blob**da conta de armazenamento.
3. Clique com o botão direito do mouse no contêiner e selecione **Obter Assinatura de Acesso Compartilhado**.

     ![SAS](media/migrate-scenarios-lift-and-shift/sas-1.png)

4. Em **Assinatura de Acesso Compartilhado**, especifique a política, as datas de início e de vencimento, o fuso horário e os níveis de acesso que você deseja para o recurso. Em seguida, clique em **Criar**.

    ![SAS](media/migrate-scenarios-lift-and-shift/sas-2.png)

5. Uma segunda caixa de diálogo exibe o contêiner de blobs juntamente com a URL e as QueryStrings que você pode usar para acessar o recurso de armazenamento. Selecione **Cópia** para copiar os valores. Mantenha-os em um local seguro. Você precisa deles ao configurar o DMS.

      ![SAS](media/migrate-scenarios-lift-and-shift/sas-3.png)  

## <a name="step-4-prepare-dms"></a>Etapa 4: Preparar o DMS

Registre o provedor de Migração de Banco de Dados e crie uma instância.

### <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrar o provedor de recursos Microsoft.DataMigration

1. Em sua assinatura, selecione **Provedores de recursos**. 
2. Pesquise "migração" e, em seguida, à direita de Microsoft.DataMigration, selecione **Registrar**. 


### <a name="create-an-instance"></a>Escolher uma instância

1. Selecione **+ Criar um recurso**, pesquise "Serviço de Migração de Banco de Dados do Azure" e, em seguida, selecione **Serviço de Migração de Banco de Dados do Azure** na lista suspensa.
2. Na tela Serviço de Migração de Banco de Dados do Azure (versão prévia), selecione **Criar**.
3. Especifique um nome para o serviço, a assinatura, o grupo de recursos, a rede virtual e o tipo de preço.
4. Selecione **Criar** para criar a conta.

    ![DMS](media/migrate-scenarios-lift-and-shift/dms-instance.png)  




## <a name="step-5-prepare-azure-for-the-site-recovery-service"></a>Etapa 5: Preparar o Azure para o serviço do Site Recovery

### <a name="set-up-a-virtual-network"></a>Configurar uma rede virtual

São necessárias uma rede do Azure na qual as VMs do Azure estarão localizadas quando forem criadas após o failover e uma conta de armazenamento do Azure na qual os dados replicados serão armazenados.

- Para os fins desse cenário, usaremos a rede do Azure criada anteriormente para o DMS.
- Observe que a rede usada deve estar na mesma região que o cofre do Site Recovery.


### <a name="create-an-azure-storage-account"></a>Criar uma conta de armazenamento do Azure

O Site Recovery replica dados de VM para o armazenamento do Azure. As VMs do Azure são criadas do armazenamento quando há failover do local para o Azure.

1. No menu [Portal do Azure](https://portal.azure.com), selecione **Novo** > **Armazenamento** > **Conta de armazenamento**.
2. Em **Criar conta de armazenamento**, insira um nome para a conta. Para esses tutoriais, use o nome **contosovmsacct1910171607**. O nome deve ser exclusivo dentro do Azure e ter entre 3 e 24 caracteres, contendo apenas números e letras minúsculas.
3. Em **Modelo de implantação**, selecione **Gerenciador de Recursos**.
4. Em **Tipo de conta**, selecione **Uso geral**. Em **Desempenho**, selecione **Padrão**. Não selecione armazenamento de blobs.
5. Em **Replicação**, selecione o padrão **Armazenamento com redundância geográfica com acesso de leitura** para redundância de armazenamento.
6. Em **Assinatura**, selecione a assinatura na qual você deseja criar a nova conta de armazenamento.
7. Em **Grupo de recursos**, digite um novo grupo de recursos. Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Para estes tutoriais, use o nome **ContosoRG**.
8. Em **Localização**, selecione a localização geográfica para sua conta de armazenamento. A conta de armazenamento deve estar na mesma região do cofre de Serviços de Recuperação. Nesse cenário, estamos usando a região **Leste dos EUA 2**.

   ![Criar uma conta de armazenamento](media/migrate-scenarios-lift-and-shift/create-storageacct.png)

9. Selecione **Criar** para criar a conta de armazenamento.

### <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

1. No portal do Azure, selecione **Criar um recurso** > **Monitoramento + Gerenciamento** > **Backup e Site Recovery**.
2. Em **Nome**, digite um nome amigável para identificar o cofre. Para este tutorial, use **ContosoVMVault**.
3. Em **Grupo de recursos**, selecione o grupo de recursos existente denominado **contosoRG**.
4. Em **Local**, insira a região do Azure **Leste dos EUA 2**.
5. Para acessar rapidamente o cofre do painel, selecione **Fixar no painel** > **Criar**.
O novo cofre é exibido em **Painel** > **Todos os recursos** e na página principal **Cofres dos Serviços de Recuperação**.

## <a name="step-6-prepare-on-premises-vmware-for-site-recovery"></a>Etapa 6: Preparar VMware local para o Site Recovery

Para preparar o VMware para o Site Recovery, veja o que é necessário:

- Preparar uma conta no vCenter Server ou no host ESXi do vSphere para automatizar a descoberta de VM
- Preparar uma conta para a instalação automática do serviço de Mobilidade nas VMs VMware
- Prepare VMs locais se desejar se conectar a VMs do Azure após o failover.


### <a name="prepare-an-account-for-automatic-discovery"></a>Preparar uma conta para a descoberta automática

O Site Recovery precisa de acesso aos servidores VMware para:

- Descobrir VMs automaticamente. Pelo menos uma conta somente leitura é necessária.
- Orquestrar a replicação, o failover e o failback. É necessária uma conta que possa executar operações como criação e remoção de discos e ativação de VMs.

Crie a conta da seguinte maneira:

1. Para usar uma conta dedicada, crie uma função no nível do vCenter. Dê um nome à função como **Azure_Site_Recovery**.
2. Atribua à função as permissões resumidas na tabela a seguir.
3. Crie um usuário no vCenter Server ou no host do vSphere. Atribua a função ao usuário.

**Tarefa** | **Função/Permissões** | **Detalhes**
--- | --- | ---
**Descoberta VM** | Pelo menos um usuário somente leitura<br/><br/> Objeto de data center –> Propagar para o objeto filho, função = somente leitura | Usuário atribuído no nível de datacenter e tem acesso a todos os objetos no datacenter.<br/><br/> Para restringir o acesso, atribua a função **Nenhum acesso** com o objeto **Propagar para filho** aos objetos filho (hosts vSphere, armazenamentos de dados, VMs e redes).
**Replicação, failover, failback totais** |  Crie uma função (Azure_Site_Recovery) com as permissões necessárias e, em seguida, atribua a função a um usuário ou grupo do VMware<br/><br/> Objeto de Data Center –> Propagar para o Objeto Filho, função = Azure_Site_Recovery<br/><br/> Armazenamento de dados -> alocar espaço, procurar armazenamento de dados, operações de arquivo de baixo nível, remover arquivo, atualizar arquivos de máquina virtual<br/><br/> Rede -> Atribuição de rede<br/><br/> Recurso -> Atribuir VM ao pool de recursos, migrar VM desligada, migrar VM ligada<br/><br/> Tarefas -> Criar tarefa, atualizar tarefa<br/><br/> Máquina virtual -> Configuração<br/><br/> Máquina virtual -> Interagir -> responder à pergunta, conexão de dispositivo, configurar mídia de CD, configurar mídia de disquete, desligar, ligar, instalação de ferramentas VMware<br/><br/> Máquina virtual -> Inventário -> Criar, registrar, cancelar registro<br/><br/> Máquina virtual -> Provisionamento -> Permitir download de máquina virtual, permitir upload de arquivos de máquina virtual<br/><br/> Máquina virtual -> Instantâneos -> Remover instantâneos | Usuário atribuído no nível de datacenter e tem acesso a todos os objetos no datacenter.<br/><br/> Para restringir o acesso, atribua a função **Nenhum acesso** com o objeto **Propagar para filho** aos objetos filho (hosts vSphere, armazenamentos de dados, VMs e redes).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparar uma conta para a instalação do serviço de Mobilidade

O serviço de Mobilidade deve ser instalado na VM que você deseja replicar.

- O Site Recovery poderá fazer uma instalação automática por push desse componente quando você habilitar a replicação da VM.
- Para instalação automática por push, é necessário preparar uma conta que o Site Recovery usará para acessar a VM.
- Especifique essa conta ao configurar a replicação no console do Azure.
- É necessária uma conta local ou de domínio com permissões para instalação no VM.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar para conectar VMs do Azure após o failover

Após o failover no Azure, conecte-se às VMs replicadas no Azure de sua rede local.

Para se conectar a VMs do Windows usando o RDP após o failover, faça o seguinte:

1. Para acessar a Internet, habilite o RDP na VM local antes do failover. Certifique-se de que as regras TCP e UDP são adicionadas para o perfil **Público** e que o RDP é permitido no **Firewall do Windows** > **Aplicativos Permitidos** para todos os perfis.
2. Para acessar por meio da VPN site a site, habilite o RDP no computador local. O RDP deve ser permitido no **Firewall do Windows** -> **Aplicativos e recursos permitidos** para redes de **Domínio e Privadas**.
3. Verifique se a política de SAN do sistema operacional está definida como **OnlineAll**. [Saiba mais](https://support.microsoft.com/kb/3031135).
4. Não deve haver nenhuma atualização pendente do Windows na VM quando você dispara um failover. Se houver, não será possível fazer logon na máquina virtual até que a atualização seja concluída.
5. Na VM do Microsoft Azure após o failover, verifique o **Diagnóstico de inicialização** para exibir uma captura de tela da VM. Se você não puder se conectar, verifique se a VM está em execução e examine estas [dicas de solução de problemas](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).



## <a name="step-7-replicate-vms-with-site-recovery"></a>Etapa 7: Replicar VMs com o Site Recovery

### <a name="select-a-replication-goal"></a>Selecione uma meta de replicação

1. Em **Cofres dos Serviços de Recuperação**, selecione no nome do cofre **ContosoVMVault**.
2. Em **Introdução**, selecione Site Recovery. Depois selecione **Preparar Infraestrutura**.
3. Em **Objetivo de proteção** > **Onde os seus computadores estão localizados**, selecione **local**.
4. Em **Para qual deseja replicar os seus computadores**, selecione **Para o Azure**.
5. Em **Os seus computadores estão virtualizados?**, selecione **Sim, com o Hipervisor do VMware vSphere**. Depois, selecione **OK**.

    ![Modelo de OVF](./media/migrate-scenarios-lift-and-shift/replication-goal.png)


### <a name="confirm-deployment-planning"></a>Confirmar planejamento de implantação

Em **Planejamento de Implantação**, clique em **Sim, eu fiz isso**, na lista suspensa.

### <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem


Para configurar o ambiente de origem, é necessário um computador local único e altamente disponível para hospedar os componentes locais do Site Recovery. Os componentes incluem o servidor de configuração e o servidor de processo.

- O servidor de configuração coordena a comunicação entre o ambiente local e o Azure, além de gerenciar a replicação de dados.
- O servidor de processo atua como um gateway de replicação. Ele recebe dados de replicação, otimiza-os com caching, compactação e criptografia e os envia para o Armazenamento do Azure.
- O servidor de processo também instala o Serviço de Mobilidade nas VMs que você deseja replicar e executa a descoberta automática de VMs VMware locais.


Para configurar o servidor de configuração como uma VM do VMware altamente disponível:

- Baixe um modelo OVF (Open Virtualization Format) preparado.
- Importe o modelo para o VMware para criar a VM.
- Configure o servidor de configuração e registre-o no cofre. 

Após o registro, o Site Discovery descobre VMs locais da VMware.



#### <a name="download-the-vm-template"></a>Baixe o modelo de VM

1. No cofre, vá para **Preparar infraestrutura** > **Origem**.
2. Em **Preparar origem**, selecione **+Servidor de configuração**.

    ![Baixar o modelo](./media/migrate-scenarios-lift-and-shift/new-cs.png)

4. Em **Adicionar Servidor**, verifique se o **Servidor de configuração para VMware** aparece em **Tipo de servidor**.
2. Baixe o modelo de OVF para o servidor de configuração.

    ![Baixe o OVF](./media/migrate-scenarios-lift-and-shift/add-cs.png)

  > [!TIP]
  É possível baixar a versão mais recente do modelo de servidor de configuração diretamente do [Centro de Download da Microsoft](https://aka.ms/asrconfigurationserver).

#### <a name="import-the-template-in-vmware"></a>Importar o modelo para a VMware

1. Entre no servidor do VMware vCenter usando o cliente VMWare vSphere.
2. No menu **Arquivo**, selecione **Implantar o modelo de OVF** para iniciar o assistente de Implantação do modelo de OVF. 

     ![Modelo de OVF](./media/migrate-scenarios-lift-and-shift/vcenter-wizard.png)

3. Em **Selecionar origem**, insira o local do OVF baixado.
4. Em **Revisar detalhes**, selecione **Avançar**.
5. Em **Selecionar nome e pasta** e **Selecionar configuração**, aceite as configurações padrão.
6. Em **Selecionar armazenamento**, para obter o melhor desempenho, selecione **Thick Provision Eager Zeroed** em **Selecionar formato de disco virtual**.
7. No restante das páginas do assistente, aceite as configurações padrão.
8. Em **Pronto para concluir**:

    * Para configurar a VM com as configurações padrão, selecione **Ligar após a implantação** > **Concluir**.

    * Caso deseje incluir uma interface de rede adicional, desmarque **Ligar após a implantação**. Em seguida, selecione **Concluir**. Por padrão, o modelo de servidor de configuração é implantado com uma única NIC. É possível incluir NICs adicionais após a implantação.



#### <a name="register-the-configuration-server"></a>Registrar o servidor de configuração 

1. No console do cliente VMWare vSphere Client, ative a VM.
2. A VM será inicializada com uma experiência de instalação do Windows Server 2016. Aceite o contrato de licença e insira uma senha de administrador.
3. Após a conclusão da instalação, entre na VM como administrador.
4. Na primeira vez que entrar, a Ferramenta de Configuração do Azure Site Recovery é iniciada.
5. Insira um nome que seja usado para registrar o servidor de configuração com o Site Recovery. Em seguida, selecione **Avançar**.

    ![Modelo de OVF](./media/migrate-scenarios-lift-and-shift/config-server-register1.png)

6. A ferramenta verifica se a VM pode se conectar ao Azure. Depois que a conexão for estabelecida, selecione **Entrar** para fazer logon na sua assinatura do Azure. As credenciais devem ter acesso ao cofre no qual você deseja registrar o servidor de configuração.

    ![Modelo de OVF](./media/migrate-scenarios-lift-and-shift/config-server-register2.png)

7. A ferramenta executa algumas tarefas de configuração e, em seguida, é reinicializada.
8. Entre novamente no computador. O assistente de gerenciamento do servidor de configuração é iniciado automaticamente.

#### <a name="configure-settings-and-add-the-vmware-server"></a>Definir as configurações e adicionar o servidor do VMware

1. No assistente de gerenciamento do servidor de configuração, selecione **Configurar conectividade** e depois selecione a NIC que receberá o tráfego de replicação. Em seguida, selecione **Salvar**. Não é possível alterar essa configuração depois de ela ter sido definida.
2. Em **Selecionar cofre de Serviços de Recuperação**, selecione sua assinatura do Azure e o grupo de recursos e o cofre relevantes.

    ![cofre](./media/migrate-scenarios-lift-and-shift/cswiz1.png) 

3. Em **Instalar software de terceiros**, aceite o contrato de licença. Selecione **Baixar e Instalar** para instalar o MySQL Server.
4. Selecione **Instalar VMware PowerCLI**. Verifique se todas as janelas de navegador estão fechadas antes de fazer isso. Depois selecione **Continuar**.
5. Em **Validar configuração de dispositivo**, os pré-requisitos serão verificados antes de continuar.
6. Em **Configurar vCenter Server/servidor vSphere ESXi**, insira o FQDN ou endereço IP do vCenter Server ou o host vSphere, onde as VMs que deseja replicar estão localizadas. Insira a porta na qual o servidor está escutando. Insira um nome amigável a ser usado para o servidor VMware no cofre.
7. Insira as credenciais a serem usadas pelo servidor de configuração para se conectar ao servidor VMware. O Site Recovery usa essas credenciais para descobrir automaticamente as VMs do VMware que estão disponíveis para replicação. Selecione **Adicionar** e depois **Continuar**.

    ![vCenter](./media/migrate-scenarios-lift-and-shift/cswiz2.png)

8. Em **Configurar credenciais de máquina virtual**, insira o nome de usuário e a senha a serem usados para instalar automaticamente o Serviço de Mobilidade em computadores, quando a replicação está habilitada. Para computadores do Windows, a conta precisa de privilégios de administrador local nos computadores que você deseja replicar. 

    ![vCenter](./media/migrate-scenarios-lift-and-shift/cswiz2.png)

7. Selecione **Finalizar configuração** para concluir o registro. 
8. Após a conclusão do registro, no portal do Azure, verifique se o servidor de configuração e o VMware Server estão listados na página **Fonte** no cofre. Em seguida, selecione **OK** para definir as configurações de destino.
9. O Site Recovery conecta-se a servidores VMware usando as configurações especificadas e descobre VMs.

> [!NOTE]
> Pode levar 15 minutos ou mais para que o nome da conta apareça no portal. Para atualizar imediatamente, selecione **Servidores de Configuração** > ***nome do servidor*** > **Atualizar Servidor**.

### <a name="set-up-the-target-environment"></a>Configurar o ambiente de origem

Selecione e verifique os recursos de destino.

1. Em **Preparar infraestrutura** > **Destino**, selecione a assinatura do Azure que deseja usar.
2. Para o modelo de implantação de destino, selecione **Azure Resource Manager**.

3. A Recuperação de Site verifica se você tem uma ou mais contas de armazenamento e redes do Azure compatíveis.


### <a name="create-a-replication-policy"></a>Criar uma política de replicação

1. Em **Preparar infraestrutura** > **Configurações de Replicação** > **Política de Replicação**, clique em **Criar e associar**.
1. Em **Criar política de replicação**, insira o nome de política **VMwareRepPolicy**.
2. Em **Limite de RPO**, use o padrão de 60 minutos. Esse valor define a frequência em que são criados os pontos de recuperação. Um alerta será gerado se a replicação contínua exceder esse limite.
3. Em **Retenção de ponto de recuperação**, use o padrão de 24 horas para a duração do período de retenção de cada ponto de recuperação. Neste tutorial, use 72 horas. VMs replicadas podem ser recuperadas para qualquer ponto em uma janela.
4. Em **Frequência do instantâneo consistente com aplicativo**, use o padrão de 60 minutos para a frequência em que os instantâneos consistentes com o aplicativo são criados. Selecione **OK** para criar a política.

    ![Criar política de replicação](./media/migrate-scenarios-lift-and-shift/replication-policy.png)

5. A política é associada automaticamente ao servidor de configuração. 

    ![Associar política de replicação](./media/migrate-scenarios-lift-and-shift/replication-policy2.png)



### <a name="enable-replication"></a>Habilitar a replicação

Agora inicie a replicação das VMs. O Site Recovery instalará o Serviço de Mobilidade em cada VM quando você habilitar a replicação para ele. 


1. Selecione **Replicar aplicativo** > **Origem**.
2. Em **Origem**, selecione o servidor de configuração.
3. Em **Tipo de máquina**, selecione **Máquinas Virtuais**.
4. Em **Hipervisor do vCenter/vSphere**, selecione o servidor vCenter que gerencia o host vSphere ou selecione o host.
5. Selecione o servidor de processo (servidor de configuração). Depois, selecione **OK**.

    ![Habilitar a replicação](./media/migrate-scenarios-lift-and-shift/enable-replication1.png)

1. Em **Destino**, selecione a assinatura e o grupo de recursos em que deseja criar as VMs com failover. Escolha o modelo de implantação que você deseja usar no Azure (clássico ou Resource Manager) para as VMs do failover.
2. Selecione a conta de armazenamento do Azure que deseja usar para replicar dados.
3. Selecione a rede e a sub-rede do Azure às quais conectar as VMs do Azure quando elas forem criadas após o failover.
4. Selecione **Configurar agora para computadores selecionados** para aplicar a configuração de rede a todos os computadores selecionados para proteção. Selecione **Configurar mais tarde** para selecionar a rede do Azure por computador.
5. Selecione a sub-rede na rede virtual. Depois, selecione **OK**.

    ![Habilitar a replicação](./media/migrate-scenarios-lift-and-shift/enable-replication2.png)

6. Em **Máquinas Virtuais** > **Selecionar máquinas virtuais**, selecione a VM (WEBVM) desejada. Você só pode selecionar computadores para os quais a replicação pode ser habilitada. 

    ![Habilitar a replicação](./media/migrate-scenarios-lift-and-shift/enable-replication3.png)

7. Para monitorar as VMs adicionadas, verifique o horário da última descoberta de VMs em **Servidores de Configuração** > **Último Contato Às**. Para adicionar VMs sem esperar pela descoberta agendada, realce o servidor de configuração (não o selecione) e selecione **Atualizar**. Pode levar 15 minutos ou mais para que as alterações entrem em vigor e apareçam no portal.
8. Em **Propriedades** > **Configurar propriedades**, selecione a conta que será usada pelo servidor de processo para instalar automaticamente o Serviço de Mobilidade no computador. 

    ![Habilitar a replicação](./media/migrate-scenarios-lift-and-shift/enable-replication4.png)

9. Em **Configurações de replicação** > **Definir configurações de replicação**, verifique se a política de replicação correta está selecionada.
10. Selecione **Habilitar Replicação**.

    ![Habilitar a replicação](./media/migrate-scenarios-lift-and-shift/enable-replication5.png)

11. Acompanhe o progresso do trabalho **Habilitar proteção** em **Configurações** > **Trabalhos** > **Trabalhos do Site Recovery**. 
12. Após a execução do trabalho **Finalizar proteção**, o computador estará pronto para failover e será exibido em **Visão geral** > **Conceitos básicos**.

    ![Conceitos básicos](./media/migrate-scenarios-lift-and-shift/essentials.png)


## <a name="step-8-migrate-the-database-with-dms"></a>Etapa 8: Migrar o banco de dados com o DMS

Crie um projeto do DMS e execute a migração.


### <a name="create-a-database-migration-project"></a>Criar um projeto de Migração de Banco de Dados

1. Localize o recurso do DMS no portal do Azure e o abra.
2. Selecione **+ Novo Projeto de Migração**.
3. Em **Novo projeto de migração**, especifique um nome para o projeto.
4. Em **Tipo de servidor de origem**, selecione **SQL Server**. Em **Tipo de servidor de destino**, selecione **Instância Gerenciada do Banco de Dados SQL do Azure**.
5. Clique em **Criar** para criar o projeto.
6. Em **Detalhes da origem**, especifique os detalhes da conexão para o SQL Server de origem local. Clique em **Salvar**.
7. Em **Detalhes do destino**, especifique os detalhes da conexão do destino, que é a Instância Gerenciada do Banco de Dados SQL do Azure pré-provisionado para a qual o banco de dados local será migrado. Em seguida, clique em **Salvar**.
8. Em **Resumo do projeto**, examine e verifique os detalhes associados ao projeto de migração.

    ![Projeto do DMS](./media/migrate-scenarios-lift-and-shift/dms-project.png)

### <a name="migrate-the-database"></a>Migrar o banco de dados

1. Após criar o projeto, o Assistente de Migração será exibido.
2. Especifique as credenciais dos servidores de origem e de destino e clique em **Salvar**. O assistente tenta fazer logon em seu SQL Server local.

    ![Assistente do DMS](./media/migrate-scenarios-lift-and-shift/dms-wiz1.png)

3. Em **Mapear para os bancos de dados de destino**, selecione o banco de dados de origem que você deseja migrar. Em seguida, clique em **Salvar**.

    ![Assistente do DMS](./media/migrate-scenarios-lift-and-shift/dms-wiz2.png)

4. Em **Detalhes do destino**, selecione **Conheço os detalhes de destino**. Forneça o nome DNS de suas Instâncias Gerenciadas do SQL e as credenciais. Em seguida, clique em **Salvar**.

    ![Assistente do DMS](./media/migrate-scenarios-lift-and-shift/dms-wiz3.png)

5. No projeto salvo, selecione **+Nova atividade**. Em seguida, selecione **Executar migração**.
6. Quando solicitado, insira as credenciais dos servidores de origem e de destino. Em seguida, clique em **Salvar**.
7. Em **Mapear para os bancos de dados de destino**, selecione o banco de dados de origem que você deseja migrar. Em seguida, clique em **Salvar**
8. Em **Definir configurações da migração** > **Local de backup do servidor**, especifique o compartilhamento de rede criado no computador local. O DMS faz backups de origem para esse compartilhamento.  Lembre-se de que a conta de serviço que executa a instância do SQL Server de origem deve ter privilégios de gravação nesse compartilhamento.
9. Especifique o nome de usuário e a senha que o DMS pode representar para carregar os arquivos de backup para o contêiner de armazenamento do Azure, para restauração.
10. Especifique o URI de SAS que fornece o DMS com acesso ao seu contêiner da conta de armazenamento para o qual o serviço carrega os arquivos de backup e que é usado para migração para a Instância Gerenciada do Banco de Dados SQL do Azure.  Em seguida, clique em **Salvar**.
11. Em **Resumo de migração**, especifique um nome para a atividade de migração > **Executar a migração**.
12. No projeto > **Visão geral**, monitorar o status da migração. Após a conclusão da migração, verifique se os bancos de dados de destino existem na instância gerenciada.


## <a name="step-9-migrate-vms-with-site-recovery"></a>Etapa 9: Migrar VMs com o Site Recovery

Sugerimos que você execute um failover de teste para garantir que tudo está funcionando conforme o esperado, antes de executar uma migração completa. Quando você executar um failover de teste, acontecerá o seguinte:

1. Uma verificação de pré-requisitos será executada para conferir se todas as condições exigidas para a migração foram cumpridas.
2. O failover processa os dados para que uma VM do Azure possa ser criada. Se o ponto de recuperação mais recente for selecionado, um ponto de recuperação será criado nos dados.
3. Uma VM do Azure é criada usando os dados processados na etapa anterior.

### <a name="run-a-test-failover"></a>Execute um teste de failover

1. Em **Itens Protegidos**, clique em **Itens Replicados** > VM.
2. Nas propriedades da VM, clique em **+Failover de teste**.

    ![Failover de Teste](./media/migrate-scenarios-lift-and-shift/test-failover.png)

3. Selecione **Último processado** para fazer failover da VM para o ponto no tempo mais recente disponível. A carimbo de data/hora é mostrado. Com esta opção, não há tempo gasto no processamento de dados, portanto, um RTO (Objetivo de Tempo de Recuperação) baixo é fornecido.
2. Em **Failover de Teste**, selecione a rede de destino do Azure à qual as VMs do Azure serão conectadas após o failover.
3. Clique em **OK** para iniciar o failover. É possível acompanhar o andamento no cofre > **Configurações** > **Trabalhos** > **Failover de teste**.
4. Após a conclusão do failover, a réplica da VM do Azure aparece no portal do Azure > **Máquinas Virtuais**. Verifique se a VM é do tamanho apropriado, se está conectada à rede correta e se está em execução. Agora você certamente poderá se conectar à VM replicada no Azure.
5. Para excluir as VMs do Azure criadas durante o failover de teste, clique em **Limpar failover de teste** na VM. Em **Observações**, registre e salve todas as observações associadas ao failover de teste.

### <a name="migrate-the-machines"></a>Migrar os computadores

Após verificar se o failover de teste funciona conforme o esperado, crie um plano de recuperação para migrar para o Azure.

### <a name="create-a-recovery-plan"></a>Criar um plano de recuperação

1. No cofre, selecione **Planos de Recuperação (Site Recovery)** > **+Plano de Recuperação**.
2. Em **Criar plano de recuperação**, especifique um nome para o plano.
3. Selecione o servidor de configuração de origem e Azure como o destino. Selecione **Resource Manager** para o modelo de implantação. O local de origem deve ter computadores habilitados para failover e recuperação. 
4. Em **Selecionar itens**, adicione os computadores (WEBVM) ao plano. Em seguida, clique em **OK**.
5. Clique em **OK** para criar o plano.

    ![Plano de recuperação](./media/migrate-scenarios-lift-and-shift/recovery-plan1.png)

### <a name="run-a-failover"></a>Executar um failover

1. Em **Planos de Recuperação**, clique no plano > **Failover**.
2. Em **Failover** > **Ponto de Recuperação**, selecione o ponto de recuperação mais recente.
3. A configuração de chave de criptografia não é relevante para esse cenário.
4. Selecione **Desligar o computador antes do início do failover**. O Site Recovery tentará fazer um desligamento das máquinas virtuais de origem antes de disparar o failover. O failover continuará mesmo o desligamento falhar. Você pode acompanhar o progresso do failover na página **Trabalhos** .

    ![Failover](./media/migrate-scenarios-lift-and-shift/failover1.png)

5. Verifique se a VM do Azure aparece no Azure, conforme o esperado.

    ![Failover](./media/migrate-scenarios-lift-and-shift/failover2.png)

6. Em **Itens replicados**, clique com o botão direito do mouse em VM > **Concluir Migração**. Isso conclui o processo de migração, interrompe a replicação da VM e interrompe a cobrança do Site Recovery para a VM.

    ![Failover](./media/migrate-scenarios-lift-and-shift/failover3.png)

### <a name="update-the-connection-string"></a>Atualizar a cadeia de conexão

A etapa final do processo de migração é atualizar a cadeia de conexão do aplicativo para apontar para o banco de dados migrado em execução em sua MI do SQL.

1. Localize a nova cadeia de conexão no portal do Azure clicando em **Configurações** > **Cadeias de conexão**.

    ![Failover](./media/migrate-scenarios-lift-and-shift/failover4.png)  

2. É necessário atualizar essa cadeia de caracteres com o nome de usuário e a senha da sua MI do SQL.
3. Após a configuração da cadeia de caracteres, será necessário substituir a cadeia de conexão atual no arquivo web.config do seu aplicativo.
4. Após atualizar o arquivo e salvá-lo, reinicie o IIS no WEBVM. Isso pode ser feito usando o IISRESET/RESTART em um prompt de comando.
5. Após reiniciar o IIS, agora seu aplicativo usará o banco de dados em execução na MI do SQL.
6. Nesse ponto, o computador local do SQLVM pode ser desligado, e a migração é concluída.



## <a name="conclusion"></a>Conclusão

Neste cenário:

> [!div class="checklist"]
> * O banco de dados local foi migrado para uma Instância Gerenciada do Azure SQL com o DMS.
> * As VMs locais foram migradas para VMs do Azure com o serviço do Azure Site Recovery.
