<properties 
	pageTitle="Tutorial: Configurar a proteção entre sites do VMM locais com SAN" 
	description="O Azure Site Recovery coordena a replicação, o failover e a recuperação de máquinas virtuais Hyper-V entre os sites do VMM locais usando a replicação SAN." 
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
	ms.date="02/18/2015" 
	ms.author="raynew"/>

# Tutorial: Configurar a proteção entre sites do VMM locais com SAN



<h2><a id="overview" name="overview" href="#overview"></a>Visão geral</h2>
<p>A recuperação de Site do Azure colabora com sua estratégia de continuidade de negócios e carga de trabalho ao gerenciar replicação, failover e recuperação de máquinas virtuais em vários cenários de implantação.<p>

<P>Este tutorial descreve como implantar o Azure Site Recovery para orquestrar e automatizar a proteção para cargas de trabalho em execução em um servidor Hyper-V gerenciado pelo System Center VMM em um site local para outro site local do VMM, usando a replicação do armazenamento com base na matriz (SAN). O tutorial usa o caminho de implantação e as configurações padrões mais rápidas, sempre que possível. Neste cenário:</P>

<ul>
	<li>Você aproveita sua infraestrutura de SAN para proteger aplicativos de missão crítica implantados em clusters Hyper-V.</li>
	<li>Replicação de SAN oferece suporte para clusters de convidado e garante a consistência de replicação em diferentes camadas de um aplicativo com a replicação assíncrona e síncrona, dependendo da capacidade da matriz de armazenamento.</li>
	<li>Configure e habilite a replicação no VMM e o cofre da recuperação de Site do Azure. Você irá detectar e classificar o armazenamento de SAN no VMM, provisionar LUN e alocar armazenamento para clusters Hyper-V. A Recuperação de Site do Azure automatiza a replicação e coordena o failover. </li>
	</ul>

<UL>
<LI>Você pode ler instruções de implantação completa dos guias de <a href="http://go.microsoft.com/fwlink/?LinkId=321294">Planejamento</a> e <a href="http://go.microsoft.com/fwlink/?LinkId=519251">Implantação</a>.</LI>
<LI>Você pode ler sobre cenários adicionais de implantação de recuperação de Site do Azure em <a href="http://go.microsoft.com/fwlink/?LinkId=518690">Visão geral de recuperação do site do Azure</a>.</LI>
<LI>Se você enfrentar problemas durante este tutorial, reveja o artigo wiki de <a href="http://go.microsoft.com/fwlink/?LinkId=389879">Recuperação de Site do Azure: Cenários comuns de erro e resoluções</a> ou poste suas perguntas no <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Fórum dos Serviços de Recuperação do Azure</a>.</LI>
</UL>




<h2><a id="before" name="before" href="#before"></a>antes</h2>
<div class="dev-callout"> 
<P>Verifique se tudo está certo antes de iniciar o passo a passo.</P>

<UL>
<LI><b>Conta do Azure</b>-Você precisará de uma conta do Azure. Se você não tem uma, consulte <a href="http://aka.ms/try-azure">Avaliação gratuita do Azure</a>. Obter informações de preço em <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Detalhes de Preços do Gerenciador do Azure Site Recovery</a>.</LI>
<LI>Se você quiser saber quais informações são coletadas, processadas ou transmitidas durante as operações do Azure Site Recovery, leia <a href="http://go.microsoft.com/fwlink/?LinkId=513016">Requisitos de privacidade</a> no MSDN.</LI>
<LI><b>Servidor do VMM</b>- Você precisará de um servidor VMM em cada site local, implantado como um servidor físico ou virtual autônomo ou como um cluster virtual, em execução no System Center 2012 R2 com a <a href="http://go.microsoft.com/fwlink/?LinkId=517707">visualização do pacote cumulativo (Rollup) de atualização 5.0 do VMM</a>.</LI>
<LI>Você precisará de um cluster de host do Hyper-V implantado nos sites primários e secundários, executando pelo menos o Windows Server 2012 com as atualizações mais recentes.</LI>
<LI><b>Nuvens do VMM</b>- Você deve ter pelo menos uma nuvem no servidor VMM primário que você deseja proteger e outra no servidor do VMM secundário. A nuvem principal que você deseja proteger deve conter o seguinte:<UL>
	<LI>Um ou mais grupos de hosts do VMM</LI>
	<LI>Um ou mais clusters de Hyper-V em cada grupo de hosts.</LI>
	<li>Um ou mais máquinas virtuais localizadas no servidor Hyper-V de origem na nuvem.</li>
		</UL>Para saber mais sobre como configurar nuvens do VMM, consulte <a href="http://go.microsoft.com/fwlink/?LinkId=513015">Planejamento da infraestrutura VMM</a> na guia de planejamento.</LI>
<LI><b>Armazenamento SAN</b>- O uso de replicação de SAN pode replicar as máquinas virtuais de cluster convidado com o armazenamento iSCSI ou fibre channel ou usando discos rígidos virtuais compartilhados (vhdx). Os pré-requisitos de SAN são os seguintes:</LI>
	<UL>
	<LI>Você precisará de duas matrizes de SAN, configuradas, uma no site primário e um secundário.</LI>
	<LI>A infraestrutura de rede deve ser configurada entre as matrizes. Devem ser configurados o emparelhamento e a replicação. Licenças de replicação devem ser configuradas de acordo com os requisitos de matriz de armazenamento.</LI>
	<LI>A rede deve ser definida entre os servidores de host Hyper-V e a matriz de armazenamento para que os hosts possam se comunicar com os LUNs de armazenamento usando ISCSI ou Fibre Channel.</LI>
	<LI>Consulte uma lista de matrizes de armazenamento em <a href="http://go.microsoft.com/fwlink/?LinkId=518669">Implantação do Azure Site Recovery com o VMM e SAN - matrizes de armazenamento suportadas</a>.</LI>
	
	<LI>Provedores de SMI-S, fornecido pelo EMC e NetApp devem ser instalados e as matrizes de SAN devem ser gerenciadas pelo provedor. Configure o provedor de acordo com sua documentação.</LI>
	<LI>Verifique se o provedor de SMI-S para a matriz está em um servidor que o servidor do VMM pode acessar na rede pelo endereço IP ou FQDN.</LI>
	<LI>Cada matriz SAN deve ter um ou mais pools de armazenamento disponíveis para uso nessa implantação.</LI>
	<LI>O servidor do VMM no local principal será necessário para gerenciar a matriz primária e o servidor secundário do VMM gerenciará a matriz secundária.</LI>
	</UL>
<LI><b>Redes</b>Você também pode definir o mapeamento de rede para assegurar que as máquinas virtuais de réplica são colocadas idealmente em servidores host Hyper-V após o failover, e que elas possam se conectar às redes VM apropriadas. Quando o mapeamento de rede está habilitado, uma máquina virtual no local principal será conectada a uma rede e sua réplica no local de destino será conectada à rede mapeada. Se você não configurar o mapeamento de rede, as máquinas virtuais não serão conectadas às redes VM após o failover. Este tutorial descreve as configurações de instruções passo a passo mais simples e não inclui o mapeamento de rede, mas você pode ler mais em:</LI>
	<UL>
	<LI><a href="http://go.microsoft.com/fwlink/?LinkId=522289">Mapeamento de rede</a> no guia de planejamento.</LI>
	<LI><a href="http://go.microsoft.com/fwlink/?LinkId=522290">Habilitar o mapeamento de rede</a> no guia de implantação de SAN.</LI>
	</UL>
</UL>
</div>

<h2><a id="tutorial" name="tutorial" href="#tutorial"></a>Etapas do tutorial</h2>
Depois de verificar os pré-requisitos, faça o seguinte:

1. <a href="#VMM">Etapa 1: Preparar a infraestrutura do VMM</a>
	- Integre e classifique o armazenamento de SAN no VMM
	- Provisione unidades lógicas (LUNs) e pools de armazenamento e aloque armazenamento para clusters Hyper-V.
	- Crie grupos de replicação para LUNs que devem replicar juntos. 
2. <a href="#vault">Etapa 2: Criar um cofre</a>
3. <a href="#download">Etapa 3: Instale o Provedor em servidores do VMM</a>- Gere uma chave de registro no cofre e baixe o arquivo de configuração do Provedor. Execute a instalação do servidor VMM para instalar o Provedor e registrar o servidor no cofre.
4. <a href="#storage">Etapa 4: Mapear matrizes de armazenamento e pools</a>- Mapeie matrizes para especificar qual pool de armazenamento secundário recebe dados de replicação do pool principal. Mapeie o armazenamento antes de configurar a proteção de nuvem, pois as informações de mapeamento são usadas quando você habilita a proteção para grupos de replicação.
5. <a href="#clouds">Etapa 5: Configurar a proteção de nuvem</a>- Configure a proteção para nuvens do VMM.
7. <a href="#replication">Etapa 6: Habilitar grupos de replicação</a>-Antes de habilitar a proteção para máquinas virtuais, você precisará habilitar a replicação para grupos de replicação de armazenamento.
8. <a href="#enablevirtual">Etapa 7: Habilitar a proteção para máquinas virtuais</a>- Depois que os grupos de replicação estiverem replicando, habilite a proteção para máquinas virtuais.
9. <a href="#recovery plans">Etapa 8: Testar a implantação</a>- Você pode testar se as máquinas virtuais e os dados fazem failover como esperado executando um teste de failover. 


<a name="VMM"></a> <h3>Etapa 1: Preparar a infraestrutura do VMM</h3>

<a name="SAN"></a> <h4>Integre e classifique o armazenamento de SAN no VMM</h4>


1. Na área de trabalho **Malha** clique em **Armazenamento**. Clique em **Home** > **Adicionar Recursos** > **Dispositivos de Armazenamento** para iniciar o Assistente para Adicionar Dispositivos de Armazenamento.
2. Em **Selecionar um tipo de provedor de armazenamento**, selecione **dispositivos SAN e NAS descobertos e gerenciados por um provedor de SMI-S**.

	![Provider type](./media/hyper-v-recovery-manager-configure-vault/SRSAN_Providertype.png)

3. Na página **Especificar o protocolo e o endereço do armazenamento do provedor de SMI-S** selecione **SMI-S CIMXML** e especifique as configurações para conectar-se ao provedor.
4. No **Endereço IP do Provedor ou FQDN** e **porta TCP/IP**, especifique as configurações para se conectar ao provedor. Você pode usar uma conexão SSL apenas para CIMXML de SMI-S.

	![Provider connect](./media/hyper-v-recovery-manager-configure-vault/SRSAN_ConnectSettings.png)

5. Em **Executar como conta** especificar uma VMM executar como conta que possas acessar o provedor ou criar uma nova conta.
6. Na página **Reunir Informações**, o VMM tenta automaticamente detectar e importar as informações de dispositivo de armazenamento. Para repetir a descoberta, clique em **Examinar Provedor**. Se o processo de descoberta for bem-sucedido, as matrizes de armazenamento, pools de armazenamento, fabricante, modelo e capacidade descobertos serão listados na página.

	![Discover storage](./media/hyper-v-recovery-manager-configure-vault/SRSAN_Discover.png)

7. Em **Selecionar pools de armazenamento para colocar sob gerenciamento e atribuir uma classificação**, selecione os pools de armazenamento que o VMM gerenciará e atribua-os uma classificação. As informações de LUN serão importadas dos pools de armazenamento. Crie LUNs de acordo com os aplicativos que você precisa proteger, seus requisitos de capacidade e o requisito para os que precisam replicar juntos.

	![Classify storage](./media/hyper-v-recovery-manager-configure-vault/SRSAN_Classify.png)

<a name="LUNs"></a> <h4>Criar LUNs e alocar armazenamento</h4>


1. Depois que o armazenamento SAN é integrado ao VMM, você vai criar LUN (provisão). Para obter mais informações, consulte:
	
	- <a href="http://go.microsoft.com/fwlink/?LinkId=518490">Como selecionar um método para criar unidades lógicas no VMM</a>
	- <a href="http://go.microsoft.com/fwlink/?LinkId=518491">Como provisionar armazenamento de unidades lógicas no VMM</a>
2. Aloque a capacidade de armazenamento ao cluster de host Hyper-V para que o VMM possa implantar dados da máquina virtual para o armazenamento provisionado: 
	- Antes de alocar o armazenamento ao cluster, você precisará alocar armazenamento para o grupo de hosts do VMM onde o cluster reside. Consulte <a href="http://go.microsoft.com/fwlink/?LinkId=518493">Como alocar unidades lógicas de armazenamento para um grupo de hosts</a> e <a href="http://go.microsoft.com/fwlink/?LinkId=518492">Como alocar pools de armazenamento a um grupo de hosts</a>.
	- Aloque a capacidade de armazenamento para o cluster conforme descrito em <a href="http://go.microsoft.com/fwlink/?LinkId=513017">Como configurar o armazenamento em um cluster de host Hyper-V no VMM</a>.
	

<a name="RGs"></a> <h4>Criar grupos de replicação</h4>

1. Crie um grupo de replicação que inclui todos os LUNs que precisarão ser replicados juntos.
2. No console do VMM, abra a guia **Grupos de Replicação** de propriedades de matriz de armazenamento e clique em **Novo**.
	![SAN replication group](./media/hyper-v-recovery-manager-configure-vault/SRSAN_RepGroup.png)



<a name="vault"></a> <h3>Etapa 2: Criar um cofre</h3>
Depois de validar os pré-requisitos de implantação, crie um cofre da recuperação de Site do Azure. Como alternativa, você pode usar um cofre existente e configurar a replicação de SAN.

1. Entre no [Portal de Gerenciamento](https://manage.windowsazure.com).


2. Expanda os <b>Serviços de Dados</b>, expanda os <b>Serviços de Recuperação</b> e clique em <b>Cofre de Recuperação de Site</b>.


3. Clique em <b>Criar Novo</b> e, em seguida, clique em <b>Criação Rápida</b>.
	
4. Em <b>Nome</b>, digite um nome amigável para identificar o cofre.

5. Em <b>Região</b>, selecione a região geográfica para o cofre. Para verificar as regiões com suporte, consulte a Disponibilidade Geográfica nos <a href="http://go.microsoft.com/fwlink/?LinkId=389880">Detalhes dos Preços do Azure Site Recovery.</a>

6. Clique em <b>Criar cofre</b>. 

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SRSAN_HvVault.png)

<P>Verifique a barra de status para confirmar que o cofre foi criado com sucesso. O cofre será listado como <b>Ativo</b> na página de Serviços de Recuperação.</P>

<a name="upload"></a> <h2>Etapa 3: Configurar o cofre</h3>


1. Na página <b>Serviços de Recuperação</b>, clique no cofre para abrir a página de Início Rápido. O Início Rápido pode também ser aberto a qualquer tempo usando o ícone.

	![Quick Start Icon](./media/hyper-v-recovery-manager-configure-vault/SRSAN_QuickStartIcon.png)

2. Na lista suspensa, selecione **Entre o Hyper-V usando a replicação de matriz de site local**.


<a name="download"></a> <h3>Etapa 3: Instalar o Provedor de Recuperação do Site do Azure</h3>
Depois de criar o cofre, gere um arquivo de registro que inclui uma chave de registro. Selecione esse arquivo ao instalar o Provedor de Recuperação de Site do Azure. 

1. Na página <b>Início Rápido</b>, em **Preparar servidores VMM**, clique no arquivo **Gerar chave de registro**. A chave é válida para 5 dias após ser gerada. Você deve baixar o arquivo em um local seguro onde os servidores VMM possam acessá-los. Por exemplo, um compartilhamento. Você precisará selecionar esse arquivo quando você configurar o Provedor.

	![Registration key](./media/hyper-v-recovery-manager-configure-vault/SRSAN_QuickStartRegKey.png)


2. Na página <b>Início Rápido</b>, em **Preparar servidores VMM**, clique <b>Baixar o Provedor de Recuperação de Site do Microsoft Azure para instalação nos servidores VMM</b> para obter a versão mais recente do arquivo de instalação do Provedor.
3. Execute esse arquivo nos servidores VMM de início e de destino.
4. Na **Verificação dos Pré-requisitos**, selecione para parar o serviço VMM para começar a configuração do Provedor. O serviço para e reiniciará automaticamente quando a configuração finaliza. 

	![Prerequisites](./media/hyper-v-recovery-manager-configure-vault/SRSAN_ProviderPrereq.png)

5. Em **Microsoft Update** você pode optar por atualizações. Com esta configuração de Provedor habilitada, a atualização será instalada de acordo com a política do Microsoft Update.

Após o Provedor ser instalado, continue com a configuração para registrar o servidor no cofre.

6. Na página Conexão com a Internet, especifique como o Provedor em execução no servidor VMM se conecta à Internet. Selecione <b>Usar configurações do proxy do sistema padrão</b> para usar as configurações de conexão à Internet padrão configuradas no servidor.

	![Internet Settings](./media/hyper-v-recovery-manager-configure-vault/SRSAN_ProviderProxy.png)

7. Em **Chave de Registro**, selecione que você baixou do Azure Site Recovery e copiou para o servidor VMM.
8. Em **Nome do servidor**, especifique um nome amigável para identificar o servidor VMM no cofre.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRSAN_ProviderRegKeyServerName.png)

9. Na sincronização dos **Metadados de nuvem inicial**, selecione se você deseja sincronizar os metadados para todas as nuvens no servidor VMM com o cofre. Esta ação só precisa acontecer uma vez em cada servidor. Se não quiser sincronizar todas as nuvens, você poderá deixar essa configuração desmarcada e sincronizar cada nuvem
10. 
11. individualmente nas propriedades de nuvem no console do VMM.

10. A opção **Criptografia de Dados** não é relevante nesse cenário. 

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRSAN_ProviderSyncEncrypt.png)

11. Clique em <b>Registrar</b> para concluir o processo. Após o registro, os metadados do servidor VMM é recuperado pela Recuperação de Site do Azure. O servidor é exibido no final da guia <b>Recursos</b> da página **Servidores** no cofre. Após a instalação do Provedor, modifique as configurações do Provedor no console do VMM.

 
<h3><a id="storage"></a>Etapa 4: Mapear matrizes de armazenamento e pools</h3>

Você precisará criar o mapeamento entre matrizes de armazenamento e pools nos sites primários e secundários.

Antes de começar, verifique se as nuvens aparecem no cofre. As nuvens são detectadas tanto pela seleção de sincronizar todas as nuvens quando você instala o provedor ou selecionando para sincronizar uma nuvem específica na guia **Geral** das propriedades da nuvem no console do VMM. Mapear as matrizes de armazenamento da seguinte forma:

1. Clique em **Recursos** > **Servidor de Armazenamento** > **Matrizes de Mapa de Origem e Destino**.
	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRSAN_StorageMap.png)
2. Selecione as matrizes de armazenamento no site primário e mapeie-as para armazenar as matrizes no site secundário.
3.  Mapeie os pools de armazenamento de origem e de destino nas matrizes. Para fazer isso, em **Pools de armazenamento** selecione um pool de armazenamento de origem e destino para mapear.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRSAN_StorageMapPool.png)




<h3><a id="clouds"></a>Etapa 5: Definir as configurações da proteção de nuvem</h3>

Depois que os servidores VMM são registrados, você pode definir as configurações de proteção de nuvem. Você habilitou a opção **Sincronizar dados de nuvem com o cofre** ao instalar o Provedor, então todas as nuvens no servidor VMM aparecerão na guia <b>Itens Protegidos</b> no cofre.

![Published Cloud](./media/hyper-v-recovery-manager-configure-vault/SRSAN_CloudsList.png)

1. Na página de Início Rápido, clique em **Configurar a proteção para nuvens VMM**.
2. Na guia **Itens Protegidos**, selecione a nuvem que você deseja configurar e vá para a guia **Configuração**. Observe que:
3. Em <b>Destino</b>, selecione <b>VMM</b>.
4. Em <b>Local de destino</b>, selecione o servidor VMM no local que gerencia a nuvem que você deseja usar para a recuperação.
5. Em <b>Nuvem de destino</b>, selecione a nuvem de destino que você deseja usar para failover de máquinas virtuais na nuvem de origem. Observe que:
	- É recomendável selecionar uma nuvem de destino que atenda aos requisitos de recuperação para máquinas virtuais que você protegerá.
	- Uma nuvem só pode pertencer a um par único de nuvem - como uma nuvem primária ou de destino.
6. A Recuperação de Site do Azure verifica se as nuvens têm acesso ao armazenamento capaz de replicação de SAN e se as matrizes de armazenamento estão igualadas. Os pares de matrizes participantes são exibidos.
7. Se a verificação for bem-sucedida, em **Tipo de replicação**, selecione **SAN**.

<p>Depois de salvar as configurações, um trabalho será criado e pode ser monitorado na guia <b>Trabalhos</b>. As configurações de nuvem podem ser modificadas na guia <b>Configurar</b>. Se você deseja modificar a nuvem de destino local ou destino, você deve remover a configuração de nuvem e, em seguida, reconfigurar a nuvem.</p>


<h3><a id="replication"></a>Etapa 6: Habilitar a replicação para grupos de replicação</h3>

Antes de habilitar a proteção para máquinas virtuais, você precisará habilitar a replicação para grupos de replicação de armazenamento. 

1. No portal de Recuperação de Site do Azure, na página de propriedades de nuvem primária abrir a guia **Máquinas Virtuais**. Clique em **Adicionar Grupo de Replicação**.
2. Selecione um ou mais grupos de replicação do VMM que estão associados com a nuvem, verifique se os conjuntos de origem e de destino e especifique a frequência de replicação.

<P>Quando essa operação for concluída, Recuperação de Site do Azure, juntamente com o VMM e os provedores de SMI-S provisionam o armazenamento do site de destino LUNs e habilitam a replicação de armazenamento. Se o grupo de replicação já foi replicado, a Recuperação de Site do Azure reutiliza a relação de replicação existente e atualiza as informações na Recuperação de Site do Azure.</P>

<h2><a id="enablevirtual"></a>Etapa 7: Habilitar a proteção da máquina virtual</h2>
<p>Depois que um grupo de armazenamento estiver replicando, habilite a proteção para máquinas virtuais no console do VMM usando um dos seguintes métodos:</p>



- **Nova máquina virtual**- No console no VMM quando você criar uma nova máquina virtual, habilite a proteção de Recuperação de Site do Azure e associe a máquina virtual no grupo de replicação.
Com essa opção, o VMM usa a colocação inteligente para otimizar o local do armazenamento de máquina virtual nos LUN do grupo de replicação. A Recuperação de Site do Azure orquestra a criação de uma máquina virtual de sombra no local secundário e aloca a capacidade para que as máquinas virtuais de réplica possam ser iniciadas após o failover.
- **Máquina virtual existente**-Se uma máquina virtual já está implantada no VMM, você pode habilitar a proteção de Recuperação de Site do Azure e fazer uma migração de armazenamento para um grupo de replicação. Após a conclusão, o VMM e a Recuperação de Site do Azure detectam a nova máquina virtual e começam a gerenciá-la na Recuperação de Site do Azure para proteção. Uma máquina virtual de sombra é criada no site secundário e a capacidade alocada para que a máquina virtual de réplica possa ser iniciada após o failover.


	![Enable protection](./media/hyper-v-recovery-manager-configure-vault/SRSAN_EnableProtection.png)
	

<P>Depois que as máquinas virtuais forem habilitadas para proteção, elas aparecem no console de Recuperação de Site do Azure. Você pode exibir propriedades da máquina virtual, acompanhar o status e o failover de grupos de replicação que contêm várias máquinas virtuais. Observe que na replicação de SAN, todas as máquinas virtuais associadas a um grupo de replicação devem fazer failover juntas. Isso ocorre porque o failover ocorre na camada de armazenamento pela primeira vez. É importante agrupar seus grupos de replicação corretamente e colocar apenas as máquinas virtuais juntas.</P>

Acompanhe o progresso da ação Habilitar Proteção na guia **Trabalhos**, incluindo a replicação inicial. Após o trabalho de Finalizar Proteção ser executado, a máquina virtual está pronta para failover. 
	![Virtual machine protection job](./media/hyper-v-recovery-manager-configure-vault/SRSAN_JobPropertiesTab.png)

<h3><a id="recoveryplans"></a>Etapa 8: Testar a implantação</h3>



Teste a implantação para garantir que as máquinas virtuais e os dados executem o failover conforme o esperado. Para fazer isso, você criará um plano de recuperação selecionando grupos de replicação. Em seguida, execute um failover de teste no plano.

1. Na guia **Planos de Recuperação**, clique em **Criar Plano de Recuperação**.
2. Especifique um nome para o plano de recuperação e servidores do VMM de origem e destino. O servidor de origem deve ter máquinas virtuais que são habilitadas para failover e recuperação. Selecione **SAN** para exibir somente as nuvens que estão configuradas para replicação de SAN.

	![Create recovery plan](./media/hyper-v-recovery-manager-configure-vault/SRSAN_RPlan.png)

4. Em **Selecionar Máquina Virtual**, selecione os grupos de replicação. Todas as máquinas virtuais associadas ao grupo de replicação serão selecionadas e adicionadas ao plano de recuperação. Essas máquinas virtuais são adicionadas ao grupo padrão do plano de recuperação - Grupo 1. Você pode adicionar mais grupos se necessário. Observe que, depois da replicação, as máquinas virtuais serão inicializadas de acordo com a ordem dos grupos de plano de recuperação.

	![Add virtual machines](./media/hyper-v-recovery-manager-configure-vault/SRSAN_RPlanVM.png)	
5. Depois que um plano de recuperação ter sido criado, ele aparece na lista da guia **Planos de Recuperação**. 
6. Na guia **Planos de Recuperação**, selecione o plano e clique em **Failover de Teste**.
7. Na página **Confirmar Failover de Teste** selecione **Nenhum**. Observe que com essa opção habilitada a falha em máquinas virtuais de réplica não será conectada a nenhuma rede. Isso irá testar se a máquina virtual falha conforme o esperado, mas não testa seu ambiente de rede de replicação. Se você quiser executar um failover de teste mais abrangente, consulte <a href="http://go.microsoft.com/fwlink/?LinkId=522291">Testar uma implantação local no MSDN</a>.

	![Select test network](./media/hyper-v-recovery-manager-configure-vault/SRSAN_TestFailover1.png)


8. A máquina virtual de teste será criada no mesmo host como o host em que a máquina virtual de réplica existe. Ele não será adicionado na nuvem em que a máquina virtual de réplica está localizada.
9. Após a replicação a máquina virtual de réplica terá um endereço IP que não é o mesmo que o endereço IP da máquina virtual primária. Se você estiver emitindo endereços do DHCP, em seguida, será atualizado automaticamente. Se você não estiver usando o DHCP e quiser verificar se os endereços são os mesmos, você precisará executar alguns scripts.
10. Execute este script de exemplo para recuperar o endereço IP.
    **$vm = Get-SCVirtualMachine -Name <VM_NAME>
	$na = $vm[0].VirtualNetworkAdapters>
	$ip = Get-SCIPAddress -GrantToObjectID $na[0].id
	$ip.address**  
11. Execute este script de exemplo para atualizar o DNS especificando o endereço IP recuperado usando o script de exemplo anterior.

	**[string]$Zone,
	[string]$name,
	[string]$IP
	)
	$Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
	$newrecord = $record.clone()
	$newrecord.RecordData[0].IPv4Address  =  $IP
	Set-DnsServerResourceRecord -zonename com -OldInputObject $record -NewInputObject $Newrecord**

<h2><a id="runtest" name="runtest" href="#runtest"></a>Monitorar atividade</h2>
<p>Você pode usar a guia <b>Trabalhos</b> e <b>Painel</b> para exibir e monitorar os principais trabalhos executados pelo cofre de Recuperação de Site do Azure, incluindo a configuração da proteção para uma nuvem; habilitação e desabilitação da proteção para uma máquina virtual; execução de um failover (planejado, não planejado ou teste) e confirmação de um failover não planejado.</p>

<p>Na guia <b>Trabalhos</b> você exibe os trabalhos, realiza busca detalhada nos detalhes do trabalho e erros, executa consultas de trabalho para recuperar trabalhos que combinam com os critérios específicos, exporta trabalhos para o Excel e reinicia os trabalhos falhos.</p>

<p>No <b>Painel</b>, você pode baixar as últimas versões dos arquivos de instalação do Provedor e do Agente, obter informações de configuração para o cofre, consultar o número de máquinas virtuais que tem a proteção gerenciada pelo cofre, ver os trabalhos recentes, gerenciar o certificado do cofre e sincronizar novamente as máquinas virtuais.</p>

<p>Para obter mais informações sobre a interação com trabalhos e o painel, consulte o <a href="http://go.microsoft.com/fwlink/?LinkId=398534">Guia de Operações e Monitoramento</a>.</p>
	
<h2><a id="next" name="next" href="#next"></a>Próximas etapas</h2>
<UL>
<LI>Para planejar e implantar o Azure Site Recovery em um ambiente de produção completo, consulte <a href="http://go.microsoft.com/fwlink/?LinkId=321294">Guia de planejamento para a Recuperação do Hyper-V</a> e <a href="http://go.microsoft.com/fwlink/?LinkId=321295">Guia de implantação do Azure Site Recovery</a>.</LI>
<LI>Se tiver dúvidas, visite o <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Fórum de serviços de recuperação do Windows Azure</a>.</LI> 
</UL>

<!--HONumber=49-->