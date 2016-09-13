<properties 
   pageTitle="Configurar uma conexão de gateway VPN Ponto a Site com a rede virtual usando o modelo de implantação do Resource Manager | Microsoft Azure"
   description="Conecte com segurança a Rede Virtual do Azure criando uma conexão de gateway VPN Ponto a Site."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="cherylmc" />

# Configurar uma conexão Ponto a Site com uma rede virtual usando PowerShell

> [AZURE.SELECTOR]
- [PowerShell – Resource Manager](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Portal - Clássico](vpn-gateway-point-to-site-create.md)

Uma configuração Ponto a Site (P2S) permite que você crie uma conexão segura de um computador cliente individual com uma rede virtual. Uma conexão P2S é útil quando você deseja se conectar à sua rede virtual de um local remoto, como de casa ou de uma conferência, ou quando há apenas alguns clientes que precisam se conectar a uma rede virtual.

Este artigo fornece uma orientação pela criação de uma rede virtual com uma conexão Ponto a Site no **Modelo de implantação do Resource Manager**. As etapas exigem o PowerShell. No momento, não é possível criar totalmente essa solução usando o Portal do Azure.

As conexões Ponto a Site não exigem um dispositivo VPN ou um endereço IP voltado para o público para funcionar. Uma conexão VPN é estabelecida por meio do início da conexão do computador cliente. Para saber mais sobre as conexões Ponto a Site, consulte as [Perguntas frequentes sobre o Gateway de VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) e [Planejamento e design](vpn-gateway-plan-design.md).



**Sobre modelos de implantação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

**Modelos de implantação e ferramentas para conexões Site a Site**

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

![Diagrama ponto a site](./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "point-to-site")


## Sobre esta configuração

Nesse cenário, você criará uma rede virtual com uma conexão Ponto a Site. As instruções também ajudarão você a gerar certificados, que são necessários para esta configuração. Uma conexão P2S é composta pelos itens a seguir: uma rede virtual com um gateway de VPN, um arquivo .cer do certificado raiz (chave pública), um certificado de cliente e a configuração de VPN no cliente.

Nós usamos os seguintes valores para esta configuração. Definimos as variáveis na seção [1](#declare) do artigo. Você pode usar as etapas como um passo a passo e usar os valores sem alterá-los, ou alterá-los para refletir seu ambiente.

- Nome: **VNet1**, usando os espaços de endereço **192.168.0.0/16** e **10.254.0.0/16**. Observe que você pode usar mais de um espaço de endereço para uma rede virtual.
- Nome da sub-rede: **FrontEnd**, usando **192.168.1.0/24**
- Nome da sub-rede: **BackEnd**, usando **10.254.1.0/24**
- Nome da sub-rede: **GatewaySubnet**, usando **192.168.200.0/24**. O nome da Sub-rede *GatewaySubnet* é obrigatório para que o gateway funcione.
- Pool de endereços do cliente do VPN: **172.16.201.0/24**. Os clientes VPN que se conectarem à rede virtual usando esta conexão Ponto a Site receberão um endereço IP desse pool.
- Assinatura: Verifique se é a assinatura correta, caso você tenha mais de uma.
- Grupo de Recursos: **TestRG**
- Local: **Leste dos EUA**
- Servidor DNS: **Endereço IP** do servidor DNS que você deseja usar para a resolução de nome.
- Nome de GW: **GW**
- Nome do IP público: **GWIP**
- VpnType: **RouteBased**


## Antes de começar

- Verifique se você tem uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [Benefícios do assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para obter uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
	
- Instale os cmdlets do PowerShell do Azure Resource Manager (1.0.2 ou mais recente). Confira [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para saber mais sobre como instalar os cmdlets do PowerShell. Ao trabalhar com o PowerShell para essa configuração, verifique se você está executando como administrador.

## <a name="declare"></a>Parte 1 - Faça logon e defina as variáveis

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
		$GWName = "GW"
		$GWIPName = "GWIP"
		$GWIPconfName = "gwipconf"
    	$P2SRootCertName = "ARMP2SRootCert.cer"


## Parte 2 - Configurar uma VNet 


1. Crie um grupos de recursos.

		New-AzureRmResourceGroup -Name $RG -Location $Location

2. Crie as configurações de sub-rede para a rede virtual e dê a elas os nomes *FrontEnd*, *BackEnd* e *GatewaySubnet*. Esses prefixos devem fazer parte do espaço de endereço da rede virtual declarada acima.

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

## Parte 3 - Adicionar certificados confiáveis

O Azure autentica clientes que desejam se conectar por meio de P2S usando certificados. Você pode importar o arquivo .cer (a chave pública) para um certificado raiz no Azure. Quando você adiciona um arquivo X.509 codificado de Base64 (. cer) ao Azure, está dizendo ao Azure para confiar no certificado raiz que o arquivo representa.

Se você estiver usando uma solução empresarial, poderá usar a cadeia de certificados existente. Se você não estiver usando uma solução de autoridade de certificação corporativa, poderá criar um certificado autoassinado. Um método para criar um certificado autoassinado é o makecert. Para obter instruções sobre como criar um certificado raiz autoassinado usando *makecert*, consulte [Trabalhando com certificados raiz autoassinados para configurações Ponto a Site](vpn-gateway-certificates-point-to-site.md).

Não importa como você obtém o certificado, carregue o arquivo .cer do certificado no Azure e gere certificados de cliente para instalar nos clientes aos quais você deseja se conectar. Não instale o certificado autoassinado diretamente no cliente. Você pode gerar certificados de cliente mais adiante, na seção [Configuração do Cliente](#cc) deste artigo.
		
Você pode adicionar até 20 certificados raiz ao Azure. Para obter a chave pública, exporte o certificado como um arquivo x. 509 codificado de Base64 (.cer). Uma maneira de exportar o arquivo cer. é abrindo **certmger.msc**, localizando o certificado em Pessoal/Certificados. Clique com o botão direito e exporte sem a chave privada como "X.509 codificado na Base 64 (.CER)". Anote o caminho do arquivo usado para exportar para o arquivo. cer. Este é um exemplo de como obter a representação de cadeia de caracteres de Base64 do certificado.

Adicione o certificado confiável ao Azure. Você precisa usar seu próprio arquivo .cer do certificado para esta etapa. Preste atenção especial na variável $P2SRootCertName = "ARMP2SRootCert.cer" que você definiu na Parte 1 deste artigo. Se o nome do seu certificado for diferente, ajuste a variável adequadamente.
    
		$filePathForCert = "pasteYourCerFilePathHere"
		$cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
		$CertBase64 = [system.convert]::ToBase64String($cert.RawData)
		$p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64

## Parte 4 - Criar o gateway de VPN

Configurar e criar o gateway de rede virtual para sua rede virtual. O *GatewayType* deve ser **Vpn** e o *VpnType* deve ser **RouteBased**. Isso pode demorar até 45 minutos para ser concluído.

		New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
		-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
		-VpnType RouteBased -EnableBgp $false -GatewaySku Standard `
		-VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## Parte 5 - Baixar o pacote de configuração do cliente VPN

Clientes que se conectam ao Azure usando P2S devem ter um certificado de cliente e um pacote de configuração de cliente VPN instalado. Pacotes de configuração de cliente VPN estão disponíveis para clientes do Windows. O pacote do cliente VPN contém informações para configurar o software do cliente VPN que é incorporado ao Windows e é específico à VPN a qual você deseja se conectar. O pacote não instala softwares adicionais. Confira as [Perguntas frequentes sobre o Gateway de VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) para saber mais.

1. Após a criação do gateway, use o exemplo a seguir para baixar o pacote de configuração do cliente:

		Get-AzureRmVpnClientPackage -ResourceGroupName $RG `
		-VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64

2. O cmdlet do PowerShell retorna o link de uma URL. Este é um exemplo da aparência da URL retornada:

    	"https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"

3. Copie e cole o link retornado em um navegador da Web para baixar o pacote. Em seguida, instale o pacote no computador cliente.

4. No computador cliente, navegue até **Configurações de Rede** e clique em **VPN**. Você verá a conexão listada. Ela mostrará o nome da rede virtual a qual se conectará e terá uma aparência parecida com esta:

	![Cliente VPN](./media/vpn-gateway-howto-point-to-site-rm-ps/vpn.png "Cliente VPN")

## <a name="cc"></a>Parte 6: Gerar um certificado de cliente

Em seguida, gere os certificados de cliente. Você pode gerar um certificado exclusivo para cada cliente que se conectará ou pode usar o mesmo certificado em vários clientes. A vantagem da geração de certificados de cliente exclusivos é a capacidade de revogar um único certificado, se necessário. Caso contrário, se todos estiverem usando o mesmo certificado de cliente e se for necessário revogar o certificado para um cliente, você precisará gerar e instalar novos certificados para todos os clientes que usam o certificado para autenticação.

- Se você estiver usando uma solução de certificado corporativo, gere um certificado de cliente com o formato de valor de nome comum 'nome@seudomínio.com.br', em vez do formato NetBIOS 'DOMÍNIO\\nomedeusuário'.

- Se você estiver usando um certificado autoassinado, veja [Trabalhando com certificados raiz autoassinados para configurações Ponto a Site](vpn-gateway-certificates-point-to-site.md) para gerar um certificado de cliente.

## Parte 7: Instalar um certificado de cliente

Instale um certificado de cliente em cada computador que você queira conectar à rede virtual. Um certificado de cliente é necessário para autenticação. Você pode automatizar a instalação do certificado de cliente ou pode instalar manualmente. As etapas a seguir guiarão você pela instalação manual do certificado de cliente.

1. Para exportar um certificado do cliente, use *certmgr.msc*. Clique com o botão direito do mouse no certificado de cliente que você deseja exportar, clique em **todas as tarefas** e clique em **exportar**.
2. Exporte o certificado de cliente com a chave privada. Este é um arquivo *.pfx*. Certifique-se de registrar ou se lembrar da senha (chave) que você definiu para esse certificado.
3. Copie o arquivo *.pfx* para o computador cliente. No computador cliente, clique duas vezes no arquivo *.pfx* para instalá-lo. Digite a senha quando solicitado. Não modifique o local de instalação.


## Parte 8: Conectar-se ao Azure

1. Para se conectar à sua rede virtual, no computador cliente, navegue até conexões VPN e localize a conexão VPN que você criou. Ele terá o mesmo nome da sua rede virtual. Clique em **Conectar**. Uma mensagem pop-up pode ser exibida sobre o uso do certificado. Se isso acontecer, clique em **Continuar** para usar os privilégios elevados.

2. Na página de status da **Conexão**, clique em **Conectar** para iniciar a conexão. Se for exibida uma tela de **Selecionar certificado**, verifique se o certificado de cliente mostrado é o que você deseja usar para se conectar. Se não for, use a seta suspensa para selecionar o certificado correto e clique em **OK**.

	![Cliente VPN 2](./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png "Conexão de cliente VPN")

3. Sua conexão já deve ter sido estabelecida.

	![Cliente VPN 3](./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png "Conexão de cliente VPN 2")

## Parte 9: Verificar sua conexão

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

## Para adicionar ou remover um certificado raiz confiável

Os certificados são usados para autenticar clientes VPN para VPNs ponto a site. As etapas a seguir explicam como adicionar e remover certificados raiz. Quando você adiciona um arquivo X.509 codificado de Base64 (. cer) ao Azure, está dizendo ao Azure para confiar no certificado raiz que o arquivo representa.

Você pode adicionar ou remover certificados raiz confiáveis usando o PowerShell ou no Portal do Azure. Se você quiser fazer isso usando o Portal do Azure, acesse **gateway de rede virtual > configurações > configuração ponto a site > certificados raiz**. As etapas a seguir mostrarão essas tarefas usando o PowerShell.

### Adicionar um certificado raiz confiável

Você pode adicionar até 20 arquivos .cer de certificado raiz ao Azure. Siga as etapas abaixo para adicionar um certificado raiz.

1. Crie e prepare o novo certificado raiz que você adicionará ao Azure. Exporte a chave pública como X.509 codificado em Base 64 (.CER) e abra-a com um editor de texto. Copie apenas a seção abaixo.
 
	Copie os valores, conforme mostra o exemplo a seguir.

	![certificado](./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png "certificado")
	
2. No exemplo a seguir, especifique o nome do certificado e as informações de chave como uma variável. Substitua as informações por suas próprias.

		$P2SRootCertName2 = "ARMP2SRootCert2.cer"
		$MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"


3. Adicionar o novo certificado raiz. Você pode adicionar apenas um certificado por vez.

		Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2


4. Você pode verificar se o novo certificado foi adicionado corretamente usando o cmdlet a seguir.

		Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
		-VirtualNetworkGatewayName "GW"

### Para remover um certificado raiz confiável

Você pode remover um certificado raiz do Azure. Quando você remove um certificado confiável, os certificados de cliente gerados desde o certificado raiz não será capaz de se conectar ao Azure por meio de Ponto a Site. Se desejar que os clientes se conectem, eles precisarão instalar um novo certificado de cliente gerado de um certificado confiável no Azure.

1. Para remover um certificado raiz confiável, modifique o exemplo a seguir:

	Declare as variáveis.

		$P2SRootCertName2 = "ARMP2SRootCert2.cer"
		$MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"

2. Remover o certificado

		Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
 
3. Use o cmdlet a seguir para verificar se o certificado foi removido com êxito.

		Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
		-VirtualNetworkGatewayName "GW"

## Para gerenciar a lista de certificados de cliente revogados

É possível revogar certificados de cliente. A lista de certificados revogados permite que você negue seletivamente conectividade ponto a site com base em certificados de cliente individuais. Se você remover um arquivo .cer de certificado raiz do Azure, ele revogará o acesso para todos os certificados de cliente gerados/assinados pelo certificado raiz revogado. É possível revogar um certificado de cliente específico que não seja o raiz. Dessa forma, os outros certificados gerados desde o certificado raiz ainda serão válidos. A prática comum é usar o certificado raiz para gerenciar o acesso em níveis de equipe ou organização, enquanto estiver usando certificados de cliente revogados para controle de acesso refinado em usuários individuais.

### Revogar um certificado de cliente

1. Obtenha a impressão digital do certificado de cliente que será revogado.

		$RevokedClientCert1 = "ClientCert1"
		$RevokedThumbprint1 = "‎ef2af033d0686820f5a3c74804d167b88b69982f"

2. Adicione a impressão digital à lista de impressão digital revogada.

		Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
		-VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

3. Verifique se a impressão digital foi adicionada à lista de certificados revogados. Você deve adicionar uma impressão digital por vez.

		Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

### Restabelecer um certificado de cliente

Você pode restabelecer um certificado de cliente removendo a impressão digital da lista de certificados de cliente revogados.

1.  Remova a impressão digital da lista de impressão digital do certificado de cliente revogado.

		Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
		-VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

2. Verifique se a impressão digital foi removida da lista revogada.

		Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

## Próximas etapas

Você pode adicionar uma máquina virtual à rede virtual. Veja [Criar uma máquina virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para obter as etapas.

<!---HONumber=AcomDC_0907_2016-->