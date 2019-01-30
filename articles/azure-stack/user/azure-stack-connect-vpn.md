---
title: Conectar o Azure Stack ao Azure usando VPN
description: Como conectar redes virtuais no Azure Stack para redes virtuais no Azure usando VPN.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/24/2018
ms.author: sethm
ms.reviewer: scottnap
ms.lastreviewed: 10/24/2018
ms.openlocfilehash: 882724c5f45cc2c5b3debb8fa80538239a98b749
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55240685"
---
# <a name="connect-azure-stack-to-azure-using-vpn"></a>Conectar o Azure Stack ao Azure usando VPN

*Aplica-se a: Sistemas integrados do Azure Stack*

Este artigo mostra como criar uma VPN site a site para se conectar a uma rede virtual no Azure Stack a uma rede virtual no Azure.

## <a name="before-you-begin"></a>Antes de começar

Para concluir a configuração de conexão, certifique-se de que ter os seguintes itens antes de começar:

* Uma pilha do Azure integrado de implantação de sistemas (com vários nós) que está conectada diretamente à internet. O intervalo de endereços IP público externo deve ser acessado diretamente pela internet pública.
* Uma assinatura válida do Azure. Se você não tiver uma assinatura do Azure, você pode criar uma [conta gratuita do Azure aqui](https://azure.microsoft.com/free/?b=17.06).

### <a name="vpn-connection-diagram"></a>Diagrama de conexão de VPN

A figura a seguir mostra o que a configuração de conexão deve ter aparência quando você terminar:

![Configuração de conexão de VPN site a site](media/azure-stack-connect-vpn/image2.png)

### <a name="network-configuration-example-values"></a>Valores de exemplo de configuração de rede

A tabela de exemplos de configuração de rede mostra os valores que são usados para os exemplos neste artigo. Você pode usar esses valores, ou você pode consultá-los para compreender melhor os exemplos neste artigo:

|   |Azure Stack|Azure|
|---------|---------|---------|
|Nome da rede virtual     |Azs-VNet|AzureVNet |
|Espaço de endereço de rede virtual |10.1.0.0/16|10.100.0.0/16|
|Nome da sub-rede     |FrontEnd|FrontEnd|
|Intervalo de endereços da sub-rede|10.1.0.0/24 |10.100.0.0/24 |
|Gateway de sub-rede     |10.1.1.0/24|10.100.1.0/24|

## <a name="create-the-network-resources-in-azure"></a>Criar os recursos de rede no Azure

Primeiro, crie os recursos de rede do Azure. As instruções a seguir mostram como criar os recursos usando o [portal do Azure](https://portal.azure.com/).

### <a name="create-the-virtual-network-and-virtual-machine-vm-subnet"></a>Criar a rede virtual e a sub-rede VM (máquina virtual)

1. Entrar para o [portal do Azure](https://portal.azure.com/) usando sua conta do Azure.
2. No portal do usuário, selecione **+ criar um recurso**.
3. Vá para **Marketplace**e, em seguida, selecione **Networking**.
4. Selecione **Rede virtual**.
5. Use as informações da tabela de configuração de rede para identificar os valores para o Azure **nome**, **espaço de endereço**, **nome da sub-rede**, e **endereço de sub-rede intervalo**.
6. Para **grupo de recursos**, crie um novo grupo de recursos ou, se você já tiver um, selecione **usar existente**.
7. Selecione o **local** da sua rede virtual.  Se você estiver usando os valores de exemplo, selecione **Leste dos EUA** ou usar outro local.
8. Selecione **Fixar no painel**.
9. Selecione **Criar**.

### <a name="create-the-gateway-subnet"></a>Criar a Sub-rede de Gateway

1. Abra o recurso de rede virtual que você criou (**AzureVNet**) do painel.
2. Sobre o **as configurações** seção, selecione **sub-redes**.
3. Selecione **sub-rede de Gateway** para adicionar uma sub-rede de gateway à rede virtual.
4. O nome da sub-rede é definido como **Sub-rede de Gateway** por padrão.

   >[!IMPORTANT]
   >Sub-redes de gateway são especiais e devem ter esse nome específico para funcionar corretamente.

5. No **intervalo de endereços** campo, verifique se o endereço está **10.100.1.0/24**.
6. Selecione **Okey** para criar a sub-rede de gateway.

### <a name="create-the-virtual-network-gateway"></a>Criar o gateway de rede virtual

1. No portal do Azure, selecione **+ criar um recurso**.  
2. Vá para **Marketplace**e, em seguida, selecione **Networking**.
3. Na lista de recursos de rede, selecione **gateway de rede Virtual**.
4. Na **nome**, digite **Azure-GW**.
5. Para escolher uma rede virtual, selecione **rede Virtual**. Em seguida, selecione **AzureVnet** na lista.
6. Selecione **Endereço IP público**. Quando o **escolher endereço IP público** seção abrir, selecione **criar novo**.
7. Na **nome**, digite **Azure-GW-PiP**e, em seguida, selecione **Okey**.
8. Por padrão, para **tipo de VPN**, **baseado em rota** está selecionado. Manter o **baseado em rota** tipo de VPN.
9. Verifique se **Assinatura** e **Local** estão corretos. Você pode fixar o recurso no painel. Selecione **Criar**.

### <a name="create-the-local-network-gateway-resource"></a>Criar o recurso de gateway de rede local

1. No portal do Azure, selecione **+ criar um recurso**.
2. Vá para **Marketplace**e, em seguida, selecione **Networking**.
3. Na lista de recursos, selecione **gateway de rede Local**.
4. Na **nome**, digite **Azs-GW**.
5. Na **endereço IP**, digite o endereço IP público para o Azure Stack Gateway de rede Virtual que está listado anteriormente na tabela de configuração de rede.
6. Na **espaço de endereço**, da pilha do Azure, digite o **10.1.0.0/24** e **10.1.1.0/24** espaço de endereço para **AzureVNet**.
7. Verifique sua **assinatura**, **grupo de recursos**, e **local** estão corretas e, em seguida, selecione **criar**.

## <a name="create-the-connection"></a>Criar a conexão

1. No portal do usuário, selecione **+ criar um recurso**.
2. Vá para **Marketplace**e, em seguida, selecione **Networking**.
3. Na lista de recursos, selecione **Conexão**.
4. No **básicas** seção de configurações para o **tipo de Conexão**, escolha **Site a site (IPSec)**.
5. Selecione o **assinatura**, **grupo de recursos**, e **local**e, em seguida, selecione **Okey**.
6. Sobre o **configurações** seção, selecione **gateway de rede Virtual**e, em seguida, selecione **Azure-GW**.
7. Selecione **gateway de rede Local**e, em seguida, selecione **Azs-GW**.
8. Na **nome de Conexão**, digite **Azs Azure**.
9. Na **chave compartilhada (PSK)**, digite **12345**, em seguida, selecione **Okey**.

   >[!NOTE]
   >Se você usar um valor diferente para a chave compartilhada, lembre-se de que ele deve corresponder ao valor para a chave compartilhada que você cria na outra extremidade de conexão.

10. Examine os **resumo** seção e, em seguida, selecione **Okey**.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Agora crie uma máquina virtual no Azure e colocá-lo em sua sub-rede VM em sua rede virtual.

1. No portal do Azure, selecione **+ criar um recurso**.
2. Vá para **Marketplace**e, em seguida, selecione **computação**.
3. Na lista de imagens de máquina virtual, selecione a **Windows Server 2016 Datacenter Eval** imagem.
4. Sobre o **Noções básicas** seção, para **nome**, tipo **AzureVM**.
5. Digite um nome de usuário válido e uma senha. Use essa conta para entrar na máquina virtual após sua criação.
6. Fornecer um **assinatura**, **grupo de recursos**, e **local**e, em seguida, selecione **Okey**.
7. Sobre o **tamanho** seção, selecione um tamanho de máquina virtual para esta instância e, em seguida, selecione **selecione**.
8. No **configurações** seção, você pode usar as configurações padrão. Antes de selecionar **Okey**, confirme se:

   * O **AzureVnet** rede virtual está selecionada.
   * A sub-rede está definida como **10.100.0.0/24**.

   Selecione **OK**.

9. Examine as configurações na **resumo** seção e, em seguida, selecione **Okey**.

## <a name="create-the-network-resources-in-azure-stack"></a>Criar os recursos de rede no Azure Stack

Em seguida, crie os recursos de rede no Azure Stack.

### <a name="sign-in-as-a-user"></a>Entrar como um usuário

Um administrador de serviço pode entrar como um usuário para testar os planos, ofertas e assinaturas que seus usuários podem usar. Se você ainda não tiver uma, [criar uma conta de usuário](../azure-stack-add-new-user-aad.md) antes de entrar no.

### <a name="create-the-virtual-network-and-a-vm-subnet"></a>Criar a rede virtual e uma sub-rede VM

1. Use uma conta de usuário para entrar no portal do usuário.
2. No portal do usuário, selecione **+ criar um recurso**.

    ![Criar nova rede virtual](media/azure-stack-connect-vpn/image3.png)

3. Vá para **Marketplace**e, em seguida, selecione **Networking**.
4. Selecione **Rede virtual**.
5. Para **nome**, **espaço de endereço**, **nome da sub-rede**, e **intervalo de endereços de sub-rede**, use os valores da tabela de configuração de rede.
6. Na **assinatura**, a assinatura que você criou anteriormente é exibida.
7. Para **grupo de recursos**, você pode criar um grupo de recursos ou se você já tiver um, selecione **usar existente**.
8. Verifique o local padrão.
9. Selecione **Fixar no painel**.
10. Selecione **Criar**.

### <a name="create-the-gateway-subnet"></a>Criar a sub-rede de gateway

1. No painel, abra o recurso de rede virtual de Azs-rede virtual que você criou.
2. Sobre o **as configurações** seção, selecione **sub-redes**.
3. Para adicionar uma sub-rede de gateway à rede virtual, selecione **sub-rede de Gateway**.

    ![Adicionar sub-rede de gateway](media/azure-stack-connect-vpn/image4.png)

4. Por padrão, o nome da sub-rede é definido como **GatewaySubnet**. Para sub-redes de gateway funcione corretamente, eles devem usar o **GatewaySubnet** nome.
5. Na **intervalo de endereços**, verifique se o endereço está **10.1.1.0/24**.
6. Selecione **Okey** para criar a sub-rede de gateway.

### <a name="create-the-virtual-network-gateway"></a>Criar o gateway de rede virtual

1. No portal do Azure Stack, selecione **+ criar um recurso**.
2. Vá para **Marketplace**e, em seguida, selecione **Networking**.
3. Na lista de recursos de rede, selecione **gateway de rede Virtual**.
4. Na **nome**, digite **Azs-GW**.
5. Selecione o **rede Virtual** item para escolher uma rede virtual. Selecione **Azs-VNet** na lista.
6. Selecione o **endereço IP público** item de menu. Quando o **escolher endereço IP público** seção abrir, selecione **criar novo**.
7. Na **nome**, digite **Azs-GW-PiP**e, em seguida, selecione **Okey**.
8. Por padrão, **baseado em rota** está selecionado para **tipo de VPN**. Manter o **baseado em rota** tipo de VPN.
9. Verifique se **Assinatura** e **Local** estão corretos. Você pode fixar o recurso no painel. Selecione **Criar**.

### <a name="create-the-local-network-gateway"></a>Criar o gateway de rede local

O conceito de um *gateway de rede local* no Azure Stack é um pouco diferente em uma implantação do Azure.

Em uma implantação do Azure, um gateway de rede local representa um dispositivo físico local (no local do usuário) que você se conectar a um gateway de rede virtual no Azure. No entanto, no Azure Stack, ambas as extremidades da conexão são gateways de rede virtual.

Uma descrição mais genérica é que o recurso de gateway de rede local sempre indica o gateway remoto na outra extremidade de conexão.

### <a name="create-the-local-network-gateway-resource"></a>Criar o recurso de gateway de rede local

1. Entrar no portal do Azure Stack.
2. No portal do usuário, selecione **+ criar um recurso**.
3. Vá para **Marketplace**e, em seguida, selecione **Networking**.
4. Na lista de recursos, selecione **gateway de rede local**.
5. Na **nome**, digite **Azure-GW**.
6. Na **endereço IP**, digite o endereço IP público para o gateway de rede virtual no Azure **Azure-GW-PiP**. Esse endereço aparece anteriormente na tabela de configuração de rede.
7. Na **espaço de endereço**, para o espaço de endereço da rede virtual do Azure que você criou, digite **10.100.0.0/24** e **10.100.1.0/24**.
8. Verifique sua **assinatura**, **grupo de recursos**, e **local** valores estão corretos e, em seguida, selecione **criar**.

### <a name="create-the-connection"></a>Criar a conexão

1. No portal do usuário, selecione **+ criar um recurso**.
2. Vá para **Marketplace**e, em seguida, selecione **Networking**.
3. Na lista de recursos, selecione **Conexão**.
4. Sobre o **Noções básicas** seção de configurações, para o **tipo de Conexão**, selecione **Site a site (IPSec)**.
5. Selecione o **assinatura**, **grupo de recursos**, e **local**e, em seguida, selecione **Okey**.
6. Sobre o **as configurações** seção, selecione **gateway de rede Virtual**e, em seguida, selecione **Azs-GW**.
7. Selecione **gateway de rede Local**e, em seguida, selecione **Azure-GW**.
8. Na **nome de Conexão**, digite **Azs-Azure**.
9. Na **chave compartilhada (PSK)**, digite **12345**e, em seguida, selecione **Okey**.
10. Sobre o **resumo** seção, selecione **Okey**.

### <a name="create-a-virtual-machine-vm"></a>Criar uma máquina virtual (VM)

Para verificar a conexão VPN, crie duas VMs: um no Azure e outro no Azure Stack. Depois de criar essas VMs, você pode usá-los para enviar e receber dados por meio do túnel VPN.

1. No portal do Azure, selecione **+ criar um recurso**.
2. Vá para **Marketplace**e, em seguida, selecione **computação**.
3. Na lista de imagens de máquina virtual, selecione a **Windows Server 2016 Datacenter Eval** imagem.
4. Sobre o **Noções básicas** seção, na **nome**, tipo **Azs-VM**.
5. Digite um nome de usuário válido e uma senha. Use essa conta para entrar na VM após sua criação.
6. Fornecer um **assinatura**, **grupo de recursos**, e **local**e, em seguida, selecione **Okey**.
7. Sobre o **tamanho** seção, para essa instância, selecione um tamanho de máquina virtual e, em seguida, selecione **selecione**.
8. Sobre o **configurações** seção, aceite os padrões. Certifique-se de que o **Azs-VNet** rede virtual está selecionada. Verificar se a sub-rede é definida como **10.1.0.0/24**. Depois, selecione **OK**.
9. Sobre o **resumo** seção, examine as configurações e, em seguida, selecione * Okey * *.

## <a name="test-the-connection"></a>Testar a conexão

Depois de estabelecer a conexão site a site, você deve verificar que você pode obter dados que fluem em ambas as direções. A maneira mais fácil de testar a conexão está fazendo um teste de ping:

* Entrar para a máquina virtual criada no Azure Stack e ping a máquina virtual no Azure.
* Entrar para a máquina virtual que você criou no Azure e executar o ping na máquina virtual no Azure Stack.

>[!NOTE]
>Para certificar-se de que você está enviando o tráfego por meio de conexão site a site, execute ping no endereço de IP (DIP) direto da máquina virtual na sub-rede remota, não o VIP.

### <a name="sign-in-to-the-user-vm-in-azure-stack"></a>Entrar para o usuário VM no Azure Stack

1. Entrar no portal do Azure Stack.
2. Na barra de navegação à esquerda, selecione **máquinas virtuais**.
3. Na lista de VMs, localize **Azs-VM** que você criou anteriormente e, em seguida, selecioná-lo.
4. Na seção para a máquina virtual, selecione **Connect**e, em seguida, abra o arquivo de Azs-VM.rdp.

     ![Botão conectar](media/azure-stack-connect-vpn/image17.png)

5. Entre com a conta que você configurou quando criou a máquina virtual.
6. Abra um prompt elevado do Windows PowerShell.
7. Digite **ipconfig/all**.
8. Na saída, localize o **endereço IPv4**, em seguida, salve o endereço para uso posterior. Esse é o endereço que você executar o ping do Azure. No ambiente de exemplo, é o endereço **10.1.0.4**, mas em seu ambiente podem ser diferente. Ela deverá cair dentro de **10.1.0.0/24** sub-rede que você criou anteriormente.
9. Para criar uma regra de firewall que permite que a máquina virtual para responder a pings, execute o seguinte comando do PowerShell:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-azure"></a>Entre locatário do VM no Azure

1. Entre no Portal do Azure.
2. Na barra de navegação à esquerda, selecione **máquinas virtuais**.
3. Na lista de máquinas virtuais, localize **Azure VM** que você criou anteriormente e, em seguida, selecioná-lo.
4. Na seção para a máquina virtual, selecione **Connect**.
5. Entre com a conta que você configurou quando criou a máquina virtual.
6. Abrir com privilégios elevados **Windows PowerShell** janela.
7. Digite **ipconfig/all**.
8. Você deve ver um endereço IPv4 que esteja dentro **10.100.0.0/24**. No ambiente de exemplo, é o endereço **10.100.0.4**, mas seu endereço pode ser diferente.
9. Para criar uma regra de firewall que permite que a máquina virtual para responder a pings, execute o seguinte comando do PowerShell:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

10. Da máquina virtual no Azure, execute ping a máquina virtual no Azure Stack, por meio do túnel. Para fazer isso, você pode executar ping o DIP que registrou na Azs-VM. No ambiente de exemplo, isso é **10.1.0.4**, mas não se esqueça de executar ping no endereço que você anotou no laboratório. Você verá um resultado que se parece com a captura de tela a seguir:

    ![Ping com êxito](media/azure-stack-connect-vpn/image19b.png)

11. Uma resposta da máquina virtual remota indica um teste bem-sucedido. Você pode fechar a janela da máquina virtual.

Você também deve fazer a transferência de dados mais rigorosa testes; Por exemplo, copiando diferentes dimensionada arquivos em ambas as direções.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Exibindo estatísticas de transferência de dados com a conexão de gateway

Se você quiser saber a quantidade de dados passa por meio de sua conexão site a site, essas informações estão disponíveis na **Conexão** seção. Esse teste também é outra maneira de verificar que o ping que você acabou de enviar realmente atravessou a conexão VPN.

1. Enquanto estiver conectado à máquina virtual usuário no Azure Stack, use sua conta de usuário para entrar no portal do usuário.
2. Vá para **todos os recursos**e, em seguida, selecione o **Azs-Azure** conexão. **Conexões** é exibida.
3. Sobre o **Conexão** seção, as estatísticas de **dados no** e **saída de dados** aparecem. Na captura de tela a seguir, os números grandes são atribuídos à transferência de arquivo adicionais. Você deve ver alguns valores de diferentes de zero.

    ![Entrada e saída de dados](media/azure-stack-connect-vpn/Connection.png)

## <a name="next-steps"></a>Próximas etapas

[Implantar aplicativos no Azure e o Azure Stack](azure-stack-solution-pipeline.md)
