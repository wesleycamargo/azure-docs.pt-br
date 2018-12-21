---
title: Início Rápido – Direcionar o tráfego da Web com o Gateway de Aplicativo do Azure – portal do Azure | Microsoft Docs
description: Saiba como usar o portal do Azure para criar um Gateway de Aplicativo do Azure que direciona o tráfego da Web para máquinas virtuais em um pool de back-end.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 11/15/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 7e11affece7e7eb133aa22e159ec07d4f15e96f7
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52999611"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-portal"></a>Início Rápido: Direcionar o tráfego da Web com o Gateway de Aplicativo do Azure – portal do Azure

Com o Gateway de Aplicativo do Azure, você pode direcionar seu tráfego de web de aplicativo para recursos específicos designando ouvintes para portas, criando regras, e adicionando recursos a um pool de back-end.

Este guia de início rápido mostra como usar o portal do Azure para criar rapidamente o gateway de aplicativo com duas máquinas virtuais em seu pool de back-end. Em seguida, teste-o para verificar se ele está funcionando corretamente.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no portal do Azure em [https://portal.azure.com](https://portal.azure.com)

## <a name="create-an-application-gateway"></a>Criar um Gateway de Aplicativo

Uma rede virtual é necessária para a comunicação entre os recursos que você criar. Duas sub-redes são criadas neste exemplo: um para o gateway de aplicativo e a outra para os servidores de back-end. Você pode criar uma rede virtual ao mesmo tempo que cria o gateway de aplicativo.

1. Clique em **Criar um recurso** encontrado na parte superior esquerda do portal do Azure.
2. Clique em **Networking** e, em seguida, clique em **Gateway de Aplicativo** na lista em destaque.

### <a name="basics"></a>Noções básicas

1. Insira esses valores para o gateway de aplicativo:

    - *myAppGateway* - para o nome do gateway de aplicativo.
    - *myResourceGroupAG* - para o novo grupo de recursos.

    ![Criar novo gateway de aplicativo](./media/application-gateway-create-gateway-portal/application-gateway-create.png)

2. Aceite os valores padrão para as outras configurações e, em seguida, clique em **OK**.

### <a name="settings"></a>Configurações

1. Clique em **Escolher uma rede virtual**, clique em **Criar novo** e insira esses valores para a rede virtual:

    - *myVNet* – para o nome da rede virtual.
    - *10.0.0.0/16* – para o espaço de endereço da rede virtual.
    - *myAGSubnet* – para o nome da sub-rede.
    - *10.0.0.0/24* - para o intervalo de endereços de sub-rede.

    ![Criar rede virtual](./media/application-gateway-create-gateway-portal/application-gateway-vnet.png)

6. Clique em **OK** para voltar a página de configuração.
7. Em **Configuração IP de frontend**, certifique-se de que o **tipo de endereço IP** esteja definido como **público** e **Endereço IP público**, assegure **Criar novo** está selecionado. Digite *myAGPublicIPAddress* para o nome do endereço IP público. Aceite os valores padrão para as outras configurações e, em seguida, clique em **OK**.

### <a name="summary"></a>Resumo

Examine as configurações na página de resumo e clique em **OK** para criar a rede virtual, o endereço IP público e o gateway de aplicativo. O gateway de aplicativo pode demorar vários minutos para ser criado. Aguarde até que a implantação seja concluída com êxito antes de passar para a próxima seção.

## <a name="add-a-subnet"></a>Adicionar uma sub-rede

1. Clique em **Todos os recursos** no menu esquerdo e depois clique em **myVNet** da lista de recursos.
2. Clique em **subredes** e, em seguida, clique em **+ subrede**.

    ![Criar sub-rede](./media/application-gateway-create-gateway-portal/application-gateway-subnet.png)

3. Digite *myBackendSubnet* para o nome da sub-rede e depois clique em **OK**.

## <a name="create-backend-servers"></a>Criar servidores de back-end

Neste exemplo, você cria duas máquinas virtuais que são usadas como servidores de backend para o gateway de aplicativo. Você também pode instalar o IIS nas máquinas virtuais para verificar se o gateway de aplicativo foi criado com êxito.

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. No portal do Azure, clique em **Criar um recurso**.
2. Clique em **Computação** e, em seguida, selecione **Datacenter do Windows Server 2016** na lista em destaque.
3. Insira esses valores para a máquina virtual:

    - *myResourceGroupAG* para o grupo de recursos.
    - *myVM* – para o nome da máquina virtual.
    - *azureuser* – para o nome de usuário do administrador.
    - *Azure123456!* para a senha.

   Aceite os outros padrões e clique em **Próximo: Discos**.
4. Aceite os padrões de disco e clique em **Avançar: Rede**.
5. Verifique se **myVNet** está selecionado para a rede virtual e se a sub-rede é **myBackendSubnet**.
6. Aceite os outros padrões e clique em **Próximo: Gerenciamento**.
7. Clique em **Desligar** para desabilitar o diagnóstico de inicialização. Aceite os outros padrões e clique em **Revisar + criar**.
8. Revise as configurações na página de resumo e clique em **Criar**.
9. Aguarde a criação da máquina virtual concluir antes de continuar.

### <a name="install-iis"></a>Instalar o IIS

1. Abra o shell interativo e verifique se ele está definido como **PowerShell**.

    ![Instalar a extensão personalizada](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Execute o comando a seguir para instalar o IIS na máquina virtual: 

    ```azurepowershell-interactive
    Set-AzureRmVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName myVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. Crie uma segunda máquina virtual e instale o IIS usando as etapas que você acabou de concluir. Insira *myVM2* para seu nome e para VMName em Set-AzureRmVMExtension.

### <a name="add-backend-servers"></a>Adicionar servidores de back-end

1. Clique em **Todos os recursos** e clique em **myAppGateway**.
4. Clique em **Pools de back-end**. Um pool padrão foi criado automaticamente com o gateway de aplicativo. Clique em **appGatewayBackendPool**.
5. Em **Destinos**, clique em **Endereço IP ou FQDN**, selecione **Máquina virtual**.
6. Sob **Máquina Virtual**, adicionar máquinas virtuais myVM e myVM2 e suas interfaces de rede associado.

    ![Adicionar servidores de back-end](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Clique em **Salvar**.

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicativo

1. Localize o endereço IP público para o gateway do aplicativo na tela de Visão geral. Clique em **Todos os recursos** e clique em **myAGPublicIPAddress**.

    ![Registre o endereço IP público do gateway de aplicativo](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png)

2. Copie o endereço IP público e cole-o na barra de endereços do seu navegador.

    ![Teste o gateway de aplicativo](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua o grupo de recursos, o gateway de aplicativo e todos os recursos relacionados. Para fazer isso, selecione o grupo de recursos na folha do gateway de aplicativo e clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Gerenciar o tráfego da web com um gateway de aplicativo usando a CLI do Azure](./tutorial-manage-web-traffic-cli.md)
