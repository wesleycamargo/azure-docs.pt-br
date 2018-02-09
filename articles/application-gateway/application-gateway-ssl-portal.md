---
title: "Criar um gateway de aplicativo com terminação SSL – Portal do Azure | Microsoft Docs"
description: "Saiba como criar um gateway de aplicativo e adicionar um certificado para a terminação SSL usando o portal do Azure."
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: davidmu
ms.openlocfilehash: daab3ada5ef0cc20883130e4c12b1dc3570e63b1
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2018
---
# <a name="create-an-application-gateway-with-ssl-termination-using-the-azure-portal"></a>Criar um gateway de aplicativo com terminação SSL usando o portal do Azure

Você pode usar o portal do Azure para criar um [gateway de aplicativo](application-gateway-introduction.md) com um certificado de terminação de SSL que usa máquinas virtuais para servidores back-end.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Crie um certificado autoassinado
> * Criar um gateway de aplicativo com o certificado
> * Criar as máquinas virtuais usadas como servidores de back-end

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon no portal do Azure em [http://portal.azure.com](http://portal.azure.com)

## <a name="create-a-self-signed-certificate"></a>Crie um certificado autoassinado

Nesta seção, você usa [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) para criar um certificado autoassinado que você carrega no portal do Azure ao criar o ouvinte para o gateway de aplicativo.

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

1. Clique em **Novo** no canto superior esquerdo do portal do Azure.
2. Selecione **Rede** e depois **Gateway de Aplicativo** na lista em destaque.
3. Digite *myAppGateway* para o nome do gateway de aplicativo e *myResourceGroupAG* para o novo grupo de recursos.
4. Aceite os valores padrão para as outras configurações e, em seguida, clique em **OK**.
5. Clique em **Escolher uma rede virtual**, clique em **Criar novo** e insira esses valores para a rede virtual:

    - *myVNet* – para o nome da rede virtual.
    - *10.0.0.0/16* – para o espaço de endereço da rede virtual.
    - *myAGSubnet* – para o nome da sub-rede.
    - *10.0.0.0/24* - para o espaço de endereço da sub-rede.

    ![Criar rede virtual](./media/application-gateway-ssl-portal/application-gateway-vnet.png)

6. Clique em **OK** para criar a rede virtual e a sub-rede.
7. Clique em **Escolher um endereço IP público**, clique em **Criar novo** e digite o nome do endereço IP público. Neste exemplo, o endereço IP público é denominado *myAGPublicIPAddress*. Aceite os valores padrão para as outras configurações e, em seguida, clique em **OK**.
8. Clique em **HTTPS** para o protocolo do ouvinte e verifique se a porta é definida como **443**.
9. Clique no ícone de pasta e navegue até o certificado *appgwcert.pfx* que você criou anteriormente para carregá-lo.
10. Insira *mycert1* para o nome do certificado e *Azure123456!* como a senha e clique em **OK**.

    ![Criar novo gateway de aplicativo](./media/application-gateway-ssl-portal/application-gateway-create.png)

11. Examine as configurações na página de resumo e, em seguida, clique em **OK** para criar os recursos de rede e o gateway de aplicativo. A criação do gateway de aplicativo pode levar vários minutos, aguarde até que a implantação seja concluída com êxito antes de passar para a próxima seção.

### <a name="add-a-subnet"></a>Adicionar uma sub-rede

1. Clique em **Todos os recursos** no menu esquerdo e depois clique em **myVNet** da lista de recursos.
2. Clique em **Sub-redes** e, em seguida, clique em **Sub-rede**.

    ![Criar sub-rede](./media/application-gateway-ssl-portal/application-gateway-subnet.png)

3. Digite *myBackendSubnet* para o nome da sub-rede e depois clique em **OK**.

## <a name="create-backend-servers"></a>Criar servidores de back-end

Neste exemplo, você cria duas máquinas virtuais para serem usadas como servidores de back-end para o gateway do aplicativo. Você também pode instalar o IIS nas máquinas virtuais para verificar se o gateway de aplicativo foi criado com êxito.

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. Clique em **Novo**.
2. Clique em **Computação** e, em seguida, selecione **Datacenter do Windows Server 2016** na lista em destaque.
3. Insira esses valores para a máquina virtual:

    - *myVM* – para o nome da máquina virtual.
    - *azureuser* – para o nome de usuário do administrador.
    - *Azure123456!* para a senha.
    - Clique em **Usar existente** e selecione *myResourceGroupAG*.

4. Clique em **OK**.
5. Selecione **DS1_V2** para o tamanho da máquina virtual e clique em **Selecionar**.
6. Verifique se **myVNet** está selecionado para a rede virtual e se a sub-rede é **myBackendSubnet**. 
7. Clique em **Desabilitado** para desabilitar o diagnóstico de inicialização.
8. Clique em **OK**, examine as configurações na página de resumo e, em seguida, clique em **Criar**.

### <a name="install-iis"></a>Instalar o IIS

1. Abra o shell interativo e verifique se ele está definido como **PowerShell**.

    ![Instalar a extensão personalizada](./media/application-gateway-ssl-portal/application-gateway-extension.png)

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

3. Clique em **Todos os recursos** e clique em **myAppGateway**.
4. Clique em **Pools de back-end**. Um pool padrão foi criado automaticamente com o gateway de aplicativo. Clique em **appGateayBackendPool**.
5. Clique em **Adicionar destino** para adicionar cada máquina virtual que você criou para o pool de back-end.

    ![Adicionar servidores de back-end](./media/application-gateway-ssl-portal/application-gateway-backend.png)

6. Clique em **Salvar**.

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicativo

1. Clique em **Todos os recursos** e, em seguida, clique em **myAGPublicIPAddress**.

    ![Registrar o endereço IP público do gateway de aplicativo](./media/application-gateway-ssl-portal/application-gateway-ag-address.png)

2. Copie o endereço IP público e cole-o na barra de endereços do seu navegador. Para aceitar o aviso de segurança caso tenha usado um certificado autoassinado, selecione Detalhes e depois prossiga para a página da Web:

    ![Aviso de segurança](./media/application-gateway-ssl-portal/application-gateway-secure.png)

    Seu site de IIS protegido é exibido, como no exemplo a seguir:

    ![Testar a URL de base no gateway de aplicativo](./media/application-gateway-ssl-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Crie um certificado autoassinado
> * Criar um gateway de aplicativo com o certificado
> * Criar as máquinas virtuais usadas como servidores de back-end

Para saber mais sobre os gateways de aplicativo e seus recursos associados, continue para os artigos de instrução.