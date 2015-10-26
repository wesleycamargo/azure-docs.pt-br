<properties
   pageTitle="Configurar uma rede virtual e um Gateway para Rota Expressa | Microsoft Azure"
   description="Este artigo o orienta na configuração de uma rede virtual (VNet) para a Rota Expressa"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="09/22/2015"
   ms.author="cherylmc"/>

# Configurar uma rede virtual para Rota Expressa

Essas etapas o orientarão ao longo da configuração de uma rede virtual e de um gateway para uso com Rota Expressa usando o modelo de implantação clássico. Não há suporte para essa configuração em VNets e gateways criados usando o modelo do Gerenciador de Recursos. Quando estiver disponível, forneceremos um link desta página para a documentação.
 
>[AZURE.IMPORTANT]É importante saber que atualmente o Azure funciona com dois modelos de implantação: Gerenciador de Recursos e clássico. Antes de começar a configuração, entenda as ferramentas e os modelos de implantação. Para obter informações sobre os modelos de implantação, consulte [Modelos de implantação do Azure](../azure-classic-rm.md).

1. Faça logon no **Portal do Azure**.

2. No canto inferior esquerdo da tela, clique em **Nova**. No painel de navegação, clique em **Serviços de Rede** e, em seguida, clique em **Rede Virtual**. Clique em **Criação Personalizada** para iniciar o assistente de configuração.

3. Na página **Detalhes da Rede Virtual**, insira as informações a seguir.

	- **Nome** – nome da sua rede virtual. Você usará esse nome de rede virtual quando implantar suas VMs e instâncias de PaaS. Portanto, é melhor não criar um nome complicado.
	- **Local** – o local está diretamente relacionado ao local físico (região) onde você deseja que os recursos (VMs) residam. Por exemplo, se você desejar que as VMs implantadas nesta rede virtual estejam localizadas fisicamente no leste dos EUA, selecione esse local. Você não pode alterar a região associada à sua rede virtual depois de criá-la.

4. Na página **Servidores DNS e Conectividade de VPN**, insira as seguintes informações e, em seguida, clique na seta avançar no canto inferior direito.

	- **Servidores DNS** – insira o nome do servidor DNS e o endereço IP ou selecione um servidor DNS previamente registrado no menu suspenso. Essa configuração não cria um servidor DNS. Ela permite que você especifique os servidores DNS que deseja usar para a resolução de nomes para essa rede virtual.
	- **Configurar VPN site a site** – marque a caixa de seleção **Configurar uma VPN site a site**.
	- **Selecione Rota Expressa** – marque a caixa de seleção **Usar Rota Expressa**. Essa opção aparece somente se você selecionou ***Configurar uma VPN site a site*** na etapa anterior.
	- **Rede local** – uma rede local representa sua localização física no local. Você pode selecionar uma rede local que já criou ou pode criar uma nova rede local.

	Se você selecionar uma rede local existente, pule a etapa 5.

5. Se estiver criando uma nova rede local, você verá a página **Conectividade site a site**. Se você tiver selecionado uma rede local que já criou, essa página não será exibida no assistente e você pode ir para a próxima seção. Para configurar sua rede local, digite as informações a seguir e clique na seta de avanço.

	- **Nome** – o nome que você deseja dar ao site de rede local (local).
	- **Espaço de endereço** – Incluindo o IP Inicial e o CIDR (Contagem de Endereços). Você pode especificar qualquer intervalo de endereços, desde que ele não sobreponha o intervalo de endereços para sua rede virtual.
	- **Adicionar espaço de endereço** – Essa configuração não é relevante para a Rota Expressa. **Observação:** é necessário criar um site de rede local para a Rota Expressa. Os prefixos do endereço especificados para o site de rede local serão ignorados. Prefixos de endereço anunciados à Microsoft através do circuito da Rota Expressa serão usados para fins de roteamento.

6. Na página **Espaços de endereço de rede virtual**, insira as seguintes informações e clique na marca de seleção na parte inferior direita para configurar sua rede.

	- **Espaço de endereço** – incluindo o IP inicial e a contagem de endereços. Garanta que os espaços de endereço que você especificar não sobreponham nenhum espaço de endereço que você tenha em sua rede local.
	- **Adicionar sub-rede** – incluindo o IP Inicial e a Contagem de Endereços. Não são necessárias sub-redes adicionais, mas convém criar uma sub-rede separada para as VMs que terão DIPS (endereços IP dinâmicos). Ou você pode colocar suas VMs em uma sub-rede separada das instâncias de PaaS (Plataforma como um Serviço).
	- **Adicionar sub-rede de gateway** - clique para adicionar a sub-rede de gateway. A sub-rede de gateway é usada apenas para o gateway de rede virtual e é necessária para esta configuração. ***Importante:*** O prefixo de sub-rede de gateway para a Rota Expressa deve ser /28 ou menor (/27, /26, etc.)

7. Clique na marca de seleção na parte inferior da página e sua rede virtual começará a ser criada. Quando ela for concluída, você verá a indicação **Criada** listada em **Status** na página **Redes** no Portal de Gerenciamento.

8. Na página **Redes** página, clique na rede virtual que você acabou de criar e em **Painel**.
9. Na parte inferior da página Painel, clique em **CRIAR GATEWAY** e em **Sim**.

10. Quando a criação do gateway for iniciada, você verá uma mensagem informando que o gateway foi iniciado. Pode levar até 15 minutos para que o gateway seja criado.

11. Vincule sua rede a um circuito. Siga as instruções no artigo [Como vincular VNets a circuitos de Rota Expressa](expressroute-howto-linkvnets-classic.md).

## Próximas etapas

- Se quiser adicionar máquinas virtuais à sua rede virtual, consulte [Como criar uma máquina virtual personalizada](../virtual-machines-create-custom.md).
- Para obter mais informações sobre a Rota Expressa, consulte [Visão geral técnica da Rota Expressa](expressroute-introduction.md).


 

<!---HONumber=Oct15_HO3-->