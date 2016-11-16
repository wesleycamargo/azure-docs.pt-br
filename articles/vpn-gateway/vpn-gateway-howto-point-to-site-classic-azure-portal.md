---
title: "Configurar uma conexão de gateway de VPN Ponto a Site com uma Rede Virtual do Azure usando o portal do Azure | Microsoft Docs"
description: "Conecte com segurança à sua rede Virtual do Azure, criando uma conexão de gateway de VPN ponto a Site usando o portal do Azure."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 65e14579-86cf-4d29-a6ac-547ccbd743bd
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/17/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 87d52de2d6ccb80390f8680371527a23904c5bb0


---
# <a name="configure-a-pointtosite-connection-to-a-vnet-using-the-azure-portal"></a>Configurar uma conexão Ponto a Site para uma rede virtual usando o portal do Azure
> [!div class="op_single_selector"]
> * [Resource Manager - Portal do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Portal do Azure - Clássico](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

Este artigo fornece uma orientação sobre a criação de uma VNet com uma conexão Ponto a Site no modelo de implantação clássica usando o Portal do Azure. Uma configuração Ponto a Site (P2S) permite que você crie uma conexão segura de um computador cliente individual com uma rede virtual. Uma conexão P2S é útil quando você deseja se conectar à rede virtual de um local remoto, como de casa ou de uma conferência. Ou quando você tem apenas alguns clientes que precisam se conectar a uma rede virtual.

As conexões Ponto a Site não exigem um dispositivo VPN ou um endereço IP voltado para o público para funcionar. Uma conexão VPN é estabelecida por meio do início da conexão do computador cliente. Para saber mais sobre as conexões Ponto a Site, confira as [Perguntas frequentes sobre o Gateway de VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) e [Sobre Gateway de VPN](vpn-gateway-about-vpngateways.md#point-to-site).

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Modelos e métodos de implantação para as conexões P2S
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

A tabela a seguir mostra os dois modelos de implantação e os métodos de implantação disponíveis para configurações de P2S. Quando houver um artigo com etapas de configuração disponível, o vincularemos diretamente desta tabela.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>Fluxo de trabalho básico
![Diagrama ponto a site](./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "point-to-site")

As seções a seguir o orientarão durante as etapas para criar uma conexão Ponto a Site segura para uma rede virtual. 

1. Criar uma rede virtual e um gateway de VPN
2. Gerar certificados
3. Carregar o arquivo .cer
4. Gerar o pacote de configuração de cliente VPN
5. Configurar o computador cliente
6. Conecte-se ao Azure

### <a name="example-settings"></a>Configurações de exemplo
Você pode usar as seguintes configurações de exemplo:

* **Nome: VNet1**
* **Espaço de endereço: 192.168.0.0/16**
* **Nome da sub-rede: FrontEnd**
* **Intervalo de endereços da sub-rede: 192.168.1.0/24**
* **Assinatura:** verifique se você tem mais de uma assinatura, verifique se está usando a correta.
* **Grupo de Recursos: TestRG**
* **Local: Leste dos EUA**
* **Tipo de conexão: ponto a site**
* **Espaço de Endereço de Cliente: 172.16.201.0/24**. Os clientes VPN que se conectarem à rede virtual usando esta conexão Ponto a Site receberão um endereço IP do pool especificado.
* **GatewaySubnet: 192.168.200.0/24**. A sub-rede de Gateway deve usar o nome "GatewaySubnet".
* **Tamanho:** selecione a SKU de gateway que você deseja usar.
* **Tipo de Roteamento: Dinâmico**

## <a name="a-namevnetvpnasection-1-create-a-virtual-network-and-a-vpn-gateway"></a><a name="vnetvpn"></a>Seção 1 - Criar uma rede virtual e um gateway de VPN
### <a name="a-namecreatevnetapart-1-create-a-virtual-network"></a><a name="createvnet"></a>Parte 1: criar uma rede virtual
Se você ainda não tiver uma rede virtual, crie uma. Capturas de tela são fornecidas como exemplos. Substitua os valores pelos seus próprios. Para criar uma rede virtual usando o portal do Azure, use as seguintes etapas: 

1. Em um navegador, navegue até o [portal do Azure](http://portal.azure.com) e, se necessário, entre com sua conta do Azure.
2. Clique em **Novo**. No campo **Pesquisar no marketplace**, digite "Rede Virtual". Localize **Rede Virtual** na lista retornada e clique para abrir a folha **Rede Virtual**.
   
    ![Pesquise a folha de rede virtual](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvnetportal700.png "Search for virtual network blade")
3. Perto da parte inferior da folha Rede Virtual, na lista **Selecionar um modelo de implantação**, selecione **Clássico** e clique em **Criar**.
   
    ![Selecionar o modelo de implantação](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/selectmodel.png "Select deployment model")
4. Na folha **Criar rede virtual** , defina as configurações da VNet. Nesta folha, você adicionará o primeiro espaço de endereço e um único intervalo de endereços de sub-rede. Depois de terminar a criação da rede virtual, você poderá voltar e adicionar espaços de endereço e sub-redes adicionais.
   
    ![Criar folha de rede virtual](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png "Create virtual network blade")
5. Verifique se a **Assinatura** é a correta. Você pode alterar as assinaturas usando o menu suspenso.
6. Clique em **Grupo de recursos** e selecione um grupo de recursos existente ou crie um novo digitando um nome para seu novo grupo de recursos. Se você estiver criando um novo grupo, dê o nome do grupo de recursos de acordo com seus valores de configuração planejados. Para saber mais sobre grupos de recursos, acesse [Visão geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups).
7. Em seguida, selecione as configurações do **Local** para sua VNet. O local determinará onde ficarão os recursos que você implanta para essa rede virtual.
8. Selecione **Fixar no painel** se quiser encontrar sua VNet facilmente no painel. Em seguida, clique em **Criar**.
   
    ![Fixar no painel](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/pintodashboard150.png "Pin to dashboard")
9. Depois de clicar em Criar, você verá um bloco em seu painel refletir o progresso de sua rede virtual. O bloco muda à medida que a rede virtual é criada.
   
    ![Criar bloco de rede virtual](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png "Creating virtual network tile")
10. Depois de criar a rede virtual, você pode adicionar o endereço IP de um servidor DNS para lidar com a resolução de nomes. Abra as configurações para a rede virtual, clique em servidores DNS e adicione o endereço IP do servidor DNS que você deseja usar. Essa configuração não cria um novo servidor DNS. Adicione um servidor DNS com o qual os recursos possam se comunicar.

Quando sua rede virtual tiver sido criada, você verá a denominação **Criada** listada em **Status** na página de redes no Portal Clássico do Azure.

### <a name="a-namegatewayapart-2-create-gateway-subnet-and-a-dynamic-routing-gateway"></a><a name="gateway"></a>Parte 2: criar um gateway de roteamento dinâmico e de sub-rede de gateway
Nesta etapa, você criará uma sub-rede de gateway e um Gateway de roteamento dinâmico. No portal do Azure para o modelo de implantação clássico, a criação da sub-rede de gateway e do gateway pode ser feita por meio das mesmas folhas de configuração.

1. No portal, navegue até a rede virtual para a qual você deseja criar um gateway.
2. Na folha de sua rede virtual, na folha **Visão geral**, na seção de conexões VPN, clique em **Gateway**.
   
    ![Clique aqui para criar um gateway](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png "Click here to create a gateway")
3. Na folha **Nova Conexão VPN**, selecione **Ponto a site**.
   
    ![Tipo de conexão P2S](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png "P2S connection type")
4. Para **Espaço de Endereço de Cliente**, adicione o intervalo de endereços IP. É o intervalo do qual os clientes VPN receberão um endereço IP ao se conectar. Exclua o intervalo de preenchimento automático e adicione seus próprios.
   
    ![Espaço de endereço de cliente](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png "Client address space")
5. Marque a caixa de seleção **Criar gateway imediatamente**.
   
    ![Criar gateway imediatamente](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/creategwimm.png "Create gateway immediately")
6. Clique em **Configuração de gateway opcional** para abrir a folha **Configuração de gateway**.
   
    ![Clique em configuração de gateway opcional](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png "Click optional gateway configuration")
7. Clique em **Definir configurações de sub-rede necessárias** para adicionar a **sub-rede de gateway**. Embora seja possível criar uma sub-rede de gateway tão pequena quanto /29, recomendamos que você crie uma sub-rede maior que inclua mais endereços selecionando pelo menos /28 ou /27. Isso permitirá endereços suficientes a fim de acomodar as possíveis configurações adicionais que você possa querer no futuro.
   
   > [!IMPORTANT]
   > Ao trabalhar com sub-redes de gateway, evite a associação de um NSG (grupo de segurança de rede) à sub-rede de gateway. Associar um grupo de segurança de rede a essa sub-rede pode fazer com que seu gateway de VPN para parar de funcionar conforme o esperado. Para obter mais informações sobre grupos de segurança de rede, confira [O que é um grupo de segurança de rede?](../virtual-network/virtual-networks-nsg.md)
   > 
   > 
   
    ![Adicionar GatewaySubnet](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png "Add GatewaySubnet")
8. Selecionar o **tamanho** do gateway. Esta é a SKU de gateway que você usará para criar o gateway de rede virtual. No portal, a SKU padrão é **Básica**. Para obter informações sobre os SKUs de gateway, confira [Sobre configurações de Gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
   
    ![tamanho de gateway](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)
9. Selecione o **Tipo de Roteamento** para seu gateway. Configurações de P2S requerem um tipo de roteamento **Dinâmico**. Clique em **OK** quando terminar de configurar esta folha.
   
    ![Configurar o tipo de roteamento](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png "Configure routing type")
10. Na folha **Nova Conexão VPN**, clique em **OK** na parte inferior da folha para começar a criar o gateway de rede virtual. Isso pode demorar até 45 minutos para ser concluído. 

## <a name="a-namegeneratecertsasection-2-generate-certificates"></a><a name="generatecerts"></a>Seção 2 - gerar certificados
Os certificados são usados pelo Azure para autenticar clientes de VPN para as VPNs Ponto a Site. Você exporta dados de certificado público (e não a chave privada) como um arquivo .cer X.509 codificado em Base 64 de um certificado raiz gerado por uma solução de certificado empresarial, ou um certificado raiz autoassinado. Em seguida, você importa os dados do certificado público do certificado raiz para o Azure. Além disso, você precisa gerar um certificado do cliente a partir do certificado raiz para os clientes. Cada cliente que deseja se conectar à rede virtual usando uma conexão P2S deve ter instalado um certificado do cliente gerado desde o certificado raiz.

### <a name="a-namecerapart-1-obtain-the-cer-file-for-the-root-certificate"></a><a name="cer"></a>Parte 1: obter o arquivo .cer do certificado raiz
Se você estiver usando uma solução empresarial, poderá usar a cadeia de certificados existente. Se você não estiver usando uma solução de autoridade de certificação corporativa, poderá criar um certificado autoassinado. Um método para criar um certificado autoassinado é o makecert.

* Se você estiver usando um sistema de certificado corporativo, obtenha o arquivo .cer do certificado raiz que deseja usar. 
* Se você não estiver usando uma solução de certificado corporativo, será preciso gerar um certificado raiz autoassinado. Para obter as etapas para o Windows 10, você pode conferir [Trabalhando com certificados raiz autoassinados para configurações de Ponto a Site](vpn-gateway-certificates-point-to-site.md).

1. Para obter um arquivo .cer de um certificado, abra **certmgr.msc** e localize o certificado raiz. Clique com o botão direito no certificado raiz autoassinado, clique em** todas as tarefas** e clique em **exportar**. Isso abre o **Assistente para Exportação de Certificados**.
2. No Assistente, clique em **Avançar**, escolha , **Não exportar a chave privada** e clique em **Avançar**.
3. Na página **Exportar Formato de Arquivo**, selecione **X.509 codificado em Base 64 (.CER).** Em seguida, clique em **Avançar**. 
4. Em **Arquivo a ser Exportado**, use **Procurar** para encontrar a localização para a qual você deseja exportar o certificado. Em **Nome do arquivo**, dê um nome ao arquivo de certificado. Em seguida, clique em **Próximo**.
5. Clique em **Concluir** para exportar o certificado.

### <a name="a-namegenclientcertapart-2-generate-a-client-certificate"></a><a name="genclientcert"></a>Parte 2: gerar um certificado de cliente
Você pode gerar um certificado exclusivo para cada cliente que se conectará ou pode usar o mesmo certificado em vários clientes. A vantagem da geração de certificados de cliente exclusivos é a capacidade de revogar um único certificado, se necessário. Caso contrário, se todos estiverem usando o mesmo certificado de cliente e se for necessário revogar o certificado para um cliente, você precisará gerar e instalar novos certificados para todos os clientes que usam o certificado para autenticação.

* Se você estiver usando uma solução de certificado corporativo, gere um certificado de cliente com o formato de valor de nome comum 'name@yourdomain.com',, em vez do formato 'nome do domínio\nomedeusuário'. 
* Se você estiver usando um certificado autoassinado, veja [Trabalhando com certificados raiz autoassinados para configurações Ponto a Site](vpn-gateway-certificates-point-to-site.md) para gerar um certificado de cliente.

### <a name="a-nameexportclientcertapart-3-export-the-client-certificate"></a><a name="exportclientcert"></a>Parte 3: exportar o certificado do cliente
Instale um certificado de cliente em cada computador que você queira conectar à rede virtual. Um certificado de cliente é necessário para autenticação. Você pode automatizar a instalação do certificado de cliente ou pode instalá-lo manualmente. As etapas a seguir guiarão você pela instalação manual do certificado de cliente.

1. Para exportar um certificado do cliente, use *certmgr.msc*. Clique com o botão direito no certificado do cliente que você deseja exportar, clique em **todas as tarefas** e, em seguida, clique em **exportar**.
2. Exporte o certificado de cliente com a chave privada. Este é um arquivo *.pfx* . Certifique-se de registrar ou se lembrar da senha (chave) que você definiu para esse certificado.

## <a name="a-nameuploadasection-3-upload-the-root-certificate-cer-file"></a><a name="upload"></a>Seção 3 - carregar o arquivo .cer do certificado raiz
Depois que o gateway tiver sido criado, você poderá carregar o arquivo .cer para um certificado raiz confiável no Azure. Você pode carregar arquivos em até 20 certificados raiz. Você não carrega a chave privada do certificado raiz no Azure. Depois que o arquivo .cer for carregado, o Azure o utilizará para autenticar clientes que se conectam à rede virtual.

1. Na seção **Conexões VPN** da folha de sua VNet, clique no gráfico **Clientes** para abrir a folha **Conexão VPN Ponto-a-site**.
   
    ![Clientes](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png "Clients")
2. Na folha **Conexão ponto a site**, clique em **Gerenciar certificados** para abrir a folha **Certificados**.<br>
   
    ![Folha Certificados](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png "Certificates blade")<br><br>
3. Na folha **Certificados**, clique em **Carregar** para abrir a folha **Carregar um certificado**.<br>
   
    ![Carregar a folha de certificados](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png "Upload certificates blade")<br>
4. Clique no gráfico de pasta para procurar o arquivo .cer. Selecione o arquivo e clique em **OK**. Atualize a página para ver o certificado carregado na folha **Certificados**.
   
    ![Carregar um certificado](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png "Upload certificate")<br>

## <a name="a-namevpnclientconfigasection-4-generate-the-vpn-client-configuration-package"></a><a name="vpnclientconfig"></a>Seção 4 - gerar o pacote de configuração de cliente VPN
Para se conectar à rede virtual, também será necessário configurar um cliente VPN. O computador cliente requer um certificado de cliente e o pacote de configuração de cliente VPN adequado para se conectar.

O pacote do cliente VPN contém informações de configuração para configurar o software de cliente VPN integrado ao Windows. O pacote não instala softwares adicionais. As configurações são específicas para a rede virtual a qual você deseja se conectar. Para obter a lista de sistemas operacionais quer recebem suporte, consulte a seção [Conexões Ponto a Site](vpn-gateway-vpn-faq.md#point-to-site-connections) das perguntas frequentes sobre o Gateway de VPN. 

### <a name="to-generate-the-vpn-client-configuration-package"></a>Para gerar o pacote de configuração de cliente VPN
1. No portal do Azure, na folha **Visão geral** da rede virtual, em **Conexões VPN**, clique no gráfico de cliente para abrir a folha **Conexão VPN Ponto-a-site**.
2. Na parte superior da folha **Conexão ponto-VPN a-site**, clique no pacote de download que corresponde ao sistema operacional cliente no qual ela será instalada:
   
   * Para clientes de 64 bits, selecione **Cliente VPN (64 bits)**.
   * Para clientes de 32 bits, selecione **Cliente VPN (32 bits)**.
     
     ![Baixe o pacote de configuração de cliente VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png "Download VPN client configuration package")<br>
3. Você verá uma mensagem de que o Azure está gerando o pacote de configuração de cliente VPN para a rede virtual. Depois de alguns minutos, o pacote será gerado e você verá uma mensagem no computador local de que o pacote foi descarregado. Salve o arquivo de pacote de configuração. Você o instalará em cada computador cliente que se conectará à rede virtual usando P2S.

## <a name="a-nameclientconfigurationasection-5-configure-the-client-computer"></a><a name="clientconfiguration"></a>Seção 5 - configurar o computador cliente
### <a name="part-1-install-the-client-certificate"></a>Parte 1: instalar um certificado de cliente
Cada computador cliente deve ter um certificado do cliente para se autenticar. Ao instalar o certificado do cliente, você precisará da senha criada quando o certificado do cliente foi exportado.

1. Copie o arquivo .pfx para o computador cliente.
2. Clique duas vezes no arquivo .pfx para instalá-lo. Não modifique o local de instalação.

### <a name="part-2-install-the-vpn-client-configuration-package"></a>Parte 2: instalar o pacote de configuração de cliente VPN
Você pode usar o mesmo pacote de configuração de cliente VPN em cada computador cliente, desde que a versão corresponda à arquitetura do cliente.

1. Copie o arquivo de configuração localmente no computador que você deseja conectar à sua rede virtual e clique duas vezes no arquivo .exe. 
2. Depois que o pacote tiver sido instalado, você pode iniciar a conexão VPN. O pacote de configuração não está assinado pela Microsoft. Convém assinar o pacote usando o serviço de assinatura de sua organização ou assiná-lo você mesmo usando [SignTool](http://go.microsoft.com/fwlink/p/?LinkId=699327). Não há problemas em usar o pacote sem assinatura. No entanto, se o pacote não estiver assinado, será exibido um aviso quando você instalar o pacote.
3. No computador cliente, navegue até **Configurações de Rede** e clique em **VPN**. Você verá a conexão listada. Ela mostra o nome da rede virtual a qual se conectará e terá uma aparência parecida com esta: 
   
    ![Cliente VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vpn.png "VNet VPN client")

## <a name="a-nameconnectasection-6-connect-to-azure"></a><a name="connect"></a>Seção 6 - conectar ao Azure
### <a name="connect-to-your-vnet"></a>Conectar-se à sua VNet
1. Para se conectar à sua rede virtual, no computador cliente, navegue até conexões VPN e localize a conexão VPN que você criou. Ele terá o mesmo nome da sua rede virtual. Clique em **Conectar**. Uma mensagem pop-up pode ser exibida sobre o uso do certificado. Se isso acontecer, clique em **Continuar** para usar os privilégios elevados. 
2. Na página de status **Conexão**, clique em **Conectar** para iniciar a conexão. Se for exibida uma tela de **Selecionar certificado** , verifique se o certificado de cliente mostrado é o que você deseja usar para se conectar. Se não for, use a seta suspensa para selecionar o certificado correto e clique em **OK**.
   
    ![Connect](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientconnect.png "VPN client connection")
3. Sua conexão já deve ter sido estabelecida.
   
    ![Conexão estabelecida](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/connected.png "Connection established")

### <a name="verify-the-vpn-connection"></a>Verificar a conexão VPN
1. Para verificar se a conexão VPN está ativa, abra um prompt de comandos com privilégios elevados e execute *ipconfig/all*.
2. Exiba os resultados. Observe que o endereço IP que você recebeu está dentro do intervalo de endereços de conectividade ponto a site que você especificou quando criou a sua VNet. Os resultados devem ser algo semelhante a isto:

Exemplo:

    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled

## <a name="next-steps"></a>Próximas etapas
Você pode adicionar máquinas virtuais à sua rede virtual. Veja [Como criar uma máquina virtual personalizada](../virtual-machines/virtual-machines-windows-classic-createportal.md).




<!--HONumber=Nov16_HO2-->


