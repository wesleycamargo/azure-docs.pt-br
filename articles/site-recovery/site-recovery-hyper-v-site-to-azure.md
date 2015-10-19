<properties
	pageTitle="Configurar a proteção entre um site do Hyper-V local e o Azure"
	description="O Azure Site Recovery coordena a replicação, o failover e a recuperação de máquinas virtuais localizadas em servidores Hyper-V locais para o Azure"
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="09/29/2015"
	ms.author="raynew"/>


# Configurar a proteção entre um site do Hyper-V local e o Azure


## Visão geral

O Azure Site Recovery contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) gerenciando replicação, failover e recuperação de máquinas virtuais em vários cenários de implantação. Leia sobre possíveis cenários de implantação na [Visão geral sobre a Recuperação de Site](site-recovery-overview.md).

Este artigo descreve como implantar a Recuperação de Site para replicar máquinas virtuais localizadas em servidores Hyper-V locais executando o Windows Server 2012 R2. A replicação para o armazenamento do Azure é orquestrada pela Recuperação de Site. Essa implantação é particularmente útil se você tiver servidores Hyper-V, mas o System Center Virtual Machine Manager (VMM) não estiver implantado.


## Sobre este artigo

Este artigo resume os pré-requisitos de implantação, ajuda a definir as configurações de replicação e a habilitar a proteção para máquinas virtuais. Ele é concluído com o teste do failover para verificar se tudo está funcionando conforme o esperado.

Se tiver problemas, publique suas perguntas no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## Antes de começar

Certifique-se de que ter tudo em colocar antes de começar.

### Pré-requisitos do Azure

- Você precisará de uma conta do [Microsoft Azure](http://azure.microsoft.com/). Você pode começar com uma [avaliação gratuita](pricing/free-trial/).
- Você precisará de uma conta de armazenamento do Azure para armazenar os dados replicados no Azure. A conta precisa estar com a replicação geográfica habilitada. Ela deve estar localizada na mesma região que o cofre do Azure Site Recovery e ser associada à mesma assinatura. Para saber mais, leia a [Introdução ao Armazenamento do Microsoft Azure](../storage/storage-introduction.md).
- Você precisará de uma rede virtual do Azure para que as máquinas virtuais replicadas estejam conectadas a uma rede após o failover.

### Pré-requisitos do Hyper-V

- No site local de origem será necessário pelo menos um servidor executando o Windows Server 2012 R2 com a função Hyper-V.
- O servidor Hyper-V deve conter uma ou mais máquinas virtuais.
- Os servidores Hyper-V devem estar conectados à Internet, diretamente ou por meio de um proxy.
- Os servidores Hyper-V devem ter as correções mencionadas no [KB2961977](https://support.microsoft.com/pt-BR/kb/2961977 "KB2961977") instaladas.

### Pré-requisitos de máquina virtual

As máquinas virtuais que você deseja proteger devem estar de acordo com os [Pré-requisitos para máquinas virtuais](site-recovery-best-practices.md/#virtual-machines).

### Pré-requisitos de provedor e do agente

Como parte da implantação do Azure Site Recovery, você instalará o Provedor do Azure Site Recovery e o Agente do Azure Site Recovery em cada servidor Hyper-V com as máquinas virtuais que você deseja proteger. Observe que:

- Você deve executar as versões mais recentes do Provedor e do agente.
- Todos os servidores Hyper-V em um cofre devem ter as mesmas versões.
- O Provedor precisará se conectar ao Azure Site Recovery pela Internet. Você pode fazer isso sem um proxy, usando as configurações de proxy configuradas no servidor VMM ou usando as configurações personalizadas de proxy definidas durante a instalação do Provedor. Para usar um servidor proxy existente, verifique se as URLs de conexão com o Azure têm permissão no firewall:
	- **.hypervrecoverymanager.windowsazure.com
- **.accesscontrol.windows.net
- **.backup.windowsazure.com
- **.blob.core.windows.net
- **.store.core.windows.net
 
- Para usar o proxy personalizado, configure-o antes de instalar o Provedor. Durante a instalação do Provedor, você precisará especificar o endereço do servidor proxy e a porta, além das credenciais que podem ser usadas para acesso. Observe que o proxy com base em HTTPS não é suportado.

A figura abaixo mostra os canais de comunicação e as portas diferentes usados pelo Azure Site Recovery para coordenação e a replicação

![Topologia B2A](./media/site-recovery-hyper-v-site-to-azure/B2ATopology.png)


## Etapa 1: criar um cofre

1. Entre no [Portal de Gerenciamento](https://portal.azure.com).


2. Expanda **Serviços de Dados** > **Serviços de Recuperação** e clique em **Cofre de Recuperação de Site**.


3. Clique em **Criar Novo** > **Criação Rápida**.

4. Em **Nome**, digite um nome amigável para identificar o cofre.

5. Em **Região**, selecione a região geográfica para o cofre. Para verificar as regiões com suporte, consulte a Disponibilidade Geográfica nos [Detalhes dos Preços do Azure Site Recovery](pricing/details/site-recovery/).

6. Clique em **Criar cofre**.

	![Novo cofre](./media/site-recovery-hyper-v-site-to-azure/SR_HvVault.png)

Verifique a barra de status para confirmar que o cofre foi criado com sucesso. O cofre será listado como **Ativo** na página de Serviços de Recuperação.


## Etapa 2: criar um site do Hyper-V

1. Na página Serviços de Recuperação, clique no cofre para abrir a página de Início Rápido. O Início Rápido pode também ser aberto a qualquer tempo usando o ícone.

	![Início rápido](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_QuickStartIcon.png)

2. Na lista suspensa, selecione **Entre um site do Hyper-V local e o Azure**.

	![Cenário de site do Hyper-V](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_SelectScenario.png)

3. Em **Criar um Site do Hyper-V**, clique em **Criar Site do Hyper-V**. Especifique um nome e salve.

	![Site do Hyper-V](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_CreateSite2.png)


## Etapa 3: instalar o Provedor e o agente
Instale o Provedor e o agente. Se você estiver instalando em um cluster do Hyper-V, execute as etapas 5 a 11 em cada nó no cluster de failover. Após o registro de todos os nós e a habilitação da proteção, as máquinas virtuais serão protegidas mesmo se migrarem entre nós no cluster de failover.

1. Em **Preparar servidores Hyper-V**, clique em **Baixar um arquivo de chave de registro**.
2. Na página **Baixar a Chave de Registro**, clique em **Baixar** ao lado do site. Baixe a chave para um local seguro onde possa ser facilmente acessada pelo servidor Hyper-V. A chave é válida para 5 dias após ser gerada.

	![Chave de Registro](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_DownloadKey2.png)

4. Clique em **Baixar o Provedor** para obter a versão mais recente.
5. Execute o arquivo em cada servidor Hyper-V que você deseja registrar no cofre. O arquivo instala dois componentes:
	- **Provedor do Azure Site Recovery** — Lida com a comunicação e a coordenação entre o servidor Hyper-V e o portal do Azure Site Recovery.
	- **Agente de Serviços de Recuperação do Azure** — Lida com o transporte de dados entre máquinas virtuais em execução no servidor de origem Hyper-V e o armazenamento do Azure.
6. No **Microsoft Update** você pode optar por atualizações. Com essa configuração habilitada, o Provedor e o Agente de atualizações serão instaladas de acordo com a diretiva do Microsoft Update.

	![Atualizações da Microsoft](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider1.png)

7. Em **Instalação ** especifique onde você deseja instalar o Provedor e o Agente no servidor Hyper-V.

	![Local de instalação](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider2.png)

8. Após a instalação continue para registrar o servidor no cofre.

	![Instalação concluída](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider3.png)


9. Na página **Conexão com a Internet**, especifique como o Provedor se conecta ao Azure Site Recovery. Selecione **Usar configurações de proxy padrão do sistema** para usar as configurações de conexão com a Internet definidas no servidor. Se você não especificar um valor, as configurações padrão serão usadas.

	![Configurações da Internet](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider4.png)

	Observe que:

	- Se o proxy padrão no servidor Hyper-V exigir autenticação, você deverá selecionar o uso de um servidor proxy personalizado. Digite os detalhes do proxy padrão e especifique as credenciais.
	- Se quiser usar um servidor proxy personalizado, você deverá configurá-lo antes de instalar o Provedor.
	- As URLs a seguir devem estar acessíveis no host Hyper-v
		- **.hypervrecoverymanager.windowsazure.com
- **.accesscontrol.windows.net
- **.backup.windowsazure.com
- **.blob.core.windows.net
- **.store.core.windows.net

	- Permita os endereços IP descritos em [Intervalos de IP do armazenamento de dados do Azure](http://go.microsoft.com/fwlink/?LinkId=511094) e o protocolo HTTPS (443). Você teria que fazer uma lista de intervalos IP válidos da região do Azure que você planeja usar e do oeste dos EUA.

9. Na página **Configurações do Cofre**, clique em **Procurar ** para selecionar o arquivo da chave. Especifica a assinatura do Azure Site Recovery, o nome do cofre e o site de Hyper-V ao qual pertence o servidor Hyper-V.

	![Registros do servidor](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_SelectKey.png)


11. Registro começa a registrar o servidor no cofre.

	![Registros do servidor](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider6.png)

11. Após a conclusão do registro, os metadados do servidor Hyper-V são recuperados pelo Azure Site Recovery e o servidor é exibido na guia **Sites do Hyper-V** da página **Servidores** no cofre.

	![Registros do servidor](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider7.png)

	> [AZURE.NOTE]O Provedor do Azure Site Recovery também pode ser instalado usando a linha de comando a seguir. Esse método pode ser usado para instalar o provedor em um NÚCLEO de Servidor para o Windows Server 2012 R2
	>
	>1. Baixar o arquivo de instalação do Provedor e a chave de registro em uma pasta, por exemplo, C:\\ASR
	>2. Extrair o instalador do provedor, executando os comandos abaixo em um prompt de comando com privilégios de **Administrador**
	>
	    	C:\Windows\System32> CD C:\ASR
	    	C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
	>4. Instalar o provedor executando o comando a seguir
	>
			C:\ASR> setupdr.exe /i
	>5. Registrar o provedor executando o comando a seguir
	>
	    	CD C:\Program Files\Microsoft Azure Site Recovery Provider\
	    	C:\Program Files\Microsoft Azure Site Recovery Provider> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file>


	>----------
          
	>####Lista de parâmetros de instalação de linha de comando####
	>- **/Credentials**: parâmetro obrigatório que especifica o local no qual o arquivo da chave de registro está localizado  
	> - **/FriendlyName**: parâmetro obrigatório para o nome do servidor do host Hyper-V que aparece no portal do Azure Site Recovery.
	> - **/proxyAddress**: parâmetro opcional que especifica o endereço do servidor proxy.
	> - **/proxyport**: parâmetro opcional que especifica a porta do servidor proxy.
	> - **/proxyUsername**: parâmetro opcional que especifica o nome de usuário de Proxy (se o proxy exige autenticação).
	> - **/proxyPassword**: parâmetro opcional que especifica a Senha para autenticação com o servidor proxy (se o proxy exige autenticação).

>[AZURE.TIP]Você pode configurar cada host Hyper-V individual para usar configurações de largura de banda de rede diferentes para replicar as máquinas virtuais no Azure. Saiba mais sobre [Como gerenciar o uso de largura de banda de rede para proteção do local para o Azure](https://support.microsoft.com/pt-BR/kb/3056159)


## Etapa 4: criar recursos do Azure

1. Na página **Preparar recursos**, selecione **Criar Conta de Armazenamento** para criar uma conta de armazenamento do Azure, caso você ainda não tenha uma. A conta deve ter a replicação geográfica habilitada. Ela deve estar na mesma região que o cofre do Azure Site Recovery e estar associada à mesma assinatura.

	![Criar conta de armazenamento](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_CreateResources1.png)

## Etapa 5: criar e configurar grupos de proteção

Grupos de proteção são agrupamentos lógicos de máquinas virtuais que você deseja proteger usando as mesmas configurações de proteção. Aplique as configurações de proteção em um grupo de proteção e essas configurações serão aplicadas em todas as máquinas virtuais que você adicionar ao grupo. 1. Em **Criar e configurar grupos de proteção**, clique em **Criar um grupo de proteção**. Se algum pré-requisito não estiver em vigor, uma mensagem será emitida e você poderá clicar em **Exibir detalhes** para saber mais.

2. Na guia **Grupos de Proteção**, adicione um grupo de proteção. Especifique um nome, o site de origem Hyper-V, o **Azure** de destino, o nome da sua assinatura do Azure Site Recovery e a conta de armazenamento do Azure.

	![Grupo de proteção](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_ProtectionGroupCreate3.png)


2. Em **Configurações de replicação**, defina a **Frequência de cópia** para especificar com que frequência o delta de dados deve ser sincronizado entre a origem e o destino. Você pode definir como 30 segundos, 5 minutos ou 15 minutos.
3. Em **Reter pontos de recuperação**, especifique quantas horas de histórico de recuperação devem ser armazenadas.
4. Em **Frequência de instantâneos consistentes com o aplicativo**, é possível especificar se deve obter instantâneos que usam o Serviço de VSS (Cópias de Sombra de Volume) para garantir que os aplicativos estejam em um estado consistente quando o instantâneo for obtido. Por padrão, eles não obtidos. Defina essa opção com um valor menor do que o número dos pontos de recuperação adicionais que você configurar. Isso só terá suporte se a máquina virtual estiver sendo executada um sistema operacional Windows.
5. Em **Hora de início da replicação**, especifique quando a replicação inicial das máquinas virtuais no grupo de proteção deve ser enviada ao Azure.

	![Grupo de proteção](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_ProtectionGroup4.png)


## Etapa 6: habilitar a proteção da máquina virtual


Adicione máquinas virtuais aos grupos de proteção para protegê-las.

1. Na guia **Máquinas** do grupo de proteção, clique em **Adicionar máquinas virtuais aos grupos de proteção para habilitar a proteção**.
2. Na página **Habilitar Proteção da Máquina Virtual** selecione as máquinas virtuais que você deseja proteger.

	![Habilitar a proteção da máquina virtual](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_AddVM3.png)

	Os trabalhos de Habilitar a Proteção são iniciados. Você pode acompanhar o andamento na guia **Trabalhos**. Após o trabalho de Finalizar Proteção ser executado, a máquina virtual está pronta para failover.
3. Após a configuração da proteção, é possível:

	- Exibir as máquinas virtuais em **Itens Protegidos** > **Grupos de Proteção** > *nome\_grupodeproteção* > **Máquinas Virtuais**. É possível detalhar as informações sobre a máquina na guia **Propriedades**.
	- Configure as propriedades de failover de uma máquina virtual em **Itens Protegidos** > **Grupos de Proteção** > *nome\_grupodeproteção* > **Máquinas Virtuais** *virtual\_machine\_name* > **Configurar**. Você pode configurar:
		- **Nome**: o nome da máquina virtual no Azure.
		- **Tamanho**: o tamanho de destino da máquina virtual que executa o failover.

		![Configurar as propriedades da máquina virtual](./media/site-recovery-hyper-v-site-to-azure/VMProperties.png)
	- Defina as configurações adicionais de máquina virtual em *Itens Protegidos* > **Grupos de Proteção** > *nome\_grupodeproteção* > **Máquinas Virtuais** *nome\_da\_máquina\_virtual* > **Configurar**, incluindo.

		- **Adaptadores de rede**: o número de adaptadores de rede é determinado pelo tamanho especificado para a máquina virtual de destino.
			- Grande (A3) e A6: 2
			- Extra grande (A4) e A7:
			- A9: 2
			- D3: 2
			- D4: 4
			- D13: 4

			Quando você altera o tamanho de uma máquina virtual e salva as configurações, na próxima vez que você abrir a página **Configurar**, os adaptadores de rede serão exibidos. O número de adaptadores para uma máquina virtual será determinado da seguinte maneira:


			- Se o número de adaptadores de rede na máquina de origem for menor ou igual ao número de adaptadores permitido para o tamanho da máquina de destino, o destino terá o mesmo número de adaptadores que a origem.
			- Se o número de adaptadores para máquina virtual de origem exceder o número permitido para o tamanho de destino e o tamanho máximo de destino será usado.
			- Por exemplo, se uma máquina de origem tiver dois adaptadores de rede e o tamanho da máquina de destino oferecer suporte a quatro, a máquina de destino terá dois adaptadores. Se a máquina de origem tiver dois adaptadores, mas o tamanho de destino com suporte oferecer suporte apenas a uma máquina de destino, ela terá apenas um adaptador. 	
		- **Rede Azure**: especifique a rede na qual a máquina virtual devem executar failover. Se a máquina virtual tiver vários adaptadores de rede, todos deverão estar conectados à mesma rede do Azure.
		- **Sub-rede** Para cada adaptador de rede na máquina virtual, selecione a sub-rede na rede do Azure à qual a máquina deve se conectar após o failover.
		- **Endereço IP de destino**: se o adaptador de rede da máquina virtual de origem estiver configurado para usar um endereço IP estático, você poderá especificar o endereço IP da máquina virtual de destino para garantir que a máquina tenha o mesmo endereço IP após o failover. Se você não especificar um endereço IP, qualquer endereço disponível no momento será atribuído durante o failover. Se você especificar um endereço que está sendo usado, o failover falhará.

		![Configurar as propriedades da máquina virtual](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_VMMultipleNic.png)

## Etapa 7: criar um plano de recuperação

Para testar a implantação, você pode executar um failover de teste para uma única máquina virtual ou um plano de recuperação que contém uma ou mais máquinas virtuais. Para criar um plano de recuperação, [siga estas instruções](site-recovery-create-recovery-plans.md)

## Etapa 8: testar a implantação

Há duas maneiras de executar um failover de teste no Azure.

- Failover de teste sem uma rede do Azure: esse tipo de failover de teste verifica se a máquina virtual é mostrada corretamente no Azure. A máquina virtual não será conectada a nenhuma rede do Azure após o failover.
- Failover de teste com uma rede do Azure: esse tipo de failover verifica se o ambiente de replicação inteiro é mostrado conforme o esperado e se as máquinas virtuais com failover serão conectadas à rede do Azure de destino especificada. Para a manipulação de sub-rede, para failover de teste, a sub-rede da máquina virtual de teste será determinada com base na sub-rede da máquina virtual de réplica. Isso é diferente da replicação normal quando a sub-rede de uma máquina virtual de réplica baseia-se na sub-rede da máquina virtual de origem.

Se você quiser executar um failover de teste para uma máquina virtual habilitada para proteção no Azure sem especificar uma rede de destino do Azure, não será necessário preparar nada. Para executar um teste de failover com um destino de rede do Azure, você precisará criar uma nova rede do Azure isolada da rede de produção do Azure (o comportamento padrão quando você cria uma nova rede no Azure) e configurar a infraestrutura para que a máquina virtual replicada funcione como esperado. Por exemplo, uma máquina virtual com o Controlador de Domínio e DNS pode ser replicada para o Azure usando o Azure Site Recovery, e pode ser criada na rede de teste usando o Failover de teste. Para realizar um failover de teste, execute as etapas a seguir:


1. Realize um failover de teste da máquina virtual com Controlador de Domínio e DNS na mesma rede que você usará para o failover de teste real da máquina virtual local.
2. Anote os endereços IP que foram alocados às máquinas virtuais DNS em estado de failover.
3. Na rede virtual do Azure que será usada para o failover, adicione os endereços IP como os endereços dos servidores DNS.
4. Execute o failover de teste da máquina virtual local de origem, especificando a rede do Azure.
5. Após verificar o funcionamento esperado do failover de teste, marque o failover de teste como concluído para o plano de recuperação e marque o failover de teste como concluído para as máquinas virtuais do Controlador de Domínio e de DNS.

Para executar o failover de teste, faça o seguinte:

1. Na guia **Planos de Recuperação**, selecione o plano e clique em **Failover de Teste**.
2. Na página **Confirmar Failover de Teste**, selecione **Nenhum** ou uma rede específica do Azure. Observe que, se você selecionar **Nenhum**, o failover de teste verificará se a máquina virtual foi replicada corretamente para o Azure, mas não verificará sua configuração de rede de replicação.

	![Failover de teste](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_TestFailoverNoNetwork.png)

3. Na guia **Trabalhos**, você pode acompanhar o progresso do failover. Você também deverá ver a réplica de teste de máquina virtual no portal do Azure. Se tiver configurado para máquinas virtuais de acesso a rede local, você pode iniciar uma conexão de área de trabalho remota para a máquina virtual.
4. Quando o failover atinge a fase **Teste completo**, clique em **Teste Completo** para concluir o failover de teste. Você pode fazer uma busca detalhada na guia **Trabalho** para acompanhar o progresso e o status do failover, e para realizar as ações necessárias.
5. Após o failover, você poderá ver a réplica de teste da máquina virtual no portal do Azure. Se tiver configurado para máquinas virtuais de acesso a rede local, você pode iniciar uma conexão de área de trabalho remota para a máquina virtual.

	1. Verificar se as máquinas virtuais foram iniciadas com êxito.
    2. Se você quiser se conectar à máquina virtual no Azure usando a Área de trabalho remota após o failover, habilite a Conexão de Área de Trabalho Remota na máquina virtual antes de executar o teste de failover. Você também precisará adicionar um ponto de extremidade RDP na máquina virtual. Você pode aproveitar um [Runbooks de Automação do Azure](site-recovery-runbook-automation.md) para fazer isso.
    3. Após o failover, se você usar um endereço IP público para se conectar à máquina virtual no Azure usando a Área de Trabalho Remota, verifique se você não tem qualquer política de domínio que impeça a conexão com uma máquina virtual usando um endereço público.

6. Após a conclusão do teste, faça o seguinte:

	- Clique em **Failover de teste concluído**. Limpe o ambiente de teste para desligar automaticamente e excluir as máquinas virtuais de teste.
	- Clique em **Observações** para gravar e salvar observações associadas ao failover de teste.
7. Quando o failover atinge a fase **Testes completos**, conclua a verificação da seguinte maneira:
	1. Visualize a máquina virtual de réplica no portal do Azure. Verifique se a máquina virtual foi iniciada com êxito.
	2. Se tiver configurado para máquinas virtuais de acesso a rede local, você pode iniciar uma conexão de área de trabalho remota para a máquina virtual.
	3. Clique em **Concluir o teste** para concluí-lo.
	4. Clique em **Observações** para gravar e salvar observações associadas ao failover de teste.
	5.  Clique em **Failover de teste concluído**. Limpa o ambiente de teste para desligar automaticamente e excluir a máquina virtual de teste.

## Próximas etapas

Depois que a implantação é configurada e está em funcionamento, [saiba mais](site-recovery-failover.md) sobre o failover.

<!---HONumber=Oct15_HO2-->