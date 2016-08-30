
<properties
   pageTitle="Criar um gateway de aplicativo usando os modelos do Gerenciador de Recursos do Azure | Microsoft Azure"
   description="Esta página oferece instruções para criar um gateway de aplicativo do Azure usando o modelo do Gerenciador de Recursos do Azure"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="jdial"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/09/2016"
   ms.author="gwallace"/>


# Criar um gateway de aplicativo usando o modelo do Gerenciador de Recursos do Azure

O Azure Application Gateway é um balanceador de carga de camada 7. Ele fornece o failover e solicitações HTTP de roteamento de desempenho entre diferentes servidores, estejam eles na nuvem ou no local. O Application Gateway tem os seguintes recursos de entrega de aplicativo: balanceamento de carga HTTP, afinidade de sessão baseada em cookie e descarregamento SSL.

> [AZURE.SELECTOR]
- [Portal do Azure](application-gateway-create-gateway-portal.md)
- [PowerShell do Azure Resource Manager](application-gateway-create-gateway-arm.md)
- [Azure Classic PowerShell](application-gateway-create-gateway.md)
- [Modelo do Azure Resource Manager ](application-gateway-create-gateway-arm-template.md)

<BR>

Você aprenderá a baixar e a modificar um modelo existente do Azure Resource Manager do GitHub e a implantar o modelo do GitHub, do PowerShell e da CLI do Azure.

Se você estiver simplesmente implantando o modelo do Gerenciador de Recursos do Azure diretamente do GitHub, sem nenhuma alteração, ignore para implantar um modelo do GitHub.


## Cenário

Neste cenário, você:

- Criará um gateway de aplicativo com duas instâncias.
- Criará uma rede virtual chamada VirtualNetwork1, com um bloco CIDR 10.0.0.0/16 reservado.
- Criará uma sub-rede chamada Appgatewaysubnet que usa 10.0.0.0/28 como seu bloco CIDR.
- Configurará dois IPs de back-end definidos anteriormente para os servidores Web nos quais deseja balancear a carga do tráfego. Neste exemplo de modelo, os IPs de back-end são 10.0.1.10 e 10.0.1.11.

>[AZURE.NOTE] Essas configurações são os parâmetros para esse modelo. Para personalizar o modelo, você pode alterar as regras, o ouvinte e o SSL que abre o azuredeploy.json.



![Cenário](./media/application-gateway-create-gateway-arm-template/scenario-arm.png)



## Baixar e compreender o modelo do Gerenciador de Recursos do Azure

Você pode baixar o modelo existente do Gerenciador de Recursos do Azure para criar uma rede virtual e duas sub-redes do GitHub, fazer as alterações desejadas e reutilizá-la. Para isso, siga as etapas abaixo:

1. Navegue até [Criar Application Gateway](https://github.com/Azure/azure-quickstart-templates/tree/master/101-application-gateway-create).
2. Clique em **azuredeploy.json** e em **RAW**.
3. Salve o arquivo em uma pasta local do computador.
4. Se você estiver familiarizado com os modelos do Gerenciador de Recursos do Azure, pule para a etapa 7.
5. Abra o arquivo que você salvou e examine o conteúdo em **parâmetros** na linha 5. Os parâmetros do modelo do Gerenciador de Recursos do Azure fornecem um espaço reservado para valores que podem ser preenchidos durante a implantação.

	| Parâmetro | Descrição |
	|---|---|
	| **local** | Região do Azure onde o gateway de aplicativo é criado |
	| **VirtualNetwork1** | Nome para a nova rede virtual |
	| **addressPrefix** | Espaço de endereço para a rede virtual, em formato CIDR |
	| **ApplicationGatewaysubnet** | Nome para a sub-rede de gateway de aplicativo |
	| **subnetPrefix** | Bloco CIDR para a sub-rede do gateway de aplicativo |
	| **skuname** | Tamanho da instância SKU |
	| **capacidade** | Número de instâncias |
	| **backendaddress1** | Endereço IP do primeiro servidor Web |
	| **backendaddress2** | Endereço IP do segundo servidor Web |


>[AZURE.IMPORTANT] Os modelos do Gerenciador de Recursos do Azure mantidos no GitHub podem mudar ao longo do tempo. Verifique o modelo antes de usá-lo.

6. Verifique o conteúdo em **resources** e observe o seguinte:

	- **type**. Tipo de recurso que está sendo criado pelo modelo. Nesse caso, o tipo é **Microsoft.Network/applicationGateways**, que representa um application gateway.
	- **name**. Nome do recurso. Observe o uso de **[parameters('applicationGatewayName')]**, que significa o nome que é fornecido como entrada por você ou um arquivo de parâmetro durante a implantação.
	- **properties**. Lista de propriedades do recurso. Esse modelo usa a rede virtual e o endereço IP público durante a criação do gateway de aplicativo.

7. Navegue de volta para [https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-create/](https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-create).
8. Clique em **azuredeploy-paremeters.json** e em **RAW**.
9. Salve o arquivo em uma pasta local do computador.
10. Abra o arquivo que você salvou e edite os valores dos parâmetros. Use os valores a seguir para implantar o gateway de aplicativo descrito em nosso cenário.

		{
		  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
		{
    	"location" : {
        "value" : "West US"
    	},
    	"addressPrefix": {
        "value": "10.0.0.0/16"
    	},
    	"subnetPrefix": {
        "value": "10.0.0.0/24"
    	},
    	"skuName": {
        "value": "Standard_Small"
    	},
    	"capacity": {
        "value": 2
    	},
    	"backendIpAddress1": {
        "value": "10.0.1.10"
    	},
    	"backendIpAddress2": {
        "value": "10.0.1.11"
    	}
		}

11. Salve o arquivo. Você pode testar o modelo JSON e o modelo de parâmetro usando ferramentas de validação de JSON online, como [JSlint.com](http://www.jslint.com/).

## Implantar o modelo do Gerenciador de Recursos do Azure usando o PowerShell

Se você nunca usou o Azure PowerShell, confira [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) e siga as instruções para entrar no Azure e selecionar sua assinatura.

### Etapa 1

	Login-AzureRmAccount



### Etapa 2

Verificar as assinaturas da conta.

	Get-AzureRmSubscription

Você deve se autenticar com suas credenciais.<BR>

### Etapa 3

Escolha quais das suas assinaturas do Azure deseja usar.<BR>


	Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### Etapa 4


Se necessário, crie um grupo de recursos usando o cmdlet **New-AzureResourceGroup**. No exemplo abaixo, você cria um novo grupo de recursos chamado AppgatewayRG no local Leste dos EUA.

	New-AzureRmResourceGroup -Name AppgatewayRG -Location "East US"
		VERBOSE: 5:38:49 PM - Created resource group 'AppgatewayRG' in location 'eastus'


		ResourceGroupName : AppgatewayRG
		Location          : eastus
		ProvisioningState : Succeeded
		Tags              :
		Permissions       :
	                 Actions  NotActions
	                 =======  ==========
	                  *

		ResourceId        : /subscriptions/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/resourceGroups/AppgatewayRG

Execute o cmdlet **New-AzureRmResourceGroupDeployment** para implantar a nova rede virtual usando os arquivos de modelo e parâmetro anteriores que você baixou e modificou.

	New-AzureRmResourceGroupDeployment -Name TestAppgatewayDeployment -ResourceGroupName AppgatewayRG `
 		-TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json

A saída gerada pela linha de comando é a seguinte:

	DeploymentName    : testappgatewaydeployment
	ResourceGroupName : appgatewayRG
	ProvisioningState : Succeeded
	Timestamp         : 9/19/2015 1:49:41 AM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
				Name             Type                       Value
				===============  =========================  ==========
				location         String                     East US
				addressPrefix    String                     10.0.0.0/16
				subnetPrefix     String                     10.0.0.0/24
				skuName          String                     Standard_Small
				capacity         Int                        2
				backendIpAddress1  String                     10.0.1.10
				backendIpAddress2  String                     10.0.1.11

	Outputs           :


## Implantar o modelo do Gerenciador de Recursos do Azure usando a CLI do Azure

Para implantar o modelo do Gerenciador de Recursos do Azure baixado usando a CLI do Azure, siga as etapas abaixo:

### Etapa 1

Se você nunca usou a CLI do Azure, confira [Instalar e configurar a CLI do Azure](../xplat-cli-install.md) e siga as instruções até o ponto em que você seleciona sua conta e assinatura do Azure.
### Etapa 2

Execute o comando **azure config mode** para alternar para o modo do Gerenciador de Recursos, como mostrado abaixo.

	azure config mode arm

Este é o resultado esperado para o comando descrito acima:

	info:	New mode is arm

### Etapa 3

Se necessário, execute o comando **azure group create** para criar um novo grupo de recursos, como mostrado abaixo. Observe a saída do comando. A lista exibida após a saída explicar os parâmetros usados. Para saber mais sobre grupos de recursos, visite [Visão geral do Azure Resource Manager](../resource-group-overview.md).

	azure group create -n appgatewayRG -l eastus

**-n (or --name)**. Nome do novo grupo de recursos. Para nosso cenário, é *appgatewayRG*.

**-l (ou --location)**. Região do Azure onde o novo grupo de recursos é criado. Para nosso cenário, é *eastus*.

### Etapa 4

Execute o cmdlet **azure group deployment create** para implantar a nova rede virtual usando o modelo e os arquivos de parâmetro que você baixou e modificou acima. A lista exibida após a saída explicar os parâmetros usados.

	azure group deployment create -g appgatewayRG -n TestAppgatewayDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json

Este é o resultado esperado para o comando descrito acima:

	azure group deployment create -g appgatewayRG -n TestAppgatewayDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json
	info:    Executing command group deployment create
	+ Initializing template configurations and parameters
	+ Creating a deployment
	info:    Created template deployment "TestAppgatewayDeployment"
	+ Waiting for deployment to complete
	data:    DeploymentName     : TestAppgatewayDeployment
	data:    ResourceGroupName  : appgatewayRG
	data:    ProvisioningState  : Succeeded
	data:    Timestamp          : 2015-09-21T20:50:27.5129912Z
	data:    Mode               : Incremental
	data:    Name               Type    Value
	data:    -----------------  ------  --------------
	data:    location           String  East US
	data:    addressPrefix      String  10.0.0.0/16
	data:    subnetPrefix       String  10.0.0.0/24
	data:    skuName            String  Standard_Small
	data:    capacity           Int     2
	data:    backendIpAddress1  String  10.0.1.10
	data:    backendIpAddress2  String  10.0.1.11
	info:    group deployment create command OK

**-g (ou --resource-group)**. Nome do grupo de recursos em que a nova rede virtual é criada.

**-f (ou --template-file)**. Caminho para o arquivo de modelo do Gerenciador de Recursos do Azure.

**-e (ou --parameters-file)**. Caminho para o arquivo de parâmetros do Gerenciador de Recursos do Azure.

## Implantar o modelo do Gerenciador de Recursos do Azure usando o clique para implantar

Clique para implantar é outra maneira de usar modelos do Gerenciador de Recursos do Azure. É uma maneira fácil de usar modelos com o portal do Azure.


### Etapa 1
Acesse [Criar um application gateway com IP público](https://azure.microsoft.com/documentation/templates/101-application-gateway-public-ip/).


### Etapa 2

Clique em **Implantar no Azure**.

![Implantar no Azure](./media/application-gateway-create-gateway-arm-template/deploytoazure.png)

### Etapa 3

Preencha os parâmetros do modelo de implantação no portal e clique em **OK**.

![Parâmetros](./media/application-gateway-create-gateway-arm-template/ibiza1.png)

### Etapa 4

Selecione **Termos legais** e clique em **Comprar**.

### Etapa 5

Na folha Implantação personalizada, clique em **Criar**.



## Próximas etapas

Se desejar configurar o descarregamento SSL, confira [Configurar um application gateway para descarregamento SSL](application-gateway-ssl.md).

Para configurar um gateway de aplicativo para usar com um balanceador de carga interno, confira [Criar um gateway de aplicativo com um ILB (balanceador de carga interno)](application-gateway-ilb.md).

Se deseja obter mais informações sobre as opções de balanceamento de carga no geral, consulte:

- [Balanceador de carga do Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gerenciador de Tráfego do Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_0824_2016-->