<properties authors="kathydav" editor="tysonn" manager="timlt" /> 


#Como conectar máquinas virtuais em um serviço de nuvem

Quando você cria uma máquina virtual, um serviço de nuvem é criado automaticamente para conter a máquina. É possível criar diversas máquinas virtuais no mesmo serviço de nuvem para que as máquinas virtuais possam se comunicar entre si. 

> [WACOM.NOTE] Quando as Vms estão no mesmo serviço de nuvem, também é possível balanceá-las e gerenciar sua disponibilidade, que ambas requerem etapas adicionais. Para obter detalhes, consulte [Balanceamento de carga de máquinas virtuais](../../articles/load-balance-virtual-machines/) e [Gerenciar a disponibilidade de máquinas virtuais](../../articles/manage-availability-virtual-machines/). 

Primeiro, você precisará criar uma máquina virtual com um novo serviço de nuvem. Então, você criará máquinas virtuais adicionais no mesmo serviço de nuvem. Isso as 'conecta'. 

1. Crie uma primeira máquina virtual usando as etapas em [Como criar uma máquina virtual personalizada](../../articles/virtual-machines-create-custom/).

2. Siga o mesmo processo básico para criar as outras máquinas virtuais, exceto que você as incluirá no serviço de nuvem em vez de criar um serviço de nuvem. Por exemplo, se você criou um serviço de nuvem chamado *EndpointTest*, escolha esse serviço. O gráfico a seguir mostra isto:

	![Add a virtual machine to an existing cloud service](./media/howto-connect-vm-cloud-service/Connect-VM-to-CS.png)

14. Preencha o restante dos campos nesta página e na próxima, em seguida, clique na marca de seleção para criar a máquina virtual conectada.

#Recursos

Após criar uma máquina virtual, é uma boa ideia incluir um disco de dados para que seus serviços e cargas de trabalho tenham um local para armazenar dados. Veja uma das opções a seguir:

[Como anexar um disco de dados a uma máquina virtual Linux](http://azure.microsoft.com/pt-br/documentation/articles/virtual-machines-linux-how-to-attach-disk/)

[Como anexar um disco de dados a uma máquina virtual Windows](http://azure.microsoft.com/pt-br/documentation/articles/storage-windows-attach-disk/)



<!--HONumber=35_1-->
