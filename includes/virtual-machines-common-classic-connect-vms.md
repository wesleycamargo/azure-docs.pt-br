

![Máquinas virtuais em um serviço de nuvem autônomo](./media/virtual-machines-common-classic-connect-vms/CloudServiceExample.png)

Ao colocar as máquinas virtuais em uma rede virtual, você pode decidir quantos serviços de nuvem deseja usar para os conjuntos de disponibilidade e o balanceamento de carga. Além disso, você pode organizar as máquinas virtuais em sub-redes da mesma forma como sua rede local e conectar a rede virtual à sua rede local. Aqui está um exemplo:

![Máquinas virtuais em uma rede virtual](./media/virtual-machines-common-classic-connect-vms/VirtualNetworkExample.png)

As redes virtuais são a maneira recomendada para conectar máquinas virtuais no Azure. A prática recomendada é configurar cada camada do aplicativo em um serviço de nuvem separado. No entanto, talvez seja necessário combinar algumas máquinas virtuais de diferentes níveis de aplicativos no mesmo serviço de nuvem para permanecer dentro do máximo de 200 serviços de nuvem por assinatura. Para verificar isso e outros limites, consulte [Assinatura do Azure e limites de serviços, cotas e restrições](../articles/azure-subscription-service-limits.md).

## <a name="connect-vms-in-a-virtual-network"></a>Conectar VMs em uma rede virtual
Para conectar máquinas virtuais em uma rede virtual:

1. Crie a rede virtual no [portal do Azure](../articles/virtual-network/virtual-networks-create-vnet-classic-pportal.md).
2. Crie o conjunto de serviços de nuvem para sua implantação de modo a refletir seu design para conjuntos de disponibilidade e balanceamento de carga. No portal clássico do Azure, clique em **Novo > Computação > Serviço de Nuvem > Criação Personalizada** para cada serviço de nuvem.
3. Para criar cada máquina virtual nova, clique em **Novo > Computação > Máquina Virtual > Da Galeria**. Escolha o serviço de nuvem e a rede virtual corretos para a VM. Se o serviço de nuvem já fizer parte de uma rede virtual, seu nome já estará selecionado para você.

![Selecionar um serviço de nuvem para uma máquina virtual](./media/virtual-machines-common-classic-connect-vms/VMConfig1.png)

## <a name="connect-vms-in-a-standalone-cloud-service"></a>Conectar VMs em um serviço de nuvem autônomo
Para conectar máquinas virtuais em um serviço de nuvem autônomo:

1. Crie o serviço de nuvem no [portal clássico do Azure](http://manage.windowsazure.com). Clique em **Novo > Computação > Serviço de Nuvem > Criação Personalizada**. Ou você pode criar o serviço de nuvem para sua implantação ao criar sua primeira máquina virtual.
2. Ao criar as máquinas virtuais, escolha o nome do serviço de nuvem criado na etapa anterior.
   
   ![Adicionar uma máquina virtual a um serviço de nuvem existente](./media/virtual-machines-common-classic-connect-vms/Connect-VM-to-CS.png)

