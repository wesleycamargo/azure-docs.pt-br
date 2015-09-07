<properties
   pageTitle="Configurar uma conexão rede virtual com rede virtual | Microsoft Azure"
	description="Como conectar redes virtuais do Azure juntas nas mesmas ou em diferentes assinaturas ou em regiões."
	services="vpn-gateway"
	documentationCenter="na"
	authors="cherylmc"
	manager="carolz"
	editor=""/>

<tags
   ms.service="vpn-gateway"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="08/20/2015"
	ms.author="cherylmc"/>


# Configurar uma conexão de rede virtual com rede virtual no Portal do Azure

> [AZURE.SELECTOR]
- [Azure Portal](virtual-networks-configure-vnet-to-vnet-connection.md)
- [PowerShell - Azure Resource Manager](vpn-gateway-vnet-vnet-rm-ps.md)

Este artigo mostrará passo a passo como conectar redes virtuais em conjunto no modo de implantação clássica usando uma combinação do Portal do Azure e do PowerShell.


Conectar uma rede virtual a outra rede virtual é bastante semelhante a conectar uma rede virtual a um site local. Os dois tipos de conectividade usam um gateway de VPN para fornecer um túnel seguro usando IPsec/IKE. As VNets que você conecta podem estar em regiões e assinaturas diferentes. Você pode até possível combinar a comunicação VNet a VNet com configurações multissite. Isso permite estabelecer topologias de rede que combinam conectividade entre instalações a conectividade de rede intervirtual, conforme mostrado no diagrama a seguir:

![Diagrama de conectividade VNet a VNet](./media/virtual-networks-configure-vnet-to-vnet-connection/IC727360.png)


>[AZURE.NOTE]Atualmente, o Azure tem dois modelos de implantação: o modelo de implantação clássico e o modelo de implantação do Gerenciador de Recursos do Azure. Os cmdlets e as etapas de configuração diferem entre modos de implantação. Este tópico ensinará você a conectar redes virtuais que são criadas usando o modo de implantação clássica. Se quiser conectar redes virtuais em conjunto que foram criadas no modo do Gerenciador de Recursos do Azure, consulte [Configurar uma conexão de rede virtual com rede virtual usando o Gerenciador de Recursos do Azure e o PowerShell](vpn-gateway-vnet-vnet-rm-ps.md). Se você quiser conectar uma rede virtual que foi criada no modo clássico a uma rede virtual criada no Gerenciador de Recursos do Azure, consulte [Conectando redes virtuais clássicas a novas redes virtuais](../virtual-network/virtual-networks-arm-asm-s2s.md).

## Por que conectar redes virtuais?

Você talvez queira conectar redes virtuais pelos seguintes motivos:

- **Redundância geográfica entre regiões e presença geográfica**
	- Você pode configurar sua própria sincronização ou replicação geográfica com conectividade segura sem passar por pontos de extremidade para a internet.
	- Com o Balanceador de Carga do Azure e a tecnologia de clustering da Microsoft ou de terceiros, você pode configurar a carga de trabalho de alta disponibilidade com redundância geográfica em várias regiões do Azure. Um exemplo importante é configurar o recurso Sempre Ativado do SQL com Grupos de Disponibilidade espalhados por várias regiões do Azure.

- **Aplicativos multicamadas regionais com limite de isolamento forte**
	- Na mesma região, você pode configurar aplicativos multicamadas com várias redes virtuais conectadas com isolamento forte e comunicação entre camadas segura.

- **Comunicação entre organizações e assinaturas no Azure**
	- Se você tem várias assinaturas do Azure, agora é possível se conectar a cargas de trabalho de assinaturas diferentes com segurança entre redes virtuais.
	- Para empresas ou provedores de serviço, você pode habilitar a comunicação entre organizações com tecnologia VPN segura dentro do Azure.

## Perguntas Frequentes de VNet para VNet

- As redes virtuais podem estar na mesma assinatura ou em assinaturas diferentes.

- As redes virtuais podem estar na mesma região ou em regiões diferentes do Azure (locais).

- Um serviço de nuvem ou um ponto de extremidade de balanceamento de carga NÃO PODE se estender entre redes virtuais, mesmo que elas estejam conectadas.

- A conexão de várias redes virtuais em conjunto não exige nenhum gateway de VPN local, a menos que a conectividade entre locais seja necessária.

- O recurso VNet a VNet dá suporte à conexão de Redes Virtuais do Azure. Ele não dá suporte à conexão de máquinas virtuais ou serviços de nuvem que NÃO estão em uma rede virtual.

- VNet a VNet requer gateways de VPN do Azure com VPNs de roteamento dinâmico. Não há suporte para o gateway de VPNs do Azure com roteamento estático.

- A conectividade de rede virtual pode ser usada simultaneamente com VPNs de multissite, com no máximo 10 túneis de VPN para um gateway de VPN de rede virtual conectando a outras redes virtuais ou a sites locais.

- Os espaços de endereço das redes virtuais e os sites de redes locais não devem se sobrepor. A sobreposição de espaços de endereço causará a falha da criação de redes virtuais ou do carregamento de arquivos de configuração netcfg.

- Não há suporte a túneis redundantes entre um par de redes virtuais.

- Todos os túneis de VPN da rede virtual, incluindo VPNs P2S, compartilham a largura de banda disponível no gateway de VPN do Azure e o mesmo SLA de tempo de ativação de gateway de VPN no Azure.

- O tráfego da rede virtual com rede virtual viaja pelo backbone do Azure.

## Configurar uma conexão de VNet a VNet

Neste procedimento, vamos orientá-lo ao longo da conexão de duas redes virtuais, VNet1 e VNet2. Você precisará estar familiarizado com redes para substituir os intervalos de endereços IP que são compatíveis com os requisitos de seu design de rede. De uma rede virtual do Azure, a conexão a outra rede virtual do Azure é igual a se conectar a uma rede local via VPN S2S (site a site).

Esse procedimento usa principalmente o Portal do Azure. No entanto, você deve usar os cmdlets do Microsoft Azure PowerShell para conectar os gateways de VPN.

![Conectando VNet a VNet](./media/virtual-networks-configure-vnet-to-vnet-connection/IC727361.png)

Há cinco seções para planejar e configurar. Configure cada seção na ordem listada abaixo:

1. [Planejar seus intervalos de endereços IP](#plan-your-ip-address-ranges)
2. [Criar suas redes virtuais](#create-your-virtual-networks)
3. [Adicionar redes locais](#add-local-networks)
4. [Criar os gateways de roteamento dinâmico para cada VNet](#create-the-dynamic-routing-gateways-for-each-vnet)
5. [Conectar os gateways de VPN](#connect-the-vpn-gateways)


## Planejar seus intervalos de endereços IP

É importante decidir os intervalos que você usará para configurar o arquivo de configuração de rede (netcfg). Da perspectiva de VNet1, VNet2 é apenas outra conexão de VPN definida na plataforma Azure. E da perspectiva de VNet2, VNet1 é apenas mais uma conexão VPN. Ambas se identificarão entre si como um site de rede local. Lembre-se de que você deve garantir que nenhum de seus intervalos de VNet ou intervalos de rede local se sobreponham de forma alguma.

A tabela 1 mostra um exemplo de como definir suas VNets. Use os intervalos abaixo apenas como uma diretriz. Anote os intervalos que você usará para suas redes virtuais. Você precisará dessas informações para etapas posteriores.

**Tabela 1**

|Rede Virtual |Definição de Site de Rede Virtual |Definição de Site de Rede Local|
|:----------------|:-------------------------------|:----------------------------|
|VNet1 |VNet1 (10.1.0.0/16) |VNet2 (10.2.0.0/16) |
|VNet2 |VNet2 (10.2.0.0/16) |VNet1 (10.1.0.0/16) |

## Criar suas redes virtuais

Para os fins deste tutorial, vamos criar duas redes virtuais, VNet1 e VNet2. Utilize seus próprios valores ao criar suas VNets. Para os fins deste tutorial, usaremos os seguintes valores para as VNets:

VNet1: Espaço de Endereço = 10.1.0.0/16; Região=Oeste dos EUA

VNet2: Espaço de Endereço = 10.2.0.0/16; Região=Leste do Japão

1. Faça logon no **Portal de Gerenciamento.**

2. No canto inferior esquerdo da tela, clique em **Nova**. No painel de navegação, clique em **Serviços de Rede** e, em seguida, clique em **Rede Virtual**. Clique em **Criação Personalizada** para iniciar o assistente de configuração.

Na página **Detalhes da Rede Virtual**, insira as informações a seguir.

  ![Detalhes de rede virtual](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736055.png)

  - **Nome** – nome de sua rede virtual. Por exemplo, VNet1.
  - **Local** – ao criar uma rede virtual, você a associa a um local do Azure (região). Por exemplo, se você desejar que as VMs implantadas em sua rede virtual estejam localizadas fisicamente no oeste dos EUA, selecione esse local. Você não pode alterar o local associado à sua rede virtual depois de criá-la.



Na página **Servidores DNS e Conectividade de VPN**, insira as informações a seguir e clique na seta avançar no canto inferior direito.

  ![Conectividade de VPN e servidores DNS](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736056.jpg)


- **Servidores DNS** – insira o nome do servidor DNS e o endereço IP ou selecione um servidor DNS previamente registrado no menu suspenso. Essa configuração não cria um servidor DNS. Ela permite que você especifique os servidores DNS que deseja usar para a resolução de nomes para essa rede virtual. Se quiser ter a resolução de nomes entre suas redes virtuais, você precisará configurar seu próprio servidor DNS, em vez de usar a resolução de nomes fornecida pelo Azure.

  - Não marque nenhuma das caixas de seleção. Basta clicar na seta no canto inferior direito para se mover para a próxima tela.

Na página **Espaços de Endereço de Rede Virtual**, especifique o intervalo de endereços que você deseja usar para sua rede virtual. Esses são os DIPS (endereços IP dinâmicos) que serão atribuídos às VMs e a outras instâncias de função que você implantar nessa rede virtual. É particularmente importante selecionar um intervalo que não se sobreponha a nenhum dos intervalos usados para sua rede local. Você precisará realizar a coordenação com o administrador da rede, que pode precisar reservar um intervalo de endereços IP de seu espaço de endereço de rede local para que você possa usar para sua rede virtual.


  ![Página Espaços de endereço de rede virtual](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736057.jpg)

  **Insira as informações a seguir** e clique na marca de seleção no canto inferior direito para configurar a rede.

  - **Espaço de endereço** – incluindo o IP Inicial e a Contagem de Endereços. Verifique se que os espaços de endereço que você especificar não se sobrepõem a nenhum espaço de endereço que você tem em sua rede local. Para este exemplo, vamos usar 10.1.0.0/16 para VNet1.
  - **Adicionar sub-rede** – incluindo o IP Inicial e a Contagem de Endereços. Sub-redes adicionais não são necessárias, mas convém criar uma sub-rede separada para VMs que terão DIPS estáticos. Ou então, você pode colocar suas VMs em uma sub-rede separada das outras instâncias de função.

**Clique na marca de seleção** no canto inferior direito da página, e a criação da sua rede virtual será iniciada. Quando ela for concluída, você verá a indicação *Criada* listada em *Status* na página *Redes* no Portal do Azure.

## Crie outra rede virtual

Em seguida, repita as etapas anteriores para criar outra rede virtual. Neste exercício, você conectará posteriormente essas duas redes virtuais. Observe que é muito importante não ter espaços de endereço sobrepostos ou duplicados. Para os fins deste tutorial, use estes valores:

- **VNet2**
- **Espaço de Endereço** = 10.2.0.0/16
- **Região** = Leste do Japão

## Adicionar redes locais

Ao criar uma configuração de rede virtual com rede virtual, você precisa configurar cada rede virtual para que elas se identifiquem como um site de rede local. Neste procedimento, você configurará cada VNet como uma rede local. Se você já configurou redes virtuais anteriormente, é assim que você deverá adicioná-las como redes locais no Portal do Azure.

1. No canto inferior esquerdo da tela, clique em **Nova**. No painel de navegação, clique em **Serviços de Rede** e, em seguida, clique em **Rede Virtual**. Clique em **Adicionar Rede Local**

2. Na página **Especificar os detalhes de sua rede local**, para **Nome**, digite o nome de uma rede virtual que deseja usar em sua configuração de rede virtual com rede virtual. Para este exemplo, vamos usar VNet 1, pois apontaremos VNet2 para essa rede virtual para nossa configuração.

  Para Endereço de IP de Dispositivo VPN, use qualquer endereço IP. Normalmente, você usaria o endereço IP externo real para um dispositivo VPN. Para configurações de rede virtual com rede virtual, você usará o endereço IP do Gateway. Porém, caso você ainda não tenha criado o gateway, usamos o endereço IP que você especificar aqui como um espaço reservado. Em seguida, você voltará para essas configurações e as definirá com os endereços IP de gateway correspondentes quando o Azure os gerar.

3. Na **página Especificar o endereço**, você colocará o intervalo de endereços IP e a contagem de endereços reais para VNet1. Isso deve corresponder exatamente ao intervalo que você especificou anteriormente para VNet1.

4. Depois de configurar VNet1 como uma rede local, volte e configure VNet2 usando os valores que correspondem a essa VNet.

5. Agora, você apontará cada VNet para a outra como uma rede local. No Portal de Gerenciamento, vá para a página **Configurar** para VNet1. Em **Conectividade site a site**, selecione **Conectar à rede local** e, em seguida, selecione **VNET2** como a rede local.

  ![Conectar à rede local](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736058.jpg)

6. Na seção **espaços de endereço de rede virtual** na mesma página, clique em **adicionar sub-rede de gateway** e, em seguida, clique no ícone **salvar** na parte inferior da página para salvar sua configuração.

7. Repita a etapa para a VNet2 para especificar VNet1 como uma rede local.

## Criar os gateways de roteamento dinâmico para cada VNet

Agora que configurou cada VNet, você vai configurar seus gateways de VNet.

1. Na página **Redes**, verifique se a coluna de status para sua rede virtual indica **Criada**.

2. Na coluna **Nome**, clique no nome de sua rede virtual.

3. Na página **Painel**, observe que essa VNet ainda não tem um gateway configurado. Você verá esse status se alterar à medida que você percorrer as etapas para configurar seu gateway.

4. Na parte inferior da página, clique em **Criar Gateway**.

  Você deve selecionar **Roteamento Dinâmico**. Quando o sistema solicitar que você confirme se deseja criar o gateway, clique em Sim.

  ![Tipo de gateway](./media/virtual-networks-configure-vnet-to-vnet-connection/IC717026.png)

5. Quando o gateway estiver sendo criado, observe que o gráfico de gateway na página muda para amarelo e indica Criando Gateway. Geralmente, leva cerca de 15 minutos para que o gateway seja criado.

6. Repita as mesmas etapas para a outra VNet e selecione **Gateway Dinâmico**. Não é preciso que o gateway da primeira VNet seja concluído para que você comece a criar o gateway da outra VNet.

7. Quando o status do gateway mudar para Conectando, o endereço IP de cada gateway estará visível no Painel. Anote o endereço IP que corresponde a cada VNet, tomando cuidado para não os misturar. Esses são os endereços IP que serão usados quando você editar seus endereços IP de espaço reservado para o Dispositivo VPN em **Redes Locais**.

## Editar a rede local

1. Na página **Redes Locais**, clique no nome da Rede Local que você deseja editar e, em seguida, clique em **Editar** na parte inferior da página. Para **Endereço IP do dispositivo VPN**, insira o endereço IP do gateway que corresponde à VNet. Por exemplo, para VNet1, insira o endereço IP do gateway atribuído a VNet1. Em seguida, clique na seta na parte inferior da página.

2. Na página **Especificar o espaço de endereço**, clique na marca de seleção no canto inferior direito sem fazer alterações.

## Conectar os gateways de VPN

Quando todas as etapas anteriores forem concluídas, você definirá as chaves pré-compartilhadas IPsec/IKE para serem iguais. Você pode fazer isso usando uma API REST ou um cmdlet do PowerShell. Se usar o PowerShell, verifique se você tem a versão mais recente dos cmdlets do Microsoft Azure PowerShell. Os exemplos abaixo usam cmdlets do PowerShell para definir o valor da chave como A1b2C3D4. Observe que ambos usam o mesmo valor de chave. Edite os exemplos abaixo para refletir seus próprios valores.

Para VNet1

	PS C:\> Set-AzureVNetGatewayKey -VNetName VNet1 -LocalNetworkSiteName VNet2 -SharedKey A1b2C3D4

Para VNet2

	PS C:\> Set-AzureVNetGatewayKey -VNetName VNet2 -LocalNetworkSiteName VNet1 -SharedKey A1b2C3D4

Aguarde até que as conexões sejam inicializadas. Depois que o gateway for inicializado, ele será semelhante ao gráfico abaixo, e suas redes virtuais estarão conectadas.

![Status do gateway – Conectado](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736059.jpg)

## Próximas etapas


Se quiser adicionar máquinas virtuais à sua rede virtual, consulte [Como criar uma máquina virtual personalizada](../virtual-machines/virtual-machines-create-custom.md).

Se você quiser configurar uma conexão de rede virtual usando o RRAS, consulte [Configurar uma VPN Site a Site usando o RRAS (Serviço de Roteamento e Acesso Remoto) do Windows Server 2012](https://msdn.microsoft.com/library/dn636917.aspx).

Para obter informações sobre o esquema de configuração, consulte [Esquema de configuração de Rede Virtual do Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).


[1]: ../hdinsight-hbase-geo-replication-configure-vnets.md
[2]: http://channel9.msdn.com/Series/Getting-started-with-Windows-Azure-HDInsight-Service/Configure-the-VPN-connectivity-between-two-Azure-virtual-networks
 

<!---HONumber=August15_HO9-->