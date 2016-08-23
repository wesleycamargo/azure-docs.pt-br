<properties
   pageTitle="Vincular uma rede virtual a um circuito de Rota Expressa usando o modelo de implantação do Gerenciador de Recursos e o Portal do Azure| Microsoft Azure"
   description="Este documento apresenta uma visão geral de como vincular redes virtuais (VNets) a circuitos da Rota Expressa."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/08/2016"
   ms.author="cherylmc" />

# Vincular uma rede virtual a um circuito de Rota Expressa

> [AZURE.SELECTOR]
- [Portal do Azure - Gerenciador de Recursos](expressroute-howto-linkvnet-portal-resource-manager.md)
- [PowerShell – Resource Manager](expressroute-howto-linkvnet-arm.md)
- [PowerShell - clássico](expressroute-howto-linkvnet-classic.md)



Este artigo ajudará você a vincular as redes virtuais (VNets) aos circuitos de Rota Expressa do Azure usando o modelo de implantação do Gerenciador de Recursos e o Portal do Azure. As redes virtuais podem estar na mesma assinatura ou fazer parte de outra assinatura.


**Sobre modelos de implantação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Pré-requisitos de configuração

- Certifique-se de que você leu os [pré-requisitos](expressroute-prerequisites.md), os [requisitos de roteamento](expressroute-routing.md) e os [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.
- Você deve ter um circuito da Rota Expressa ativo.
	- Siga as instruções para [criar um circuito da Rota Expressa](expressroute-howto-circuit-arm.md) e para que o circuito seja habilitado pelo provedor de conectividade.

	- Verifique se o emparelhamento privado do Azure está configurado para seu circuito. Veja o artigo [Configurar roteamento](expressroute-howto-routing-portal-resource-manager.md) para obter instruções sobre roteamento.

	- Verifique se o emparelhamento privado do Azure está configurado e se o emparelhamento BGP entre sua rede e a Microsoft está ativo para que você possa habilitar a conectividade de ponta a ponta.

	- Verifique se tem uma rede virtual e um gateway de rede virtual criados e totalmente provisionados. Siga as instruções para criar um [Gateway de VPN](../articles/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) (siga somente as etapas 1-5).

Você pode vincular até 10 redes virtuais a um circuito de Rota Expressa padrão. Todas as redes virtuais deverão estar na mesma região geopolítica ao usar um circuito de Rota Expressa padrão. Você poderá vincular uma rede virtual fora da região geopolítica do circuito da Rota Expressa ou conectar um grande número de redes virtuais ao circuito de Rota Expressa, se tiver habilitado o complemento premium da Rota Expressa. Confira as [perguntas frequentes](expressroute-faqs.md) para obter mais detalhes sobre o complemento premium.

## Conectar uma rede virtual na mesma assinatura a um circuito


### Para criar uma conexão

1. Certifique-se de que o circuito de Rota Expressa e emparelhamento privado do Azure foram configurados com êxito. Siga as instruções nos artigos para [Criar um circuito de Rota Expressa](expressroute-howto-circuit-arm.md) e [Configurar o roteamento](expressroute-howto-routing-arm.md). O circuito de Rota Expressa deve se parecer com a imagem a seguir.

	![Captura de tela de circuito da Rota Expressa](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png)

	>[AZURE.NOTE] As informações de configuração do BGP não aparecerão se o provedor de camada 3 tiver configurado seus emparelhamentos. Se o circuito estiver no estado de provisionamento, você poderá criar conexões.

2. Agora, você pode começar a provisionar uma conexão para vincular seu gateway de rede virtual ao circuito de Rota Expressa. Clique em **Conexão** > **Adicionar** para abrir a folha **Adicionar conexão** e, em seguida, configure os valores. Consulte o seguinte exemplo de referência.


	![Adicionar a captura de tela de conexão](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png)


3. Depois que sua conexão foi configurada com êxito, seu objeto de conexão mostrará as informações para a conexão.

	![Captura de tela de objeto de conexão](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)


### Para excluir uma conexão

Você pode excluir uma conexão selecionando o ícone **Excluir** na folha de sua conexão.

## Conectar uma rede virtual em uma assinatura diferente a um circuito

Neste momento, você não pode conectar as redes virtuais nas assinaturas usando o Portal do Azure. No entanto, você pode usar o PowerShell para fazer isso. Confira o artigo [PowerShell](expressroute-howto-linkvnet-arm.md) para obter mais informações.

## Próximas etapas

Para obter mais informações sobre a Rota Expressa, consulte [Perguntas Frequentes sobre Rota Expressa](expressroute-faqs.md).

<!---HONumber=AcomDC_0810_2016-->