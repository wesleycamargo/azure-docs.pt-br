<properties
   pageTitle="Configurar uma rede Virtual para a Rota Expressa | Microsoft Azure"
   description="Este artigo o orienta na configuração de uma rede virtual (VNet) para a Rota Expressa"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="06/25/2015"
   ms.author="cherylmc"/>

#  Configurar uma rede virtual para Rota Expressa

1. Faça logon no **Portal de Gerenciamento**.
2. No canto inferior esquerdo da tela, clique em **Nova**. No painel de navegação, clique em **Serviços de Rede** e, em seguida, clique em **Rede Virtual**. Clique em **Criação Personalizada** para iniciar o assistente de configuração.
3. Na página **Detalhes da Rede Virtual**, insira as informações a seguir. Para obter mais informações sobre as configurações na página de detalhes, consulte a [página de Detalhes da Rede Virtual](https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNetDetails).

	- **Nome** – nome da sua rede virtual. Você usará esse nome de rede virtual quando implantar suas VMs e instâncias de PaaS. Portanto, é melhor não criar um nome complicado.
	- **Local** – o local está diretamente relacionado ao local físico (região) onde você deseja que os recursos (VMs) residam. Por exemplo, se você desejar que as VMs implantadas nesta rede virtual estejam localizadas fisicamente no leste dos EUA, selecione esse local. Você não pode alterar a região associada à sua rede virtual depois de criá-la.

4. Na página **Servidores DNS e Conectividade de VPN**, insira as seguintes informações e, em seguida, clique na seta avançar no canto inferior direito. Para obter mais informações sobre as configurações nessa página, consulte a página [Servidores DNS e conectividade de VPN](https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETDNS).

	- **Servidores DNS** – insira o nome do servidor DNS e o endereço IP ou selecione um servidor DNS previamente registrado no menu suspenso. Essa configuração não cria um servidor DNS. Ela permite que você especifique os servidores DNS que deseja usar para a resolução de nomes para essa rede virtual.
	- **Configurar VPN site a site** – marque a caixa de seleção **Configurar uma VPN site a site**.
	- **Selecione Rota Expressa** – marque a caixa de seleção **Usar Rota Expressa**. Essa opção aparece somente se você selecionou ***Configurar uma VPN site a site*** na etapa anterior.
	- **Rede local** – uma rede local representa sua localização física no local. Você pode selecionar uma rede local que já criou ou pode criar uma nova rede local.

	Se você selecionar uma rede local existente, pule a etapa 5.

5. Se estiver criando uma nova rede local, você verá a página **Conectividade site a site**. Se você tiver selecionado uma rede local que já criou, essa página não será exibida no assistente e você pode ir para a próxima seção. Para configurar sua rede local, digite as informações a seguir e clique na seta de avanço. Para obter mais informações sobre as configurações nessa página, consulte a página [Conectividade site a site](https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETSITE).

	- **Nome** – o nome que você deseja dar ao site de rede local (local).
	- **Espaço de endereço** – Incluindo o IP Inicial e o CIDR (Contagem de Endereços). Você pode especificar qualquer intervalo de endereços, desde que ele não sobreponha o intervalo de endereços para sua rede virtual.
	- **Adicionar espaço de endereço** – Essa configuração não é relevante para a Rota Expressa.
	- 
**Observação:** é necessário criar um site de rede local para a Rota Expressa. Os prefixos do endereço especificados para o site de rede local serão ignorados. Prefixos de endereço anunciados à Microsoft através do circuito da Rota Expressa serão usados para fins de roteamento.

6. Na página **Espaços de endereço de rede virtual**, insira as seguintes informações e clique na marca de seleção na parte inferior direita para configurar sua rede. Há muitas regras quanto ao espaço de endereço de rede virtual. Portanto, convém consultar a página [Espaços de endereço de rede virtual](https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNET_ADDRESS) para obter mais informações.

	- **Espaço de endereço** – incluindo o IP inicial e a contagem de endereços. Garanta que os espaços de endereço que você especificar não sobreponham nenhum espaço de endereço que você tenha em sua rede local.
	- **Adicionar sub-rede** – incluindo o IP Inicial e a Contagem de Endereços. Não são necessárias sub-redes adicionais, mas convém criar uma sub-rede separada para as VMs que terão DIPS (endereços IP dinâmicos). Ou você pode colocar suas VMs em uma sub-rede separada das instâncias de PaaS (Plataforma como um Serviço).
	- **Adicionar sub-rede de gateway** - clique para adicionar a sub-rede de gateway. A sub-rede de gateway é usada apenas para o gateway de rede virtual e é necessária para esta configuração. ***Importante:*** a sub-rede de gateway para a Rota Expressa deve ser /28 ou maior.

7. Clique na marca de seleção no canto inferior direito da página, e sua rede virtual começará a ser criada. Quando ela for concluída, você verá a indicação **Criada** listada em **Status** na página **Redes** no Portal de Gerenciamento.

8. Na página **Redes** página, clique na rede virtual que você acabou de criar e em **Painel**.
9. Na parte inferior da página Painel, clique em **CRIAR GATEWAY** e em **Sim**.

10. Quando a criação do gateway for iniciada, você verá uma mensagem informando que o gateway foi iniciado. Pode levar até 15 minutos para que o gateway seja criado.
11. **Vincule sua rede a um circuito.** Prossiga com as instruções a seguir somente depois de confirmar que o circuito passou para o seguinte estado e status: 

	- ServiceProviderState: provisionado
	- Status: Habilitado

	Verifique se você tem pelo menos uma rede virtual do Azure com um gateway criado. A sub-rede de gateway deve ser /28 ou maior para trabalhar com uma conexão de Rota Expressa, e deve estar ativada e em execução.

			PS C:> $Vnet = "MyTestVNet"
			New-AzureDedicatedCircuitLink -ServiceKey $ServiceKey -VNetName $Vnet
			Provisioned

## Próximas etapas
Se quiser adicionar máquinas virtuais à sua rede virtual, consulte [Como criar uma máquina virtual personalizada](../virtual-machines-create-custom.md).

Para obter mais informações sobre a Rota Expressa, consulte [Visão geral técnica da Rota Expressa](expressroute-introduction.md).

Para saber mais sobre Redes Virtuais do Azure, consulte [Perguntas frequentes de rede virtual](https://msdn.microsoft.com/library/azure/dn133803.aspx).

 

<!---HONumber=July15_HO4-->