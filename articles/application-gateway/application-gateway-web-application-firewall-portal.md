---
title: Tutorial – criar um gateway de aplicativo com o firewall do aplicativo web - portal do Azure | Microsoft Docs
description: Saiba como criar um gateway de aplicativo com o firewall do aplicativo Web usando o portal do Azure.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/25/2019
ms.author: victorh
ms.openlocfilehash: 1284ddec4cd9cea3ea53c20d437550405dd614d9
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905861"
---
# <a name="create-an-application-gateway-with-a-web-application-firewall-using-the-azure-portal"></a>Criar um gateway de aplicativo com um firewall do aplicativo Web usando o portal do Azure

> [!div class="op_single_selector"]
>
> - [Portal do Azure](application-gateway-web-application-firewall-portal.md)
> - [PowerShell](tutorial-restrict-web-traffic-powershell.md)
> - [CLI do Azure](tutorial-restrict-web-traffic-cli.md)
>
> 

Este tutorial mostra como usar o portal do Azure para criar uma [gateway de aplicativo](application-gateway-introduction.md) com um [firewall do aplicativo web](application-gateway-web-application-firewall-overview.md) (WAF). O WAF usa as regras de [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) para proteger o seu aplicativo. Essas regras incluem proteção contra ataques, como injeção de SQL, ataques de script entre sites e sequestros de sessão. Após criar o gateway de aplicativo, você vai testá-lo para verificar se está funcionando corretamente. Com Gateway de Aplicativo do Azure, você direciona o tráfego de aplicativo Web para recursos específicos, atribuindo ouvintes a portas, criando regras e adicionando recursos a um pool de back-end. Para simplificar, este artigo usa uma configuração simples com um IP de front-end público, um ouvinte básico para hospedar um único site nesse gateway de aplicativo, duas máquinas virtuais usadas para o pool de back-end e uma regra de roteamento de solicitações básica.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Criar um gateway de aplicativo com o WAF habilitado
> * Criar as máquinas virtuais usadas como servidores de back-end
> * Criar uma conta de armazenamento e configurar diagnósticos

![Exemplo de Firewall do aplicativo Web](./media/application-gateway-web-application-firewall-portal/scenario-waf.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no portal do Azure em [https://portal.azure.com](https://portal.azure.com)

## <a name="create-an-application-gateway"></a>Criar um Gateway de Aplicativo

Para que o Azure se comunique entre os recursos que você cria, ele precisa de uma rede virtual. Você pode criar uma nova rede virtual ou usar uma existente. Neste exemplo, criaremos uma nova rede virtual. Você pode criar uma rede virtual ao mesmo tempo que cria o gateway de aplicativo. As instâncias do Gateway de Aplicativo são criadas em sub-redes separadas. Crie duas sub-redes neste exemplo: uma para o gateway de aplicativo e outra para os servidores de back-end.

Selecione **Criar um recurso** no menu esquerdo do portal do Azure. A janela **Novo** é exibida.

Selecione **Rede** e depois **Gateway de Aplicativo** na lista **em destaque**.

### <a name="basics-page"></a>Página de Noções Básicas

1. Na página **Noções Básicas**, insira esses valores para as seguintes configurações de gateway de aplicativo:
   - **Nome**: Insira *myAppGateway* para o nome do gateway de aplicativo.
   - **Grupo de recursos**: Selecione **myResourceGroupAG** para o grupo de recursos. Se ele não existir, selecione **Criar novo** para criá-lo.
   - Selecione *WAF* para a camada do gateway de aplicativo.![ Criar um novo gateway de aplicativo](./media/application-gateway-web-application-firewall-portal/application-gateway-create.png)

Aceite os valores padrão para as outras configurações e, em seguida, clique em **OK**.

### <a name="settings-page"></a>Página Configurações

1. Na página **Configurações**, em **Configuração de sub-rede**, selecione **Escolher uma rede virtual**. <br>
2. Na página **Escolher rede virtual**, selecione **Criar novo** e insira valores para as seguintes configurações de rede virtual:
   - **Nome**: Insira *myVNet* para o nome da rede virtual.
   - **Espaço de endereço**: Insira *10.0.0.0/16* para o espaço de endereço da rede virtual.
   - **Nome da sub-rede**: Insira *myAGSubnet* para o nome da sub-rede.<br>A sub-rede de gateway de aplicativo pode conter apenas gateways de aplicativo. Nenhum outro recurso é permitido.
   - **Intervalo de endereços da sub-rede**: Insira *10.0.0.0/24* para o intervalo de endereços de sub-rede.![ Criar rede virtual](./media/application-gateway-web-application-firewall-portal/application-gateway-vnet.png)
3. Clique em **OK** para criar a rede virtual e a sub-rede.
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

3. Crie uma segunda máquina virtual e instale o IIS usando as etapas que você concluiu anteriormente. Use *myVM2* para o nome da máquina virtual e o **VMName** configuração do **AzVMExtension conjunto** cmdlet.

### <a name="add-backend-servers-to-backend-pool"></a>Adicionar servidores back-end ao pool de back-end

1. Selecione **Todos os recursos** e, em seguida, **myAppGateway**.

2. Selecione **Pools de back-end** no menu esquerdo. O Azure criou automaticamente um pool padrão **appGatewayBackendPool** quando você criou o gateway de aplicativo. 

3. Selecione **appGatewayBackendPool**.

4. Em **Destinos**, selecione **Máquina virtual** na lista suspensa.

5. Em **MÁQUINA VIRTUAL** e **INTERFACES DE REDE**, selecione as máquinas virtuais **myVM** e **myVM2** e suas interfaces de rede associadas nas listas suspensas.

   ![Adicionar servidores de back-end](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Clique em **Salvar**.

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Criar uma conta de armazenamento e configurar diagnósticos

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Neste tutorial, o gateway de aplicativo usa uma conta de armazenamento para armazenar dados para fins de detecção e prevenção. Use também os logs do Azure Monitor ou o Hub de Eventos para registrar dados.

1. Clique em **Novo** no canto superior esquerdo do portal do Azure.
2. Selecione **Armazenamento** e, em seguida, selecione **Conta de armazenamento - blob, arquivo, tabela, fila**.
3. Insira o nome da conta de armazenamento, selecione **Usar existente** para o grupo de recursos e, em seguida, selecione **myResourceGroupAG**. Neste exemplo, o nome da conta de armazenamento é *myagstore1*. Aceite os valores padrão para as outras configurações e clique em **Criar**.

### <a name="configure-diagnostics"></a>Configurar o diagnóstico

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

Neste artigo, você aprendeu a:

> [!div class="checklist"]
> * Criar um gateway de aplicativo com o WAF habilitado
> * Criar as máquinas virtuais usadas como servidores de back-end
> * Criar uma conta de armazenamento e configurar diagnósticos

Para saber mais sobre os gateways de aplicativo e seus recursos associados, siga até os artigos de instrução.
