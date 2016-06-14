<properties
   pageTitle="Configurar uma rede virtual e um Gateway para Rota Expressa | Microsoft Azure"
   description="Este artigo o orienta na configuração de uma rede virtual (VNet) para a Rota Expressa usando o modelo de implantação clássico."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="05/25/2016"
   ms.author="cherylmc"/>

# Criar uma rede virtual para a Rota Expressa no portal clássico

As etapas neste artigo o orientarão ao longo da configuração de uma rede virtual e de um gateway para uso com Rota Expressa usando o modelo de implantação clássico e o portal clássico.

Se estiver procurando instruções sobre o modelo de implantação do Gerenciador de Recursos, você poderá usar os seguintes artigos, que explicam como [Criar uma rede virtual usando o PowerShell](../virtual-network/virtual-networks-create-vnet-arm-ps.md) e [Adicionar um Gateway de VPN para uma VNet do Gerenciador de Recursos para a Rota Expressa](expressroute-howto-add-gateway-resource-manager.md).

**Sobre modelos de implantação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Criar uma rede virtual clássica e um gateway

As etapas abaixo criarão uma rede virtual clássica e um gateway de rede virtual. Se você já tiver uma rede virtual clássica, confira a seção [Configurar uma rede virtual clássica existente](#config) neste artigo.

1. Faça logon no [portal clássico do Azure](http://manage.windowsazure.com).

2. No canto inferior esquerdo da tela, clique em **Nova**. No painel de navegação, clique em **Serviços de Rede** e, em seguida, clique em **Rede Virtual**. Clique em **Criação Personalizada** para iniciar o assistente de configuração.

3. Na página **Detalhes da Rede Virtual**, insira as informações a seguir.

	- **Nome** – nome da sua rede virtual. Você usará esse nome de rede virtual quando implantar suas VMs e instâncias de PaaS. Portanto, é melhor não criar um nome complicado.
	- **Local** – o local está diretamente relacionado ao local físico (região) onde você deseja que os recursos (VMs) residam. Por exemplo, se você desejar que as VMs implantadas nesta rede virtual estejam localizadas fisicamente no leste dos EUA, selecione esse local. Você não pode alterar a região associada à sua rede virtual depois de criá-la.

4. Na página **Servidores DNS e Conectividade de VPN**, insira as seguintes informações e, em seguida, clique na seta avançar no canto inferior direito.

	- **Servidores DNS** – insira o nome do servidor DNS e o endereço IP ou selecione um servidor DNS previamente registrado no menu suspenso. Essa configuração não cria um servidor DNS. Ela permite que você especifique os servidores DNS que deseja usar para a resolução de nomes para essa rede virtual.
	- **Conectividade Site a Site** — marque a caixa de seleção para **Configurar uma VPN site a site**.
	- **Rota Expressa** — marque a caixa de seleção **Usar Rota Expressa**. Essa opção aparece somente se você tiver selecionado **Configurar uma VPN Site a Site**.
	- **Rede Local** — é necessário criar um site de rede local para a Rota Expressa. No entanto, no caso de uma conexão da Rota Expressa, os prefixos de endereço especificados para o site da rede local serão ignorados. Em vez disso, os prefixos de endereço anunciados à Microsoft por meio do circuito de Rota Expressa serão usados para fins de roteamento.<BR>Se já tiver uma rede local criada para a conexão de Rota Expressa, você poderá selecioná-la no menu suspenso. Caso não tenha, escolha **Especificar uma Nova Rede Local**.

5. A página **Conectividade Site a Site** será exibida se você tiver selecionado para especificar uma nova rede local na etapa anterior. Para configurar sua rede local, digite as informações a seguir e clique na seta de avanço.

	- **Nome** – o nome que você deseja dar ao site de rede local (local).
	- **Espaço de endereço** – Incluindo o IP Inicial e o CIDR (Contagem de Endereços). Você pode especificar qualquer intervalo de endereços, desde que ele não sobreponha o intervalo de endereços para sua rede virtual. Geralmente, isso especificaria os intervalos de endereços para suas redes locais, mas no caso da Rota Expressa, essas configurações não são usadas. No entanto, essa configuração é necessária para criar a rede local quando você estiver usando o portal clássico.
	- **Adicionar espaço de endereço** – Essa configuração não é relevante para a Rota Expressa.


6. Na página **Espaços de endereço de rede virtual**, insira as seguintes informações e clique na marca de seleção na parte inferior direita para configurar sua rede.

	- **Espaço de endereço** – incluindo o IP inicial e a contagem de endereços. Garanta que os espaços de endereço que você especificar não sobreponham nenhum espaço de endereço que você tenha em sua rede local.
	- **Adicionar sub-rede** – incluindo o IP Inicial e a Contagem de Endereços. As sub-redes adicionais não são necessárias.
	- **Adicionar sub-rede de gateway** - clique para adicionar a sub-rede de gateway. A sub-rede de gateway é usada apenas para o gateway de rede virtual e é obrigatória para essa configuração.<BR>O CIDR da sub-rede de gateway (contagem de endereços) para a Rota Expressa deve ser /28 ou maior (/27, /26, etc.). Isso permitirá endereços IP suficientes nessa sub-rede para que a configuração funcione. No portal clássico, se você tiver marcado a caixa de seleção para usar a Rota Expressa, o portal especificará uma sub-rede de gateway com /28. Observe que você não pode ajustar a contagem de endereços CIDR no portal clássico. A sub-rede de gateway será exibida como **Gateway** no portal clássico, embora o nome real da sub-rede de gateway que é criada seja de fato **GatewaySubnet**. Você pode exibir esse nome usando o PowerShell ou no portal do Azure.

7. Clique na marca de seleção na parte inferior da página e sua rede virtual começará a ser criada. Quando ela for concluída, você verá **Criada** listada em **Status** na página **Redes** no portal clássico.

## <a name="gw"></a>Criar o gateway

1. Na página **Redes**, clique na rede virtual que você acabou de criar e em **Painel**, na parte superior da página.

2. Na parte inferior da página **Painel**, clique em **Criar Gateway** e escolha **Roteamento Dinâmico**. Clique em **Sim** para confirmar que deseja criar um gateway.

3. Quando a criação do gateway for iniciada, você verá uma mensagem informando que o gateway foi iniciado. Pode levar até 45 minutos para que o gateway seja criado.

4. Vincule sua rede a um circuito. Siga as instruções no artigo [Como vincular VNets a circuitos de Rota Expressa](expressroute-howto-linkvnet-classic.md).

## <a name="config"></a>Configurar uma rede virtual clássica existente para a Rota Expressa

Se já tiver uma rede virtual clássica, você poderá configurá-la para se conectar à Rota Expressa no portal clássico. As configurações serão as mesmas das seções acima. Sendo assim, leia essas seções para se familiarizar com as configurações necessárias. Se quiser criar uma conexão coexistente de Rota Expressa/Site a Site, confira [este artigo](expressroute-howto-coexist-classic.md) para conhecer as etapas. Elas são diferentes das etapas neste artigo.
 
1. Você precisará criar a rede local antes de atualizar o restante das configurações de rede virtual. Para criar uma nova rede local, que é necessária ao configurar a Rota Expressa por meio do portal clássico, clique em **Novo** **>** **Serviços de Rede** **>** **Rede Virtual** **>** **Adicionar rede local**. Siga as etapas do assistente para criar a rede local.

2. Use a página **Configurar** para atualizar o restante das configurações da rede virtual e associá-la à rede local.

3. Depois de definir as configurações, vá para a seção [Criar o gateway](#gw) deste artigo para criar o gateway.


## Próximas etapas

- Se quiser adicionar máquinas virtuais à sua rede virtual, consulte [Roteiros de aprendizado sobre máquinas virtuais](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/).
- Se quiser saber mais sobre a Rota Expressa, confira a [Visão geral da Rota Expressa](expressroute-introduction.md).


 

<!---HONumber=AcomDC_0601_2016-->