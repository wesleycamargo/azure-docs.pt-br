---
title: 'Conectar um computador a uma rede virtual do Azure usando o Ponto a Site: portal | Microsoft Docs'
description: "Conecte com segurança à sua rede Virtual do Azure, criando uma conexão de gateway de VPN ponto a Site usando o Resource Manager e o portal do Azure."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a15ad327-e236-461f-a18e-6dbedbf74943
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 6703df0f64534ed638e570342eef7fbda2a74d2e
ms.lasthandoff: 04/03/2017


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-azure-portal"></a>Configurar uma conexão Ponto a Site para uma rede virtual usando o portal do Azure
> [!div class="op_single_selector"]
> * [Resource Manager - Portal do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Portal do Azure - Clássico](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

Uma configuração Ponto a Site (P2S) permite que você crie uma conexão segura de um computador cliente individual com uma rede virtual. O P2S é uma conexão VPN sobre SSTP (Secure Socket Tunneling Protocol). Conexões ponto a site são úteis quando você deseja se conectar à sua rede virtual de um local remoto, como de casa ou de uma conferência, ou quando há apenas alguns clientes que precisam se conectar a uma rede virtual. As conexões ponto a site não exigem um dispositivo VPN ou um endereço IP voltado para o público. Estabeleça a conexão VPN no computador cliente.

Este artigo orienta na criação de uma Rede Virtual com uma conexão Ponto a site usando o Portal do Azure. Para saber mais sobre conexões Ponto a site, consulte as [Perguntas frequentes sobre Ponto a site](#faq) no final deste artigo.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Modelos e métodos de implantação para as conexões P2S
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

A tabela a seguir mostra os dois modelos de implantação e os métodos de implantação disponíveis para configurações de P2S. Quando houver um artigo com etapas de configuração disponível, o vincularemos diretamente desta tabela.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>Fluxo de trabalho básico
![Diagrama ponto a site](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-connection-diagram.png)

### <a name="example"></a>Valores de exemplo
* **Nome: VNet1**
* **Espaço de endereço: 192.168.0.0/16**<br>Neste exemplo, usamos apenas um espaço de endereço. Você pode ter mais de um espaço de endereço para sua rede virtual.
* **Nome da sub-rede: FrontEnd**
* **Intervalo de endereços da sub-rede: 192.168.1.0/24**
* **Assinatura:** verifique se você tem mais de uma assinatura, verifique se está usando a correta.
* **Grupo de Recursos: TestRG**
* **Local: Leste dos EUA**
* **GatewaySubnet: 192.168.200.0/24**
* **Nome do gateway de rede virtual: VNet1GW**
* **Tipo de gateway: VPN**
* **Tipo de VPN: baseada em rota**
* **Endereço IP público: VNet1GWpip**
* **Tipo de conexão: Ponto a Site**
* **Pool de endereços do cliente: 172.16.201.0/24**<br>Os clientes VPN que se conectarem à rede virtual usando esta conexão Ponto a Site receberão um endereço IP do pool de endereços do cliente.

## <a name="createvnet"></a>Parte 1 - Criar uma rede virtual
Antes de começar, verifique se você tem uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [Benefícios do assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se para obter uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial). Se você estiver criando esta configuração como um exercício, poderá consultar estes [valores de exemplo](#example).

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="address"></a>Parte 2 - Especificar o espaço de endereço e sub-redes
Você pode adicionar um espaço de endereço e sub-redes adicionais para sua rede virtual após sua criação.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="gatewaysubnet"></a>Parte 3 - Adicionar uma sub-rede de gateway

Antes de conectar sua Rede Virtual a um gateway, você precisará criar a sub-rede de gateway para a Rede Virtual à qual você deseja se conectar. Os serviços de gateway usam os endereços IP especificados na sub-rede do gateway. Se possível, crie uma sub-rede de gateway usando um bloco CIDR de /28 ou /27, para fornecer endereços IP suficientes para acomodar requisitos de configuração futuros.

As capturas de tela desta seção são fornecidas como um exemplo de referência. Use o intervalo de endereços GatewaySubnet que corresponda aos valores necessários para sua configuração.

###<a name="to-create-a-gateway-subnet"></a>Para criar uma sub-rede de gateway

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="dns"></a>Parte 4 - Especificar um servidor DNS (opcional)

O DNS não é necessário para uma conexão Ponto a Site. No entanto, se você quiser ter a resolução de nomes dos recursos que são implantados em sua rede virtual, deverá especificar um servidor DNS. Essa configuração permite que você especifique o servidor DNS que deseja usar para a resolução de nomes dessa rede virtual. Ela não cria um servidor DNS.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="creategw"></a>Parte 5 - Criar um gateway de rede virtual
As conexões Ponto a Site exigem as seguintes configurações:

* Tipo de gateway: VPN
* Tipo de VPN: baseada em rota

### <a name="to-create-a-virtual-network-gateway"></a>Para criar um gateway da rede virtual
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="generatecert"></a>Parte 6 - Gerar certificados

Os certificados são usados pelo Azure para autenticar clientes de VPN para as VPNs Ponto a Site. Depois de criar o certificado raiz, exporte os dados de certificado público (não a chave privada) como o arquivo .cer X.509 codificado em Base 64. Em seguida, carregue os dados do certificado público do certificado raiz para o Azure.

Cada computador cliente que se conecta a uma rede virtual usando ponto a site deve ter um certificado de cliente instalado. O certificado de cliente é gerado a partir do certificado raiz e instalado em cada computador cliente. Se um certificado de cliente válido não for instalado, e o cliente tentar se conectar à rede virtual, a autenticação falhará.

### <a name="getcer"></a>Etapa 1 - Obter o arquivo .cer do certificado raiz

####<a name="enterprise-certificate"></a>Certificado corporativo
 
Se você estiver usando uma solução empresarial, poderá usar a cadeia de certificados existente. Obtenha o arquivo .cer do certificado raiz que você deseja usar.

####<a name="self-signed-root-certificate"></a>Certificado raiz autoassinado

Se você não estiver usando uma solução de certificado corporativo, será necessário criar um certificado raiz autoassinado. Para criar um certificado raiz autoassinado que contém os campos necessários para a autenticação P2S, use o PowerShell. [Criar um certificado raiz autoassinado para conexões ponto a site usando o PowerShell](vpn-gateway-certificates-point-to-site.md) explica as etapas para criar um certificado raiz autoassinado.

> [!NOTE]
> Anteriormente, o makecert era o método recomendado para criar certificados raiz autoassinados e gerar certificados cliente para conexões ponto a site. Agora você pode usar o PowerShell para criar esses certificados. Uma vantagem de usar o PowerShell é a capacidade de criar certificados SHA-2. Consulte [Criar um certificado raiz autoassinado para conexões ponto a site usando o PowerShell](vpn-gateway-certificates-point-to-site.md) para os valores necessários.
>
>


#### <a name="to-export-the-public-key-for-a-self-signed-root-certificate"></a>Para exportar a chave pública para um certificado raiz autoassinado

Conexões ponto a site exigem a chave pública (.cer) carregadas no Azure. As etapas a seguir ajudarão você a exportar o arquivo .cer para seu certificado raiz autoassinado.

1. Para obter um arquivo .cer do certificado, abra **certmgr.msc**. Localize o certificado raiz autoassinado, normalmente em 'Certificados – Usuário Atual\Pessoal\Certificados', então clique nele com o botão direito do mouse. Clique em **Todas as Tarefas** e, em seguida, em **Exportar**. Isso abre o **Assistente para Exportação de Certificados**.
2. No Assistente, clique em **Avançar**. Selecione **Não exportar a chave privada** e clique em **Avançar**.
3. Na página **Exportar Formato de Arquivo**, selecione **X.509 codificado em Base 64 (.CER).** e clique em **Avançar**. 
4. Em **Arquivo a ser Exportado**, use **Procurar** para encontrar a localização para a qual você deseja exportar o certificado. Em **Nome do arquivo**, dê um nome ao arquivo de certificado. Em seguida, clique em **Próximo**.
5. Clique em **Concluir** para exportar o certificado. Você verá a mensagem **A exportação foi bem-sucedida**. Clique em **OK** para fechar o assistente.

### <a name="generateclientcert"></a>Etapa 2 - Gerar um certificado de cliente
Você pode gerar um certificado exclusivo para cada cliente que se conectará à rede virtual ou pode usar o mesmo certificado em vários clientes. A vantagem da geração de certificados de cliente exclusivos é a capacidade de revogar um único certificado, se necessário. Caso contrário, se todos estiverem usando o mesmo certificado de cliente e se for necessário revogar o certificado para um cliente, você precisará gerar e instalar novos certificados para todos os clientes que usam aquele certificado para autenticação.

####<a name="enterprise-certificate"></a>Certificado corporativo
- Se você estiver usando uma solução de certificado corporativo, gere um certificado de cliente com o formato de valor de nome comum 'name@yourdomain.com', em vez do formato 'nome do domínio\nomedeusuário'.
- Verifique se o certificado do cliente que você emite baseia-se no modelo de certificado 'Usuário' que tem 'Autenticação de cliente' como o primeiro item na lista de uso, em vez de Logon de Cartão Inteligente, etc. Você pode verificar o certificado clicando duas vezes no certificado do cliente e exibindo **Detalhes > Uso Avançado de Chave**.

####<a name="self-signed-root-certificate"></a>Certificado raiz autoassinado 
Se você estiver usando um certificado raiz autoassinado, consulte [Gerar um certificado de cliente usando o PowerShell](vpn-gateway-certificates-point-to-site.md#clientcert) para obter as etapas para gerar um certificado de cliente compatível com as conexões ponto a site.


### <a name="exportclientcert"></a>Etapa 3 - Exportar o certificado de cliente
Se você gerar um certificado de cliente de um certificado autoassinado usando as instruções do [PowerShell](vpn-gateway-certificates-point-to-site.md#clientcert), ele será instalado automaticamente no computador que você usou para gerá-lo. Se você quiser instalar um certificado de cliente em outro computador cliente, será necessário exportá-lo.

1. Para exportar um certificado de cliente, abra **certmgr.msc**. Clique com o botão direito no certificado do cliente que você deseja exportar, clique em **todas as tarefas** e, em seguida, clique em **exportar**. Isso abre o **Assistente para Exportação de Certificados**.
2. No Assistente, clique em **Avançar**, escolha **Sim, exportar a chave privada** e clique em **Avançar**.
3. Na página **Formato do Arquivo de Exportação**, deixe os padrões selecionados. Verifique se a opção **Incluir todos os certificados no caminho de certificação, se possível** está selecionada. Em seguida, clique em **Próximo**. 
4. Na página **Segurança** , você deve proteger a chave privada. Se você optar por usar uma senha, não deixe de anotar ou lembrar da senha definida para esse certificado. Em seguida, clique em **Próximo**.
5. Em **Arquivo a ser Exportado**, use **Procurar** para encontrar a localização para a qual você deseja exportar o certificado. Em **Nome do arquivo**, dê um nome ao arquivo de certificado. Em seguida, clique em **Próximo**.
6. Clique em **Concluir** para exportar o certificado.   

## <a name="addresspool"></a>Parte 7 - Adicionar o pool de endereços de cliente
1. Quando o gateway de rede virtual tiver sido criado, navegue até a seção **Configurações** da folha do gateway de rede virtual. Na seção **Configurações**, clique em **Configuração Ponto a Site** para abrir a folha **Configuração**.
   
    ![Folha Ponto a Site](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configuration.png)
2. **Pool de endereços** é o pool de endereços IP do qual os clientes que se conectam receberão um endereço IP. Adicione o pool de endereços e então clique em **Salvar**.
   
    ![Pool de endereços do cliente](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/ipaddresspool.png)

## <a name="uploadfile"></a>Parte 8 - Carregar o arquivo .cer do certificado raiz
Depois que o gateway tiver sido criado, você poderá carregar o arquivo .cer para um certificado raiz confiável no Azure. Você pode carregar arquivos em até 20 certificados raiz. Você não carrega a chave privada do certificado raiz no Azure. Depois que o arquivo .cer for carregado, o Azure o utilizará para autenticar clientes que se conectam à rede virtual.

1. Os certificados são adicionados na folha de **Configuração Ponto a site** na seção **Certificado raiz**.  
2. Verifique se você exportou o certificado raiz como um arquivo x.509 (.cer) codificado em Base 64. Você precisa exportá-lo neste formato para poder abrir o certificado em um editor de texto.
3. Abra o certificado com um editor de texto, como o Bloco de Notas. Copie apenas a seção a seguir como uma linha contínua:
   
    ![Dados do certificado](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/copycert.png)

    > [!NOTE]
    > Ao copiar os dados do certificado, certifique-se de copiar o texto como uma linha contínua sem retornos de carro ou alimentações de linha. Talvez seja necessário modificar a exibição no editor de texto para 'Mostrar símbolo/Mostrar todos os caracteres' para ver os retornos de carro e alimentações de linha.                                                                                                                                                                            
    >
    >

4. Cole os dados do certificado no campo **Dados de Certificado Público**. **Nomeie** o certificado e, em seguida, clique em **Salvar**. Pode adicionar até 20 certificados raiz.
   
    ![Carregamento do certificado](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/rootcertupload.png)

## <a name="clientconfig"></a>Parte 9 - Baixar e instalar o pacote de configuração do cliente VPN
Os clientes que se conectam ao Azure usando P2S devem ter um certificado de cliente e um pacote de configuração de cliente VPN instalado. Pacotes de configuração de cliente VPN estão disponíveis para clientes do Windows. 

O pacote do cliente VPN contém informações de para configurar o software de cliente VPN integrado ao Windows. A configuração é específica da VPN à qual você deseja se conectar. O pacote não instala softwares adicionais.

Você pode usar o mesmo pacote de configuração de cliente VPN em cada computador cliente, desde que a versão corresponda à arquitetura do cliente.

### <a name="step-1---download-the-client-configuration-package"></a>Etapa 1- Baixar o pacote de configuração de cliente

1. Na folha **Configuração ponto a site** , clique em **Baixar cliente VPN** para abrir a folha **Baixar cliente VPN**. Demora um ou dois minutos para gerar o pacote.
   
    ![Download do cliente VPN 1](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/downloadvpnclient1.png)
2. Selecione o pacote correto para o cliente e clique em **Baixar**. Salve o arquivo de pacote de configuração. Você o instalará em cada computador cliente que se conectará à rede virtual.

    ![Download do cliente VPN 2](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/vpnclient.png)

### <a name="step-2---install-the-client-configuration-package"></a>Etapa 2 - instalar o pacote de configuração do cliente

1. Copie o arquivo de configuração localmente no computador que você deseja conectar à sua rede virtual. 
2. No computador cliente, clique duas vezes no arquivo .exe para instalar o pacote. Como você criou o pacote de configuração, ele não está assinado. Isso significa que você pode ver um aviso. Se aparecer um pop-up do SmartScreen do Windows, clique em **Mais informações** (à esquerda)e em **Executar mesmo assim** para instalar o pacote.
3. No computador cliente, navegue até **Configurações de Rede** e clique em **VPN**. Você verá a conexão listada. Ela mostra o nome da rede virtual a qual se conectará e terá uma aparência parecida com esta:
3. Instale o pacote no computador cliente. Se aparecer um pop-up do SmartScreen do Windows, clique em **Mais informações** (à esquerda)e em **Executar mesmo assim** para instalar o pacote.
4. No computador cliente, navegue até **Configurações de Rede** e clique em **VPN**. Você verá a conexão listada. Isso mostrará o nome da rede virtual a qual se conectará, e terá uma aparência parecida com esta: 
   
    ![Cliente VPN](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/vpn.png)


## <a name="installclientcert"></a>Parte 10 - Instalar um certificado de cliente exportado

Se você quiser criar uma conexão P2S de um computador cliente diferente daquele usada para gerar os certificados cliente, instale um certificado de cliente. Ao instalar um certificado do cliente, você precisará da senha criada durante a exportação do certificado do cliente. 

1. Localize e copie o arquivo *.pfx* no computador cliente. No computador cliente, clique duas vezes no arquivo *.pfx* para instalá-lo. Deixe **Localização do Repositório** como **Usuário Atual** e clique em **Avançar**.
2. Na página **Arquivo** a importar, não faça nenhuma alteração. Clique em **Próximo**.
3. Na página **Proteção da chave privada**, insira a senha do certificado, caso tenha usado uma, ou verifique se a entidade de segurança que está instalando o certificado está correta e clique em **Avançar**.
4. Na página **Repositório de Certificados**, deixe a localização padrão e clique em **Avançar**.
5. Clique em **Concluir**. No **Aviso de Segurança** da instalação do certificado, clique em **Sim**. Clique em 'Sim' sem medo, pois você gerou o certificado. O certificado foi importado com êxito.

## <a name="connect"></a>Parte 11 - Conectar-se ao Azure
1. Para se conectar à sua rede virtual, no computador cliente, navegue até conexões VPN e localize a conexão VPN que você criou. Ele terá o mesmo nome da sua rede virtual. Clique em **Conectar**. Uma mensagem pop-up pode ser exibida sobre o uso do certificado. Se isso acontecer, clique em **Continuar** para usar os privilégios elevados. 
2. Na página de status **Conexão**, clique em **Conectar** para iniciar a conexão. Se for exibida uma tela de **Selecionar certificado** , verifique se o certificado de cliente mostrado é o que você deseja usar para se conectar. Se não for, use a seta suspensa para selecionar o certificado correto e clique em **OK**.
   
    ![Cliente VPN conectando-se ao Azure](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/clientconnect.png)

    
3. Sua conexão já deve ter sido estabelecida.
   
    ![Cliente VPN conectado ao Azure](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/connected.png)
                                                                                                                                                                           

> [!NOTE]
> Se você estiver usando um certificado emitido com uma solução de AC corporativa e estiver com problemas na autenticação, verifique a ordem de autenticação no certificado do cliente. Você pode verificar a ordem da lista de autenticação ao clicar duas vezes no certificado do cliente e acessar **Detalhes > Uso Avançado de Chave**. Verifique se a lista mostra 'Autenticação de cliente' como o primeiro item. Caso contrário, é necessário emitir um certificado de cliente com base no modelo Usuário que tem a Autenticação de cliente como o primeiro item na lista. 
>
>

## <a name="verify"></a>Parte 12 - Verificar sua conexão
1. Para verificar se a conexão VPN está ativa, abra um prompt de comandos com privilégios elevados e execute *ipconfig/all*.
2. Exiba os resultados. Observe que o endereço IP que você recebeu está dentro do pool de endereços de cliente VPN Ponto a Site especificado em sua configuração. Os resultados devem ser algo semelhante a isto:
   
        PPP adapter VNet1:
            Connection-specific DNS Suffix .:
            Description.....................: VNet1
            Physical Address................:
            DHCP Enabled....................: No
            Autoconfiguration Enabled.......: Yes
            IPv4 Address....................: 172.16.201.3(Preferred)
            Subnet Mask.....................: 255.255.255.255
            Default Gateway.................:
            NetBIOS over Tcpip..............: Enabled

## <a name="add"></a>Adicionar ou remover certificados raiz confiáveis
Você pode adicionar e remover um certificado raiz do Azure. Quando você remove um certificado confiável, os certificados de cliente gerados desde o certificado raiz não será capaz de se conectar ao Azure por meio de Ponto a Site. Se desejar que os clientes se conectem, eles precisarão instalar um novo certificado de cliente gerado de um certificado confiável no Azure.

### <a name="to-add-a-trusted-root-certificate"></a>Para adicionar um certificado raiz confiável

Você pode adicionar até 20 arquivos .cer de certificado raiz ao Azure. Para obter instruções, consulte a seção [Carregar um certificado raiz confiável](#uploadfile) neste artigo.

### <a name="to-remove-a-trusted-root-certificate"></a>Para remover um certificado raiz confiável

1. Para remover um certificado raiz confiável, navegue até a folha **Configuração ponto a site** de seu gateway de rede virtual.
2. Na seção **Certificado raiz** da folha, localize o certificado que você deseja remover.
3. Clique no botão de reticências ao lado do certificado e clique em 'Remover'.

## <a name="revokeclient"></a>Revogar um certificado de cliente
É possível revogar certificados de cliente. A lista de certificados revogados permite que você negue seletivamente conectividade ponto a site com base em certificados de cliente individuais. Isso é diferente da remoção de um certificado raiz confiável. Se você remover um arquivo .cer de certificado raiz confiável do Azure, ele revogará o acesso para todos os certificados de cliente gerados/assinados pelo certificado raiz revogado. Revogar um certificado de cliente, em vez do certificado raiz, permite que os outros certificados gerados a partir do certificado raiz continuem a ser usados para autenticar a conexão ponto a site.

A prática comum é usar o certificado raiz para gerenciar o acesso em níveis de equipe ou organização, enquanto estiver usando certificados de cliente revogados para controle de acesso refinado em usuários individuais.

### <a name="to-revoke-a-client-certificate"></a>Para revogar um certificado de cliente

Você pode revogar um certificado de cliente adicionando a impressão digital à lista de revogação.

1. Recupere a impressão digital do certificado de cliente. Para saber mais, confira [Como recuperar a impressão digital de um certificado](https://msdn.microsoft.com/library/ms734695.aspx).
2. Copie as informações para um editor de texto e remova todos os espaços para que seja uma cadeia de caracteres contínua.
3. Navegue até a folha **Configuração ponto a site** do gateway de rede virtual. Esta é a folha que você usou para [carregar um certificado raiz confiável](#uploadfile).
4. Na seção **Certificados revogados**, insira um nome amigável para o certificado (não precisa ser o CN do certificado).
5. Copie e cole a cadeia de caracteres de impressão digital no campo **Impressão digital**.
6. A impressão digital será validada e automaticamente adicionada à lista de revogação. Você verá uma mensagem na tela informando que a lista está atualizando. 
7. Após a conclusão da atualização, o certificado não poderá mais ser usado para se conectar. Os clientes que tentam se conectar usando este certificado receberão uma mensagem informando que o certificado não é mais válido.

## <a name="faq"></a>Perguntas frequentes sobre Ponto a site

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>Próximas etapas
Quando sua conexão for concluída, você poderá adicionar máquinas virtuais às suas redes virtuais. Para saber mais, veja [Máquinas virtuais](https://docs.microsoft.com/azure/#pivot=services&panel=Compute). Para saber mais sobre redes e máquinas virtuais, consulte [Visão geral de rede do Azure e VM Linux](../virtual-machines/linux/azure-vm-network-overview.md).



