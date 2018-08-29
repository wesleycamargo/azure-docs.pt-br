---
title: Hospede novamente um aplicativo Contoso com a migração para máquinas virtuais do Azure com o Azure Site Recovery | Microsoft Docs
description: Saiba como reutilizar um aplicativo local com uma migração de migração de máquinas locais para o Azure, usando o serviço Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: raynew
ms.openlocfilehash: 11859beb3d7bf0d0b0b801328c6570d274f1ea68
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/13/2018
ms.locfileid: "42139769"
---
# <a name="contoso-migration-rehost-an-on-premises-app-to-azure-vms"></a>Migração de Contoso: Hospede novamente um aplicativo local nas VMs do Azure


Este artigo demonstra como a Contoso hospeda novamente o aplicativo SmartHotel local no Azure, ao migrar as VMs do aplicativo para máquinas virtuais do Azure.


Este documento é um de uma série de artigos que mostra como a empresa fictícia Contoso migra os recursos locais para a nuvem do Microsoft Azure. A série inclui informações básicas e cenários que ilustram a configuração de uma infraestrutura de migração, avaliando recursos locais para migração e executando diferentes tipos de migrações. Os cenários crescem em complexidade e adicionaremos artigos adicionais ao longo do tempo.


**Artigo** | **Detalhes** | **Status**
--- | --- | ---
[Artigo 1: Visão geral](contoso-migration-overview.md) | Fornece uma visão geral de estratégia de migração da Contoso, a série de artigos e os aplicativos de exemplo que usamos. | Disponível
[Artigo 2: Implantar uma infraestrutura do Azure](contoso-migration-infrastructure.md) | Descreve como Contoso prepara seu local e a infraestrutura do Azure para a migração. A mesma infraestrutura é usada para todos os cenários de migração da Contoso. | Disponível
[Artigo 3: Avaliar recursos locais para migração para o Azure](contoso-migration-assessment.md)  | Mostra como a Contoso executa uma avaliação de um aplicativo SmartHotel de dois níveis no local em execução no VMware. A Contoso avalia as VMs de aplicativos com o serviço [ do Azure Migrate ](migrate-overview.md) e o banco de dados do SQL Server do aplicativo com o [ Assistente de Migração de Banco de Dados ](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Disponível
[Artigo 4: Hospedar novamente um aplicativo em VMs do Azure e uma Instância Gerenciada do SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Demonstra como a Contoso executa uma migração lift-and-shift para o Azure para o aplicativo SmartHotel local. A Contoso migra a VM frontend do aplicativo usando [ Azure Site Recovery ](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) e o banco de dados do aplicativo para uma Instância Gerenciada do SQL, usando o [ Serviço de Migração do Banco de Dados do Azure ](https://docs.microsoft.com/azure/dms/dms-overview). | Disponível
Artigo 5: Hospedar novamente um aplicativo em VMs do Azure | Mostra como a Contoso migra as VMs do aplicativo SmartHotel para VMs do Azure usando o serviço Site Recovery. | Este artigo.
[Artigo 6: Hospedar novamente um aplicativo em VMs do Azure e no Grupos de disponibilidade Always On do SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Mostra como a Contoso migra o aplicativo SmartHotel. A Contoso usa o Site Recovery para migrar as VMs do aplicativo e o serviço Database Migration para migrar o banco de dados do aplicativo para um cluster do SQL Server protegido por um grupo de disponibilidade AlwaysOn. | Disponível
[Artigo 7: Hospedar novamente um aplicativo do Linux nas VMs do Azure](contoso-migration-rehost-linux-vm.md) | Mostra como a Contoso faz uma migração de elevação e deslocamento do aplicativo osTicket do Linux para VMs do Azure, usando o Site Recovery | Disponível
[Artigo 8: Hospedar novamente um aplicativo Linux em VMs do Azure e no MySQL do Azure](contoso-migration-rehost-linux-vm-mysql.md) | Demonstra como a Contoso migra o aplicativo osTicket do Linux para VMs do Azure usando o Site Recovery e migra o banco de dados do aplicativo para uma instância do Azure MySQL Server usando o MySQL Workbench. | Disponível
[Artigo 9: Refatorar um aplicativo em aplicativos Web do Azure e no Banco de Dados SQL do Azure](contoso-migration-refactor-web-app-sql.md) | Demonstra como a Contoso migra o aplicativo SmartHotel para um aplicativo Web do Azure e migra o banco de dados do aplicativo para a instância do SQL Server do Azure | Disponível
[Artigo 10: Refatorar um aplicativo Linux em aplicativos Web do Azure e para o Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Mostra como a Contoso migra o aplicativo osTicket Linux para os aplicativos Web do Azure em vários sites, integrados com o GitHub para oferecer entrega contínua. Eles migram o banco de dados do aplicativo para uma instância do MySQL do Azure. | Disponível
[Artigo 11: Refatorar o TFS no VSTS](contoso-migration-tfs-vsts.md) | Mostra como a Contoso migra sua implantação do TFS (Team Foundation Server) local migrando-a para o VSTS (Visual Studio Team Services) no Azure. | Disponível
[Artigo 12: Recriar a arquitetura de um aplicativo nos contêineres do Azure e no Banco de Dados SQL do Azure](contoso-migration-rearchitect-container-sql.md) | Mostra como a Contoso migra e recria a arquitetura de seu aplicativo SmartHotel para o Azure. Eles recriam a arquitetura da camada da Web do aplicativo como um contêiner do Windows e do banco de dados do aplicativo em um Banco de Dados SQL do Azure. | Disponível
[Artigo 13: Recompilar um aplicativo no Azure](contoso-migration-rebuild.md) | Mostra como a Contoso recompila seu aplicativo SmartHotel usando uma variedade de funcionalidades e serviços do Azure, incluindo os Serviços de Aplicativos, o Kubernetes do Azure, o Azure Functions, os Serviços Cognitivos e o Cosmos DB. | Disponível



Neste artigo, a Contoso vai migrar do Windows de duas camadas. Aplicativo de NET SmartHotel em execução em máquinas virtuais VMware, para o Azure. Se você deseja usar esse aplicativo, ele é fornecido como o código-fonte aberto e você pode baixá-lo do [github](https://github.com/Microsoft/SmartHotel360).



## <a name="business-drivers"></a>Geradores de negócios

A equipe de liderança de TI trabalhou em conjunto com parceiros comerciais para entender o que eles desejam obter com essa migração:

- **Mais endereços comerciais**: A Contoso está crescendo e, como resultado, há pressão nos seus sistemas locais e da infraestrutura.
- **Limitar o risco**: o aplicativo SmartHotel é essencial para a empresa Contoso. Querem movê-lo para o Azure com zero risco.
- **Estender**: a Contoso não quer modificar o aplicativo. Eles simplesmente querem garantir que ele seja estável.


## <a name="migration-goals"></a>Metas de migração

A equipe de nuvem da Contoso fixou metas para esta migração. Essas metas são usadas para determinar o melhor método de migração:

- Após a migração, o aplicativo no Azure deve ter os mesmos recursos de desempenho de hoje no VMware.  O aplicativo permanecerá essencial tanto na nuvem quanto localmente. 
- A Contoso não quiser investir nesse aplicativo.  É importante para os negócios, mas em sua forma atual, eles simplesmente desejam movê-lo com segurança para a nuvem.
- A Contoso não quer alterar o modelo de operações para este aplicativo. Eles querem interagir com ele na nuvem da mesma maneira que fazem agora.
- A Contoso não quer alterar nenhuma funcionalidade do aplicativo. Somente o local do aplicativo será alterado.

## <a name="proposed-architecture"></a>Arquitetura proposta

Aqui está o ambiente atual

- O aplicativo está em camadas em duas VMs (**WEBVM** e **SQLVM**).
- As VMs estão localizadas no host VMware ESXi **contosohost1.contoso.com** (versão 6.5).
- O ambiente VMware é gerenciado pelo vCenter Server 6.5 (**vcenter.contoso.com**) em execução em uma VM.
- A Contoso tem um datacenter local (contoso-datacenter), com um controlador de domínio local (**contosodc1**).
- As VMs locais no datacenter da Contoso serão descomissionadas após a migração.

![Arquitetura de cenário](./media/contoso-migration-rehost-vm/architecture.png) 

## <a name="migration-process"></a>Processo de migração

A Contoso vai migrar o front-end do aplicativo e as VMs de banco para máquinas virtuais do Azure usando o Site Recovery:

- Como uma primeira etapa, eles irão preparar e configurar componentes para o Site Recovery e preparar a infra-estrutura de VMware local.
- Eles já têm sua [infraestrutura do Azure](contoso-migration-infrastructure.md) pronta, portanto, precisam apenas adicionar alguns dos componentes Azure especificamente para o Site Recovery.
- Com tudo preparado, eles poderão começar a replicar as máquinas virtuais.
-Após a replicação ser habilitada e estar funcionando, eles migram a VM fazendo o failover para o Azure.

![Processo de migração](./media/contoso-migration-rehost-vm/migraton-process.png) 



### <a name="azure-services"></a>Serviços do Azure

**Serviço** | **Descrição** | **Custo**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | O serviço coordena e gerencia a migração e a recuperação de desastre para VMs do Azure e servidores físicos e de VMs locais.  | Durante a replicação para o Azure, são gerados encargos do Armazenamento do Azure.  As VMs do Azure são criadas e incorrem em encargos quando ocorre failover. [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre encargos e preços.


## <a name="prerequisites"></a>Pré-requisitos

Aqui está o que você (e a Contoso) precisam para executar este cenário.

**Requisitos** | **Detalhes**
--- | ---
**Assinatura do Azure** | Você já deve ter criado uma assinatura durante os artigos anteriores desta série. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Se você criar uma conta gratuita, será o administrador da assinatura e poderá executar todas as ações.<br/><br/> Se você usar uma assinatura existente e não for o administrador, será necessário trabalhar com o administrador para receber permissões de Proprietário ou de Colaborador.<br/><br/> Se forem necessárias permissões mais granulares, leia [este artigo](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Infraestrutura do Azure** | [ Saiba como ](contoso-migration-infrastructure.md) a Contoso configurou uma infraestrutura do Azure.<br/><br/> Saiba mais sobre os requisitos de [rede](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) e [armazenamento](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) específicos para o Site Recovery.
**Servidores locais** | vCenter Servers locais devem estar executando a versão 5.5, 6.0 ou 6.5<br/><br/> Hosts ESXi devem executar a versão 5.5, 6.0 ou 6.5<br/><br/> Uma ou mais VMs do VMware devem estar em execução no host ESXi.
**VMs locais** | As VMs devem atender aos [requisitos do Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>Etapas do cenário

Aqui está como a Contoso executará a migração:

> [!div class="checklist"]
> * **Etapa 1: Preparar o Azure para o Site Recovery**: Eles criam uma conta de armazenamento do Azure para armazenar os dados replicados, e um cofre dos Serviços de Recuperação.
> * **Etapa 2: Preparar VMware local para o Site Recovery**: eles preparam contas para a instalação de descoberta e agente VM e se preparam para se conectar a máquinas virtuais do Azure após o failover.
> * **Etapa 3: Replicar VMs**: Eles configuram a replicação e iniciam a replicação das máquinas virtuais para o armazenamento do Azure.
> * **Etapa 4: Migrar as máquinas virtuais com o Site Recovery**: Eles executam um failover de teste para verificar se tudo está funcionando e, em seguida, executam um failover completo para migrar as máquinas virtuais para o Azure.




## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>Etapa 1: Preparar o Azure para o serviço do Site Recovery

Aqui estão os componentes do Azure que a Contoso precisa para migrar as máquinas virtuais para o Azure:

- Uma rede virtual na qual as VMs do Azure serão localizadas quando forem criadas durante o failover.
- Uma conta de armazenamento do Azure para armazenar os dados replicados. 
- Um cofre do Recovery Services no Azure.

Eles configuram isso tudo da seguinte maneira:

1. A Contoso já configurou uma rede que eles podem usar para o Site Recovery quando [implantaram a infraestrutura do Azure](contoso-migration-infrastructure.md)

    - O aplicativo SmartHotel é um aplicativo de produção e as VMs serão migradas para a rede de produção do Azure (VNET-PROD-EUS2) na região Leste dos EUA 2 primária.
    - As duas máquinas virtuais serão colocadas no grupo de recursos ContosoRG, que é usado para recursos de produção.
    - A VM do front-end do aplicativo (WEBVM) será migrada para a sub-rede do front-end (PROD-FE-EUS2), na rede de produção.
    - A VM do banco de dados do aplicativo (SQLVM) será migrada para a sub-rede do banco de dados (PROD-DB-EUS2), na rede de produção.

2. A Contoso cria uma conta de armazenamento do Azure (contosovmsacc20180528) na região primária.
    - A conta de armazenamento deve estar na mesma região do cofre de Serviços de Recuperação.
    - Eles usam uma conta de propósito geral, com armazenamento padrão e replicação de LRS. 

    ![Armazenamento do Site Recovery](./media/contoso-migration-rehost-vm/asr-storage.png)

3. Com a conta de armazenamento e a rede prontos, a Contoso agora cria um cofre de Serviços de Recuperação (ContosoMigrationVault) e coloca-o no grupo de recursos ContosoFailoverRG na região Leste dos EUA 2 primária.

    ![Cofre dos Serviços de Recuperação](./media/contoso-migration-rehost-vm/asr-vault.png)

**Precisa de mais ajuda?**

[Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) como configurar o Azure para o Site Recovery.


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>Etapa 2: Preparar VMware locais para o Site Recovery

Aqui está o que a Contoso prepara localmente:

- Uma conta no vCenter Server ou no host ESXi do vSphere para automatizar a descoberta de VM.
- Uma conta que permite a instalação automática do serviço de Mobilidade nas VMs VMware. 
- Configurações de VM locais, para que a Contoso possa se conectar a máquinas virtuais do Azure replicadas após o failover.


### <a name="prepare-an-account-for-automatic-discovery"></a>Preparar uma conta para a descoberta automática

O Site Recovery precisa de acesso aos servidores VMware para:

- Descobrir VMs automaticamente. 
- Orquestrar a replicação, o failover e o failback para VMs.
- Pelo menos uma conta somente leitura é necessária. A conta deve ser capaz de executar operações como criação e remoção de discos e ativação de VMs.

A Contoso configura a conta da seguinte maneira:

1. Eles criam uma função no nível do vCenter.
2. Eles atribuir as permissões necessárias a essa função.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparar uma conta para a instalação do serviço de Mobilidade

O serviço de Mobilidade deve ser instalado em cada VM.

- O Site Recovery pode fazer uma instalação automática por push do serviço de Mobilidade quando a replicação da VM for habilitada.
- Uma conta é necessária, para que o Site Recovery possa acessar as máquinas virtuais para a instalação por push. Especifique essa conta ao configurar a replicação.
- A conta pode ser de domínio ou local, com permissões para instalação nas VMs.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar para conectar VMs do Azure após o failover

Após o failover, a Contoso deseja se conectar a máquinas virtuais do Azure. Para fazer isso, eles devem fazer o seguinte antes da migração:

1. Para acesso pela internet eles:

 - Habilite o RDP na VM local antes do failover
 - Assegure-se de que as regras TCP e UDP sejam incluídas para o perfil **Público**.
 - Verifique se o RDP é permitido no **Firewall do Windows**  >  **Aplicativos permitidos** para todos os perfis.
 
2. Para acesso por VPN site-to-site, eles:

 - Ative o RDP na máquina no local.
 - Permitir o RDP no **Firewall do Windows**  ->  **Aplicativos e recursos permitidos**, para **Domínio e redes particulares**.
 - Defina a política de SAN do sistema operacional na VM local para **OnlineAll**.

Além disso, quando eles executam um failover, precisam verificar o seguinte:

- Não deve haver atualizações do Windows pendentes na VM ao acionar um failover. Se houver, eles não poderão efetuar login na VM até que a atualização seja concluída.
- Após o failover, eles podem verificar **Diagnósticos de inicialização** para exibir uma captura de tela da VM. Se isso não funcionar, eles devem verificar se a VM está em execução e revisar essas [ dicas de solução de problemas ](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


**Precisa de mais ajuda?**

- [Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) como criar e atribuir uma função para a descoberta automática.
- [Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) como criar uma conta para a instalação do serviço de mobilidade por push.


## <a name="step-3-replicate-the-on-premises-vms"></a>Etapa 3: Replicar VMs locais

Antes de poderem executar uma migração para o Azure, a Contoso deve configurar e habilitar a replicação.

### <a name="set-a-replication-goal"></a>Definir uma meta de replicação

1. No cofre, sob o nome do vault (ContosoVMVault), eles selecionam uma meta de replicação (**Introdução**  >  **Recuperação do site**  >  **Preparar a infraestrutura**.
2. Eles especificam que suas máquinas estão localizadas no local, sendo executadas no VMware e replicadas no Azure.

    ![Meta de replicação](./media/contoso-migration-rehost-vm/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Confirmar planejamento de implantação

Para continuar, eles confirmam que concluíram o planejamento da implantação, selecionando **Sim, eu fiz isso**. Nesse cenário, a Contoso só está migrando duas máquinas virtuais e não é necessário um planejamento de implantação.


### <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

A Contoso precisa configurar seu ambiente de origem. Para fazer isso, eles baixam um modelo OVF e o utilizam para implantar o servidor de configuração do Site Recovery como uma VM VMware local, altamente disponível. Depois que a configuração do servidor tiver sido finalizada, registre-o no cofre.

O servidor de configuração executa vários componentes:

- O componente de servidor de configuração que coordena a comunicação entre o ambiente local e o Azure e gerencia a replicação de dados.
- Servidor em processo que age como um gateway de replicação. Ele recebe dados de replicação, otimiza-os com caching, compactação e criptografia e os envia para o Armazenamento do Azure.
- O servidor de processo também instala o Serviço de Mobilidade nas VMs que você deseja replicar e executa a descoberta automática de VMs VMware locais.



A Contoso executa essas etapas da seguinte maneira:

1. No cofre, eles baixam o modelo OVF em **Preparar a infraestrutura** > **Fonte** > **Servidor de configuração**.
    
    ![Baixe o OVF](./media/contoso-migration-rehost-vm/add-cs.png)

2. Eles importam o modelo para o VMware para criar e implantar a VM.

    ![Modelo de OVF](./media/contoso-migration-rehost-vm/vcenter-wizard.png)

3.  Ao ligar a máquina virtual pela primeira vez, ela é inicializada como uma experiência de instalação do Windows Server 2016. Eles aceitam o contrato de licença e inserem uma senha de administrador.
4. Após a conclusão da instalação, entram na VM como administrador. No primeiro logon, a Ferramenta de Configuração do Azure Site Recovery é iniciada por padrão.
5. Na ferramenta, eles especificam um nome para registrar o servidor de configuração no cofre.
6. A ferramenta verifica se a VM pode se conectar ao Azure. Depois que a conexão é estabelecida, entram na assinatura do Azure. As credenciais devem ter acesso ao cofre no qual você deseja registrar o servidor de configuração.

    ![Registrar servidor de configuração](./media/contoso-migration-rehost-vm/config-server-register2.png)

7. A ferramenta executa algumas tarefas de configuração e, em seguida, é reinicializada.
8. Entram na máquina novamente e o Assistente de Gerenciamento do Servidor de Configuração é iniciado automaticamente.
9. No assistente, selecionam o NIC que vai receber o tráfego de replicação. Essa configuração não pode ser alterada depois de definida.
10. Eles selecionam a assinatura, o grupo de recursos e o cofre no qual será registrado o servidor de configuração.
        ![vault](./media/contoso-migration-rehost-vm/cswiz1.png) 

10. Eles baixam e instalam o MySQL Server e VMWare PowerCLI. 
11. Após a validação, ela especifica o FQDN ou endereço IP do host do servidor vSphere ou vCenter. Eles não alteram a porta padrão e especificam um nome amigável para o servidor no Azure.
12. Eles especificam a conta que criaram para descoberta automática e as credenciais que são usadas para instalar automaticamente o Serviço de Mobilidade. Para computadores do Windows, a conta precisa de privilégios de administrador local nas VMs.

    ![vCenter](./media/contoso-migration-rehost-vm/cswiz2.png)

7. Após a conclusão do registro, no portal do Azure, A Contoso verifica novamente se o servidor de configuração e o VMware Server estão listados na página **Fonte** no cofre. Descoberta pode levar 15 minutos ou mais. 
8. O Site Recovery então conecta-se aos servidores VMware usando as configurações especificadas e descobre VMs.

### <a name="set-up-the-target"></a>Configurar o destino

Agora, a Contoso especifica as configurações de replicação de destino.

1. Em **Preparar infraestrutura** > **Destino**, selecionam as configurações de destino.
2. O Site Recovery verifica se há uma conta de armazenamento do Azure e rede no local de destino especificado.

### <a name="create-a-replication-policy"></a>Criar uma política de replicação

Agora a Contoso pode criar uma política de replicação.

1. Em **Preparar infraestrutura** > **Configurações de Replicação** > **Política de Replicação** >  **Criar e Associar**, criam uma política **ContosoMigrationPolicy**.
2. Usam as configurações padrão:
    - **Limite de RPO**: padrão de 60 minutos. Esse valor define a frequência em que são criados os pontos de recuperação. Um alerta será gerado se a replicação contínua exceder esse limite.
    - **Retenção do ponto de recuperação**. Padrão de 24 horas. Esse valor especifica qual é o tempo da janela de retenção para cada ponto de recuperação. VMs replicadas podem ser recuperadas para qualquer ponto em uma janela.
    - **Frequência de instantâneos consistentes com o aplicativo**. O padrão é de uma hora. Esse valor especifica a frequência com que são criados instantâneos consistentes com o aplicativo.

        ![Criar política de replicação](./media/contoso-migration-rehost-vm/replication-policy.png)

5. A política é associada automaticamente ao servidor de configuração. 

    ![Associar política de replicação](./media/contoso-migration-rehost-vm/replication-policy2.png)

### <a name="enable-replication-for-webvm"></a>Habilitar a replicação para WEBVM

Com tudo já preparado, a Contoso pode habilitar a replicação para as VMs. Eles começam com a WebVM.

1. Em **Replicar aplicativo** > **Fonte** > **+Replicar**, ela seleciona as configurações de fonte.
2. Eles indicam que desejam ativar as VMs, selecionar o servidor vCenter e o servidor de configuração.

    ![Habilitar a replicação](./media/contoso-migration-rehost-vm/enable-replication1.png)

3. Eles selecionam as configurações de destino, incluindo a conta de armazenamento, a rede do Azure e o grupo de recursos.

     ![Habilitar a replicação](./media/contoso-migration-rehost-vm/enable-replication2.png)

4. A Contoso seleciona **WebVM** para replicação, verifica a política de replicação e habilita a replicação.

    - Neste estágio a Contoso seleciona apenas WEBVM porque a rede virtual e a sub-rede devem ser selecionadas e a Contoso está colocando as VMs do aplicativo em sub-redes diferentes.
    - O Site Recovery instala automaticamente o serviço de Mobilidade na VM quando a replicação é habilitada.

    ![Habilitar a replicação](./media/contoso-migration-rehost-vm/enable-replication3.png)

5. Eles rastreiam o progresso da replicação em **Trabalhos**. Após o trabalho de **Finalizar Proteção** ser executado, o computador estará pronto para failover.
6. Em **Essentials** no portal do Azure, a Contoso pode ver a estrutura para as VMs sendo replicadas para o Azure.


### <a name="enable-replication-for-sqlvm"></a>Habilitar a replicação para SQLVM

Agora a Contoso pode começar a replicar a máquina SQLVM, usando o mesmo processo apresentado acima.

1. Eles selecionam as configurações da fonte.

    ![Habilitar a replicação](./media/contoso-migration-rehost-vm/enable-replication1.png)

2. Eles, em seguida, especificam as configurações de destino.

     ![Habilitar a replicação](./media/contoso-migration-rehost-vm/enable-replication2-sqlvm.png)

3. Eles selecionam SQLVM para replicação. 

    ![Habilitar a replicação](./media/contoso-migration-rehost-vm/enable-replication3-sqlvm.png)

4. Eles aplicam a mesma política de replicação que foi usada para WEBVM e habilitam a replicação.

    ![Modo de exibição de infraestrutura](./media/contoso-migration-rehost-vm/essentials.png)

**Precisa de mais ajuda?**

- Você pode ler uma explicação completa de todas essas etapas em [Configurar a recuperação de desastres para máquinas virtuais do VMware locais](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- As instruções detalhadas estão disponíveis para ajudá-lo a [configurar o ambiente da fonte](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [implantar o servidor de configuração](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), e [definir configurações de replicação](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- Você pode saber mais sobre a [ habilitação da replicação](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-4-migrate-the-vms"></a>Etapa 4: Migrar as VMs 

A Contoso executa um failover de teste rápido e, em seguida, um failover completo para migrar as máquinas virtuais.

### <a name="run-a-test-failover"></a>Execute um teste de failover

Um failover de teste ajuda a garantir que tudo está funcionando como esperado. 

1. A Contoso executa um failover de teste para o ponto no tempo mais recente disponível (**Último processado**).
2. Selecionam **Desligar o computador antes do início do failover**, para que o Site Recovery tente desligar a VM da fonte antes de disparar o failover. O failover continuará mesmo o desligamento falhar. 
3. O failover de teste é executado: 

    - Uma verificação de pré-requisitos será executada para conferir se todas as condições exigidas para a migração foram cumpridas.
    - O failover processa os dados para que uma VM do Azure possa ser criada. Se o ponto de recuperação mais recente for selecionado, um ponto de recuperação será criado nos dados.
    - Uma VM do Azure é criada usando os dados processados na etapa anterior.
    
3. Após o failover ser concluído, a réplica da VM do Azure aparecerá no portal do Azure. A Contoso verifica se a VM é do tamanho apropriado, se está conectada à rede correta e se está em execução. 
4. Depois de verificar o failover de teste, eles limpam o failover e registram e salvam todas as observações. 

### <a name="create-and-customize-a-recovery-plan"></a>Criar e personalizar um plano de recuperação

 Depois de verificar se o failover de teste funcionou como esperado, a Contoso cria um plano de recuperação para a migração. 

- Um plano de recuperação especifica a ordem na qual o failover ocorre e indica como as VMs do Azure ficarão online no Azure.
- Como o aplicativo é de duas camadas, eles personalizam o plano de recuperação para que os dados de VM (SQLVM) sejam iniciados antes do front-end (WEBVM).

1. Em **planos de recuperação (recuperação de Site)** > **+ plano de recuperação**, criam um plano e adicione as máquinas virtuais a ele.

    ![Plano de recuperação](./media/contoso-migration-rehost-vm/recovery-plan.png)

2. Depois de criar o plano, eles o personalizam (**Planos de recuperação** > **SmartHotelMigrationPlan** > **Personalizar**).
2.  Eles removem a WEBVM do **Grupo 1: Iniciar**.  Isso garante que a primeira ação de início afete somente a SQLVM.
3.  Em **+Grupo** > **Adicionar itens protegidos**, eles aumentam a WEBVM para Grupo 2: Iniciar.  As VMs precisam estar em dois grupos diferentes.


### <a name="migrate-the-vms"></a>Migrar as VMs


Agora a Contoso pode executar um failover completo para concluir a migração.

1. Eles selecionam o plano de recuperação > **Failover**.
2. Eles selecionam fazer o failover para o último ponto de recuperação e que o Site Recovery deve tentar desligar a VM local antes de acionar o failover. Podem acompanhar o progresso do failover na página **Trabalhos**.

    ![Failover](./media/contoso-migration-rehost-vm/failover1.png)


3. Após o failover, eles verificam se a VM do Azure aparece conforme o esperado no portal do Azure.

    ![Failover](./media/contoso-migration-rehost-vm/failover2.png)  

3. Após a verificação, eles concluem a migração para cada VM. Isso interrompe a replicação da VM e interrompe a cobrança do Site Recovery para a VM.

    ![Failover](./media/contoso-migration-rehost-vm/failover3.png)

**Precisa de mais ajuda?**

- [Saiba mais](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) sobre como executar failovers de teste. 
- [Saiba](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) como criar um plano de recuperação.
- [Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) como fazer failover para o Azure.

## <a name="clean-up-after-migration"></a>Limpar após a migração

Com a migração concluída, as camadas do aplicativo SmartHotel agora estão em execução em VMs do Azure.

Agora, a Contoso precisa concluir estas etapas de limpeza:  

- Remover a máquina WEBVM do inventário do vCenter.
- Remova a máquina do SQLVM do inventário do vCenter.
- Remover WEBVM e SQLVM dos trabalhos de backup locais.
- Atualizar a documentação interna para mostrar o novo local e endereços IP para as máquinas virtuais.
- Examinar todos os recursos que interagem com as máquinas virtuais e atualizar configurações relevantes ou documentação para refletir a nova configuração.

## <a name="review-the-deployment"></a>Revisar a implantação

Com o aplicativo em execução, a Contoso agora precisa utilizá-lo totalmente e protegê-lo no Azure.

### <a name="security"></a>Segurança

A equipe de segurança da Contoso revisa as VMs do Azure, para determinar os problemas de segurança.

- Para controlar o acesso, eles revisam os Grupos de Segurança de Rede (NSGs) para as VMs. Os NSGs são usados para garantir que somente o tráfego permitido para o aplicativo pode acessá-lo.
- Eles também consideram proteger os dados no disco usando o Azure Disk Encryption e KeyVault.

[Leia mais](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) sobre as práticas recomendadas de segurança para máquinas virtuais.

### <a name="backups"></a>Backups

A Contoso vai fazer backup dos dados nas máquinas virtuais usando o serviço de Backup do Azure. [Saiba mais](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="licensing-and-cost-optimization"></a>Licenciamento e otimização de custo

1. A Contoso possui licenças existentes para suas VMs e aproveitará o Benefício Híbrido do Azure.  Eles converterão as VMs do Azure existentes, para aproveitar os preços.
2. A Contoso habilitará o Gerenciamento de Custos do Azure licenciado pela Cloudyn, uma subsidiária da Microsoft. Essa é uma solução de gerenciamento de custos de várias nuvens que ajuda a usar e gerenciar o Azure e outros recursos de nuvem. [Saiba mais](https://docs.microsoft.com/azure/cost-management/overview) sobre o Gerenciamento de Custos do Azure. 

## <a name="conclusion"></a>Conclusão

Neste artigo, a Contoso hospedou novamente o aplicativo SmartHotel no Azure ao migrar as VMs do aplicativo para VMs do Azure usando o serviço de Site Recovery. 


## <a name="next-steps"></a>Próximas etapas

No [próximo artigo](contoso-migration-rehost-vm-sql-ag.md) na série, mostraremos como a Contoso hospeda novamente a VM de front-end do aplicativo SmartHotel na VM do Azure e migra o banco de dados para um grupo de disponibilidade AlwaysOn do SQL no Azure.

