---
title: "Criar um gateway de aplicativo com hospedagem de vários sites – Portal do Azure | Microsoft Docs"
description: "Saiba como criar um gateway de aplicativo que hospeda vários sites usando o portal do Azure."
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: davidmu
ms.openlocfilehash: 403c6c254d8547b09e42f0b1561e5eff350a1f9b
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="create-an-application-gateway-with-multiple-site-hosting-using-the-azure-portal"></a>Criar um gateway de aplicativo com hospedagem de vários sites usando o Portal do Azure

Você pode usar o portal do Azure para configurar [a hospedagem de vários sites da Web](application-gateway-multi-site-overview.md) ao criar um [gateway de aplicativo](application-gateway-introduction.md). Neste tutorial, você criará pools de back-end usando conjuntos de dimensionamento de máquinas virtuais. Em seguida, você configurará ouvintes e regras com base em domínios que possui para garantir que o tráfego da Web chegue aos servidores apropriados nos pools. Este tutorial presume que você possui vários domínios e usa exemplos do *www.contoso.com* e do *www.fabrikam.com*.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Criar um Gateway de Aplicativo
> * Criar máquinas virtuais para servidores de back-end
> * Criar pools de back-end com os servidores de back-end
> * Criar ouvintes e regras de roteamento
> * Criar um registro CNAME em seu domínio

![Exemplo de roteamento de vários sites](./media/application-gateway-create-multisite-portal/scenario.png)

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon no portal do Azure em [http://portal.azure.com](http://portal.azure.com)

## <a name="create-an-application-gateway"></a>Criar um Gateway de Aplicativo

Uma rede virtual é necessária para a comunicação entre os recursos que você criar. Duas sub-redes são criadas neste exemplo: um para o gateway de aplicativo e a outra para os servidores de back-end. Você pode criar uma rede virtual ao mesmo tempo que cria o gateway de aplicativo.

1. Clique em **Novo** no canto superior esquerdo do portal do Azure.
2. Selecione **Rede** e depois **Gateway de Aplicativo** na lista em destaque.
3. Insira esses valores para o gateway de aplicativo:

    - *myAppGateway* - para o nome do gateway de aplicativo.
    - *myResourceGroupAG* - para o novo grupo de recursos.

    ![Criar novo gateway de aplicativo](./media/application-gateway-create-multisite-portal/application-gateway-create.png)

4. Aceite os valores padrão para as outras configurações e, em seguida, clique em **OK**.
5. Clique em **Escolher uma rede virtual**, clique em **Criar novo** e insira esses valores para a rede virtual:

    - *myVNet* – para o nome da rede virtual.
    - *10.0.0.0/16* – para o espaço de endereço da rede virtual.
    - *myAGSubnet* – para o nome da sub-rede.
    - *10.0.0.0/24* - para o espaço de endereço da sub-rede.

    ![Criar rede virtual](./media/application-gateway-create-multisite-portal/application-gateway-vnet.png)

6. Clique em **OK** para criar a rede virtual e a sub-rede.
7. Clique em **Escolher um endereço IP público**, clique em **Criar novo** e digite o nome do endereço IP público. Neste exemplo, o endereço IP público é denominado *myAGPublicIPAddress*. Aceite os valores padrão para as outras configurações e, em seguida, clique em **OK**.
8. Aceite os valores padrão para a Configuração do ouvinte, deixe o firewall do aplicativo Web desabilitado e, em seguida, clique em **OK**.
9. Examine as configurações na página de resumo e, em seguida, clique em **OK** para criar os recursos de rede e o gateway de aplicativo. A criação do gateway de aplicativo pode levar vários minutos, aguarde até que a implantação seja concluída com êxito antes de passar para a próxima seção.

### <a name="add-a-subnet"></a>Adicionar uma sub-rede

1. Clique em **Todos os recursos** no menu esquerdo e depois clique em **myVNet** da lista de recursos.
2. Clique em **Sub-redes** e, em seguida, clique em **Sub-rede**.

    ![Criar sub-rede](./media/application-gateway-create-multisite-portal/application-gateway-subnet.png)

3. Digite *myBackendSubnet* para o nome da sub-rede e depois clique em **OK**.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Neste exemplo, você cria duas máquinas virtuais para serem usadas como servidores de back-end para o gateway do aplicativo. Você também pode instalar o IIS nas máquinas virtuais para verificar se o tráfego está sendo roteado corretamente.

1. Clique em **Novo**.
2. Clique em **Computação** e, em seguida, selecione **Datacenter do Windows Server 2016** na lista em destaque.
3. Insira esses valores para a máquina virtual:

    - *contosoVM* – para o nome da máquina virtual.
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

    ![Instalar a extensão personalizada](./media/application-gateway-create-multisite-portal/application-gateway-extension.png)

2. Execute o comando a seguir para instalar o IIS na máquina virtual: 

    ```azurepowershell-interactive
    $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/davidmu1/samplescripts/master/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
    Set-AzureRmVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -Location eastus `
      -ExtensionName IIS `
      -VMName contosoVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -Settings $publicSettings
    ```

3. Crie a segunda máquina virtual e instale o IIS usando as etapas que você acabou de concluir. Insira os nomes de *fabrikamVM* para o nome e o valor de VMName em Set-AzureRmVMExtension.

## <a name="create-backend-pools-with-the-virtual-machines"></a>Criar pools de back-end com as máquinas virtuais

1. Clique em **Todos os recursos** e, em seguida, clique em **myAppGateway**.
2. Clique em **Pools de back-end** e, em seguida, clique em **Adicionar**.
3. Insira um nome de *contosoPool* e adicione *contosoVM* usando **Adicionar destino**.

    ![Adicionar servidores de back-end](./media/application-gateway-create-multisite-portal/application-gateway-multisite-backendpool.png)

4. Clique em **OK**.
5. Clique em **Pools de back-end** e, em seguida, clique em **Adicionar**.
6. Crie o *fabrikamPool* com o *fabrikamVM* usando as etapas que você acabou de concluir.

## <a name="create-listeners-and-routing-rules"></a>Criar ouvintes e regras de roteamento

1. Clique em **Ouvintes** e, em seguida, clique em **Vários sites**.
2. Insira esses valores para o ouvinte:
    
    - *contosoListener* – para o nome do ouvinte.
    - *www.contoso.com* – substitua este exemplo de nome de host pelo nome de domínio.

3. Clique em **OK**.
4. Crie um segundo ouvinte usando o nome de *fabrikamListener* e use seu segundo nome de domínio. Neste exemplo, *www.fabrikam.com* é usado.

As regras são processadas na ordem em que são listadas e o tráfego é direcionado usando da primeira regra correspondente, independentemente de especificidade. Por exemplo, se você tiver uma regra usando um ouvinte básico e outra usando um ouvinte multissite, ambas na mesma porta, a regra com o ouvinte multissite deverá ser listada antes daquela com o ouvinte básico, para que a função multissite funcione conforme esperado. 

Neste exemplo, você criará duas novas regras e excluirá a regra padrão que foi criada quando você criou o gateway de aplicativo. 

1. Clique em **Regras** e, em seguida, clique em **Básicas**.
2. Digite *contosoRule* para o nome.
3. Selecione *contosoListener* para o ouvinte.
4. Selecione *contosoPool* para o pool de back-end.

    ![Criar uma regra baseada em caminho](./media/application-gateway-create-multisite-portal/application-gateway-multisite-rule.png)

5. Clique em **OK**.
6. Crie uma segunda regra usando os nomes de *fabrikamRule*, *fabrikamListener* e *fabrikamPool*.
7. Exclua a regra padrão denominada *rule1* clicando nela e, em seguida, clicando em **Excluir**.

## <a name="create-a-cname-record-in-your-domain"></a>Criar um registro CNAME em seu domínio

Depois de criar o gateway de aplicativo com seu endereço IP público, você pode obter o endereço DNS e usá-lo para criar um registro CNAME em seu domínio. O uso de registros A não é recomendável, pois o VIP pode mudar quando o gateway de aplicativo for reinicializado.

1. Clique em **Todos os recursos** e, em seguida, clique em **myAGPublicIPAddress**.

    ![Registrar o endereço DNS público do gateway de aplicativo](./media/application-gateway-create-multisite-portal/application-gateway-multisite-dns.png)

2. Copie o endereço DNS e use-o como o valor para um novo registro CNAME em seu domínio.

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicativo

1. Digite seu nome de domínio na barra de endereços do navegador. Por exemplo, http://www.contoso.com.

    ![Testar o site do contoso no gateway do aplicativo](./media/application-gateway-create-multisite-portal/application-gateway-iistest.png)

2. Altere o endereço para seu outro domínio e você verá algo parecido com o exemplo a seguir:

    ![Testar site do fabrikam no gateway de aplicativo](./media/application-gateway-create-multisite-portal/application-gateway-iistest2.png)

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a:

> [!div class="checklist"]
> * Criar um Gateway de Aplicativo
> * Criar máquinas virtuais para servidores de back-end
> * Criar pools de back-end com os servidores de back-end
> * Criar ouvintes e regras de roteamento
> * Criar um registro CNAME em seu domínio

> [!div class="nextstepaction"]
> [Saiba mais sobre o que você pode fazer com o gateway de aplicativo](application-gateway-introduction.md)