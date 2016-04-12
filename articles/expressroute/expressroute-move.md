<properties
   pageTitle="Movendo circuitos da Rota Expressa do Clássico para o Gerenciador de Recursos | Microsoft Azure"
   description="Esta página fornece uma visão geral do que você precisa saber sobre fazer uma ponte para os modelos de implantação clássico e do Gerenciador de Recursos."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/01/2016"
   ms.author="ganesr"/>

# Movendo circuitos da Rota Expressa do modelo de implantação Clássico para o Gerenciador de Recursos

Este artigo fornece uma visão geral do que significa mover um circuito da Rota Expressa do modelo de implantação clássico para o Gerenciador de Recursos.

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

Um circuito da Rota Expressa pode ser usado para conectar às redes virtuais (VNets) implantadas nos modelos de implantação Clássico e do Gerenciador de Recursos. Um circuito da Rota Expressa, independentemente de como é criado, agora pode vincular às redes virtuais nos dois modelos de implantação.

![](./media/expressroute-move/expressroute-move-1.png)

## Circuitos da Rota Expressa criados no modelo de implantação Clássico

Os circuitos de Rota Expressa criados no modelo de implantação clássico precisarão ser movidos para o modelo de implantação do Gerenciador de Recursos primeiro para habilitar a conectividade com os modelos de implantação clássico e do Gerenciador de Recursos. Não haverá perda de conectividade nem interrupção quando uma conexão estiver sendo movida. Todos os links de rede virtual do circuito no modelo de implantação clássico (dentro da mesma assinatura e entre as assinaturas) serão preservados. Depois da migração ser concluída com êxito, o circuito da Rota Expressa será parecido e executado exatamente como um circuito da Rota Expressa criado no modelo de implantação do Gerenciador de Recursos. Agora, você poderá criar conexões com as redes virtuais no modelo de implantação do Gerenciador de Recursos.

Depois de um circuito da Rota Expressa ser movido para o modelo de implantação do Gerenciador de Recursos, você poderá gerenciar o ciclo de vida do circuito da Rota Expressa usando apenas o modelo de implantação do Gerenciador de Recursos. Isso significa que operações como adicionar/atualizar/excluir emparelhamentos, atualizar propriedades do circuito como largura de banda, SKU e tipo de cobrança, e excluir circuitos só poderão ser executadas no modelo de implantação do Gerenciador de Recursos. Consulte a seção abaixo sobre os circuitos criados no modelo de implantação do gerenciador de recursos para obter mais detalhes sobre como você pode gerenciar o acesso a ambos os modelos de implantação.

Você não terá de envolver seu provedor de conectividade para realizar a migração.

## Circuitos da Rota Expressa criados no modelo de implantação do Gerenciador de Recursos

Você pode habilitar os circuitos da Rota Expressa criados no modelo de implantação do Gerenciador de Recursos para ser acessível a partir de ambos os modelos de implantação. Qualquer circuito da Rota Expressa em sua assinatura pode ser habilitado para ser acessado a partir de ambos os modelos de implantação.

- Os circuitos da Rota Expressa criados no modelo de implantação do Gerenciador de Recursos não terão acesso ao modelo de implantação clássico por padrão.
- Os circuitos da Rota Expressa movidos do modelo de implantação clássico para o modelo de implantação do Gerenciador de Recursos serão acessíveis a partir de ambos os modelos de implantação por padrão.
- Um circuito da Rota Expressa sempre terá acesso ao modelo de implantação do Gerenciador de Recursos, independentemente dele ter sido criado no modelo de implantação do Gerenciador de Recursos ou no modelo de implantação clássico. Isso significa que você pode criar conexões com as redes virtuais criadas no modelo de implantação do Gerenciador de Recursos seguindo as instruções em [como vincular as redes virtuais](expressroute-howto-linkvnet-arm.md). 
- O acesso ao modelo de implantação clássico é controlado pelo parâmetro "allowClassicOperations" no circuito da Rota Expressa. 

>[AZURE.IMPORTANT] Todas as cotas documentadas na página de [limites do serviço](../azure-subscription-service-limits.md) serão aplicadas. Por exemplo, um circuito padrão pode ter no máximo 10 links de VNet/conexões nos modelos de implantação clássico e do Gerenciador de Recursos.


### Controlando o acesso ao modelo de implantação clássico

Você pode habilitar um único circuito da Rota Expressa para vincular as VNets em ambos os modelos de implantação definindo o parâmetro "allowClassicOperations" do circuito da Rota Expressa.

Definir "allowClassicOperations" para TRUE permitirá vincular as vnets de ambos os modelos de implantação ao circuito da Rota Expressa. Você pode vincular às VNets no modelo de implantação clássico, seguindo as diretrizes em [como vincular as redes virtuais no modelo de implantação clássico](expressroute-howto-linkvnet-classic.md). Você pode vincular as VNets no modelo de implantação do Gerenciador de Recursos seguindo as diretrizes em [como vincular as redes virtuais no modelo de implantação do Gerenciador de Recursos](expressroute-howto-linkvnet-arm.md).

Definir "allowClassicOperations" para FALSE bloqueará o acesso ao circuito a partir do modelo de implantação clássico. Porém, todos os links da rede virtual no modelo de implantação clássico serão preservados. Nesse caso, o circuito da Rota Expressa não será visível no modelo de implantação clássico.

### Operações com suporte no modelo de implantação clássico

As seguintes operações clássicas têm suporte em um circuito da Rota Expressa quando "allowClassicOperations" é definido para TRUE.

 - Obter informações do Circuito da Rota Expressa
 - Criar/Atualizar/Obter/Excluir links da rede virtual para as VNets clássicas
 - Criar/Atualizar/Obter/Excluir autorizações de link da rede virtual para a conectividade entre assinaturas

Você não poderá executar as seguintes operações clássicas quando "allowClassicOperations" estiver definido para TRUE.

 - Criar/Atualizar/Obter/Excluir emparelhamentos BGP do Azure privado e dos emparelhamentos do Azure público e da Microsoft
 - Excluir um circuito da Rota Expressa

## Comunicação do Clássico com o Gerenciador de Recursos

O circuito da Rota Expressa atuará como uma ponte entre os modelos de implantação clássico e do Gerenciador de Recursos. O tráfego entre as máquinas virtuais implantadas nas vnets no modelo de implantação clássico e aquelas implantadas nas vnets no modelo de implantação do Gerenciador Recursos fluirá pela Rota Expressa se ambas as vnets forem vinculadas ao mesmo circuito da Rota Expressa. A taxa de transferência agregada será limitada pela capacidade da taxa de transferência do gateway da rede virtual. O tráfego não entrará no provedor de conectividade nem em suas redes em tais casos. O fluxo do tráfego entre as VNets estará totalmente contido na rede da Microsoft.

## Acesso aos recursos de emparelhamento do Azure Público e da Microsoft

Você pode continuar a acessar os recursos normalmente acessíveis por meio do emparelhamento do Azure Público e do emparelhamento da Microsoft sem interrupções.

## O que tem suporte

Esta seção descreve o que tem suporte com esse recurso

 - Um circuito da Rota Expressa pode ser usado para conectar as vnets implantadas nos modelos de implantação clássico e do Gerenciador de Recursos.
 - Você pode mover um circuito da Rota Expressa do modelo de implantação clássico para o Gerenciador de Recursos. Após a migração, o circuito da Rota Expressa será parecido e executado como qualquer outro circuito da Rota Expressa criado no modelo de implantação do Gerenciador de Recursos.
 - Apenas o circuito da Rota Expressa pode ser movido. Os links do circuito, VNets e gateways VPN não serão movidos por essa operação.
 - Depois de um circuito da Rota Expressa ser movido para o modelo de implantação do Gerenciador de Recursos, você poderá gerenciar o ciclo de vida do circuito da Rota Expressa usando apenas o modelo de implantação do Gerenciador de Recursos. Isso significa que operações como adicionar/atualizar/excluir emparelhamentos, atualizar propriedades do circuito como largura de banda, SKU e tipo de cobrança, e excluir circuitos só poderão ser executadas no modelo de implantação do Gerenciador de Recursos.
 - O circuito da Rota Expressa atuará como uma ponte entre os modelos de implantação clássico e do Gerenciador de Recursos. O tráfego entre as máquinas virtuais implantadas nas vnets no modelo de implantação clássico e aquelas implantadas nas vnets no modelo de implantação do Gerenciador Recursos fluirá pela Rota Expressa se ambas as vnets forem vinculadas ao mesmo circuito da Rota Expressa. 
 - Conectividade entre as assinaturas nos modelos de implantação clássico e do Gerenciador de Recursos.

## O que NÃO tem suporte

Esta seção descreve o que não tem suporte com esse recurso

 - Mover os links do circuito, gateways e redes virtuais do modelo de implantação clássico para o modelo do Gerenciador de Recursos.
 - Gerenciar o ciclo de vida do circuito da Rota Expressa a partir do modelo de implantação clássico.
 - Suporte RBAC para o modelo de implantação clássico. Você não conseguirá realizar os controles RBAC para o circuito no modelo de implantação clássico. Qualquer administrador/coadministrador da assinatura poderá vincular/desvincular as vnets para o circuito.

## Configuração

Siga as instruções descritas em [Movendo um circuito da Rota Expressa do clássico para o Gerenciador de Recursos](expressroute-howto-move-arm.md)

## Próximas etapas

- Para obter informações sobre o fluxo de trabalho, consulte [Fluxos de trabalho de provisionamento e estados do circuito da Rota Expressa](expressroute-workflows.md)
- Configurar sua conexão da Rota Expressa.

	- [Criar um circuito da Rota Expressa](expressroute-howto-circuit-arm.md)
	- [Configurar o roteamento](expressroute-howto-routing-arm.md)
	- [Vincular uma rede virtual a um circuito da Rota Expressa](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0406_2016-->