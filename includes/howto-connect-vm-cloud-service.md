<properties authors="kathydav" editor="tysonn" manager="timlt" /> 


#Como conectar máquinas virtuais a uma rede virtual ou serviço de nuvem

Máquinas virtuais devem estar em um serviço de nuvem, que funciona como um contêiner e fornece um nome DNS público exclusivo, um endereço IP público e um conjunto de pontos de extremidade para acessar a máquina virtual pela Internet. O serviço de nuvem pode estar opcionalmente em uma rede virtual. 

Se um serviço de nuvem não está em uma rede virtual, a máquina virtual nesse serviço de nuvem só poderá se comunicar com outras máquinas virtuais com o uso de nomes DNS públicos de outras máquinas virtuais, e esse tráfego viajaria pela Internet. Se um serviço de nuvem estiver em uma rede virtual, as máquinas virtuais no serviço de nuvem podem se comunicar com todas as outras máquinas virtuais na rede virtual sem enviar tráfego pela Internet.

Se você colocar as máquinas virtuais no mesmo serviço de nuvem autônomo, você poderá tirar proveito do balanceamento de carga e conjuntos de disponibilidade. Para obter detalhes, consulte [Máquinas virtuais de balanceamento de carga](../../articles/load-balance-virtual-machines/) e [Gerenciar a disponibilidade das máquinas virtuais](../../articles/manage-availability-virtual-machines/). No entanto, você não pode organizar as máquinas virtuais em sub-redes ou conectar um serviço de nuvem autônomo em sua rede local. Aqui está um exemplo.

![Virtual machines in a standalone cloud service](./media/howto-connect-vm-cloud-service/CloudServiceExample.png)
 
Se colocar as máquinas virtuais em uma rede virtual, você poderá decidir quantos serviços de nuvem deseja usar para tirar proveito de conjuntos de disponibilidade e balanceamento de carga. Além disso, você pode organizar as máquinas virtuais em sub-redes da mesma forma como sua rede local e conectar a rede virtual à sua rede local. Aqui está um exemplo.

![Virtual machines in a virtual network](./media/howto-connect-vm-cloud-service/VirtualNetworkExample.png)

As redes virtuais são a maneira recomendada para conectar máquinas virtuais no Azure. A prática recomendada é configurar cada camada do aplicativo em um serviço de nuvem separado. Isso permite a delegação de direitos de usuário avançado por meio do controle de acesso com base em função (RBAC). Para obter mais informações, consulte [Controle de acesso com base em função no Portal de visualização do Azure](../../articles//role-based-access-control-configure/). No entanto, talvez seja necessário combinar algumas máquinas virtuais de diferentes níveis de aplicativos no mesmo serviço de nuvem para permanecer dentro do máximo de 200 serviços de nuvem por assinatura.

Para conectar máquinas virtuais em uma rede virtual:

1.	Crie a rede virtual no Portal de gerenciamento do Azure. Para obter mais informações, consulte [Tarefas de configuração de rede virtual](https://msdn.microsoft.com/library/azure/jj156206.aspx).
2.	Crie o conjunto de serviços de nuvem para a implantação no Portal de gerenciamento do Azure para refletir seu design para conjuntos de disponibilidade e balanceamento de carga com **Novo > Computação > Serviço de nuvem > Criação personalizada**.
3.	Ao criar novas máquinas virtuais, especifique o serviço de nuvem correto e sua rede virtual. Se o serviço de nuvem tiver sido previamente associado à sua rede virtual, seu nome já será selecionado para você.

Aqui está um exemplo usando o Portal de gerenciamento do Azure.

![Selecting a cloud service for a virtual machine](./media/howto-connect-vm-cloud-service/VMConfig1.png)
 
Para conectar máquinas virtuais em um serviço de nuvem autônomo:

1.	Criar o serviço de nuvem para sua implantação no Portal de gerenciamento do Azure com **Novo > Computação > Serviço de nuvem > Criação personalizada**.
2.	Ao criar a máquina virtual, especifique o nome do serviço de nuvem criado na etapa 1. 
Como alternativa, você pode criar o serviço de nuvem para sua implantação ao criar sua primeira máquina virtual.

Aqui está um exemplo usando o Portal de gerenciamento do serviço de nuvem existente chamado EndpointTest.
 
![Add a virtual machine to an existing cloud service](./media/howto-connect-vm-cloud-service/Connect-VM-to-CS.png)

##Recursos
[Balanceando a carga de máquinas virtuais](../../articles/load-balance-virtual-machines/)

[Gerenciar a disponibilidade de máquinas virtuais](../../articles/manage-availability-virtual-machines/)

[Tarefas de configuração da rede virtual](https://msdn.microsoft.com/library/azure/jj156206.aspx)

Após criar uma máquina virtual, é uma boa ideia adicionar um disco de dados para que seus serviços e cargas de trabalho tenham um local para armazenar dados. Veja uma das opções a seguir:

[Como anexar um disco de dados na máquina virtual Linux](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-how-to-attach-disk/)

[Como anexar um disco de dados a uma máquina virtual Windows](http://azure.microsoft.com/documentation/articles/storage-windows-attach-disk/)


<!--HONumber=45--> 
