<properties 
   pageTitle="Como conectar redes virtuais clássicas a redes virtuais ARM no Azure"
   description="Saiba como criar uma conexão VPN entre redes virtuais clássicas e novas redes virtuais"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/11/2015"
   ms.author="telmos" />

# Conectando redes virtuais clássicas a novas redes virtuais

Atualmente, o Azure tem dois modos de gerenciamento: Gerenciador de Serviços do Azure (conhecido como clássico) e o ARM (Gerenciador de Recursos do Azure). Se você já usa o Azure há algum tempo, provavelmente terá as VMs do Azure e as funções de instância em execução em uma rede virtual clássica. E talvez suas VMs e instâncias de função mais recentes podem estar em execução em uma rede virtual criada no ARM.

Em tais situações, convém garantir que a nova infraestrutura seja capaz de se comunicar com os recursos clássicos. Você pode fazer isso criando uma conexão VPN entre as duas redes virtuais.

Neste artigo, você aprenderá a criar uma conexão VPN site a site (S2S) entre uma rede virtual clássica e uma rede virtual de ARM.

>[AZURE.NOTE]Este artigo pressupõe que você já tem redes virtuais clássicas, e redes virtuais ARM, e que você está familiarizado com a configuração de uma conexão VPN S2S para redes virtuais clássicas. Para uma solução completa e detalhada em conectividade VPN S2S entre redes virtuais clássicas e ARM, visite [Guia de soluções - conectar uma rede virtual clássica a uma rede virtual ARM usando uma VPN S2S](../virtual-networks-arm-asm-s2s.md).

Obtenha uma visão geral das tarefas a serem executadas para criar uma conexão VPN S2S entre uma rede virtual clássica e uma rede virtual ARM usando os gateways do Azure abaixo.

1 -[Criar um gateway de VPN para rede virtual clássica](#Step-1:-Create-a-VPN-gateway-for-the-classic-VNet)

2 -[Criar um gateway de VPN para rede virtual ARM](#Step-2:-Create-a-VPN-gateway-for-the-ARM-VNet)

3 -[Criar uma conexão entre os gateways](#Step-3:-Create-a-connection-between-the-gateways)

## Etapa 1: criar um gateway de VPN para rede virtual clássica

Para criar o gateway de VPN para a rede virtual clássica, siga as instruções abaixo.

1. Abra o portal clássico em https://manage.windowsazure.com e insira suas credenciais, se for necessário.
2. No canto inferior esquerdo da tela, clique no botão **NOVO** e clique em **SERVIÇOS DE REDE**. Em seguida, clique em **REDES VIRTUAIS** e clique em **ADICIONAR REDE LOCAL**.
3. Na janela **Especificar os detalhes da sua rede local**, digite um nome para a rede virtual ARM a qual você deseja se conectar e, no canto inferior direito da janela, clique no botão de seta.
3. No espaço de endereço da caixa de texto **IP INICIAL**, digite o prefixo da rede para a rede virtual ARM a qual você deseja se conectar. 
4. Na lista suspensa **CIDR (CONTAGEM DE ENDEREÇO)**, selecione o número de bits usados para a parte de rede do bloco CIDR usado pela rede virtual ARM a qual você deseja se conectar.
5. Em **ENDEREÇO IP DO DISPOSITIVO VPN (OPCIONAL)**, digite qualquer endereço IP público válido. Alteraremos esse endereço IP mais tarde. Em seguida, clique no botão de marca de seleção no canto inferior direito da tela. A figura a seguir mostra exemplos de definições para essa página.

	![Configurações de rede local](..\virtual-network\media\virtual-networks-arm-asm-s2s-howto\figurex1.png)

5. Na página **redes**, clique em **REDES VIRTUAIS**, clique em sua rede virtual clássica e, em seguida, clique em **CONFIGURAR**.
6. Em **conectividade site a site**, marque a caixa de seleção **conectar-se à rede local**.
7. Selecione a rede local criada na etapa 4 a partir da lista de redes disponíveis na lista suspensa **REDE LOCAL** e clique em **SALVAR**.
8. Depois que as configurações forem salvas, clique em **PAINEL** e, na parte inferior da página, clique em **CRIAR GATEWAY**, clique em **ROTEAMENTO DINÂMICO** e, em seguida, clique em **SIM**.
9. Aguarde a criação do gateway e copie seu endereço IP público. Você precisará dele para configurar o gateway na rede virtual ARM.

## Etapa 2: criar um gateway de VPN para rede virtual ARM

Para criar um gateway de VPN para rede virtual ARM, siga as instruções abaixo.

1. Em um console do PowerShell, alterne para modo ARM executando o comando a seguir.

		Switch-AzureMode AzureResourceManager

2. Crie uma rede local, executando o comando a seguir. A rede local deve usar o bloco CIDR da rede virtual clássica a qual você deseja se conectar, e o endereço IP público do gateway criado na etapa 1 acima.

		New-AzureLocalNetworkGateway -Name VNetClassicNetwork `
			-Location "East US" -AddressPrefix "10.0.0.0/20" `
			-GatewayIpAddress "168.62.190.190" -ResourceGroupName RG1

3. Crie um endereço IP público do gateway executando o comando a seguir.

		$ipaddress = New-AzurePublicIpAddress -Name gatewaypubIP`
			-ResourceGroupName RG1 -Location "East US" `
			-AllocationMethod Dynamic

4. Recupere a sub-rede usada para o gateway, executando o comando a seguir.

		$subnet = Get-AzureVirtualNetworkSubnetConfig -Name GatewaySubnet `
			-VirtualNetwork (Get-AzureVirtualNetwork -Name VNetARM -ResourceGroupName RG1) 

	>[AZURE.IMPORTANT]A sub-rede do gateway já deve existir e deve ter o nome GatewaySubnet.

5. Crie um objeto de configuração de IP do gateway executando o comando a seguir. Observe a ID de uma sub-rede do gateway. Essa sub-rede deve existir na rede virtual.

		$ipconfig = New-AzureVirtualNetworkGatewayIpConfig `
			-Name ipconfig -PrivateIpAddress 10.1.2.4 `
			-SubnetId $subnet.id -PublicIpAddressId $ipaddress.id

	>[AZURE.IMPORTANT]Os parâmetros *SubnetId* e *PublicIpAddressId* devem receber a propriedade id da sub-rede e objetos de endereço IP, respectivamente. Você não pode usar uma cadeia de caracteres simples.
	
5. Crie o gateway da rede virtual ARM executando o comando a seguir.

		New-AzureVirtualNetworkGateway -Name v1v2Gateway -ResourceGroupName RG1 `
			-Location "East US" -GatewayType Vpn -IpConfigurations $ipconfig `
			-EnableBgp $false -VpnType RouteBased

6. Após a criação do gateway de VPN, recupere seu endereço IP público executando o comando a seguir. Copie o endereço IP, você precisará dele para configurar a rede local para a rede virtual clássica.

		Get-AzurePublicIpAddress -Name gatewaypubIP -ResourceGroupName RG1

## Etapa 3: criar uma conexão entre os gateways

1. Abra o portal clássico em https://manage.windowsazure.com e insira suas credenciais, se for necessário.
2. No portal do clássico, role a tela para baixo e clique em**REDES**, clique em **REDES LOCAIS**, clique na rede virtual ARM a qual você deseja se conectar e, em seguida, clique no botão **EDITAR**.
3. Em **ENDEREÇO IP DO DISPOSITIVO VPN (OPCIONAL)** digite o endereço IP para a recuperação de gateway da rede virtual ARM na etapa 2 acima, em seguida, clique na seta à direita ,no canto inferior direito, e clique no botão da marca de seleção.
4. Em um console do PowerShell, alterne para modo de Gerenciador de Serviços do Azure executando o comando a seguir.

		Switch-AzureMode AzureServiceManager

5. Configure uma chave compartilhada executando o comando a seguir. Altere os nomes das redes virtuais para seus próprios nomes de rede virtual.

		Set-AzureVNetGatewayKey -VNetName VNetClassic `
			-LocalNetworkSiteName VNetARM -SharedKey abc123

6. Em um console do PowerShell, alterne para modo do Gerenciador de Recursos do Azure executando o comando a seguir.

		Switch-AzureMode AzureResourceManager

7. Crie a conexão VPN executando os comandos a seguir.

		$vnet01gateway = Get-AzureLocalNetworkGateway -Name VNetClassic -ResourceGroupName RG1
		$vnet02gateway = Get-AzureVirtualNetworkGateway -Name v1v2Gateway -ResourceGroupName RG1
		
		New-AzureVirtualNetworkGatewayConnection -Name arm-asm-s2s-connection `
			-ResourceGroupName RG1 -Location "East US" -VirtualNetworkGateway1 $vnet02gateway `
			-LocalNetworkGateway2 $vnet01gateway -ConnectionType IPsec `
			-RoutingWeight 10 -SharedKey 'abc123'

## Próximas etapas

- Saiba mais sobre [o Provedor de recursos de rede (NRP) para ARM](../resource-groups-networking.md).
- Crie uma [solução completa conectando uma rede virtual clássica a uma rede virtual de ARM usando uma VPN S2S](../virtual-networks-arm-asm-s2s.md).

<!---HONumber=AcomDC_1217_2015-->