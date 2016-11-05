


Os conjuntos de disponibilidade ajudam a manter as máquinas virtuais disponíveis em caso de tempo de inatividade (por exemplo, durante a manutenção). Ter duas ou mais máquinas virtuais com configurações parecidas em um conjunto de disponibilidade cria a redundância necessária para manter a disponibilidade dos aplicativos ou serviços executados em sua máquina virtual. Para obter detalhes sobre como isso funciona, veja [Gerenciar a disponibilidade das máquinas virtuais][Gerenciar a disponibilidade das máquinas virtuais].

Você deve usar uma combinação de conjuntos de disponibilidade e pontos de extremidade de balanceamento de carga para ajudar a garantir que seu aplicativo esteja sempre disponível e sendo executado com eficiência. Para obter mais detalhes sobre pontos de extremidade de balanceamento de carga, confira [Balanceamento de carga de serviços de infraestrutura do Azure][Balanceamento de carga de serviços de infraestrutura do Azure].

Você pode adicionar máquinas virtuais clássicas a um conjunto de disponibilidade usando uma das duas opções:

* [Opção 1: crie uma máquina virtual e um conjunto de disponibilidade ao mesmo tempo.][Opção 1: crie uma máquina virtual e um conjunto de disponibilidade ao mesmo tempo.]. Em seguida, adicione novas máquinas virtuais ao conjunto ao criá-las.
* [Opção 2: adicione uma máquina virtual já existente a um conjunto de disponibilidade][Opção 2: adicione uma máquina virtual já existente a um conjunto de disponibilidade].

> [!NOTE]
> No modelo clássico, as máquinas virtuais que você pretende inserir em um mesmo conjunto de disponibilidade devem pertencer ao mesmo serviço de nuvem.
> 
> 

## <a id="createset"> </a>Opção 1: crie uma máquina virtual e um conjunto de disponibilidade ao mesmo tempo
Você pode usar comandos do Portal do Azure ou do Azure PowerShell para fazer isso.

Para usar o portal do Azure:

1. Se ainda não tiver feito isso, entre no [portal do Azure](https://portal.azure.com).
2. No menu Hub, clique em **+ Novo** e em **Máquina Virtual**.
   
    ![Texto Alt da imagem](./media/virtual-machines-common-classic-configure-availability/ChooseVMImage.png)
3. Selecione a imagem de máquina virtual do Marketplace que você deseja usar. Você pode optar por criar uma máquina virtual Linux ou Windows.
4. Para a máquina virtual selecionada, verifique se o modelo de implantação está definido como **Clássico** e clique em **Criar**
   
    ![Texto Alt da imagem](./media/virtual-machines-common-classic-configure-availability/ChooseClassicModel.png)
5. Insira um nome da máquina virtual, nome de usuário e senha (para computadores Windows) ou a chave pública SSH (para computadores Linux).
6. Escolha um tamanho de VM e clique em **Selecionar** para continuar.
7. Escolha **Configuração Opcional > Conjunto de disponibilidade** e selecione o conjunto de disponibilidade que você deseja adicionar à máquina virtual.
   
    ![Texto Alt da imagem](./media/virtual-machines-common-classic-configure-availability/ChooseAvailabilitySet.png)
8. Analise suas definições de configuração. Quando tiver concluído, clique em **Criar**.
9. Enquanto o Azure cria sua máquina virtual, você pode acompanhar o andamento em **Máquinas Virtuais**, no menu Hub.

Para usar comandos do Azure PowerShell para criar uma máquina virtual do Azure e adicioná-la a um conjunto de disponibilidade novo ou existente, consulte [Usar o Azure PowerShell para criar e pré-configurar máquinas virtuais baseadas em Windows](../articles/virtual-machines/virtual-machines-windows-classic-create-powershell.md)

## <a id="addmachine"> </a>Opção 2: adicione uma máquina virtual já existente a um conjunto de disponibilidade
No Portal do Azure, é possível adicionar máquinas virtuais clássicas existentes a um conjunto de disponibilidade existente ou criar um novo conjunto para essas máquinas. (Lembre-se de que as máquinas virtuais no mesmo conjunto de disponibilidade devem pertencer ao mesmo serviço de nuvem). As etapas são praticamente as mesmas. Como o PowerShell do Azure é possível adicionar a máquina virtual a um conjunto de disponibilidade existente.

1. Se ainda não tiver feito isso, entre no [Portal do Azure](https://portal.azure.com).
2. No menu Hub, clique em **Máquinas Virtuais (clássicas)**.
   
    ![Texto Alt da imagem](./media/virtual-machines-common-classic-configure-availability/ChooseClassicVM.png)
3. Na lista de máquinas virtuais, selecione o nome das máquinas virtuais que você deseja adicionar ao conjunto.
4. Escolha **Conjunto de disponibilidade** nas **Configurações** de máquina virtual.
   
    ![Texto Alt da imagem](./media/virtual-machines-common-classic-configure-availability/AvailabilitySetSettings.png)
5. Selecione a conjunto de disponibilidade que você deseja adicionar à máquina virtual. A máquina virtual deve pertencer ao mesmo serviço de nuvem que o conjunto de disponibilidade.
   
    ![Texto Alt da imagem](./media/virtual-machines-common-classic-configure-availability/AvailabilitySetPicker.png)
6. Clique em **Salvar**.

Para usar os comandos do PowerShell do Azure, abra uma sessão do PowerShell do Azure de nível de administrador e execute o comando a seguir. Para os espaços reservados (como &lt;VmCloudServiceName&gt;), substitua tudo entre aspas, incluindo os caracteres < e >, pelos nomes corretos.

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

> [!NOTE]
> A máquina virtual pode precisar ser reiniciada para concluir a adição ao conjunto de disponibilidade.
> 
> 

<!-- LINKS -->
[Opção 1: crie uma máquina virtual e um conjunto de disponibilidade ao mesmo tempo.]: #createset
[Opção 2: adicione uma máquina virtual já existente a um conjunto de disponibilidade]: #addmachine

[Balanceamento de carga de serviços de infraestrutura do Azure]: ../articles/virtual-machines/virtual-machines-linux-load-balance.md
[Gerenciar a disponibilidade das máquinas virtuais]: ../articles/virtual-machines/virtual-machines-linux-manage-availability.md

[Create a virtual machine running Windows]: ../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md
[Virtual Network overview]: ../articles/virtual-network/virtual-networks-overview.md

<!---HONumber=AcomDC_0713_2016-->