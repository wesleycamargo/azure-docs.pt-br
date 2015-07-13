
As máquinas virtuais sempre são colocadas em um serviço de nuvem, que funciona como um contêiner e fornece um nome DNS público exclusivo, um endereço IP público e um conjunto de pontos de extremidade para acessar a máquina virtual pela Internet. O serviço de nuvem pode estar opcionalmente em uma rede virtual.

Se um serviço de nuvem não estiver em uma rede virtual, ele será chamado de serviço de nuvem *autônomo*. A máquina virtual nesse serviço de nuvem só poderá se comunicar com outras máquinas virtuais com o uso de nomes DNS públicos de outras máquinas virtuais, e esse tráfego viajará pela Internet. Se um serviço de nuvem estiver em uma rede virtual, as máquinas virtuais no serviço de nuvem podem se comunicar com todas as outras máquinas virtuais na rede virtual sem enviar tráfego pela Internet.

Ao colocar as máquinas virtuais no mesmo serviço de nuvem autônomo, você ainda pode aproveitar o balanceamento de carga e os conjuntos de disponibilidade. Para obter detalhes, consulte [Máquinas virtuais de balanceamento de carga](../articles/load-balance-virtual-machines.md) e [Gerenciar a disponibilidade das máquinas virtuais](../articles/manage-availability-virtual-machines.md). No entanto, você não pode organizar as máquinas virtuais em sub-redes ou conectar um serviço de nuvem autônomo à sua rede local. Aqui está um exemplo.

![Máquinas virtuais em um serviço de nuvem autônomo](./media/howto-connect-vm-cloud-service/CloudServiceExample.png)
 
Ao colocar as máquinas virtuais em uma rede virtual, você pode decidir quantos serviços de nuvem deseja usar para os conjuntos de disponibilidade e o balanceamento de carga. Além disso, você pode organizar as máquinas virtuais em sub-redes da mesma forma como sua rede local e conectar a rede virtual à sua rede local. Aqui está um exemplo.

![Máquinas virtuais em uma rede virtual](./media/howto-connect-vm-cloud-service/VirtualNetworkExample.png)

As redes virtuais são a maneira recomendada para conectar máquinas virtuais no Azure. A prática recomendada é configurar cada camada do aplicativo em um serviço de nuvem separado. Isso permite a delegação de direitos de usuário avançado por meio do controle de acesso com base em função (RBAC). Para obter mais informações, consulte [Controle de acesso com base em função no Portal de visualização do Azure](../articles/role-based-access-control-configure.md). No entanto, talvez seja necessário combinar algumas máquinas virtuais de diferentes níveis de aplicativos no mesmo serviço de nuvem para permanecer dentro do máximo de 200 serviços de nuvem por assinatura.

## Conectar VMs em uma rede virtual

Para conectar máquinas virtuais em uma rede virtual:

1.	Crie a rede virtual no Portal de gerenciamento do Azure. Para obter mais informações, consulte [Tarefas de configuração de rede virtual](https://msdn.microsoft.com/library/azure/jj156206.aspx).
2.	Crie o conjunto de serviços de nuvem para a implantação no Portal de Gerenciamento do Azure para refletir seu design para conjuntos de disponibilidade e balanceamento de carga com **Novo > Computação > Serviço de nuvem > Criação personalizada**.
3.	Ao criar novas máquinas virtuais, especifique o serviço de nuvem correto e sua rede virtual. Se o serviço de nuvem tiver sido previamente associado à sua rede virtual, seu nome já será selecionado para você.

Aqui está um exemplo usando o Portal de gerenciamento do Azure.

![Selecionar um serviço de nuvem para uma máquina virtual](./media/howto-connect-vm-cloud-service/VMConfig1.png)

## Conectar VMs em um serviço de nuvem autônomo
 
Para conectar máquinas virtuais em um serviço de nuvem autônomo:

1.	Crie o serviço de nuvem para implantação no Portal de Gerenciamento do Azure. Clique em **Novo > Computação > Serviço de Nuvem > Criação Personalizada**.
2.	Ao criar a máquina virtual, escolha o nome do serviço de nuvem criado na etapa anterior. Ou você pode criar o serviço de nuvem para sua implantação ao criar sua primeira máquina virtual.

Aqui está um exemplo usando o Portal de gerenciamento do serviço de nuvem existente chamado EndpointTest.
 
![Adicionar uma máquina virtual a um serviço de nuvem existente](./media/howto-connect-vm-cloud-service/Connect-VM-to-CS.png)

##Recursos
[Máquinas virtuais de balanceamento de carga](../articles/load-balance-virtual-machines.md)

[Gerencie a disponibilidade de máquinas virtuais](../articles/manage-availability-virtual-machines.md)

[Tarefas de configuração da rede virtual](https://msdn.microsoft.com/library/azure/jj156206.aspx)

Após criar uma máquina virtual, é uma boa ideia adicionar um disco de dados para que seus serviços e cargas de trabalho tenham um local para armazenar dados. Veja uma das opções a seguir:

[Como anexar um disco de dados na máquina virtual Linux](../articles/virtual-machines/virtual-machines-linux-how-to-attach-disk.md)

[Como anexar um disco de dados a uma máquina virtual Windows](../articles/virtual-machines/storage-windows-attach-disk.md)

<!---HONumber=July15_HO1-->