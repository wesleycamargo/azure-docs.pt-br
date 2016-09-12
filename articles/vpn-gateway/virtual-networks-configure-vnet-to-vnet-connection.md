<properties
   pageTitle="Configurar uma conexão de VNet a VNet do modelo de implantação clássica | Microsoft Azure"
   description="Como conectar redes virtuais do Azure entre si usando o PowerShell e o Portal Clássico do Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="cherylmc"/>


# Configurar uma conexão de rede virtual a rede virtual para o modelo de implantação clássica

> [AZURE.SELECTOR]
- [Portal Clássico do Azure](virtual-networks-configure-vnet-to-vnet-connection.md)
- [PowerShell - Azure Resource Manager](vpn-gateway-vnet-vnet-rm-ps.md)


Este artigo o guiará pelas etapas para criar e conectar redes virtuais usando o modelo de implantação clássico (também conhecido como gerenciamento de serviço). As etapas a seguir usam o Portal Clássico do Azure para criar as VNets, os gateways e o PowerShell para configurar a conexão de VNet a VNet. Não é possível configurar a conexão no portal.

![Diagrama de conectividade VNet a VNet](./media/virtual-networks-configure-vnet-to-vnet-connection/v2vclassic.png)


### Ferramentas e modelos de implantação para conexões de rede virtual a rede virtual


[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Uma conexão de rede virtual para rede virtual pode ser configurada em ambos os modelos de implantação e usando várias ferramentas diferentes. Confira a tabela a seguir para saber mais. Podemos atualizar esta tabela conforme os novos artigos, novos modelos de implantação e ferramentas adicionais ficam disponíveis para esta configuração. Quando um artigo estiver disponível, o vincularemos diretamente da tabela.

[AZURE.INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## Sobre conexões de rede virtual a rede virtual

Conectar uma rede virtual a outra rede virtual (rede virtual a rede virtual) é semelhante a conectar uma rede virtual a um site local. Os dois tipos de conectividade usam um gateway de VPN para fornecer um túnel seguro usando IPsec/IKE.

As VNets que você conecta podem estar em regiões e assinaturas diferentes. Você pode combinar a comunicação de VNet a VNet usando configurações multissite. Isso permite estabelecer topologias de rede que combinam conectividade entre instalações a conectividade de rede intervirtual.


### Por que conectar redes virtuais?

Você talvez queira conectar redes virtuais pelos seguintes motivos:

- **Redundância geográfica entre regiões e presença geográfica**
	- Você pode configurar sua própria sincronização ou replicação geográfica com conectividade segura sem passar por pontos de extremidade voltados para a Internet.
	- Com o Azure Load Balancer e a tecnologia de clustering da Microsoft ou de terceiros, você pode configurar uma carga de trabalho altamente disponível com redundância geográfica entre várias regiões do Azure. Um exemplo importante é configurar o Always On do SQL com Grupos de Disponibilidade espalhados por várias regiões do Azure.

- **Aplicativos multicamadas regionais com limite de isolamento forte**
	- Na mesma região, você pode configurar aplicativos multicamadas com várias VNets conectadas com isolamento forte e comunicação entre camadas segura.

- **Comunicação entre organizações e assinaturas no Azure**
	- Se você tem várias assinaturas do Azure, agora é possível se conectar a cargas de trabalho de assinaturas diferentes com segurança entre redes virtuais.
	- Para empresas ou provedores de serviço, é possível habilitar a comunicação entre organizações usando a tecnologia VPN segura no Azure.

### Perguntas frequentes sobre VNet a VNet, para VNets clássicas

- As redes virtuais podem estar na mesma assinatura ou em assinaturas diferentes.

- As redes virtuais podem estar na mesma região ou em regiões diferentes do Azure (locais).

- Um serviço de nuvem ou um ponto de extremidade de balanceamento de carga não pode abranger redes virtuais, mesmo que elas estejam conectadas entre si.

- A conexão de várias redes virtuais entre si não exige nenhum dispositivo VPN.

- O recurso VNet a VNet dá suporte à conexão de Redes Virtuais do Azure. Ele não dá suporte à conexão de máquinas virtuais ou de serviços de nuvem que não estejam implantados em uma rede virtual.

- VNet a VNet requer gateways de roteamento dinâmico. Não há suporte para gateways de roteamento estático do Azure.

- A conectividade de rede virtual pode ser usada simultaneamente com VPNs de multissite. Há um máximo 10 túneis de VPN para um gateway de VPN de rede virtual conectado a outras redes virtuais ou a sites locais.

- Os espaços de endereço das redes virtuais e os sites de redes locais não devem se sobrepor. A sobreposição de espaços de endereço causará a falha da criação de redes virtuais ou do carregamento de arquivos de configuração netcfg.

- Não há suporte a túneis redundantes entre um par de redes virtuais.

- Todos os túneis de VPN para VNet, incluindo VPNs P2S, compartilham a largura de banda disponível no gateway de VPN e o mesmo SLA de tempo de ativação de gateway de VPN no Azure.

- O tráfego da rede virtual com rede virtual viaja pelo backbone do Azure.


## <a name="step1"></a>Etapa 1 – Planejar os intervalos de endereços IP

É importante decidir os intervalos que você usará para configurar as redes virtuais. Para esta configuração, você deve garantir que nenhum dos intervalos de VNet se sobreponham entre si ou aos das redes locais às quais se conectam.

A tabela a seguir mostra um exemplo de como definir as VNets. Use os intervalos apenas como uma diretriz. Anote os intervalos para suas redes virtuais. Você precisa dessas informações para etapas posteriores.

**Configurações de exemplo**

|Rede Virtual |Espaço de endereço |Região |Conecta ao site de rede local|
|:----------------|:---------------------------|:-----------|:-----------------------------|
|VNet1 |VNet1 (10.1.0.0/16) |Oeste dos EUA |VNet2Local (10.2.0.0/16) |
|VNet2 |VNet2 (10.2.0.0/16) |Leste do Japão |VNet1Local (10.1.0.0/16) |
  
## Etapa 2 – Criar a VNet1

Nesta etapa, criamos a VNet1. Ao usar qualquer um dos exemplos, faça as substituições pelos seus próprios valores. Se sua VNet já existir, você não precisará realizar esta etapa. Mas você precisa verificar se os intervalos de endereços IP não se sobrepõem aos intervalos da segunda VNet ou de qualquer outra rede virtual à qual deseja se conectar.

1. Faça logon no [portal clássico do Azure](https://manage.windowsazure.com). Neste artigo, podemos usar o portal clássico porque algumas das definições de configuração necessárias ainda não estão disponíveis no portal do Azure.

2. No canto inferior esquerdo da tela, clique em **Novo** > **Serviços de Rede** > **Rede Virtual** > **Criação Personalizada** para iniciar o assistente de configuração. Ao navegar pelo assistente, adicione os valores especificados para cada página.

### Detalhes de rede virtual

Na página Detalhes da rede virtual, insira as seguintes informações:

  ![Detalhes de rede virtual](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736055.png)

  - **Nome** – nome de sua rede virtual. Por exemplo, VNet1.
  - **Local** – ao criar uma rede virtual, você a associa a um local do Azure (região). Por exemplo, se você desejar que as VMs implantadas em sua rede virtual estejam localizadas fisicamente no oeste dos EUA, selecione esse local. Você não pode alterar o local associado à sua rede virtual depois de criá-la.

### Conectividade de VPN e servidores DNS

Na página Conectividade entre Servidores DNS e VPN, insira as informações a seguir e clique na seta avançar na parte inferior direita.

  ![Conectividade de VPN e servidores DNS](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736056.jpg)

- **Servidores DNS** – insira o nome do servidor DNS e o endereço IP ou selecione um servidor DNS previamente registrado no menu suspenso. Essa configuração não cria um servidor DNS. Ela permite que você especifique os servidores DNS que deseja usar para a resolução de nomes dessa rede virtual. Se quiser ter a resolução de nomes entre suas redes virtuais, você precisa configurar seu próprio servidor DNS, em vez de usar a resolução de nomes fornecida pelo Azure.
- Não marque nenhuma das caixas de seleção para conectividade de P2S ou S2S. Clique na seta no canto inferior direito para se mover para a próxima tela.

### Espaços de Endereço da Rede Virtual

Na página Espaços de Endereço de Rede Virtual, especifique o intervalo de endereços que deseja usar para a rede virtual. Esses são os DIPS (endereços IP dinâmicos) que serão atribuídos às VMs e a outras instâncias de função que você implantar nessa rede virtual.

Se estiver criando uma VNet que também terá uma conexão com a rede local, será extremamente importante selecionar um intervalo que não se sobreponha a nenhum dos intervalos usados para a rede local. Neste caso, você precisa coordenar com o administrador da rede. Talvez seu administrador da rede precise reservar um intervalo de endereços IP de seu espaço de endereço de rede local para que você possa usar para sua VNet.

  ![Página Espaços de endereço de rede virtual](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736057.jpg)

  - **Espaço de endereço** – incluindo o IP Inicial e a Contagem de Endereços. Verifique se os espaços de endereço especificados não se sobrepõem a nenhum espaço de endereço na rede local. Para este exemplo, usamos 10.1.0.0/16 para VNet1.
  - **Adicionar sub-rede** – incluindo o IP Inicial e a Contagem de Endereços. Sub-redes adicionais não são necessárias, mas convém criar uma sub-rede separada para VMs que terão DIPS estáticos. Ou então, você pode colocar suas VMs em uma sub-rede separada das outras instâncias de função.
 
**Clique na marca de seleção** no canto inferior direito da página, e a criação da sua rede virtual será iniciada. Quando ela for concluída, você verá “Criada” listado em Status, na página Redes.

## Etapa 3 – Criar a VNet2

Em seguida, repita as etapas anteriores para criar outra VNet. Em etapas posteriores, você conectará as duas VNets. Você pode consultar as [configurações de exemplo](#step1) na Etapa 1. Se sua VNet já existir, você não precisará realizar esta etapa. Entretanto, você precisa verificar se os intervalos de endereços IP não se sobrepõem aos intervalos de nenhuma VNet ou rede local à qual deseja se conectar.

## Etapa 4 – Adicionar os sites de rede local

Ao criar uma configuração de VNet a VNet, você precisa configurar os sites de rede local, que são mostrados na página **Redes Locais** do portal. O Azure usa as configurações especificadas em cada site de rede local para determinar como rotear o tráfego entre as VNets. Você determina o nome que deseja usar para se referir a cada site de rede local. É melhor usar um nome descritivo, pois o valor é selecionado em uma lista suspensa em etapas posteriores.

Por exemplo, a VNet1 se conecta a um site de rede local criado por você, chamado "VNet2Local". As configurações da VNet2Local contêm os prefixos de endereço da VNet2, além de um endereço IP público do gateway da VNet2. A VNet2 se conecta a um site de rede local criado por você, chamado "VNet1Local", que conterá os prefixos de endereço da VNet1 e o endereço IP público do gateway da VNet1.

### <a name="localnet"></a>Adicionar o site de rede local VNet1Local

1. No canto inferior esquerdo da tela, clique em **Novo** > **Serviços de Rede** > **Rede Virtual** > **Adicionar Rede Local**.

2. Na página **Especificar os detalhes da rede local**, em **Nome**, insira o nome que deseja usar para representar a rede à qual deseja se conectar. Neste exemplo, você pode usar "VNet1Local" para se referir aos intervalos de endereços IP e ao gateway da VNet1.

3. Em **Endereço IP do dispositivo VPN (opcional)**, especifique qualquer endereço IP público válido. Normalmente, você usaria o endereço IP externo real para um dispositivo VPN. Para configurações de VNet a VNet, você usa o endereço IP público atribuído ao gateway da VNet. Mas, considerando que você ainda não criou o gateway, você pode especificar qualquer endereço IP público válido como um espaço reservado. Não deixe em branco. Não é opcional para essa configuração. Em uma etapa posterior, você voltará para essas configurações e as definirá com os endereços IP de gateway correspondentes quando o Azure os gerar. Clique na seta para avançar para a próxima tela.

4. Na **página Especificar o endereço**, insira o intervalo de endereços IP e a contagem de endereços para VNet1. É necessário corresponder exatamente ao intervalo configurado para a VNet1. O Azure usa os intervalos de endereços IP que você especificar para rotear o tráfego destinado à VNet1. Clique na marca de seleção para criar a rede local.

### Adicionar o site de rede local VNet2Local

Siga as etapas acima para criar o site de rede local "VNet2Local". Caso seja necessário, consulte os valores em [configurações de exemplo](#step1) na Etapa 1.

### Configure cada VNet para apontar para uma rede local

Cada VNet deve apontar para a respectiva rede local à qual você deseja rotear o tráfego.

#### Para VNet1

1. Navegue para a página **Configurar** da rede virtual **VNet1**.
2. Em conectividade de site a site, selecione “Conectar à rede local” e, em seguida, selecione **VNet2Local** como a rede local, na lista suspensa.
3. Salve suas configurações.

#### Para VNet2

1. Navegue para a página **Configurar** da rede virtual **VNet2**.
2. Em conectividade de site a site, selecione “Conectar à rede local” e, em seguida, selecione **VNet1Local** na lista suspensa, como a rede local.
3. Salve suas configurações.

## Etapa 5 – Configurar um gateway para cada VNet

Configure um gateway de Roteamento Dinâmico para cada rede virtual. Essa configuração não dá suporte a gateways de Roteamento Estático. Se estiver usando Vnets que já estejam configuradas e que já tenham gateways de Roteamento Dinâmico, você não precisará realizar esta etapa. Se os gateways forem de Roteamento Estático, você precisa excluí-los e recriá-los como gateways de Roteamento Dinâmico. Ao excluir um gateway, o endereço IP público atribuído a ele é liberado e você precisa voltar e reconfigurar suas redes locais e dispositivos VPN com o novo endereço IP público do novo gateway.

1. Na página **Redes**, verifique se a coluna de status para sua rede virtual indica **Criada**.

2. Na coluna **Nome**, clique no nome de sua rede virtual. Para este exemplo, vamos usar “VNet1”.

3. Na página **Painel**, observe que essa VNet ainda não tem um gateway configurado. Você verá esse status se alterar à medida que você percorrer as etapas para configurar seu gateway.

4. Na parte inferior da página, clique em **Criar Gateway** e em **Roteamento Dinâmico**. Quando o sistema solicitar que você confirme se deseja criar o gateway, clique em Sim.

  	![Tipo de gateway](./media/virtual-networks-configure-vnet-to-vnet-connection/IC717026.png)

5. Quando o gateway estiver sendo criado, observe que o gráfico de gateway na página será alterado para amarelo e indicará “Criando Gateway”. Geralmente, leva cerca de 30 minutos para que o gateway seja criado.

6. Repita as mesmas etapas para VNet2. Não é preciso que o gateway da primeira VNet seja concluído para que você comece a criar o gateway da outra VNet.

7. Quando o status do gateway for alterado para “Conectando”, o endereço IP público de cada gateway fica visível no Painel. Anote o endereço IP que corresponde a cada VNet, tomando cuidado para não os misturar. Esses são os endereços IP que são usados quando você editar os endereços IP de espaço reservado para o Dispositivo VPN de cada rede local.

## Etapa 6: Editar a rede local

1. Na página **Redes Locais**, clique no nome da Rede Local que você deseja editar e, em seguida, clique em **Editar** na parte inferior da página. Para **Endereço IP do dispositivo VPN**, insira o endereço IP do gateway que corresponde à VNet. Por exemplo, para VNet1Local, insira o endereço IP do gateway atribuído à VNet1. Em seguida, clique na seta na parte inferior da página.

2. Na página **Especificar o espaço de endereço**, clique na marca de seleção no canto inferior direito sem fazer alterações.

## Etapa 7 – Criar a conexão VPN

Quando todas as etapas anteriores forem concluídas, defina as chaves pré-compartilhadas IPsec/IKE e crie a conexão. Esse conjunto de etapas usa o PowerShell e não pode ser configurado no portal. Consulte [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações de como instalar os cmdlets do Azure PowerShell. Certifique-se de baixar a versão mais recente dos cmdlets de SM (Gerenciamento de Serviços).

1. Abra o Windows PowerShell e faça logon.

		Add-AzureAccount

2. Selecione a assinatura na qual suas VNets residem.

		Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
		Select-AzureSubscription -SubscriptionName "<Subscription Name>"

3. Cria as conexões. Nos exemplos, observe que a chave compartilhada é exatamente a mesma. A chave compartilhada sempre deve corresponder.


	Conexão da VNet1 à VNet2

		Set-AzureVNetGatewayKey -VNetName VNet1 -LocalNetworkSiteName VNet2Local -SharedKey A1b2C3D4

	Conexão da VNet2 à VNet1

		Set-AzureVNetGatewayKey -VNetName VNet2 -LocalNetworkSiteName VNet1Local -SharedKey A1b2C3D4

4. Aguarde até que as conexões sejam inicializadas. Depois que o gateway for inicializado, o gateway é semelhante à figura a seguir.

	![Status do gateway – Conectado](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736059.jpg)

	[AZURE.INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

## Próximas etapas

Você pode adicionar máquinas virtuais às suas redes virtuais. Consulte a [Documentação sobre Máquinas Virtuais](https://azure.microsoft.com/documentation/services/virtual-machines/) para obter mais informações.



[1]: ../hdinsight-hbase-geo-replication-configure-vnets.md
[2]: http://channel9.msdn.com/Series/Getting-started-with-Windows-Azure-HDInsight-Service/Configure-the-VPN-connectivity-between-two-Azure-virtual-networks
 

<!---HONumber=AcomDC_0831_2016-->