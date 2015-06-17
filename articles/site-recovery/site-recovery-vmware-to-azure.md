<properties 
	pageTitle="Configurar a proteção entre máquinas virtuais VMWare locais ou servidores físicos e o Azure" 
	description="O Azure Site Recovery coordena a replicação, o failover e a recuperação de máquinas virtuais localizadas nos servidores VMWare locais para o Azure e entre servidores locais físicos e o Azure." 
	services="site-recovery" 
	documentationCenter="" 
	authors="raynew" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/26/2015" 
	ms.author="raynew"/>


# Configurar a proteção entre máquinas virtuais VMWare locais ou servidores físicos e o Azure

O Azure Site Recovery contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) gerenciando replicação, failover e recuperação de máquinas virtuais em vários cenários de implantação. Leia sobre possíveis cenários de implantação na [Visão geral do Azure Site Recovery](../hyper-v-recovery-manager-overview.md).

Este passo a passo descreve como implantar o Site Recoveryf para:

- **Proteger as máquinas virtuais VMWare locais para o Azure**
- **Proteger servidores físicos locais do Windows e Linux para o Azure**

As vantagens de negócios incluem:

- Proteção de servidores físicos do Windows ou Linux.
- Replicação, failover e recuperação simples usando o portal do Azure Site Recovery.
- Replicação de dados pela Internet, uma conexão VPN site a site ou por meio da Rota Expressa do Azure.   
- Failback (restauração) do Azure para uma infraestrutura de VMWare local. 
- Detecção simplificada das máquinas virtuais VMWare.
- Consistência de múltiplas VMs para que as máquinas virtuais e os servidores físicos executando cargas de trabalho específicas possam ser recuperados juntos para um ponto de dados consistente.
- Planos de recuperação para failover e recuperação simplificados das cargas de trabalho em camadas em vários computadores.

Esse recurso está atualmente na visualização. Leia os [Termos de Uso Adicionais para Visualizações](preview-supplemental-terms).

## Sobre este guia

O guia inclui uma visão geral e pré-requisitos de implantação. Ele o orienta ao longo da configuração de todos os componentes de implantação e da habilitação da proteção para máquinas virtuais e servidores. Ele é concluído com o teste do failover para verificar se tudo está funcionando conforme o esperado.

Se tiver problemas, poste suas perguntas no [Fórum de Serviços de Recuperação do Azure](http://go.microsoft.com/fwlink/?LinkId=313628).

## Visão geral

Este diagrama ilustra os componentes de implantação.

![New vault](./media/site-recovery-vmware-to-azure/ASRVMWare_Arch.png)

### Componentes de implantação

- **Computadores locais** - seu site local tem computadores que você deseja proteger. Trata-se de máquinas virtuais em execução em um hipervisor VMWare ou servidores físicos executando o Windows ou o Linux.

- **Servidor em processo local** - os computadores protegidos enviam dados de replicação ao servidor em processo local. O servidor em processo executa várias ações nesses dados. Ele os otimiza antes de enviar para o servidor de destino mestre no Azure. Ele tem dados de replicação de cache para cache baseada em disco que recebe. Ele também manipula a instalação por push do Serviço de Mobilidade que deve ser instalado em cada máquina virtual ou servidor físico que você deseja proteger e executa a descoberta automática de servidores VMWare vCenter. O servidor em processo é um servidor físico ou virtual que executa o Windows Server 2012 R2. Recomendamos que ele seja colocado na mesma rede e no mesmo segmento de LAN que os computadores que você deseja proteger, mas ele pode ser executado em uma rede diferente, contanto que os computadores protegidos tenham visibilidade de rede L3 para ele. Durante a implantação, você configurará o servidor em processo e o registrará para o servidor de configuração.

- **Cofre do Azure Site Recovery** - o cofre coordena e orquestra a réplica de dados, o failover e a recuperação entre o site local e o Azure.

- **Servidor de configuração do Azure** - o servidor de configuração coordena a comunicação entre computadores protegidos, o servidor em processo e os servidores de destino mestre no Azure. Ele configura a replicação e coordena a recuperação no Azure quando o failover ocorre. O servidor de configuração é executado em uma máquina virtual A3 do Azure padrão em sua assinatura do Azure. Durante a implantação, você configurará o servidor e o registrará no cofre do Azure Site Recovery.

- **Servidor de destino mestre** - o servidor de destino mestre no Azure mantém os dados replicados de seus computadores protegidos usando VHDs conectados criados no armazenamento de blob em sua conta de armazenamento do Azure. Você pode implantá-lo como uma máquina virtual do Azure como um servidor Windows com base em uma galeria de imagens do Windows Server 2012 R2 (para proteger os computadores do Windows) ou como um servidor Linux com base em uma galeria de imagens do OpenLogic CentOS 6.6 (para proteger os computadores do Linux). Estão disponíveis duas opções de dimensionamento: A3 padrão e D14 padrão. O servidor está conectado à mesma rede do Azure que o servidor de configuração. Durante a implantação, você criará o servidor e o registrará para o servidor de configuração.

- **Serviço de mobilidade** - você instala o Serviço de mobilidade em cada máquina virtual VMWare ou servidor físico do Windows/Linux que deseja proteger. O serviço envia dados de replicação ao servidor em processo, que, por sua vez, os envia ao servidor de destino mestre no Azure. O servidor em processo pode instalar automaticamente o Serviço de mobilidade em computadores protegidos, ou você pode implantar o serviço manualmente usando o processo de implantação de software interno.

- **Canal de replicação e comunicação de dados** - há algumas opções. Observe que nenhuma opção exige a abertura de portas de rede de entrada em computadores protegidos. Toda a comunicação de rede é iniciada do site local.
	- **Pela Internet** - comunica e replica dados de servidores locais protegidos e do Azure em uma conexão com a Internet pública segura. Essa é a opção padrão.
	- **VPN/Rota Expressa** - comunica e replica dados entre servidores locais e o Azure em uma conexão VPN. Você precisará configurar uma conexão VPN site a site ou [Rota Expressa](expressroute) entre o site local e sua rede do Azure. 

 
## Planejamento da capacidade

- Para otimizar o desempenho e aproveitar o recurso de consistência de várias VMs que recupera vários computadores protegidos para um ponto de dados consistente, recomendamos que você reúna as máquinas virtuais em grupos de proteção por carga de trabalho.
- Você não pode proteger um único computador entre vários servidores de destino mestre porque, à medida que os discos são replicados, um VHD que reflete o tamanho do disco é criado no armazenamento de blobs do Azure e conectado como um disco de dados ao servidor de destino mestre. Obviamente, você pode proteger vários computadores com um único servidor mestre de destino.
- A máquina virtual do servidor de destino mestre pode ser A4 ou D14 padrão do Azure:
	- Com um disco A4 padrão, você pode adicionar 16 discos de dados (máximo de 1023 GB por disco de dados) a cada máquina virtual.
	- Com um disco D14 padrão, você pode adicionar 32 discos de dados (máximo de 1023 GB por disco de dados) a cada máquina virtual.
- Observe que um disco conectado ao servidor de destino mestre é reservado como uma unidade de retenção. O Azure Site Recovery permite que você defina janelas de retenção e recupere computadores protegidos a qualquer momento dentro dessa janela. A unidade de retenção mantém um diário de alterações de disco pela duração da janela.  Isso reduz para 15 o máximo de discos para A4 e 31 para D14. 
- Para dimensionar sua implantação, você adiciona vários servidores em processo e servidores de destino mestre. Você deverá implantar um segundo servidor de destino mestre se não tiver discos suficientes livres em um servidor de destino mestre existente. Você deverá implantar um servidor em processo adicional se a taxa de alteração de dados de computadores protegidos exceder a capacidade de um servidor em processo existente. Observe que servidores em processo e servidores de destino mestres não exigem mapeamento. Você pode implantar o primeiro servidor em processo com o segundo servidor de destino mestre e assim por diante.
O servidor em processo usa o cache baseado em disco. Verifique se há espaço livre suficiente em C:/ para o cache. O dimensionamento de cache será afetado pela taxa de alteração de dados dos computadores que você está protegendo. Em geral, recomendamos um tamanho do diretório de cache de 600 GB para implantações de médio porte, mas você pode usar as diretrizes a seguir.

	![Cache guidelines](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerSize.png)


## Antes de começar

### Pré-requisitos do Azure

- Você precisará de uma conta do [Microsoft Azure](http://azure.microsoft.com/). Você pode começar com uma [avaliação gratuita](http://aka.ms/try-azure). 
- Você precisará de uma conta de armazenamento do Azure para armazenar os dados replicados no Azure. A conta precisa estar com a replicação geográfica habilitada. Ela deve estar localizada na mesma região que o cofre do Azure Site Recovery e ser associada à mesma assinatura. Para saber mais, leia a [Introdução ao armazenamento do Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=398704).
- Você precisará de uma rede virtual do Azure na qual o servidor de configuração e o servidor de destino mestre serão implantados. Ela deve estar na mesma assinatura e na mesma região que o cofre do Azure Site Recovery.
- Verifique se você tem recursos suficientes do Azure para implantar todos os componentes. Leia mais em [Limites da assinatura do Azure](azure-subscription-service-limits).
- Verifique se os computadores que você deseja proteger estão em conformidade com os requisitos de máquina virtual do Azure. 

	- **Contagem de discos** - um máximo de 31 discos podem ter suporte em um único servidor protegido
	- **Tamanhos de disco** - a capacidade de disco individual não deve ser superior a 1023 GB
	- **Clustering** - não há suporte a servidores clusterizados
	- **Inicialização** - não há suporte à inicialização UEFI (Unified Extensible Firmware Interface)/EFI (Extensible Firmware Interface)
	- **Volumes** - não há suporte a volumes criptografados com Bitlocker
	- **Nomes de servidor** - os nomes devem conter entre 1 e 63 caracteres (letras, números e hifens). O nome deve começar com uma letra ou número e terminar com uma letra ou número. Depois que um computador é protegido, você pode modificar o nome do Azure.	

	Leia mais sobre os requisitos do Azure no [Suporte a máquina virtual](https://msdn.microsoft.com/library/azure/dn469078.aspx#BKMK_E2A).

### Pré-requisitos de componentes de cenário

- Servidor em processo:
	- Você pode implantar o servidor em processo no computador físico ou na máquina virtual quer executa o Windows Server 2012 R2 com as últimas atualizações. Instale em C:/.
	- O servidor precisa de pelo menos oito núcleos de processador. São recomendáveis 64 GB de RAM e 300 GB de espaço livre na unidade C:.
	- Recomendamos que você coloque o servidor na mesma rede e sub-rede que os computadores que deseja proteger.
	- Instale o VMware vSphere 5.1 CLI no servidor para que ele possa executar a descoberta automática de servidores VMWare vCenter.
- O caminho de instalação para o servidor de configuração, o servidor de destino mestre, o servidor em processo e os servidores de failback deve ter somente em caracteres em inglês. Por exemplo, o caminho deve ser **/usr/local/ASR** para um servidor de destino mestre que executa o Linux.

### Pré-requisitos do VMWare

- Um servidor VMWare vCenter gerenciando seus hipervisores do VMware vSphere. Ele deve estar executando o vCenter versão 5.0 ou posterior com as últimas atualizações. 
- Um ou mais hipervisores vSphere contendo máquinas virtuais VMWare que você deseja proteger. O hipervisor deve estar executando o ESX/ESXi versão 5.0 ou posterior com as últimas atualizações.
- As máquinas virtuais VMWare descobertas por meio de um servidor do vCenter devem ter ferramentas do VMware instaladas e em execução.

### Pré-requisitos de computadores do Windows protegidos

Servidores físicos protegidos ou máquinas virtuais VMware que executam o Windows devem ter:

- Um sistema de operacional de 64 bits com suporte: Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2 com no mínimo SP1.
- O nome do host, pontos de montagem, nomes de dispositivo e o caminho de sistema do Windows (por exemplo: C:\Windows) devem ser somente em inglês.
- O sistema operacional deve ser instalado na unidade C:.
- Há suporte a opções de armazenamento regular para servidores do Windows.
- As regras de firewall em computadores protegidos devem permitir que eles acessem os servidores de configuração e de destino mestres no Azure. 
- Após o failover, se você quiser se conectar a máquinas virtuais do Windows no Azure com a Área de Trabalho Remota, verifique se ela está habilitada para o computador local. Se você não estiver se conectando por meio de uma VPN, as regras de firewall deverão permitir conexões da Área de Trabalho Remota pela Internet.

### Pré-requisitos de computadores do Linux protegidos

Servidores físicos protegidos ou máquinas virtuais VMware que executam o Linux devem ter:

- Um sistema operacional com suporte: Centos 6.4, 6.5, 6.6 (32 bits ou 64 bits); Oracle Enterprise Linux  6.4, 6.5 (32 bits ou 64 bits) executando o kernel compatível do Red Hat ou o Unbreakable Enterprise Kern Release 3 (UEK3), SUSE Linux Enterprise Server 11 SP3 (32 bits ou 64 bits)
- O nome do host, os pontos de montagem, os nomes de dispositivos e os caminhos do sistema e nomes de arquivos do Linux (por exemplo: /etc/; /usr) devem ser somente em inglês.
- A proteção pode ser habilitada para computadores locais com o seguinte armazenamento:
	- Sistema de arquivos: EXT3, ETX4, ReiserFS, XFS
	- Software multipath: Mapeador de dispositivos - multipath
	- Gerenciador de volumes: LVM2
	- Não há suporte a servidores físicos com o armazenamento de controlador HP CCISS.
- As regras de firewall em computadores protegidos devem permitir que eles acessem os servidores de configuração e de destino mestres no Azure.
- Se você quiser se conectar a uma máquina virtual do Azure que executa o Linux após o failover usando um cliente Secure Shell (ssh), verifique se o serviço do Secure Shell no computador protegido está definido para ser iniciado automaticamente na inicialização do sistema e se as regras de firewall permitem uma conexão ssh para ele.  

### Pré-requisitos de terceiros

Alguns componentes de implantação neste cenário dependem de software de terceiros para funcionar corretamente. Para obter uma lista completa, consulte <a href="#thirdparty">Avisos e informações de software de terceiros</a>. 

## Etapa 1: Criar um cofre

1. Entre no [Portal de Gerenciamento](https://portal.azure.com).


2. Expanda **Serviços de Dados** > **Serviços de Recuperação** e clique em **Cofre de Recuperação de Site**.


3. Clique em **Criar Novo** > **Criação Rápida**.
	
4. Em **Nome**, digite um nome amigável para identificar o cofre.

5. Em **Região**, selecione a região geográfica para o cofre. Para verificar as regiões com suporte, consulte a Disponibilidade Geográfica nos [Detalhes dos Preços do Azure Site Recovery](href="http://go.microsoft.com/fwlink/?LinkId=389880)

6. Clique em **Criar cofre**. 

	![New vault](./media/site-recovery-vmware-to-azure/ASRVMWare_CreateVault.png)

Verifique a barra de status para confirmar que o cofre foi criado com sucesso. O cofre será listado como **Ativo** na página principal de **Serviços de Recuperação**.

## Etapa 2: Implantar um servidor de configuração


1. Na página **Serviços de Recuperação**, clique no cofre para abrir a página de Início Rápido. O Início Rápido pode também ser aberto a qualquer tempo usando o ícone.

	![Quick Start Icon](./media/site-recovery-vmware-to-azure/ASRVMWare_QuickStartIcon.png)

2. Na lista suspensa, selecione **Entre um site local com servidores VMware/físicos e o Azure**.
3. Em **Preparar Recursos de Destino**, clique em **Implantar o Servidor de Configuração**.

	![Deploy configuration server](./media/site-recovery-vmware-to-azure/ASRVMWare_DeployCS.png)

4. Especifique os detalhes e as credenciais do servidor de configuração para se conectar a ele. Selecione a rede do Azure na qual o servidor deve estar localizado. Especifique o endereço IP interno e a sub-rede para atribuir ao servidor. Quando você clicar em **OK**, uma máquina virtual A3 padrão com base em uma galeria de imagens do Windows Server 2012 R2 do Azure Site Recovery será criada em sua assinatura para o servidor de configuração. Ela é criada como a primeira instância em um novo serviço de nuvem com um endereço IP público reservado.

	![Configuration server settings](./media/site-recovery-vmware-to-azure/ASRVMWare_CSDetails.png)

5. Você pode monitorar o progresso na guia **Trabalhos**.

	![Monitor progress](./media/site-recovery-vmware-to-azure/ASRVMWare_MonitorConfigServer.png)

8.  Depois que o servidor de configuração for implantado, anote o endereço IP público atribuído a ele na página **Máquinas Virtuais** no portal do Azure. Em seguida, na guia **Pontos de Extremidade**, anote a porta pública HTTPS mapeada para a porta privada 443. Você precisará dessas informações posteriormente quando registrar is servidores de destino mestre e em processo no servidor de configuração. O servidor de configuração é implantado com quatro pontos de extremidade públicos:

	- 443: Canal HTTPS usado para coordenar a comunicação entre servidores de componentes e o Azure.
	- 9443: Usado para a ferramenta de failback e a comunicação de failback.
	- Área de Trabalho Remota
	- PowerShell


## Etapa 3: Registrar o servidor de configuração no cofre

1. Em **Preparar Recursos de Destino**, clique em **Baixar uma chave de registro**. O arquivo de chave é gerado automaticamente. Ele é válido por cinco dias após ter sido gerado. Copie o arquivo para o servidor de configuração.
2. Na página **Painel** da máquina virtual, clique em **Conectar**. Use o arquivo RDP baixado para fazer logon no servidor de configuração com a Área de Trabalho Remota.  Quando você faz logon pela primeira vez, o Assistente de Instalação e Registro do Azure Site Recovery é executado automaticamente.

	![Registration](./media/site-recovery-vmware-to-azure/ASRVMWareRegister1.png)

3. Siga as instruções do assistente. Você precisará baixar e instalar o MySQL Server e especificar credenciais para ele. Na página **Registro do Azure Site Recovery**, navegue até o arquivo de chave que você copiou para o servidor.

	![Registration key](./media/site-recovery-vmware-to-azure/ASRVMWareRegister2.png)

4. Após a conclusão do registro, uma senha é gerada. Copie-a para um local seguro. Você precisará dela para autenticar e registrar os servidores de processo e de destino mestre no servidor de configuração. Ela também é usada para garantir a integridade do canal nas comunicações do servidor de configuração. É possível gerar novamente a senha, mas, em seguida, você precisará registrar novamente os servidores de destino mestre e de processo usando a nova senha.

	![Passphrase](./media/site-recovery-vmware-to-azure/ASRVMWareRegister2.png)

Após o registro, o servidor de configuração será listado na página **Servidores de Configuração** no cofre.

## Etapa 4: Configurar uma conexão VPN
 
Você pode se conectar ao servidor de configuração pela Internet ou por meio de uma conexão VPN ou Rota Expressa. Uma conexão com a Internet usa os pontos de extremidade da máquina virtual em conjunto com o endereço IP virtual público do servidor. A VPN usa o endereço IP interno do servidor junto com as portas privadas do ponto de extremidade.
 
Você pode configurar uma conexão VPN com o servidor da seguinte maneira:

1. Se não tiver uma conexão site a site ou de Rota Expressa do Azure configurada, você poderá saber mais aqui:

	- [Rota Expressa ou VPN: o que é adequado para mim](http://azure.microsoft.com/blog/2014/06/10/expressroute-or-virtual-network-vpn-whats-right-for-me/) 
	- [Configurar uma conexão site a site para uma máquina virtual do Azure](https://msdn.microsoft.com/library/azure/dn133795.aspx)
	- [Configurar a Rota Expressa](https://msdn.microsoft.com/library/azure/dn606306.aspx) 
	
2. No cofre, clique em **Servidores** > **Servidores de Configuração** > servidor de configuração > **Configurar**.
3. Em **Configurações de Conectividade**, defina **Tipo de Conectividade** como **VPN**. Observe que, se você tiver configurado a VPN e não tiver acesso à Internet por meio do site local, selecione a opção VPN. Se você não fizer isso, o servidor em processo não poderá enviar dados de replicação ao servidor de destino mestre em seus pontos de extremidade públicos.

	![Enable VPN](./media/site-recovery-vmware-to-azure/ASRVMWare_EnableVPN.png)

## Etapa 5: Implantar o servidor de destino mestre

1. Em **Preparar Recursos de Destino**, clique em **Implantar servidor de destino mestre**.
2. Especifique os detalhes e as credenciais do servidor de destino mestre. O servidor será implantado na mesma rede que o Azure como o servidor de configuração para o qual você o registrar. Quando você clicar em Concluir, uma máquina virtual do Azure será criada com uma imagem de galeria do Windows ou Linux. 

	![Target server settings](./media/site-recovery-vmware-to-azure/ASRVMWare_TSDetails.png)

3. Uma máquina virtual de servidor mestre do Windows é criada com estes pontos de extremidade TCP públicos:

	- Personalizado: A porta pública é usada para enviar dados de replicação pela Internet. A porta privada 9443 é usada pelo servidor em processo para enviar dados ao servidor de destino mestre pela VPN.
	- Custom1: A porta privada 9080 é usada pelo servidor em processo para enviar dados ao servidor de destino pela VPN.
	- PowerShell Porta privada: 5986
	- Área de trabalho remota: Porta privada: 3389

4. Uma máquina virtual de servidor mestre do Linux é criada com estes pontos de extremidade:

	- Personalizado: A porta pública é usada para enviar dados de replicação pela Internet. A porta privada 9443 é usada pelo servidor em processo para enviar dados ao servidor de destino mestre pela VPN.
	- Custom1: A porta privada 9080 é usada pelo servidor em processo para enviar dados ao servidor de destino mestre pela VPN
	- SSH: Porta privada 22

5. Em **Máquinas virtuais**, aguarde até que a máquina virtual seja iniciada. 

	- Se você tiver configurado o servidor com o Windows, anote os detalhes da área de trabalho remota.
	- Se você tiver configurado com o Linux e estiver se conectando por meio da VPN, anote o endereço IP interno da máquina virtual. Se você estiver se conectando pela Internet, anote o endereço IP público.
6.  Faça logon no servidor para concluir a instalação e registrá-lo no servidor de configuração. Se você estiver executando o Windows:

	1. Inicie uma conexão da área de trabalho remota para a máquina virtual. Na primeira vez que você fizer logon, um script será executado em uma janela do PowerShell. Não o feche. Quando ele for concluído, a ferramenta de Configuração do Agente de Host será aberta automaticamente para registrar o servidor.
	2. Em **Configuração do Agente de Host**, especifique o endereço IP interno do servidor de configuração e a porta 443. Você pode usar o endereço interno e a porta privada 443, mesmo que não esteja se conectando por meio do modo VPN, porque a máquina virtual está conectada à mesma rede do Azure que o servidor de configuração. Deixe **Usar HTTPS** habilitado. Insira a senha do servidor de configuração que você anotou anteriormente. Clique em **OK** para registrar o servidor. Observe que você pode ignorar as opções de NAT na página. Elas não são usadas.

	![Windows master target server](./media/site-recovery-vmware-to-azure/ASRVMWare_TSRegister.png)

6. Se você estiver executando o Linux:
	1. Copie o [arquivo tar de instalador de servidor](http://go.microsoft.com/fwlink/?LinkID=529757&clcid=0x409) para a máquina virtual usando um cliente de sftp. Como alternativa, você pode fazer logon e usar wget para baixar o arquivo do link na página de Início Rápido. 
	2. Faça logon no servidor usando um cliente do Secure Shell. Observe que, se você estiver conectado à rede do Azure pela VPN, use o endereço IP interno. Caso contrário, use o endereço IP externo e o ponto de extremidade público do SSH.
	3. Extraia os arquivos do instalador compactados com gzip executando: **tar -xvzf Microsoft-ASR_UA_8.2.0.0_RHEL6-64***."   

		![Linux master target server](./media/site-recovery-vmware-to-azure/ASRVMWare_TSLinuxTar.png)

	4. Verifique se você está no diretório no qual extraiu o conteúdo do arquivo tar e execute o comando "**sudo ./install.sh**". Selecione a opção **2. de Destino Mestre**. Deixe as outras opções com as configurações padrão.

		![Register target server](./media/site-recovery-vmware-to-azure/ASRVMWare_TSLinux.png)

	5. Após a conclusão da instalação, a linha de comando **Interface de Configuração de Host** será exibida. Não redimensione a janela. 
	6. Use as teclas de direção para selecionar **Global** e pressione Enter.
	7. Em **Digite o endereço IP**, digite o endereço interno do servidor de configuração e a porta 22.
	8.  Especifique a senha do servidor de configuração que você anotou anteriormente e pressione Enter. Selecione **Sair** para concluir a instalação. Observe que, se estiver usando o cliente PuTTY para executar ssh para a máquina virtual, você poderá usar Shift+Insert para colar. 
	9.  Use a tecla de seta para a direita para sair e pressione Enter.

		![Master target server settings](./media/site-recovery-vmware-to-azure/ASRVMWare_TSLinux2.png)

7. Espere por alguns minutos (5-10) e, na página **Servidores** > **Servidores de Configuração**, verifique se o servidor de destino mestre está listado como registrado na guia **Detalhes do Servidor**. Se você estiver executando o Linux e ele não tiver sido registrado, execute a ferramenta de configuração de host novamente em /usr/local/ASR/Vx/bin/hostconfigcli. Você precisará definir permissões de acesso executando chmod como superusuário.

	![Verify target server](./media/site-recovery-vmware-to-azure/ASRVMWare_TSList.png)

## Etapa 6: Implantar o servidor em processo local

1. Clique em Início Rápido > **Instalar Servidor em Processo Local** > **Baixe e instale o servidor em processo**.

	![Install process server](./media/site-recovery-vmware-to-azure/ASRVMWare_PSDeploy.png)

2. Copie o arquivo zip baixado para o servidor no qual você vai instalar o servidor em processo. O arquivo zip contém dois arquivos de instalação:

	- Microsoft-ASR_CX_TP_8.2.0.0_Windows*
	- Microsoft-ASR_CX_8.2.0.0_Windows*

3. Descompacte o arquivo morto e copie os arquivos de instalação para um local no servidor.
4. Execute o arquivo de instalação **Microsoft-ASR_CX_TP_8.2.0.0_Windows*** e siga as instruções. Isso instala os componentes de terceiros necessários para a implantação.
5. Em seguida, execute **Microsoft-ASR_CX_8.2.0.0_Windows***.
6. Na página **Modo de Servidor**, selecione **Servidor em Processo**.
7.	Em **Detalhes do Servidor de Configuração**, se você estiver se conectando pela VPN, especifique o endereço IP interno do servidor de configuração e 443 para a porta. Caso contrário, especifique o endereço IP virtual público e o ponto de extremidade HTTP público mapeado.
8.	Desmarque **Verificar assinatura de software do Serviço de mobilidade** se desejar desabilitar a verificação ao usar o envio automático para instalar o serviço. A verificação de assinatura precisa de conectividade com a Internet do servidor em processo.
9.	Digite a senha do servidor de configuração.

	![Register configuration server](./media/site-recovery-vmware-to-azure/ASRVMWare_CSRegister.png)

8. Conclua a instalação do servidor. Lembre-se de que você precisará instalar o VMware vSphere CLI 5.1 no servidor para descobrir Servidores vCenter.

	![Register process server](./media/site-recovery-vmware-to-azure/ASRVMWare_PSRegister2.png)

Valide se o servidor em processo foi registrado com êxito no cofre > **Servidor de Configuração** > **Detalhes do Servidor**.

![Validate process server](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerRegister.png)

Observe que, se não desabilitar a verificação de assinatura para o Serviço de mobilidade ao registrar o servidor em processo, você poderá fazer isso mais tarde, da seguinte maneira:

1. Faça logon no servidor em processo como administrador e abra o arquivo C:\pushinstallsvc\pushinstaller.conf para edição. Na seção **[PushInstaller.transport]**, adicione esta linha: **SignatureVerificationChecks="0"**. Salve e feche o arquivo.
1. Reinicie o serviço InMage PushInstall.


## Etapa 7: Adicionar servidores vCenter

1. Na guia **Servidores** > **Servidores de Configuração**, selecione o servidor de configuração e clique para adicionar um servidor vCenter.

	![Select vCenter server](./media/site-recovery-vmware-to-azure/ASRVMWare_AddVCenter.png)

2. Especifique os detalhes para o servidor vCenter e selecione o servidor em processo que será usado para descobri-lo.  O servidor em processo deve estar na mesma rede que o servidor vCenter e deve ter o VMware vSphere CLI 5.1 instalado.
3. Após a conclusão da descoberta, o servidor vCenter será listado nos detalhes do servidor de configuração.
	
	![vCenter server settings](./media/site-recovery-vmware-to-azure/ASRVMWare_AddVCenter2.png)


## Etapa 8: Criar um grupo de proteção

1. Abra **Itens Protegidos** > **Grupo de Proteção** e clique para adicionar um grupo de proteção.

	![Create protection group](./media/site-recovery-vmware-to-azure/ASRVMWare_CreatePG1.png)

2. Na página **Especificar Configurações do Grupo de Proteção**, especifique um nome para o grupo e selecione o servidor de configuração no qual você deseja criar o grupo.

	![Protection group settings](./media/site-recovery-vmware-to-azure/ASRVMWare_CreatePG2.png)

3. Na página **Especificar Configurações de Replicação**, defina as configurações de replicação que serão usadas para todos os computadores do grupo.   

	![Protection group replication](./media/site-recovery-vmware-to-azure/ASRVMWare_CreatePG3.png)

4. Configurações:
	- **Consistência de várias máquinas virtuais**: Se você ativar essa configuração, ela criará pontos de recuperação consistentes com aplicativos nos computadores do grupo de proteção. Essa configuração é mais relevante quando todos os computadores no grupo de proteção estão executando a mesma carga de trabalho. Todos os computadores serão recuperados para o mesmo ponto de dados. Disponível somente para servidores do Windows. 
	- **Limite RPO**: Alertas serão gerados quando o RPO de replicação de proteção de dados contínuos excede o valor do limite de RPO configurado.
	- **Retenção de ponto de recuperação**: Especifica a janela de retenção. Computadores protegidos podem ser recuperados para qualquer ponto nessa janela.
	- **Frequência de instantâneos consistentes com aplicativos**: Especifica com que frequência os pontos de recuperação contendo instantâneos consistentes com aplicativos serão criados.

Você pode monitorar o grupo de proteção conforme eles são criados na página **Itens Protegidos**. 

## Etapa 9: Enviar por push o Serviço de mobilidade

Quando você adiciona computadores a um grupo de proteção, o Serviço de mobilidade é enviado automaticamente e instalado em cada computador pelo servidor em processo. Se desejar usar esse mecanismo de envio automático para computadores protegidos que executam o Windows, você precisará fazer o seguinte em cada computador:

1. Configure o firewall do Windows para permitir **Compartilhamento de Arquivo e Impressora** e **Instrumentação de Gerenciamento do Windows**. Para computadores que pertencem a um domínio, você pode configurar a política de firewall com um GPO.
2. A conta usada para executar a instalação por push deve estar do grupo Administradores no computador que você deseja proteger. Observe que essas credenciais são usadas apenas para a instalação por push. Elas não são armazenadas em lugar nenhum pelo Serviço de mobilidade e são descartadas depois que o servidor está protegido. Ao adicionar um computador a um grupo de proteção, você fornecerá essas credenciais.

	![Mobility credentials](./media/site-recovery-vmware-to-azure/ASRVMWare_PushCredentials.png)

3. Se a conta de administrador não for uma conta de domínio, você precisará desabilitar o controle Acesso de Usuário Remoto no computador local. Para fazer isso em HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System, crie a entrada LocalAccountTokenFilterPolicy se ela não existir e atribua a ela um valor DWORD de 

Se desejar proteger computadores que executam o Linux, você precisará fazer o seguinte:

1. Verifique se a conta é um usuário raiz no servidor Linux de origem
1. Instale os últimos pacotes openssh, openssh-server e openssl no computador que você deseja proteger.
1. Habilite a porta ssh 22.
2. Habilite a autenticação de subsistema e senha Sftp no arquivo de configuração sshd:
	1. Faça logon na conta de usuário raiz.
	2. No arquivo /etc/ssh/sshd_config, localize a linha que começa com "PasswordAuthentication". Remova a marca de comentário da linha e altere o valor de "no" para "yes". 

		![Linux mobility](./media/site-recovery-vmware-to-azure/ASRVMWare_LinuxPushMobility1.png)

	4. Localize a linha que começa com "Subsystem" e remova a marca de comentário da linha. 

		![Linux push mobility](./media/site-recovery-vmware-to-azure/ASRVMWare_LinuxPushMobility1.png)

## Etapa 10: Adicionar computadores a um grupo de proteção

1. Abra **Itens Protegidos** > **Grupo de Proteção** > guia **Computadores** e adicione computadores físicos ou virtuais gerenciados por um servidor vCenter descoberto. É recomendável que os grupos de proteção espelhem suas cargas de trabalho para que você adicione computadores que executam um aplicativo específico ao mesmo grupo.

	![Add machines](./media/site-recovery-vmware-to-azure/ASRVMWare_PushCredentials.png)

2. Na página **Selecionar Máquina Virtual** de **Adicionar Máquina Virtual**, selecione um servidor V-Center e selecione computadores nele.

	![Add V-Center server](./media/site-recovery-vmware-to-azure/ASRVMWare_SelectVMs.png)

3. Quando você adiciona computadores a um grupo de proteção, o Serviço de mobilidade é instalado automaticamente por meio do servidor em processo local. Para que o mecanismo de envio automático funcione, verifique se você configurou seus computadores protegidos conforme descrito na etapa anterior.
4. Em **selecionar Máquinas Virtuais**, selecione o servidor vCenter que gerencia os computadores que você deseja proteger e, em seguida, selecione as máquinas virtuais.

4. Selecione os servidores e o armazenamento a serem usados para replicação. 

	![vCenter server](./media/site-recovery-vmware-to-azure/ASRVMWare_MachinesResources.png)

5. Forneça as credenciais de usuário para o servidor de origem. Isso é necessário para instalar o Serviço de mobilidade automaticamente nos computadores de origem. Para o servidor do Windows, a conta deve ter privilégios de administrador no servidor de origem. Para o Linux, a conta deve ter privilégios de superusuário no servidor.

	![Linux credentials](./media/site-recovery-vmware-to-azure/ASRVMWare_VMMobilityInstall.png)

6. Clique na marca de seleção para terminar de adicionar computadores ao grupo de proteção e iniciar a replicação inicial para cada computador. Você pode monitorar o status na página **Trabalhos**.

	![Add V-Center server](./media/site-recovery-vmware-to-azure/ASRVMWare_PGJobs.png)

7. Além disso, clique em **Itens Protegidos** > <grupo de proteção> > **Máquinas Virtuais** para monitorar o status de proteção. Depois que a replicação inicial for concluída e os computadores estiverem sincronizando dados, eles mostrarão o status **Protegido**.

	![Virtual machine jobs](./media/site-recovery-vmware-to-azure/ASRVMWare_PGJobs.png)

## Etapa 11: Definir propriedades de computador protegido

1. Depois que um computador tiver o status **Protegido**, você poderá configurar suas propriedades de failover. Nos detalhes do grupo de proteção, selecione o computador e abra a guia **Configurar**.
2. Você pode modificar o nome que será dado ao computador no Azure depois do failover e o tamanho do Azure. Você também pode selecionar a rede do Azure à qual o computador será conectado após o failover. Observe que:

	- O nome do computador do Azure deve atender aos requisitos do Azure descritos nos pré-requisitos.
	- Por padrão, as máquinas virtuais replicadas no Azure não são conectadas a uma rede do Azure. Se você quiser que as máquinas virtuais replicadas se comuniquem, defina a mesma rede do Azure para elas.

	![Set virtual machine properties](./media/site-recovery-vmware-to-azure/ASRVMWare_VMProperties.png)

## Etapa 12: Executar um failover

1. Na página **Planos de Recuperação**, adicione um plano de recuperação. Especifique os detalhes do plano e selecione **Azure** como destino.

	![Configure recovery plan](./media/site-recovery-vmware-to-azure/ASRVMWare_RP1.png)

2. Em **Selecionar Máquina Virtual**, selecione um grupo de proteção e selecione computadores do grupo para adicionar ao plano de recuperação.

	![Add virtual machines](./media/site-recovery-vmware-to-azure/ASRVMWare_RP2.png)

3. Se necessário, você pode personalizar o plano para criar grupos e sequenciar a ordem em que os computadores no plano de recuperação são submetidos a failover. Você também pode adicionar prompts para ações manuais e scripts.

	![Customize recovery plan](./media/site-recovery-vmware-to-azure/ASRVMWare_RP2.png)

5. Na página **Planos de Recuperação**, selecione o plano e clique em **Failover de Teste**.
6. Em **Confirmar Failover**, verifique a direção do failover (para o Azure) e selecione o ponto de recuperação para o failover. 
7. Aguarde até que o trabalho de failover seja concluído e, em seguida, verifique se o failover funcionou conforme o esperado e se as máquinas virtuais replicadas são iniciadas com êxito no Azure.

<a name="thirdparty"></a><h2>AVISOS E INFORMAÇÕES DE SOFTWARES DE TERCEIROS</h2>

Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, "Third Party Code").  Microsoft is the not original author of the Third Party Code.  The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only.  This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.  

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms. 

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=530254)  h. Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.


<!--HONumber=49--> 