---
title: Criar um gateway de aplicativo com o firewall do aplicativo Web - portal do Azure | Microsoft Docs
description: Saiba como criar um gateway de aplicativo com o firewall do aplicativo Web usando o portal do Azure.
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
ms.openlocfilehash: d2b8fc65e6cd03f61151dbae66bb89821cdab13b
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2018
---
# <a name="create-an-application-gateway-with-a-web-application-firewall-using-the-azure-portal"></a>Criar um gateway de aplicativo com um firewall do aplicativo Web usando o portal do Azure

Você pode usar o portal do Azure para criar um [gateway de aplicativo](application-gateway-introduction.md) com um [firewall do aplicativo Web](application-gateway-web-application-firewall-overview.md) (WAF). O WAF usa as regras de [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) para proteger o seu aplicativo. Essas regras incluem proteção contra ataques, como injeção de SQL, ataques de script entre sites e sequestros de sessão.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Criar um gateway de aplicativo com o WAF habilitado
> * Criar as máquinas virtuais usadas como servidores de back-end
> * Criar uma conta de armazenamento e configurar diagnósticos

![Exemplo de Firewall do aplicativo Web](./media/application-gateway-web-application-firewall-portal/scenario-waf.png)

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon no portal do Azure em [http://portal.azure.com](http://portal.azure.com)

## <a name="create-an-application-gateway"></a>Criar um Gateway de Aplicativo

Uma rede virtual é necessária para a se comunicar entre os recursos que você criar. Duas sub-redes são criadas neste exemplo: um para o gateway de aplicativo e a outra para os servidores de back-end. Você pode criar uma rede virtual ao mesmo tempo que cria o gateway de aplicativo.

1. Clique no botão **Novo** encontrado no canto superior esquerdo do portal do Azure.
2. Selecione **Rede** e depois **Gateway de Aplicativo** na lista em destaque.
3. Insira esses valores para o gateway do aplicativo:

    - *myAppGateway* - para o nome do gateway do aplicativo.
    - *myResourceGroupAG* - para o novo grupo de recursos.
    - Selecione *WAF* para a camada do gateway de aplicativo.

    ![Criar novo gateway de aplicativo](./media/application-gateway-web-application-firewall-portal/application-gateway-create.png)

4. Aceite os valores padrão para as outras configurações e clique em **OK**.
5. Clique em **Escolher uma rede virtual**, clique em **Criar novo** e insira esses valores para a rede virtual:

    - *myVNet* – para o nome da rede virtual.
    - *10.0.0.0/16* – para o espaço de endereço da rede virtual.
    - *myAGSubnet* – para o nome da sub-rede.
    - *10.0.0.0/24* – para o espaço de endereço da sub-rede.

    ![Criar rede virtual](./media/application-gateway-web-application-firewall-portal/application-gateway-vnet.png)

6. Clique em **OK** para criar a rede virtual e a sub-rede.
7. Clique em **Escolher um endereço IP público**, clique em **Criar novo** e digite o nome do endereço IP público. Neste exemplo, o endereço IP público é denominado *myAGPublicIPAddress*. Aceite os valores padrão para as outras configurações e clique em **OK**.
8. Aceite os valores padrão para a configuração de ouvinte, deixe o firewall do aplicativo Web desabilitado e, em seguida, clique em **OK**.
9. Examine as configurações na página de resumo e clique em **OK** para criar os recursos de rede e o gateway de aplicativo. Pode demorar vários minutos para o gateway de aplicativo ser criado, aguarde até que a implantação seja concluída com êxito antes de passar para a próxima seção.

### <a name="add-a-subnet"></a>Adicionar uma sub-rede

1. Clique em **Todos os recursos** no menu esquerdo e depois clique em **myVNet** da lista de recursos.
2. Clique em **Sub-redes** e depois clique em **Sub-rede**.

    ![Criar sub-rede](./media/application-gateway-web-application-firewall-portal/application-gateway-subnet.png)

3. Insira *myBackendSubnet* como o nome da subrede e clique em **OK**.

## <a name="create-backend-servers"></a>Criar servidores de back-end

Neste exemplo, você cria duas máquinas virtuais para serem usadas como servidores de back-end para o gateway do aplicativo. Você também instala o IIS nas máquinas virtuais para verificar se o gateway do aplicativo foi criado com êxito.

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. Clique em **Novo**.
2. Clique em **Computação** e, em seguida, selecione **Windows Server 2016 Datacenter** na lista de Recursos.
3. Insira esses valores para a máquina virtual:

    - *myVM* – para o nome da máquina virtual.
    - *azureuser* – para o nome de usuário do administrador.
    - *Azure123456!* para a senha.
    - Clique em **Usar existente** e selecione *myResourceGroupAG*.

4. Clique em **OK**.
5. Selecione **DS1_V2** para o tamanho da máquina virtual e clique em **Selecionar**.
6. Verifique se **myVNet** está selecionada para a rede virtual e se a sub-rede é **myBackendSubnet**. 
7. Clique em **Desabilitado** para desabilitar o diagnóstico de inicialização.
8. Clique em **OK**, examine as configurações na página de resumo e clique em **Criar**.

### <a name="install-iis"></a>Instalar o IIS

1. Abra o shell interativo e verifique se ele está definido como **PowerShell**.

    ![Instalar a extensão personalizada](./media/application-gateway-web-application-firewall-portal/application-gateway-extension.png)

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
2. Clique em **Pools do back-end**. Um pool padrão foi criado automaticamente com o gateway de aplicativo. Clique em **appGateayBackendPool**.
3. Clique em **Adicionar destino** para adicionar cada máquina virtual que você criou para o pool de back-end.

    ![Adicionar servidores de back-end](./media/application-gateway-web-application-firewall-portal/application-gateway-backend.png)

4. Clique em **Salvar**.

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Criar uma conta de armazenamento e configurar diagnósticos

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Neste tutorial, o gateway de aplicativo usa uma conta de armazenamento para armazenar dados para fins de detecção e prevenção. Você também pode usar o Log Analytics ou Hub de eventos para registrar os dados.

1. Clique no botão **Novo** encontrado no canto superior esquerdo do portal do Azure.
2. Selecione **Armazenamento** e, em seguida, selecione **Conta de armazenamento - blob, arquivo, tabela, fila**.
3. Insira o nome da conta de armazenamento, selecione **Usar existente** para o grupo de recursos e, em seguida, selecione **myResourceGroupAG**. Neste exemplo, o nome da conta de armazenamento é *myagstore1*. Aceite os valores padrão para as outras configurações e clique em **Criar**.

## <a name="configure-diagnostics"></a>Configurar o diagnóstico

Configure o diagnóstico para registrar dados nos logs de ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog, e ApplicationGatewayFirewallLog.

1. No menu à esquerda, clique em **Todos os recursos** e, em seguida, selecione *myAppGateway*.
2. Em Monitoramento, clique em **Logs de diagnóstico**.
3. Clique em **Adicionar configurações de diagnóstico**.
4. Digite *myDiagnosticsSettings* como o nome para as configurações de diagnóstico.
5. Selecione **Arquivo para uma conta de armazenamento** e, em seguida, clique em **Configurar** para selecionar a conta de armazenamento *myagstore1* que você criou anteriormente.
6. Selecione os logs do gateway do aplicativo para coletar e manter.
7. Clique em **Salvar**.

    ![Configurar o diagnóstico](./media/application-gateway-web-application-firewall-portal/application-gateway-diagnostics.png)

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicativo

1. Localize o endereço IP público para o gateway do aplicativo na tela de Visão geral. Clique em **Todos os recursos** e clique em **myAGPublicIPAddress**.

    ![Registrar o endereço IP público do gateway de aplicativo](./media/application-gateway-web-application-firewall-portal/application-gateway-record-ag-address.png)

2. Copie o endereço IP público e cole-o na barra de endereços do seu navegador.

    ![Testar gateway de aplicativo](./media/application-gateway-web-application-firewall-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a:

> [!div class="checklist"]
> * Criar um gateway de aplicativo com o WAF habilitado
> * Criar as máquinas virtuais usadas como servidores de back-end
> * Criar uma conta de armazenamento e configurar diagnósticos

Para saber mais sobre os gateways de aplicativo e seus recursos associados, siga até os artigos de instrução.