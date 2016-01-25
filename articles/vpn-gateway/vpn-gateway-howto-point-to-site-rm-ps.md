<properties 
   pageTitle="Configurar uma conexão de Gateway VPN Ponto a Site para uma Rede Virtual do Azure| Microsoft Azure"
   description="Conecte-se com segurança à Rede Virtual do Azure criando uma conexão VPN ponto a site. Essa configuração será útil quando você precisar de uma conexão entre locais de um local remoto sem usar um dispositivo VPN e pode ser usada com as configurações de rede híbrida. Este artigo contém instruções do PowerShell para redes virtuais criadas usando o modelo de implantação do Gerenciador de Recursos."
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
   ms.date="01/11/2016"
   ms.author="cherylmc" />

# Configurar uma conexão Ponto a Site com uma rede virtual usando o PowerShell

> [AZURE.SELECTOR]
- [PowerShell - Resource Manager](vpn-gateway-howto-point-to-site-rm-ps.md)
- [PowerShell - Classic](vpn-gateway-point-to-site-create.md)

Uma configuração Ponto a Site permite que você crie uma conexão segura com sua rede virtual em um computador cliente, individualmente. Uma conexão VPN é estabelecida por meio do início da conexão do computador cliente. A conexão Ponto a Site é uma excelente solução quando você deseja se conectar à sua rede virtual de um local remoto, como de casa ou de uma conferência, ou quando você tiver apenas alguns clientes que precisam se conectar a uma rede virtual. As conexões Ponto a Site não exigem um dispositivo VPN ou um endereço IP voltado para o público para funcionar. Para saber mais sobre as conexões Ponto a Site, consulte as [Perguntas frequentes sobre o Gateway de VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) e [Sobre conexões entre locais](vpn-gateway-cross-premises-options.md).

Este artigo se aplica aos Gateways de Redes Virtuais e de VPN criados com o modelo de implantação do **Gerenciador de Recursos do Azure**.\\ Caso você pretenda configurar a conexão Ponto a Site para uma rede virtual criada usando o Gerenciamento de Serviços (também conhecido como modelo de implantação clássico), confira [este artigo](vpn-gateway-point-to-site-create.md).

[AZURE.INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Sobre esta configuração

Nesse cenário, você criará uma rede virtual com uma conexão Ponto a Site. Uma conexão Ponto a Site é composta pelos itens a seguir: rede virtual com um gateway, um arquivo .cer do certificado raiz carregado, um certificado de cliente e a configuração de VPN no lado do cliente.

Para esta configuração, usaremos os seguintes valores:

- Nome = **TestVNet**, usando os espaços de endereço 192.168.0.0/16 e 10.254.0.0/16. Observe que você pode usar mais de um espaço de endereço para uma rede virtual.
- Nome da sub-rede = **FrontEnd**, usando 192.168.1.0/24
- Nome da sub-rede = **BackEnd**, usando 10.254.1.0/24
- Nome da sub-rede = **GatewaySubnet**, usando 192.168.200.0/24. O nome da sub-rede *GatewaySubnet* é obrigatório para o gateway funcionar. 
- Pool de endereços de cliente VPN: 172.16.201.0/24. Os clientes VPN que se conectarem à rede virtual usando esta conexão Ponto a Site receberão um endereço IP desse pool.
- Assinatura = verifique se é a assinatura correta, caso você tenha mais de uma.
- Grupo de Recursos = **TestRG**
- Local = **Leste dos EUA**
- Servidor DNS = Endereço IP do servidor DNS que você deseja usar para resolução de nome.
- Nome de GW = **GW**
- Nome do IP público = **GWIP**
- VpnType = **RouteBased**


## Antes de começar

Verifique se você tem uma assinatura do Azure e se instalou os cmdlets do Azure PowerShell necessários para esta configuração (1.0.2 ou posterior). Se ainda não tiver uma assinatura do Azure, você poderá ativar os [benefícios de assinante do MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para uma [avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/).
	
**Sobre a instalação dos módulos de cmdlet do PowerShell**

	[AZURE.INCLUDE [vpn-gateway-ps-rm-howto](../../includes/vpn-gateway-ps-rm-howto-include.md)]

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

6. Crie as configurações de sub-rede para a rede virtual e dê a eles os nomes *FrontEnd*, *BackEnd* e *GatewaySubnet*. Observe que esses prefixos devem fazer parte do espaço de endereço da rede virtual declarada acima.

		$fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
		$besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
		$gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix

7. Crie a rede virtual. Observe que o servidor DNS especificado deve ser um servidor DNS que pode resolver os nomes dos recursos aos quais você está se conectando. Neste exemplo, usamos um endereço IP público, mas convém colocar seus próprios valores aqui.

		New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer $DNS

8. Especifique as variáveis da rede virtual que você acabou de criar.

		$vnet   = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet

9. Solicite um endereço IP público atribuído dinamicamente. Esse endereço IP é necessário para o gateway funcionar corretamente. Posteriormente, você conectará o gateway à configuração de IP do gateway.
		
		$pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
		$ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
		
10. Carregue o arquivo .cer do certificado raiz no Azure. Você pode usar um certificado raiz de seu ambiente de certificação corporativa ou pode usar um certificado raiz autoassinado. É possível carregar até 20 certificados raiz. Para obter instruções sobre a criação de um certificado raiz autoassinado usando *makecert*, consulte a seção no final deste artigo. Observe que o arquivo .cer não deve conter a chave privada do certificado raiz.
	
	Veja abaixo um exemplo da aparência do arquivo. A parte difícil de carregar os dados do certificado público é que você deve copiar e colar toda a cadeia de caracteres sem espaços. Caso contrário, o carregamento não funcionará. Você precisará usar seu próprio arquivo .cer do certificado para esta etapa. Não tente copiar e colar o exemplo abaixo.

		$MyP2SRootCertPubKeyBase64 = "MIIDUzCCAj+gAwIBAgIQRggGmrpGj4pCblTanQRNUjAJBgUrDgMCHQUAMDQxEjAQBgNVBAoTCU1pY3Jvc29mdDEeMBwGA1UEAxMVQnJrIExpdGUgVGVzdCBSb290IENBMB4XDTEzMDExOTAwMjQxOFoXDTIxMDExOTAwMjQxN1owNDESMBAGA1UEChMJTWljcm9zb2Z0MR4wHAYDVQQDExVCcmsgTGl0ZSBUZXN0IFJvb3QgQ0EwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQC7SmE+iPULK0Rs7mQBO/6a6B6/G9BaMxHgDGzAmSG0Qsyt5e08aqgFnPdkMl3zRJw3lPKGha/JCvHRNrO8UpeAfc4IXWaqxx2iBipHjwmHPHh7+VB8lU0EJcUe7WBAI2n/sgfCwc+xKtuyRVlOhT6qw/nAi8e5don/iHPU6q7GCcnqoqtceQ/pJ8m66cvAnxwJlBFOTninhb2VjtvOfMQ07zPP+ZuYDPxvX5v3nd6yDa98yW4dZPuiGO2s6zJAfOPT2BrtyvLekItnSgAw3U5C0bOb+8XVKaDZQXbGEtOw6NZvD4L2yLd47nGkN2QXloiPLGyetrj3Z2pZYcrZBo8hAgMBAAGjaTBnMGUGA1UdAQReMFyAEOncRAPNcvJDoe4WP/gH2U+hNjA0MRIwEAYDVQQKEwlNaWNyb3NvZnQxHjAcBgNVBAMTFUJyayBMaXRlIFRlc3QgUm9vdCBDQYIQRggGmrpGj4pCblTanQRNUjAJBgUrDgMCHQUAA4IBAQCGyHhMdygS0g2tEUtRT4KFM+qqUY5HBpbIXNAav1a1dmXpHQCziuuxxzu3iq4XwnWUF1OabdDE2cpxNDOWxSsIxfEBf9ifaoz/O1ToJ0K757q2Rm2NWqQ7bNN8ArhvkNWa95S9gk9ZHZLUcjqanf0F8taJCYgzcbUSp+VBe9DcN89sJpYvfiBiAsMVqGPc/fHJgTScK+8QYrTRMubtFmXHbzBSO/KTAP5rBTxse88EGjK5F8wcedvge2Ksk6XjL3sZ19+Oj8KTQ72wihN900p1WQldHrrnbixSpmHBXbHr9U0NQigrJp5NphfuU5j81C8ixvfUdwyLmTv7rNA7GTAD"
		$p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $MyP2SRootCertPubKeyBase64

11. Crie o gateway de rede virtual para sua rede virtual. O GatewayType deve ser Vpn e o VpnType deve ser RouteBased.

		New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Vpn -VpnType RouteBased -EnableBgp $false -GatewaySku Standard -VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## Configuração do cliente

Cada cliente que se conecta ao Azure usando o Ponto a Site deve ter duas coisas: o cliente VPN deve estar configurado para se conectar e o cliente deve ter um certificado de cliente instalado. Pacotes de configuração de cliente VPN estão disponíveis para clientes do Windows. Confira as [Perguntas frequentes sobre o Gateway de VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) para saber mais.

1. Baixe o pacote de configuração de cliente VPN. Nesta etapa, use o exemplo a seguir para baixar o pacote de configuração do cliente.

		Get-AzureRmVpnClientPackage -ResourceGroupName $RG -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64

	O cmdlet do PowerShell retornará o link de uma URL. Copie e cole o link retornado em um navegador da Web para baixar o pacote para seu computador. Veja abaixo um exemplo da aparência da URL retornada.

    	"https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"
	
2. Gere e instale os certificados de cliente (*.pfx) criados a partir do certificado raiz nos computadores cliente. Você pode usar qualquer método de instalação com o qual esteja familiarizado. Se você estiver usando um certificado raiz autoassinado e não estiver familiarizado com o modo de fazer isso, consulte as instruções localizadas no final deste artigo.

3. Para se conectar à sua rede virtual, no computador cliente, navegue até conexões VPN e localize a conexão VPN que você acabou de criar. Ele será nomeado com o mesmo nome da sua rede virtual. Clique em **Conectar**. Uma mensagem pop-up pode ser exibida sobre o uso do certificado. Se isso acontecer, clique em **Continuar** para usar privilégios elevados.

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

## Para criar um certificado raiz autoassinado usando makecert

Makecert é uma maneira de criar um certificado raiz autoassinado. As etapas abaixo orientarão você pelo procedimento.

1. Em um computador com Windows 10, baixe e instale o SDK do Windows 10 [neste link](https://dev.windows.com/pt-BR/downloads/windows-10-sdk).

2. Após a instalação, localize o utilitário makecert.exe nesse caminho: C:\\Program Files (x86) \\Windows Kits\\10\\bin<arch>.
		
	Exemplo:
	
		C:\Program Files (x86)\Windows Kits\10\bin\x64\makecert.exe

3. Em seguida, crie e instale um certificado raiz no repositório de certificados Pessoal em seu computador. O exemplo abaixo cria um arquivo *.cer* correspondente que você carregará posteriormente. Execute o seguinte comando, como administrador, no qual *RootCertificateName* é o nome que você deseja usar para o certificado.

	Observação: se você executar o exemplo a seguir sem alterações, o resultado será um certificado raiz e o arquivo *RootCertificateName.cer* correspondente. Você pode encontrar o arquivo .cer no diretório onde o comando foi executado. O certificado estará em seus Certificados - Usuário Atual\\Pessoal\\Certificados.

    	makecert -sky exchange -r -n "CN=RootCertificateName" -pe -a sha1 -len 2048 -ss My "RootCertificateName.cer"

	>[AZURE.NOTE]Como você criou um certificado raiz do qual serão gerados certificados de cliente, você pode querer exportar o certificado juntamente com a sua chave privada e salvá-lo em um local seguro onde possa ser recuperado.

## Para gerar um certificado de cliente a partir de um certificado raiz autoassinado

As etapas a seguir ajudarão você a gerar um certificado de cliente a partir de um certificado raiz autoassinado e, em seguida, a exportar e instalar o certificado de cliente.

### Gerar um certificado de cliente

As etapas abaixo orientarão você sobre uma maneira de gerar um certificado de cliente a partir de um certificado autoassinado. Você pode gerar vários certificados de cliente a partir do mesmo certificado raiz. Cada certificado de cliente pode ser exportado e instalado no computador cliente.

1. No mesmo computador usado para criar o certificado raiz autoassinado, abra um prompt de comando como administrador.
2. Altere o diretório para o local no qual você deseja salvar o arquivo de certificado de cliente. *RootCertificateName* refere-se ao certificado raiz autoassinado que você gerou. Se você executar o exemplo a seguir (alterando o RootCertificateName para o nome do seu certificado raiz), o resultado será um certificado de cliente chamado "ClientCertificateName" no seu repositório de certificados Pessoais.
3. Digite o seguinte comando:

    	makecert.exe -n "CN=ClientCertificateName" -pe -sky exchange -m 96 -ss My -in "RootCertificateName" -is my -a sha1

4. Todos os certificados são armazenados no repositório Certificado - Usuário Atual\\Pessoal\\Certificados do computador. Você pode gerar quantos certificados de cliente forem necessários com base neste procedimento.

### Exportar e instalar um certificado de cliente

A instalação de um certificado do cliente em cada computador que você queira conectar à rede virtual é uma etapa obrigatória. As etapas a seguir guiarão você pela instalação manual do certificado do cliente.

1. Para exportar um certificado de cliente, use *certmgr.msc*. Clique com o botão direito do mouse no certificado de cliente que você deseja exportar, clique em **todas as tarefas** e clique em **exportar**. Isso abrirá o Assistente de Exportação de Certificado.
2. No Assistente, clique em **Avançar** , escolha **Sim, exportar a chave privada** e clique em **Avançar**.
3. Na página **Exportar Formato de Arquivo**, você pode deixar o padrão selecionado. Em seguida, clique em **Próximo**.  
4. Na página **Segurança**, você deve proteger a chave privada. Se você optar por usar uma senha, não deixe de anotar ou lembrar da senha definida para esse certificado. Em seguida, clique em **Avançar**.
5. Em **Arquivo a ser Exportado**, **Procure** o local para o qual você deseja exportar o certificado. Em **Nome do arquivo**, dê um nome ao arquivo de certificado. Em seguida, clique em **Próximo**.
6. Clique em **Concluir** para exportar o certificado.	
3. Localize e copie o arquivo *.pfx* no computador cliente. No computador cliente, clique duas vezes no arquivo *.pfx* para instalá-lo. Deixe **Local do Repositório** como **Usuário Atual** e clique em **Avançar**.
4. Na página **Arquivo a importar**, não faça qualquer alteração. Clique em **Próximo**.
5. Na página **Proteção da chave privada**, insira a senha do certificado, caso tenha usado uma, ou verifique se a entidade de segurança que está instalando o certificado está correta e clique em **Avançar**.
6. Na página **Repositório de Certificados**, deixe o local padrão e, em seguida, clique em **Avançar**.
7. Clique em **Concluir**. No **Aviso de Segurança** da instalação do certificado, clique em **Sim**. O certificado foi importado com êxito.

## Próximas etapas

Você pode adicionar uma máquina virtual à rede virtual. Veja [Criar uma máquina virtual](../virtual-machines/virtual-machines-windows-tutorial.md) para obter as etapas.

<!---HONumber=AcomDC_0114_2016-->