<properties 
   pageTitle="Configurar uma conexão de Gateway VPN Ponto a Site para uma Rede Virtual do Azure| Microsoft Azure"
   description="Conecte-se com segurança à Rede Virtual do Azure criando uma conexão VPN ponto a site. Essa configuração é útil quando você precisa de uma conexão entre locais de um local remoto sem usar um dispositivo VPN e pode ser usada com as configurações de rede híbrida. Este artigo contém instruções do PowerShell para redes virtuais criadas usando o modelo de implantação do Gerenciador de Recursos."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/30/2016"
   ms.author="cherylmc" />

# Configurar uma conexão Ponto a Site com uma rede virtual usando o PowerShell

> [AZURE.SELECTOR]
- [PowerShell - Gerenciador de Recursos](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Portal - Clássico](vpn-gateway-point-to-site-create.md)

Uma configuração Ponto a Site permite que você crie uma conexão segura com sua rede virtual em um computador cliente, individualmente. Uma conexão VPN é estabelecida por meio do início da conexão do computador cliente. A conexão Ponto a Site é uma excelente solução quando você deseja se conectar à sua rede virtual de um local remoto, como de casa ou de uma conferência, ou quando você tiver apenas alguns clientes que precisam se conectar a uma rede virtual.

As conexões Ponto a Site não exigem um dispositivo VPN ou um endereço IP voltado para o público para funcionar. Para saber mais sobre as conexões Ponto a Site, consulte as [Perguntas frequentes sobre o Gateway de VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) e [Sobre conexões entre locais](vpn-gateway-cross-premises-options.md).

Este artigo se aplica a conexões de Gateway de VPN Ponto a Site para uma rede virtual criada usando o **Modelo de implantação do Resource Manager** (Service Management).

**Sobre modelos de implantação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

**Modelos de implantação e ferramentas para conexões Site a Site**

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

![Diagrama ponto a site](./media/vpn-gateway-point-to-site-create/point2site.png "point-to-site")


## Sobre esta configuração

Nesse cenário, você criará uma rede virtual com uma conexão Ponto a Site. Uma conexão Ponto a Site é composta pelos itens a seguir: rede virtual com um gateway, um arquivo .cer do certificado raiz carregado, um certificado de cliente e a configuração de VPN no lado do cliente.

Para esta configuração, usaremos os seguintes valores:

- Nome: **TestVNet**, usando os espaços de endereço **192.168.0.0/16** e **10.254.0.0/16**. Observe que você pode usar mais de um espaço de endereço para uma rede virtual.
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
	
- Você precisará instalar os cmdlets do Azure Resource Manager PowerShell (1.0.2 ou mais recente). Consulte [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para saber mais sobre como instalar os cmdlets do PowerShell.

## Configurar uma conexão Ponto a Site para o Azure

1. No console do PowerShell, faça logon na conta do Azure. O cmdlet solicita as credenciais de logon dessa conta. Depois de entrar, ele baixa as configurações da conta para que elas estejam disponíveis para o Azure PowerShell.

		Login-AzureRmAccount 

2. Obtenha uma lista das assinaturas do Azure.

		Get-AzureRmSubscription

3. Especifique a assinatura que você quer usar.

		Select-AzureRmSubscription -SubscriptionName "Name of subscription"

4. Nessa configuração, as seguintes variáveis do PowerShell são declaradas com os valores que você deseja usar. Os valores declarados serão usados nos scripts de exemplo. Declare os valores que você quer usar. Use o exemplo a seguir, substituindo os valores existentes pelos seus quando for necessário.

		$VNetName  = "TestVNet"
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

5. Criar um novo grupo de recursos.

		New-AzureRmResourceGroup -Name $RG -Location $Location

6. Crie as configurações de sub-rede para a rede virtual e dê a elas os nomes *FrontEnd*, *BackEnd* e *GatewaySubnet*. Observe que esses prefixos devem fazer parte do espaço de endereço da rede virtual declarada acima.

		$fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
		$besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
		$gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix

7. Crie a rede virtual. Observe que o servidor DNS especificado deve ser um servidor DNS que pode resolver os nomes dos recursos aos quais você está se conectando. Neste exemplo, usamos um endereço IP público, mas convém colocar seus próprios valores aqui.

		New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer $DNS

8. Especifique as variáveis da rede virtual que você acabou de criar.

		$vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet

9. Solicite um endereço IP público atribuído dinamicamente. Esse endereço IP é necessário para o gateway funcionar corretamente. Posteriormente, você conectará o gateway à configuração de IP do gateway.
		
		$pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
		$ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
		
10. Carregue o arquivo .cer do certificado raiz no Azure. Você pode usar um certificado raiz de seu ambiente de certificação corporativa ou pode usar um certificado raiz autoassinado. É possível carregar até 20 certificados raiz. Para obter instruções sobre como criar um certificado raiz autoassinado usando *makecert*, consulte [Trabalhando com certificados raiz autoassinados para configurações Ponto a Site](vpn-gateway-certificates-point-to-site.md). Observe que o arquivo .cer não deve conter a chave privada do certificado raiz.
	
	Veja abaixo um exemplo da aparência do arquivo. A parte difícil de carregar os dados do certificado público é que você deve copiar e colar toda a cadeia de caracteres sem espaços. Caso contrário, o carregamento não funcionará. Você precisará usar seu próprio arquivo .cer do certificado para esta etapa. Não tente copiar e colar o exemplo abaixo.

		$MyP2SRootCertPubKeyBase64 = "MIIDUzCCAj+gAwIBAgIQRggGmrpGj4pCblTanQRNUjAJBgUrDgMCHQUAMDQxEjAQBgNVBAoTCU1pY3Jvc29mdDEeMBwGA1UEAxMVQnJrIExpdGUgVGVzdCBSb290IENBMB4XDTEzMDExOTAwMjQxOFoXDTIxMDExOTAwMjQxN1owNDESMBAGA1UEChMJTWljcm9zb2Z0MR4wHAYDVQQDExVCcmsgTGl0ZSBUZXN0IFJvb3QgQ0EwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQC7SmE+iPULK0Rs7mQBO/6a6B6/G9BaMxHgDGzAmSG0Qsyt5e08aqgFnPdkMl3zRJw3lPKGha/JCvHRNrO8UpeAfc4IXWaqxx2iBipHjwmHPHh7+VB8lU0EJcUe7WBAI2n/sgfCwc+xKtuyRVlOhT6qw/nAi8e5don/iHPU6q7GCcnqoqtceQ/pJ8m66cvAnxwJlBFOTninhb2VjtvOfMQ07zPP+ZuYDPxvX5v3nd6yDa98yW4dZPuiGO2s6zJAfOPT2BrtyvLekItnSgAw3U5C0bOb+8XVKaDZQXbGEtOw6NZvD4L2yLd47nGkN2QXloiPLGyetrj3Z2pZYcrZBo8hAgMBAAGjaTBnMGUGA1UdAQReMFyAEOncRAPNcvJDoe4WP/gH2U+hNjA0MRIwEAYDVQQKEwlNaWNyb3NvZnQxHjAcBgNVBAMTFUJyayBMaXRlIFRlc3QgUm9vdCBDQYIQRggGmrpGj4pCblTanQRNUjAJBgUrDgMCHQUAA4IBAQCGyHhMdygS0g2tEUtRT4KFM+qqUY5HBpbIXNAav1a1dmXpHQCziuuxxzu3iq4XwnWUF1OabdDE2cpxNDOWxSsIxfEBf9ifaoz/O1ToJ0K757q2Rm2NWqQ7bNN8ArhvkNWa95S9gk9ZHZLUcjqanf0F8taJCYgzcbUSp+VBe9DcN89sJpYvfiBiAsMVqGPc/fHJgTScK+8QYrTRMubtFmXHbzBSO/KTAP5rBTxse88EGjK5F8wcedvge2Ksk6XjL3sZ19+Oj8KTQ72wihN900p1WQldHrrnbixSpmHBXbHr9U0NQigrJp5NphfuU5j81C8ixvfUdwyLmTv7rNA7GTAD"
		$p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $MyP2SRootCertPubKeyBase64

11. Crie o gateway de rede virtual para sua rede virtual. O GatewayType deve ser Vpn e o VpnType deve ser RouteBased.

		New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Vpn -VpnType RouteBased -EnableBgp $false -GatewaySku Standard -VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## Configuração do cliente

Cada cliente que se conecta ao Azure usando o Ponto a Site deve ter duas coisas: o cliente VPN deve estar configurado para se conectar e o cliente deve ter um certificado de cliente instalado. Pacotes de configuração de cliente VPN estão disponíveis para clientes do Windows. Consulte as [Perguntas frequentes sobre o Gateway de VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) para obter mais informações.

1. Baixe o pacote de configuração de cliente VPN. Nesta etapa, use o exemplo a seguir para baixar o pacote de configuração do cliente.

		Get-AzureRmVpnClientPackage -ResourceGroupName $RG -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64

	O cmdlet do PowerShell retornará o link de uma URL. Copie e cole o link retornado em um navegador da Web para baixar o pacote para seu computador. Veja abaixo um exemplo da aparência da URL retornada.

    	"https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"
	
2. Gere e instale os certificados de cliente (*.pfx) criados a partir do certificado raiz nos computadores cliente. Você pode usar qualquer método de instalação com o qual esteja familiarizado. Se estiver usando um certificado raiz autoassinado e não souber como fazer isso, consulte [Trabalhando com certificados raiz autoassinados para configurações Ponto a Site](vpn-gateway-certificates-point-to-site.md).

3. Para se conectar à sua rede virtual, no computador cliente, navegue até conexões VPN e localize a conexão VPN que você acabou de criar. Ele será nomeado com o mesmo nome da sua rede virtual. Clique em **Conectar**. Uma mensagem pop-up pode ser exibida sobre o uso do certificado. Se isso acontecer, clique em **Continuar** para usar os privilégios elevados.

4. Na página de status da **Conexão**, clique em **Conectar** para iniciar a conexão. Se for exibida uma tela de **Selecionar certificado**, verifique se o certificado de cliente mostrado é o que você deseja usar para se conectar. Se não for, use a seta suspensa para selecionar o certificado correto e clique em **OK**.

5. Sua conexão já deve ter sido estabelecida. Você pode verificar a conexão usando o procedimento abaixo.

## Verificar a conexão

1. Para verificar se a conexão VPN está ativa, abra um prompt de comandos com privilégios elevados e execute *ipconfig/all*.

2. Exiba os resultados. Observe que o endereço IP que você recebeu está dentro do pool de endereços de cliente VPN Ponto a Site especificado em sua configuração. Os resultados devem ser algo semelhante a isto:
    
		PPP adapter VNetEast:
			Connection-specific DNS Suffix .:
			Description.....................: VNetEast
			Physical Address................:
			DHCP Enabled....................: No
			Autoconfiguration Enabled.......: Yes
			IPv4 Address....................: 172.16.201.3(Preferred)
			Subnet Mask.....................: 255.255.255.255
			Default Gateway.................:
			NetBIOS over Tcpip..............: Enabled

## Para adicionar ou remover um certificado raiz

Os certificados são usados para autenticar clientes VPN para VPNs ponto a site. As etapas a seguir explicam como adicionar e remover certificados raiz.

### Adicionar um certificado raiz

É possível adicionar até 20 certificados raiz ao Azure. Siga as etapas abaixo para adicionar um certificado raiz.

1. Criar e preparar o novo certificado raiz para carregar.

		$P2SRootCertName2 = "ARMP2SRootCert2.cer"
		$MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"

2. Carregar o novo certificado raiz. Observe que você pode adicionar apenas um certificado raiz por vez.

		Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2

3. Você pode verificar se o novo certificado foi adicionado corretamente usando o cmdlet a seguir.

		Get-AzureRmVpnClientRootCertificate -ResourceGroupName $RG -VirtualNetworkGatewayName $GWName

### Remover um certificado raiz

Pode-se remover um certificado raiz do Azure. Quando você remove um certificado raiz, os certificados de cliente que foram gerados do certificado raiz não serão mais capazes de se conectar ao Azure por ponto a site até instalarem um certificado de cliente que seja gerado de um certificado raiz válido no Azure.

1. Remova um certificado raiz.

		Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"

 
2. Use o cmdlet a seguir para verificar se o certificado foi removido com êxito.

		Get-AzureRmVpnClientRootCertificate -ResourceGroupName $RG -VirtualNetworkGatewayName $GWName

## Gerenciar a lista de certificados de cliente revogados

É possível revogar certificados de cliente. A lista de certificados revogados permite que você negue seletivamente conectividade ponto a site com base em certificados de cliente individuais. Embora a remoção de um certificado raiz do Azure revogue o acesso a todos os certificados de cliente gerados/assinados pelo certificado revogado raiz, revogar um certificado de cliente permitirá que você remova o acesso para um certificado específico. A prática comum é usar o certificado raiz para gerenciar o acesso em níveis de equipe ou organização, enquanto estiver usando certificados de cliente revogados para controle de acesso refinado em usuários individuais.

### Revogar um certificado de cliente

1. Obtenha a impressão digital do certificado de cliente que será revogado.

		$RevokedClientCert1 = "ClientCert1"
		$RevokedThumbprint1 = "‎ef2af033d0686820f5a3c74804d167b88b69982f"

2. Adicione a impressão digital à lista de impressão digital revogada.

		Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

3. Verifique se a impressão digital foi adicionada à lista de certificados revogados. Você precisa adicionar uma impressão digital por vez.

		Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

### Restabelecer um certificado de cliente

Você pode restabelecer um certificado de cliente removendo a impressão digital da lista de certificados de cliente revogados.

1.  Remova a impressão digital da lista de impressão digital do certificado de cliente revogado.

		Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

2. Verifique se a impressão digital foi removida da lista revogada.

		Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

## Próximas etapas

Você pode adicionar uma máquina virtual à rede virtual. Veja [Criar uma máquina virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para obter as etapas.

<!---HONumber=AcomDC_0427_2016-->