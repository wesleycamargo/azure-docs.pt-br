---
title: "Conectar a sua rede local a uma rede virtual do Azure: VPN Site a Site (clássico): portal | Microsoft Docs"
description: "Etapas para criar uma conexão IPsec de sua rede local para uma rede virtual do Azure pela Internet pública. Essas etapas o ajudarão a criar uma conexão de Gateway de VPN Site a Site entre locais usando o portal."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 619ea430b13c16e8e4338413613d5798f36458ba
ms.lasthandoff: 03/25/2017


---
# <a name="create-a-site-to-site-connection-using-the-azure-portal-classic"></a>Criar uma conexão Site a Site usando o portal do Azure (clássico)
> [!div class="op_single_selector"]
> * [Resource Manager - Portal do Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Portal do Azure - Clássico](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [Clássico - Portal Clássico](vpn-gateway-site-to-site-create.md)
>
>


Uma conexão de gateway VPN Site a Site (S2S) é uma conexão por túnel VPN IPsec/IKE (IKEv1 ou IKEv2). Esse tipo de conexão exige um dispositivo VPN local com um endereço IP público atribuído a ele e não por uma NAT. As conexões Site a Site podem ser usadas para configurações híbridas e entre instalações.

Este artigo mostra como criar uma rede virtual e uma conexão de gateway de VPN Site a Site com sua rede local usando o modelo de implantação clássica e o Portal do Azure. 

![Diagrama de conexão Site a Site de Gateway de VPN entre locais](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>Modelos e métodos de implantação para conexões Site a Site
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

A tabela a seguir mostra os modelos de implantação e os métodos de configurações de site a site disponíveis no momento. Quando houver um artigo com etapas de configuração disponível, o vincularemos diretamente desta tabela.

[!INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Configurações adicionais
Se você quiser conectar Redes Virtuais, mas não estiver criando uma conexão com uma instalação local, confira [Configurar uma conexão de Rede Virtual para Rede Virtual](virtual-networks-configure-vnet-to-vnet-connection.md). Se você deseja adicionar uma conexão Site a Site a uma rede virtual que já tem uma conexão, consulte [Adicionar uma conexão de S2S a uma rede virtual com uma conexão de gateway de VPN existente](vpn-gateway-multi-site.md).

## <a name="before-you-begin"></a>Antes de começar
Antes de começar a configurar, verifique se você tem os seguintes itens:

* Um dispositivo VPN compatível e alguém que possa configurá-lo. Confira [Sobre dispositivos VPN](vpn-gateway-about-vpn-devices.md). Se você não estiver familiarizado com a configuração de seu dispositivo VPN ou se não estiver familiarizado com os intervalos de endereços IP localizados em sua configuração de rede local, será necessário coordenar com alguém que possa fornecer os detalhes para você.
* Um endereço IP IPv4 público voltado para o exterior para seu dispositivo VPN. Esse endereço IP não pode estar localizado atrás de um NAT.
* Uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [Benefícios do assinante do MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se para obter uma [conta gratuita](http://azure.microsoft.com/pricing/free-trial).
* No momento, o PowerShell é necessário para especificar a chave compartilhada e criar a conexão de gateway de VPN. Instale a versão mais recente dos cmdlets do PowerShell do SM (Gerenciamento de Serviços) do Azure. Para saber mais, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs): Ao trabalhar com o PowerShell para essa configuração, verifique se você está executando como administrador. 

> [!NOTE]
> Ao configurar uma conexão Site a Site, um endereço IP IPv4 público é necessário para seu dispositivo VPN.                                                                                                                                                                               
>
>

### <a name="values"></a>Exemplo de valores de configuração para este exercício
Ao usar estas etapas como um exercício, você poderá usar os valores de configuração de exemplo:

* **Nome da rede virtual:** TestVNet1
* **Espaço de endereço:** 
    * 10.11.0.0/16
    * 10.12.0.0/16 (opcional para este exercício)
* **Sub-redes:**
  * FrontEnd: 10.11.0.0/24
  * BackEnd: 10.12.0.0/24 (opcional para este exercício)
* **GatewaySubnet:** 10.11.255.0/27
* **Grupo de recursos:** TestRG1
* **Local:** Leste dos EUA
* **Servidor DNS:** 8.8.8.8 (opcional para este exercício)
* **Nome do site local:** Site2

## <a name="CreatVNet"></a>1. Criar uma rede virtual

Ao criar uma rede virtual a ser usada para uma conexão de S2S, você precisa certificar-se de que os espaços de endereço especificados não coincidam com um dos espaços de endereço de cliente dos sites locais aos quais você deseja se conectar. Se você tiver uma sobreposição de sub-redes, a conexão não funcionará corretamente.

* Se você já tiver uma rede virtual, verifique se as configurações são compatíveis com seu design de gateway de VPN. Preste atenção especial em todas as sub-redes que possam se sobrepor a outras redes. 

* Se você ainda não tiver uma rede virtual, crie uma. Capturas de tela são fornecidas como exemplos. Substitua os valores pelos seus próprios.

### <a name="to-create-a-virtual-network"></a>Para criar uma rede virtual

1. Em um navegador, navegue até o [portal do Azure](http://portal.azure.com) e, se necessário, entre com sua conta do Azure.
2. Clique em **Novo**. No campo **Pesquisar no Marketplace**, digite "Rede Virtual". Localize **Rede Virtual** na lista retornada e clique para abrir a folha **Rede Virtual**.

    ![Pesquise a folha de rede virtual](./media/vpn-gateway-howto-site-to-site-classic-portal/newvnetportal700.png)
3. Perto da parte inferior da folha Rede Virtual, na lista **Selecionar um modelo de implantação**, selecione **Clássico** e clique em **Criar**.

    ![Selecionar o modelo de implantação](./media/vpn-gateway-howto-site-to-site-classic-portal/selectmodel.png)
4. Na folha **Criar rede virtual** , defina as configurações da VNet. Nessa folha, você adiciona o primeiro espaço de endereço e um único intervalo de endereços de sub-rede. Depois de terminar a criação da rede virtual, você poderá voltar e adicionar espaços de endereço e sub-redes adicionais.

    ![Folha Criar rede virtual](./media/vpn-gateway-howto-site-to-site-classic-portal/createvnet.png "Folha Criar rede virtual")
5. Verifique se a **Assinatura** é a correta. Você pode alterar as assinaturas usando o menu suspenso.
6. Clique em **Grupo de recursos** e selecione um grupo de recursos existente ou crie um novo digitando um nome para seu novo grupo de recursos. Para saber mais sobre grupos de recursos, acesse [Visão geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups).
7. Em seguida, selecione as configurações do **Local** para sua VNet. O local determina onde ficarão os recursos que você implanta nessa rede virtual.
8. Selecione **Fixar no painel** se quiser encontrar sua VNet facilmente no painel. Em seguida, clique em **Criar**.

    ![Fixar no painel](./media/vpn-gateway-howto-site-to-site-classic-portal/pintodashboard150.png "Fixar no painel")
9. Depois de clicar em 'Criar', você verá um bloco em seu painel refletir o progresso de sua rede virtual. O bloco muda à medida que a rede virtual é criada.

    ![Bloco Criando rede virtual](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png "Criando rede virtual")

Quando sua rede virtual tiver sido criada, você verá a denominação **Criada** listada em **Status** na página de redes no Portal Clássico do Azure.

## <a name="additionaladdress"></a>2. Adicionar espaço de endereço adicional

Depois de criar sua rede virtual, adicione o outro espaço de endereço. A adição do outro espaço de endereço não é uma parte obrigatória de uma configuração de S2S, mas se você precisar de vários espaços de endereço, use as etapas a seguir:

1. Localize as redes virtuais no portal.
2. Na folha de sua rede virtual, na seção **Configurações**, clique em **Espaço de endereço**.
3. Na folha Espaço de endereço, clique em **+Adicionar** e insira o espaço de endereço adicional.
 
## <a name="dns"></a>3. Especificar um servidor DNS
As configurações de DNS não são uma parte obrigatória de uma configuração de S2S, mas o DNS é necessário se você quiser resolução de nomes.

Depois de criar a rede virtual, você pode adicionar o endereço IP de um servidor DNS para lidar com a resolução de nomes. Abra as configurações de sua rede virtual, clique em servidores DNS e adicione o endereço IP do servidor DNS que você deseja usar para resolução de nome. Essa configuração não cria um servidor DNS. Nas configurações do exemplo, usamos um servidor DNS público. Normalmente, convém usar um servidor DNS privado. Adicione um servidor DNS com o qual os recursos possam se comunicar.

1. Localize as redes virtuais no portal.
2. Na folha de sua rede virtual, na seção **Configurações**, clique em **Servidores DNS**.
3. Adicionar um servidor DNS.
4. Clique em **Salvar** na parte superior da página para salvar as configurações.
 
## <a name="localsite"></a>4. Configurar o site local

O site local normalmente se refere ao seu site local. Ele contém o endereço IP do dispositivo VPN no qual você criará uma conexão e os intervalos de endereços IP que serão roteados através do gateway de VPN para o dispositivo VPN.

1. No portal, navegue até a rede virtual para a qual você deseja criar um gateway.
2. Na folha de sua rede virtual, na folha **Visão geral**, na seção Conexões VPN, clique em **Gateway** para abrir a folha **Nova Conexão VPN**.

    ![Clique para definir as configurações de gateway](./media/vpn-gateway-howto-site-to-site-classic-portal/beforegw125.png "Clique para definir as configurações de gateway")
3. Na folha **Nova Conexão VPN**, selecione **Site a site**.

    ![Clique em site a site](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site.png "site a site")
4. Clique em **Site local - Definir configurações necessárias** para abrir a folha **Site local**. Defina as configurações e, em seguida, clique em **OK** para salvar as configurações.
    - **Nome:** crie um nome para seu site local a fim de facilitar a identificação.
    - **Endereço IP do gateway de VPN:** esse é o endereço IP público do dispositivo VPN de sua rede local. O dispositivo VPN exige um endereço IP IPv4 público. Especifique um endereço IP público válido para o dispositivo VPN ao qual você deseja se conectar. Ele não pode estar por trás do NAT e deve poder ser acessado pelo Azure.
    - **Espaço de endereço do cliente:** lista os intervalos de endereços IP que você deseja rotear até a rede local por meio deste gateway. Você pode adicionar vários intervalos de espaço de endereço. Certifique-se de que os intervalos especificados aqui não se sobreponham aos intervalos de outras redes as quais sua rede virtual se conecta, ou com os intervalos de endereços da própria rede virtual.

    ![Site local](./media/vpn-gateway-howto-site-to-site-classic-portal/localnetworksite.png "Configurar site local")

## <a name="gatewaysubnet"></a>5. Configurar a sub-rede de gateway

Você deve criar uma sub-rede de gateway para seu gateway de VPN. A sub-rede de gateway contém os endereços IP que os serviços de gateway de VPN usarão.

1. Na folha **Nova Conexão VPN**, marque a caixa de seleção **Criar gateway imediatamente**. A folha 'Configuração de gateway opcional' será exibida. Se você não marcar a caixa de seleção, não verá a folha para configurar a sub-rede de gateway.

    ![Configuração de gateway - Sub-rede, tamanho, tipo de roteamento](./media/vpn-gateway-howto-site-to-site-classic-portal/optional.png "Configuração de gateway - Sub-rede, tamanho, tipo de roteamento")
2. Clique em **Configuração de gateway opcional - Sub-rede, tamanho e tipo de roteamento** para abrir a folha **Configuração de gateway**.
3. Na folha **Configuração de gateway**, clique em **Subnet - Definir as configurações necessárias** para abrir a folha **Adicionar sub-rede**.

    ![Configuração de gateway - Sub-rede de gateway](./media/vpn-gateway-howto-site-to-site-classic-portal/subnetrequired.png "Configuração de gateway - Sub-rede de gateway")
4. Na folha **Adicionar sub-rede**, adicione a sub-rede de gateway. Ao adicionar a sub-rede de gateway, se for possível, é melhor criar uma sub-rede de gateway usando um bloco CIDR de /28 ou /27. Isso garantirá uma quantidade suficiente de endereços IP para acomodar outros requisitos futuros de configuração.  Clique em **OK** para salvar as configurações.

    ![Adicionar sub-rede de gateway](./media/vpn-gateway-howto-site-to-site-classic-portal/addgwsubnet.png "Adicionar sub-rede de gateway")

## <a name="sku"></a>6. Especificar o tipo de SKU e VPN
1. Selecionar o **tamanho** do gateway. Esta é a SKU de gateway que você usará para criar o gateway de rede virtual. No portal, o 'SKU Padrão' = **Básico**. Para obter informações sobre os SKUs de gateway, confira [Sobre configurações de Gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

    ![Selecionar tipo de SKUL e VPN](./media/vpn-gateway-howto-site-to-site-classic-portal/sku.png "Selecionar tipo de SKUL e VPN")
2. Selecione o **Tipo de Roteamento** para seu gateway. Isso é também conhecido como o tipo de VPN. É importante selecionar o tipo de gateway correto, pois não é possível converter o gateway de um tipo para outro. Seu dispositivo VPN deve ser compatível com o tipo de roteamento selecionado. Para saber mais sobre o tipo de VPN, confira [Sobre as Configurações de Gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md#vpntype). Você pode ver artigos sobre os tipos de VPN 'RouteBased' e 'PolicyBased'. 'Dinâmico' corresponde a 'RouteBased' e 'Estático' corresponde a 'PolicyBased'.
3. Clique em **OK** para salvar as configurações.
4. Na folha **Nova Conexão VPN**, clique em **OK** na parte inferior da folha para começar a criar o gateway de rede virtual. Isso pode demorar até 45 minutos para ser concluído.

## <a name="vpndevice"></a>7. Configurar o dispositivo de VPN

Trabalhe com o fabricante do dispositivo para obter informações específicas de configuração e configurar seu dispositivo. Confira [Dispositivos VPN](vpn-gateway-about-vpn-devices.md) para saber mais sobre dispositivos VPN que funcionam bem com o Azure. Além disso, verifique qualquer [Problemas de compatibilidade de dispositivo conhecidos](vpn-gateway-about-vpn-devices.md#known) para o dispositivo VPN que você deseja usar. 

Ao configurar seu dispositivo VPN, você precisará do endereço IP do gateway de VPN que você criou. Você pode localizar isso acessando a folha **Visão geral** de sua rede virtual.

## <a name="CreateConnection"></a>8. Criar a conexão
Nesta etapa, defina a chave compartilhada e crie a conexão. A chave que você define deve ser a mesma chave usada na configuração do dispositivo VPN.

> [!NOTE]
> No momento, essa configuração não está disponível no Portal do Azure. Use a versão de SM (Gerenciamento de Serviço) dos cmdlets do Azure PowerShell.                                                                                                                                                                             
>
>

### <a name="step-1-connect-to-your-azure-account"></a>Etapa 1. Conectar-se à sua conta do Azure

1. Abra o console do PowerShell com direitos elevados e conecte-se à sua conta. Use o exemplo a seguir para ajudar a se conectar:

        Login-AzureRmAccount
2. Verificar as assinaturas da conta.

        Get-AzureRmSubscription
3. Se você tiver mais de uma assinatura, selecione a assinatura que deseja usar.

        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
4. Adicione a versão de SM dos cmdlets do PowerShell.

        Add-AzureAccount

### <a name="step-2-set-the-shared-key-and-create-the-connection"></a>Etapa 2. Definir a chave compartilhada e criar a conexão

Ao trabalhar com o PowerShell e com o modelo de implantação clássica, às vezes, os nomes de recursos no portal não são os nomes esperados pelo Azure ao usar o PowerShell. As etapas a seguir ajudarão você a exportar o arquivo de configuração de rede a fim de obter os valores exatos dos nomes.

1. Crie um diretório em seu computador e exporte o arquivo de configuração de rede para o diretório. Neste exemplo, o arquivo de configuração de rede é exportado para C:\AzureNet.

         Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
2. Abra o arquivo de configuração de rede com um editor de xml e verifique os valores de 'Nome de LocalNetworkSite' e 'Nome de VirtualNetworkSite'. Modifique o exemplo para refletir os valores. Ao especificar um nome que contenha espaços, use aspas simples ao redor do valor.

3. Defina a chave compartilhada e crie a conexão. '-SharedKey' é um valor que você pode gerar e especificar. Neste exemplo, usamos ‘abc123’, mas você pode (e deve) gerar e usar algo mais complexo. O importante é que o valor especificado aqui deve ser o mesmo valor especificado ao configurar seu dispositivo VPN.

        Set-AzureVNetGatewayKey -VNetName 'Group TestRG1 TestVNet1' `
        -LocalNetworkSiteName 'D1BFC9CB_Site2' -SharedKey abc123
Quando a conexão é criada, o resultado é: **Status: Bem-sucedida**.

## <a name="verify"></a>9. Verificar a conexão

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="next-steps"></a>Próximas etapas
Quando sua conexão for concluída, você poderá adicionar máquinas virtuais às suas redes virtuais. Para saber mais, veja [Máquinas virtuais](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).


