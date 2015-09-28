<properties
	pageTitle="Como configurar um conjunto de disponibilidade para máquinas virtuais"
	description="Fornece as etapas para configurar um conjunto de disponibilidade para uma máquina virtual nova ou existente no Azure usando os comandos do Portal do Azure e do Azure PowerShell."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/10/2015"
	ms.author="cynthn"/>

# Como configurar um conjunto de disponibilidade para máquinas virtuais

Os conjuntos de disponibilidade ajudam a manter as máquinas virtuais disponíveis em caso de tempo de inatividade (por exemplo, durante a manutenção). Ter duas ou mais máquinas virtuais com configurações parecidas em um conjunto de disponibilidade cria a redundância necessária para manter a disponibilidade dos aplicativos ou serviços executados em sua máquina virtual. Para ver detalhes sobre como isso funciona, consulte [Gerenciar a disponibilidade das máquinas virtuais][].

Você deve usar uma combinação de conjuntos de disponibilidade e pontos de extremidade de balanceamento de carga para ajudar a garantir que seu aplicativo esteja sempre disponível e sendo executado com eficiência. Para obter mais detalhes sobre pontos de extremidade de balanceamento de carga, confira [Balanceamento de carga de serviços de infraestrutura do Azure][].

Você pode escolher uma destas opções para colocar máquinas virtuais em um conjunto de disponibilidade:

- [Opção 1: crie uma máquina virtual e um conjunto de disponibilidade ao mesmo tempo.][]. Em seguida, adicione novas máquinas virtuais ao conjunto ao criá-las.
- [Opção 2: adicione uma máquina virtual já existente a um conjunto de disponibilidade][].

>[AZURE.NOTE]As máquinas virtuais que você pretende inserir em um mesmo conjunto de disponibilidade devem pertencer ao mesmo serviço de nuvem.

## <a id="createset"> </a>Opção 1: crie uma máquina virtual e um conjunto de disponibilidade ao mesmo tempo##

Você pode usar comandos do Portal do Azure ou do Azure PowerShell para fazer isso.

Para usar o portal:

1. Se você ainda não tiver feito isso, entre no [portal](http://manage.windowsazure.com).

2. Na barra de comandos, clique em **Nova**.

3. Clique em **Máquina Virtual** e, em seguida, clique em **Da Galeria**.

4. Nas duas primeiras telas, selecione uma imagem, um nome de usuário, uma senha, etc. Para obter mais detalhes, consulte [Criar uma máquina virtual que execute o Windows][].

5. Na terceira tela, você pode configurar recursos para rede, armazenamento e disponibilidade. Faça o seguinte:

	1. Escolha o serviço de nuvem apropriado. Selecione a opção **Criar um novo serviço de nuvem** (a menos que você esteja adicionando uma nova máquina virtual a um serviço de nuvem de máquina virtual existente). Insira um nome em **Nome DNS de serviço de nuvem**. O nome DNS torna-se parte do URI usado no contato com a máquina virtual. O serviço de nuvem atua como grupo de comunicação e isolamento. Todas as máquinas virtuais do mesmo serviço de nuvem podem comunicar-se entre si. Além disso, é possível configurá-las para balanceamento de carga e colocá-las no mesmo conjunto de disponibilidade.

	2. Se você pretende usar uma rede virtual, especifique-a em **Região/Grupo de Afinidade/Rede Virtual**. **Importante**: se quiser que uma máquina virtual use uma rede virtual, você deve adicioná-la à rede virtual ao criar a máquina virtual. Você não poderá adicionar a máquina virtual a uma rede virtual depois de criar a máquina virtual. Para saber mais, confira [Visão geral da rede virtual][].

	3. Crie o conjunto de disponibilidade. Em **Conjunto de Disponibilidade**, selecione a opção **Criar um conjunto de disponibilidade**. Em seguida, atribua um nome ao conjunto.

	4. Crie os pontos de extremidade padrão e adicione mais pontos de extremidade se necessário. Também é possível adicionar os pontos de extremidade em outro momento.

	![Criar um conjunto de disponibilidade para uma nova máquina virtual](./media/virtual-machines-how-to-configure-availability/VMavailabilityset.png)

6. Na quarta tela, clique nas extensões que você deseja instalar. As extensões oferecem recursos que facilitam o gerenciamento das máquinas virtuais, como a execução de antimalware ou redefinição de senhas. Para obter detalhes, confira [Agente de máquina virtual Linux e extensões de máquina virtual do Azure](virtual-machines-extensions-agent-about.md).

7.	Clique na seta para criar a máquina virtual e o grupo de disponibilidade.

	No painel da nova máquina virtual, você pode clicar em **Configurar** e verificar que a máquina virtual pertence ao novo conjunto de disponibilidade.

Para usar comandos do Azure PowerShell para criar uma máquina virtual do Azure e adicioná-la a um conjunto de disponibilidade novo ou existente, consulte o seguinte:

- [Usar o Azure PowerShell para criar e pré-configurar máquinas virtuais baseadas em Windows](virtual-machines-ps-create-preconfigure-windows-vms.md)
- [Usar o Azure PowerShell para criar e pré-configurar máquinas virtuais baseadas em Linux](virtual-machines-ps-create-preconfigure-linux-vms.md)

## <a id="addmachine"> </a>Opção 2: adicione uma máquina virtual já existente a um conjunto de disponibilidade##

No Portal, é possível adicionar máquinas virtuais existentes a um conjunto de disponibilidade existente ou criar um novo conjunto para essas máquinas. (Lembre-se de que as máquinas virtuais no mesmo conjunto de disponibilidade devem pertencer ao mesmo serviço de nuvem). As etapas são praticamente as mesmas. Como o PowerShell do Azure é possível adicionar a máquina virtual a um conjunto de disponibilidade existente.

1. Se você ainda não fez isso, entre no [portal](http://manage.windowsazure.com).

2. Na barra de comandos, clique em **Máquinas Virtuais**.

3. Na lista de máquinas virtuais, selecione o nome das máquinas virtuais que deseja adicionar ao conjunto.

4. Dentre as guias abaixo do nome da máquina virtual, clique em **Configurar**.

5. Na seção Configurações, localize a opção **Conjunto de Disponibilidade**. Faça uma das opções a seguir:

	R. Selecione **Criar um conjunto de disponibilidade** e atribua um nome ao conjunto.

	B. Selecione **Selecionar um conjunto de disponibilidade** e, em seguida, selecione um dos conjuntos da lista.

	![Criar um conjunto de disponibilidade para uma máquina virtual existente](./media/virtual-machines-how-to-configure-availability/VMavailabilityExistingVM.png)

6. Clique em **Salvar**.

Para usar os comandos do PowerShell do Azure, abra uma sessão do PowerShell do Azure de nível de administrador e execute o comando a seguir. Para os espaços reservados (como &lt;VmCloudServiceName&gt;), substitua tudo entre aspas, incluindo os caracteres < and >, pelos nomes corretos.

	Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

>[AZURE.NOTE]A máquina virtual pode precisar ser reiniciada para concluir a adição ao conjunto de disponibilidade.

## Recursos adicionais

[Artigos para máquinas virtuais no Gerenciamento de Serviços]

<!-- LINKS -->
[Opção 1: crie uma máquina virtual e um conjunto de disponibilidade ao mesmo tempo.]: #createset
[Opção 2: adicione uma máquina virtual já existente a um conjunto de disponibilidade]: #addmachine

[Balanceamento de carga de serviços de infraestrutura do Azure]: virtual-machines-load-balance.md
[Gerenciar a disponibilidade das máquinas virtuais]: virtual-machines-manage-availability.md
[Criar uma máquina virtual que execute o Windows]: virtual-machines-windows-tutorial.md
[Visão geral da rede virtual]: virtual-networks-overview.md
[Artigos para máquinas virtuais no Gerenciamento de Serviços]: virtual-machines-service-management-articles.md

<!---HONumber=Sept15_HO3-->