---
title: "Conectar um computador a uma rede virtual do Azure usando o Ponto a Site: portal do Azure: clássico | Microsoft Docs"
description: "Conecte com segurança à sua Rede Virtual do Azure clássica, criando uma conexão de gateway de VPN ponto a Site usando o portal do Azure."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 65e14579-86cf-4d29-a6ac-547ccbd743bd
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 92e573d7f3ebfbe41c8012068a8262d6fc324da8
ms.lasthandoff: 03/21/2017


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-azure-portal-classic"></a>Configurar uma conexão Ponto a Site com uma rede virtual usando o Portal do Azure (Clássico)
> [!div class="op_single_selector"]
> * [Resource Manager - Portal do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Portal do Azure - Clássico](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>
>

Uma configuração Ponto a Site (P2S) permite que você crie uma conexão segura de um computador cliente individual com uma rede virtual. O P2S é uma conexão VPN sobre SSTP (Secure Socket Tunneling Protocol). Conexões ponto a site são úteis quando você deseja se conectar à sua rede virtual de um local remoto, como de casa ou de uma conferência, ou quando há apenas alguns clientes que precisam se conectar a uma rede virtual. As conexões ponto a site não exigem um dispositivo VPN ou um endereço IP voltado para o público. Estabeleça a conexão VPN no computador cliente.

Este artigo fornece uma orientação sobre a criação de uma VNet com uma conexão Ponto a Site no modelo de implantação clássica usando o Portal do Azure. Para saber mais sobre conexões Ponto a site, consulte as [Perguntas frequentes sobre Ponto a site](#faq) no final deste artigo.


### <a name="deployment-models-and-methods-for-p2s-connections"></a>Modelos e métodos de implantação para as conexões P2S
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

A tabela a seguir mostra os dois modelos de implantação e os métodos de implantação disponíveis para configurações de P2S. Quando houver um artigo com etapas de configuração disponível, o vincularemos diretamente desta tabela.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>Fluxo de trabalho básico
![Diagrama ponto a site](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)

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
* **Espaço de endereço: 192.168.0.0/16**<br>Neste exemplo, usamos apenas um espaço de endereço. Você pode ter mais de um espaço de endereço para sua rede virtual.
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



## <a name="vnetvpn"></a>Seção 1 - Criar uma rede virtual e um gateway de VPN

Antes de começar, verifique se você tem uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [Benefícios do assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se para obter uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial).
### <a name="createvnet"></a>Parte 1: criar uma rede virtual
Se você ainda não tiver uma rede virtual, crie uma. Capturas de tela são fornecidas como exemplos. Substitua os valores pelos seus próprios. Para criar uma rede virtual usando o portal do Azure, use as seguintes etapas:

1. Em um navegador, navegue até o [portal do Azure](http://portal.azure.com) e, se necessário, entre com sua conta do Azure.
2. Clique em **Novo**. No campo **Pesquisar no Marketplace**, digite "Rede Virtual". Localize **Rede Virtual** na lista retornada e clique para abrir a folha **Rede Virtual**.

    ![Pesquise a folha de rede virtual](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvnetportal700.png)
3. Perto da parte inferior da folha Rede Virtual, na lista **Selecionar um modelo de implantação**, selecione **Clássico** e clique em **Criar**.

    ![Selecionar o modelo de implantação](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/selectmodel.png)
4. Na folha **Criar rede virtual** , defina as configurações da VNet. Nesta folha, você adicionará o primeiro espaço de endereço e um único intervalo de endereços de sub-rede. Depois de terminar a criação da rede virtual, você poderá voltar e adicionar espaços de endereço e sub-redes adicionais.

    ![Criar folha de rede virtual](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png)
5. Verifique se a **Assinatura** é a correta. Você pode alterar as assinaturas usando o menu suspenso.
6. Clique em **Grupo de recursos** e selecione um grupo de recursos existente ou crie um novo digitando um nome para seu novo grupo de recursos. Se você estiver criando um novo grupo, dê o nome do grupo de recursos de acordo com seus valores de configuração planejados. Para saber mais sobre grupos de recursos, acesse [Visão geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups).
7. Em seguida, selecione as configurações do **Local** para sua VNet. O local determinará onde ficarão os recursos que você implanta para essa rede virtual.
8. Selecione **Fixar no painel** se quiser encontrar sua VNet facilmente no painel. Em seguida, clique em **Criar**.

    ![Fixar no painel](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/pintodashboard150.png)
9. Depois de clicar em Criar, você verá um bloco em seu painel refletir o progresso de sua rede virtual. O bloco muda à medida que a rede virtual é criada.

    ![Criar bloco de rede virtual](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png)
10. Depois de criar a rede virtual, você pode adicionar o endereço IP de um servidor DNS para lidar com a resolução de nomes. Abra as configurações para a rede virtual, clique em servidores DNS e adicione o endereço IP do servidor DNS que você deseja usar. Essa configuração não cria um novo servidor DNS. Adicione um servidor DNS com o qual os recursos possam se comunicar.

Quando sua rede virtual tiver sido criada, você verá a denominação **Criada** listada em **Status** na página de redes no Portal Clássico do Azure.

### <a name="gateway"></a>Parte 2: criar um gateway de roteamento dinâmico e de sub-rede de gateway
Nesta etapa, você criará uma sub-rede de gateway e um Gateway de roteamento dinâmico. No portal do Azure para o modelo de implantação clássico, a criação da sub-rede de gateway e do gateway pode ser feita por meio das mesmas folhas de configuração.

1. No portal, navegue até a rede virtual para a qual você deseja criar um gateway.
2. Na folha de sua rede virtual, na folha **Visão geral**, na seção de conexões VPN, clique em **Gateway**.

    ![Clique para criar um gateway](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png)
3. Na folha **Nova Conexão VPN**, selecione **Ponto a site**.

    ![Tipo de conexão Ponto a Site](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png)
4. Para **Espaço de Endereço de Cliente**, adicione o intervalo de endereços IP. É o intervalo do qual os clientes VPN receberão um endereço IP ao se conectar. Exclua o intervalo de preenchimento automático e adicione seus próprios.

    ![Espaço de endereço de cliente](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png)
5. Marque a caixa de seleção **Criar gateway imediatamente**.

    ![Criar gateway imediatamente](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/creategwimm.png)
6. Clique em **Configuração de gateway opcional** para abrir a folha **Configuração de gateway**.

    ![Clique em configuração de gateway opcional](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png)
7. Clique em **Definir configurações de sub-rede necessárias** para adicionar a **sub-rede de gateway**. Embora seja possível criar uma sub-rede de gateway tão pequena quanto /29, recomendamos que você crie uma sub-rede maior que inclua mais endereços selecionando pelo menos /28 ou /27. Isso permitirá endereços suficientes a fim de acomodar as possíveis configurações adicionais que você possa querer no futuro.

   > [!IMPORTANT]
   > Ao trabalhar com sub-redes de gateway, evite a associação de um NSG (grupo de segurança de rede) à sub-rede de gateway. Associar um grupo de segurança de rede a essa sub-rede pode fazer com que seu gateway de VPN para parar de funcionar conforme o esperado. Para obter mais informações sobre grupos de segurança de rede, confira [O que é um grupo de segurança de rede?](../virtual-network/virtual-networks-nsg.md)
   >
   >

    ![Adicionar GatewaySubnet](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png)
8. Selecionar o **tamanho** do gateway. Esta é a SKU de gateway que você usará para criar o gateway de rede virtual. No portal, a SKU padrão é **Básica**. Para obter informações sobre os SKUs de gateway, confira [Sobre configurações de Gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

    ![tamanho de gateway](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)
9. Selecione o **Tipo de Roteamento** para seu gateway. Configurações de P2S requerem um tipo de roteamento **Dinâmico**. Clique em **OK** quando terminar de configurar esta folha.

    ![Configurar o tipo de roteamento](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png)
10. Na folha **Nova Conexão VPN**, clique em **OK** na parte inferior da folha para começar a criar o gateway de rede virtual. Isso pode demorar até 45 minutos para ser concluído.

## <a name="generatecerts"></a>Seção 2 - Criar certificados
Os certificados são usados pelo Azure para autenticar clientes de VPN para as VPNs Ponto a Site. Depois de criar o certificado raiz, exporte os dados de certificado público (não a chave privada) como o arquivo .cer X.509 codificado em Base 64. Em seguida, carregue os dados do certificado público do certificado raiz para o Azure.

Cada computador cliente que se conecta a uma rede virtual usando ponto a site deve ter um certificado de cliente instalado. O certificado de cliente é gerado a partir do certificado raiz e instalado em cada computador cliente. Se um certificado de cliente válido não for instalado, e o cliente tentar se conectar à rede virtual, a autenticação falhará.

### <a name="cer"></a>Parte 1: obter a chave pública (.cer) do certificado raiz

####<a name="enterprise-certificate"></a>Certificado corporativo
 
Se você estiver usando uma solução empresarial, poderá usar a cadeia de certificados existente. Obtenha o arquivo .cer do certificado raiz que você deseja usar.

####<a name="self-signed-root-certificate"></a>Certificado raiz autoassinado

Se você não estiver usando uma solução de certificado corporativo, será necessário criar um certificado raiz autoassinado. Para criar um certificado autoassinado que contém os campos necessários para a autenticação P2S, use o PowerShell. [Criar um certificado autoassinado para conexões ponto a site usando o PowerShell](vpn-gateway-certificates-point-to-site.md) explica as etapas para criar um certificado raiz autoassinado.

> [!NOTE]
> Anteriormente, o makecert era o método recomendado para criar certificados raiz autoassinados e gerar certificados cliente para conexões ponto a site. Agora você pode usar o PowerShell para criar esses certificados. Uma vantagem de usar o PowerShell é a capacidade de criar certificados SHA-2. Consulte [Criar um certificado autoassinado para conexões ponto a site usando o PowerShell](vpn-gateway-certificates-point-to-site.md) para os valores necessários.
>
>

#### <a name="to-export-the-public-key-for-a-self-signed-root-certificate"></a>Para exportar a chave pública para um certificado raiz autoassinado

Conexões ponto a site exigem a chave pública (.cer) carregadas no Azure. As etapas a seguir ajudarão você a exportar o arquivo .cer para seu certificado raiz autoassinado.

1. Para obter um arquivo .cer do certificado, abra **certmgr.msc**. Localize o certificado raiz autoassinado, normalmente em 'Certificados – Usuário Atual\Pessoal\Certificados', então clique nele com o botão direito do mouse. Clique em **Todas as Tarefas** e, em seguida, em **Exportar**. Isso abre o **Assistente para Exportação de Certificados**.
2. No Assistente, clique em **Avançar**. Selecione **Não exportar a chave privada** e clique em **Avançar**.
3. Na página **Exportar Formato de Arquivo**, selecione **X.509 codificado em Base 64 (.CER).** e clique em **Avançar**. 
4. Em **Arquivo a ser Exportado**, use **Procurar** para encontrar a localização para a qual você deseja exportar o certificado. Em **Nome do arquivo**, dê um nome ao arquivo de certificado. Em seguida, clique em **Próximo**.
5. Clique em **Concluir** para exportar o certificado. Você verá a mensagem **A exportação foi bem-sucedida**. Clique em **OK** para fechar o assistente.

### <a name="genclientcert"></a>Parte 2: gerar um certificado de cliente

Você pode gerar um certificado exclusivo para cada cliente que se conectará ou pode usar o mesmo certificado em vários clientes. A vantagem da geração de certificados de cliente exclusivos é a capacidade de revogar um único certificado, se necessário. Caso contrário, se todos estiverem usando o mesmo certificado de cliente e se for necessário revogar o certificado para um cliente, você precisará gerar e instalar novos certificados para todos os clientes que usam o certificado para autenticação.

####<a name="enterprise-certificate"></a>Certificado corporativo
- Se você estiver usando uma solução de certificado corporativo, gere um certificado de cliente com o formato de valor de nome comum 'name@yourdomain.com', em vez do formato 'nome do domínio\nomedeusuário'.
- Verifique se o certificado do cliente que você emite baseia-se no modelo de certificado 'Usuário' que tem 'Autenticação de cliente' como o primeiro item na lista de uso, em vez de Logon de Cartão Inteligente, etc. Você pode verificar o certificado clicando duas vezes no certificado do cliente e exibindo **Detalhes > Uso Avançado de Chave**.

####<a name="self-signed-root-certificate"></a>Certificado raiz autoassinado 
Se você estiver usando um certificado raiz autoassinado, consulte [Gerar um certificado de cliente usando o PowerShell](vpn-gateway-certificates-point-to-site.md#clientcert) para obter as etapas para gerar um certificado de cliente compatível com as conexões ponto a site.

### <a name="exportclientcert"></a>Parte 3: exportar o certificado do cliente
Se você gerar um certificado de cliente de um certificado autoassinado usando as instruções do [PowerShell](vpn-gateway-certificates-point-to-site.md#clientcert), ele será instalado automaticamente no computador que você usou para gerá-lo. Se você quiser instalar um certificado de cliente em outro computador cliente, será necessário exportá-lo.

1. Para exportar um certificado de cliente, abra **certmgr.msc**. Clique com o botão direito no certificado do cliente que você deseja exportar, clique em **todas as tarefas** e, em seguida, clique em **exportar**. Isso abre o **Assistente para Exportação de Certificados**.
2. No Assistente, clique em **Avançar**, escolha **Sim, exportar a chave privada** e clique em **Avançar**.
3. Na página **Formato do Arquivo de Exportação**, deixe os padrões selecionados. Verifique se a opção **Incluir todos os certificados no caminho de certificação, se possível** está selecionada. Em seguida, clique em **Próximo**.
4. Na página **Segurança** , você deve proteger a chave privada. Se você optar por usar uma senha, não deixe de anotar ou lembrar da senha definida para esse certificado. Em seguida, clique em **Próximo**.
5. Em **Arquivo a ser Exportado**, use **Procurar** para encontrar a localização para a qual você deseja exportar o certificado. Em **Nome do arquivo**, dê um nome ao arquivo de certificado. Em seguida, clique em **Próximo**.
6. Clique em **Concluir** para exportar o certificado.

## <a name="upload"></a>Seção 3 - carregar o arquivo .cer do certificado raiz
Depois que o gateway tiver sido criado, você poderá carregar o arquivo .cer para um certificado raiz confiável no Azure. Você pode carregar arquivos em até 20 certificados raiz. Você não carrega a chave privada do certificado raiz no Azure. Depois que o arquivo .cer for carregado, o Azure o utilizará para autenticar clientes que se conectam à rede virtual.

1. Na seção **Conexões VPN** da folha de sua VNet, clique no gráfico **Clientes** para abrir a folha **Conexão VPN Ponto-a-site**.

    ![Clientes](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)
2. Na folha **Conexão ponto a site**, clique em **Gerenciar certificados** para abrir a folha **Certificados**.<br>

    ![Folha Certificados](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)<br><br>
3. Na folha **Certificados**, clique em **Carregar** para abrir a folha **Carregar um certificado**.<br>

    ![Carregar a folha de certificados](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png)<br>
4. Clique no gráfico de pasta para procurar o arquivo .cer. Selecione o arquivo e clique em **OK**. Atualize a página para ver o certificado carregado na folha **Certificados**.

    ![Carregar um certificado](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png)<br>

## <a name="vpnclientconfig"></a>Seção 4 - gerar o pacote de configuração de cliente VPN
Para se conectar à rede virtual, também será necessário configurar um cliente VPN. O computador cliente requer um certificado de cliente e o pacote de configuração de cliente VPN adequado para se conectar.

O pacote do cliente VPN contém informações de configuração para configurar o software de cliente VPN integrado ao Windows. O pacote não instala softwares adicionais. As configurações são específicas para a rede virtual a qual você deseja se conectar. Para obter a lista de sistemas operacionais clientes com suporte, consulte as [Perguntas frequentes sobre conexões Ponto a site](#faq) ao final desse artigo.

### <a name="to-generate-the-vpn-client-configuration-package"></a>Para gerar o pacote de configuração de cliente VPN
1. No portal do Azure, na folha **Visão geral** da rede virtual, em **Conexões VPN**, clique no gráfico de cliente para abrir a folha **Conexão VPN Ponto-a-site**.
2. Na parte superior da folha **Conexão ponto-VPN a-site**, clique no pacote de download que corresponde ao sistema operacional cliente no qual ela será instalada:

   * Para clientes de 64 bits, selecione **Cliente VPN (64 bits)**.
   * Para clientes de 32 bits, selecione **Cliente VPN (32 bits)**.

     ![Baixe o pacote de configuração de cliente VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png)<br>
3. Você verá uma mensagem de que o Azure está gerando o pacote de configuração de cliente VPN para a rede virtual. Depois de alguns minutos, o pacote será gerado e você verá uma mensagem no computador local de que o pacote foi descarregado. Salve o arquivo de pacote de configuração. Você o instalará em cada computador cliente que se conectará à rede virtual usando P2S.

## <a name="clientconfiguration"></a>Seção 5 - configurar o computador cliente
### <a name="part-1-install-an-exported-client-certificate"></a>Parte 1: instalar um certificado de cliente exportado

Se você quiser criar uma conexão P2S de um computador cliente diferente daquele usada para gerar os certificados cliente, instale um certificado de cliente. Ao instalar um certificado do cliente, você precisará da senha criada durante a exportação do certificado do cliente.

1. Localize e copie o arquivo *.pfx* no computador cliente. No computador cliente, clique duas vezes no arquivo *.pfx* para instalá-lo. Deixe **Localização do Repositório** como **Usuário Atual** e clique em **Avançar**.
2. Na página **Arquivo** a importar, não faça nenhuma alteração. Clique em **Próximo**.
3. Na página **Proteção da chave privada**, insira a senha do certificado, caso tenha usado uma, ou verifique se a entidade de segurança que está instalando o certificado está correta e clique em **Avançar**.
4. Na página **Repositório de Certificados**, deixe a localização padrão e clique em **Avançar**.
5. Clique em **Concluir**. No **Aviso de Segurança** da instalação do certificado, clique em **Sim**. Clique em 'Sim' sem medo, pois você gerou o certificado. O certificado foi importado com êxito.

### <a name="part-2-install-the-vpn-client-configuration-package"></a>Parte 2: instalar o pacote de configuração de cliente VPN
Você pode usar o mesmo pacote de configuração de cliente VPN em cada computador cliente, desde que a versão corresponda à arquitetura do cliente.

1. Copie o arquivo de configuração localmente no computador que você deseja conectar à sua rede virtual. 
2. No computador cliente, clique duas vezes no arquivo .exe para instalar o pacote. Como você criou o pacote de configuração, ele não está assinado. Isso significa que você pode ver um aviso. Se aparecer um pop-up do SmartScreen do Windows, clique em **Mais informações** (à esquerda)e em **Executar mesmo assim** para instalar o pacote.
3. No computador cliente, navegue até **Configurações de Rede** e clique em **VPN**. Você verá a conexão listada. Ela mostra o nome da rede virtual a qual se conectará e terá uma aparência parecida com esta:

    ![Cliente VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vpn.png)

## <a name="connect"></a>Seção 6 - conectar ao Azure
### <a name="connect-to-your-vnet"></a>Conectar-se à sua VNet
1. Para se conectar à sua rede virtual, no computador cliente, navegue até conexões VPN e localize a conexão VPN que você criou. Ele terá o mesmo nome da sua rede virtual. Clique em **Conectar**. Uma mensagem pop-up pode ser exibida sobre o uso do certificado. Se isso acontecer, clique em **Continuar** para usar os privilégios elevados.
2. Na página de status **Conexão**, clique em **Conectar** para iniciar a conexão. Se for exibida uma tela de **Selecionar certificado** , verifique se o certificado de cliente mostrado é o que você deseja usar para se conectar. Se não for, use a seta suspensa para selecionar o certificado correto e clique em **OK**.

    ![Conexão de cliente VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientconnect.png)
3. Sua conexão já deve ter sido estabelecida.

    ![Conexão estabelecida](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/connected.png)

> [!NOTE]
> Se você estiver usando um certificado emitido com uma solução de AC corporativa e estiver com problemas na autenticação, verifique a ordem de autenticação no certificado do cliente. Você pode verificar a ordem da lista de autenticação ao clicar duas vezes no certificado do cliente e acessar **Detalhes > Uso Avançado de Chave**. Verifique se a lista mostra 'Autenticação de cliente' como o primeiro item. Caso contrário, é necessário emitir um certificado de cliente com base no modelo Usuário que tem a Autenticação de cliente como o primeiro item na lista. 
>
>

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

## <a name="add"></a>Adicionar ou remover certificados raiz confiáveis

Você pode adicionar e remover um certificado raiz do Azure. Quando você remove um certificado confiável, os certificados de cliente gerados desde o certificado raiz não será capaz de se conectar ao Azure por meio de Ponto a Site. Se desejar que os clientes se conectem, eles precisarão instalar um novo certificado de cliente gerado de um certificado confiável no Azure.

### <a name="to-add-a-trusted-root-certificate"></a>Para adicionar um certificado raiz confiável

Você pode adicionar até 20 arquivos .cer de certificado raiz ao Azure. Para obter instruções, confira [Seção 3 - Carregar o arquivo .cer do certificado raiz](#upload).

### <a name="to-remove-a-trusted-root-certificate"></a>Para remover um certificado raiz confiável


1. Na seção **Conexões VPN** da folha de sua VNet, clique no gráfico **Clientes** para abrir a folha **Conexão VPN Ponto-a-site**.

    ![Clientes](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)
2. Na folha **Conexão ponto a site**, clique em **Gerenciar certificados** para abrir a folha **Certificados**.<br>

    ![Folha Certificados](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)<br><br>
3. Na folha **Certificados**, clique no botão de reticências ao lado do certificado que você deseja remover e clique em **Excluir**.

     ![Excluir o certificado raiz](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deleteroot.png)<br>


## <a name="revokeclient"></a>Revogar um certificado de cliente
É possível revogar certificados de cliente. A lista de certificados revogados permite que você negue seletivamente conectividade ponto a site com base em certificados de cliente individuais. Isso é diferente da remoção de um certificado raiz confiável. Se você remover um arquivo .cer de certificado raiz confiável do Azure, ele revogará o acesso para todos os certificados de cliente gerados/assinados pelo certificado raiz revogado. Revogar um certificado de cliente, em vez do certificado raiz, permite que os outros certificados gerados a partir do certificado raiz continuem a ser usados para autenticar a conexão ponto a site.

A prática comum é usar o certificado raiz para gerenciar o acesso em níveis de equipe ou organização, enquanto estiver usando certificados de cliente revogados para controle de acesso refinado em usuários individuais.

### <a name="to-revoke-a-client-certificate"></a>Para revogar um certificado de cliente

Você pode revogar um certificado de cliente adicionando a impressão digital à lista de revogação.

1. Recupere a impressão digital do certificado de cliente. Para saber mais, confira [Como recuperar a impressão digital de um certificado](https://msdn.microsoft.com/library/ms734695.aspx).
2. Copie as informações para um editor de texto e remova todos os espaços para que seja uma cadeia de caracteres contínua.
3. Navegue até a folha **'nome de rede virtual clássica' > Conexão VPN ponto a site > Certificados** e clique em **Lista de revogação** para abrir a folha de Lista de revogação. 
4. Na folha **Lista de revogação**, clique em **+Adicionar certificado** para abrir a folha **Adicionar certificado à lista de revogação**.
5. Na folha **Adicionar certificado à lista de revogação**, cole a impressão digital do certificado como uma linha contínua de texto, sem espaços. Clique em **OK** na parte inferior da folha.
6. Após a conclusão da atualização, o certificado não poderá mais ser usado para se conectar. Os clientes que tentam se conectar usando este certificado receberão uma mensagem informando que o certificado não é mais válido.


## <a name="faq"></a>Perguntas frequentes sobre Ponto a site

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>Próximas etapas
Quando sua conexão for concluída, você poderá adicionar máquinas virtuais às suas redes virtuais. Para saber mais, veja [Máquinas virtuais](https://docs.microsoft.com/azure/#pivot=services&panel=Compute). Para saber mais sobre redes e máquinas virtuais, consulte [Visão geral de rede do Azure e VM Linux](../virtual-machines/virtual-machines-linux-azure-vm-network-overview.md).

