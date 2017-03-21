---
title: "Migração para o Armazenamento Premium do Azure usando o Azure Site Recovery | Microsoft Docs"
description: "Migre máquinas virtuais existentes para o Armazenamento Premium do Azure usando o Site Recovery. O Armazenamento Premium dá suporte ao disco de alto desempenho e baixa latência para cargas de trabalho que usam muita E/S em execução em máquinas virtuais do Azure."
services: storage
cloud: Azure
documentationcenter: na
author: luywang
manager: kavithag
ms.assetid: 
ms.service: <service per approved list>
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/1/2016
ms.author: luywang
translationtype: Human Translation
ms.sourcegitcommit: 106e03a5a99134eb6e5744cbf29ba32efc31f0ba
ms.openlocfilehash: d76aa3e62c691c4537684bc70d3a91a3dbb8b446
ms.lasthandoff: 03/02/2017


---
# <a name="migrating-to-premium-storage-using-azure-site-recovery"></a>Migração para o Armazenamento Premium do Azure usando o Azure Site Recovery

O [Armazenamento Premium do Azure](storage-premium-storage.md) dá suporte de disco de alto desempenho e baixa latência para máquinas virtuais (VMs) que estão executando cargas de trabalho intensivas para entradas e saídas. A finalidade deste guia é ajudar os usuários a migrar os discos de VM de uma conta de armazenamento padrão para uma conta de armazenamento Premium usando o [Azure Site Recovery](../site-recovery/site-recovery-overview.md).

O Site Recovery é um serviço do Azure que colabora com sua estratégia de continuidade dos negócios e recuperação de desastre por meio da coordenação da replicação de servidores físicos e VMs na nuvem (Azure) ou em um datacenter secundário. Quando ocorrem paralisações em seu local primário, você realiza o failover em um local secundário a fim de manter aplicativos e cargas de trabalho disponíveis. Quando o local primário retoma as operações normais, você realiza o failback. O Site Recovery fornece failovers de teste para dar suporte à simulações de recuperação de desastre sem afetar os ambientes de produção. É possível executar failovers com perda mínima de dados (dependendo da frequência de replicação) para desastres inesperados. No cenário de migração para o Armazenamento Premium, é possível usar um [Failover no Site Recovery](../site-recovery/site-recovery-failover.md) no Azure Site Recovery para migrar os discos de destino para uma conta de armazenamento Premium.

É recomendável migrar para o armazenamento Premium usando o Site Recovery, porque essa opção fornece tempo de inatividade mínimo e evita a execução manual de cópias de discos e criação de novas VMs. O Site Recovery vai copiar os discos sistematicamente e criar novas VMs durante o failover. O Site Recovery oferece suporte a vários tipos de failover com pouco ou nenhum tempo de inatividade. Para planejar o tempo de inatividade e estimar a perda de dados, consulte a tabela de [Tipos de failover](../site-recovery/site-recovery-failover.md) no Site Recovery. Se estiver [preparado para se conectar a VMs do Azure após o failover](../site-recovery/site-recovery-vmware-to-azure.md#prepare-vms-for-replication), você conseguirá se conectar à VM do Azure usando RDP após o failover.

![][1]

## <a name="migration-scenario-components"></a>Componentes do cenário de migração

**Componentes relevantes do Site Recovery neste cenário de migração:**

* Um **servidor de configuração** é uma VM do Azure que coordena a comunicação e gerencia os processos de recuperação e replicação de dados. Nesta VM, você executará um arquivo único de instalação para instalar o servidor de configuração e um componente adicional, chamado de servidor de processo, como gateway de replicação. Leia sobre os [pré-requisitos do servidor de configuração](../site-recovery/site-recovery-vmware-to-azure.md#prerequisites). O servidor de configuração só precisa ser configurado uma vez e pode ser usado em todas as migrações para a mesma região.

* O **servidor de processo** é um gateway de replicação que recebe os dados de replicação das VMs de origem, otimiza os dados com caching, compactação e criptografia e os envia para uma conta de armazenamento. Ele também manipula a instalação por push do serviço de mobilidade nas VMs de origem e executa a descoberta automática destas. O servidor de processo padrão é instalado no servidor de configuração. Você pode implantar servidores de processo autônomo adicionais para dimensionar sua implantação. Leia sobre [práticas recomendadas para implantação de servidor de processo](https://azure.microsoft.com/en-us/blog/best-practices-for-process-server-deployment-when-protecting-vmware-and-physical-workloads-with-azure-site-recovery/) e [implantação de servidores de processo adicionais](../site-recovery/site-recovery-plan-capacity-vmware.md#deploy-additional-process-servers). O servidor de processo só precisa ser configurado uma vez e pode ser usado em todas as migrações para a mesma região.

* O **serviço de mobilidade** é um componente que é implantado em todas as VMs padrão que você deseja replicar. Ele captura gravações de dados na VM padrão e as encaminha ao servidor de processo. Leia sobre [Pré-requisitos de computadores replicados](../site-recovery/site-recovery-vmware-to-azure.md#prerequisites).

**Conceitos básicos do Azure**: 

* Uma assinatura do Azure
* Uma conta de armazenamento Premium do Azure para armazenar os dados replicados
* Uma rede virtual do Azure (VNet) à qual as VMs serão conectadas quando forem criadas no failover. A VNet do Azure precisa estar na mesma região daquela que o Site Recovery é executado
* Uma conta de armazenamento padrão do Azure para armazenar logs de replicação. Pode ser a mesma conta de armazenamento para a qual os discos de VM estão sendo migrados

O gráfico mostra como esses componentes interagem.

![][15]

> [!NOTE]
> O Site Recovery não dá suporte à migração de discos dos espaços de armazenamento.

Para componentes adicionais para outros cenários, consulte [Arquitetura de cenário](../site-recovery/site-recovery-vmware-to-azure.md).

## <a name="prerequisites"></a>Pré-requisitos

* Compreender os componentes relevantes do cenário de migração da seção anterior
* Planejar o tempo de inatividade sabendo mais sobre o [Failover no Site Recovery](../site-recovery/site-recovery-failover.md)

## <a name="setup-and-migration-steps"></a>Etapas de configuração e migração

Você pode usar o Site Recovery para migrar VMs IaaS do Azure entre regiões ou na mesma região. As instruções a seguir foram adaptadas para este cenário de migração com base no artigo [Replicar VMs VMware ou servidores físicos no Azure](../site-recovery/site-recovery-vmware-to-azure.md). Siga os links para obter as etapas detalhadas que complementam as instruções deste artigo.

1. **Crie um cofre de Serviços de Recuperação**. Crie e gerencie o cofre do Site Recovery por meio do [portal do Azure](https://portal.azure.com). Clique em **Novo** > **Backup** > **de Gerenciamento** e **Site Recovery (OMS)**. Como alternativa, você pode clicar em **Procurar** > **Cofres dos Serviços de Recuperação** > **Adicionar**. As VMs serão replicadas para a região que você especificar nesta etapa. Para fins de migração na mesma região, selecione a região em que estão as VMs e as contas de armazenamento de origem. Observe que a migração de contas de armazenamento Premium tem suporte no [portal do Azure](https://portal.azure.com), mas não no [portal clássico](https://manage.windowsazure.com).

2. **Escolha as metas de proteção**. Na VM em que você deseja instalar o servidor de configuração, abra o [portal do Azure](https://portal.azure.com). Vá para **Configurações dos** > **cofres dos Serviços de Recuperação**. Em **Configurações**, selecione **Site Recovery**. Em **Site Recovery**, selecione **Etapa 1: Preparar infraestrutura**. Em **Preparar infraestrutura**, selecione **Objetivo de proteção**.
  
  ![][2]
  
  Em **Objetivo de proteção**, na primeira lista suspensa, selecione **Para o Azure**. Na segunda lista suspensa, selecione **Não virtualizados / outros**e clique em **OK**.
  
  ![][3]
  
3. **Configure o ambiente de origem (servidor de configuração)**. Baixe a **Configuração unificada do Azure Site Recovery** e a **chave de registro do cofre** indo para a folha **Preparar infraestrutura** > **Preparar fonte** > **Adicionar servidor**. Você vai precisar da chave de registro do cofre para executar a instalação unificada. A chave é válida por 5 dias após ser gerada.
  
  ![][4]
  
  ![][5]
  
  Na VM que você estiver usando como servidor de configuração, execute a Configuração Unificada para instalar o servidor de configuração e o servidor de processo. Você pode percorrer as capturas de tela [aqui](../site-recovery/site-recovery-vmware-to-azure.md#set-up-the-source-environment) para concluir a instalação. Você pode consultar as capturas de tela a seguir para ver as etapas especificadas para este cenário de migração.

  Em **Antes de começar**, selecione **Instalar o servidor de configuração e o servidor em processo**.
  
  ![][6]

  Em **Registro**, procure e selecione a chave de registro que você baixou do cofre.
  
  ![][7]

  Em **Detalhes do Ambiente**, selecione se você replicará as VMs VMware. Para esse cenário de migração, escolha **Não**.
  
  ![][8]
  
  Quando a instalação estiver concluída, você verá a janela do **Servidor de configuração do Microsoft Azure Site Recovery**. Use a guia **Gerenciar contas** para criar a conta que o Site Recovery pode usar para descoberta automática. (No cenário sobre proteção de computadores físicos, configurar a conta não é relevante, mas você precisa de pelo menos uma conta para habilitar uma das etapas a seguir. Nesse caso, você pode nomear a conta e a senha como qualquer.) Use a guia **Registro do cofre** para carregar o arquivo de credencial de cofre.
  
  ![][9]

4. **Configure o ambiente de origem**. Clique em **Preparar infraestrutura** > **Destino**e especifique o modelo de implantação que você deseja usar para as VMs após o failover. Você pode escolher **Clássico** ou **Gerenciador de recursos**, dependendo do cenário.
  
  ![][10]

  A Recuperação de Site verifica se você tem uma ou mais contas de armazenamento e redes do Azure compatíveis. Se estiver usando uma conta de armazenamento premium para os dados replicados, você precisará configurar uma conta de armazenamento padrão adicional para armazenar os logs de replicação.

5. **Defina as configurações de replicação**. Siga as etapas [aqui](../site-recovery/site-recovery-vmware-to-azure.md#set-up-replication-settings) para verificar se o servidor de configuração está corretamente associado à política de replicação que você criou.

6. **Planejamento de capacidade**. Use o [planejador de capacidade](../site-recovery/site-recovery-capacity-planner.md) para estimar com precisão a largura de banda de rede, o armazenamento e outros requisitos para atender às suas necessidades de replicação. Quando terminar, selecione **Sim** em **Você concluiu o planejamento da capacidade?**.
  
  ![][11]

7. **Instale o serviço de mobilidade e habilite a replicação**. Você pode optar pela [instalação por push](../site-recovery/site-recovery-vmware-to-azure.md#prepare-for-automatic-discovery-and-push-installation) para suas VMs de origem ou [instalar manualmente o serviço de mobilidade](../site-recovery/site-recovery-vmware-to-azure-install-mob-svc.md) nestas. Você pode encontrar a solicitação de instalação por push e o caminho do instalador do manual no link fornecido. Se estiver fazendo uma instalação manual, será preciso usar um endereço IP interno para localizar o servidor de configuração. 
  
  ![][12]
  
  A VM que sofreu o failover terá dois discos temporários: um da VM primária e outro criado durante o provisionamento da VM na região de recuperação. Para excluir o disco temporário antes da replicação, instale o serviço de mobilidade antes de habilitar a replicação. Para saber mais sobre como excluir o disco temporário, consulte [Excluir discos da replicação](../site-recovery/site-recovery-vmware-to-azure.md#exclude-disks-from-replication).
**Agora habilite a replicação da seguinte maneira:**
  * Clique em **Replicar aplicativo** > **Origem**. Depois de habilitar a replicação pela primeira vez, clique em +Replicar no cofre para habilitar a replicação para outros computadores.
  * Na etapa 1, configure Fonte como o servidor de processo.
  * Na etapa 2, especifique o modelo de implantação pós-failover, uma conta de armazenamento Premium para a migração, uma conta de armazenamento padrão para salvar os logs e uma rede virtual para falhas. 
  * Na etapa 3, adicione VMs protegidas por endereço IP (talvez você precise de um endereço IP interno para encontrá-las). 
  * Na etapa 4, configure as propriedades, selecionando as contas que você configurou anteriormente no servidor de processo. 
  * Na etapa 5, escolha a política de replicação que você criou anteriormente em Definir configurações de replicação. 
  Clique em **OK** e habilite a replicação.
  
  > [!NOTE]
  > Quando uma VM do Azure é desalocada e reiniciada, não há nenhuma garantia de que ela receberá o mesmo endereço IP. Se houver alteração no endereço IP do servidor de processo/configuração ou nas VMs do Azure protegidas, a replicação pode não funcionar corretamente neste cenário.
  
  ![][13]
  
  Ao criar seu ambiente de Armazenamento do Azure, é recomendável usar contas de armazenamento separadas para cada VM em um conjunto de disponibilidade. Recomendamos que você siga a prática recomendada na camada de armazenamento para VMs [Windows](../virtual-machines/virtual-machines-windows-manage-availability.md#use-multiple-storage-accounts-for-each-availability-set) e [Linux](../virtual-machines/virtual-machines-linux-manage-availability.md#use-multiple-storage-accounts-for-each-availability-set). A distribuição de discos VM para várias contas de armazenamento ajuda a melhorar a disponibilidade de armazenamento e distribui a E/S em toda a infraestrutura de armazenamento do Azure. Se suas máquinas virtuais estiverem em um conjunto de disponibilidade, em vez de replicar os discos de todas elas em uma conta de armazenamento, é altamente recomendável migrar várias VMs várias vezes, para que as VMs em um mesmo conjunto de disponibilidade não compartilhem uma única conta de armazenamento. Use a folha **Habilitar replicação** para configurar uma conta de armazenamento de destino para cada VM, uma de cada vez. Você pode escolher um modelo de implantação pós-failover de acordo com suas necessidades. Se escolher o Gerenciador de recursos (RM) como modelo de implantação pós-failover, você pode realizar o failover de uma VM do RM para outra VM do RM ou de uma máquina virtual clássica para uma VM do RM.

8. **Execute um teste de failover**. Para verificar se a replicação foi concluída, clique no Site Recovery e, em seguida, clique em **Configurações** > **Itens replicados**. Você verá o status e a porcentagem do processo de replicação. Após a conclusão da replicação inicial, execute o Failover de Teste para validar a estratégia de replicação. Para obter as etapas detalhadas do failover de teste, consulte [Executar um teste de failover no Site Recovery](../site-recovery/site-recovery-vmware-to-azure.md#run-a-test-failover). Você pode ver o status do failover de teste em **Configurações** > **Trabalhos** > **YOUR_FAILOVER_PLAN_NAME**. Na folha, você verá uma divisão das etapas e resultados de êxito/falha. Se o failover de teste falhar em alguma etapa, clique nela para verificar a mensagem de erro. Verifique se as VMs e sua estratégia de replicação atendem aos requisitos antes de executar um failover. Leia [Failover de teste para o Azure no Site Recovery](../site-recovery/site-recovery-test-failover-to-azure.md) para obter mais informações e instruções sobre o failover de teste.

9. **Executar um failover**. Após a conclusão do failover de teste, execute um failover para migrar os discos para o Armazenamento Premium e replicar as instâncias de VM. Siga as etapas detalhadas em [Executar um failover](../site-recovery/site-recovery-failover.md#run-a-failover). Selecione **Desligar VMs e sincronizar dados mais recentes** para especificar que o Site Recovery deve tentar desligar as VMs protegidas e sincronizar os dados para que ocorra o failover da versão mais recente dos dados. Se você não selecionar essa opção ou a tentativa não tiver êxito, o failover será do último ponto de recuperação disponível da VM. O Site Recovery vai criar uma instância de VM cujo tipo é igual ou semelhante ou de uma VM compatível com armazenamento Premium. Para verificar o desempenho e o preço de várias instâncias de VM, vá para [Preços de Máquinas Virtuais Windows](https://azure.microsoft.com/en-us/pricing/details/virtual-machines/windows/) ou [Preços de Máquinas Virtuais Linux](https://azure.microsoft.com/en-us/pricing/details/virtual-machines/linux/).

## <a name="post-migration-steps"></a>Etapas pós-migração

1. **Configurar as VMs replicadas para o conjunto de disponibilidade, se aplicável**. O Site Recovery não dá suporte à migração de VMs junto com o conjunto de disponibilidade. Dependendo da implantação da VM replicada, siga um dos seguintes procedimentos:
  * Para uma VM criada usando o modelo de implantação clássico: adicione a VM ao conjunto de disponibilidade no portal do Azure. Para obter as etapas detalhadas, vá para [Adicionar uma máquina virtual existente ao conjunto de disponibilidade](../virtual-machines/virtual-machines-windows-classic-configure-availability.md#a-idaddmachine-aoption-2-add-an-existing-virtual-machine-to-an-availability-set).
  * Para o modelo de implantação do Gerenciador de recursos: salve a configuração da VM e, em seguida, exclua e recrie as VMs no conjunto de disponibilidade. Para fazer isso, use o script em [Definir Conjunto de Disponibilidade de VM do Azure Resource Manager](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4). Verifique se a limitação do script e planeje o tempo de inatividade antes de executá-lo.

2. **Exclua VMs e discos antigos**. Antes de excluir, verifique se os discos Premium são consistentes com os discos de origem e se as novas VMs realizam a mesma função que as VMs de origem. No modelo de implantação do Gerenciador de Recursos (RM), exclua a VM e exclua os discos das contas de armazenamento de origem no portal do Azure. No modelo de implantação clássico, você pode excluir a VM e os discos no portal clássico ou portal do Azure. Se houver um problema em que o disco não seja excluído mesmo que você tenha excluído a VM, consulte [Solucionar erros ao excluir VHDs em uma implantação do RM](storage-resource-manager-cannot-delete-storage-account-container-vhd.md) ou [Solucionar erros ao excluir VHDs em uma implantação clássica](storage-cannot-delete-storage-account-container-vhd.md).

3. **Limpe a infraestrutura do Azure Site Recovery**. Se o Site Recovery não for mais necessário, você pode limpar a infraestrutura, excluindo itens duplicados, o servidor de configuração e a Política de Recuperação e, em seguida, excluir o cofre do Azure Site Recovery.

## <a name="troubleshooting"></a>Solucionar problemas

* [Monitorar e solucionar problemas de proteção para máquinas virtuais e sites físicos](../site-recovery/site-recovery-monitoring-and-troubleshooting.md)
* [Fórum do Microsoft Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hypervrecovmgr)

## <a name="next-steps"></a>Próximas etapas

Consulte as seguintes fontes para cenários específicos de migração de máquinas virtuais:

* [Migrar Máquinas Virtuais do Azure entre as Contas de Armazenamento](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Crie e carregue um VHD do Windows Server no Azure.](../virtual-machines/virtual-machines-windows-classic-createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Criando e Carregando um Disco Rígido Virtual que Contém o Sistema Operacional Linux](../virtual-machines/virtual-machines-linux-classic-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Migrando Máquinas Virtuais do Amazon AWS para o Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Confira também as fontes a seguir para saber mais sobre o Armazenamento do Azure e as Máquinas Virtuais do Azure:

* [Armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/)
* [Máquinas Virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Armazenamento Premium: Armazenamento de Alto Desempenho para as Cargas de Trabalho da Máquina Virtual do Azure](storage-premium-storage.md)

[1]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-1.png
[2]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-2.png
[3]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-3.png
[4]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-4.png
[5]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-5.png
[6]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-6.PNG
[7]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-7.PNG
[8]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-8.PNG
[9]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-9.PNG
[10]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-10.png
[11]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-11.PNG
[12]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-12.PNG
[13]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-13.png
[14]:../site-recovery/media/site-recovery-vmware-to-azure/v2a-architecture-henry.png
[15]:./media/storage-migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-14.png

