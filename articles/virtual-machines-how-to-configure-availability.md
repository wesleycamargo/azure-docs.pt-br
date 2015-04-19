<properties 
	pageTitle="Como configurar um conjunto de disponibilidade para máquinas virtuais" 
	description="Fornece as etapas para configurar uma conjunto de disponibilidade para uma VM nova ou existente no Azure usando os comandos do Portal de Gerenciamento do Azure e o PowerShell do Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/24/2015" 
	ms.author="kathydav"/>

#Como configurar um conjunto de disponibilidade para máquinas virtuais#


Um conjunto de disponibilidade ajuda a manter as máquinas virtuais disponíveis em caso de tempo de inatividade (por exemplo, durante a manutenção). Ter duas ou mais máquinas virtuais com configurações parecidas em um conjunto de disponibilidade cria a redundância necessária para manter a disponibilidade dos aplicativos ou serviços executados em sua máquina virtual. Para ver detalhes sobre como isso funciona, consulte [Gerenciar a disponibilidade de máquinas virtuais] []. 

Você deve usar uma combinação de conjuntos de disponibilidade e pontos de extremidade de balanceamento de carga para ajudar a garantir que seu aplicativo esteja sempre disponível e sendo executado com eficiência. Para obter mais detalhes sobre pontos de extremidade de balanceamento de carga, confira [Balanceamento de carga de máquinas virtuais][].

Você pode escolher uma destas opções para colocar máquinas virtuais em um conjunto de disponibilidade:

- [Opção 1: Crie uma máquina virtual e um conjunto de disponibilidade ao mesmo tempo][]. Em seguida, adicione novas máquinas virtuais ao conjunto ao criá-las.
- [Opção 2: Adicione uma máquina virtual já existente a um conjunto de disponibilidade][].


>[AZURE.NOTE] As máquinas virtuais que você pretende inserir em um mesmo conjunto de disponibilidade devem pertencer ao mesmo serviço de nuvem.   

## <a id="createset"> </a>Opção 1: Crie uma máquina virtual e um conjunto de disponibilidade ao mesmo tempo##

Você pode usar comandos do Portal de Gerenciamento ou o PowerShell do Azure para fazer isso. 

Para usar o Portal de Gerenciamento:

1. Se você ainda não fez isso, entre no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com).

2. Na barra de comandos, clique em **Novo**.

3. Clique em **Máquina Virtual** e, em seguida, clique em **Da Galeria**.

4. Nas duas primeiras telas, escolha a imagem, o nome de usuário, a senha etc. Para obter mais detalhes, confira [Criar uma máquina virtual que execute o Windows][].
 
5. A terceira tela permite configurar recursos para rede, armazenamento e disponibilidade. Faça o seguinte:
	 
	1. Escolha a opção de serviço de nuvem adequada. Selecione a opção **Criar um novo serviço de nuvem** (a menos que você esteja adicionando uma nova máquina virtual a um serviço de nuvem de máquina virtual existente). Insira um nome em **Nome DNS de serviço de nuvem**. O nome DNS torna-se parte do URI usado no contato com a máquina virtual. O serviço de nuvem atua como grupo de comunicação e isolamento. Todas as máquinas virtuais do mesmo serviço de nuvem podem comunicar-se entre si. Além disso, é possível configurá-las para balanceamento de carga e colocá-las no mesmo conjunto de disponibilidade. 

	2. Se você pretende usar uma rede virtual, especifique-a em **Região/Grupo de Afinidade/Rede Virtual**. **Importante**: Se quiser que uma máquina virtual use uma rede virtual, você deve adicioná-la ao criar a máquina virtual. Você não poderá adicionar a máquina virtual a uma rede virtual depois de criar a VM. Para obter mais informações, consulte [Visão geral da Rede Virtual do Azure][]. 
	
	3. Crie o conjunto de disponibilidade. Em **Conjunto de Disponibilidade**, selecione a opção **Criar um conjunto de disponibilidade**. Em seguida, atribua um nome ao conjunto. 

	4. Crie os pontos de extremidade padrão e adicione mais pontos de extremidade se necessário. Também é possível adicionar os pontos de extremidade em outro momento. 

	![Criar um conjunto de disponibilidade para uma nova máquina virtual](./media/virtual-machines-how-to-configure-availability/VMavailabilityset.png) 

6. Na quarta tela, selecione as extensões que você deseja instalar. As extensões oferecem recursos que facilitam o gerenciamento da máquina virtual, como a execução de antimalware ou redefinição de senhas. Para obter detalhes, confira [Agente de máquina virtual Linux e extensões de máquina virtual do Azure](http://go.microsoft.com/fwlink/p/?LinkId=XXX).

7.	Clique na seta para criar a máquina virtual e o conjunto de disponibilidade.

	No painel da nova máquina virtual, você pode clicar em **Configurar** e verificar que a máquina virtual pertence ao novo conjunto de disponibilidade.

Para usar comandos do PowerShell do Azure para criar uma VM do Azure e adicioná-la a um conjunto de disponibilidade novo ou existente, consulte o seguinte:

- [Usar o PowerShell do Azure para criar e pré-configurar máquinas virtuais baseadas em Windows](virtual-machines-ps-create-preconfigure-windows-vms.md)
- [Usar o PowerShell do Azure para criar e pré-configurar máquinas virtuais baseadas em Linux](virtual-machines-ps-create-preconfigure-linux-vms.md)

<<<<<<< HEAD
	>[AZURE.NOTE] Execute `Get-AzureVMImage` sem parâmetros para obter uma lista de todas as imagens que se aplicam à sua assinatura. Isso pode retornar uma lista grande. Para reduzir a lista, use propriedades como o nome de família da imagem. Para dicas e exemplos que mostrem como fazer isso para encontrar uma imagem específica, consulte [Gerenciar imagens usando o Windows PowerShell](http://msdn.microsoft.com/library/azure/dn790330.aspx).

3.	Especifique a configuração da nova máquina virtual e use o pipeline para transmitir um objeto de configuração ao cmdlet que cria a máquina virtual. Não se esqueça de substituir os próprios valores para espaços reservados, como  &lt;VmName&gt; e &lt;VmSize&gt;.

	`C:\PS> New-AzureVMConfig -Name "<VmName>" -InstanceSize <VmSize> -AvailabilitySetName "<SetName>" -ImageName $image | Add-AzureProvisioningConfig -Windows -AdminUsername "<UserName>" -Password "<MyPassword>" | Add-AzureDataDisk -CreateNew -DiskSizeInGB 50 -DiskLabel 'datadisk1' -LUN 0 | New-AzureVM -ServiceName "<MySvc1>" `

=======
>>>>>>> 1dc830a16302e6b943a5d55ebacef659451b22d6

## <a id="addmachine"> </a>Opção 2: Adicione uma máquina virtual já existente a um conjunto de disponibilidade##

No Portal de gerenciamento é possível adicionar máquinas virtuais existentes a um conjunto de disponibilidade existente ou criar um novo conjunto para essas máquinas. (Lembre-se de que as máquinas virtuais no mesmo conjunto de disponibilidade devem pertencer ao mesmo serviço de nuvem). As etapas são praticamente as mesmas. Como o PowerShell do Azure é possível adicionar a máquina virtual a um conjunto de disponibilidade existente. 

1. Se você ainda não tiver feito isso, entre no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com).

2. Na barra de navegação, clique em **Máquinas Virtuais**.

3. Na lista de máquinas virtuais, clique no nome das máquinas virtuais que deseja adicionar ao conjunto.

4. Dentre as guias abaixo do nome da máquina virtual, clique em **Configurar**. 

5. Na seção Configurações, localize a opção **Conjunto de Disponibilidade**. Faça uma das opções a seguir:

	A. Escolha **Criar um conjunto de disponibilidade** e atribua um nome ao conjunto.

	B. Escolha **Selecionar um conjunto de disponibilidade** e selecione um dos conjuntos da lista.

	![Criar um conjunto de disponibilidade para uma máquina virtual existente](./media/virtual-machines-how-to-configure-availability/VMavailabilityExistingVM.png) 

6. Clique em **Salvar**.

Para usar os comandos do PowerShell do Azure, abra uma sessão do PowerShell do Azure de nível de administrador e execute o comando a seguir. Para os espaços reservados (como &lt;VmCloudServiceName&gt;), substitua tudo entre aspas, incluindo os caracteres < e >, pelos nomes corretos.

	Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

>[AZURE.NOTE] A máquina virtual pode ser reiniciada para concluir a adição ao conjunto de disponibilidade.


##Recursos adicionais
[Sobre as definições de configuração da VM do Azure]

[Opção 1: Crie uma máquina virtual e um conjunto de disponibilidade ao mesmo tempo]: #createset
[Opção 2: Adicione uma máquina virtual já existente a um conjunto de disponibilidade]: #addmachine

<!-- LINKS -->
[Balanceamento de carga para Serviços de infraestrutura do Azure]: ../virtual-machines-load-balance
[Gerenciar a disponibilidade de máquinas virtuais]: ../virtual-machines-manage-availability
[Criação de uma Máquina virtual executando o Windows]: ../virtual-machines-windows-tutorial
[Visão geral da rede virtual do Azure]: http://go.microsoft.com/fwlink/p/?linkid=294063
[Sobre grupos de afinidade para rede virtual]: http://msdn.microsoft.com/library/windowsazure/jj156085.aspx
<<<<<<< HEAD
[Como conectar máquinas virtuais em um serviço de nuvem]: ../virtual-machines-connect-cloud-service
[Sobre as definições de configuração da VM do Azure]: http://msdn.microsoft.com/library/azure/dn763935.aspx
=======
[Como conectar máquinas virtuais em um serviço de nuvem]: ../cloud-services-connect-virtual-machine
[Sobre as definições de configuração da VM do Azure]: http://msdn.microsoft.com/library/azure/dn763935.aspx
>>>>>>> 1dc830a16302e6b943a5d55ebacef659451b22d6


<!--HONumber=47-->
