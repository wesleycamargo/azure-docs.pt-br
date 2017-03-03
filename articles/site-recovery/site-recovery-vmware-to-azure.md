---
title: Replicar VMs VMware no Azure | Microsoft Docs
description: "Resume as etapas necessárias para replicar as cargas de trabalho em execução em VMs VMware no armazenamento do Azure"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: dab98aa5-9c41-4475-b7dc-2e07ab1cfd18
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 9eb2d7f4b431c01983620cb0cfcffd63a9f4d4e2
ms.openlocfilehash: f7251dffc3dd922a6abeba0faca90843de64430f
ms.lasthandoff: 02/22/2017


---
# <a name="replicate-vmware-virtual-machines-to-azure-with-azure-site-recovery"></a>Replicar VMs VMware no Azure com o Azure Site Recovery

> [!div class="op_single_selector"]
> * [Portal do Azure](site-recovery-vmware-to-azure.md)
> * [Azure clássico](site-recovery-vmware-to-azure-classic.md)


Este artigo descreve como replicar VMs VMware locais para o Azure, usando o serviço [Azure Site Recovery](site-recovery-overview.md) no portal do Azure.

 Se sua intenção é migrar VMs VMware para o Azure, leia [este artigo](site-recovery-migrate-to-azure.md) para saber mais antes de continuar.

Poste perguntas e comentários na parte inferior deste artigo ou no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="steps"></a>Etapas

Você precisa saber do seguinte:

1. Verifique os pré-requisitos e as limitações.
2. Configure contas de rede e armazenamento do Azure.
3. Prepare o computador local que você deseja implantar como servidor de configuração.
4. Prepare contas VMware a serem usadas para a descoberta automática de VMs e, opcionalmente, para instalação por push do serviço de Mobilidade.
4. Crie um cofre dos Serviços de Recuperação. O cofre contém definições de configuração e orquestra a replicação.
5. Especifique as configurações de origem, destino e replicação.
6. Implante o serviço de Mobilidade em VMs que você deseja replicar.
7. Habilite a replicação para as VMs.
7. Execute um failover de teste para verificar se tudo está funcionando como esperado.

## <a name="prerequisites"></a>Pré-requisitos

**Requisito de suporte** | **Detalhes**
--- | ---
**As tabelas** | Saiba mais sobre [requisitos do Azure](site-recovery-prereq.md#azure-requirements)
**Servidor de configuração local** | Você precisa de uma VM VMware que esteja executando o Windows Server 2012 R2 ou posterior. Você pode configurar este servidor durante a implantação de Site Recovery.<br/><br/> Por padrão, o servidor de processo e o servidor de destino mestre também são instalados nesta VM. Quando você escalar verticalmente, talvez seja necessário um servidor de processo separado; ele tem os mesmos requisitos que o servidor de configuração.<br/><br/> Saiba mais sobre esses componentes [aqui](site-recovery-set-up-vmware-to-azure.md#configuration-server-minimum-requirements)
**Servidores VMware locais** | Um ou mais servidores VMware vSphere, executando 6.0, 5.5, 5.1 com as últimas atualizações. Os servidores devem estar localizados na mesma rede que o servidor de configuração (ou servidor de processo separado).<br/><br/> Recomendamos um servidor vCenter para gerenciar hosts, executando 6.0 ou 5.5 com as atualizações mais recentes. Somente recursos que estão disponíveis no 5.5 têm suporte quando você implanta a versão 6.0.
**VMs locais** | As VMs que você deseja replicar devem estar executando um [sistema operacional com suporte](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) e estar de acordo com os [pré-requisitos do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements). A VM deve ter ferramentas VMware em execução.
**URLs** | O servidor de configuração precisa acessar estas URLs:<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> Se tiver regras de firewall baseadas no endereço IP, verifique se elas permitem a comunicação com o Azure.<br/></br> Permita os [Intervalos de IP do Datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e a porta HTTPS (443).<br/></br> Permita os intervalos de endereços IP para a região do Azure da sua assinatura e para o Oeste dos EUA (usados para Controle de Acesso e Gerenciamento de Identidade).<br/><br/> Baixe o MySQL desta URL: .http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi
**Serviço de mobilidade** | Instalado em todas as VMs replicadas.




## <a name="limitations"></a>Limitações

**Limitação** | **Detalhes**
--- | ---
**As tabelas** | Contas de armazenamento e rede devem estar na mesma região que o cofre<br/><br/> Se usar uma conta de armazenamento premium, você também precisará de uma conta de armazenamento padrão para armazenar logs de replicação<br/><br/> Você não pode replicar contas premium no Sul e no Centro da Índia.
**Servidor de configuração local** | O tipo de adaptador de VM VMware deve ser VMXNET3. Caso contrário, [instale esta atualização](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1)<br/><br/> O vSphere PowerCLI 6.0 deve estar instalado.<br/><br> O computador não deve ser um controlador de domínio. O computador deve ter um endereço IP estático.<br/><br/> O nome do host deve ter 15 caracteres ou menos, e o sistema operacional deve estar em inglês.
**VMware** | Apenas recursos 5.5 têm suporte no vCenter 6.0. O Site Recovery não dá suporte aos novos recursos do vCenter e vSphere 6.0, como o vCenter vMotion cruzado, os volumes virtuais e o DRS de armazenamento.
**VMs** | Verifique [Limitações de VM do Azure](site-recovery-prereq.md#azure-requirements)<br/><br/> Você não pode replicar máquinas virtuais com discos criptografados ou máquinas virtuais com inicialização EFI/UEFI.<br/><br> Não há suporte para clusters de disco compartilhado. Se a VM de origem tiver agrupamento NIC é feita a conversão para uma única NIC após o failover.<br/><br/> Se as VMs tiverem um disco iSCSI, a Site Recovery o converterá em um arquivo VHD após o failover. Se o destino iSCSI puder ser acessado pela VM do Azure, ela se conectará a ele e o verá juntamente com o VHD. Se isso acontecer, desconecte o destino de iSCSI.<br/><br/> Se desejar habilitar a consistência de várias VMs, o que habilita VMs que executam a mesma carga de trabalho a serem recuperadas juntas em um ponto de dados consistente, abra a porta 20004 na VM.<br/><br/> O Windows deve estar instalado na unidade C. O disco do SO deve ser básico e não dinâmico. O disco de dados pode ser dinâmico.<br/><br/> Os arquivos Linux /etc/hosts nas VMs devem conter entradas que mapeiam o nome do host local para os endereços IP associados a todos os adaptadores de rede. O nome do host, os pontos de montagem, o nome do dispositivo e os caminhos do sistema e nomes de arquivos (/etc/; /usr) devem ser somente em inglês.<br/><br/> Tipos específicos de [armazenamento do Linux](site-recovery-support-matrix-to-azure.md#support-for-storage) têm suporte.<br/><br/>Crie ou defina **disk.enableUUID=true** nas configurações de VM. Isso fornece um UUID consistente para o VMDK, para que ele seja montado corretamente, e garante que apenas as alterações delta sejam transferidas para o local durante o failback, sem replicação completa.

## <a name="set-up-azure"></a>Configurar o Azure

1. [Configure uma rede do Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).
    - As VMs do Azure serão colocadas nessa rede quando forem criadas após o failover.
    - Você pode configurar uma rede no [Gerenciador de Recursos](../resource-manager-deployment-model.md) ou no modo clássico.

2. [Configure uma conta de armazenamento do Azure](../storage/storage-create-storage-account.md#create-a-storage-account) para os dados replicados.
    - A conta pode ser padrão ou [premium](../storage/storage-premium-storage.md).
    - Você pode configurar uma conta no Gerenciador de Recursos ou no modo clássico.

3. [Prepare uma conta](#prepare-for-automatic-discovery-and-push-installation) no servidor vCenter ou nos hosts vSphere para que o Site Recovery possa detectar automaticamente as VMs VMware.

## <a name="prepare-the-configuration-server"></a>Prepare o servidor de configuração

1. Instale o Windows Server 2012 R2 ou posterior em uma VM VMware.
2. Verifique se a VM tem acesso às URLs listadas em [Pré-requisitos](#prerequisites).
3. Instale o [VMware vSphere PowerCLI 6.0](https://developercenter.vmware.com/tool/vsphere_powercli/6.0).


## <a name="prepare-for-automatic-discovery-and-push-installation"></a>Preparar para instalação automática de descoberta e por push

- **Preparar uma conta para a descoberta automática**: o servidor de processo de Site Recovery descobre automaticamente as VMs. Para fazer isso, a Site Recovery precisa de credenciais que possam acessar servidoresvCenter/hosts vSphere ESXi.

    1. Para usar uma conta dedicada, crie uma função (no nível do vCenter com essas [permissões](#vmware-account-permissions). Use um nome como **Azure_Site_Recovery**.
    2. Em seguida, crie um usuário no host vSphere/servidor vCenter e atribua a função ao usuário. Você especifica essa conta de usuário durante a implantação de Site Recovery.

- **Prepare uma conta para enviar por push o serviço de Mobilidade**: se você quiser enviar por push o serviço de Mobilidade para VMs, precisará de uma conta que possa ser usada pelo servidor de processo para acessar a VM. A conta é usada apenas para a instalação por push. Você pode usar uma conta local ou de domínio:

    - Para Windows, se não estiver usando uma conta de domínio, precisará desabilitar o controle de Acesso de Usuário Remoto no computador local. Para fazer isso, no registro, em **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, adicione a entrada DWORD **LocalAccountTokenFilterPolicy** com um valor de 1.
    - Se deseja adicionar a entrada do Registro do Windows de uma CLI, digite:   ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
    - Para Linux, a conta deve ser a raiz no servidor Linux de origem.


[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-the-protection-goal"></a>Selecionar o objetivo de proteção

Selecione o que você deseja replicar e para onde deseja replicar.

1. Clique em **Cofres dos Serviços de Recuperação** > cofre.
2. No Menu Recursos > clique em **Site Recovery** > **Etapa 1: preparar infraestrutura** > **Objetivo de proteção**.

    ![Escolher metas](./media/site-recovery-vmware-to-azure/choose-goals.png)
3. Em **Meta de proteção**, selecione **Para o Azure** e selecione **Sim, com o Hipervisor VMware vSphere**.

    ![Escolher metas](./media/site-recovery-vmware-to-azure/choose-goals2.png)

## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Defina o servidor de configuração, registre-o no cofre e descubra VMs.

1. Clique em **Site Recovery** > **Etapa 1: preparar a infraestrutura** > **Origem**.
2. Se não tiver um servidor de configuração, clique em **+Servidor de configuração**.

    ![Configurar origem](./media/site-recovery-vmware-to-azure/set-source1.png)
3. Em **Adicionar Servidor**, verifique se **Servidor de Configuração** aparece em **Tipo de servidor**.
4. Baixe o arquivo de instalação Configuração Unificada da Recuperação de Site.
5. Baixe a chave do registro do cofre. Você precisará dela quando executar a Configuração Unificada. A chave é válida por cinco dias após ser gerada.

   ![Configurar origem](./media/site-recovery-vmware-to-azure/set-source2.png)
6. Na VM do servidor de configuração, verifique se o relógio do sistema está sincronizado com um [Servidor de Horário](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) e execute a Instalação Unificada para instalar o servidor de configuração, o servidor de processo e o servidor de destino mestre.

## <a name="run-site-recovery-unified-setup"></a>Execute a Configuração Unificada da Recuperação de Site

Antes de começar:

- Verifique se o horário na máquina virtual é o mesmo que o horário em seu fuso horário local. Ele deve ser correspondente. Se ele estiver 15 minutos adiantado ou atrasado, a instalação poderá falhar.
- Execute a instalação como um Administrador Local na VM do servidor de configuração.
- Verifique se TLS 1.0 está habilitado na VM.

Em seguida, execute o arquivo de instalação da Instalação Unificada no servidor de configuração.


[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> O servidor de configuração pode ser instalado por meio de linha de comando. Para saber mais, veja [instalando o servidor de configuração usando as ferramentas de linha de comando](http://aka.ms/installconfigsrv).

### <a name="add-the-account-for-automatic-discovery"></a>Adicione a conta para descoberta automática

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

### <a name="connect-to-vmware-servers"></a>Conectar a servidores VMware

Conecte-se a hosts vSphere ESXi ou servidores vCenter para descobrir VMs VMware.

- Se você adicionar o servidor vCenter ou hosts vSphere com uma conta sem privilégios de administrador no servidor, a conta precisará ter esses privilégios habilitados:
    - Datacenter, Repositório de Dados, Pasta, Host, Rede, Recurso, Máquina Virtual, vSphere Distributed Switch.
    - O servidor vCenter precisa do privilégio de exibição do Armazenamento.
- Quando você adiciona servidores VMware, pode levar 15 minutos ou mais para que eles apareçam no portal.
Para permitir que o Azure Site Recovery descubra máquinas virtuais em execução no ambiente local, você precisa conectar seu VMware vCenter Server ou hosts vSphere ESXi ao Site Recovery.

Selecione **+vCenter** para iniciar a conexão de um VMware vCenter Server ou um host VMware vSphere ESXi.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

A Site Recovery conecta-se a servidores VMware usando as configurações especificadas e descobre VMs.

## <a name="set-up-the-target"></a>Configurar o destino

Antes de configurar o ambiente de destino, verifique se você tem uma [conta de armazenamento do Azure e rede](#set-up-azure)

1. Clique em **Preparar infraestrutura** > **Destino** e selecione a assinatura do Azure que você deseja usar.
2. Especifique se o seu modelo de implantação de destino é baseada no Gerenciador de Recursos ou clássico.
3. A Recuperação de Site verifica se você tem uma ou mais contas de armazenamento e redes do Azure compatíveis.

   ![Destino](./media/site-recovery-vmware-to-azure/gs-target.png)
4. Se não tiver criado uma conta de armazenamento ou de rede, clique em **+Conta de armazenamento** ou **+Rede** para criar uma conta do Gerenciador de Recursos ou embutida de rede.

## <a name="set-up-replication-settings"></a>Definir as configurações de replicação

1. Para criar uma nova política de replicação, clique em **Infraestrutura de Site Recovery** > **Políticas de Replicação** > **+Política de Replicação**.
2. Em **Criar política de replicação**, especifique um nome de política.
3. Em **Limite de RPO**, especifique o limite de RPO. Esse valor especifica com que frequência os pontos de recuperação de dados são criados. Um alerta será gerado se a replicação contínua exceder esse limite.
4. Em **Retenção do ponto de recuperação**, especifique (em horas) qual será a duração da janela de retenção para cada ponto de recuperação. VMs replicadas podem ser recuperadas para qualquer ponto em uma janela. Há suporte para retenção de até 24 horas para máquinas replicadas para armazenamento premium e 72 horas para o armazenamento padrão.
5. Em **Frequência do instantâneo consistente com o aplicativo**, especifique com que frequência (em minutos) os pontos de recuperação contendo instantâneos consistentes com aplicativos serão criados. Clique em **OK** para criar a política.

    ![Política de replicação](./media/site-recovery-vmware-to-azure/gs-replication2.png)
8. Quando você cria uma nova política, ela é automaticamente associada ao servidor de configuração. Por padrão, uma política de correspondência é criada automaticamente para failback. Por exemplo, se a política de replicação for **rep-policy**, a política de failback será **rep-policy-failback**. Essa política não é usada até você iniciar um failback do Azure.  


## <a name="plan-capacity"></a>Planejar a capacidade

1. Agora que você tem a infraestrutura básica configurada, pense sobre o planejamento da capacidade e descubra se precisa de recursos adicionais. [Saiba mais](site-recovery-plan-capacity-vmware.md).
2. Quando terminar o planejamento de capacidade, selecione **Sim** em **Você concluiu o planejamento de capacidade?**

   ![Planejamento da capacidade](./media/site-recovery-vmware-to-azure/gs-capacity-planning.png)


## <a name="prepare-vms-for-replication"></a>Preparar VMs para replicação

Todas as máquinas que você deseja replicar devem ter o serviço de Mobilidade instalado. Você pode instalar o serviço de mobilidade de várias maneiras:

1. Instale com uma instalação por push do servidor de processo. Você precisa preparar as VMs para usar esse método.
2. Instale usando ferramentas de implantação, como System Center Configuration Manager ou DSC de automação do Azure.
3.  Instalar manualmente.

[Saiba mais](site-recovery-vmware-to-azure-install-mob-svc.md)


## <a name="enable-replication"></a>Habilitar a replicação

Antes de começar:

- Quando você adiciona ou modifica as VMs, pode levar 15 minutos ou mais para que as alterações entrem em vigor e para que apareçam no portal.
- Você pode verificar a hora da última descoberta de VMs em **Servidores de Configuração** > **Último Contato às**.
- Para adicionar VMs sem esperar pela descoberta agendada, realce o servidor de configuração (não clique nele) e clique em **Atualizar**.
* Se uma VM for preparada para a instalação por push, o servidor de processo instalará automaticamente o serviço de Mobilidade quando você habilitar a replicação.


### <a name="exclude-disks-from-replication"></a>Excluir discos da replicação

Por padrão, todos os discos em um computador são replicados. Você pode excluir discos da replicação. Por exemplo, talvez você não queira replicar discos com dados temporários ou dados atualizados cada vez que um computador ou um aplicativo é reiniciado (por exemplo, pagefile.sys ou SQL Server tempdb).

### <a name="replicate-vms"></a>Replicar VMs

1. Clique em **Etapa 2: replicar aplicativo** > **Origem**.
2. Em **Origem**, selecione o servidor de configuração.
3. Em **Tipo de máquina**, selecione **Máquinas Virtuais**.
4. Em **Hipervisor do vCenter/vSphere**, selecione o servidor vCenter que gerencia o host vSphere ou selecione o host.
5. Selecione o servidor de processo. Se você não criou nenhum servidor de processo adicional, esse será o servidor de configuração. Em seguida, clique em **OK**.

    ![Habilitar a replicação](./media/site-recovery-vmware-to-azure/enable-replication2.png)

6. Em **Destino**, selecione a assinatura e o grupo de recursos em que você deseja criar as VMs com failover. Escolha o modelo de implantação que você deseja usar no Azure (clássico ou gerenciamento de recursos) para as VMs do failover.


7. Selecione a conta de armazenamento do Azure que você deseja usar para replicar os dados. Se não quiser usar uma conta que já configurou, você poderá criar uma nova.

8. Selecione a rede e a sub-rede do Azure às quais as VMs do Azure se conectarão quando forem criadas após o failover. Selecione **Configurar agora para computadores selecionados** para aplicar a configuração de rede a todos os computadores selecionados para proteção. Selecione **Configurar mais tarde** para selecionar a rede do Azure por computador. Se não quiser usar uma rede existente, você poderá criar uma.

    ![Habilitar a replicação](./media/site-recovery-vmware-to-azure/enable-rep3.png)
9. Em **Máquinas Virtuais** > **Selecionar máquinas virtuais**, clique e selecione cada máquina que você deseja replicar. Você só pode selecionar computadores para os quais a replicação pode ser habilitada. Em seguida, clique em **OK**.

    ![Habilitar a replicação](./media/site-recovery-vmware-to-azure/enable-replication5.png)
10. Em **Propriedades** > **Configurar propriedades**, selecione a conta que será usada pelo servidor de processo para instalar automaticamente o serviço de Mobilidade no computador.
11. Por padrão, todos os discos são replicados. Clique em **Todos os Discos** e desmarque os discos que você não deseja replicar. Em seguida, clique em **OK**. Você pode definir propriedades de VM adicionais posteriormente.

    ![Habilitar a replicação](./media/site-recovery-vmware-to-azure/enable-replication6.png)
11. Em **Configurações de replicação** > **Definir configurações de replicação**, verifique se a política de replicação correta está selecionada. Se você modificar uma política, as alterações serão aplicadas à máquina de replicação e às novas máquinas.
12. Habilite **Consistência de várias VMs** se você quiser reunir computadores em um grupo de replicação e especifique um nome para o grupo. Em seguida, clique em **OK**. Observe que:

    * Os computadores nos grupos de replicação são replicados em conjunto e têm pontos de recuperação consistentes compartilhados com o aplicativo e com falhas quando executam failover.
    * É recomendável que você colete VMs e servidores físicos para que espelhem suas cargas de trabalho. Habilitar a consistência de várias VMs pode afetar o desempenho da carga de trabalho e só deve ser usada se os computadores estão executando a mesma carga de trabalho e precisam de consistência.

    ![Habilitar a replicação](./media/site-recovery-vmware-to-azure/enable-replication7.png)
13. Clique em **Habilitar a Replicação**. Você pode acompanhar o progresso do trabalho **Habilitar Proteção** em **Configurações** > **Trabalhos** > **Trabalhos de Recuperação de Site**. Após o trabalho de **Finalizar Proteção** ser executado, o computador estará pronto para failover.

Depois que você habilitar a replicação, o serviço de Mobilidade será instalado se você configurar a instalação por push. Depois que o serviço de Mobilidade for instalado por push em uma VM, um trabalho de proteção será iniciado e falhará. Apos a falha, você precisa reiniciar manualmente cada computador. O trabalho de proteção começa novamente e ocorre a replicação inicial.


### <a name="view-and-manage-vm-properties"></a>Exibir e gerenciar as propriedades da VM

É recomendável que você verifique as propriedades da VM e faça as alterações necessárias.

1. Clique em **Itens replicados** > e selecione a máquina. A folha **Conceitos básicos** mostra as informações sobre as configurações e o status dos computadores.
2. Em **Propriedades**, você pode exibir informações de replicação e de failover para a VM.
3. Em **Computação e Rede** > **Propriedades de computação**, você pode especificar o nome da VM do Azure e o tamanho de destino. Modifique o nome para que ele fique em conformidade com os [requisitos do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) , se for necessário.
4. Modifique as configurações da rede de destino, a sub-rede e o endereço IP que será atribuído à VM do Azure:

   - Você pode definir o endereço IP de destino.

    - Se você não fornecer um endereço, o computador com failover usará o DHCP.
    - Se definir um endereço que não esteja disponível no failover, o failover não funcionará.
    - O mesmo endereço IP de destino poderá ser usado para failover de teste caso o endereço esteja disponível na rede de failover de teste.

   - O número de adaptadores de rede é determinado pelo tamanho especificado para a máquina virtual de destino:

     - Se o número de adaptadores de rede na máquina de origem for menor ou igual ao número de adaptadores permitido para o tamanho da máquina de destino, o destino terá o mesmo número de adaptadores que a origem.
     - Se o número de adaptadores para máquina virtual de origem exceder o número permitido para o tamanho de destino e o tamanho máximo de destino será usado.
     - Por exemplo, se uma máquina de origem tiver dois adaptadores de rede e o tamanho da máquina de destino der suporte a quatro, a máquina de destino terá dois adaptadores. Se a máquina de origem tiver dois adaptadores, mas o tamanho de destino com suporte oferecer suporte apenas a uma máquina de destino, ela terá apenas um adaptador.     
   - Se a máquina virtual tiver vários adaptadores de rede, todos eles se conectarão à mesma rede.
   - Se a máquina virtual tiver vários adaptadores de rede, o primeiro deles mostrado na lista se tornará o adaptador de rede *Padrão* na máquina virtual do Azure.
5. Em **Discos**, você pode ver o sistema operacional da VM e os discos de dados que serão replicados.

## <a name="run-a-test-failover"></a>Execute um teste de failover

Depois de configurar tudo, execute um failover de teste para conferir se tudo está funcionando conforme o esperado.


1. Para fazer failover em um único computador, em **Configurações** > **Itens Replicados**, clique na VM > ícone **+Failover de Teste**.

    ![Failover de Teste](./media/site-recovery-vmware-to-azure/TestFailover.png)

1. Para fazer failover de um plano de recuperação, em **Configurações** > **Planos de Recuperação**, clique com o botão direito do mouse no plano > **Failover de Teste**. Para criar um plano de recuperação, [siga estas instruções](site-recovery-create-recovery-plans.md).  

1. Em **Failover de Teste**, selecione a rede do Azure à qual as VMs do Azure serão conectadas após o failover.

1. Clique em **OK** para iniciar o failover. Você pode acompanhar o andamento clicando na VM para abrir suas propriedades ou no trabalho **Failover de Teste** no nome do cofre **Configurações** > **Trabalhos** > **Trabalhos de Recuperação de Site**.

1. Após a conclusão do failover, você também deve ver a réplica do computador do Azure no portal do Azure > **Máquinas Virtuais**. Verifique se a VM é do tamanho apropriado, se está conectada à rede adequada e se está em execução.

1. Se você tiver se [preparado para conexões após o failover](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover), deverá poder se conectar à VM do Azure.

1. Quando terminar, clique em **Failover de teste de limpeza** no plano de recuperação. Em **Observações** , registre e salve todas as observações associadas ao failover de teste. Isso excluirá as máquinas virtuais que foram criadas durante o failover de teste.

Para obter mais detalhes, consulte o documento [Failover de teste para o Azure](site-recovery-test-failover-to-azure.md).
## <a name="vmware-account-permissions"></a>Permissões de conta do VMware

A Site Recovery precisa de acesso ao VMware para que o servidor de processo descubra automaticamente as VMs e para failover e failback de VMs.

- **Migrar**: se você quiser migrar VMs VMware para o Azure, sem nunca realizar failback novamente, poderá usar uma conta do VMware com uma função somente leitura. Essa função pode executar o failover, mas não pode desligar máquinas de origem protegidas. Isso não é necessário para a migração.
- **Replicação/recuperação**: se você desejar implantar a replicação completa (replicar, failover e failback) a conta deverá ser capaz de executar operações como criar e remover discos, ativar VMs etc.
- **Descoberta automática**: é necessário ter pelo menos uma conta somente leitura.


**Tarefa** | **Conta/função necessária** | **Permissões** | **Detalhes**
--- | --- | --- | ---
**Servidor de processo descobre automaticamente as VMs VMware** | Você precisa de pelo menos um usuário somente leitura | Objeto de data center –> Propagar para o objeto filho, função = somente leitura | Usuário atribuído no nível de datacenter e tem acesso a todos os objetos no datacenter.<br/><br/> Para restringir o acesso, atribua a função **Nenhum acesso** com o objeto **Propagar para filho** aos objetos filho (hosts vSphere, armazenamentos de dados, VMs e redes).
**Failover** | Você precisa de pelo menos um usuário somente leitura | Objeto de data center –> Propagar para o objeto filho, função = somente leitura | Usuário atribuído no nível de datacenter e tem acesso a todos os objetos no datacenter.<br/><br/> Se desejar restringir o acesso, atribua a função **Nenhum acesso** com o objeto **Propagar para objeto filho** aos objetos filho (hosts vSphere, armazenamentos de dados, VMs e redes).<br/><br/> É útil para fins de migração, mas não para replicação completa, failover ou failback.
**Failover e failback** | Sugerimos que você crie uma função (Azure_Site_Recovery) com as permissões necessárias e atribua a função a um usuário ou grupo do VMware | Objeto de Data Center –> Propagar para o Objeto Filho, função = Azure_Site_Recovery<br/><br/> Armazenamento de dados -> alocar espaço, procurar armazenamento de dados, operações de arquivo de baixo nível, remover arquivo, atualizar arquivos de máquina virtual<br/><br/> Rede -> Atribuição de rede<br/><br/> Recurso -> Atribuir VM ao pool de recursos, migrar VM desligada, migrar VM ligada<br/><br/> Tarefas -> Criar tarefa, atualizar tarefa<br/><br/> Máquina virtual -> Configuração<br/><br/> Máquina virtual -> Interagir -> responder à pergunta, conexão de dispositivo, configurar mídia de CD, configurar mídia de disquete, desligar, ligar, instalação de ferramentas VMware<br/><br/> Máquina virtual -> Inventário -> Criar, registrar, cancelar registro<br/><br/> Máquina virtual -> Provisionamento -> Permitir download de máquina virtual, permitir upload de arquivos de máquina virtual<br/><br/> Máquina virtual -> Instantâneos -> Remover instantâneos | Usuário atribuído no nível de datacenter e tem acesso a todos os objetos no datacenter.<br/><br/> Para restringir o acesso, atribua a função **Nenhum acesso** com o objeto **Propagar para filho** aos objetos filho (hosts vSphere, armazenamentos de dados, VMs e redes).


## <a name="next-steps"></a>Próximas etapas

Depois que você ativar a replicação, quando ocorrer uma paralisação, você fará o failover no Azure, e as VMs do Azure serão criadas com base nos dados replicados. Você pode acessar as cargas de trabalho e aplicativos no Azure, até fazer failback para o local principal quando ele retornar às operações normais.

- [Saiba mais](site-recovery-failover.md) sobre diferentes tipos de failovers e como executá-los.
- Se estiver migrando máquinas em vez de replicar e fazer failback, [leia mais](site-recovery-migrate-to-azure.md#migrate-on-premises-vms-and-physical-servers).
- [Leia sobre failback](site-recovery-failback-azure-to-vmware.md), para executar failback e replicar VMs do Azure de volta para o site primário local.

## <a name="third-party-software-notices-and-information"></a>Avisos e informações de softwares de terceiros
Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”).  Microsoft is the not original author of the Third Party Code.  The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only.  This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.  

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.

