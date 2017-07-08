---
title: "Replicar servidores físicos para o Azure | Microsoft Docs"
description: "Descreve como implantar o Azure Site Recovery para coordenar a replicação, o failover e a recuperação de servidores físicos Windows/Linux locais para o Azure usando o portal do Azure"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: raynew
ROBOTS: NOINDEX, NOFOLLOW
redirect_url: physical-walkthrough-overview
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: a9655ce1540c788d02d178eb619d2051cddda1c2
ms.contentlocale: pt-br
ms.lasthandoff: 06/29/2017

---
---
# <a name="replicate-physical-machines-to-azure-by-using-site-recovery"></a>Replicar computadores físicos para o Azure usando o Site Recovery


Este artigo descreve como replicar computadores físicos locais para o Azure usando o serviço Azure Site Recovery no portal do Azure.

Se você deseja migrar computadores físicos para o Azure (somente failover), leia [Migrar para o Azure com Site Recovery](site-recovery-migrate-to-azure.md) para obter mais informações.

Publique perguntas e comentários na parte inferior deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="prerequisites"></a>Pré-requisitos

**Requisito de suporte** | **Detalhes**
--- | ---
**As tabelas** | Saiba mais sobre os [requisitos do Azure](site-recovery-prereq.md#azure-requirements).
**Servidor de configuração local** | Computador local (físico ou VM do VMware) executando o Windows Server 2012 R2 ou posterior. Você pode configurar este servidor durante a implantação de Site Recovery.<br/><br/> Por padrão, o servidor de processo e o servidor de destino mestre também são instalados nesse computador. Quando você escalar verticalmente, talvez seja necessário um servidor de processo separado; ele tem os mesmos requisitos que o servidor de configuração.<br/><br/> Saiba mais sobre esses componentes em [Configurar o ambiente de origem](site-recovery-set-up-vmware-to-azure.md#configuration-server-minimum-requirements).
**VMs locais** | Os computadores que você deseja replicar devem estar executando um [sistema operacional com suporte](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) e estar em conformidade com os [pré-requisitos do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
**URLs** | O servidor de configuração precisa acessar estas URLs:<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> Se você tiver regras de firewall baseadas no endereço IP, verifique se elas permitem a comunicação com o Azure.<br/></br> Permita os [Intervalos de IP do Datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e a porta HTTPS (443).<br/></br> Permita os intervalos de endereços IP para a região do Azure da sua assinatura e para o Oeste dos EUA (usados para controle de acesso e gerenciamento de identidade).<br/><br/> Baixe o MySQL desta URL: .http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi
**Serviço de mobilidade** | Esse serviço deverá ser instalado em cada computador que você deseje replicar.

## <a name="limitations"></a>Limitações

**Limitação** | **Detalhes**
--- | ---
**As tabelas** | Contas de armazenamento e rede devem estar na mesma região que o cofre.<br/><br/> Se você usar uma conta de armazenamento premium, também precisará de uma conta de armazenamento padrão para armazenar logs de replicação.<br/><br/> Você não pode replicar contas premium no Sul e no Centro da Índia.
**Servidor de configuração local** | Se você instalar o servidor de configuração em uma VM do VMware, o tipo de adaptador da VM deverá ser VMXNET3. Se não for, [instale esta atualização](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1).<br/><br/> Se você estiver usando uma VM do VMware, o vSphere 6.0 PowerCLI deverá ser instalado nela.<br/><br> O computador não deve ser um controlador de domínio.<br/><br/> O computador deve ter um endereço IP estático.<br/><br/> O nome do host deve ter 15 caracteres ou menos e o sistema operacional deve estar em inglês.
**Computadores replicados** | Verifique as [Limitações de VM do Azure](site-recovery-prereq.md#azure-requirements).<br/><br/> Se desejar habilitar a consistência de várias máquinas, o que habilita VMs que executam a mesma carga de trabalho a serem recuperadas juntas em um ponto de dados consistente, abra a porta 20004 na máquina.<br/><br/> Tipos específicos de [armazenamento do Linux](site-recovery-support-matrix-to-azure.md#support-for-storage) têm suporte.
**Failback** | Você não pode realizar o failback do Azure para um computador físico. Se você quiser poder realizar failback para o local após o failover, precisará de um ambiente VMware para que possa executar failback para uma VM do VMware.


## <a name="set-up-azure"></a>Configurar o Azure

1. [Configure uma rede do Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

      a. As VMs do Azure são colocadas nessa rede quando são criadas após o failover.

      b. Você pode configurar uma rede no Azure [Resource Manager](../resource-manager-deployment-model.md) ou no modo clássico.

2. [Configure uma conta de armazenamento do Azure](../storage/storage-create-storage-account.md#create-a-storage-account) para os dados replicados.

    a. A conta pode ser padrão ou [premium](../storage/storage-premium-storage.md).

    b. Você pode configurar uma conta no Resource Manager ou no modo clássico.

## <a name="prepare-the-configuration-server"></a>Prepare o servidor de configuração

1. Instale o Windows Server 2012 R2 ou posterior em um servidor físico local ou VM do VMware.

2. Verifique se o computador tem acesso às URLs listadas em [Pré-requisitos](#prerequisites).

## <a name="prepare-for-mobility-service-installation"></a>Preparar para a instalação do serviço de mobilidade

Se você quiser enviar por push o serviço de Mobilidade para um computador físico, precisará de uma conta que possa ser usada pelo servidor de processo para acessar os computadores. A conta é usada apenas para a instalação por push. Você pode usar uma conta local ou de domínio:

  - Para Windows, se não estiver usando uma conta de domínio, precisará desabilitar o controle de Acesso de Usuário Remoto no computador local. Para fazer isso, no Registro, em **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, adicione a entrada DWORD **LocalAccountTokenFilterPolicy** com um valor de 1. Se você quiser adicionar a entrada do Registro para o Windows de uma interface de linha de comando, digite:

        ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
  - Para Linux, a conta deve ser de um usuário raiz no servidor Linux de origem.


## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="select-the-protection-goal"></a>Selecionar o objetivo de proteção

Selecione o que você deseja replicar e para onde deseja replicar.

1. Clique em **Cofres dos Serviços de Recuperação** > **cofre**.
2. No menu **Recurso**, clique em **Site Recovery** > **Preparar Infraestrutura** > **Meta de proteção**.

    ![Escolher metas](./media/site-recovery-vmware-to-azure/choose-goal-physical.PNG)

3. Em **Meta de proteção**, selecione **Para o Azure** > **Não virtualizado/outro**.


## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Defina o servidor de configuração, registre-o no cofre e descubra VMs.

1. Clique em **Site Recovery** > **Preparar a infraestrutura** > **Origem**.
2. Se não tiver um servidor de configuração, clique em **+Servidor de Configuração**.

    ![Configurar origem](./media/site-recovery-vmware-to-azure/set-source1.png)

3. Em **Adicionar Servidor**, verifique se **Servidor de Configuração** aparece em **Tipo de servidor**.
4. Baixe o arquivo de instalação **Configuração Unificada do Site Recovery**.
5. Baixe a **chave do registro do cofre**. Você precisa dessa chave ao executar a Configuração Unificada. A chave é válida por cinco dias após ser gerada.

   ![Configurar origem](./media/site-recovery-vmware-to-azure/set-source2.png)


## <a name="run-site-recovery-unified-setup"></a>Execute a Configuração Unificada da Recuperação de Site

Antes de começar, faça o seguinte:

- Obtenha uma rápida visão geral em vídeo. (O vídeo descreve como replicar VMs do VMware, mas o processo é semelhante para replicação de computador físico.)

    > [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video1-Source-Infrastructure-Setup/player]

- No computador do servidor de configuração, verifique se o relógio do sistema está sincronizado com um [servidor de horário](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service). Se estiver 15 minutos adiantado ou atrasado, a Instalação poderá falhar.
- Execute a Instalação como administrador local no computador do servidor de configuração.
- Verifique se TLS 1.0 está habilitado na máquina.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> O servidor de configuração também pode ser instalado [da linha de comando](http://aka.ms/installconfigsrv).


## <a name="set-up-the-target-environment"></a>Configurar o ambiente de origem

Antes de configurar o ambiente de destino, verifique se você tem uma [rede e conta de armazenamento do Azure](#set-up-azure).

1. Clique em **Preparar infraestrutura** > **Destino** e selecione a assinatura do Azure que deseja usar.
2. Especifique se o seu modelo de implantação de destino é Resource Manager ou clássico.
3. O Site Recovery verifica se você tem uma ou mais redes e contas de armazenamento do Azure compatíveis.

   ![Destino](./media/site-recovery-vmware-to-azure/gs-target.png)

4. Se você não tiver criado uma conta de armazenamento ou rede, clique em **+Conta de armazenamento** ou **+Rede** para criar uma conta do Resource Manager ou uma rede embutida.

## <a name="set-up-replication-settings"></a>Definir as configurações de replicação

Antes de começar assista a uma rápida visão geral em vídeo. (O vídeo descreve como replicar VMs do VMware, mas o processo é semelhante para replicação de computador físico.)

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video2-vCenter-Server-Discovery-and-Replication-Policy/player]

1. Para criar uma nova política de replicação, clique em **Infraestrutura de Site Recovery** > **Políticas de Replicação** > **+Política de Replicação**.
2. Em **Criar política de replicação**, especifique um nome de política.
3. Em **Limite de RPO**, especifique o limite de RPO. Esse valor especifica com que frequência os pontos de recuperação de dados são criados. Um alerta será gerado se a replicação contínua exceder esse limite.
4. Em **Retenção do ponto de recuperação**, especifique (em horas) qual será a duração da janela de retenção para cada ponto de recuperação. VMs replicadas podem ser recuperadas para qualquer ponto em uma janela. Há suporte para até 24 horas de retenção para computadores replicados para o armazenamento premium. Há suporte para até 72 horas de retenção para computadores replicados no armazenamento standard.
5. Em **Frequência de instantâneos consistentes com o aplicativo**, especifique com que frequência (em minutos) serão criados os pontos de recuperação que contenham instantâneos consistentes com o aplicativo. Clique em **OK** para criar a política.

    ![Política de replicação](./media/site-recovery-vmware-to-azure/gs-replication2.png)

6. Ao criar uma nova política, ela é automaticamente associada ao servidor de configuração. Por padrão, uma política de correspondência é criada automaticamente para failback. Por exemplo, se a política de replicação for **rep-policy**, a política de failback será **rep-policy-failback**. Essa política não é usada até você iniciar um failback do Azure.  


## <a name="plan-capacity"></a>Planejar a capacidade

1. Agora que você tem a infraestrutura básica configurada, pode pensar no planejamento da capacidade e descobrir se precisa de recursos adicionais. [Saiba mais](site-recovery-plan-capacity-vmware.md).
2. Quando terminar o planejamento de capacidade, selecione **Sim** em **Você concluiu o planejamento de capacidade?**

   ![Planejamento da capacidade](./media/site-recovery-vmware-to-azure/gs-capacity-planning.png)


## <a name="prepare-vms-for-replication"></a>Preparar VMs para replicação

Todas as máquinas que você deseja replicar devem ter o serviço de Mobilidade instalado. Você pode instalar o serviço de mobilidade de várias maneiras:

- Instale o serviço com uma instalação por push do servidor de processo. Você precisa preparar máquinas com antecedência para usar esse método.
- Instale o serviço usando as ferramentas de implantação, como o System Center Configuration Manager ou a Configuração de Estado Desejado da Automação do Azure.
- Instale o serviço manualmente.

[Saiba mais](site-recovery-vmware-to-azure-install-mob-svc.md).


## <a name="enable-replication"></a>Habilitar a replicação

Antes de começar:

- Sua conta de usuário do Azure precisa ter certas [permissões](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) para habilitar a replicação de uma nova máquina virtual para o Azure.
- Quando você adiciona ou modifica VMs, pode levar 15 minutos ou mais para que as alterações entrem em vigor e apareçam no portal.
- Você pode verificar a hora da última descoberta de VMs em **Servidores de Configuração** > **Último Contato às**.
- Para adicionar VMs sem esperar pela descoberta agendada, realce o servidor de configuração (não clique nele) e clique em **Atualizar**.
- Se uma VM for preparada para a instalação por push, o servidor de processo instalará automaticamente o serviço de Mobilidade quando você habilitar a replicação.
- Obtenha uma rápida visão geral em vídeo. (O vídeo descreve como replicar VMs do VMware, mas o processo é semelhante para replicação de computador físico.)

    >[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video3-Protect-VMware-Virtual-Machines/player]


### <a name="exclude-disks-from-replication"></a>Excluir discos da replicação

Por padrão, todos os discos em um computador são replicados. Você pode excluir discos da replicação. Por exemplo, talvez você não queira replicar discos que tenha dados temporários ou dados que sejam atualizados sempre que um computador ou um aplicativo é reiniciado (por exemplo, pagefile.sys ou tempdb do SQL Server).

### <a name="replicate-vms"></a>Replicar VMs

1. Clique em **Replicar aplicativo** > **Origem**.
2. Em **Origem**, selecione **Local**.
3. Em **Local de origem**, selecione o nome do servidor de configuração.
4. Em **Tipo de máquina**, selecione **Máquinas Físicas**.
5. Em **Servidor de processo**, escolha o servidor de processo. Se você não criou nenhum servidor de processo adicional, esse servidor será o servidor de configuração. Em seguida, clique em **OK**.

    ![Habilitar a replicação](./media/site-recovery-physical-to-azure/chooseVM.png)

6. Em **Destino**, selecione a **Assinatura** e o **Grupo de recursos** em que você deseja criar as VMs do Azure após o failover. Escolha o modelo de implantação que você deseja usar no Azure (clássico ou Resource Manager) para as VMs do failover.

7. Selecione a conta de armazenamento do Azure que você deseja usar para replicar os dados. Se não quiser usar uma conta que já configurou, você poderá criar uma nova.

8. Selecione a **Rede** e a **Sub-rede do Azure** às quais as VMs do Azure serão conectadas após o failover. Selecione **Configurar agora para computadores selecionados** para aplicar a configuração de rede a todos os computadores selecionados para proteção. Selecione **Configurar mais tarde** para selecionar a rede do Azure por computador. Se não quiser usar uma rede existente, você poderá criar uma.

    ![Habilitar a replicação](./media/site-recovery-physical-to-azure/targetsettings.png)

9. Em **Computadores físicos**, clique em **+Computador físico** e insira o **Nome** e **Endereço IP**. Escolha o sistema operacional do computador que você deseja replicar. Demora alguns minutos até que os computadores sejam descobertos e mostrados na lista.

    ![Habilitar a replicação](./media/site-recovery-physical-to-azure/machineselect.png)

10. Em **Propriedades** > **Configurar propriedades**, selecione a conta que será usada pelo servidor de processo para instalar automaticamente o serviço de Mobilidade no computador.
11. Por padrão, todos os discos são replicados. Clique em **Todos os Discos** e desmarque quaisquer discos que você não deseje replicar. Em seguida, clique em **OK**. Você pode definir propriedades de VM adicionais posteriormente.

    ![Habilitar a replicação](./media/site-recovery-physical-to-azure/configprop.png)

12. Em **Configurações de replicação** > **Definir configurações de replicação**, verifique se a política de replicação correta está selecionada. Se você modificar uma política, as alterações serão aplicadas ao computador de replicação e aos novos computadores.
13. Habilite **Consistência de várias VMs** se você quiser reunir computadores em um grupo de replicação e especifique um nome para o grupo. Em seguida, clique em **OK**. Observe que:

    a. Os computadores nos grupos de replicação são replicados em conjunto e têm pontos de recuperação consistentes com falha e consistentes com aplicativos compartilhados quando executam failover.

    b. É recomendável que você colete VMs e servidores físicos para que espelhem suas cargas de trabalho. Habilitar a consistência de várias VMs pode afetar o desempenho da carga de trabalho. Ela deverá ser usada somente se os computadores estiverem executando a mesma carga de trabalho e você precisar de consistência.

    ![Habilitar a replicação](./media/site-recovery-physical-to-azure/policy.png)

14. Clique em **Habilitar a Replicação**. Você pode acompanhar o progresso do trabalho **Habilitar Proteção** em **Configurações** > **Trabalhos** > **Trabalhos de Recuperação de Site**. Após o trabalho de **Finalizar Proteção** ser executado, o computador estará pronto para failover.

Depois de habilitar a replicação, o serviço de Mobilidade será instalado se você configurar uma instalação por push. Depois que o serviço de Mobilidade for instalado por push em um computador, um trabalho de proteção será iniciado e falhará. Após a falha, você precisa reiniciar manualmente cada computador. O trabalho de proteção começa novamente e ocorre a replicação inicial.


### <a name="view-and-manage-azure-vm-properties"></a>Exibir e gerenciar as propriedades da VM do Azure

É recomendável verificar as propriedades da VM e fazer as alterações necessárias.

1. Clique em **Itens replicados** e selecione o computador. A folha **Conceitos básicos** mostra as informações sobre as configurações e o status dos computadores.
2. Em **Propriedades**, você pode exibir informações de replicação e de failover para a VM.
3. Em **Computação e Rede** > **Propriedades de computação**, você pode especificar o nome da VM do Azure e o tamanho de destino. Modifique o nome para que ele fique em conformidade com os [requisitos do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) , se for necessário.
4. Modifique as configurações para a rede de destino, a sub-rede e o endereço IP atribuídos à VM do Azure:

    a. Você pode definir o endereço IP de destino.

    b.  Se você não fornecer um endereço, o computador que fez failover usará o DHCP.

    c. Se você definir um endereço que não esteja disponível no failover, o failover não funcionará.

    d. O mesmo endereço IP de destino poderá ser usado para failover de teste caso o endereço esteja disponível na rede de failover de teste.

    e. O número de adaptadores de rede é determinado pelo tamanho especificado para a máquina virtual de destino:

     - Se o número de adaptadores de rede no computador de origem for menor ou igual ao número de adaptadores permitido para o tamanho do computador de destino, o destino terá o mesmo número de adaptadores que a origem.
     - Se o número de adaptadores para máquina virtual de origem exceder o número permitido para o tamanho de destino e o tamanho máximo de destino usado.
     - Por exemplo, se um computador de origem tiver dois adaptadores de rede e o tamanho do computador de destino der suporte a quatro, o computador de destino terá dois adaptadores. Se o computador de origem tiver dois adaptadores, mas o tamanho de destino com suporte só aceitar um, o computador de destino terá apenas um adaptador.     
   - Se a máquina virtual tiver vários adaptadores de rede, todos eles se conectarão à mesma rede.
   - Se a máquina virtual tiver vários adaptadores de rede, o primeiro deles mostrado na lista se tornará o adaptador de rede *Padrão* na VM do Azure.
5. Em **Discos**, você pode ver o sistema operacional da VM e os discos de dados que são replicados.

## <a name="run-a-test-failover"></a>Execute um teste de failover

Depois de configurar tudo, execute um failover de teste para garantir que tudo esteja funcionando conforme o esperado. Assista uma rápida visão geral em vídeo antes de começar.

>[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video4-Recovery-Plan-DR-Drill-and-Failover/player]


1. Para fazer o failover de um único computador, em **Configurações** > **Itens Replicados**, clique em **Failover de Teste**.

    ![Failover de Teste](./media/site-recovery-vmware-to-azure/TestFailover.png)

2. Para fazer failover de um plano de recuperação, em **Configurações** > **Planos de Recuperação**, clique com o botão direito do mouse no plano > **Failover de Teste**. Para criar um plano de recuperação, [siga estas instruções](site-recovery-create-recovery-plans.md).  
3. Em **Failover de Teste**, selecione a rede do Azure à qual as VMs do Azure serão conectadas após o failover.
4. Clique em **OK** para iniciar o failover. Você pode acompanhar o andamento clicando na VM para abrir suas propriedades ou clicando no trabalho **Failover de Teste** no nome do cofre **Configurações** > **Trabalhos** > **Trabalhos do Site Recovery**.
5. Após a conclusão do failover, você também deve conseguir ver a réplica do computador Azure exibida no Portal do Azure > **Máquinas Virtuais**. Verifique se a VM é do tamanho apropriado, se está conectada à rede adequada e se está em execução.
6. Se você tiver se [preparado para conexões após o failover](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover), deverá poder se conectar à VM do Azure.
7. Quando terminar, clique em **Limpar failover de teste** no plano de recuperação. Em **Observações**, registre e salve todas as observações associadas ao failover de teste. Essa etapa exclui máquinas virtuais criadas durante o failover de teste.

Para obter mais informações, consulte o documento [Failover de teste para o Azure](site-recovery-test-failover-to-azure.md).

## <a name="next-steps"></a>Próximas etapas

Depois que você ativar a replicação, quando ocorrer uma paralisação, você fará o failover no Azure, e as VMs do Azure serão criadas com base nos dados replicados. Você pode acessar as cargas de trabalho e aplicativos no Azure, até fazer failback para o local principal quando ele retornar às operações normais.

- [Saiba mais](site-recovery-failover.md) sobre diferentes tipos de failovers e como executá-los.
- Se estiver migrando máquinas em vez de replicar e fazer failback, [leia mais](site-recovery-migrate-to-azure.md#migrate-on-premises-vms-and-physical-servers).
- Ao replicar máquinas físicas, você pode fazer failback apenas para um ambiente VMware. [Leia sobre failback](site-recovery-failback-azure-to-vmware.md).

## <a name="third-party-software-notices-and-information"></a>Avisos e informações de softwares de terceiros
Do Not Translate or Localize

O software e o firmware em execução no serviço ou produto da Microsoft incorporam ou se baseiam no material dos projetos listados abaixo (coletivamente, "Código de Terceiros"). A Microsoft não é o autor original do Código de Terceiros. A notificação de direitos autorais original e a licença, sob as quais a Microsoft recebeu esse Código de Terceiros, são definidas abaixo.

As informações na Seção A referem-se a componentes de Código de Terceiros dos projetos listados abaixo. Such licenses and information are provided for informational purposes only. Esse Código de Terceiros está sendo relicenciado para você pela Microsoft conforme os termos de licenciamento de software da Microsoft para o produto ou o serviço da Microsoft.  

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

O arquivo completo pode ser encontrado no [Centro de Download da Microsoft](http://go.microsoft.com/fwlink/?LinkId=529428). A Microsoft reserva todos os direitos não explicitamente concedidos neste documento, seja por implicação, preclusão ou de outra forma.

