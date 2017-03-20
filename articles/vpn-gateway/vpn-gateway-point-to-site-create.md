---
title: "Conectar um computador a uma rede virtual do Azure usando o ponto a site: portal clássico | Microsoft Docs"
description: "Conecte com segurança à sua Rede Virtual do Azure clássica criando uma conexão de gateway de VPN ponto a site usando o portal clássico."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 4f5668a5-9b3d-4d60-88bb-5d16524068e0
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: ffd5690853a7344c7122940ad9676d1903ba2d4a
ms.lasthandoff: 03/09/2017


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-classic-portal-classic"></a>Configurar uma conexão ponto a site com uma rede virtual usando o portal clássico (clássico)
> [!div class="op_single_selector"]
> * [Resource Manager - Portal do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Portal do Azure - Clássico](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> * [Clássico - Portal Clássico](vpn-gateway-point-to-site-create.md)
> 
> 

Uma configuração Ponto a Site (P2S) permite que você crie uma conexão segura de um computador cliente individual com uma rede virtual. O P2S é uma conexão VPN sobre SSTP (Secure Socket Tunneling Protocol). Conexões ponto a site são úteis quando você deseja se conectar à sua rede virtual de um local remoto, como de casa ou de uma conferência, ou quando há apenas alguns clientes que precisam se conectar a uma rede virtual. As conexões ponto a site não exigem um dispositivo VPN ou um endereço IP voltado para o público. Estabeleça a conexão VPN no computador cliente.

Este artigo fornece uma orientação sobre a criação de uma Rede Virtual com uma conexão Ponto a site no modelo de implantação clássica usando o portal clássico. Para saber mais sobre conexões Ponto a site, consulte as [Perguntas frequentes sobre Ponto a site](#faq) no final deste artigo.



### <a name="deployment-models-and-methods-for-p2s-connections"></a>Modelos e métodos de implantação para as conexões P2S
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

A tabela a seguir mostra os dois modelos de implantação e os métodos de implantação disponíveis para configurações de P2S. Quando houver um artigo com etapas de configuração disponível, o vincularemos diretamente desta tabela.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>Fluxo de trabalho básico
![Diagrama Ponto a Site](./media/vpn-gateway-point-to-site-create/p2sclassic.png "ponto a site")

As etapas a seguir orientarão você durante as etapas para criar uma conexão Ponto a Site segura para uma rede virtual. A configuração de uma conexão Ponto a Site é dividida em quatro seções. A ordem na qual você configura cada uma dessas seções é importante. Não ignore as etapas ou pule para as seguintes.

* **Seção 1** Criar uma rede virtual e um gateway de VPN.
* **Seção 2** Criar os certificados usados para autenticação e a carregá-los.
* **Seção 3** Exportar e instalar seus certificados de cliente.
* **Seção 4** Configurar seu cliente VPN.



## <a name="vnetvpn"></a>Seção 1 - Criar uma rede virtual e um gateway de VPN

Antes de começar, verifique se você tem uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [Benefícios do assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se para obter uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial).

### <a name="part-1-create-a-virtual-network"></a>Parte 1: criar uma rede virtual.
1. Faça logon no [portal clássico do Azure](https://manage.windowsazure.com). Estas etapas usam o portal clássico, não o portal do Azure. No momento, você não pode criar uma conexão P2S usando o portal do Azure.
2. No canto inferior esquerdo da tela, clique em **Novo**. No painel de navegação, clique em **Serviços de Rede** e, em seguida, clique em **Rede Virtual**. Clique em **Criação Personalizada** para iniciar o assistente de configuração.
3. Na página **Detalhes da Rede Virtual** , insira as informações a seguir e, em seguida, clique na seta de avanço na parte inferior direita.
   
   * **Nome**: nome da sua rede virtual. Por exemplo, “VNet1”. Esse é o nome você usará ao implantar as VMs nessa rede virtual.
   * **Local**: o local está diretamente relacionado ao local físico (região) onde você deseja que os recursos (VMs) residam. Por exemplo, se você desejar que as VMs implantadas nesta rede virtual estejam localizadas fisicamente no leste dos EUA, selecione esse local. Você não pode alterar a região associada à sua rede virtual depois de criá-la.
4. Na página **Servidores DNS e Conectividade de VPN** , insira as seguintes informações e, em seguida, clique na seta avançar no canto inferior direito.
   
   * **Servidores DNS**: insira o nome do servidor DNS e o endereço IP ou selecione um servidor DNS previamente registrado no menu de atalho. Essa configuração não cria um servidor DNS. Ela permite que você especifique os servidores DNS que deseja usar para a resolução de nomes dessa rede virtual. Se você quiser usar o serviço de resolução de nome padrão do Azure, deixe esta seção em branco.
   * **Configurar VPN Ponto a Site**: selecione a caixa de seleção.
5. Na página **Conectividade ponto a site** , especifique o intervalo de endereços IP do qual os seus clientes VPN receberão um endereço IP quando conectados. Há algumas regras sobre os intervalos de endereços que você pode especificar. É importante verificar se o intervalo especificado não coincide com nenhum dos intervalos existentes na rede local.
6. Digite as informações a seguir e, em seguida, clique na seta de avanço.
   
   * **Espaço de endereço**: inclua o IP Inicial e a Contagem de Endereços (CIDR).
   * **Adicionar espaço de endereço**: adicione o espaço de endereço somente se necessário para seu design de rede.
7. Na página **Espaços de Endereço de Rede Virtual** , especifique o intervalo de endereços que você deseja usar para sua rede virtual. Estes são os endereços IP dinâmicos (DIPS) que serão atribuídos às VMs e a outras instâncias de função que você implantar nessa rede virtual.<br><br>É especialmente importante selecionar um intervalo que não se sobreponha a nenhum dos intervalos usados para sua rede local. Você deve trabalhar em conjunto com o administrador da rede, que pode precisar reservar um intervalo de endereços IP de seu espaço de endereço de rede local para que você possa usar para sua rede virtual.
8. Insira as informações a seguir e clique na marca de seleção para iniciar a criação da sua rede virtual.
   
   * **Espaço de endereço**: adicione o intervalo de endereços IP internos que você deseja usar para essa rede virtual, incluindo IP Inicial e Contagem. É importante selecionar um intervalo que não se sobreponha a qualquer um dos intervalos usados para sua rede local. 
   * **Adicionar sub-rede**: não são necessárias sub-redes adicionais, mas convém criar uma sub-rede separada para as VMs que terão DIPS estáticos. Ou então, você pode colocar suas VMs em uma sub-rede separada das outras instâncias de função.
   * **Adicionar sub-rede de gateway**: a sub-rede de gateway é necessária para uma VPN ponto a site. Clique para adicionar a sub-rede de gateway. A sub-rede de gateway é usada apenas para o gateway de rede virtual.
9. Quando sua rede virtual tiver sido criada, você verá a denominação **Criada** listada em **Status** na página de redes no Portal Clássico do Azure. Depois que sua rede virtual tiver sido criada, você poderá criar o gateway de roteamento dinâmico.

### <a name="part-2-create-a-dynamic-routing-gateway"></a>Parte 2: criar um gateway de roteamento dinâmico
O tipo de gateway deve ser configurado como dinâmico. Os gateways de roteamento estáticos não funcionam com este recurso.

1. No portal clássico do Azure, na página **Redes**, clique na rede virtual que você criou e navegue até a página **Painel**.
2. Na parte inferior da página **Painel**, clique em **Criar Gateway**. Será exibida uma mensagem perguntando se **Você deseja criar um gateway para a rede virtual "VNet1"**. Clique em **Sim** para começar a criar o gateway. Pode levar até 45 minutos para que o gateway seja criado.

## <a name="generate"></a>Seção 2 - Gerar e carregar certificados
Os certificados são usados pelo Azure para autenticar clientes de VPN para as VPNs Ponto a Site. Depois de criar o certificado raiz, exporte os dados de certificado público (não a chave privada) como o arquivo .cer X.509 codificado em Base&64;. Em seguida, carregue os dados do certificado público do certificado raiz para o Azure.

Cada computador cliente que se conecta a uma rede virtual usando ponto a site deve ter um certificado de cliente instalado. O certificado de cliente é gerado a partir do certificado raiz e instalado em cada computador cliente. Se um certificado de cliente válido não for instalado, e o cliente tentar se conectar à rede virtual, a autenticação falhará.

Nesta seção, você fará o seguinte:

* Obtenha o arquivo .cer de um certificado raiz. Pode ser um certificado raiz autoassinado, ou você pode usar o sistema de certificados corporativo.
* Carregue o arquivo .cer no Azure.
* Gere certificados de cliente.

### <a name="root"></a>Parte 1: obter o arquivo .cer do certificado raiz


Se você estiver usando uma solução empresarial, poderá usar a cadeia de certificados existente. Obtenha o arquivo .cer do certificado raiz que você deseja usar.


Se você não estiver usando uma solução de certificado corporativo, será necessário criar um certificado raiz autoassinado. Para criar um certificado raiz autoassinado que contém os campos necessários para a autenticação P2S, use o PowerShell. [Criar um certificado raiz autoassinado para conexões ponto a site usando o PowerShell](vpn-gateway-certificates-point-to-site.md) explica as etapas para criar um certificado raiz autoassinado.

> [!NOTE]
> Anteriormente, o makecert era o método recomendado para criar certificados raiz autoassinados e gerar certificados cliente para conexões ponto a site. Agora você pode usar o PowerShell para criar esses certificados. Uma vantagem de usar o PowerShell é a capacidade de criar certificados SHA-2. Consulte [Criar um certificado raiz autoassinado para conexões ponto a site usando o PowerShell](vpn-gateway-certificates-point-to-site.md) para os valores necessários.
>
>


#### <a name="to-export-the-public-key-for-a-self-signed-root-certificate"></a>Para exportar a chave pública para um certificado raiz autoassinado.

Conexões ponto a site exigem a chave pública (.cer) carregadas no Azure. As etapas a seguir ajudarão você a exportar o arquivo .cer para seu certificado autoassinado.

1. Para obter um arquivo .cer do certificado, abra **certmgr.msc**. Localize o certificado raiz autoassinado, normalmente em 'Certificados – Usuário Atual\Pessoal\Certificados', então clique nele com o botão direito do mouse. Clique em **Todas as Tarefas** e, em seguida, em **Exportar**. Isso abre o **Assistente para Exportação de Certificados**.
2. No Assistente, clique em **Avançar**. Selecione **Não exportar a chave privada** e clique em **Avançar**.
3. Na página **Exportar Formato de Arquivo**, selecione **X.509 codificado em Base&64; (.CER).** e clique em **Avançar**. 
4. Em **Arquivo a ser Exportado**, use **Procurar** para encontrar a localização para a qual você deseja exportar o certificado. Em **Nome do arquivo**, dê um nome ao arquivo de certificado. Em seguida, clique em **Próximo**.
5. Clique em **Concluir** para exportar o certificado. Você verá a mensagem **A exportação foi bem-sucedida**. Clique em **OK** para fechar o assistente.

### <a name="upload"></a>Parte 2: Carregar o arquivo .cer do certificado raiz no portal clássico do Azure

Adicione um certificado confiável ao Azure. Quando você adiciona um arquivo X.509 codificado de Base64 (. cer) ao Azure, está dizendo ao Azure para confiar no certificado raiz que o arquivo representa. Você pode carregar arquivos em até 20 certificados raiz. Você não carrega a chave privada do certificado raiz no Azure. Depois que o arquivo .cer for carregado, o Azure o utilizará para autenticar clientes que se conectam à rede virtual.

1. No portal clássico do Azure, na página **Certificados** de sua rede virtual, clique em **Carregar um certificado raiz**.
2. Na página **Carregar certificado** , procure o certificado raiz .cer e clique na marca de seleção.

### <a name="createclientcert"></a>Parte 3: Gerar um certificado de cliente
Você pode gerar um certificado exclusivo para cada cliente que se conectará ou pode usar o mesmo certificado em vários clientes. A vantagem da geração de certificados de cliente exclusivos é a capacidade de revogar um único certificado, se necessário. Caso contrário, se todos estiverem usando o mesmo certificado de cliente e se for necessário revogar o certificado para um cliente, você precisará gerar e instalar novos certificados para todos os clientes que usam o certificado para autenticação.

####<a name="enterprise-certificate"></a>Certificado corporativo
- Se você estiver usando uma solução de certificado corporativo, gere um certificado de cliente com o formato de valor de nome comum 'name@yourdomain.com', em vez do formato 'nome do domínio\nomedeusuário'.
- Verifique se o certificado do cliente que você emite baseia-se no modelo de certificado 'Usuário' que tem 'Autenticação de cliente' como o primeiro item na lista de uso, em vez de Logon de Cartão Inteligente, etc. Você pode verificar o certificado clicando duas vezes no certificado do cliente e exibindo **Detalhes > Uso Avançado de Chave**.

####<a name="self-signed-root-root-certificate"></a>Certificado raiz autoassinado 
Se você estiver usando um certificado raiz autoassinado, consulte [Gerar um certificado de cliente usando o PowerShell](vpn-gateway-certificates-point-to-site.md#clientcert) para obter as etapas para gerar um certificado de cliente compatível com as conexões ponto a site.


## <a name="installclientcert"></a>Seção 3 - Exportar e instalar o certificado de cliente

Depois de gerar um certificado cliente, exporte o certificado como um arquivo .pfx e instale-o no computador cliente. Cada computador cliente deve ter um certificado do cliente para se autenticar. Automatize a instalação do certificado cliente ou instale-o manualmente. As etapas a seguir guiarão você pela instalação manual do certificado de cliente.

### <a name="export-the-client-certificate"></a>Exportar o certificado do cliente

1. Para exportar um certificado de cliente, abra **certmgr.msc**. Clique com o botão direito no certificado do cliente que você deseja exportar, clique em **todas as tarefas** e, em seguida, clique em **exportar**. Isso abre o **Assistente para Exportação de Certificados**.
2. No Assistente, clique em **Avançar**, escolha **Sim, exportar a chave privada** e clique em **Avançar**.
3. Na página **Exportar Formato de Arquivo** , você pode deixar os padrões selecionados. Em seguida, clique em **Próximo**. 
4. Na página **Segurança** , você deve proteger a chave privada. Se você optar por usar uma senha, não deixe de anotar ou lembrar da senha definida para esse certificado. Em seguida, clique em **Próximo**.
5. Em **Arquivo a ser Exportado**, use **Procurar** para encontrar a localização para a qual você deseja exportar o certificado. Em **Nome do arquivo**, dê um nome ao arquivo de certificado. Em seguida, clique em **Próximo**.
6. Clique em **Concluir** para exportar o certificado.

### <a name="install-the-client-certificate"></a>Instalar um certificado cliente

Ao instalar o certificado do cliente, você precisará da senha criada quando o certificado do cliente foi exportado.

1. Localize e copie o arquivo *.pfx* no computador cliente. No computador cliente, clique duas vezes no arquivo *.pfx* para instalá-lo. Deixe **Localização do Repositório** como **Usuário Atual** e clique em **Avançar**.
2. Na página **Arquivo** a importar, não faça nenhuma alteração. Clique em **Próximo**.
3. Na página **Proteção da chave privada**, insira a senha do certificado, caso tenha usado uma, ou verifique se a entidade de segurança que está instalando o certificado está correta e clique em **Avançar**.
4. Na página **Repositório de Certificados**, deixe a localização padrão e clique em **Avançar**.
5. Clique em **Concluir**. No **Aviso de Segurança** da instalação do certificado, clique em **Sim**. Clique em 'Sim' sem medo, pois você gerou o certificado. O certificado foi importado com êxito.

## <a name="vpnclientconfig"></a>Seção 4 - Configurar seu cliente VPN
Para se conectar à rede virtual, também será necessário configurar um cliente VPN. O cliente exige um certificado de cliente e o pacote de configuração de cliente VPN adequado para se conectar. Para configurar seu cliente VPN, execute as etapas a seguir, na ordem.

### <a name="part-1-create-the-vpn-client-configuration-package"></a>Parte 1: criar o pacote de configuração de cliente VPN.
1. No portal clássico do Azure, na página **Painel** da sua rede virtual, navegue até o menu de visão rápida no canto direito. O pacote do cliente VPN contém informações de configuração para configurar o software de cliente VPN integrado ao Windows. O pacote não instala softwares adicionais. As configurações são específicas para a rede virtual a qual você deseja se conectar. Para obter a lista de sistemas operacionais clientes com suporte, consulte as [Perguntas frequentes sobre conexões Ponto a site](#faq) ao final desse artigo.<br><br>Selecione o pacote de download que corresponde ao sistema operacional cliente no qual ela será instalada:
   
   * Para clientes de 32 bits, selecione **Baixar o Pacote de Cliente VPN de 32 bits**.
   * Para clientes de 64 bits, selecione **Baixar o Pacote de Cliente VPN de 64 bits**.
2. A criação do pacote de cliente pode demorar um pouco. Depois que o pacote tiver sido concluído, você poderá baixar o arquivo. O arquivo *.exe* baixado pode ser armazenado com segurança no seu computador local.
3. Depois de gerar e baixar o pacote de cliente VPN no portal clássico do Azure, você pode instalar o pacote do cliente no computador cliente por meio do qual deseja se conectar à sua rede virtual. Se você planeja instalar o pacote do cliente VPN em vários computadores cliente, certifique-se de que cada um deles também tenha um certificado de cliente instalado.

### <a name="part-2-install-the-vpn-configuration-package-on-the-client"></a>Parte 2: Instalar o pacote de configuração VPN no cliente
1. Copie o arquivo de configuração localmente no computador que você deseja conectar à sua rede virtual e clique duas vezes no arquivo .exe. 
2. Depois que o pacote tiver sido instalado, você pode iniciar a conexão VPN. O pacote de configuração não está assinado pela Microsoft. Convém assinar o pacote usando o serviço de assinatura de sua organização ou assiná-lo você mesmo usando [SignTool](http://go.microsoft.com/fwlink/p/?LinkId=699327). Não há problemas em usar o pacote sem assinatura. No entanto, se o pacote não estiver assinado, será exibido um aviso quando você instalar o pacote.
3. No computador cliente, navegue até **Configurações de Rede** e clique em **VPN**. Você verá a conexão listada. Ela mostrará o nome da rede virtual a qual se conectará e terá uma aparência parecida com esta: 
   
    ![Cliente VPN](./media/vpn-gateway-point-to-site-create/vpn.png "Cliente VPN")

### <a name="part-3-connect-to-azure"></a>Parte 3: Conectar-se ao Azure
1. Para se conectar à sua rede virtual, no computador cliente, navegue até conexões VPN e localize a conexão VPN que você criou. Ele terá o mesmo nome da sua rede virtual. Clique em **Conectar**. Uma mensagem pop-up pode ser exibida sobre o uso do certificado. Se isso acontecer, clique em **Continuar** para usar os privilégios elevados. 
2. Na página de status **Conexão**, clique em **Conectar** para iniciar a conexão. Se for exibida uma tela de **Selecionar certificado** , verifique se o certificado de cliente mostrado é o que você deseja usar para se conectar. Se não for, use a seta suspensa para selecionar o certificado correto e clique em **OK**.
   
    ![Cliente VPN 2](./media/vpn-gateway-point-to-site-create/clientconnect.png "Conexão de cliente VPN")
3. Sua conexão já deve ter sido estabelecida.
   
    ![Cliente VPN 3](./media/vpn-gateway-point-to-site-create/connected.png "Conexão de cliente VPN 2")

> [!NOTE]
> Se você estiver usando um certificado emitido com uma solução de AC corporativa e estiver com problemas na autenticação, verifique a ordem de autenticação no certificado do cliente. Você pode verificar a ordem da lista de autenticação ao clicar duas vezes no certificado do cliente e acessar **Detalhes > Uso Avançado de Chave**. Verifique se a lista mostra 'Autenticação de cliente' como o primeiro item. Caso contrário, é necessário emitir um certificado de cliente com base no modelo Usuário que tem a Autenticação de cliente como o primeiro item na lista. 
>
>

### <a name="part-4-verify-the-vpn-connection"></a>Parte 4: Verificar a conexão VPN
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

## <a name="faq"></a>Perguntas frequentes sobre Ponto a site

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>Próximas etapas

Quando sua conexão for concluída, você poderá adicionar máquinas virtuais às suas redes virtuais. Para saber mais, veja [Máquinas virtuais](https://docs.microsoft.com/azure/#pivot=services&panel=Compute). Para saber mais sobre redes e máquinas virtuais, consulte [Visão geral de rede do Azure e VM Linux](../virtual-machines/virtual-machines-linux-azure-vm-network-overview.md).


