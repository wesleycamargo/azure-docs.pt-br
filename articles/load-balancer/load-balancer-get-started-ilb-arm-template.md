---
title: Criar um balanceador de carga interno usando um modelo no Azure Resource Manager | Microsoft Docs
description: Saiba como criar um balanceador de carga interno no Gerenciador de Recursos usando um modelo
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 64150862-6ced-42de-85dc-89d323257d7c
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/24/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 482c9cd46902d9e3f4e1e0f001182fdb43ce9367


---
# <a name="create-an-internal-load-balancer-using-a-template"></a>Criar um balanceador de carga interno usando um modelo
[!INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]

<BR>
[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]

[modelo de implantação clássico](load-balancer-get-started-ilb-classic-ps.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="deploy-the-template-by-using-click-to-deploy"></a>Implantar o modelo usando o clique para implantar
O modelo de exemplo disponível no repositório público usa um arquivo de parâmetro que contém os valores padrão usados para gerar o cenário descrito acima. Para implantar esse modelo usando a opção de clique para implantar, acesse [este link](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-internal-load-balancer), clique em **Implantar no Azure**, substitua os valores de parâmetro padrão, se necessário, e siga as instruções no portal.

## <a name="deploy-the-template-by-using-powershell"></a>Implantar o modelo usando o PowerShell
Para implantar o modelo baixado usando o PowerShell, faça o seguinte.

1. Se você nunca usou o Azure PowerShell, consulte [Como Instalar e Configurar o Azure PowerShell](../powershell-install-configure.md) e siga as instruções até o fim para entrar no Azure e selecionar sua assinatura.
2. Baixe o arquivo de parâmetros no disco local.
3. Edite o arquivo e salve-o.
4. Execute o cmdlet **New-AzureRmResourceGroupDeployment** para criar um grupo de recursos usando o modelo.
   
        New-AzureRmResourceGroupDeployment -Name TestRG -Location westus `
            -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json' `
            -TemplateParameterFile 'C:\temp\azuredeploy.parameters.json'

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Implantar o modelo usando a CLI do Microsoft Azure
Para implantar o modelo usando a CLI do Microsoft Azure, faça o seguinte:

1. Se você nunca usou a CLI do Azure, veja [Instalar e configurar a CLI do Azure](../xplat-cli-install.md) e siga as instruções até o ponto em que você seleciona sua conta e assinatura do Azure.
2. Execute o comando **azure config mode** para alternar para o modo do Gerenciador de Recursos, como mostrado abaixo.
   
        azure config mode arm
   
    Este é o resultado esperado para o comando descrito acima:
   
        info:    New mode is arm
3. Abra o arquivo de parâmetro, selecione o seu conteúdo e salve-o em um arquivo em seu computador. Para este exemplo, salvamos o arquivo de parâmetros em *parameters.json*.
4. Execute o comando **azure group deployment create** para implantar o novo balanceador de carga interno usando o modelo e os arquivos de parâmetro baixados e modificados acima. A lista exibida após a saída explicar os parâmetros usados.
   
        azure group create --name TestRG --location westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json --parameters-file parameters.json

## <a name="next-steps"></a>Próximas etapas
[Configurar um modo de distribuição do balanceador de carga usando a afinidade de IP de origem](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite de TCP ocioso para o balanceador de carga](load-balancer-tcp-idle-timeout.md)




<!--HONumber=Nov16_HO2-->


