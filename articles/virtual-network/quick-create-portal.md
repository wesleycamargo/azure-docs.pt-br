---
title: Criar uma rede virtual no Azure - Portal | Microsoft Docs
description: Aprenda rapidamente a criar uma rede virtual usando o Portal do Azure. Uma rede virtual permite que muitos tipos de recursos do Azure comuniquem-se em modo privado.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/25/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 61100b9786245204502686a47e5aae2a6d210259
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-virtual-network-using-the-azure-portal"></a>Criar uma Rede Virtual usando o portal do Azure

Neste artigo, você aprenderá como criar uma rede virtual. Após criar uma rede virtual, você implantará duas máquinas virtuais na rede virtual e se comunicarão em modo privado.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="log-in-to-azure"></a>Fazer logon no Azure 

Faça logon no Portal do Azure em http://portal.azure.com.

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. Clique em **+ Novo** no canto superior esquerdo do Portal do Azure.

2. Selecione **Rede** e, sem seguida, selecione **Rede Virtual**.

3. Conforme mostrado na imagem a seguir, digite *myVirtualNetwork* para **Nome**, *myResourceGroup* para **Grupo de recursos**, selecione um **Local** e sua **Assinatura**, aceite os padrões restantes e, em seguida, clique em **Criar**. 

    ![Insira s informações básicas sobre sua rede virtual](./media/quick-create-portal/virtual-network.png)

    O **Espaço de endereço** é especificado na notação CIDR. Uma rede virtual contém zero ou mais sub-redes. O **Intervalo de endereços** da sub-rede padrão de 10.0.0.0/24 usa todo o intervalo de endereços da rede virtual para que outra sub-rede não possa ser criada na rede virtual usando o espaço e intervalo de endereços padrão. O intervalo de endereços especificado inclui os endereços IP 10.0.0.0-10.0.0.254. No entanto, apenas 10.0.0.4-10.0.0.254 estão disponíveis porque o Azure reserva os quatro primeiros endereços (0-3) e o último endereço em cada sub-rede. Os endereços IP disponíveis são atribuídos aos recursos implantados em uma rede virtual.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Uma rede virtual permite que vários tipos de recursos do Azure comuniquem-se em modo privado. Um tipo de recurso que pode ser implantado em uma rede virtual é uma máquina virtual. Crie duas máquinas virtuais na rede virtual para que você possa validar e entender como a comunicação entre máquinas virtuais em uma rede virtual funciona em uma etapa posterior.

1. Clique no botão **Novo** no canto superior esquerdo do Portal do Azure.

2. Selecione **Computação** e, em seguida, selecione **Windows Server 2016 Datacenter**.

3. Insira as informações da máquina virtual mostradas na figura a seguir. O **Nome de usuário** e **Senha** que você inserir serão utilizados para fazer logon na máquina virtual em uma etapa posterior. A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm). Selecione sua **Assinatura**, escolha usar o grupo de recursos *myResourceGroup* existente e assegure-se de que o **Local** selecionado seja o mesmo local em que a rede virtual foi criada. Ao concluir, clique em **OK**.

    ![Digite informações básicas sobre uma máquina virtual](./media/quick-create-portal/virtual-machine-basics.png)

4. Selecione um tamanho para a máquina virtual e clique em **Selecionar**. Para ver mais tamanhos, selecione **Exibir todos os** ou altere o filtro **Tipo de disco com suporte**. Os tamanhos exibidos poderão ser diferentes do exemplo a seguir: 

    ![Selecionar um tamanho para uma máquina virtual](./media/quick-create-portal/virtual-machine-size.png)

5. Em **Configurações**, *myVirtualNetwork* já deverá estar selecionado para a **Rede virtual**, mas, se não estiver, clique em **Rede virtual** e, em seguida, selecione *myVirtualNetwork*. Deixe *padrão* selecionado para **Sub-rede** e, em seguida, clique em **OK**.

    ![Selecione uma rede virtual](./media/quick-create-portal/virtual-machine-network-settings.png)

6. Na página **Resumo**, clique em **Criar** para iniciar a implantação da máquina virtual. 

7. A máquina virtual leva alguns minutos para criar. Após a criação, a máquina virtual será fixada no painel do Portal do Azure e o resumo de máquina virtual abrirá automaticamente. Clique em **Rede**.

    ![Informações de rede da máquina virtual](./media/quick-create-portal/virtual-machine-networking.png)

    É possível ver que o endereço do **IP Privado** é *10.0.0.4*. Na etapa 5, em **Configurações**, você selecionou a rede virtual *myVirtualNetwork* e aceitou a sub-rede nomeada *padrão* para **Sub-rede**. Ao [criar a rede virtual](#create-a-virtual-network), você aceitou o valor padrão de 10.0.0.0/24 para a sub-rede **Intervalo de endereços**. O servidor DHCP do Azure atribui o primeiro o endereço disponível para a sub-rede selecionada para a máquina virtual. Como o Azure reserva os primeiros quatro endereços (0-3) de cada sub-rede, 10.0.0.4 é o primeiro endereço IP disponível para a sub-rede.

    O **IP Público** atribuído é diferente do endereço atribuído à sua máquina virtual. O Azure atribui, por padrão, um endereço IP roteável de internet público para cada máquina virtual. O endereço IP público é atribuído à máquina virtual a partir de um [pool de endereços atribuídos a cada região do Azure](https://www.microsoft.com/download/details.aspx?id=41653). Enquanto o Azure sabe qual endereço IP público é atribuído a uma máquina virtual, o sistema operacional executado em uma máquina virtual não reconhece nenhum endereço IP público atribuído a ele.

8. Complete as etapas de 1 a 7 novamente, mas na etapa 3, nomeie a máquina virtual *myVm2*. 

9. Depois que a máquina virtual for criada, clique em **Rede**, como feito na etapa 7. É possível ver que o endereço **IP Privado** é *10.0.0.5*. Quando o Azure atribuiu previamente o primeiro endereço utilizável de *10.0.0.4* na sub-rede para a máquina virtual *myVm1*, ele atribuiu *10.0.0.5* para a máquina virtual *myVm2* porque era o próximo endereço disponível na sub-rede.

## <a name="connect-to-a-virtual-machine"></a>Conectar-se a uma máquina virtual

1. Conecte-se remotamente à máquina virtual *myVm1*. Na parte superior do Portal do Azure, digite *myVm1*. Clique em **myVm1** quando aparecer nos resultados da pesquisa. Clique no botão **Conectar** .

    ![Visão geral da máquina virtual](./media/quick-create-portal/virtual-machine-overview.png)


2. Após clicar no botão **Conectar**, um arquivo Remote Desktop Protocol (.rdp) será criado e baixado no computador.  
3. Abra o arquivo rdp baixado. Se solicitado, clique em **Conectar**. Insira o nome de usuário e a senha que você especificou ao criar a máquina virtual e, em seguida, clique em **OK**. Você pode receber um aviso do certificado durante o processo de logon. Clique em **Sim** ou em **Continuar** para prosseguir com o processo de conexão.

## <a name="validate-communication"></a>Validar a comunicação

A tentativa de executar ping de uma máquina virtual do Windows falha porque, por padrão, executar ping não é permitido por meio do firewall do Windows. Para permitir executar ping na *myVm1*, insira o seguinte comando a partir de um prompt de comando:

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

Para validar a comunicação com *myVm2*, insira o seguinte comando a partir de um prompt de comando na máquina virtual *myVm1*. Forneça as credenciais utilizadas quando a máquina virtual foi criada e, em seguida, complete a conexão:

```
mstsc /v:myVm2
```

A conexão de área de trabalho remota ocorre com êxito porque ambas as máquinas virtuais possuem endereços IP privados atribuídos da sub-rede *padrão* e porque a área de trabalho remota, por padrão, é aberta pelo firewall do Windows. Você pode se conectar em *myVm2* pelo nome do host porque o Azure fornece automaticamente a resolução de nomes DNS para todos os hosts dentro de uma rede virtual. De um prompt de comando, execute ping *myVm1* de *myVm2*.

```
ping myvm1
```

O ping é executado com êxito porque você permitiu através do firewall do Windows na máquina virtual *myVm1* na etapa anterior. Para confirmar a comunicação de saída para a Internet, insira o comando a seguir:

```
ping bing.com
```

Você recebe quatro respostas do bing.com. Por padrão, qualquer máquina virtual em uma rede virtual pode comunicar-se com a Internet.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua o grupo de recursos e todos os seus conteúdos. Na parte superior do Portal do Azure, digite *myResourceGroup*. Clique em **myResourceGroup** quando aparecer nos resultados da pesquisa. Clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você implantou uma rede virtual padrão com uma sub-rede e duas máquinas virtuais. Para saber como criar uma rede virtual personalizada com várias sub-redes e executar tarefas básicas de gerenciamento, continue no tutorial para criar uma rede virtual personalizada e gerenciá-la.


> [!div class="nextstepaction"]
> [Criar uma rede virtual personalizada e gerenciá-la](virtual-networks-create-vnet-arm-pportal.md#portal)
