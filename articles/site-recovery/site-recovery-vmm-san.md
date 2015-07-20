<properties 
	pageTitle="Configurar a proteção entre sites locais do VMM com SAN" 
	description="O Azure Site Recovery coordena a replicação, o failover e a recuperação de máquinas virtuais Hyper-V entre os sites do VMM locais usando a replicação SAN." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/07/2015" 
	ms.author="raynew"/>

# Configurar a proteção entre sites locais do VMM com SAN

O Azure Site Recovery contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) gerenciando replicação, failover e recuperação de máquinas virtuais em vários cenários de implantação. Leia sobre possíveis cenários de implantação na [Visão geral sobre o Azure Site Recovery](site-recovery-overview.md).

Este passo a passo descreve como implantar a Recuperação de Site a fim de coordenar e automatizar a proteção de cargas de trabalho de máquina virtual em execução em um servidor Hyper-V local gerenciado pelo System Center VMM para outro site local do VMM, usando a replicação SAN (armazenamento com base em matriz).

As vantagens de negócios incluem:

- O fornecimento de uma solução corporativa de replicação escalonável automatizada pela Recuperação de Site.
- Utilização dos recursos de replicação de SAN fornecidos pelos parceiros de armazenamento corporativo por meio do armazenamento fibre channel e iSCSI. Confira nossos [Parceiros de armazenamento SAN](http://go.microsoft.com/fwlink/?LinkId=518669).
- Aproveita de sua infraestrutura de SAN atual para proteger aplicativos fundamentais implantados em clusters Hyper-V. 
- Fornecimento de suporte para clusters convidados.
- Garantia de consistência da replicação em camadas diferentes de um aplicativo, com replicação sincronizada para RTO e RPO baixo, e replicação assíncrona para alta flexibilidade, dependendo das capacidades da matriz de armazenamento.  
- A integração com o VMM fornece gerenciamento de SAN no console do VMM, e o SMI-S no VMM descobre o armazenamento existente.  


## Sobre este artigo

O artigo inclui uma visão geral e pré-requisitos de implantação. Ele o orienta pela configuração e habilitação da replicação no VMM e no cofre da Recuperação de Site. Você irá detectar e classificar o armazenamento de SAN no VMM, provisionar LUN e alocar armazenamento para clusters Hyper-V. Ele é concluído com o teste do failover para verificar se tudo está funcionando conforme o esperado.

Se tiver problemas, publique suas perguntas no [Fórum de Serviços de Recuperação do Azure](http://go.microsoft.com/fwlink/?LinkId=313628).

## Visão geral
Este cenário protege suas cargas de trabalho por meio do backup das máquinas virtuais Hyper-V de um site local do VMM para outro usando a replicação SAN.

![Arquitetura de SAN](./media/site-recovery-vmm-san/ASRSAN_Arch.png)

### Componentes do cenário

- **Máquinas virtuais locais**: seus servidores Hyper-V locais gerenciados em nuvens privadas do VMM contêm máquinas virtuais que você deseja proteger.
- **Servidores locais do VMM local**: um ou mais servidores do VMM em execução no site primário que você deseja proteger e no site secundário.
- **Armazenamento SAN**: uma matriz de SAN no site primário e outra no site secundário
-  **Cofre do Azure Site Recovery**: o cofre coordena e orquestra a réplica de dados, o failover e a recuperação entre os sites locais.
- **Provedor Azure Site Recovery**: o provedor é instalado em cada servidor VMM.

## Antes de começar

### Pré-requisitos do Azure

- Você precisará de uma conta do [Microsoft Azure](http://azure.microsoft.com/). Você pode começar com uma [avaliação gratuita](http://aka.ms/try-azure). Obter informações de preço em [Detalhes de Preços do Gerenciador de Recuperação de Site do Azure](http://go.microsoft.com/fwlink/?LinkId=378268).

### Pré-requisitos do VMM

- Você precisará de pelo menos um servidor VMM em cada site local, implantado como um servidor físico ou virtual autônomo ou como um cluster virtual, em execução no System Center 2012 R2 com o [pacote cumulativo de atualizações 5.0 do VMM](http://go.microsoft.com/fwlink/?LinkId=517707).
- Você deve ter pelo menos uma nuvem no servidor VMM primário que quer proteger e outra no servidor VMM secundário. A nuvem principal que você deseja proteger deve conter o seguinte:
	- Um ou mais grupos de hosts do VMM
	- Um ou mais clusters de Hyper-V em cada grupo de hosts.
	- Um ou mais máquinas virtuais localizadas no servidor Hyper-V de origem na nuvem.
		
### Requisitos do Hyper-V

- Você precisará de um cluster de host do Hyper-V implantado nos sites primários e secundários, executando pelo menos o Windows Server 2012 com as atualizações mais recentes.

### Pré-requisitos de SAN

- Com a replicação de SAN, você pode replicar as máquinas virtuais de cluster convidado com o armazenamento iSCSI ou fibre channel, ou usando discos rígidos virtuais compartilhados (vhdx). Os pré-requisitos de SAN são os seguintes:
	- Você precisará de duas matrizes de SAN, configuradas, uma no site primário e um secundário.
	- A infraestrutura de rede deve ser configurada entre as matrizes. Devem ser configurados o emparelhamento e a replicação. Licenças de replicação devem ser configuradas de acordo com os requisitos de matriz de armazenamento.
	- A rede deve ser definida entre os servidores de host Hyper-V e a matriz de armazenamento para que os hosts possam se comunicar com os LUNs de armazenamento usando ISCSI ou Fibre Channel.
	- Veja uma lista de [matrizes de armazenamento com suporte](http://go.microsoft.com/fwlink/?LinkId=518669).
	- Provedores de SMI-S, fornecidos pelos fabricantes de matriz de armazenamento, devem ser instalados e as matrizes de SAN devem ser gerenciadas pelo Provedor. Configure o Provedor de acordo com a documentação apropriada.
	- Verifique se o provedor de SMI-S para a matriz está em um servidor que o servidor do VMM pode acessar na rede pelo endereço IP ou FQDN.
	- Cada matriz de SAN deve ter um ou mais pools de armazenamento disponíveis para uso nessa implantação. O servidor VMM no site primário precisará gerenciar a matriz primária e o servidor VMM secundário gerenciará a matriz secundária.
	- O servidor VMM no site primário deverá gerenciar a matriz primária e o servidor VMM secundário gerenciará a matriz secundária.

### Pré-requisitos de rede

Você também pode definir o mapeamento de rede para assegurar que as máquinas virtuais de réplica sejam colocadas da melhor forma possível em servidores host Hyper-V após o failover, e que elas possam se conectar às redes VM apropriadas. Observe que:

- Quando o mapeamento de rede está habilitado, uma máquina virtual no local principal será conectada a uma rede e sua réplica no local de destino será conectada à rede mapeada.
- Se você não configurar o mapeamento de rede, as máquinas virtuais não serão conectadas às redes VM após o failover.
- Redes de VM devem ser configuradas no VMM. Para obter detalhes sobre como configurar as redes de VM e gateways no VMM
- As máquinas virtuais no servidor VMM de origem devem estar conectadas a uma rede de VM. Essa rede de VM de origem deve estar vinculada a uma rede lógica associada à nuvem.


## Etapa 1: preparar a infraestrutura do VMM

Para preparar a infraestrutura do VMM, é necessário:

1. Certificar-se de que as nuvens do VMM estejam configuradas
2. Integre e classifique o armazenamento de SAN no VMM
3. Criar LUNs e alocar armazenamento
4. Criar grupos de replicação
5. Configurar redes de VMM

### Certificar-se de que as nuvens do VMM estejam configuradas

A Recuperação de Site coordena a proteção de máquinas virtuais localizadas nos servidores do host Hyper-V em nuvens do VMM. Será necessário garantir que essas nuvens estejam configuradas corretamente antes de começar a implantação da Recuperação de Site. Estas são algumas fontes válidas:

- [Novidades na nuvem privada](http://go.microsoft.com/fwlink/?LinkId=324952)
- [VMM 2012 e as nuvens](http://go.microsoft.com/fwlink/?LinkId=324956) no blog de Gunter Danzeisen.
- [Configurando a malha de nuvem do VMM](https://msdn.microsoft.com/library/azure/dn883636.aspx#BKMK_Fabric)
- [Criando uma nuvem privada no VMM](http://go.microsoft.com/fwlink/?LinkId=324953)
- [Passo a passo: criando nuvens privadas](http://go.microsoft.com/fwlink/?LinkId=324954) no blog de Keith Mayer.

### Integre e classifique o armazenamento de SAN no VMM


Adicionar e classificar SANs no console do VMM:

1. No espaço de trabalho **Malha**, clique em **Armazenamento**. Clique em **Início** > **Adicionar Recursos** > **Dispositivos de Armazenamento** para iniciar o Assistente para Adicionar Dispositivos de Armazenamento.
2. Na página **Selecionar um tipo de provedor de armazenamento**, selecione **Dispositivos SAN e NAS descobertos e gerenciados por um provedor de SMI-S**.

	![Tipo de provedor](./media/site-recovery-vmm-san/SRSAN_Providertype.png)

3. Na página **Especificar o protocolo e o endereço do armazenamento do provedor de SMI-S** selecione **SMI-S CIMXML** e especifique as configurações para conectar-se ao provedor.
4. No **Endereço IP do Provedor ou FQDN** e **porta TCP/IP**, especifique as configurações para se conectar ao provedor. Você pode usar uma conexão SSL apenas para CIMXML de SMI-S.

	![Conexão com o provedor](./media/site-recovery-vmm-san/SRSAN_ConnectSettings.png)

5. Em **Executar como conta** especifique uma conta Executar como VMM que possa acessar o provedor ou crie uma nova conta.
6. Na página **Reunir Informações**, o VMM tenta automaticamente detectar e importar as informações do dispositivo de armazenamento. Para repetir a descoberta, clique em **Examinar Provedor**. Se o processo de descoberta for bem-sucedido, as matrizes de armazenamento, pools de armazenamento, fabricante, modelo e capacidade descobertos serão listados na página.

	![Descoberta do armazenamento](./media/site-recovery-vmm-san/SRSAN_Discover.png)

7. Em **Selecionar pools de armazenamento para colocar sob gerenciamento e atribuir uma classificação**, selecione os pools de armazenamento que o VMM gerenciará e atribua-os uma classificação. As informações de LUN serão importadas dos pools de armazenamento. Crie LUNs de acordo com os aplicativos que você precisa proteger, seus requisitos de capacidade e o requisito para os que precisam replicar juntos.

	![Classificação do armazenamento](./media/site-recovery-vmm-san/SRSAN_Classify.png)

### Criar LUNs e alocar armazenamento

1. Depois que o armazenamento SAN estiver integrado ao VMM, crie LUNs (unidades lógicas (provisão).

- [Como selecionar um método para criar unidades lógicas no VMM](http://go.microsoft.com/fwlink/?LinkId=518490)
- [Como provisionar armazenamento de unidades lógicas no VMM](http://go.microsoft.com/fwlink/?LinkId=518491)

2. Aloque a capacidade de armazenamento ao cluster de host Hyper-V para que o VMM possa implantar dados da máquina virtual para o armazenamento provisionado: 

	- Antes de alocar o armazenamento ao cluster, você precisará alocá-lo ao grupo de hosts do VMM no qual o cluster reside. Confira [Como alocar unidades lógicas de armazenamento para um grupo de hosts](http://go.microsoft.com/fwlink/?LinkId=518493) e [Como alocar pools de armazenamento a um grupo de hosts](http://go.microsoft.com/fwlink/?LinkId=518492).</a>
	- Em seguida, aloque a capacidade de armazenamento para o cluster conforme descrito em [Como configurar o armazenamento em um cluster de host Hyper-V no VMM](http://go.microsoft.com/fwlink/?LinkId=513017).</a>.

### Criar grupos de replicação

Crie um grupo de replicação que inclui todos os LUNs que precisarão ser replicados juntos.

1. No console do VMM, abra a guia **Grupos de Replicação** das propriedades de matriz de armazenamento e clique em **Novo**.
2. Em seguida, crie o grupo de replicação.

	![Grupo de replicação de SAN](./media/site-recovery-vmm-san/SRSAN_RepGroup.png)

### Configurar redes

Se você quiser configurar o mapeamento de rede, faça o seguinte:

1. Leia sobre [Mapeamento de rede](https://msdn.microsoft.com/library/azure/dn801052.aspx).
2. Prepare redes de VM no VMM:
 
	- Saiba mais sobre [a configuração de redes lógicas](http://go.microsoft.com/fwlink/?LinkId=386307). Configurar redes lógicas: leia a Visão geral sobre a configuração de rede lógica no VMM.
	- [Configure as redes de VMM](http://go.microsoft.com/fwlink/?LinkId=386308).

## Etapa 2: criar um cofre


1. Entre no [Portal de Gerenciamento](https://portal.azure.com).


2. Expanda **Serviços de Dados** > **Serviços de Recuperação** e clique em **Cofre de Recuperação de Site**.


3. Clique em **Criar Novo** > **Criação Rápida**.
	
4. Em **Nome**, digite um nome amigável para identificar o cofre.

5. Em **Região**, selecione a região geográfica para o cofre. Para verificar as regiões suportadas, consulte a Disponibilidade Geográfica nos [Detalhes dos Preços de Recuperação de Site do Azure](href="http://go.microsoft.com/fwlink/?LinkId=389880)

6. Clique em **Criar cofre**.

	![Novo cofre](./media/site-recovery-vmm-san/SRSAN_HvVault.png)

Verifique a barra de status para confirmar que o cofre foi criado com sucesso. O cofre será listado como **Ativo** na página principal de **Serviços de Recuperação**.


### Registrar os servidores VMM

1. Na página <b>Serviços de Recuperação</b>, clique no cofre para abrir a página de Início Rápido. O Início Rápido pode também ser aberto a qualquer tempo usando o ícone.

	![Ícone de Inicialização Rápida](./media/site-recovery-vmm-san/SRSAN_QuickStartIcon.png)

2. Na lista suspensa, selecione **Entre o site local do Hyper-V usando a replicação de matriz**.

	![Chave de Registro](./media/site-recovery-vmm-san/SRSAN_QuickStartRegKey.png)

3. Em **Preparar servidores VMM**, clique em **Gerar arquivo de chave registro.** A chave é válida para 5 dias após ser gerada. Se você não estiver executando o console no servidor VMM que deseja proteger, baixe o arquivo para um local seguro que possa ser acessado pelos servidores VMM. Por exemplo, um compartilhamento. Observe que:

- Se você estiver instalando o Provedor pela primeira vez, instale-o em um nó ativo no cluster e conclua a instalação para registrar o servidor VMM no cofre do Azure Site Recovery. Em seguida, instale o Provedor nos outros nós no cluster.
- Se você estiver atualizando a versão do Provedor, execute a instalação do Provedor em todos os nós no cluster.

4. Clique em **Baixar o Provedor de Recuperação de Site do Microsoft Azure para instalação nos servidores VMM** para obter a versão mais recente do arquivo de instalação do Provedor.
5. Execute esse arquivo nos servidores VMM de origem e de destino para abrir o Assistente de Instalação do Provedor do Azure Site Recovery.
6. Na página **Verificação dos pré-requisitos**, selecione para interromper o serviço VMM a fim de começar a configuração do Provedor. O serviço para e reiniciará automaticamente quando a configuração finaliza. Se estiver instalando em um cluster do VMM, você deverá parar a função de Cluster.

	![Pré-requisitos](./media/site-recovery-vmm-san/SRSAN_ProviderPrereq.png)

5. No **Microsoft Update** você pode optar por atualizações. Com esta configuração de Provedor habilitada, a atualização será instalada de acordo com a política do Microsoft Update.

Após o Provedor ser instalado, continue com a configuração para registrar o servidor no cofre.

6. Na página Conexão de Internet, especifique como o Provedor em execução no servidor VMM se conecta à Internet. Você pode selecionar não usar um proxy, usar o proxy padrão configurado no servidor VMM ou usar um servidor proxy personalizado. Observe que:

	- Se quiser usar um servidor proxy personalizado, você deverá configurá-lo antes de instalar o Provedor.
	- As URLs a seguir devem estar acessíveis no servidor VMM
		- *.hypervrecoverymanager.windowsazure.com - *.accesscontrol.windows.net - *.backup.windowsazure.com - *.blob.core.windows.net - *.store.core.windows.net - Permita os endereços IP descritos em [Intervalos de IP do armazenamento de dados do Azure](http://go.microsoft.com/fwlink/?LinkId=511094) e o protocolo HTTPS (443). Você teria que fazer uma lista de intervalos IP válidos da região do Azure que você planeja usar e do oeste dos EUA. 
	
	- Se você optar por usar um proxy personalizado, uma conta RunAs do VMM (DRAProxyAccount) será criada automaticamente usando as credenciais de proxy especificadas. Configure o servidor proxy para que essa conta possa ser autenticada com êxito.
	- As configurações da conta RunAs VMM podem ser modificadas no console do VMM. Para fazer isso, abra o espaço de trabalho Configurações, expanda Segurança, clique em contas Executar como e modifique a senha de DRAProxyAccount. Você precisará reiniciar o serviço VMM para que essa configuração entre em vigor.
	- Um teste será executado para verificar a conexão com a Internet. Quaisquer erros de proxy serão exibidos no console do VMM.

	![Configurações da Internet](./media/site-recovery-vmm-san/SRSAN_ProviderProxy.png)

7. Em **Chave de registro**, selecione que você baixou a partir de Recuperação de Site do Azure e copiou para o servidor VMM.
8. Em **Nome do servidor**, especifique um nome amigável para identificar o servidor VMM no cofre.

	![Registros do servidor](./media/site-recovery-vmm-san/SRSAN_ProviderRegKeyServerName.png)

9. Na sincronização dos **Metadados de nuvem inicial**, selecione se você deseja sincronizar os metadados para todas as nuvens no servidor VMM com o cofre. Esta ação só precisa acontecer uma vez em cada servidor. Se você não quiser sincronizar todas as nuvens, você pode deixar essa configuração desmarcada e sincronizar cada nuvem individualmente nas propriedades da nuvem no console VMM. A opção **Criptografia de Dados** não é relevante nesse cenário.

	![Registros do servidor](./media/site-recovery-vmm-san/SRSAN_ProviderSyncEncrypt.png)

11. Na próxima página, clique em **Registrar** para concluir o processo. Após o registro, os metadados do servidor VMM são recuperado pelo Azure Site Recovery. O servidor é exibido no final da guia <b>Recursos</b> da página **Servidores** no cofre. Após a instalação, modifique as configurações do Provedor no console do VMM.


## Etapa 4: mapear as matrizes e pools de armazenamento

Mapeie as matrizes para especificar qual pool de armazenamento secundário recebe os dados de replicação do pool principal. Você deve mapear o armazenamento antes de configurar a proteção em nuvem, pois as informações de mapeamento são usadas quando você ativa a proteção para grupos de replicação.

Antes de começar, verifique se as nuvens aparecem no cofre. As nuvens são detectadas tanto pela seleção de sincronizar todas as nuvens quando você instala o provedor ou selecionando para sincronizar uma nuvem específica na guia **Geral** das propriedades da nuvem no console do VMM. Mapear as matrizes de armazenamento da seguinte forma:

1. Clique em **Recursos** > **Armazenamento do Servidor** > **Mapear as Matrizes de Origem e Destino**. ![Registros do servidor](./media/site-recovery-vmm-san/SRSAN_StorageMap.png).
2. Selecione as matrizes de armazenamento no site primário e mapeie-as para armazenar as matrizes no site secundário.
3.  Mapeie os pools de armazenamento de origem e de destino nas matrizes. Para fazer isso, em **Pools de Armazenamento** selecione um pool de armazenamento de origem e de destino para mapear.

	![Registros do servidor](./media/site-recovery-vmm-san/SRSAN_StorageMapPool.png)

## Etapa 5: definir as configurações da proteção de nuvem

Depois que os servidores VMM são registrados, você pode definir as configurações de proteção de nuvem. Você habilitou a opção **Sincronizar dados de nuvem com o cofre** ao instalar o Provedor, então todas as nuvens no servidor VMM aparecerão na guia <b>Itens Protegidos </b> no cofre.

![Publicar uma nuvem](./media/site-recovery-vmm-san/SRSAN_CloudsList.png)

1. Na página de Início Rápido, clique em **Configurar a proteção para nuvens VMM**.
2. Na guia **Itens Protegidos**, selecione a nuvem que você deseja configurar e vá para a guia **Configuração**. Observe que:
3. Em <b>Destino</b>, selecione <b>VMM</b>.
4. Em <b>Local de destino</b>, selecione o servidor VMM local que gerencia a nuvem que você deseja usar para a recuperação.
5. Em <b>Nuvem de destino</b>, selecione a nuvem de destino que você deseja usar para failover de máquinas virtuais na nuvem de origem. Observe que:
	- É recomendável selecionar uma nuvem de destino que atende aos requisitos de recuperação para máquinas virtuais que você protegerá.
	- Uma nuvem só pode pertencer a um par único de nuvem - como uma nuvem primária ou de destino.
6. A Recuperação de Site do Azure verifica se as nuvens têm acesso ao armazenamento capaz de replicação de SAN e se as matrizes de armazenamento estão igualadas. Os pares de matrizes participantes são exibidos.
7. Se a verificação for bem-sucedida, em **Tipo de replicação**, selecione **SAN**.

<p>Depois de salvar as configurações, um trabalho será criado e poderá ser monitorado pela guia <b>Trabalhos</b>. As configurações de nuvem podem ser modificadas na guia <b>Configurar</b>. Se você deseja modificar a nuvem de destino local ou destino, você deve remover a configuração de nuvem e, em seguida, reconfigurar a nuvem.</p>

## Etapa 6: habilitar o mapeamento de rede

1. Na página Início Rápido, clique em **Mapear redes**.
2. Selecione o servidor VMM de origem do qual você deseja mapear redes e o servidor VMM de destino para o qual as redes serão mapeadas. A lista de redes de origem e suas redes de destino associadas é exibida. Um valor vazio é mostrado para redes que não estão mapeadas no momento. Clique no ícone de informações ao lado dos nomes de rede de origem e de destino para exibir as sub-redes de cada rede.
3. Selecione uma rede em **Rede na origem** e clique em **Mapear**. O serviço detecta as redes VM no servidor de destino e as exibe.

	![Arquitetura de SAN](./media/site-recovery-vmm-san/ASRSAN_NetworkMap1.png)

4. Na caixa de diálogo exibida, selecione uma das redes de VM no servidor VMM de destino.

	![Arquitetura de SAN](./media/site-recovery-vmm-san/ASRSAN_NetworkMap2.png)

5. Quando você seleciona uma rede de destino, as nuvens protegidas que usam a rede de origem são exibidas. As redes de destino disponíveis que estão associados às nuvens usadas para proteção também são exibidas. Recomendamos que você selecione uma rede de destino disponível para todas as nuvens que você está usando para proteção.
6.  Clique na marca de seleção para concluir o processo de mapeamento. Um trabalho é iniciado para rastrear o progresso do mapeamento. Você pode exibi-lo na Guia **Trabalhos**.


## Etapa 7: habilitar a replicação para grupos de replicação</h3>

Antes de habilitar a proteção para máquinas virtuais, você precisará habilitar a replicação para grupos de replicação de armazenamento.

1. No portal do Azure Site Recovery, na página de propriedades da nuvem primária, abra a guia **Máquinas Virtuais**. Clique em **Adicionar Grupo de Replicação**.
2. Selecione um ou mais grupos de replicação do VMM que estão associados com a nuvem, verifique se os conjuntos de origem e de destino e especifique a frequência de replicação.

Quando essa operação for concluída, Recuperação de Site do Azure, juntamente com o VMM e os provedores de SMI-S provisionam o armazenamento do site de destino LUNs e habilitam a replicação de armazenamento. Se o grupo de replicação já foi replicado, a Recuperação de Site do Azure reutiliza a relação de replicação existente e atualiza as informações na Recuperação de Site do Azure.

## Habilitar a proteção para máquinas virtuais

Depois que um grupo de armazenamento estiver replicando, habilite a proteção para máquinas virtuais no console do VMM usando um dos seguintes métodos:

- **Nova máquina virtual**: no console do VMM, ao criar uma nova máquina virtual, habilite a proteção do Azure Site Recovery e associe a máquina virtual ao grupo de replicação. Com essa opção, o VMM usa a colocação inteligente para otimizar o local do armazenamento de máquina virtual nos LUN do grupo de replicação. A Recuperação de Site do Azure orquestra a criação de uma máquina virtual de sombra no local secundário e aloca a capacidade para que as máquinas virtuais de réplica possam ser iniciadas após o failover.
- **Máquina virtual existente**: se uma máquina virtual já estiver implantada no VMM, habilite a proteção do Azure Site Recovery e realize uma migração de armazenamento para um grupo de replicação. Após a conclusão, o VMM e a Recuperação de Site do Azure detectam a nova máquina virtual e começam a gerenciá-la na Recuperação de Site do Azure para proteção. Uma máquina virtual de sombra é criada no site secundário e a capacidade alocada para que a máquina virtual de réplica possa ser iniciada após o failover.


	![Habilitar proteção](./media/site-recovery-vmm-san/SRSAN_EnableProtection.png)
	

<P>Depois que as máquinas virtuais forem habilitadas para proteção, elas aparecerão no console do Azure Site Recovery. Você pode exibir propriedades da máquina virtual, acompanhar o status e o failover de grupos de replicação que contêm várias máquinas virtuais. Observe que na replicação de SAN, todas as máquinas virtuais associadas a um grupo de replicação devem fazer failover juntas. Isso ocorre porque o failover ocorre na camada de armazenamento pela primeira vez. É importante agrupar seus grupos de replicação corretamente e colocar apenas as máquinas virtuais associadas juntas.</P>

Acompanhe o progresso da ação Habilitar Proteção na guia **Trabalhos**, incluindo a replicação inicial. Após o trabalho de Finalizar Proteção ser executado, a máquina virtual estará pronta para o failover.![Trabalho de proteção da máquina virtual](./media/site-recovery-vmm-san/SRSAN_JobPropertiesTab.png)

## Etapa 8: testar a implantação</h3>

Teste a implantação para garantir que as máquinas virtuais e os dados executem o failover conforme o esperado. Para fazer isso, você criará um plano de recuperação selecionando grupos de replicação. Em seguida, execute um failover de teste no plano.

1. Na guia **Planos de Recuperação**, clique em **Criar Plano de Recuperação**.
2. Especifique um nome para o plano de recuperação e servidores do VMM de origem e destino. O servidor de origem deve ter máquinas virtuais que são habilitadas para failover e recuperação. Selecione **SAN** para exibir somente as nuvens que estão configuradas para replicação de SAN. 3. ![Criar plano de recuperação](./media/site-recovery-vmm-san/SRSAN_RPlan.png)

4. Em **Selecionar Máquina Virtual**, selecione os grupos de replicação. Todas as máquinas virtuais associadas ao grupo de replicação serão selecionadas e adicionadas ao plano de recuperação. Essas máquinas virtuais são adicionadas ao grupo padrão do plano de recuperação — Grupo 1. Você pode adicionar mais grupos se necessário. Observe que, depois da replicação, as máquinas virtuais serão inicializadas de acordo com a ordem dos grupos de plano de recuperação.

	![Adicionar máquinas virtuais](./media/site-recovery-vmm-san/SRSAN_RPlanVM.png)	
5. Depois que um plano de recuperação tiver sido criado, ele aparecerá na lista da guia **Planos de Recuperação**. 
6. Na guia **Planos de Recuperação**, selecione o plano e clique em **Failover de Teste**.
7. Na página **Confirmar Failover de Teste** selecione **Nenhum**. Observe que com essa opção habilitada a falha em máquinas virtuais de réplica não será conectada a nenhuma rede. Isso irá testar se a máquina virtual falha conforme o esperado, mas não testa seu ambiente de rede de replicação. Se você quiser executar um failover de teste mais abrangente, confira <a href="http://go.microsoft.com/fwlink/?LinkId=522291">Testar uma implantação local no MSDN</a>.

	![Selecionar rede de teste](./media/site-recovery-vmm-san/SRSAN_TestFailover1.png)


8. A máquina virtual de teste será criada no mesmo host como o host em que a máquina virtual de réplica existe. Ele não será adicionado na nuvem em que a máquina virtual de réplica está localizada.
9. Após a replicação a máquina virtual de réplica terá um endereço IP que não é o mesmo que o endereço IP da máquina virtual primária. Se você estiver emitindo endereços do DHCP, em seguida, será atualizado automaticamente. Se você não estiver usando o DHCP e quiser verificar se os endereços são os mesmos, você precisará executar alguns scripts.
10. Execute este script de exemplo para recuperar o endereço IP.

    	$vm = Get-SCVirtualMachine -Name <VM_NAME>
		$na = $vm[0].VirtualNetworkAdapters>
		$ip = Get-SCIPAddress -GrantToObjectID $na[0].id
		$ip.address  

11. Execute este script de exemplo para atualizar o DNS especificando o endereço IP recuperado usando o script de exemplo anterior.

		[string]$Zone,
		[string]$name,
		[string]$IP
		)
		$Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
		$newrecord = $record.clone()
		$newrecord.RecordData[0].IPv4Address  =  $IP
		Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord

## Monitorar atividade

Você pode usar a guia **Trabalhos** e **Painel** para exibir e monitorar os principais trabalhos executados pelo cofre de Recuperação de Site do Azure, incluindo a configuração da proteção para uma nuvem; habilitação e desabilitação da proteção para uma máquina virtual; execução de um failover (planejado, não planejado ou teste) e confirmação de um failover não planejado.

A partir da guia **Trabalhos** você exibe os trabalhos, realiza busca detalhada nos detalhes do trabalho e erros, executa consultas de trabalho para recuperar trabalhos que combinam com os critérios específicos, exporta trabalhos para o Excel e reinicia os trabalhos falhos.

A partir de **Painel**, você pode baixar as últimas versões dos arquivos de instalação do Provedor e do Agente, obtenha informações de configuração para o cofre, consulte o número de máquinas virtuais que tem a proteção gerenciada pelo cofre, veja os trabalhos recentes, grencie o certificado do cofre e sincronize novamente as máquinas virtuais.

Para saber mais sobre a interação com trabalhos e o painel, confira [Operações e monitoramento](http://go.microsoft.com/fwlink/?LinkId=398534).
	
 

<!---HONumber=July15_HO2-->