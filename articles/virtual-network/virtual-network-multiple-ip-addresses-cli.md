---
title: "Diversos endereços IP para máquinas virtuais – CLI do Azure | Microsoft Docs"
description: "Saiba como atribuir diversos endereços IP a uma máquina virtual usando a CLI do Azure | Resource Manager."
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: bf92cdc3f6adf1bfaffbcae4e8a0257d0682e33d
ms.openlocfilehash: 0bb3e5f74cb6d013a2b14b4e4c81f0936f90004a


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-azure-cli"></a>Atribuir vários endereços IP a máquinas virtuais usando a CLI do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
> * [CLI](virtual-network-multiple-ip-addresses-cli.md)

Uma VM (máquina virtual) do Azure tem uma ou mais NICs (adaptadores de rede) conectadas a ele. Qualquer NIC pode ter um ou mais endereços IP públicos e privados estáticos ou dinâmicos atribuídos a ele. A atribuição de vários endereços IP a uma VM permite as seguintes capacidades:

* Hospede vários sites ou serviços com diferentes endereços IP e os certificados SSL em um único servidor.
* Funcione como um dispositivo virtual de rede, como um firewall ou balanceador de carga.
* A capacidade de adicionar qualquer um dos endereços IP privados para qualquer uma das NICs a um pool de back-end do Azure Load Balancer. No passado, somente o endereço IP primário para a NIC primária pode ser adicionado a um pool de back-end. Para saber mais sobre como balancear a carga de várias configurações de IP, leia o artigo [Balanceamento de carga de várias configurações de IP](../load-balancer/load-balancer-multiple-ip.md).

Todas as NICs anexadas a uma VM têm uma ou mais configurações IP associadas a elas. Cada configuração recebe um endereço IP privado estático ou dinâmico. Cada configuração também pode ter um recurso de endereço IP público associado a ele. Um recurso de endereço IP público tem um endereço IP dinâmico ou estático atribuído a ele. Se você não está familiarizado com endereços IP no Azure, leia o artigo [endereços IP no Azure](virtual-network-ip-addresses-overview-arm.md) para saber mais sobre eles.

Este artigo explica como usar o PowerShell para atribuir vários endereços IP a uma VM criada por meio do modelo de implantação do Azure Resource Manager. Múltiplos endereços IP não podem ser atribuídos a recursos criados por meio do modelo de implantação clássico. Para saber mais sobre modelos de implantação do Azure, leia o artigo [Compreender os modelos de implantação](../resource-manager-deployment-model.md).

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

## <a name="scenario"></a>Cenário
Uma VM com uma única NIC é criada e conectada a uma rede virtual. A VM requer três endereços IP *privados* diferentes e dois endereços IP *públicos*. Os endereços IP são atribuídos às seguintes configurações de IP:

* **IPConfig-1:** atribui um endereço IP privado *dinâmico* (padrão) e um endereço IP público *estático*.
* **IPConfig-2:** atribui um endereço IP privado *estático* e um endereço IP público *estático*.
* **IPConfig-3:** atribui um endereço IP privado *dinâmico* e nenhum endereço IP público.
  
    ![Vários endereços IP](./media/virtual-network-multiple-ip-addresses-powershell/OneNIC-3IP.png)

As configurações de IP são associadas à NIC quando a NIC é criada e a NIC é conectada à máquina Virtual quando a VM é criada. Os tipos de endereços IP usados para o cenário são para fins de ilustração. Você pode atribuir quaisquer tipos de atribuição e de endereço IP necessários.

## <a name="a-name--createacreate-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Criar uma VM com vários endereços IP

As etapas a seguir explicam como criar uma VM de exemplo com vários endereços IP, como descrito no cenário. Altere os nomes da variável e os tipos de endereço IP como exigido por sua implementação.

1. Instale e configure a CLI do Azure seguindo as etapas do artigo [Instalar e configurar a CLI do Azure](../xplat-cli-install.md) e faça logon em sua conta do Azure.

2. Registre-se para a visualização ao enviar um email para [Vários IPs](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) com sua ID de assinatura e o uso pretendido. Não tente concluir as etapas restantes:
    - Até que você receba um email notificando de que foi aceito para a visualização
    - Sem seguir as instruções no email recebido
3. [Crie um grupo de recursos](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-resource-groups-and-choose-deployment-locations) seguido por uma [rede virtual e uma sub-rede](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-network-and-subnet). Altere os campos ``` --address-prefixes ``` e ```--address-prefix``` para o seguinte para seguir o cenário exato descrito neste artigo:

    ```azurecli
    --address-prefixes 10.0.0.0/16
    --address-prefix 10.0.0.0/24
    ```
    >[!NOTE] 
    >O artigo mencionado acima usa Europa Ocidental como o local para criar recursos, mas este artigo usa Centro-Oeste dos EUA. Faça as alterações de localização de forma adequada.

4. [Crie uma nova conta de armazenamento](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-storage-account) para sua VM.


5. Crie a NIC e as configurações do IP que você deseja atribuir à NIC. Você pode adicionar, remover ou alterar as configurações conforme necessário. As configurações a seguir são descritas no cenário:

    **IPConfig-1**

    Insira os comandos a seguir para criar:

    - Um recurso de endereço IP público com um endereço IP público estático
    - Uma configuração de IP com o recurso de endereço IP público e um endereço IP privado dinâmico

    ```azurecli
    azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP --domain-name-label mypublicdns --allocation-method Static
    ```
    > [!NOTE]
    > Endereços IP públicos têm um valor nominal. Para saber mais sobre preços de endereço IP, leia a página [Preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Há um limite para o número de endereços IP públicos que podem ser usados em uma assinatura. Para saber mais sobre os limites, leia o artigo [Limites do Azure](../azure-subscription-service-limits.md#networking-limits).

    ```azurecli
    azure network nic create --resource-group myResourceGroup --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic1 --public-ip-name myPublicIP
    ```

    **IPConfig-2**

     Insira os seguintes comandos para criar um novo recurso de endereço IP público e uma nova configuração de IP com um endereço IP público estático e um endereço IP privado estático:
    
    ```azurecli
    azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP2 --domain-name-label mypublicdns2 --allocation-method Static

    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-2 --private-ip-address 10.0.0.5 --public-ip-name myPublicIP2
    ```

    **IPConfig-3**

    Insira os comandos a seguir para criar uma configuração de IP com um endereço IP privado dinâmico e nenhum endereço IP público:

    ```azurecli
    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-3
    ```

    >[!NOTE] 
    >Embora este artigo atribua todas as configurações de IP a uma única NIC, você também pode atribuir várias configurações de IP para qualquer NIC em uma VM. Para saber como criar uma VM com várias NICs, leia o artigo Criar uma VM com várias NICs.

6. Artigo [Criar uma VM Linux](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-the-linux-vms). Remova a propriedade ```  --availset-name myAvailabilitySet \ ``` porque ela não é necessária neste cenário. Use a localização apropriada com base em seu cenário. 

    >[!WARNING] 
    > A Etapa 6 do artigo Criar uma VM falhará se o tamanho da VM não tiver suporte na localização selecionada. Execute o comando a seguir para obter uma lista completa de VMs em Centro-Oeste dos EUA, por exemplo. Esse nome de localização pode ser alterado com base em seu cenário.
    > 
    >       azure vm sizes --location westcentralus

    Para alterar o tamanho da VM para Standard DS2 v2, por exemplo, basta adicionar a seguinte propriedade ```  --vm-size Standard_DS3_v2``` ao comando ``` azure vm create ``` na etapa 6.

7. Insira o comando a seguir para exibir a NIC e as configurações de IP associadas:

    ```azurecli
    azure network nic show --resource-group myResourceGroup --name myNic1
    ```

## <a name="a-nameosconfigaadd-ip-addresses-to-a-vm-operating-system"></a><a name="OsConfig"></a>Adicionar endereços IP em um sistema operacional da VM

Conecte-se e faça logon em uma VM criada com vários endereços IP privados. Você deve adicionar manualmente todos os endereços IP privados (incluindo o principal) que você adicionou à VM. Complete as etapas a seguir para seu sistema operacional VM:

### <a name="windows"></a>Windows

1. Em um prompt de comando, digite *ipconfig /all*.  Você vê apenas o endereço IP privado *Primário* (por meio do DHCP).
2. Digite *ncpa.cpl* no prompt de comando para abrir a janela **Conexões de rede**.
3. Abra as propriedades de **Rede local**.
4. Clique duas vezes em versão do Protocolo de Internet 4 (IPv4).
5. Selecione **Usar o seguinte endereço IP** e insira os seguintes valores:

    * **Endereço IP**: insira o endereço IP privado *primário*
    * **Máscara de sub-rede**: defina com base na sua sub-rede. Por exemplo, se a sub-rede for uma sub-rede /24, então, a máscara de sub-rede será 255.255.255.0.
    * **Gateway padrão**: o primeiro endereço IP na sub-rede. Se sua sub-rede for 10.0.0.0/24, o endereço IP do gateway será 10.0.0.1.
    * Clique em **Usar os seguintes endereços do servidor DNS** e insira os seguintes valores:
        * **Servidor DNS preferencial**: digite 168.63.129.16 se você não estiver usando seu próprio servidor DNS.  Se você estiver usando seu próprio servidor DNS, digite o endereço IP do seu servidor.
    * Clique no botão **Avançado** e adicione mais endereços IP. Adicione cada um dos endereços IP privados secundários listados na etapa 8 à NIC com a mesma sub-rede especificada para o endereço IP primário.
    * Clique em **OK** para fechar as configurações de TCP/IP e, em seguida, em **OK** novamente para fechar as configurações do adaptador. A conexão RDP é restabelecida.
6. Em um prompt de comando, digite *ipconfig /all*. Todos os endereços IP que você adicionou são mostrados e o DHCP está desativado.
    
### <a name="linux-ubuntu"></a>Linux (Ubuntu)

1. Abra uma janela de terminal.
2. Verifique se você é o usuário raiz. Se não for, digite o seguinte comando:

    ```bash
    sudo -i
    ```

3. Atualize o arquivo de configuração do adaptador de rede (supondo que 'eth0').

    * Mantenha o item de linha existente para o dhcp. O endereço IP principal permanece configurado como era anteriormente.
    * Adicione uma configuração para um endereço IP estático adicional com os seguintes comandos:

        ```bash
        cd /etc/network/interfaces.d/
        ls
        ```

    Você deve ver um arquivo. cfg.
4. Abra o arquivo: vi *filename*.

    Você verá as seguintes linhas ao final do arquivo:

    ```bash
    auto eth0
    iface eth0 inet dhcp
    ```

5. Adicione as seguintes linhas após as linhas existentes neste arquivo:

    ```bash
    iface eth0 inet static
    address <your private IP address here>
    ```

6. Salve o arquivo usando o seguinte comando:

    ```bash
    :wq
    ```

7. Reinicie o adaptador de rede com o seguinte comando:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

    > [!IMPORTANT]
    > Execute ifdown e ifup na mesma linha se você estiver usando uma conexão remota.
    >

8. Verifique se que o endereço IP foi adicionado ao adaptador de rede com o seguinte comando:

    ```bash
    Ip addr list eth0
    ```

    Você verá o endereço IP adicionado como parte da lista.
    
### <a name="linux-redhat-centos-and-others"></a>Linux (Redhat, CentOS e outros)

1. Abra uma janela de terminal.
2. Verifique se você é o usuário raiz. Se não for, digite o seguinte comando:

    ```bash
    sudo -i
    ```

3. Digite sua senha e siga as instruções conforme solicitado. Quando você for o usuário raiz, navegue até a pasta de scripts de rede com o seguinte comando:

    ```bash
    cd /etc/sysconfig/network-scripts
    ```

4. Liste os arquivos ifcfg relacionados usando o seguinte comando:

    ```bash
    ls ifcfg-*
    ```

    Você deve ver *ifcfg-eth0* como um dos arquivos.

5. Copie o arquivo *ifcfg-eth0* e nomeie-o *ifcfg-eth0:0* com o seguinte comando:

    ```bash
    cp ifcfg-eth0 ifcfg-eth0:0
    ```

6. Edite o arquivo *ifcfg-eth0:0* com o seguinte comando:

    ```bash
    vi ifcfg-eth1
    ```

7. Altere o dispositivo para o nome apropriado no arquivo; *eth0:0* , nesse caso, com o seguinte comando:

    ```bash
    DEVICE=eth0:0
    ```

8. Altere a linha *IPADDR = YourPrivateIPAddress* para refletir o endereço IP.
9. Salve o arquivo com o seguinte comando:

    ```bash
    :wq
    ```

10. Reinicie os serviços de rede e certifique-se de que as alterações foram bem-sucedidas executando os seguintes comandos:

    ```bash
    /etc/init.d/network restart
    Ipconfig
    ```

    Você verá o endereço IP adicionado, *eth0:0*, na lista retornada.

## <a name="a-nameaddaadd-ip-addresses-to-a-vm"></a><a name="add"></a>Adicionar endereços IP a uma VM

Você pode adicionar endereços IP públicos e privados adicionais para uma NIC existente ao concluir as etapas a seguir. Os exemplos baseiam-se no [cenário](#Scenario) descrito neste artigo.

1. Abra a CLI do Azure e conclua as etapas restantes nesta seção dentro de uma única sessão da CLI. Se você ainda não tiver a CLI do Azure instalada e configurada, conclua as etapas do artigo [Instalar e configurar a CLI do Azure](../xplat-cli-install.md) e faça logon em sua conta do Azure.

2. Registre-se para a visualização ao enviar um email para [Vários IPs](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) com sua ID de assinatura e o uso pretendido. Não tente concluir as etapas restantes:
    - Até que você receba um email notificando de que foi aceito para a visualização
    - Sem seguir as instruções no email recebido


3. Conclua as etapas em uma das seções a seguir, com base em seus requisitos:

    **Adicionar um endereço IP privado**
    
    Para adicionar um endereço IP privado a uma NIC, você deve criar uma configuração de IP usando o comando a seguir.  Se você deseja adicionar um endereço IP privado dinâmico, remova ```-PrivateIpAddress 10.0.0.7``` antes de inserir o comando. Ao especificar um endereço IP estático, ele deve ser um endereço não usado para a sub-rede.

    ```azurecli
    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 --private-ip-address 10.0.0.7 --name IPConfig-4
    ```
    Crie quantas configurações forem necessárias, usando nomes de configuração exclusivos e endereços IP privados (para configurações com endereços IP estáticos).

    **Adicionar um endereço IP público**
    
    Um endereço IP público é adicionado por meio de sua associação a uma nova configuração de IP ou de uma configuração de IP existente. Conclua as etapas em uma das seções a seguir, quando você precisar.

    > [!NOTE]
    > Endereços IP públicos têm um valor nominal. Para saber mais sobre preços de endereço IP, leia a página [Preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Há um limite para o número de endereços IP públicos que podem ser usados em uma assinatura. Para saber mais sobre os limites, leia o artigo [Limites do Azure](../azure-subscription-service-limits.md#networking-limits).
    >

    **Associar o recurso a uma nova configuração de IP**
    
    Sempre que você adicionar um endereço IP público em uma nova configuração de IP, também deverá adicionar um endereço IP privado, pois todas as configurações de IP devem ter um endereço IP privado. Você pode adicionar um recurso de endereço IP público existente ou criar um novo. Para criar um novo, insira o seguinte comando:
    
    ```azurecli
    azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP3 --domain-name-label mypublicdns3
    ```

    Para criar uma nova configuração de IP com um endereço IP privado dinâmico e o recurso de endereço IP público *myPublicIP3* associado, insira o seguinte comando:

    ```azurecli
    azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic --name IPConfig-4 --public-ip-name myPublicIP3
    ```

    **Associar o recurso a uma configuração de IP existente**
    Um recurso de endereço IP público só pode ser associado a uma configuração de IP que ainda não tenha um associado. Você pode determinar se uma configuração de IP tem um endereço IP público associado inserindo o seguinte comando:

    ```azurecli
    azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
    ```

    Procure uma linha semelhante à seguinte na saída retornada:
    
        Name               Provisioning state  Primary  Private IP allocation  Private IP version  Private IP address  Subnet    Public IP
        -----------------  ------------------  -------  ---------------------  ------------------  ------------------  --------  -----------
        default-ip-config  Succeeded           true     Dynamic                IPv4                10.0.0.4            mySubnet  myPublicIP
        IPConfig-2         Succeeded           false    Static                 IPv4                10.0.0.5            mySubnet  myPublicIP2
        IPConfig-3         Succeeded           false    Dynamic                IPv4                10.0.0.6            mySubnet
     
    Como a coluna **IP Público** para *IpConfig 3* está em branco, nenhum recurso de endereço IP público está associado a ele no momento. Você pode adicionar um recurso de endereço IP público existente como IpConfig-3 ou inserir o seguinte comando para criar um:

    ```azurecli
    azure network public-ip create --resource-group  myResourceGroup --location westcentralus --name myPublicIP3 --domain-name-label mypublicdns3 --allocation-method Static
    ```
    
    Insira o comando a seguir para associar o recurso de endereço IP público à configuração de IP existente chamada *IPConfig-3*:
    
    ```azurecli
    azure network nic ip-config set --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-3 --public-ip-name myPublicIP3
    ```


7. Exiba os endereços IP privados e o recurso de endereço IP público atribuído à NIC, digitando o seguinte comando:

    ```azurecli
    azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
    ```
    Você deverá ver uma saída semelhante ao seguinte: 
    
        Name               Provisioning state  Primary  Private IP allocation  Private IP version  Private IP address  Subnet    Public IP
        -----------------  ------------------  -------  ---------------------  ------------------  ------------------  --------  -----------
        default-ip-config  Succeeded           true     Dynamic                IPv4                10.0.0.4            mySubnet  myPublicIP
        IPConfig-2         Succeeded           false    Static                 IPv4                10.0.0.5            mySubnet  myPublicIP2
        IPConfig-3         Succeeded           false    Dynamic                IPv4                10.0.0.6            mySubnet  myPublicIP3
     
9. Adicione os endereços IP que você adicionou à NIC para o sistema operacional da VM seguindo as instruções da seção [Adicionar endereços IP a um sistema operacional de VM](#OsConfig) deste artigo.


<!--HONumber=Nov16_HO3-->


