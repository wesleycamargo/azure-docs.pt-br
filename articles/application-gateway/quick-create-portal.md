---
title: Início Rápido – Direcionar o tráfego da Web com o Gateway de Aplicativo do Azure – portal do Azure | Microsoft Docs
description: Saiba como usar o portal do Azure para criar um Gateway de Aplicativo do Azure que direciona o tráfego da Web para máquinas virtuais em um pool de back-end.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 1/8/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: b474d3579a7c20c190a427f503d97ec7471a1b12
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58091147"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-portal"></a>Início Rápido: Direcionar o tráfego da Web com o Gateway de Aplicativo do Azure – portal do Azure

Este início rápido mostra como usar o portal do Azure para criar um gateway de aplicativo.  Após criar o gateway de aplicativo, você irá testá-lo para verificar se está funcionando corretamente. Com Gateway de Aplicativo do Azure, você direciona o tráfego de aplicativo Web para recursos específicos, atribuindo ouvintes a portas, criando regras e adicionando recursos a um pool de back-end. Para simplificar, este artigo usa uma configuração simples com um IP de front-end público, um ouvinte básico para hospedar um único site nesse gateway de aplicativo, duas máquinas virtuais usadas para o pool de back-end e uma regra de roteamento de solicitações básica.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com) com sua conta do Azure.

## <a name="create-an-application-gateway"></a>Criar um Gateway de Aplicativo

Para que o Azure se comunique entre os recursos que você cria, ele precisa de uma rede virtual. Você pode criar uma nova rede virtual ou usar uma existente. Neste exemplo, criaremos uma nova rede virtual. Você pode criar uma rede virtual ao mesmo tempo que cria o gateway de aplicativo. As instâncias do Gateway de Aplicativo são criadas em sub-redes separadas. Crie duas sub-redes neste exemplo: uma para o gateway de aplicativo e outra para os servidores de back-end.

1. Selecione **Criar um recurso** no menu esquerdo do portal do Azure. A janela **Novo** é exibida.

2. Selecione **Rede** e depois **Gateway de Aplicativo** na lista **em destaque**.

### <a name="basics-page"></a>Página de Noções Básicas

1. Na página **Noções Básicas**, insira esses valores para as seguintes configurações de gateway de aplicativo:

   - **Nome**: Insira *myAppGateway* para o nome do gateway de aplicativo.
   - **Grupo de recursos**: Selecione **myResourceGroupAG** para o grupo de recursos. Se ele não existir, selecione **Criar novo** para criá-lo.

     ![Criar novo gateway de aplicativo](./media/application-gateway-create-gateway-portal/application-gateway-create.png)

2. Aceite os valores padrão para as outras configurações e selecione **OK**.

### <a name="settings-page"></a>Página Configurações

1. Na página **Configurações**, em **Configuração de sub-rede**, selecione **Escolher uma rede virtual**. <br>

2. Na página **Escolher rede virtual**, selecione **Criar novo** e insira valores para as seguintes configurações de rede virtual:

   - **Nome**: Insira *myVNet* para o nome da rede virtual.

   - **Espaço de endereço**: Insira *10.0.0.0/16* para o espaço de endereço da rede virtual.

   - **Nome da sub-rede**: Insira *myAGSubnet* para o nome da sub-rede.<br>A sub-rede de gateway de aplicativo pode conter apenas gateways de aplicativo. Nenhum outro recurso é permitido.

   - **Intervalo de endereços da sub-rede**: Insira *10.0.0.0/24* para o intervalo de endereços de sub-rede.

     ![Criar rede virtual](./media/application-gateway-create-gateway-portal/application-gateway-vnet.png)

3. Selecione **OK** para voltar à página **Configurações**.

4. Escolha a **Configuração de IP de front-end**. Em **Configuração de IP de front-end**, verifique se **Tipo de endereço IP** está definido como **Público**. Em **Endereço IP público**, verifique se **Criar novo** está selecionado. <br>É possível configurar o IP de front-end como Público ou Privado, de acordo com o caso de uso. Neste exemplo, escolheremos um IP de front-end Público. 

5. Insira *myAGPublicIPAddress* para o nome do endereço IP público. 

6. Aceite os valores padrão para as outras configurações e selecione **OK**.<br>Nós escolheremos valores padrão neste artigo para simplificar, mas você poderá configurar valores personalizados para as outras configurações, dependendo do seu caso de uso 

### <a name="summary-page"></a>Página Resumo

Examine as configurações na página **Resumo** e selecione **OK** para criar a rede virtual, o endereço IP público e o gateway de aplicativo. Pode levar vários minutos para que o Azure crie o gateway de aplicativo. Aguarde até que a implantação seja concluída com êxito antes de passar para a próxima seção.

## <a name="add-backend-pool"></a>Adicionar pool de back-end

O pool de back-end é usado para rotear solicitações aos servidores back-end que atenderão à solicitação. Os pools de back-end podem ser formados por NICs, conjuntos de dimensionamento de máquinas virtuais, IPs públicos, IPs internos, FQDN (nomes de domínio totalmente qualificados) e back-ends multilocatário como Serviço de Aplicativo do Azure. É necessário adicionar os destinos de back-end a um pool de back-end.

Neste exemplo, usaremos máquinas virtuais como o back-end de destino. Nós podemos usar máquinas virtuais existentes ou criar novas. Neste exemplo, criaremos duas máquinas virtuais que o Azure usará como servidores back-end para o gateway de aplicativo. Para fazer isso, nós iremos:

1. Crie uma nova sub-rede, *myBackendSubnet*, na qual as novas VMs serão criadas. 
2. Criar 2 novas VMS, *myVM* e *myVM2*, para serem usadas como servidores back-end.
3. Instale IIS nas máquinas virtuais para verificar se o gateway de aplicativo foi criado com êxito.
4. Adicione os servidores back-end ao pool de back-end.

### <a name="add-a-subnet"></a>Adicionar uma sub-rede

Adicione uma sub-rede à rede virtual que você criou seguindo estas etapas:

1. Selecione **Todos os recursos** no menu esquerdo do portal do Azure, insira *myVNet* na caixa de pesquisa e selecione **myVNet** nos resultados da pesquisa.

2. Selecione **Sub-redes** no menu esquerdo e selecione **+ Sub-rede**. 

   ![Criar sub-rede](./media/application-gateway-create-gateway-portal/application-gateway-subnet.png)

3. Na página **Adicionar sub-rede**, insira *myBackendSubnet* para o **Nome** da sub-rede e selecione **OK**.

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. No portal do Azure, selecione **Criar um recurso**. A janela **Novo** é exibida.
2. Selecione **Computação** e, em seguida, selecione **Datacenter do Windows Server 2016** na lista **em destaque**. A página **Criar uma máquina virtual** é exibida.<br>O Gateway de Aplicativo pode rotear o tráfego para qualquer tipo de máquina virtual usada no pool de back-end. Neste exemplo, você usa um Windows Server 2016 Datacenter.
3. Insira esses valores na guia **Noções básicas** para as seguintes configurações de máquina virtual:

    - **Grupo de recursos**: Selecione **myResourceGroupAG** para o nome do grupo de recursos.
    - **Nome da máquina virtual**: Insira *myVM* para o nome da máquina virtual.
    - **Nome de usuário**: Insira *azureuser* para o nome de usuário do administrador.
    - **Senha**: Insira *Azure123456!* para a senha de administrador.
4. Aceite os outros padrões e selecione **Próximo: Discos**.  
5. Aceite os padrões na guia **Discos** e selecione **Próximo: Rede**.
6. Na guia **Rede**, verifique se **myVNet** está selecionado para a **Rede virtual** e se a **Sub-rede** está definida como **myBackendSubnet**. Aceite os outros padrões e selecione **Próximo: Gerenciamento**.<br>O Gateway de Aplicativo pode comunicar-se com instâncias fora da rede virtual em que está, mas é necessário garantir que há conectividade IP. 
7. Na guia **Gerenciamento**, defina **Diagnóstico de inicialização** como **Desligado**. Aceite os outros padrões e selecione **Revisar + criar**.
8. Na guia **Revisar + criar**, examine as configurações, corrija os erros de validação e selecione **Criar**.
9. Aguarde a criação da máquina virtual concluir antes de continuar.

### <a name="install-iis-for-testing"></a>Instalar IIS para teste

Neste exemplo, estamos instalando IIS nas máquinas virtuais apenas para verificar se o Azure criou o gateway de aplicativo com êxito. 

1. Abra o [PowerShell do Azure](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell). Para fazer isso, selecione **Cloud Shell** na barra de navegação superior do portal do Azure e selecione **PowerShell** na lista suspensa. 

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

3. Crie uma segunda máquina virtual e instale o IIS usando as etapas que você concluiu anteriormente. Use *myVM2* para o nome da máquina virtual e a configuração **VMName** do cmdlet **Set-AzureRmVMExtension**.

### <a name="add-backend-servers-to-backend-pool"></a>Adicionar servidores back-end ao pool de back-end

1. Selecione **Todos os recursos** e, em seguida, **myAppGateway**.

2. Selecione **Pools de back-end** no menu esquerdo. O Azure criou automaticamente um pool padrão **appGatewayBackendPool** quando você criou o gateway de aplicativo. 

3. Selecione **appGatewayBackendPool**.

4. Em **Destinos**, selecione **Máquina virtual** na lista suspensa.

5. Em **MÁQUINA VIRTUAL** e **INTERFACES DE REDE**, selecione as máquinas virtuais **myVM** e **myVM2** e suas interfaces de rede associadas nas listas suspensas.

    ![Adicionar servidores de back-end](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Clique em **Salvar**.

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicativo

Embora o IIS não seja exigido para criar o gateway de aplicativo, você o instalou neste início rápido para verificar se o Azure criou o gateway de aplicativo com êxito. Use o IIS para testar o gateway de aplicativo:

1. Localize o endereço IP público do gateway de aplicativo na página**Visão geral**. ![Registre o endereço IP público do gateway de aplicativo](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png)Como alternativa, você pode selecionar **Todos os recursos**, inserir *myAGPublicIPAddress* na caixa de pesquisa e, em seguida, selecione-o nos resultados da pesquisa. O Azure exibe o endereço IP público na página **Visão geral**.
2. Copie o endereço IP público e cole-o na barra de endereços do seu navegador.
3. Verifique a resposta. Uma resposta válida verifica se o gateway de aplicativo foi criado com êxito e é capaz de conectar-se com êxito ao back-end.![Teste o gateway de aplicativo](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>Limpar recursos

Quando não precisar mais dos recursos que você criou com o gateway de aplicativo, remova o grupo de recursos. Ao remover o grupo de recursos, você também remove o gateway de aplicativo e todos os recursos relacionados. 

Para remover o grupo de recursos:
1. No menu esquerdo do portal do Azure, selecione **Grupos de recursos**.
2. Na página **Grupos de recursos**, pesquise por **myResourceGroupAG** na lista e, em seguida, selecione.
3. Na página **Grupo de recursos**, selecione **Excluir grupo de recursos**.
4. Insira *myResourceGroupAG* para **DIGITAR O NOME DO GRUPO DE RECURSOS** e selecione **Excluir**

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Gerenciar o tráfego da web com um gateway de aplicativo usando a CLI do Azure](./tutorial-manage-web-traffic-cli.md)
