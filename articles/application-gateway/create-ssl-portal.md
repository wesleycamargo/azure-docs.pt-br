---
title: Tutorial – Configurar um gateway de aplicativo com terminação SSL – portal do Azure
description: Neste tutorial, você aprende como configurar um gateway de aplicativo e adicionar um certificado para terminação SSL usando o portal do Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 4/17/2019
ms.author: victorh
ms.openlocfilehash: f3ba3eb12dc85a72c4e49c374e62209b83400d33
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59677835"
---
# <a name="tutorial-configure-an-application-gateway-with-ssl-termination-using-the-azure-portal"></a>Tutorial: Configurar um gateway de aplicativo com terminação SSL usando o portal do Azure

É possível usar o portal do Azure para configurar um [gateway de aplicativos](overview.md) com um certificado para terminação SSL que use máquinas virtuais para servidores back-end.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Crie um certificado autoassinado
> * Criar um gateway de aplicativo com o certificado
> * Criar as máquinas virtuais usadas como servidores de back-end
> * Testar o gateway de aplicativo

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no portal do Azure em [https://portal.azure.com](https://portal.azure.com)

## <a name="create-a-self-signed-certificate"></a>Crie um certificado autoassinado

Nesta seção, você usa [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) para criar um certificado autoassinado. Você pode carregar o certificado no portal do Azure quando você cria o ouvinte para o Gateway de Aplicativo.

No computador local, abra uma janela do Windows PowerShell como administrador. Execute o comando a seguir para criar o certificado:

```powershell
New-SelfSignedCertificate \
  -certstorelocation cert:\localmachine\my \
  -dnsname www.contoso.com
```

Você deverá ver algo como esta resposta:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com

Use [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) with the Thumbprint that was returned to export a pfx file from the certificate:
```

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate \
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 \
  -FilePath c:\appgwcert.pfx \
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Criar um Gateway de Aplicativo

Uma rede virtual é necessária para a comunicação entre os recursos que você criar. Duas sub-redes são criadas neste exemplo: um para o gateway de aplicativo e a outra para os servidores de back-end. Você pode criar uma rede virtual ao mesmo tempo que cria o gateway de aplicativo.

1. Selecione **Novo**, localizado no canto superior esquerdo do portal do Azure.
2. Selecione **Rede** e depois **Gateway de Aplicativo** na lista em destaque.
3. Digite *myAppGateway* para o nome do gateway de aplicativo e *myResourceGroupAG* para o novo grupo de recursos.
4. Aceite os valores padrão para as outras configurações e selecione **OK**.
5. Selecione **Escolher uma rede virtual** e **Criar** e insira estes valores para a rede virtual:

   - *myVNet* – para o nome da rede virtual.
   - *10.0.0.0/16* – para o espaço de endereço da rede virtual.
   - *myAGSubnet* – para o nome da sub-rede.
   - *10.0.0.0/24* - para o espaço de endereço da sub-rede.

     ![Criar rede virtual](./media/create-ssl-portal/application-gateway-vnet.png)

6. Selecione **OK** para criar a rede virtual e a sub-rede.
7. Selecione **Escolher um endereço IP público** e **Criar** e digite o nome do endereço IP público. Neste exemplo, o endereço IP público é denominado *myAGPublicIPAddress*. Aceite os valores padrão para as outras configurações e selecione **OK**.
8. Selecione **HTTPS** para o protocolo do ouvinte e verifique se a porta é definida como **443**.
9. Selecione o ícone de pasta e navegue até o certificado *appgwcert.pfx* que você criou anteriormente para carregá-lo.
10. Insira *mycert1* para o nome do certificado e *Azure123456!* como a senha e selecione **OK**.

    ![Criar novo gateway de aplicativo](./media/create-ssl-portal/application-gateway-create.png)

11. Examine as configurações na página de resumo e selecione **OK** para criar os recursos de rede e o gateway de aplicativo. A criação do gateway de aplicativo pode levar vários minutos, aguarde até que a implantação seja concluída com êxito antes de passar para a próxima seção.

### <a name="add-a-subnet"></a>Adicionar uma sub-rede

1. Selecione **Todos os recursos** no menu esquerdo e selecione **myVNet** na lista de recursos.
2. Selecione **Sub-redes** e, em seguida, selecione **Sub-rede**.

    ![Criar sub-rede](./media/create-ssl-portal/application-gateway-subnet.png)

3. Digite *myBackendSubnet* para o nome da sub-rede e depois selecione **OK**.

## <a name="create-backend-servers"></a>Criar servidores de back-end

Neste exemplo, você cria duas máquinas virtuais para serem usadas como servidores de back-end para o Gateway de Aplicativo. Você também pode instalar o IIS nas máquinas virtuais para verificar se o Gateway de Aplicativo foi criado com êxito.

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. Selecione **Novo**.
2. Clique em **Computação** e, em seguida, selecione **Datacenter do Windows Server 2016** na lista em Destaque.
3. Insira esses valores para a máquina virtual:

    - *myVM* – para o nome da máquina virtual.
    - *azureuser* – para o nome de usuário do administrador.
    - *Azure123456!* para a senha.
    - Clique em **Usar existente** e selecione *myResourceGroupAG*.

4. Selecione **OK**.
5. Selecione **DS1_V2** para o tamanho da máquina virtual e selecione **Selecionar**.
6. Verifique se **myVNet** está selecionado para a rede virtual e se a sub-rede é **myBackendSubnet**. 
7. Selecione **Desabilitado** para desabilitar o diagnóstico de inicialização.
8. Selecione **OK**, examine as configurações na página de resumo e, em seguida, selecione **Criar**.

### <a name="install-iis"></a>Instalar o IIS

1. Abra o shell interativo e verifique se ele está definido como **PowerShell**.

    ![Instalar a extensão personalizada](./media/create-ssl-portal/application-gateway-extension.png)

2. Execute o comando a seguir para instalar o IIS na máquina virtual: 

    ```azurepowershell-interactive
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName myVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. Crie uma segunda máquina virtual e instale o IIS usando as etapas que você acabou de concluir. Insira *myVM2* para seu nome e para VMName em Set-AzVMExtension.

### <a name="add-backend-servers"></a>Adicionar servidores de back-end

1. Selecione **Todos os recursos** e, em seguida, **myAppGateway**.
1. Selecione **Pools de back-end**. Um pool padrão foi criado automaticamente com o gateway de aplicativo. Selecione **appGatewayBackendPool**.
1. Selecione **Adicionar destino** para adicionar cada máquina virtual que você criou para o pool de back-end.

    ![Adicionar servidores de back-end](./media/create-ssl-portal/application-gateway-backend.png)

1. Clique em **Salvar**.

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicativo

1. Selecione **Todos os recursos** e, em seguida, **myAGPublicIPAddress**.

    ![Registrar o endereço IP público do gateway de aplicativo](./media/create-ssl-portal/application-gateway-ag-address.png)

2. Copie o endereço IP público e cole-o na barra de endereços do seu navegador. Para aceitar o aviso de segurança caso tenha usado um certificado autoassinado, selecione Detalhes e depois prossiga para a página da Web:

    ![Aviso de segurança](./media/create-ssl-portal/application-gateway-secure.png)

    Seu site de IIS protegido é exibido, como no exemplo a seguir:

    ![Testar a URL de base no gateway de aplicativo](./media/create-ssl-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre o que você pode fazer com o Gateway de Aplicativo do Azure](application-gateway-introduction.md)
