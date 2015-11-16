<properties 
   pageTitle="Vinculando redes virtuais a circuitos da Rota Expressa | Microsoft Azure"
   description="Este documento apresenta uma visão geral de como vincular redes virtuais (VNets) a circuitos da Rota Expressa."
   services="expressroute"
   documentationCenter="na"
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
   ms.date="11/02/2015"
   ms.author="cherylmc" />

# Vinculando redes virtuais a circuitos da Rota Expressa

> [AZURE.SELECTOR]
- [PowerShell - Classic](expressroute-howto-linkvnet-classic.md)
- [Template - Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/ecad62c231848ace2fbdc36cbe3dc04a96edd58c/301-expressroute-circuit-vnet-connection)

Este artigo apresenta uma visão geral de como vincular redes virtuais a circuitos da Rota Expressa. As redes virtuais podem estar na mesma assinatura ou fazerem parte de outra assinatura. Este artigo aplica-se a Redes Virtuais implantadas com o modelo de implantação clássica. Se desejar vincular uma rede virtual implantada com o método de implantação do Gerenciador de Recursos do Azure, você poderá fazer isso usando um modelo. Veja a guia acima para navegar até o modelo.

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

## Pré-requisitos de configuração

- Você precisará da versão mais recente dos módulos do Azure PowerShell. Baixe o módulo mais recente do PowerShell na seção PowerShell da [página Downloads do Azure](http://azure.microsoft.com/downloads). Siga as instruções na página [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter orientações detalhadas sobre como configurar o computador para usar os módulos do Azure PowerShell. 
- Leia as páginas de [pré-requisitos](expressroute-prerequisites.md), [requisitos de roteamento](expressroute-routing.md) e [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.
- Você deve ter um circuito da Rota Expressa ativo. 
	- Siga as instruções para [criar um circuito da Rota Expressa](expressroute-howto-circuit-classic.md) e para que o circuito seja habilitado pelo provedor de conectividade. 
	- Verifique se o emparelhamento privado do Azure está configurado para seu circuito. Veja o artigo [Configurar roteamento](expressroute-howto-routing-classic.md) para obter instruções sobre roteamento. 
	- O emparelhamento privado do Azure deve estar configurado e o emparelhamento BGP entre a rede e a Microsoft deve estar em atividade para que você habilite a conectividade de ponta a ponta.

Você pode vincular até 10 redes virtuais a um circuito da Rota Expressa. Todos os circuitos da Rota Expressa devem estar na mesma região geopolítica. É possível vincular um grande número de redes virtuais ao circuito da Rota Expressa se você tiver habilitado o complemento premium da Rota Expressa. Confira as [perguntas frequentes](expressroute-faqs.md) para obter mais detalhes sobre o complemento premium.

## Vinculando uma rede virtual na mesma assinatura do Azure a um circuito da Rota Expressa

Você pode vincular uma rede virtual a um circuito da Rota Expressa usando o cmdlet a seguir. Verifique se o gateway de rede virtual foi criado e se está pronto para vinculação antes de executar o cmdlet.

	C:\> New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
	Provisioned

## Vinculando uma rede virtual em uma assinatura diferente do Azure a um circuito da Rota Expressa

Um circuito da Rota Expressa pode ser compartilhado entre várias assinaturas. A figura abaixo mostra um esquema simples de como funciona o compartilhamento de circuitos da Rota Expressa entre várias assinaturas. Cada uma das nuvens menores dentro da nuvem grande é usada para representar assinaturas pertencentes a diferentes departamentos dentro de uma organização. Cada um dos departamentos dentro da organização pode usar sua própria assinatura para implantar seus serviços, mas pode compartilhar um único circuito da Rota Expressa para se conectar de volta à respectiva rede local. Um único departamento (neste exemplo: TI) pode ter o circuito da Rota Expressa. Outras assinaturas dentro da organização podem usar o circuito de Rota Expressa.

>[AZURE.NOTE]As cobranças por conectividade e largura de banda do circuito dedicado serão aplicadas ao proprietário do circuito da Rota Expressa. Todas as redes virtuais compartilham a mesma largura de banda.

![Conectividade entre assinaturas](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### Administração

O *proprietário do circuito* é o administrador/coadministrador da assinatura na qual o circuito da Rota Expressa foi criado. O proprietário do circuito pode autorizar administradores/coadministradores de outras assinaturas (chamados de *usuário do circuito* no diagrama do fluxo de trabalho) a usar o circuito dedicado que eles possuem. Os usuários do circuito autorizados a usar o circuito da Rota Expressa da organização podem vincular a rede virtual de sua assinatura ao circuito da Rota Expressa após serem autorizados.

O proprietário do circuito tem a capacidade de modificar e revogar autorizações a qualquer momento. Revogar uma autorização fará com que todos os links sejam excluídos da assinatura cujo acesso foi revogado.

### Operações do proprietário do circuito 

#### Criando uma autorização
	
O proprietário do circuito autoriza os administradores de outras assinaturas a usar o circuito especificado. No exemplo a seguir, o administrador do circuito (TI da Contoso) permite que o administrador de outra assinatura (Desenvolvimento-Teste), ao especificar sua ID da Microsoft, vincule até duas redes virtuais ao circuito. O cmdlet não envia um email para a ID da Microsoft especificada. O proprietário do circuito precisa notificar explicitamente o outro proprietário da assinatura de que a autorização for concluída.

		PS C:\> New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'
		
		Description         : Dev-Test Links 
		Limit               : 2 
		LinkAuthorizationId : ********************************** 
		MicrosoftIds        : devtest@contoso.com 
		Used                : 0

#### Examinando autorizações

O proprietário do circuito pode examinar todas as autorizações emitidas em um circuito específico executando o cmdlet a seguir.

	PS C:\> Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"
	
	Description         : EngineeringTeam 
	Limit               : 3 
	LinkAuthorizationId : #################################### 
	MicrosoftIds        : engadmin@contoso.com 
	Used                : 1 
	
	Description         : MarketingTeam 
	Limit               : 1 
	LinkAuthorizationId : @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ 
	MicrosoftIds        : marketingadmin@contoso.com 
	Used                : 0 
	
	Description         : Dev-Test Links 
	Limit               : 2 
	LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
	MicrosoftIds        : salesadmin@contoso.com 
	Used                : 2 
	

#### Atualizando autorizações

O proprietário do circuito pode modificar autorizações usando o cmdlet a seguir.

	PS C:\> set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5
		
	Description         : Dev-Test Links 
	Limit               : 5 
	LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
	MicrosoftIds        : devtest@contoso.com 
	Used                : 0


#### Excluindo autorizações

O proprietário do circuito pode revogar/excluir autorizações usando o cmdlet a seguir.

	PS C:\> Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"


### Operações do usuário do circuito

#### Examinando autorizações

O usuário do circuito pode examinar autorizações usando o cmdlet a seguir.

	PS C:\> Get-AzureAuthorizedDedicatedCircuit
		
	Bandwidth                        : 200
	CircuitName                      : ContosoIT
	Location                         : Washington DC
	MaximumAllowedLinks              : 2
	ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
	ServiceProviderName              : equinix
	ServiceProviderProvisioningState : Provisioned
	Status                           : Enabled
	UsedLinks                        : 0

#### Resgatando autorizações de link

O usuário de circuito pode executar o cmdlet a seguir para resgatar uma autorização de vínculo.

	PS C:\> New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1' 
		
	State VnetName 
	----- -------- 
	Provisioned SalesVNET1

## Próximas etapas

Para obter mais informações sobre a Rota Expressa, consulte [Perguntas Frequentes sobre Rota Expressa](expressroute-faqs.md).

<!---HONumber=Nov15_HO2-->