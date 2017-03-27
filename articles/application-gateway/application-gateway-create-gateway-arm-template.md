---
title: Criar um Gateway de Aplicativo do Azure - modelos | Microsoft Docs
description: "Esta página oferece instruções para criar um gateway de aplicativo do Azure usando o modelo do Gerenciador de Recursos do Azure"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 8192ee25-d9f0-4b32-a45e-1d74629c54e5
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 02b758b06332abe190b322c83da7c4135d358fa4
ms.lasthandoff: 03/21/2017


---
# <a name="create-an-application-gateway-by-using-the-azure-resource-manager-template"></a>Criar um gateway de aplicativo usando o modelo do Gerenciador de Recursos do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-create-gateway-portal.md)
> * [PowerShell do Azure Resource Manager](application-gateway-create-gateway-arm.md)
> * [Azure Classic PowerShell](application-gateway-create-gateway.md)
> * [Modelo do Azure Resource Manager](application-gateway-create-gateway-arm-template.md)
> * [CLI do Azure](application-gateway-create-gateway-cli.md)

O Azure Application Gateway é um balanceador de carga de camada&7;. Ele fornece o failover e solicitações HTTP de roteamento de desempenho entre diferentes servidores, estejam eles na nuvem ou no local. O Gateway de Aplicativo fornece muitos recursos do ADC (Controlador de Entrega de Aplicativos), incluindo o balanceamento de carga de HTTP, a afinidade de sessão baseada em cookies, o descarregamento de SSL (Secure Sockets Layer), as sondas de integridade personalizadas, suporte para vários sites e muitos outros. Para localizar uma lista completa dos recursos com suporte, visite [Visão geral do Gateway de Aplicativo](application-gateway-introduction.md)

Você aprenderá a baixar e a modificar um modelo existente do Azure Resource Manager do GitHub e a implantar o modelo do GitHub, do PowerShell e da CLI do Azure.

Se você estiver simplesmente implantando o modelo do Gerenciador de Recursos do Azure diretamente do GitHub, sem nenhuma alteração, ignore para implantar um modelo do GitHub.

## <a name="scenario"></a>Cenário

Neste cenário, você:

* Criará um gateway de aplicativo com duas instâncias.
* Criará uma rede virtual chamada VirtualNetwork1, com um bloco CIDR 10.0.0.0/16 reservado.
* Criará uma sub-rede chamada Appgatewaysubnet que usa 10.0.0.0/28 como seu bloco CIDR.
* Configurará dois IPs de back-end definidos anteriormente para os servidores Web nos quais deseja balancear a carga do tráfego. Neste exemplo de modelo, os IPs de back-end são 10.0.1.10 e 10.0.1.11.

> [!NOTE]
> Essas configurações são os parâmetros para esse modelo. Para personalizar o modelo, você pode alterar as regras, o ouvinte e o SSL que abre o azuredeploy.json.

![Cenário](./media/application-gateway-create-gateway-arm-template/scenario.png)

## <a name="download-and-understand-the-azure-resource-manager-template"></a>Baixar e compreender o modelo do Gerenciador de Recursos do Azure

Você pode baixar o modelo existente do Gerenciador de Recursos do Azure para criar uma rede virtual e duas sub-redes do GitHub, fazer as alterações desejadas e reutilizá-la. Para fazer isso, execute as seguintes etapas:

1. Navegue até [Criar Application Gateway](https://github.com/Azure/azure-quickstart-templates/tree/master/101-application-gateway-create).
2. Clique em **azuredeploy.json** e em **RAW**.
3. Salve o arquivo em uma pasta local do computador.
4. Se você estiver familiarizado com os modelos do Gerenciador de Recursos do Azure, pule para a etapa 7.
5. Abra o arquivo que você salvou e examine o conteúdo em **parâmetros** na linha 5. Os parâmetros do modelo do Gerenciador de Recursos do Azure fornecem um espaço reservado para valores que podem ser preenchidos durante a implantação.
   
   | Parâmetro | Descrição |
   | --- | --- |
   | **local** |Região do Azure onde o gateway de aplicativo é criado |
   | **VirtualNetwork1** |Nome para a nova rede virtual |
   | **addressPrefix** |Espaço de endereço para a rede virtual, em formato CIDR |
   | **ApplicationGatewaysubnet** |Nome para a sub-rede de gateway de aplicativo |
   | **subnetPrefix** |Bloco CIDR para a sub-rede do gateway de aplicativo |
   | **skuname** |Tamanho da instância SKU |
   | **capacidade** |Número de instâncias |
   | **backendaddress1** |Endereço IP do primeiro servidor Web |
   | **backendaddress2** |Endereço IP do segundo servidor Web |

    > [!IMPORTANT]
    >Os modelos do Gerenciador de Recursos do Azure mantidos no GitHub podem mudar ao longo do tempo. Verifique o modelo antes de usá-lo.

6. Verifique o conteúdo em **recursos** e observe as seguintes propriedades:

   * **type**. Tipo de recurso que está sendo criado pelo modelo. Nesse caso, o tipo é `Microsoft.Network/applicationGateways`, que representa um gateway de aplicativo.
   * **name**. Nome do recurso. Observe o uso de `[parameters('applicationGatewayName')]`, que significa o nome que é fornecido como entrada por você ou um arquivo de parâmetro durante a implantação.
   * **properties**. Lista de propriedades do recurso. Esse modelo usa a rede virtual e o endereço IP público durante a criação do gateway de aplicativo.

7. Navegue de volta para [https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-create/](https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-create).
8. Clique em **azuredeploy-paremeters.json** e em **RAW**.
9. Salve o arquivo em uma pasta local do computador.
10. Abra o arquivo que você salvou e edite os valores dos parâmetros. Use os valores a seguir para implantar o gateway de aplicativo descrito em nosso cenário.

    ```json
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
    ```

11. Salve o arquivo. Você pode testar o modelo JSON e o modelo de parâmetro usando ferramentas de validação de JSON online, como [JSlint.com](http://www.jslint.com/).

## <a name="deploy-the-azure-resource-manager-template-by-using-powershell"></a>Implantar o modelo do Gerenciador de Recursos do Azure usando o PowerShell

Se você nunca usou o Azure PowerShell, confira [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) e siga as instruções para entrar no Azure e selecionar sua assinatura.

### <a name="step-1"></a>Etapa 1

```powershell
Login-AzureRmAccount
```

### <a name="step-2"></a>Etapa 2

Verificar as assinaturas da conta.

```powershell
Get-AzureRmSubscription
```

Você deve se autenticar com suas credenciais.

### <a name="step-3"></a>Etapa 3

Escolha quais das suas assinaturas do Azure deseja usar.

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>Etapa 4

Se necessário, crie um grupo de recursos usando o cmdlet **New-AzureResourceGroup** . No exemplo a seguir, você cria um grupo de recursos chamado AppgatewayRG no local do Leste dos EUA.

```powershell
New-AzureRmResourceGroup -Name AppgatewayRG -Location "East US"
```

Execute o cmdlet **New-AzureRmResourceGroupDeployment** para implantar a nova rede virtual usando os arquivos de modelo e parâmetro anteriores que você baixou e modificou.

```powershell
New-AzureRmResourceGroupDeployment -Name TestAppgatewayDeployment -ResourceGroupName AppgatewayRG `
-TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json
```

## <a name="deploy-the-azure-resource-manager-template-by-using-the-azure-cli"></a>Implantar o modelo do Gerenciador de Recursos do Azure usando a CLI do Azure

Para implantar o modelo do Azure Resource Manager baixado usando a CLI do Azure, execute as etapas abaixo:

### <a name="step-1"></a>Etapa 1

Se você nunca usou a CLI do Azure, confira [Instalar e configurar a CLI do Azure](../cli-install-nodejs.md) e siga as instruções até o ponto em que você seleciona sua conta e assinatura do Azure.

### <a name="step-2"></a>Etapa 2

Execute o comando **azure config mode** para alternar para o modo do Resource Manager, como mostra o trecho de código abaixo.

```azurecli
azure config mode arm
```

Este é o resultado esperado para o comando anterior:

```azurecli
info:    New mode is arm
```

### <a name="step-3"></a>Etapa 3

Se for necessário, execute o comando **azure group create** para criar um novo grupo de recursos, conforme mostra o trecho de código abaixo. Observe a saída do comando. A lista exibida após a saída explicar os parâmetros usados. Para saber mais sobre grupos de recursos, visite [Visão geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

```azurecli
azure group create -n appgatewayRG -l eastus
```

**-n (or --name)**. Nome do novo grupo de recursos. Para nosso cenário, é *appgatewayRG*.

**-l (ou --location)**. Região do Azure onde o novo grupo de recursos é criado. Para nosso cenário, é *eastus*.

### <a name="step-4"></a>Etapa 4

Execute o cmdlet **azure group deployment create** para implantar a nova rede virtual usando o modelo e os arquivos de parâmetro que você baixou e modificou na etapa anterior. A lista exibida após a saída explicar os parâmetros usados.

```azurecli
azure group deployment create -g appgatewayRG -n TestAppgatewayDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json
```

## <a name="deploy-the-azure-resource-manager-template-by-using-click-to-deploy"></a>Implantar o modelo do Gerenciador de Recursos do Azure usando o clique para implantar

Clique para implantar é outra maneira de usar modelos do Gerenciador de Recursos do Azure. É uma maneira fácil de usar modelos com o portal do Azure.

### <a name="step-1"></a>Etapa 1

Acesse [Criar um application gateway com IP público](https://azure.microsoft.com/documentation/templates/101-application-gateway-public-ip/).

### <a name="step-2"></a>Etapa 2

Clique em **Implantar no Azure**.

![Implantar no Azure](./media/application-gateway-create-gateway-arm-template/deploytoazure.png)

### <a name="step-3"></a>Etapa 3

Preencha os parâmetros do modelo de implantação no portal e clique em **OK**.

![parâmetros](./media/application-gateway-create-gateway-arm-template/ibiza1.png)

### <a name="step-4"></a>Etapa 4

Selecione **Termos legais** e clique em **Comprar**.

### <a name="step-5"></a>Etapa 5

Na folha Implantação personalizada, clique em **Criar**.

## <a name="providing-certificate-data-to-resource-manager-templates"></a>Fornecendo dados de certificado aos modelos do Resource Manager

Ao usar o SSL com um modelo, o certificado precisa ser fornecido em uma cadeia de caracteres base64 em vez de ser carregado. Para converter um .pfx ou .cer em uma cadeia de caracteres base64, execute o comando do PowerShell a seguir. Esse trecho converterá o certificado em uma cadeia de caracteres base64 que pode ser fornecida para o modelo. A saída esperada é uma cadeia de caracteres que pode ser armazenada em uma variável e colada no modelo.

```powershell
[System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes("<certificate path and name>.pfx"))
```

## <a name="next-steps"></a>Próximas etapas

Se desejar configurar o descarregamento SSL, confira [Configurar um application gateway para descarregamento SSL](application-gateway-ssl.md).

Para configurar um gateway de aplicativo para usar com um balanceador de carga interno, confira [Criar um gateway de aplicativo com um ILB (balanceador de carga interno)](application-gateway-ilb.md).

Se deseja saber mais sobre as opções de balanceamento de carga no geral, visite:

* [Balanceador de carga do Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Gerenciador de Tráfego do Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)


