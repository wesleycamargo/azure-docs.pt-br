---
title: 'Conectar um computador a uma rede virtual do Azure usando o Ponto a Site: PowerShell | Microsoft Docs'
description: "Conectar com segurança um computador à Rede Virtual do Azure criando uma conexão de gateway de VPN Ponto a Site."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3eddadf6-2e96-48c4-87c6-52a146faeec6
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/17/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: cf72197aba2c6e6c7a51f96d1161cf1fbe88a0c5
ms.openlocfilehash: fe3bb0a5faee806e7956acba23c22b9aefd1f0a8
ms.lasthandoff: 02/18/2017


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-powershell"></a>Configurar uma conexão Ponto a Site com uma rede virtual usando PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager - Portal do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Portal do Azure - Clássico](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

Uma configuração Ponto a Site (P2S) permite que você crie uma conexão segura de um computador cliente individual com uma rede virtual. Uma conexão P2S é útil quando você deseja se conectar à sua rede virtual de um local remoto, como de casa ou de uma conferência, ou quando há apenas alguns clientes que precisam se conectar a uma rede virtual.

As conexões Ponto a Site não exigem um dispositivo VPN ou um endereço IP voltado para o público para funcionar. Uma conexão VPN é estabelecida por meio do início da conexão do computador cliente. Para saber mais sobre conexões Ponto a site, consulte as [Perguntas frequentes sobre Ponto a site](#faq) no final deste artigo. 

Este artigo orienta você durante a criação de uma rede virtual com uma conexão Ponto a Site no modelo de implantação do Resource Manager usando o portal o PowerShell.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Modelos e métodos de implantação para as conexões P2S
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

A tabela a seguir mostra os dois modelos de implantação e os métodos de implantação disponíveis para configurações de P2S. Quando houver um artigo com etapas de configuração disponível, o vincularemos diretamente desta tabela.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>Fluxo de trabalho básico
![Conectar um computador a um diagrama de conexão de ponto para Site da rede virtual do Azure](./media/vpn-gateway-howto-point-to-site-rm-ps/point-to-site-diagram.png)

Nesse cenário, você criará uma rede virtual com uma conexão Ponto a Site. As instruções também ajudarão você a gerar certificados, que são necessários para esta configuração. Uma conexão P2S é composta pelos itens a seguir: uma rede virtual com um gateway de VPN, um arquivo .cer do certificado raiz (chave pública), um certificado de cliente e a configuração de VPN no cliente. 

Nós usamos os seguintes valores para esta configuração. Definimos as variáveis na seção [1](#declare) do artigo. Você pode usar as etapas como um passo a passo e usar os valores sem alterá-los, ou alterá-los para refletir seu ambiente. 

### <a name="a-nameexampleaexample-values"></a><a name="example"></a>Valores de exemplo
* **Nome: VNet1**
* **Espaço de endereço: 192.168.0.0/16** e **10.254.0.0/16**<br>Neste exemplo, usamos mais de um espaço de endereço para ilustrar que esta configuração funciona com vários espaços de endereço. No entanto, vários espaços de endereço não são necessários para esta configuração.
* **Nome da sub-rede: FrontEnd**
  * **Intervalo de endereços da sub-rede: 192.168.1.0/24**
* **Nome da sub-rede: BackEnd**
  * **Intervalo de endereços da sub-rede: 10.254.1.0/24**
* **Nome da sub-rede: GatewaySubnet**<br>O nome da Sub-rede *GatewaySubnet* é obrigatório para que o gateway de VPN funcione.
  * **Intervalo de endereços da sub-rede: 192.168.200.0/24** 
* **Pool de endereços do cliente VPN: 172.16.201.0/24**<br>Os clientes VPN que se conectarem à rede virtual usando esta conexão Ponto a Site receberão um endereço IP do pool de endereços do cliente VPN.
* **Assinatura:** verifique se você tem mais de uma assinatura, verifique se está usando a correta.
* **Grupo de Recursos: TestRG**
* **Local: Leste dos EUA**
* **Servidor DNS: Endereço IP** do servidor DNS que você deseja usar para a resolução de nome.
* **Nome de GW: Vnet1GW**
* **Nome do IP público: VNet1GWPIP**
* **VpnType: RouteBased**

## <a name="before-beginning"></a>Antes de começar
* Verifique se você tem uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [Benefícios do assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se para obter uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial).
* Instale a versão mais recente dos cmdlets do PowerShell do Azure Resource Manager. Confira [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) para saber mais sobre como instalar os cmdlets do PowerShell. Ao trabalhar com o PowerShell para essa configuração, verifique se você está executando como administrador. 

## <a name="a-namedeclareapart-1---log-in-and-set-variables"></a><a name="declare"></a>Parte 1 - Faça logon e defina as variáveis
Nesta seção, faça logon e declare os valores usados para esta configuração. Os valores declarados são usados nos scripts de exemplo. Altere os valores para refletir seu próprio ambiente. Ou, você pode usar os valores declarados e percorrer as etapas como um exercício.

1. No console do PowerShell, faça logon na conta do Azure. O cmdlet solicita as credenciais de logon dessa conta. Depois de entrar, ele baixa as configurações da conta para que elas estejam disponíveis para o Azure PowerShell.
   
        Login-AzureRmAccount 
2. Obtenha uma lista das assinaturas do Azure.
   
        Get-AzureRmSubscription
3. Especifique a assinatura que você quer usar. 
   
        Select-AzureRmSubscription -SubscriptionName "Name of subscription"
4. Declare as variáveis que você quer usar. Use o exemplo a seguir, substituindo os valores existentes pelos seus quando necessário.
   
        $VNetName  = "VNet1"
        $FESubName = "FrontEnd"
        $BESubName = "Backend"
        $GWSubName = "GatewaySubnet"
        $VNetPrefix1 = "192.168.0.0/16"
        $VNetPrefix2 = "10.254.0.0/16"
        $FESubPrefix = "192.168.1.0/24"
        $BESubPrefix = "10.254.1.0/24"
        $GWSubPrefix = "192.168.200.0/26"
        $VPNClientAddressPool = "172.16.201.0/24"
        $RG = "TestRG"
        $Location = "East US"
        $DNS = "8.8.8.8"
        $GWName = "VNet1GW"
        $GWIPName = "VNet1GWPIP"
        $GWIPconfName = "gwipconf"

## <a name="a-nameconfigurevnetapart-2---configure-a-vnet"></a><a name="ConfigureVNet"></a>Parte 2 - Configurar uma rede virtual
1. Crie um grupos de recursos.
   
        New-AzureRmResourceGroup -Name $RG -Location $Location
2. Crie as configurações de sub-rede para a rede virtual e dê a elas os nomes *FrontEnd*, *BackEnd* e *GatewaySubnet*. Esses prefixos devem fazer parte do espaço de endereço da rede virtual declarada por você.
   
        $fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
        $besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
        $gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix
3. Crie a rede virtual. O servidor DNS especificado deve ser um servidor DNS que pode resolver os nomes dos recursos aos quais você está se conectando. Neste exemplo, usamos um endereço IP público. Use seus próprios valores.
   
        New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer $DNS
4. Especifique as variáveis da rede virtual que você criou.
   
        $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
5. Solicite um endereço IP público atribuído dinamicamente. Esse endereço IP é necessário para o gateway funcionar corretamente. Posteriormente, você conectará o gateway à configuração de IP do gateway.
   
        $pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
        $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip

## <a name="a-namecertificatesapart-3---certificates"></a><a name="Certificates"></a>Parte 3 - Certificados
Os certificados são usados pelo Azure para autenticar clientes de VPN para as VPNs Ponto a Site. Você exporta dados de certificado público (e não a chave privada) como um arquivo .cer X.509 codificado em Base&64; de um certificado raiz gerado por uma solução de certificado empresarial, ou um certificado raiz autoassinado. Em seguida, você importa os dados do certificado público do certificado raiz para o Azure. Além disso, você precisa gerar um certificado do cliente a partir do certificado raiz para os clientes. Cada cliente que deseja se conectar à rede virtual usando uma conexão P2S deve ter instalado um certificado do cliente gerado desde o certificado raiz.

### <a name="a-namecerastep-1---obtain-the-cer-file-for-the-root-certificate"></a><a name="cer"></a>Etapa 1 - Obter o arquivo .cer do certificado raiz
Você precisará obter os dados de certificado público para o certificado raiz que você deseja usar.

* Se você estiver usando um sistema de certificado corporativo, obtenha o arquivo .cer do certificado raiz que deseja usar. 
* Se você não estiver usando uma solução de certificado corporativo, será preciso gerar um certificado raiz autoassinado. O método recomendado para criar um certificado autoassinado para uso de P2S é makecert. Embora seja possível usar o PowerShell para criar certificados autoassinados, o certificado que é gerado usando o PowerShell não contém os campos necessários para conexões P2S. Para obter as etapas para o Windows 10, você pode conferir [Trabalhando com certificados raiz autoassinados para configurações de Ponto a Site](vpn-gateway-certificates-point-to-site.md).

1. Para obter um arquivo .cer de um certificado, abra **certmgr.msc** e localize o certificado raiz. Clique com o botão direito no certificado raiz autoassinado, clique em** todas as tarefas** e clique em **exportar**. Isso abre o **Assistente para Exportação de Certificados**.
2. No Assistente, clique em **Avançar**, escolha , **Não exportar a chave privada** e clique em **Avançar**.
3. Na página **Exportar Formato de Arquivo**, selecione **X.509 codificado em Base&64; (.CER).** Em seguida, clique em **Avançar**. 
4. Em **Arquivo a ser Exportado**, use **Procurar** para encontrar a localização para a qual você deseja exportar o certificado. Em **Nome do arquivo**, dê um nome ao arquivo de certificado. Em seguida, clique em **Próximo**.
5. Clique em **Concluir** para exportar o certificado.

### <a name="a-namegenerateastep-2---generate-the-client-certificate"></a><a name="generate"></a>Etapa 2 - Gerar o certificado de cliente
Em seguida, gere os certificados de cliente. Você pode gerar um certificado exclusivo para cada cliente que se conectará ou pode usar o mesmo certificado em vários clientes. A vantagem da geração de certificados de cliente exclusivos é a capacidade de revogar um único certificado, se necessário. Caso contrário, se todos estiverem usando o mesmo certificado de cliente e se for necessário revogar o certificado para um cliente, você precisará gerar e instalar novos certificados para todos os clientes que usam o certificado para autenticação. Os certificados do cliente são instalados em cada computador cliente posteriormente neste exercício.


####<a name="enterprise-certificate"></a>Certificado corporativo
- Se você estiver usando uma solução de certificado corporativo, gere um certificado de cliente com o formato de valor de nome comum 'name@yourdomain.com',, em vez do formato 'nome do domínio\nomedeusuário'.
- Verifique se o certificado do cliente que você emite baseia-se no modelo de certificado 'Usuário' que tem 'Autenticação de cliente' como o primeiro item na lista de uso, em vez de Logon de Cartão Inteligente, etc. Você pode verificar o certificado clicando duas vezes no certificado do cliente e exibindo **Detalhes > Uso Avançado de Chave**.

####<a name="self-signed-certificate"></a>Certificado autoassinado 
Se você estiver usando um certificado autoassinado, veja [Trabalhando com certificados raiz autoassinados para configurações Ponto a Site](vpn-gateway-certificates-point-to-site.md) para gerar um certificado de cliente.

### <a name="a-nameexportclientcertastep-3---export-the-client-certificate"></a><a name="exportclientcert"></a>Etapa 3 - Exportar o certificado de cliente
Um certificado de cliente é necessário para autenticação. Depois de gerar o certificado do cliente, exporte-o. O certificado do cliente exportado será instalado posteriormente em cada computador cliente.

1. Para exportar um certificado do cliente, use *certmgr.msc*. Clique com o botão direito no certificado do cliente que você deseja exportar, clique em **todas as tarefas** e, em seguida, clique em **exportar**.
2. Exporte o certificado de cliente com a chave privada. Este é um arquivo *.pfx* . Certifique-se de registrar ou se lembrar da senha (chave) que você definiu para esse certificado.

### <a name="a-nameuploadastep-4---upload-the-root-certificate-cer-file"></a><a name="upload"></a>Etapa 4 - Carregar o arquivo .cer do certificado raiz
Declare a variável para o nome do certificado, substituindo o valor pelo seu:

        $P2SRootCertName = "Mycertificatename.cer"

Adicione os dados do certificado público para o certificado raiz ao Azure. Você pode carregar arquivos em até 20 certificados raiz. Você não carrega a chave privada do certificado raiz no Azure. Depois que o arquivo .cer for carregado, o Azure o utilizará para autenticar clientes que se conectam à rede virtual. 

Substitua o caminho do arquivo pelo seu e execute os cmdlets.

        $filePathForCert = "C:\cert\Mycertificatename.cer"
        $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
        $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
        $p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64

## <a name="a-namecreategatewayapart-4---create-the-vpn-gateway"></a><a name="creategateway"></a>Parte 4 - Criar o gateway de VPN
Configurar e criar o gateway de rede virtual para sua rede virtual. O *-GatewayType* deve ser **Vpn** e o *-VpnType* deve ser **RouteBased**. Isso pode demorar até 45 minutos para ser concluído.

        New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
        -Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
        -VpnType RouteBased -EnableBgp $false -GatewaySku Standard `
        -VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## <a name="a-nameclientconfigapart-5---download-the-vpn-client-configuration-package"></a><a name="clientconfig"></a>Parte 5 - Baixar o pacote de configuração do cliente VPN
Clientes que se conectam ao Azure usando P2S devem ter um certificado de cliente e um pacote de configuração de cliente VPN instalado. Pacotes de configuração de cliente VPN estão disponíveis para clientes do Windows.

O pacote do cliente VPN contém informações de configuração para configurar o software de cliente VPN integrado ao Windows. O pacote não instala softwares adicionais. As configurações são específicas para a rede virtual a qual você deseja se conectar. Para obter a lista de sistemas operacionais clientes com suporte, consulte as [Perguntas frequentes sobre conexões Ponto a site](#faq) ao final desse artigo.

1. Depois que o gateway tiver sido criado, você poderá baixar o pacote de configuração do cliente. Este exemplo baixa o pacote para clientes de 64 bits. Se você quiser baixar o cliente de 32 bits, substitua 'Amd64' por 'x86'. Você também pode baixar o cliente VPN usando o portal do Azure.
   
        Get-AzureRmVpnClientPackage -ResourceGroupName $RG `
        -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64
2. O cmdlet do PowerShell retorna o link de uma URL. Este é um exemplo da aparência da URL retornada:
   
        "https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"
3. Copie e cole o link retornado em um navegador da Web para baixar o pacote. Em seguida, instale o pacote no computador cliente. Se você receber um pop-up do SmartScreen, clique em **Mais informações** e em **Executar mesmo assim** para instalar o pacote.
4. No computador cliente, navegue até **Configurações de Rede** e clique em **VPN**. Você verá a conexão listada. Isso mostrará o nome da rede virtual a qual se conectará, e terá uma aparência parecida com esta: 
   
    ![Cliente VPN](./media/vpn-gateway-howto-point-to-site-rm-ps/vpn.png)

## <a name="a-nameclientcertificateapart-6---install-the-client-certificate"></a><a name="clientcertificate"></a>Parte 6 – Instalar um certificado de cliente
Cada computador cliente deve ter um certificado do cliente para se autenticar. Ao instalar o certificado do cliente, você precisará da senha criada quando o certificado do cliente foi exportado.

1. Copie o arquivo .pfx para o computador cliente.
2. Clique duas vezes no arquivo .pfx para instalá-lo. Não modifique o local de instalação.

## <a name="a-nameconnectapart-7---connect-to-azure"></a><a name="connect"></a>Parte 7 - Conectar-se ao Azure
1. Para se conectar à sua rede virtual, no computador cliente, navegue até conexões VPN e localize a conexão VPN que você criou. Ele terá o mesmo nome da sua rede virtual. Clique em **Conectar**. Uma mensagem pop-up pode ser exibida sobre o uso do certificado. Se isso acontecer, clique em **Continuar** para usar os privilégios elevados. 
2. Na página de status **Conexão**, clique em **Conectar** para iniciar a conexão. Se for exibida uma tela de **Selecionar certificado** , verifique se o certificado de cliente mostrado é o que você deseja usar para se conectar. Se não for, use a seta suspensa para selecionar o certificado correto e clique em **OK**.
   
    ![Cliente VPN se conecta ao Azure](./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png)
3. Sua conexão já deve ter sido estabelecida.
   
    ![Conexão estabelecida](./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png)

> [!NOTE]
> Se você estiver usando um certificado emitido com uma solução de AC corporativa e estiver com problemas na autenticação, verifique a ordem de autenticação no certificado do cliente. Você pode verificar a ordem da lista de autenticação ao clicar duas vezes no certificado do cliente e acessar **Detalhes > Uso Avançado de Chave**. Verifique se a lista mostra 'Autenticação de cliente' como o primeiro item. Caso contrário, é necessário emitir um certificado de cliente com base no modelo Usuário que tem a Autenticação de cliente como o primeiro item na lista. 
>
>

## <a name="a-nameverifyapart-8---verify-your-connection"></a><a name="verify"></a>Parte 8 - Verificar sua conexão
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

## <a name="a-nameaddremovecertato-add-or-remove-a-trusted-root-certificate"></a><a name="addremovecert"></a>Para adicionar ou remover um certificado raiz confiável
Os certificados são usados para autenticar clientes VPN para VPNs ponto a site. As etapas a seguir explicam como adicionar e remover certificados raiz. Quando você adiciona um arquivo X.509 codificado de Base64 (. cer) ao Azure, está dizendo ao Azure para confiar no certificado raiz que o arquivo representa. 

Você pode adicionar ou remover certificados raiz confiáveis usando o PowerShell ou no Portal do Azure. Se você quiser fazer isso usando o Portal do Azure, acesse seu **gateway de rede virtual > configurações > Configuração ponto a site > Certificados de raiz**. As etapas a seguir mostrarão essas tarefas usando o PowerShell. 

### <a name="add-a-trusted-root-certificate"></a>Adicionar um certificado raiz confiável
Você pode adicionar até 20 arquivos .cer de certificado raiz ao Azure. Siga as etapas abaixo para adicionar um certificado raiz.

1. Crie e prepare o novo certificado raiz que você adicionará ao Azure. Exporte a chave pública como X.509 codificado em Base&64; (.CER) e abra-a com um editor de texto. Copie apenas a seção abaixo. 
   
    Copie os valores, conforme mostra o exemplo a seguir:
   
    ![certificado](./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png)

    > [!NOTE]
    > Ao copiar os dados do certificado, certifique-se de copiar o texto como uma linha contínua sem retornos de carro ou alimentações de linha. Talvez seja necessário modificar a exibição no editor de texto para 'Mostrar símbolo/Mostrar todos os caracteres' para ver os retornos de carro e alimentações de linha.                                                                                                                                                                            
    >


2. Especifique o nome do certificado e as informações de chave como uma variável. Substitua as informações por suas próprias, conforme mostrado no seguinte exemplo:
   
        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
3. Adicionar o novo certificado raiz. Você pode adicionar apenas um certificado por vez.
   
        Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2
4. Você pode verificar se o novo certificado foi adicionado corretamente usando o cmdlet a seguir.
   
        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "VNet1GW"

### <a name="to-remove-a-trusted-root-certificate"></a>Para remover um certificado raiz confiável
Você pode remover um certificado raiz do Azure. Quando você remove um certificado confiável, os certificados de cliente gerados desde o certificado raiz não será capaz de se conectar ao Azure por meio de Ponto a Site. Se desejar que os clientes se conectem, eles precisarão instalar um novo certificado de cliente gerado de um certificado confiável no Azure.

1. Para remover um certificado raiz confiável, modifique o exemplo a seguir:
   
    Declare as variáveis.
   
        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
2. Remova o certificado.
   
        Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
3. Use o cmdlet a seguir para verificar se o certificado foi removido com êxito. 
   
        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "VNet1GW"

## <a name="a-namerevokeato-manage-the-list-of-revoked-client-certificates"></a><a name="revoke"></a>Para gerenciar a lista de certificados de cliente revogados
É possível revogar certificados de cliente. A lista de certificados revogados permite que você negue seletivamente conectividade ponto a site com base em certificados de cliente individuais. Se você remover um arquivo .cer de certificado raiz do Azure, ele revogará o acesso para todos os certificados de cliente gerados/assinados pelo certificado raiz revogado. É possível revogar um certificado de cliente específico que não seja o raiz. Dessa forma, os outros certificados gerados desde o certificado raiz ainda serão válidos.

A prática comum é usar o certificado raiz para gerenciar o acesso em níveis de equipe ou organização, enquanto estiver usando certificados de cliente revogados para controle de acesso refinado em usuários individuais.

### <a name="revoke-a-client-certificate"></a>Revogar um certificado de cliente
1. Obtenha a impressão digital do certificado de cliente que será revogado.
   
        $RevokedClientCert1 = "ClientCert1"
        $RevokedThumbprint1 = "?ef2af033d0686820f5a3c74804d167b88b69982f"
2. Adicione a impressão digital à lista de impressão digital revogada.
   
        Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
        -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
3. Verifique se a impressão digital foi adicionada à lista de certificados revogados. Você deve adicionar uma impressão digital por vez.
   
        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

### <a name="reinstate-a-client-certificate"></a>Restabelecer um certificado de cliente
Você pode restabelecer um certificado de cliente removendo a impressão digital da lista de certificados de cliente revogados.

1. Remova a impressão digital da lista de impressão digital do certificado de cliente revogado.
   
       Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
       -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
2. Verifique se a impressão digital foi removida da lista revogada.
   
        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
## <a name="a-namefaqapoint-to-site-faq"></a><a name="faq"></a>Perguntas frequentes sobre Ponto a site

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>Próximas etapas
Quando sua conexão for concluída, você poderá adicionar máquinas virtuais às suas redes virtuais. Para saber mais, veja [Máquinas virtuais](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).


