<properties
   pageTitle="Mova os circuitos de Rota Expressa do Clássico para o Gerenciador de Recursos | Microsoft Azure"
   description="Esta página descreve como mover um circuito clássico para o modelo de implantação do Gerenciador de Recursos."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/19/2016"
   ms.author="ganesr"/>


# Mova os circuitos de Rota Expressa do modelo de implantação Clássico para o Gerenciador de Recursos

## Pré-requisitos de configuração

- Você precisará da versão mais recente dos módulos do Azure PowerShell (pelo menos a versão 1.0).
- Certifique-se de que você leu os [pré-requisitos](expressroute-prerequisites.md), os [requisitos de roteamento](expressroute-routing.md) e os [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.
- Antes de proceder, examine as informações fornecidas em [Movendo um circuito de Rota Expressa do Clássico para o Gerenciador de Recursos](expressroute-move.md). Certifique-se de que você compreendeu totalmente os limites e limitações do que é possível.
- Se você quiser mover um circuito de Rota Expressa do Azure do modelo de implantação clássico para o modelo de implantação do Azure Resource Manager, você deverá ter o circuito totalmente configurado e operacional no modelo de implantação clássico.
- Verifique se você tem um grupo de recursos que foi criado no modelo de implantação do Gerenciador de Recursos.

## Mova o circuito de Rota Expressa para o modelo de implantação do Gerenciador de Recursos

Você deverá mover um circuito de Rota Expressa para o modelo de implantação do Gerenciador de Recursos para que você possa usá-lo tanto no modelo de implantação Clássico quanto no do Gerenciador de Recursos. Podemos fazer isso executando os comandos do PowerShell a seguir.

### Etapa 1: Coletar detalhes do circuito do modelo de implantação clássico

Você precisa coletar informações sobre o circuito de Rota Expressa primeiro.

Entre no ambiente clássico do Azure e obtenha a chave de serviço. Você pode usar o seguinte trecho do PowerShell para coletar as informações:

	# Sign in to your Azure account
	Add-AzureAccount

	# Select the appropriate Azure subscription
	Select-AzureSubscription "<Enter Subscription Name here>"

	# Import the PowerShell modules for Azure and ExpressRoute
	Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

	# Get the service keys of all your ExpressRoute circuits
	Get-AzureDedicatedCircuit

Copie a **chave de serviço** do circuito que você deseja mover para o modelo de implantação do Gerenciador de Recursos.

### Etapa 2: Entrar no ambiente do Gerenciador de Recursos e criar um novo grupo de recursos

Você pode criar um novo grupo de recursos usando o seguinte trecho:

	# Sign in to your Azure Resource Manager environment
	Login-AzureRmAccount

	# Select the appropriate Azure subscription
	Get-AzureRmSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzureRmSubscription

	#Create a new resource group if you don't already have one
	New-AzureRmResourceGroup -Name "DemoRG" -Location "West US"

Você também poderá usar um grupo de recursos existente, se já tiver um.

### Etapa 3: Mover o circuito de Rota Expressa para o modelo de implantação do Gerenciador de Recursos

Agora você está pronto para mover o circuito de Rota Expressa do modelo de implantação Clássico para o do Gerenciador de Recursos. Examine as informações fornecidas em [Movendo circuitos de Rota Expressa do modelo de implantação Clássico para o Gerenciador de Recursos](expressroute-move.md) antes de continuar.

Você pode fazê-lo executando o seguinte trecho:

	Move-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"

>[AZURE.NOTE] Após a movimentação, o novo nome que está relacionado no cmdlet anterior será usado para o recurso de endereço. O circuito essencialmente será renomeado.

## Habilitar um circuito de Rota Expressa para ambos os modelos de implantação

Você deve mover seu circuito de Rota Expressa para o modelo de implantação do Gerenciador de Recursos antes de controlar o acesso para o modelo de implantação.

Execute o seguinte cmdlet para habilitar o acesso a ambos os modelos de implantação:

    # Get details of the ExpressRoute circuit
    $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"

    #Set "Allow Classic Operations" to TRUE
    $ckt.AllowClassicOperations = $true

    # Update circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

Depois que a operação for concluída com êxito, você poderá exibir o circuito no modelo de implantação clássico.

Execute o seguinte para obter os detalhes do circuito de Rota Expressa:

    get-azurededicatedcircuit

Você deve ser capaz de ver a chave de serviço relacionada. Agora você pode gerenciar links para o circuito de Rota Expressa usando os comandos de modelo clássico de implantação padrão para redes virtuais clássicas e comandos padrão do ARM para redes virtuais ARM. Os artigos a seguir explicam como gerenciar links para o circuito de Rota Expressa:

- [Vincule sua rede virtual ao circuito de Rota Expressa no modelo de implantação do Gerenciador de Recursos](expressroute-howto-linkvnet-arm.md)
- [Vincule sua rede virtual ao circuito de Rota Expressa no modelo de implantação clássico](expressroute-howto-linkvnet-classic.md)


## Desabilitar o circuito de Rota Expressa para o modelo de implantação clássico

Execute o seguinte cmdlet para desabilitar o acesso ao modelo de implantação clássico:

    # Get details of the ExpressRoute circuit
    $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"

    #Set "Allow Classic Operations" to FALSE
    $ckt.AllowClassicOperations = $false

    # Update circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

Depois que a operação for concluída com êxito, você não poderá exibir o circuito no modelo de implantação clássico.

## Próximas etapas

Depois de criar seu circuito, faça o seguinte:

- [Criar e modificar o roteamento do circuito da Rota Expressa](expressroute-howto-routing-arm.md)
- [Vincular a rede virtual ao circuito da Rota Expressa](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0720_2016-->