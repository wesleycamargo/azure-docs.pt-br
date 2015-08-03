<properties 
   pageTitle="Compartilhando um circuito de Rota Expressa entre várias assinaturas | Microsoft Azure"
   description="Este artigo mostra como compartilhar seu circuito de Rota Expressa entre várias assinaturas do Azure."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="jdial"
   editor="tysonn" />
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/20/2015"
   ms.author="cherylmc" />

# Compartilhando um circuito de Rota Expressa entre várias assinaturas

Um circuito único de Rota Expressa pode ser compartilhado entre várias assinaturas. A **Figura 1** mostra um esquema simples do compartilhamento de circuitos da Rota Expressa entre várias assinaturas.

Cada uma das nuvens menores dentro da nuvem grande é usada para representar assinaturas pertencentes a diferentes departamentos dentro de uma organização. Cada um dos departamentos da organização pode usar sua própria assinatura para implantar seus serviços, mas pode compartilhar um único circuito de Rota Expressa dedicado para conectar-se à rede corporativa. Um único departamento (neste exemplo: IT) pode ter o circuito de Rota Expressa dedicado. Cobranças pela largura de banda e conectividade para o circuito dedicado serão aplicadas ao proprietário do circuito dedicado. Outras assinaturas dentro da organização podem usar o circuito de Rota Expressa.

**Figura 1**

![Compartilhamento de assinatura](./media/expressroute-share-circuit/IC766124.png)

## Administração

O *proprietário do circuito* é o administrador/coprodutos-administrator da assinatura na qual o circuito de Rota Expressa dedicado é criado. O proprietário do circuito pode autorizar administradores/coadministradores de outras assinaturas (chamados de *usuários do circuito* no diagrama do fluxo de trabalho) a usar o circuito dedicado que eles possuem. Os usuários do circuito autorizados a usar o circuito dedicado da organização podem vincular a VNet de sua assinatura ao circuito dedicado após serem autorizados.

O proprietário do circuito tem a capacidade de modificar e revogar autorizações a qualquer momento. Revogar uma autorização fará com que todos os links sejam excluídos da assinatura cujo acesso foi revogado.

## Fluxo de trabalho

1. O proprietário do circuito autoriza os administradores de outras assinaturas a usar o circuito especificado. No exemplo a seguir, o administrador do circuito (TI da Contoso) permite que o administrador de outra assinatura (Vendas da Contoso), ao especificar sua ID da Microsoft (Live), vincule até 2 VNETs ao circuito. O cmdlet não envia um email para a ID da Microsoft especificada. O proprietário do circuito precisa notificar explicitamente o outro proprietário da assinatura de que a autorização for concluída.

		PS C:> New-AzureDedicatedCircuitLinkAuthorization -ServiceKey '6ed7e310-1a02-4261-915f-6ccfedc416f1' -Description 'SalesTeam' -Limit 2 -MicrosoftIds 'salesadmin@contoso.com'
		
		Description         : SalesTeam 
		Limit               : 2 
		LinkAuthorizationId : e2bc2645-6fd4-44a4-94f5-f2e43e6953ed 
		MicrosoftIds        : salesadmin@contoso.com 
		Used                : 0

1. Após ser notificado pelo proprietário do circuito, o administrador da assinatura autorizada pode executar o seguinte cmdlet para recuperar a chave de serviço do circuito. Neste exemplo, o administrador de Vendas da Contoso deverá entrar usando a ID da Microsoft especificada, salesadmin@contoso.com.

		PS C:> Get-AzureAuthorizedDedicatedCircuit
		
		Bandwidth                        : 100
		CircuitName                      : ContosoIT
		Location                         : Washington DC
		MaximumAllowedLinks              : 2
		ServiceKey                       : 6ed7e310-1a02-4261-915f-6ccfedc416f1
		ServiceProviderName              : ###########
		ServiceProviderProvisioningState : Provisioned
		Status                           : Enabled
		UsedLinks                        : 0

1. O administrador da assinatura autorizado executa o cmdlet a seguir para concluir a operação de vinculação.

		PS C:> New-AzureDedicatedCircuitLink –servicekey 6ed7e310-1a02-4261-915f-6ccfedc416f1 –VnetName 'SalesVNET1' 
		
			State VnetName 
			----- -------- 
			Provisioned SalesVNET1

E isso é tudo. A VNet das Vendas da Contoso no Azure agora está vinculada a um circuito de propriedade e criado pela TI da Contoso.

## Gerenciando a autorização

O proprietário do circuito pode compartilhar um circuito com até 10 assinaturas do Azure. O proprietário do circuito pode ver que foi autorizado para o circuito. O proprietário pode revogar a autorização a qualquer momento. Quando o proprietário do circuito revoga uma autorização, identificada por LinkAuthorizationId, todos os links permitidos pela autorização serão excluídos imediatamente. As VNETs vinculadas perderão a conectividade com a rede local por meio do circuito Rota Expressa.

	PS C:> Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: 6ed7e310-1a02-4261-915f-6ccfedc416f1 
	
	Description         : EngineeringTeam 
	Limit               : 3 
	LinkAuthorizationId : cc958457-c8c1-4f16-af09-e7f099da64bf 
	MicrosoftIds        : engadmin@contoso.com 
	Used                : 1 
	
	Description         : MarketingTeam 
	Limit               : 1 
	LinkAuthorizationId : d972726f-c7b9-4658-8598-ad3208ac9348 
	MicrosoftIds        : marketingadmin@contoso.com 
	Used                : 0 
	
	Description         : SalesTeam 
	Limit               : 2 
	LinkAuthorizationId : e2bc2645-6fd4-44a4-94f5-f2e43e6953ed 
	MicrosoftIds        : salesadmin@contoso.com 
	Used                : 2 
	
	PS C:> Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey '6ed7e310-1a02-4261-915f-6ccfedc416f1' -AuthorizationId 'e2bc2645-6fd4-44a4-94f5-f2e43e6953ed'


O diagrama a seguir mostra o Fluxo de trabalho de autorização:

![Fluxo de trabalho do compartilhamento de assinatura](./media/expressroute-share-circuit/IC759525.png)

## Próximas etapas

Para obter mais informações sobre a Rota Expressa, consulte [Visão geral da Rota Expressa](expressroute-introduction.md).

<!---HONumber=July15_HO4-->