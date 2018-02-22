---
title: Conecte-se a pilha do Azure para o Azure usando VPN
description: Como conectar redes virtuais na pilha do Azure para redes virtuais no Azure usando VPN.
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: brenduns
ms.reviewer: scottnap
ms.openlocfilehash: 16cc1962eb72ac219adc8483f38cecf41a4296c1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="connect-azure-stack-to-azure-using-vpn"></a>Conecte-se a pilha do Azure para o Azure usando VPN

*Aplica-se a: sistemas integrados de pilha do Azure*

Este artigo mostra como criar uma VPN site a site para se conectar a uma rede virtual na pilha do Azure a uma rede virtual no Azure.

### <a name="connection-diagram"></a>Diagrama de conexão
O diagrama a seguir mostra o que a configuração de conexão deve ter aparência quando você terminar:

![Configuração de conexão VPN site a site](media/azure-stack-connect-vpn/image2.png)

### <a name="before-you-begin"></a>Antes de começar
Para concluir a configuração de conexão, verifique se que você tem os seguintes itens antes de começar:

* Uma pilha do Azure integradas a implantação de sistemas (com vários nós) que está conectada diretamente à Internet. Isso significa que o intervalo de endereço IP público externo deve ser acessado diretamente pela Internet pública.
* Uma assinatura do Azure válida.  Se você não tiver uma assinatura do Azure, você pode criar um [livre conta do Azure aqui](https://azure.microsoft.com/free/?b=17.06).

## <a name="network-example-values-table"></a>Tabela de valores de exemplo de rede
A tabela de valores de exemplo de rede mostra os valores de exemplo que são usados neste artigo. Você pode usar esses valores, ou você pode consultá-los para entender melhor os exemplos neste artigo.

Tabela de valores de exemplo de rede
|   |Azure Stack|As tabelas|
|---------|---------|---------|
|Nome de rede virtual     |Azs-VNet|AzureVNet |
|Espaço de endereço de rede virtual |10.1.0.0/16|10.100.0.0/16|
|Nome da sub-rede     |FrontEnd|FrontEnd|
|Intervalo de endereços da sub-rede|10.1.0.0/24 |10.100.0.0/24 |
|Gateway de sub-rede     |10.1.1.0/24|10.100.1.0/24|

## <a name="create-the-network-resources-in-azure"></a>Criar os recursos de rede no Azure

Primeiro você deve criar os recursos de rede do Azure. As instruções a seguir mostram como criar os recursos usando o [portal do Azure](http://portal.azure.com/).

### <a name="create-the-virtual-network-and-vm-subnet"></a>Criar a rede virtual e sub-rede de VM

1. Entrar para o [portal do Azure](http://portal.azure.com/) usando sua conta do Azure.
2. No portal do usuário, selecione **novo**.
3. Vá para **Marketplace**e, em seguida, selecione **rede**.
4. Selecione **Rede virtual**.
5. Use as informações da tabela de configuração de rede para identificar os valores para o Azure **nome**, **espaço de endereço**, **nome da sub-rede**, e **endereço de sub-rede intervalo**.
6. Para **grupo de recursos**, crie um novo grupo de recursos ou, se você já tiver um, selecione **usar existente**.
7. Selecione o **local** da sua rede virtual.  Se você estiver usando os valores de exemplo, selecione **Leste dos EUA** ou use outro local, se preferir.
8. Selecione **Fixar no painel**.
9. Selecione **Criar**.

### <a name="create-the-gateway-subnet"></a>Criar a Sub-rede de Gateway
1. Abrir o recurso de rede Virtual criada por você (**AzureVNet**) do painel.
2. Sobre o **configurações** seção, selecione **sub-redes**.
3. Selecione **sub-rede de Gateway** para adicionar uma sub-rede do gateway para a rede virtual.
4. O nome da sub-rede é definido como **Sub-rede de Gateway** por padrão.
   Sub-redes de gateway são especiais e devem ter esse nome específico para funcionar corretamente.
5. No **um intervalo de endereços** campo, verifique se o endereço é **10.100.1.0/24**.
6. Selecione **Okey** para criar a sub-rede de gateway.

### <a name="create-the-virtual-network-gateway"></a>Criar o gateway de rede virtual
1. No portal do Azure, selecione **novo**.  
2. Vá para **Marketplace**e, em seguida, selecione **rede**.
3. Na lista de recursos de rede, selecione **gateway de rede Virtual**.
4. Em **nome**, tipo **GW Azure**.
5. Para escolher uma rede virtual, selecione **rede Virtual**. Em seguida, selecione **AzureVnet** na lista.
6. Selecione **endereço IP público**. Quando o **escolher endereço IP público** seção é aberta, selecione **criar novo**.
7. Em **nome**, tipo **Azure-GW-PiP**e, em seguida, selecione **Okey**.
8. Por padrão, para **tipo VPN**, **baseadas em rota** está selecionado.
    Manter o **baseadas em rota** tipo de VPN.
9. Verifique se **Assinatura** e **Local** estão corretos. Você pode fixar o recurso para o painel. Selecione **Criar**.

### <a name="create-the-local-network-gateway-resource"></a>Criar o recurso de gateway de rede local

1. No portal do Azure, selecione **novo**. 
4. Vá para **Marketplace**e, em seguida, selecione **rede**.
5. Na lista de recursos, selecione **gateway de rede Local**.
6. Em **nome**, tipo **Azs-GW**.
7. Em **endereço IP**, digite o endereço IP público para o Azure pilha de Gateway de rede Virtual que está listado anteriormente na tabela de configuração de rede.
8. Em **espaço de endereço**, da pilha do Azure, digite o **10.1.0.0/24** e **10.1.1.0/24** espaço de endereço **AzureVNet**.
9. Verifique seu **assinatura**, **grupo de recursos**, e **local** estão corretas e, em seguida, selecione **criar**.

## <a name="create-the-connection"></a>Criar a conexão
1. No portal do usuário, selecione **novo**. 
2. Vá para **Marketplace**e, em seguida, selecione **rede**.
3. Na lista de recursos, selecione **Conexão**.
4. No **básica** seção de configurações para o **o tipo de Conexão**, escolha **Site a site (IPSec)**.
5. Selecione o **assinatura**, **grupo de recursos**, e **local**e, em seguida, selecione **Okey**.
6. Sobre o **configurações** seção, selecione **gateway de rede Virtual**e, em seguida, selecione **GW Azure**.
7. Selecione **gateway de rede Local**e, em seguida, selecione **Azs-GW**.
8. Em **nome de Conexão**, tipo **Azs Azure**.
9. Em **compartilhado PSK (chave)**, tipo **12345**. Se você escolher um valor diferente, lembre-se que ele *deve* corresponde ao valor para a chave compartilhada que você criar na outra extremidade de conexão. Selecione **OK**.
10. Examine o **resumo** seção e, em seguida, selecione **Okey**.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
Criar uma máquina virtual no Azure agora e colocá-la na sua sub-rede da VM em sua rede virtual.

1. No portal do Azure, selecione **novo**.
2. Vá para **Marketplace**e, em seguida, selecione **de computação**.
3. Na lista de imagens da máquina virtual, selecione o **Datacenter avaliação do Windows Server 2016** imagem.
4. Sobre o **Noções básicas de** seção, para **nome**, tipo **AzureVM**.
5. Digite um nome de usuário válido e uma senha. Você pode usar essa conta para entrar na máquina virtual após sua criação.
6. Forneça um **assinatura**, **grupo de recursos**, e **local**e, em seguida, selecione **Okey**.
7. Sobre o **tamanho** seção, selecione um tamanho de máquina virtual para essa instância e, em seguida, selecione **selecione**.
8. Sobre o **configurações** seção, você pode aceitar os padrões. Verifique se o **AzureVnet** rede virtual está selecionado e verificar se a sub-rede é definida como **10.100.0.0/24**. Selecione **OK**.
9. Examine as configurações no **resumo** seção e, em seguida, selecione **Okey**.

## <a name="create-the-network-resources-in-azure-stack"></a>Criar os recursos de rede na pilha do Azure
Em seguida, crie os recursos de rede na pilha do Azure.

### <a name="sign-in-as-a-user"></a>Entrar como um usuário
Um administrador de serviço pode entrar como um usuário para testar os planos, ofertas e assinaturas que seus usuários podem usar. Se você ainda não tiver um, [criar uma conta de usuário](azure-stack-add-new-user-aad.md) antes de você entrar.

### <a name="create-the-virtual-network-and-vm-subnet"></a>Criar a rede virtual e sub-rede de VM
1. Use uma conta de usuário para entrar no portal do usuário.
2. No portal do usuário, selecione **novo**.

    ![Criar nova rede virtual](media/azure-stack-create-vpn-connection-one-node-tp2/image3.png)

3. Vá para **Marketplace**e, em seguida, selecione **rede**.
4. Selecione **Rede virtual**.
5. Para **nome**, **espaço de endereço**, **nome da sub-rede**, e **intervalo de endereços de sub-rede**, use os valores da tabela de configuração de rede.
6. Em **assinatura**, a assinatura que você criou anteriormente é exibida.
7. Para **grupo de recursos**, você pode criar um grupo de recursos ou se você já tiver um, selecione **usar existente**.
8. Verifique o local padrão.
9. Selecione **Fixar no painel**.
10. Selecione **Criar**.

### <a name="create-the-gateway-subnet"></a>Criar a sub-rede de gateway
1. No painel, abra o recurso de rede virtual Azs VNet criado por você.
2. Sobre o **configurações** seção, selecione **sub-redes**.
3. Para adicionar uma sub-rede do gateway para a rede virtual, selecione **sub-rede de Gateway**.
   
    ![Adicionar sub-rede de gateway](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)

4. Por padrão, o nome da sub-rede é definido como **GatewaySubnet**.
   Sub-redes de gateway são especiais. Para funcionar corretamente, eles devem usar o *GatewaySubnet* nome.
5. Em **um intervalo de endereços**, verifique se o endereço é **10.1.1.0/24**.
6. Selecione **Okey** para criar a sub-rede de gateway.

### <a name="create-the-virtual-network-gateway"></a>Criar o gateway de rede virtual
1. No portal do Azure pilha, selecione **novo**. 
2. Vá para **Marketplace**e, em seguida, selecione **rede**.
3. Na lista de recursos de rede, selecione **gateway de rede Virtual**.
4. Em **nome**, tipo **Azs-GW**.
5. Selecione o **rede Virtual** item para escolher uma rede virtual.
   Selecione **Azs VNet** na lista.
6. Selecione o **endereço IP público** item de menu. Quando o **escolher endereço IP público** seção é aberta, selecione **criar novo**.
7. Em **nome**, tipo **Azs-GW-PiP**e, em seguida, selecione **Okey**.
8.  Por padrão, para **tipo VPN**, **baseadas em rota** está selecionado.
    Manter o **baseadas em rota** tipo de VPN.
9. Verifique se **Assinatura** e **Local** estão corretos. Você pode fixar o recurso para o painel. Selecione **Criar**.

### <a name="create-the-local-network-gateway"></a>Criar o gateway de rede local
A noção de um *gateway de rede local* na pilha do Azure é um pouco diferente em uma implantação do Azure.

Em uma implantação do Azure, um gateway de rede local representa um dispositivo físico no local (no local do usuário), que você usa para se conectar a um gateway de rede virtual no Azure. Na pilha do Azure, ambas as extremidades da conexão são gateways de rede virtual!

Uma maneira de pensar sobre isso mais genericamente é que o recurso de gateway de rede local sempre indica o gateway remoto na outra extremidade de conexão. 

### <a name="create-the-local-network-gateway-resource"></a>Criar o recurso de gateway de rede local
1. Entre portal do Azure pilha.
2. No portal do usuário, selecione **novo**.
3. Vá para **Marketplace**e, em seguida, selecione **rede**.
4. Na lista de recursos, selecione **gateway de rede local**.
5. Em **nome**, tipo **GW Azure**.
6. Em **endereço IP**, digite o endereço IP público para o gateway de rede virtual no Azure **Azure-GW-PiP**. Esse endereço aparece anteriormente na tabela de configuração de rede.
7. Em **espaço de endereço**, para o espaço de endereço do VNET do Azure que você criou, digite **10.100.0.0/24** e **10.100.1.0/24**.
8. Verifique seu **assinatura**, **grupo de recursos**, e **local** estão corretas e, em seguida, selecione **criar**.

### <a name="create-the-connection"></a>Criar a conexão
1. No portal do usuário, selecione **novo**.
2. Vá para **Marketplace**e, em seguida, selecione **rede**.
3. Na lista de recursos, selecione **Conexão**.
4. No **Noções básicas de** seção de configurações para o **o tipo de Conexão**, selecione **Site a site (IPSec)**.
5. Selecione o **assinatura**, **grupo de recursos**, e **local**e, em seguida, selecione **Okey**.
6. Sobre o **configurações** seção, selecione **gateway de rede Virtual**e, em seguida, selecione **Azs-GW**.
7. Selecione **gateway de rede Local**e, em seguida, selecione **GW Azure**.
8. Em **nome de Conexão**, tipo **Azs Azure**.
9. Em **compartilhado PSK (chave)**, tipo **12345**e, em seguida, selecione **Okey**.
10. Sobre o **resumo** seção, selecione **Okey**.

### <a name="create-a-vm"></a>Criar uma máquina virtual
Para validar os dados que passa a conexão VPN, você precisa criar as máquinas virtuais em cada extremidade para enviar e receber dados por meio do túnel VPN. 

1. No portal do Azure, selecione **novo**.
2. Vá para **Marketplace**e, em seguida, selecione **de computação**.
3. Na lista de imagens da máquina virtual, selecione o **Datacenter avaliação do Windows Server 2016** imagem.
4. Sobre o **Noções básicas** seção, **nome**, tipo **Azs VM**.
5. Digite um nome de usuário válido e uma senha. Você pode usar essa conta para entrar para a máquina virtual após sua criação.
6. Forneça um **assinatura**, **grupo de recursos**, e **local**e, em seguida, selecione **Okey**.
7. Sobre o **tamanho** seção, nessa instância, selecione um tamanho de máquina virtual e, em seguida, selecione **selecione**.
8. Sobre o **configurações** seção, aceite os padrões. Verifique se o **Azs VNet** rede virtual está selecionado. Verificar se a sub-rede é definida como **10.1.0.0/24**. Depois, selecione **OK**.
9. Sobre o **resumo** seção, examine as configurações e, em seguida, selecione **Okey**.


## <a name="test-the-connection"></a>Testar a conexão
Agora que a conexão site a site é estabelecida, você deve validar que você pode obter o tráfego que flui através dele. Para validar, entrar em uma das máquinas virtuais que você criou na pilha do Azure. Em seguida, execute ping a máquina virtual que você criou no Azure. 

Para certificar-se de que você envie tráfego por meio de conexão site a site, execute ping no endereço IP direto (DIP) da máquina virtual na sub-rede remota, não o VIP. Para fazer isso, localize o endereço DIP na outra extremidade de conexão. Salve o endereço para uso posterior.

### <a name="sign-in-to-the-user-vm-in-azure-stack"></a>Entrar para o usuário VM na pilha do Azure
1. Entre portal do Azure pilha.
2. Na barra de navegação à esquerda, selecione **máquinas virtuais**.
3. Na lista de máquinas virtuais, localizar **Azs VM** que você criou anteriormente e selecioná-lo.
4. Na seção para a máquina virtual, clique em **conectar**e, em seguida, abra o arquivo de Azs VM.rdp.
   
     ![Botão conectar](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)
5. Entre com a conta que você configurou quando criou a máquina virtual.
6. Abra um elevado **do Windows PowerShell** janela.
7. Digite **ipconfig/all**.
8. Na saída, localize o **endereço IPv4**e, em seguida, salve o endereço para uso posterior. Este é o endereço que você irá executar o ping do Azure. O ambiente de exemplo, o endereço é **10.1.0.4**, mas em seu ambiente pode ser diferente. Ele deve estar dentro do **10.1.0.0/24** sub-rede que você criou anteriormente.
9. Para criar uma regra de firewall que permite que a máquina virtual responder a pings, execute o seguinte comando do PowerShell:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-azure"></a>Entrar para o locatário VM no Azure
1. Entre no Portal do Azure.
2. Na barra de navegação à esquerda, clique em **máquinas virtuais**.
3. Na lista de máquinas virtuais, localizar **VM do Azure** que você criou anteriormente e selecioná-lo.
4. Na seção para a máquina virtual, clique em **conectar**.
5. Entre com a conta que você configurou quando criou a máquina virtual.
6. Abra um elevado **do Windows PowerShell** janela.
7. Digite **ipconfig/all**.
8. Você verá um endereço IPv4 que fica dentro de **10.100.0.0/24**. O ambiente de exemplo, o endereço é **10.100.0.4**, mas seu endereço pode ser diferente.
9. Para criar uma regra de firewall que permite que a máquina virtual responder a pings, execute o seguinte comando do PowerShell:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

10. Da máquina virtual no Azure, execute ping a máquina virtual na pilha do Azure, por meio do túnel. Para fazer isso, você pode executar ping o DIP que você registrou Azs VM.
   No ambiente de exemplo, isso é **10.1.0.4**, mas certifique-se de executar ping no endereço que você anotou no laboratório. Você verá um resultado semelhante à captura de tela a seguir:
   
    ![Ping com êxito](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)
11. Uma resposta da máquina virtual remota indica um teste com êxito! Você pode fechar a janela da máquina virtual. Para testar sua conexão, você pode tentar outros tipos de transferências de dados como uma cópia do arquivo.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Exibindo estatísticas de transferência de dados com a conexão de gateway
Se você quiser saber a quantidade de dados passa por meio de sua conexão site a site, essas informações estão disponíveis no **Conexão** seção. Esse teste também é outra maneira de verificar se o ping que você acabou de ser enviado foi realmente por meio de conexão VPN.

1. Quando estiver conectado à máquina virtual usuário na pilha do Azure, use sua conta de usuário para entrar no portal do usuário.
2. Vá para **todos os recursos**e, em seguida, selecione o **Azs Azure** conexão. **Conexões** é exibida.
4. Sobre o **Conexão** seção, as estatísticas de **dados em** e **dados** aparecem. Na captura de tela a seguir, os números grandes são atribuídos a transferência de arquivos adicionais. Você deve ver alguns valores de zero.
   
    ![Entrada e saída de dados](media/azure-stack-connect-vpn/Connection.png)

## <a name="next-steps"></a>Próximas etapas

[Implantar aplicativos do Azure e o Azure pilha](azure-stack-solution-pipeline.md)
