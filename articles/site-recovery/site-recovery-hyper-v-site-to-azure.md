<properties
	pageTitle="Replicar máquinas virtuais do Hyper-V (sem VMM) para o Azure usando o Azure Site Recovery no portal do Azure | Microsoft Azure"
	description="Descreve como implantar o Azure Site Recovery para orquestrar a replicação, o failover e a recuperação de VMs do Hyper-V locais que não são gerenciadas pelo VMM para o Azure usando o portal do Azure"
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
	ms.date="09/19/2016"
	ms.author="raynew"/>


# Replicar máquinas virtuais do Hyper-V (sem VMM) para o Azure usando o Azure Site Recovery no portal do Azure | Microsoft Azure

> [AZURE.SELECTOR]
- [Portal do Azure](site-recovery-hyper-v-site-to-azure.md)
- [Clássico do Azure](site-recovery-hyper-v-site-to-azure-classic.md)
- [ARM do PowerShell](site-recovery-deploy-with-powershell-resource-manager.md)



Bem-vindo ao Azure Site Recovery! Use este artigo se você quiser replicar máquinas virtuais do Hyper-V locais gerenciado que **não** são gerenciadas em nuvens do VMM (Virtual Machine Manager) do System Center no Azure. Este artigo descreve como configurar a replicação usando o Azure Site Recovery no portal do Azure.

> [AZURE.NOTE] O Azure tem dois [modelos de implantação](../resource-manager-deployment-model.md) diferentes para criar e trabalhar com recursos: ARM (Azure Resource Manager) e clássico. O Azure também tem dois portais – o portal clássico do Azure, que dá suporte ao modelo de implantação clássica, e o portal do Azure, com suporte para ambos os modelos de implantação.


No portal do Azure, o Azure Site Recovery fornece alguns recursos novos:

- No portal do Azure, os serviços Backup do Azure e Azure Site Recovery são combinados em um único cofre de Serviços de Recuperação para que você possa configurar e gerenciar a continuidade dos negócios e a recuperação de desastres (BCDR) em um único local. Um painel unificado permite monitorar e gerenciar operações em seus sites locais e de nuvem pública do Azure.
- Agora, os usuários com assinaturas do Azure provisionadas pelo programa CSP (Provedor de Soluções na Nuvem) podem gerenciar as operações de Recuperação de Site no portal do Azure.
- A Recuperação de Site no portal do Azure pode replicar máquinas para contas de armazenamento do ARM. No failover, a Recuperação de Site cria VMs baseadas no ARM no Azure.
- A Recuperação de Site continua a dar suporte à replicação para as contas de armazenamento clássico e fazer failover de VMs usando o modelo clássico.


Depois de ler este artigo, publique quaisquer comentários na parte inferior dos comentários do Disqus. Faça perguntas técnicas no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## Visão geral


As organizações precisam de uma estratégia de BCDR que determine como os aplicativos, as cargas de trabalho e os dados permanecerão em execução e disponíveis durante o tempo de inatividade planejado e não planejado, e como recuperarão as condições normais de trabalho assim que possível. Sua estratégia de BCDR manterá os dados comerciais seguros e passíveis de recuperação e garantir que as cargas de trabalho estejam continuamente disponíveis mediante um desastre.

A Recuperação de Site é um serviço do Azure que colabora com sua estratégia de BCDR por meio da coordenação da replicação de servidores físicos e máquinas virtuais locais na nuvem (Azure) ou em um datacenter secundário. Quando ocorrem paralisações em seu local primário, você realiza o failover em um local secundário a fim de manter os aplicativos e cargas de trabalho disponíveis. Quando o local primário retoma as operações normais, você realiza o failback. Saiba mais em [O que é o Azure Site Recovery?](site-recovery-overview.md)

Este artigo fornece todas as informações de que você precisa para replicar VMs do Hyper-V que não são gerenciadas em nuvens do VMM para o Azure. Ele inclui uma visão de geral da arquitetura, das informações de planejamento e das etapas de implantação para configurar os servidores locais, o Azure, uma política de replicação e o planejamento de capacidade. Depois de configurar a infraestrutura, você poderá habilitar a replicação nos computadores que deseja proteger e testar o failover.

## Vantagens de negócios

- Fornece failover fora do local (Azure) para cargas de trabalho e aplicativos executados em máquinas virtuais do Hyper-V.
- Réplicas das VMs do Hyper-V usando o Azure Site Recovery sem a necessidade de um servidor do VMM.
- Fornece um único console dos Serviços de Recuperação para uma instalação simples e para o gerenciamento de replicação, failover e processos de recuperação.
- Permite a você executar facilmente failovers de sua infraestrutura local para o Azure e failback (restauração) do Azure para site local.
- Você pode configurar planos de recuperação com vários computadores para que as cargas de trabalho do aplicativo em camadas façam failover juntas.

## Arquitetura de cenário

Estes são os componentes do cenário:

- **Host ou cluster do Hyper-V**: servidores ou clusters de host do Hyper-V locais. Os hosts do Hyper-V executando VMs que você deseja proteger são reunidos em sites do Hyper-V lógicos durante a implantação da Recuperação de Site.
- **Provedor do Azure Site Recovery e agente de Serviços de Recuperação**: durante a implantação, você instala o Provedor do Azure Site Recovery e agente dos Serviços de Recuperação do Microsoft Azure nos servidores host do Hyper-V. O Provedor se comunica com a Azure Site Recovery por HTTPS 443 para replicar a orquestração. Por padrão, o agente no servidor host do Hyper-V replica dados no armazenamento do Azure por HTTPS 443.
- **Azure**: você precisa de uma assinatura do Azure, de uma conta de armazenamento do Azure para armazenar os dados replicados e de uma rede virtual do Azure para que as VMs do Azure sejam conectadas a uma rede após o failover.

![Arquitetura de site do Hyper-V](./media/site-recovery-hyper-v-site-to-azure/architecture.png)
 


## Pré-requisitos do Azure

Aqui está o que será necessário no Azure para implantar este cenário.

**Pré-requisito** | **Detalhes**
--- | ---
**Conta do Azure**| Você precisará de uma conta do [Microsoft Azure](http://azure.microsoft.com/). Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre os preços da Recuperação de Site. 
**Armazenamento do Azure** | Você precisará de uma conta de armazenamento padrão. Você pode usar uma conta de armazenamento LRS ou GRS. É recomendável usar GRS para que os dados sejam resilientes caso ocorra uma interrupção regional, ou se a região principal não puder ser recuperada. [Saiba mais](../storage/storage-redundancy.md). A conta deve estar na mesma região que o cofre dos Serviços de Recuperação.<br/><br/> Não há suporte para o armazenamento Premium.<br/><br/> Os dados replicados são armazenados no armazenamento do Azure e as VMs do Azure são criadas quando ocorre failover.<br/><br/> [Leia sobre](../storage/storage-introduction.md) o armazenamento do Azure.
**Rede do Azure** | Você precisará de uma rede virtual do Azure com a qual as máquinas virtuais do Azure se conectarão quando ocorrer failover. A rede virtual do Azure deve estar na mesma região que o cofre dos Serviços de Recuperação. 

## Pré-requisitos do local

Veja o que será necessário no local.

**Pré-requisito** | **Detalhes**
--- | ---
**Hyper-V**| Um ou mais servidores locais executando o Windows Server 2012 R2 com a função de Hyper-V e as atualizações mais recentes.<br/><br/>O servidor Hyper-V deve conter uma ou mais máquinas virtuais.<br/><br/>Servidores Hyper-V devem estar conectados à Internet, diretamente ou por meio de um proxy.<br/><br/>Servidores Hyper-V devem ter correções mencionadas no [KB2961977](https://support.microsoft.com/pt-BR/kb/2961977 "KB2961977") instalado.
**Provedor e agente** | Durante a implantação do Azure Site Recovery, você instalará o provedor do Azure Site Recovery. A instalação do Provedor também instalará o Agente dos Serviços de Recuperação do Azure em cada servidor Hyper-V executando máquinas virtuais que deseja proteger. Todos os servidores Hyper-V em um cofre de Recuperação de Site devem ter as mesmas versões do Provedor e do agente.<br/><br/>O Provedor precisará se conectar ao Azure Site Recovery pela Internet. O tráfego pode ser enviado diretamente ou por meio de um proxy. Observe que o proxy com base em HTTPS não é suportado. O servidor proxy deve permitir o acesso a: <br/><br/> *.hypervrecoverymanager.windowsazure.com <br/><br/> *.accesscontrol.windows.net <br/><br/> *.backup.windowsazure.com <br/><br/> *.blog.core.windows.net <br/><br/> *store.core.windows.net <br/><br/> https://www.msftncsi.com/ncsi.txt<br/><br/>Se você tiver regras de firewall baseadas em endereço IP no servidor, verifique se as regras permitem a comunicação com o Azure. Você precisará permitir os [Intervalos de IPs de Datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e o protocolo HTTPS (433).<br/><br/>Permita os intervalos de endereços IP para a região do Azure da sua assinatura e para o Oeste dos EUA.

## Pré-requisitos de computadores protegidos


**Pré-requisito** | **Detalhes**
--- | ---
**VMs protegidas** | Antes de fazer failover de uma VM, será necessário verificar se o nome que será atribuído à VM do Azure é compatível com os [pré-requisitos do Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements). Você pode modificar o nome depois de habilitar a replicação para a VM.<br/><br/> A capacidade de disco individual em computadores protegidos não deve ser superior a 1023 GB. Uma VM pode ter até 64 discos (portanto, até 64 TB).<br/><br/> Não há suporte para clusters de convidado de disco de compartilhado.<br/><br/> Se a VM de origem tiver um agrupamento NIC, ele será convertido em uma única NIC após o failover para o Azure.<br/><br/>Não há suporte para a proteção de VMs que executam o Linux com um endereço IP estático.

## Preparar para a implantação

Para se preparar para a implantação, você precisará:

1. [Configure uma rede do Azure](#set-up-an-azure-network) na qual as VMs do Azure estarão localizadas quando criadas após o failover.
2. [Configure uma conta de armazenamento do Azure](#set-up-an-azure-storage-account) para os dados replicados.
3. [Prepare os hosts do Hyper-V](#prepare-the-hyper-v-hosts) para garantir que eles possam acessar as URLs necessárias.

### Configurar uma rede do Azure

Configure uma rede do Azure. Você precisará dela para que as VMs do Azure criadas após o failover sejam conectadas à rede.

- A rede deve estar na mesma região do que a que será implantada no cofre dos Serviços de Recuperação.
- Dependendo do modelo de recurso que você deseja usar para as VMs do Azure com failover, você configurará a rede do Azure no [modo ARM](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) ou no [modo clássico](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).
- É recomendável configurar uma rede antes de começar. Caso você não faça isso, será necessário fazê-lo durante a implantação da Recuperação de Site.

> [AZURE.NOTE] [Migration of networks](../resource-group-move-resources.md) nos grupos de recursos dentro da mesma assinatura ou nas assinaturas não tem suporte para as redes usadas para a implantação do Site Recovery.

### Configurar uma conta de armazenamento do Azure

- Você precisará de uma conta de armazenamento padrão do Azure para armazenar os dados replicados para o Azure.
- Dependendo do modelo de recurso que deseja usar para failover de VMs do Azure, você definirá uma conta no [modo ARM](../storage/storage-create-storage-account.md) ou no [modo clássico](../storage/storage-create-storage-account-classic-portal.md).
- É recomendável que você configure uma conta de armazenamento antes de começar. Caso você não faça isso, será necessário fazê-lo durante a implantação da Recuperação de Site. A rede deve estar na mesma região do que o cofre dos Serviços de Recuperação.

> [AZURE.NOTE] [Migration of storage accounts](../resource-group-move-resources.md) nos grupos de recursos dentro da mesma assinatura ou nas assinaturas não tem suporte para as contas de armazenamento usadas para a implantação do Site Recovery.

### Preparar os hosts do Hyper-V

- Verifique se os hosts do Hyper-V são compatíveis com os [pré-requisitos](#on-premises-prerequisites).

### Criar um cofre dos Serviços de Recuperação

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Clique em **Novo** > **Gerenciamento** > **Backup e Recuperação de Site (OMS)**. Como alternativa, você pode clicar em **Procurar** > cofres dos **Serviços de Recuperação** > **Adicionar**.

	![Novo cofre](./media/site-recovery-hyper-v-site-to-azure/new-vault3.png)

3. Em **Nome**, especifique um nome amigável para identificar o cofre. Se você tiver mais de uma assinatura, selecione uma delas.
4. [Crie um novo grupo de recursos](../resource-group-template-deploy-portal.md) ou selecione um existente e especifique uma região do Azure. Os computadores serão replicados para essa região. Para verificar as regiões suportadas, consulte a Disponibilidade Geográfica nos [Detalhes dos Preços de Recuperação de Site do Azure](https://azure.microsoft.com/pricing/details/site-recovery/)
4. Se você deseja acessar rapidamente o cofre pelo Painel, clique em **Fixar no painel** e clique em **Criar cofre**.

	![Novo cofre](./media/site-recovery-hyper-v-site-to-azure/new-vault-settings.png)

O novo cofre será exibido no **Painel** > **Todos os recursos** e na folha **Cofres dos Serviços de Recuperação** principal.

## Introdução

A Recuperação de Site fornece uma experiência de introdução que ajuda você a implantar o mais rápido possível. A Introdução verifica os pré-requisitos e orienta você durante as etapas de implantação da Recuperação de Site na ordem correta.

Na Introdução, você seleciona o tipo de computador que deseja replicar e para onde deseja replicar. Configure servidores locais, contas de armazenamento e redes do Azure. Você crie políticas de replicação e execute o planejamento de capacidade. Depois de configurar sua infraestrutura, habilite a replicação para VMs. Você pode executar failovers para computadores específicos ou criar planos de recuperação para failover de vários computadores.

Inicie a Introdução ao escolher como deseja implantar a Recuperação de Site. O fluxo de Introdução mudará ligeiramente, dependendo dos requisitos da sua replicação.



## Etapa 1: Escolher os objetivos de proteção

Selecione o que você deseja replicar e para onde deseja replicar.

1. Na folha **Cofres dos Serviços de Recuperação**, selecione seu cofre e clique em **Configurações**.
2. Em **Configurações** > **Introdução**, clique em **Recuperação de Site** > **Etapa 1: Preparar a Infraestrutura** > **Meta de proteção**.

	![Escolher metas](./media/site-recovery-hyper-v-site-to-azure/choose-goals.png)

3. Em **Objetivo de proteção**, selecione **Para o Azure** e selecione **Sim, com o Hyper-V**. Selecione **Não** para confirmar que você não está usando o VMM. Em seguida, clique em **OK**.

	![Escolher metas](./media/site-recovery-hyper-v-site-to-azure/choose-goals2.png)


## Etapa 2: Configurar o ambiente de origem

Configure o site do Hyper-V, instale o Provedor do Azure Site Recovery e o agente dos Serviços de Recuperação do Azure em hosts do Hyper-V e registre os hosts no cofre.


1. Clique em **Etapa 2: Preparar a Infraestrutura** > **Origem**. Para adicionar um novo site do Hyper-V como um contêiner para seus hosts ou clusters do Hyper-V, clique em **+ Site Hyper-V**.

	![Configurar origem](./media/site-recovery-hyper-v-site-to-azure/set-source1.png)

2. Na folha **Criar site do Hyper-V**, especifique um nome para o site. Em seguida, clique em **OK**. Selecione o site que você acabou de criar.

	![Configurar origem](./media/site-recovery-hyper-v-site-to-azure/set-source2.png)

3. Clique em **+Servidor Hyper-V** para adicionar um servidor ao site.
4. Em **Adicionar Servidor** > **Tipo de servidor**, verifique se **Servidor Hyper-V** é exibido. Certifique-se de que o servidor Hyper-V que você deseja adicionar seja compatível com os [pré-requisitos](#on-premises-prerequisites) e seja capaz de acessar as URLs especificadas.
4. Baixe o arquivo de instalação do Provedor do Azure Site Recovery. Você executará esse arquivo para instalar o Provedor e o agente dos Serviços de Recuperação em cada host do Hyper-V.
5. Baixe a chave do registro. Você precisará dela quando executar a instalação. A chave é válida por 5 dias após ser gerada.

	![Configurar origem](./media/site-recovery-hyper-v-site-to-azure/set-source3.png)

6. Execute o arquivo de configuração do Provedor em cada host que você adicionou ao site do Hyper-V. Se você estiver instalando em um cluster do Hyper-V, execute a instalação em cada nó de cluster. Instalar e registrar cada nós de Cluster do Hyper-V garante que as máquinas virtuais permanecerão protegidas mesmo se migrarem entre nós.

### Instalar o Provedor e o agente

1. Execute o de arquivo de instalação do Provedor.
2. Em **Microsoft Update**, você pode aceitar as atualizações para que as atualizações do Provedor sejam instaladas de acordo com a política do Microsoft Update.
3. Em **Instalação**, aceite ou modifique o local de instalação padrão do Provedor e clique em **Instalar**.
4. Na página **Configurações do Cofre**, clique em **Procurar** para selecionar o arquivo da chave do cofre baixado. Especifique a assinatura do Azure Site Recovery, o nome do cofre e o site de Hyper-V ao qual pertence o servidor Hyper-V.

	![Registros do servidor](./media/site-recovery-hyper-v-site-to-azure/provider3.png)

5\. Em **Configurações de Proxy**, especifique como o Provedor que será instalado no servidor se conectará ao Azure Site Recovery pela Internet.

- Se quiser que o Provedor se conecte diretamente, selecione **Connect directly without a proxy (Conectar diretamente sem um proxy)**.
- Se quiser se conectar ao proxy que está configurado atualmente no servidor, selecione **Connect with existing proxy settings (Conectar com as configurações de proxy existentes)**.
- Se o proxy existente exigir autenticação ou se você quiser usar um proxy personalizado para a conexão do Provedor, selecione **Connect with custom proxy settings (Conectar com as configurações personalizadas do proxy)**.
- Se você usar um proxy personalizado, precisará especificar o endereço, a porta e as credenciais
- Se você estiver usando um proxy, verifique se as URLs descritas nos [pré-requisitos](#on-premises-prerequisites) são permitidas através dele.

	![internet](./media/site-recovery-hyper-v-site-to-azure/provider7.PNG)

6\. Quando a instalação terminar, clique em **Registrar** para registrar o servidor no cofre. ![Local de instalação](./media/site-recovery-hyper-v-site-to-azure/provider2.png)

7\. Após a conclusão do registro, os metadados do servidor Hyper-V são recuperados pelo Azure Site Recovery e o servidor é exibido na folha **Configurações** > **Infraestrutura do Site Recovery** > **Hosts do Hyper-V**.


### Instalação de linha de comando

O Provedor e o agente do Azure Site Recovery também podem ser instalados usando a linha de comando a seguir. Esse método pode ser usado para instalar o provedor em um Núcleo de Servidor para o Windows Server 2012 R2.

1. Baixar o arquivo de instalação do provedor e a chave de registro em uma pasta. Por exemplo, C:\\ASR.
2. Em um prompt de comando com privilégios elevados, execute estes comandos para extrair o instalador do Provedor:

	    	C:\Windows\System32> CD C:\ASR
	    	C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
3. Execute este comando para instalar os componentes:

			C:\ASR> setupdr.exe /i

4. Execute estes comandos para registrar o servidor no cofre: CD C:\\Program Files\\Microsoft Azure Site Recovery Provider\\ C:\\Program Files\\Microsoft Azure Site Recovery Provider> DRConfigurator.exe /r /Friendlyname <nome amigável do servidor> /Credenciais <caminho do arquivo de credenciais> em que:

- **/Credentials**: parâmetro obrigatório que especifica o local no qual o arquivo da chave de registro está localizado
- **/FriendlyName**: parâmetro obrigatório para o nome do servidor do host Hyper-V que aparece no portal do Azure Site Recovery.
- **/proxyAddress**: parâmetro opcional que especifica o endereço do servidor proxy.
- **/proxyport**: parâmetro opcional que especifica a porta do servidor proxy.
- **/proxyUsername**: parâmetro opcional que especifica o nome de usuário de Proxy (se o proxy exige autenticação).
- **/proxyPassword**: parâmetro opcional que especifica a senha para autenticação com o servidor proxy (se o proxy exigir autenticação).


## Etapa 3: Configurar o ambiente de origem

Especifique a conta de armazenamento do Azure a ser usada para a replicação e a rede do Azure à qual as VMs do Azure se conectarão após o failover.

1.	Clique em **Preparar infraestrutura** > **Destino** e selecione a assinatura do Azure que você deseja usar.
2.	Especifique o modelo de implantação que você deseja usar para as VMs após o failover.
3.	A Recuperação de Site verifica se você tem uma ou mais contas de armazenamento e redes do Azure compatíveis.

	![Armazenamento](./media/site-recovery-hyper-v-site-to-azure/select-target.png)

4.	Se você não tiver criado uma conta de armazenamento e se desejar criar uma usando o ARM, clique em **+Conta de armazenamento** para fazer isso de forma embutida. Na folha **Criar conta de armazenamento**, especifique um nome de conta, um tipo, uma assinatura e uma localização. A conta deve estar no mesmo local do que o cofre dos Serviços de Recuperação.

	![Armazenamento](./media/site-recovery-hyper-v-site-to-azure/gs-createstorage.png)

	Se você quiser criar uma conta de armazenamento usando o modelo clássico, terá de fazer isso [no portal do Azure](../storage/storage-create-storage-account-classic-portal.md).
	
5.	Se você não tiver criado uma rede do Azure e se quiser criar uma usando o ARM, clique em **+Rede** para fazer isso de forma embutida. Na folha **Criar rede virtual**, especifique um nome de rede, um intervalo de endereços, detalhes de sub-rede, uma assinatura e uma localização. A rede deve estar no mesmo local do que o cofre dos Serviços de Recuperação.

	![Rede](./media/site-recovery-hyper-v-site-to-azure/gs-createnetwork.png)

	Se você quiser criar uma rede usando o modelo clássico, terá de fazer isso no [portal do Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).
	

## Etapa 4: Definir as configurações da replicação

1. Para criar uma nova política de replicação, clique em **Preparar a Infraestrutura** > **Configurações de Replicação** > **+Criar e associar**.

	![Rede](./media/site-recovery-hyper-v-site-to-azure/gs-replication.png)

2. Em **Criar e associar política**, especifique um nome de política.
3. Em **Frequência de cópia**, especifique com que frequência você deseja replicar os dados delta após a replicação inicial (a cada 30 segundos, 5 ou 15 minutos).
4. Em **Retenção do ponto de recuperação**, especifique, em horas, qual será a duração da janela de retenção para cada ponto de recuperação. Os computadores protegidos podem ser recuperados para qualquer ponto nessa janela.
6. Em **Frequência do instantâneo consistente com aplicativo**, especifique com que frequência (1 a 12 horas) são criados os pontos de recuperação que incluam instantâneos consistentes com aplicativos. O Hyper-V usa dois tipos de instantâneos: um instantâneo padrão, que fornece um instantâneo incremental de toda a máquina virtual, e um instantâneo consistente com aplicativos, que cria um instantâneo pontual dos dados do aplicativo na máquina virtual. Os instantâneos consistentes com aplicativos usam o Serviço de VSS (Cópias de Sombra de Volume) para garantir que os aplicativos estejam em um estado consistente quando o instantâneo for obtido. Observe que, se você habilitar instantâneos consistentes com aplicativos, isso afetará o desempenho de aplicativos executados em máquinas virtuais de origem. Verifique se o valor definido é menor do que o número de pontos de recuperação adicionais que você configurar.
3. Em **Hora de início para replicação inicial**, especifique quando a replicação inicial deve começar. A replicação ocorre pela largura de banda da Internet, pois talvez você queira agendá-la fora dos horários de pico. Em seguida, clique em **OK**.

	![Política de replicação](./media/site-recovery-hyper-v-site-to-azure/gs-replication2.png)

Quando você cria uma nova política, ela é automaticamente associada ao site do Hyper-V. Clique em **OK**. Você pode associar um site do Hyper-V (e as VMs nele) com várias políticas de replicação em **Configurações** > **Replicação** > nome da política > **Associar Site do Hyper-V**.

## Etapa 5: Planejamento de capacidade

Agora que você tem a infraestrutura básica configurada, pode pensar sobre o planejamento de capacidade e descobrir se precisa de recursos adicionais.

A Recuperação de Site fornece um planejador de capacidade para ajudar você a alocar os recursos certos para seu ambiente de origem, para os componentes da recuperação de site, para a rede e para o armazenamento. Você pode executar o planejador no modo rápido para estimativas baseadas em um número médio de VMs, de discos e de armazenamento, ou no modo detalhado, no qual você vai inserir números no nível de carga de trabalho. Antes de começar, será necessário:

- Reunir informações sobre seu ambiente de replicação, inclusive VMs, discos por VMs e armazenamento por disco.
- Estimar a taxa de alteração (variação) diária de dados replicados. Você pode usar o [Planejador de Capacidade para Réplica do Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) para ajudá-lo a fazer isso.

1.	Clique em **Baixar** para baixar a ferramenta e, então, executá-la. [Leia o artigo](site-recovery-capacity-planner.md) que acompanha a ferramenta.
2.	Quando terminar, selecione **Sim** em **Você executou o Planejador de Capacidade**?

	![Planejamento da capacidade](./media/site-recovery-hyper-v-site-to-azure/gs-capacity-planning.png)

### Considerações sobre largura de banda de rede

Você pode usar a ferramenta de planejador de capacidade para calcular a largura de banda necessária para a replicação (replicação inicial e depois a delta). Para controlar a quantidade de uso de largura de banda para a replicação, você tem algumas opções:

- **Restringir a largura de banda**: o tráfego do Hyper-V replicado para o Azure passa por um host específico do Hyper-V. Você pode limitar a largura de banda no servidor host.
- **Ajustar largura de banda**: você pode influenciar a largura de banda usada para a replicação usando algumas chaves do Registro.

#### Limitar a largura de banda

1. Abra o snap-in MMC do Backup do Microsoft Azure no servidor host do Hyper-V. Por padrão, um atalho para o Backup do Microsoft Azure está disponível na área de trabalho ou C:\\Arquivos de Programas\\Microsoft Azure Recovery Services Agent\\bin\\wabadmin.
2. No snap-in, clique em **Alterar Propriedades**.
3. Na guia **Limitação**, selecione **Habilitar limitação de uso de largura de banda da Internet para operações de backup** e defina os limites do horário comercial e não comercial. Os intervalos válidos são de 512 Kbps a 102 Mbps por segundo.

	![Limitar a largura de banda](./media/site-recovery-hyper-v-site-to-azure/throttle2.png)

Você também pode usar o cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) para definir a limitação. Veja um exemplo:

    $mon = [System.DayOfWeek]::Monday 
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** indica que nenhuma limitação é necessária.


#### Influência da largura de banda de rede

1. No Registro, navegue até **HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows Azure Backup\\Replication**.
	- Para influenciar o tráfego de largura de banda em um disco de replicação, modifique o valor de **UploadThreadsPerVM** ou crie a chave caso ela não exista.
	- Para influenciar a largura de banda para o tráfego de failback do Azure, modifique o valor **DownloadThreadsPerVM**.
2. O valor padrão é 4. Em uma rede "sobreprovisionada", os valores padrão dessas chaves do registro precisam ser alterados. O máximo é 32. Monitore o tráfego para otimizar o valor.

## Etapa 6: Habilitar a replicação

Agora habilite a replicação da seguinte maneira:

1. Clique em **Etapa 2: replicar aplicativo** > **Origem**. Depois de habilitar a replicação pela primeira vez, clique em **+Replicar** no cofre para habilitar a replicação para outros computadores.

	![Habilitar a replicação](./media/site-recovery-hyper-v-site-to-azure/enable-replication.png)

2. No folha **Origem** > selecione o site do Hyper-V. Em seguida, clique em **OK**.
3. Em **Destino**, selecione a assinatura de cofre e o modelo de failover que você deseja usar no Azure (gerenciamento de recursos ou clássico) após o failover.
4. Selecione a conta de armazenamento que você deseja usar. Se quiser usar uma conta de armazenamento diferente da que você tem, poderá [criar uma](#set-up-an-azure-storage-account). Para criar uma conta de armazenamento usando o modelo ARM, clique em **Criar novo**. Se você quiser criar uma conta de armazenamento usando o modelo clássico, terá de fazer isso [no portal do Azure](../storage/storage-create-storage-account-classic-portal.md). Em seguida, clique em **OK**.
5.  Selecione a rede e a sub-rede do Azure às quais as VMs do Azure se conectarão quando forem rotacionadas após o failover. Selecione **Configurar agora para computadores selecionados** para aplicar a configuração de rede a todos os computadores selecionados para proteção. Selecione **Configurar mais tarde** para selecionar a rede do Azure por computador. Se quiser usar uma rede diferente da que você tem, poderá [criar uma](#set-up-an-azure-network). Para criar uma rede usando o modelo ARM, clique em **Criar nova**. Se você quiser criar uma rede usando o modelo clássico, fará isso [no portal do Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Selecione uma sub-rede, se aplicável. Em seguida, clique em **OK**.

	![Habilitar a replicação](./media/site-recovery-hyper-v-site-to-azure/enable-replication11.png)

6. Em **Máquinas Virtuais** > **Selecionar máquinas virtuais**, clique e selecione cada máquina que você deseja replicar. Você só pode selecionar computadores para os quais a replicação pode ser habilitada. Em seguida, clique em **OK**.

	![Habilitar a replicação](./media/site-recovery-hyper-v-site-to-azure/enable-replication5.png)

11. Em **Propriedades** > **Configurar propriedades**, selecione o sistema operacional para as VMs selecionadas e o disco do sistema operacional. Verifique se o nome da VM do Azure (Nome de Destino) está em conformidade com os [requisitos de máquina virtual do Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements) e modifique-o, se for necessário. Em seguida, clique em **OK**. Você pode definir propriedades adicionais posteriormente.

	![Habilitar a replicação](./media/site-recovery-hyper-v-site-to-azure/enable-replication6.png)

12. Em **Configurações de replicação** > **Definir configurações de replicação**, selecione a política de replicação que você deseja aplicar para as VMs protegidas. Em seguida, clique em **OK**. Você pode modificar a política de replicação em **Configurações** > **Políticas de replicação** > nome da política > **Editar Configurações**. As alterações aplicadas serão usadas para computadores que já estejam replicando e para novas máquinas.

	![Habilitar a replicação](./media/site-recovery-hyper-v-site-to-azure/enable-replication7.png)

Você pode acompanhar o progresso do trabalho **Habilitar Proteção** em **Configurações** > **Trabalhos** > **Trabalhos de Recuperação de Site**. Após o trabalho de **Finalizar Proteção** ser executado, o computador estará pronto para failover.

### Exibir e gerenciar as propriedades da VM

É recomendável que você verifique as propriedades do computador de origem.

1. Clique em **Configurações** > **Itens Protegidos** > **Itens Replicados** > e selecione o computador.

	![Habilitar a replicação](./media/site-recovery-hyper-v-site-to-azure/test-failover1.png)

2. Em **Propriedades**, você pode exibir informações de replicação e de failover para a VM.

	![Habilitar a replicação](./media/site-recovery-hyper-v-site-to-azure/test-failover2.png)

3. Em **Computação e Rede** > **Propriedades de computação**, você pode especificar o nome da VM do Azure e o tamanho de destino. Modifique o nome para que ele fique em conformidade com os requisitos do Azure, se for necessário. Você também pode exibir e modificar as informações sobre a rede de destino, a sub-rede e o endereço IP que será atribuído à VM do Azure. Observe o seguinte:

	- Você pode definir o endereço IP de destino. Se você não fornecer um endereço, o computador com failover usará o DHCP. Se você definir um endereço que não esteja disponível no failover, o failover falhará. O mesmo endereço IP de destino poderá ser usado para failover de teste caso o endereço esteja disponível na rede de failover de teste.
	- O número de adaptadores de rede é determinado pelo tamanho especificado para a máquina virtual de destino, como a seguir:

		- Se o número de adaptadores de rede na máquina de origem for menor ou igual ao número de adaptadores permitido para o tamanho da máquina de destino, o destino terá o mesmo número de adaptadores que a origem.
		- Se o número de adaptadores para máquina virtual de origem exceder o número permitido para o tamanho de destino e o tamanho máximo de destino será usado.
		- Por exemplo, se uma máquina de origem tiver dois adaptadores de rede e o tamanho da máquina de destino oferecer suporte a quatro, a máquina de destino terá dois adaptadores. Se a máquina de origem tiver dois adaptadores, mas o tamanho de destino com suporte oferecer suporte apenas a uma máquina de destino, ela terá apenas um adaptador.
		- Se a máquina virtual tiver vários adaptadores de rede, todos eles se conectarão à mesma rede.

	![Habilitar a replicação](./media/site-recovery-hyper-v-site-to-azure/test-failover4.png)

5.	Em **Discos**, você pode ver o sistema operacional e os discos de dados na VM que serão replicados.


## Etapa 7: testar a implantação

Para testar a implantação, você pode executar um failover de teste para uma única máquina virtual ou um plano de recuperação que contém uma ou mais máquinas virtuais.


### Preparar para failover de teste

- Para executar um failover de teste, é recomendável que você crie uma nova rede do Azure que esteja isolada da rede de produção do Azure (esse é o comportamento padrão quando você cria uma nova rede no Azure). [Saiba mais](site-recovery-failover.md#run-a-test-failover) sobre a execução de failovers de teste.
- Para obter o melhor desempenho ao fazer um failover para o Azure, instale o Agente do Azure no computador protegido. Ele torna a inicialização mais rápida e ajuda na solução de problemas. Instale o agente do [Linux](https://github.com/Azure/WALinuxAgent) ou do [Windows](http://go.microsoft.com/fwlink/?LinkID=394789).
- Para testar totalmente a implantação, você precisará de uma infraestrutura para o computador replicado funcionar como esperado. Se você quiser testar o Active Directory e o DNS, poderá criar uma máquina virtual como um controlador de domínio com DNS e replicar isso para o Azure usando o Azure Site Recovery. Leia mais em [considerações sobre failover de teste para o Active Directory](site-recovery-active-directory.md#considerations-for-test-failover).
- Se você quiser executar um failover não planejado em vez de um teste de failover, observe o seguinte:

	- Se possível, você deve desligar os computadores primários antes de fazer um failover não planejado. Isso faz com que você não tenha os computadores de origem e de réplica em execução ao mesmo tempo.
	- Quando você executa um failover não planejado, ele interrompe a replicação de dados de computadores primários para que qualquer delta de dados não seja transferido após o início de um failover não planejado. Além disso se você executar um failover não planejado em um plano de recuperação, ele será executado até ser concluído, mesmo se ocorrer um erro.
	
### Preparar para conectar VMs do Azure após o failover

Se você quiser se conectar às VMs do Azure usando o RDP após o failover, faça o seguinte:

**No computador local antes do failover**:

- Para acesso pela Internet, habilite o RDP, certifique-se de que as regras de TCP e UDP sejam adicionadas para o **Público** e verifique se o RDP foi permitido no **Firewall do Windows** -> **Aplicativos e recursos permitidos** para todos os perfis.
- Para acesso por meio de uma conexão site a site, habilite o RDP na máquina e verifique se o RDP foi permitido no **Firewall do Windows** -> **Aplicativos e recursos permitidos** para as redes **Domínio** e **Particular**.
- Instale o [Agente de VM do Azure](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) no computador local.
- Certifique-se de que a política de SAN do sistema operacional esteja definida como OnlineAll. [Saiba mais](https://support.microsoft.com/kb/3031135)
- Desative o serviço IPSec antes de executar o failover.

**Na VM do Azure após o failover**:

- Adicione um Endereço IP público à NIC associada à VM do Azure para permitir o RDP.
- Verifique se não há nenhuma política de domínio que o impeça de se conectar a uma máquina virtual usando um endereço público.
- Tente se conectar. Se você não puder se conectar, verifique se a VM está em execução. Para obter mais dicas de solução de problemas, leia este [artigo](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Se você quiser acessar uma VM do Azure que esteja executando o Linux após o failover usando um cliente do Secure Shell (ssh), faça o seguinte:

**No computador local antes do failover**:

- Verifique se o serviço Secure Shell na VM do Azure está definido para iniciar automaticamente na inicialização do sistema.
- Verifique se as regras de firewall permitem uma conexão SSH com ele.

**Na VM do Azure após o failover**:

- As regras de grupo de segurança de rede na VM com failover e a sub-rede do Azure à qual ela está conectada precisam permitir conexões de entrada para a porta SSH.
- Um ponto de extremidade público deve ser criado para permitir conexões de entrada na porta SSH (porta TCP 22, por padrão).
- Se a VM for acessada por meio de uma conexão VPN (Rota Expressa ou VPN site a site), então o cliente poderá ser usado para se conectar diretamente à VM via SSH.

### Execute um teste de failover

Para executar o failover de teste, faça o seguinte:

1. Para fazer failover em uma única VM, em **Configurações** > **Itens Replicados**, clique na VM > **+Failover de Teste**.

	![Failover de teste](./media/site-recovery-hyper-v-site-to-azure/run-failover1.png)

2. Para fazer failover de um plano de recuperação, em **Configurações** > **Planos de Recuperação**, clique com o botão direito do mouse no plano > **Failover de Teste**. Para criar um plano de recuperação, [siga estas instruções](site-recovery-create-recovery-plans.md).

3. Em **Failover de Teste**, selecione a rede do Azure à qual as VMs do Azure serão conectadas após o failover.

	![Failover de teste](./media/site-recovery-hyper-v-site-to-azure/run-failover2.png)

4. Clique em **OK** para iniciar o failover. Você pode acompanhar o andamento clicando na VM para abrir suas propriedades ou no trabalho **Failover de Teste** em **Configurações** > **Trabalhos do Site Recovery**.
5. Quando o failover atingir a fase **Testes concluídos**, faça o seguinte:
	1. Visualize a máquina virtual de réplica no portal do Azure. Verifique se a máquina virtual foi iniciada com êxito.
	2. Se tiver configurado para máquinas virtuais de acesso a rede local, você pode iniciar uma conexão de área de trabalho remota para a máquina virtual.
	3. Clique em **Concluir o teste** para concluí-lo.
	4. Clique em **Observações** para gravar e salvar observações associadas ao failover de teste.
	5. Clique em **Failover de teste concluído**. Limpa o ambiente de teste para desligar automaticamente e excluir a máquina virtual de teste.
	6. Neste ponto, todos os elementos ou máquinas virtuais criadas automaticamente pela Recuperação de Site durante o teste de failover são excluídos. Quaisquer elementos adicionais que você criou para o failover de teste não serão excluídos.
	
	> [AZURE.NOTE] Se um failover de teste continuar por mais de duas semanas, ele será concluído à força.

6. Após a conclusão do failover, você também deve ver a réplica do computador do Azure no portal do Azure > **Máquinas Virtuais**. Verifique se a VM é do tamanho apropriado, se está conectada à rede adequada e se está em execução.
7. Se você tiver se [preparado para conexões após o failover](#prepare-to-connect-to-Azure-VMs-after-failover), deverá ser capaz de se conectar à VM do Azure.


## Monitorar a implantação

Veja como você pode monitorar as definições de configuração, o status e a integridade para a implantação da Recuperação de Site:

1. Clique no nome do cofre para acessar o painel **Conceitos básicos**. Neste painel, você pode ver os trabalhos da Recuperação de Site, o status da replicação, os planos de recuperação, a integridade do servidor e os eventos. Você pode personalizar os Conceitos básicos para mostrar os blocos e os layouts mais úteis, incluindo o status de outros cofres da Recuperação de Site e do Backup.

	![Conceitos básicos](./media/site-recovery-hyper-v-site-to-azure/essentials.png)

2. No bloco **Integridade**, você pode monitorar os servidores de site que estejam enfrentando o problema, além dos eventos gerados pelo Site Recovery nas últimas 24 horas.
3. Você pode gerenciar e monitorar a replicação nos blocos **Itens Replicados**, **Planos de Recuperação** e **Trabalhos de Recuperação de Site**. Você pode analisar detalhadamente os trabalhos em **Configurações** -> **Trabalhos** -> **Trabalhos de Recuperação de Site**.





## Próximas etapas

Depois que a implantação estiver configurada e em funcionamento, [saiba mais](site-recovery-failover.md) sobre o os diferentes tipos de failover.

<!---HONumber=AcomDC_0921_2016-->