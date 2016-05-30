<properties 
   pageTitle="Realizar o failback de máquinas virtuais VMware e servidores físicos para o site local | Microsoft Azure"
   description="Saiba mais sobre como realizar o failback no site local após o failover de VMs do VMware e de servidores físicos no Azure." 
   services="site-recovery" 
   documentationCenter="" 
   authors="rayne-wiselman" 
   manager="jwhit" 
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="required" 
   ms.date="01/11/2015"
   ms.author="raynew"/>

# Realizar o failback de máquinas virtuais VMware e servidores físicos para o site local

> [AZURE.SELECTOR]
- [Portal do Azure](site-recovery-failback-azure-to-vmware.md)
- [Portal Clássico do Azure](site-recovery-failback-azure-to-vmware-classic.md)
- [Portal Clássico do Azure (Herdado)](site-recovery-failback-azure-to-vmware-classic-legacy.md)



Este artigo descreve como realizar o failback de máquinas virtuais do Azure para o site local. Siga as instruções neste artigo quando estiver pronto para realizar o failback de suas máquinas virtuais VMware ou de servidores físicos Windows/Linux após a realização do failover do site local para o Azure usando este [tutorial](site-recovery-vmware-to-azure-classic.md).



## Visão geral

Este diagrama mostra a arquitetura de failback para esse cenário.

Use essa arquitetura quando o servidor de processo é local e você está usando uma Rota Expressa.

![](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)

Use essa arquitetura quando o servidor de processo está no Azure e você tem uma VPN ou uma conexão de Rota Expressa.

![](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)

Para ver a lista completa de portas e o diagrama da arquitetura de failback, consulte a imagem abaixo.

![](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

Veja a seguir como o failback funciona:

- Após a realização do failover para o Azure, execute o failback para seu site local por meio de alguns estágios:
	- **Estágio 1**: proteja novamente as VMs do Azure para que elas comecem a replicar de volta para as VMs do VMware em execução em seu site local. A habilitação da nova proteção move a VM para um grupo de proteção de failback criado automaticamente durante a criação original do grupo de proteção de failover. Se você adicionou seu grupo de proteção de failover a um plano de recuperação, o grupo de proteção de failback também terá sido adicionado automaticamente ao plano. Durante a nova proteção, especifique como planejar o failback.
	- **Estágio 2**: após as VMs do Azure estarem replicando para seu site local, execute um failover para realizar o failback do Azure.
	- **Estágio 3**: após realizar o failback de seus dados, você protege novamente as VMs locais para as quais você realizou o failback, para que comecem a serem replicadas no Azure.

> [AZURE.VIDEO enhanced-vmware-to-azure-failback]


### Failback para o local original ou para um local alternativo

Se você realizou o failover de uma VM do VMware, poderá realizar o failback para a mesma VM de origem, caso ela ainda exista no local. Nessa situação, apenas as alterações delta passarão por failback. Observe que:

- Se você realizou o failover de servidores físicos, o failback sempre ocorrerá para uma nova VM do VMware.
	- Antes de realizar failback em uma máquina física, observe que
	- A máquina física protegida voltará como uma máquina virtual após o failover do Azure para o VMware
	- Você deverá descobrir pelo menos um servidor de Destino Mestre juntamente com os hosts ESX/ESXi necessários para os quais você precisa fazer failback.
- Se você realizar o failback para a VM original, precisará do seguinte:
	- Se a VM for gerenciada por um servidor vCenter, o host do ESX de Destino Mestre deve ter acesso ao repositório de dados das máquinas virtuais.
	- Se a VM estiver em um host ESX, mas não for gerenciada pelo vCenter, o disco rígido da VM deverá estar em um repositório de dados acessível ao host do MT.
	- Se sua VM estiver em um host ESX e não usar o vCenter, complete a descoberta do host ESX do MT antes de protegê-la novamente. Isso se aplicará se você também estiver realizando o failback de servidores físicos.
	- Outra opção (se a VM local existir) é excluí-la antes de fazer um failback. Assim, o failback criará uma nova VM no mesmo host que o host ESX de destino mestre.
	
- Ao realizar o failback para um local alternativo, os dados serão recuperados no mesmo armazenamento de dados e no mesmo host ESX usados pelo servidor de destino mestre local.


## Pré-requisitos

- Você precisará de um ambiente VMware para realizar failbacks de VMs do VMware e servidores físicos. Não há suporte para a realização de failback para um servidor físico.
- Para realizar o failback você deve ter criado uma rede do Azure durante a configuração inicial da proteção. O failback precisa de uma conexão VPN ou de Rota Expressa da rede do Azure onde estão as VMs do Azure para o site local.
- Se as VMs para as quais você deseja realizar o failback forem gerenciadas por um servidor vCenter, você precisará das permissões necessárias para a descoberta das VMs nos servidores vCenter. [Leia mais](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
- Se houver instantâneos em uma VM, a nova proteção falhará. Você pode excluir os instantâneos ou os discos. 
- Antes de realizar failback, será necessário criar diversos componentes:
	- **Crie um servidor de processos no Azure**. Esse servidor de processos é uma VM do Azure que você precisará criar e manter em execução durante o failback. Você pode excluir a máquina após a conclusão do failback.
	- **Criar um servidor de destino mestre**: o servidor de destino mestre envia e recebe dados do failback. O servidor de gerenciamento criado no local tem um servidor de destino mestre instalado por padrão. No entanto, dependendo do volume do tráfego de failback, talvez seja necessário criar um servidor de destino mestre separado para o failback.
	- Se você quiser criar um servidor de destino mestre adicional em execução no Linux, será necessário configurar a VM do Linux antes de instalar o servidor de destino mestre, conforme descrito abaixo.
- O servidor de configuração é necessário localmente para fazer um failback. Durante o failback, a máquina virtual deve existir no banco de dados de servidor de configuração. Caso contrário, o failback não será bem-sucedido. Sendo assim, certifique-se de fazer o backup agendado regular de seu servidor. Em caso de desastre, você precisará restaurá-lo com o mesmo endereço IP para que o failback funcione.

## Configurar o servidor de processos no Azure

É necessário instalar um servidor de processo no Azure para que as VMs do Azure possam enviar os dados de volta para um servidor de destino mestre local.

1.  No portal da Recuperação de Site > **Servidores de Configuração**, selecione a adição de um novo servidor de processo.

	![](./media/site-recovery-failback-azure-to-vmware-classic/ps1.png)

2.  Especifique um nome para o servidor de processo e digite um nome e senha para conexão com a VM do Azure como administrador. Em **Servidor de Configuração**, selecione o servidor de gerenciamento local e especifique a rede do Azure na qual o servidor de processo deve ser implantado. Ela deve ser a rede onde estão localizadas as VMs do Azure. Especifique um endereço IP exclusivo nas opções de sub-rede e inicie a implantação.

	![](./media/site-recovery-failback-azure-to-vmware-classic/ps2.png)

	Ocorrerá o disparo de um trabalho de implantação do servidor de processo.

	![](./media/site-recovery-failback-azure-to-vmware-classic/ps3.png)

	Após a implantação do servidor de processo no Azure, você poderá fazer logon nele usando as credenciais especificadas. No primeiro logon, a caixa de diálogo do servidor processo é executada. Digite o endereço IP do servidor de gerenciamento local e sua senha. Deixe a configuração padrão da porta 443. Você também pode deixar a porta 9443 padrão para replicação de dados, a menos que você tenha modificado especificamente essa configuração durante a configuração do servidor de gerenciamento local.

	>[AZURE.NOTE] O servidor não ficará visível nas **Propriedades da VM**. Ele ficará visível apenas na guia **Servidores**, no servidor de gerenciamento no qual foi registrado. Pode demorar de 10 a 15 minutos para que o servidor de processo apareça.


## Configurar o servidor de destino mestre no local

O servidor de destino mestre recebe os dados do failback. Um servidor de destino mestre é instalado automaticamente no servidor de gerenciamento local, mas se você estiver realizando o failback de muitos dados, talvez seja necessário configurar um servidor de destino mestre adicional. Faça isso da seguinte forma:

>[AZURE.NOTE] Se você quiser instalar um servidor de destino mestre no Linux, siga as instruções do próximo procedimento.

1. Se você estiver instalando o servidor de destino mestre no Windows, abra a página Início Rápido da VM na qual você está instalando o servidor de destino mestre, e baixe o arquivo de instalação do Assistente de Instalação Unificada do Azure Site Recovery.
2. Execute a instalação e, em **Antes de começar**, escolha **Adicionar servidores de processo adicionais para escalar horizontalmente a implantação**.
3. Conclua o assistente da mesma forma que fez ao [configurar o servidor de gerenciamento](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server). Na página **Detalhes do Servidor de Configuração**, especifique o endereço IP deste servidor de destino mestre e uma senha para acessar a VM.

### Configurar uma VM do Linux como o servidor de destino mestre
Para configurar o servidor de gerenciamento executando o servidor de destino mestre como uma VM do Linux, será necessário instalar o sistema operacional mínimo CentOS 6.6, recuperar as IDs de SCSI de cada disco SCSI, instalar alguns pacotes adicionais e aplicar algumas alterações personalizadas.

#### Instalar o CentOS 6.6

1.	Instale o sistema operacional mínimo CentOS 6.6 na VM do servidor de gerenciamento. Mantenha o ISO em uma unidade de DVD e inicialize o sistema. Ignore o teste de mídia, escolha Português (Brasil) como idioma, escolha **Dispositivos de Armazenamento Básico**, verifique se o disco rígido não tem dados importantes e clique em **Sim**, descartando quaisquer dados. Insira o nome do host do servidor de gerenciamento e escolha o adaptador de rede do servidor. Na caixa de diálogo **Sistema de Edição**, escolha **Conectar automaticamente** e adicione um endereço IP estático, a rede e as configurações de DNS. Especifique um fuso horário e uma senha raiz para acessar o servidor de gerenciamento. 
2.	Quando receber uma solicitação pelo tipo de instalação desejado, escolha **Criar Layout Personalizado** como a partição. Depois de clicar em **Avançar**, escolha **Gratuito** e clique em Criar. Crie as partições **/**, **/var/crash** e **/home** com **Tipo FS:** **ext4**. Crie a partição de troca como **Tipo FS: troca**.
3.	Se algum dispositivo pré-existente for encontrado, uma mensagem de aviso será exibida. Clique em **Formatar** para formatar a unidade com as configurações de partição. Clique em **Gravar alteração no disco** para aplicar as alterações à partição.
4.	Escolha **Instalar carregador de inicialização** > **Avançar** para instalar o carregador de inicialização na partição raiz.
5.	Após a conclusão da instalação, clique em **Reiniciar**.


#### Recuperar as IDs de SCSI

1. Após a instalação, recupere as IDs de SCSI de cada disco SCSI na VM. Para fazer isso, desligue a VM do servidor de gerenciamento; nas propriedades da VM no VMware, clique com o botão direito do mouse na entrada da VM > **Editar Configurações** > **Opções**.
2. Escolha **Avançado** > **Item geral** e clique em **Parâmetros de Configuração**. Essa opção será desativada quando a máquina estiver em execução. Para torná-la ativa, a máquina deve ser desligada.
3. Se a linha **disk.EnableUUID** existir, defina seu valor como **True** (diferencia maiúsculas de minúsculas). Se já estiver definida como True, cancele e teste o comando SCSI dentro do sistema operacional convidado após a inicialização. 
4.	Se a linha não existir, clique em **Adicionar Linha** e adicione-a com o valor **True**. Não use aspas duplas.

#### Instalar pacotes adicionais

Será necessário baixar e instalar alguns pacotes adicionais.

1.	Verifique se o servidor de destino mestre está conectado à internet.
2.	Execute este comando para baixar e instalar 15 pacotes do repositório CentOS: **# yum install –y xfsprogs perl lsscsi rsync wget kexec-tools**.
3.	Se as máquinas de origem que você está protegendo estiverem executando o sistema de arquivos Linux wit Reiser ou XFS no dispositivo de inicialização ou raiz, baixe e instale outros pacotes da seguinte maneira:

	- # cd /usr/local
	- # wget [http://elrepo.org/linux/elrepo/el6/x86\_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86\_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm)
	- # wget [http://elrepo.org/linux/elrepo/el6/x86\_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86\_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm)
	- # rpm –ivh kmod-reiserfs-0.0-1.el6.elrepo.x86\_64.rpm reiserfs-utils-3.6.21-1.el6.elrepo.x86\_64.rpm
	- # wget [http://mirror.centos.org/centos/6.6/os/x86\_64/Packages/xfsprogs-3.1.1-16.el6.x86\_65.rpm](http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm)
	- # rpm –ivh xfsprogs-3.1.1-16.el6.x86\_64.rpm

#### Aplicar alterações personalizadas

Faça o seguinte para aplicar as alterações personalizadas após a conclusão das etapas pós-instalação e após a instalação dos pacotes:

1.	Copie o binário do Agente Unificado RHEL 6-64 para a VM. Execute este comando para descompactar o binário: **tar –zxvf <file name>**
2.	Execute este comando para dar permissões: **# chmod 755 ./ApplyCustomChanges.sh**
3.	Execute o script: **# ./ApplyCustomChanges.sh**. Você só deve executar o script uma vez. Reinicie o servidor após a execução bem-sucedida do script.


## Executar o failback

### Proteja novamente as VMs do Azure

1.	No portal da Recuperação de Site > guia **Máquinas**, escolha a VM que passou por failover e clique em **Proteger novamente**.
2.	Em **Servidor de Destino Mestre** e **Servidor de Processo**, escolha o servidor de destino mestre local e o servidor de processo da VM do Azure.
3.	Escolha a conta configurada por você para se conectar à VM.
4.	Escolha a versão de failback do grupo de proteção. Por exemplo, se a VM for protegida no PG1, será necessário selecionar PG1\_Failback.
5.	Se você quiser recuperar para um local alternativo, escolha a unidade de retenção e o armazenamento de dados configurados para o servidor de destino mestre. Quando você realiza o failback para o site local, as VMs do VMware no plano de proteção de failback usarão o mesmo armazenamento de dados que o servidor de destino mestre. Se você quiser recuperar a VM réplica do Azure para a mesma VM local, a VM local já deve estar no mesmo armazenamento de dados que o servidor de destino mestre. Se não houver uma VM local, uma nova será criada durante a nova proteção.

	![](./media/site-recovery-failback-azure-to-vmware-classic/failback1.png)

6.	Depois de clicar em **OK** para iniciar a nova proteção, um trabalho começará a replicar a VM do Azure para o site local. Você pode acompanhar o andamento na guia **Trabalhos**.

	![](./media/site-recovery-failback-azure-to-vmware-classic/failback2.png)

### Executar um failover para o site local

Após a nova proteção, a VM é transferida para a versão de failback de seu grupo de proteção e adicionada automaticamente ao plano de recuperação que você usou para a realização do failover no Azure, se houver um.

1.	Na página **Planos de Recuperação**, selecione o plano de recuperação que contém o grupo de failback e clique em **Failover** > **Failover Não Planejado**.
2.	Em **Confirmar Failover**, verifique a direção do failover (para o Azure) e escolha o ponto de recuperação que você deseja usar para o failover (o mais recente). Se tiver habilitado **Várias VMs** durante a configuração das propriedades de replicação, você poderá recuperar para o ponto de recuperação mais recente consistente com o aplicativo ou com controle de falhas. Clique na marca de seleção para iniciar o failover.
3.	Durante o failover, a Recuperação de Site desligará as VMs do Azure. Depois de verificar se o failback foi concluído conforme o esperado, você pode verificar se as VMs do Azure foram desligadas conforme o esperado.

### Proteger novamente o site local

Após a conclusão do failback, seus dados voltarão ao site local, mas não estarão protegidos. Para iniciar a replicação no Azure novamente, faça o seguinte:

1.	No portal da Recuperação de Site > guia **Máquinas**, escolha a VM que passou por failback e clique em **Proteger novamente**. 
2.	Depois de verificar se a replicação para o Azure está funcionando conforme o esperado, você pode excluir, no Azure, as VMs do Azure (que não estão em execução no momento) que passaram por failback.


### Problemas Comuns de failback

1. Se você executar a descoberta de vCenter de Usuário Somente Leitura e proteger as máquinas virtuais, ela terá êxito e o failover funcionará. No momento de Proteger Novamente, ele falhará pois os repositórios de dados não podem ser descobertos. Como sintoma, você não verá os armazenamentos de dados listados ao proteger novamente. Para resolver esse problema, você pode atualizar a credencial do vCenter com a conta apropriada que tenha permissões e repetir o trabalho. [Leia mais](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access)
2. Ao fazer failback de uma VM do Linux e executá-la no local, você poderá observar que o pacote do Gerenciador de Rede é desinstalado do computador. Isso ocorre porque quando a VM é recuperada no Azure, o pacote do Gerenciador de Rede é removido.
3. Quando uma máquina virtual é configurada com um endereço IP Estático e o failover é feito para o Azure, o endereço IP é obtido por meio de DHCP. Quando você executa o failover de volta para o Local, a VM continua a usar o DHCP para obter o endereço IP. Você precisará fazer logon manualmente na máquina e, se necessário, definir o endereço IP de volta para o endereço Estático.
4. Se você estivesse usando a edição gratuita ESXi 5.5 ou a edição gratuita do 6 vSphere Hypervisor, o failover teria êxito, mas isso não acontecerá. Será preciso atualizar para a Licença de Avaliação para habilitar o failback.

## Realizar o failback com a Rota Expressa

Você pode realizar o failback em uma conexão VPN ou pela Rota Expressa do Azure. Se você quiser usar a Rota Expressa, observe o seguinte:

- A Rota Expressa deve ser configurada na rede virtual do Azure para a qual as máquinas de origem passam por failover, e nas quais as VMs do Azure ficam após o failover.
- Os dados são replicados para uma conta de armazenamento do Azure em um ponto de extremidade público. Você deve configurar o emparelhamento público na Rota Expressa com o data center de destino para que a replicação da Recuperação de Site use a Rota Expressa.

<!---HONumber=AcomDC_0518_2016-->