<properties
   pageTitle="Configurar uma conexão VNet a VNet"
   description="O recurso VNet a VNet dá suporte à conexão de Redes Virtuais do Azure. Ele não dá suporte à conexão de máquinas virtuais ou serviços de nuvem que NÃO estão em uma rede virtual."
   services="virtual-network"
   documentationCenter="na"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/28/2015"
   ms.author="cherylmc"/>


# Configurar uma conexão VNet a VNet

Conectar uma VNet \(rede virtual\) do Azure a outra rede virtual do Azure é muito semelhante a conectar uma rede virtual a um site local. Os dois tipos de conectividade usam um gateway de rede virtual para fornecer um túnel seguro usando IPsec/IKE. As VNets que você conecta podem estar em regiões e assinaturas diferentes. Você pode até possível combinar a comunicação VNet a VNet com configurações multissite. Isso permite estabelecer topologias de rede que combinam conectividade entre instalações a conectividade de rede intervirtual, conforme mostrado no diagrama a seguir:

![Diagrama de conectividade VNet a VNet](./media/virtual-networks-configure-vnet-to-vnet-connection/IC727360.png)

## O que posso fazer com a conectividade VNet a VNet?

### Redundância geográfica entre regiões e presença geográfica
  - Você pode configurar sua própria sincronização ou replicação geográfica com conectividade segura sem passar por pontos de extremidade para a internet.
  - Com o Balanceador de Carga do Azure e a tecnologia de clustering da Microsoft ou de terceiros, você pode configurar a carga de trabalho de alta disponibilidade com redundância geográfica em várias regiões do Azure. Um exemplo importante é configurar o recurso Sempre Ativado do SQL com Grupos de Disponibilidade espalhados por várias regiões do Azure.

### Aplicativos multicamadas regionais com limite de isolamento forte
  - Na mesma região, você pode configurar aplicativos multicamadas com várias redes virtuais conectadas com isolamento forte e comunicação entre camadas segura.

### Comunicação entre organizações e assinaturas no Azure
  - Se tem várias assinaturas do Azure, agora você pode conectar em conjunto cargas de trabalho de assinaturas diferentes com segurança entre redes virtuais.
  - Para empresas ou provedores de serviços, agora é possível habilitar a comunicação entre organizações com tecnologia de VPN segura no Azure.

### Requisitos e considerações
  - O recurso VNet a VNet dá suporte à conexão de Redes Virtuais do Azure. Ele não dá suporte à conexão de máquinas virtuais ou serviços de nuvem que NÃO estão em uma rede virtual.
  - A opção VNet a VNet requer gateways de VPN do Azure com VPNs de roteamento dinâmico. Não há suporte a VPNs de roteamento estático do Azure. A conexão de várias redes virtuais do Azure em conjunto não requer gateways de VPN lovais, a menos que a conectividade entre locais seja necessária.
  - A conectividade de rede virtual pode ser usada simultaneamente com VPNs de multissite, com um máximo de 10 túneis de VPN para um gateway de VPN de rede virtual conectando a outras redes virtuais ou a sites locais.
  - Os espaços de endereço das redes virtuais e os sites de redes locais NÃO DEVEM se sobrepor. A sobreposição de espaços de endereço causará a falha da criação de redes virtuais ou do carregamento de arquivos de configuração netcfg.
  - As redes virtuais podem estar na mesma assinatura ou em assinaturas diferentes.
  - As redes virtuais podem estar na mesma região ou em regiões diferentes do Azure \(locais\).
  - Não há suporte a túneis redundantes entre um par de redes virtuais.
  - Um serviço de nuvem ou um ponto de extremidade de balanceamento de carga NÃO PODE se estender entre redes virtuais, mesmo que elas estejam conectadas.
  - Todos os túneis de VPN da rede virtual, incluindo VPNs P2S, compartilham a largura de banda disponível no gateway de VPN do Azure e o mesmo SLA de tempo de ativação de gateway de VPN no Azure.

## Configurar uma conexão de VNet a VNet

Neste procedimento, vamos orientá-lo ao longo da conexão de duas redes virtuais, VNet1 e VNet2. Você precisará estar familiarizado com redes para substituir os intervalos de endereços IP que são compatíveis com os requisitos de seu design de rede. De uma rede virtual do Azure, a conexão a outra rede virtual do Azure é o mesmo que se conectar a uma rede local via VPN S2S \(Site a Site\).

Esse procedimento utiliza principalmente o Portal de Gerenciamento. No entanto, você deve usar os cmdlets do PowerShell do Microsoft Azure para conectar os gateways de VPN.

![Conectando VNet a VNet](./media/virtual-networks-configure-vnet-to-vnet-connection/IC727361.png)

Há cinco seções para planejar e configurar. Configure cada seção na ordem listada abaixo: 1. [Planejar seus intervalos de endereços IP](#plan-your-ip-address-ranges) 2. [Criar suas redes virtuais](#create-your-virtual-networks) 3. [Adicionar redes locais](#add-local-networks) 4. [Criar os gateways de roteamento dinâmico para cada VNet](#create-the-dynamic-routing-gateways-for-each-vnet) 5. [Conectar os gateways de VPN](#connect-the-vpn-gateways)

## Planejar seus intervalos de endereços IP

É importante decidir os intervalos que você usará para configurar o arquivo de configuração de rede \(netcfg\). Da perspectiva de VNet1, VNet2 é apenas outra conexão de VPN definida na plataforma Azure. E da perspectiva de VNet2, VNet1 é apenas mais uma conexão VPN. Ambas se identificarão entre si como um site de rede local. Lembre-se de que você deve garantir que nenhum de seus intervalos de VNet ou intervalos de rede local se sobreponham de forma alguma.

A tabela 1 mostra um exemplo de como definir suas VNets. Use os intervalos abaixo apenas como uma diretriz. Anote os intervalos que você usará para suas redes virtuais. Você precisará dessas informações para etapas posteriores.

**Tabela 1**

|Rede Virtual |Definição de Site de Rede Virtual |Definição de Site de Rede Local|
|:----------------|:-------------------------------|:----------------------------|
|VNet1 |VNet1 \(10.1.0.0/16\) |VNet2 \(10.2.0.0/16\) |
|VNet2 |VNet2 \(10.2.0.0/16\) |VNet1 \(10.1.0.0/16\) |

## Criar suas redes virtuais

Para os fins deste tutorial, vamos criar duas redes virtuais, VNet1 e VNet2. Utilize seus próprios valores ao criar suas VNets. Para os fins deste tutorial, usaremos os seguintes valores para as VNets:

VNet1: Espaço de Endereço = 10.1.0.0/16; Região=Oeste dos EUA

VNet2: Espaço de Endereço = 10.2.0.0/16; Região=Leste do Japão

1. Faça logon no **Portal de Gerenciamento.**

2. No canto inferior esquerdo da tela, clique em **Nova**. No painel de navegação, clique em **Serviços de Rede** e, em seguida, clique em **Rede Virtual**. Clique em **Criação Personalizada** para iniciar o assistente de configuração.

3. Na página **Detalhes da Rede Virtual**, insira as informações a seguir. Para obter mais informações sobre as configurações na página de detalhes, consulte a [página de Detalhes da Rede Virtual](https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNetDetails).

  ![Detalhes de rede virtual](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736055.png)

  - **Nome** – nome de sua rede virtual. Por exemplo, VNet1.
  - **Local** – ao criar uma rede virtual, você a associa a um local do Azure \(região\). Por exemplo, se você desejar que as VMs implantadas em sua rede virtual estejam localizadas fisicamente no oeste dos EUA, selecione esse local. Você não pode alterar o local associado à sua rede virtual depois de criá-la.

4. Na página **Servidores DNS e Conectividade de VPN**, insira as seguintes informações e, em seguida, clique na seta avançar no canto inferior direito. Para obter mais informações sobre as configurações nessa página, consulte a [página Servidores DNS e conectividade de VPN](https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETDNS).

  ![Conectividade de VPN e servidores DNS](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736056.jpg) – **Servidores DNS** – insira o nome do servidor DNS e o endereço IP ou selecione um servidor DNS previamente registrado no menu suspenso. Essa configuração não cria um servidor DNS. Ela permite que você especifique os servidores DNS que deseja usar para a resolução de nomes para essa rede virtual. Se quiser ter a resolução de nomes entre suas redes virtuais, você precisará configurar seu próprio servidor DNS, em vez de usar a resolução de nomes fornecida pelo Azure.

  - Não marque nenhuma das caixas de seleção. Basta clicar na seta no canto inferior direito para se mover para a próxima tela.

5. Na página **Espaços de Endereço de Rede Virtual**, especifique o intervalo de endereços que você deseja usar para sua rede virtual. Esses são os DIPS \(endereços IP dinâmicos\) que serão atribuídos às VMs e a outras instâncias de função que você implantar nessa rede virtual. Há muitas regras quanto ao espaço de endereço de rede virtual, assim, convém consultar a página [Espaços de Endereço de Rede Virtual](https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNET_ADDRESS) para obter mais informações. É particularmente importante selecionar um intervalo que não se sobreponha a nenhum dos intervalos usados para sua rede local. Você precisará realizar a coordenação com o administrador da rede, que pode precisar reservar um intervalo de endereços IP de seu espaço de endereço de rede local para que você possa usar para sua rede virtual.

  Insira as informações a seguir e clique na marca de seleção no canto inferior direito para configurar sua rede.

  ![Página Espaços de endereço de rede virtual](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736057.jpg)

  - **Espaço de endereço** – incluindo o IP Inicial e a Contagem de Endereços. Verifique se que os espaços de endereço que você especificar não se sobrepõem a nenhum espaço de endereço que você tem em sua rede local. Para este exemplo, vamos usar 10.1.0.0/16 para VNet1.
  - **Adicionar sub-rede** – incluindo o IP Inicial e a Contagem de Endereços. Sub-redes adicionais não são necessárias, mas convém criar uma sub-rede separada para VMs que terão DIPS estáticos. Ou então, você pode colocar suas VMs em uma sub-rede separada das outras instâncias de função.

6. Clique na marca de seleção no canto inferior direito da página, e sua rede virtual começará a ser criada. Quando ela for concluída, você verá a indicação Criada listada em **Status** na página **Redes** no Portal de Gerenciamento.

7. Crie outra rede virtual. Para os fins deste tutorial, use estes valores: **VNet2**: Espaço de Endereço = 10.2.0.0/16; Região=Leste do Japão

## Adicionar redes locais

Ao criar uma configuração de VNet a VNet, você precisa configurar cada VNet para que elas identifiquem umas às outras como um site de rede local. Neste procedimento, você configurará cada VNet como uma rede local. Se você já configurou VNets anteriormente, essa é a maneira como você deve adicioná-las como redes locais no Portal de Gerenciamento.

1. No canto inferior esquerdo da tela, clique em **Nova**. No painel de navegação, clique em **Serviços de Rede** e, em seguida, clique em **Rede Virtual**. Clique em **Adicionar Rede Local**

2. Na página **Especificar os detalhes de sua rede local**, para **Nome**, digite o nome de uma rede virtual que você deseja usar em sua configuração de VNet a VNet. Para este exemplo, vamos usar VNet 1, pois apontaremos VNet2 para essa rede virtual para nossa configuração.

  Para Endereço de IP de Dispositivo VPN, use qualquer endereço IP. Normalmente, você usaria o endereço IP externo real para um dispositivo VPN. Para configurações de VNet a VNet, você usará o endereço IP do Gateway. Porém, caso você ainda não tenha criado o gateway, usamos o endereço IP que você especificar aqui como um espaço reservado. Em seguida, você voltará para essas configurações e as definirá com os endereços IP de gateway correspondentes quando o Azure os gerar.

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

7. Quando o status do gateway mudar para Conectando, o endereço IP de cada Gateway estará visível no Painel. Anote o endereço IP que corresponde a cada VNet, tomando cuidado para não os misturar. Esses são os endereços IP que serão usados quando você editar seus endereços IP de espaço reservado para o Dispositivo VPN em **Redes Locais**.

## Editar a rede local

1. Na página **Redes Locais**, clique no nome da Rede Local que você deseja editar e, em seguida, clique em **Editar** na parte inferior da página. Para **Endereço IP do dispositivo VPN**, insira o endereço IP do gateway que corresponde à VNet. Por exemplo, para VNet1, insira o endereço IP do gateway atribuído a VNet1. Em seguida, clique na seta na parte inferior da página.

2. Na página **Especificar o espaço de endereço**, clique na marca de seleção no canto inferior direito sem fazer alterações.

## Conectar os gateways de VPN

Quando todas as etapas anteriores forem concluídas, você definirá as chaves pré-compartilhadas IPsec/IKE para serem iguais. Você pode fazer isso usando uma API REST ou um cmdlet do PowerShell. Se usar o PowerShell, verifique se você tem a versão mais recente dos cmdlets do PowerShell do Microsoft Azure. Os exemplos abaixo usam cmdlets do PowerShell para definir o valor da chave como A1b2C3D4. Observe que ambos usam o mesmo valor de chave. Edite os exemplos abaixo para refletir seus próprios valores.

Para VNet1

````PS C:\> Set-AzureVNetGatewayKey -VNetName VNet1 -LocalNetworkSiteName VNet2 -SharedKey A1b2C3D4````

For VNet2

````PS C:\> Set-AzureVNetGatewayKey -VNetName VNet2 -LocalNetworkSiteName VNet1 -SharedKey A1b2C3D4````

Aguarde até que as conexões sejam inicializadas. Depois que o Gateway for inicializado, ele será semelhante ao gráfico abaixo, e suas redes virtuais estarão conectadas.

![Status do gateway – Conectado](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736059.jpg)

##Consulte também

**Conceitos**

[Visão geral da Rede Virtual](https://msdn.microsoft.com/library/azure/jj156007.aspx)

[Esquema de configuração de Rede Virtual do Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx)

[Operações em Redes Virtuais](https://msdn.microsoft.com/library/azure/jj157182.aspx)

[Operações em Gateways de Rede Virtual](https://msdn.microsoft.com/library/azure/jj154113.aspx)

**Outros recursos**

[Postagem do blog – conexões VNet a VNet entre regiões diferentes](http://azure.microsoft.com/blog/2014/06/17/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/)

<!--HONumber=52-->
