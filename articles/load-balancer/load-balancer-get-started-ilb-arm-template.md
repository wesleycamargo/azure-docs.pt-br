---
title: Criar um balanceador de carga interno usando um modelo no Gerenciador de Recursos | Microsoft Docs
description: Saiba como criar um balanceador de carga interno no Gerenciador de Recursos usando um modelo
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: ''
tags: azure-resource-manager

ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2016
ms.author: sewhee

---
# Introdução à criação de um balanceador de carga interno usando um modelo
[!INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]

<BR>
[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]

[modelo de implantação clássico](load-balancer-get-started-ilb-classic-ps.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## Implantar o modelo usando o clique para implantar
O modelo de exemplo disponível no repositório público usa um arquivo de parâmetro que contém os valores padrão usados para gerar o cenário descrito acima. Para implantar esse modelo usando a opção de clique para implantar, acesse [este link](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-2-vms-internal-load-balancer%2Fazuredeploy.json), clique em **Implantar no Azure**, substitua os valores de parâmetro padrão, se necessário, e siga as instruções no portal.

## Implantar o modelo usando o PowerShell
Para implantar o modelo baixado usando o PowerShell, faça o seguinte.

1. Se você nunca usou o Azure PowerShell, consulte [Como Instalar e Configurar o Azure PowerShell](../powershell-install-configure.md) e siga as instruções até o fim para entrar no Azure e selecionar sua assinatura.
2. Baixe o arquivo de [parâmetros](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.parameters.json) no disco local.
3. Edite o arquivo e salve-o.
4. Execute o cmdlet **New-AzureRmResourceGroupDeployment** para criar um grupo de recursos usando o modelo.

        New-AzureRmResourceGroupDeployment -Name TestRG -Location westus `
            -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json' `
            -TemplateParameterFile 'C:\temp\azuredeploy.parameters.json'



## Implantar o modelo usando a CLI do Microsoft Azure
Para implantar o modelo usando a CLI do Microsoft Azure, faça o seguinte:

1. Se você nunca usou a CLI do Azure, veja [Instalar e configurar a CLI do Azure](../xplat-cli-install.md) e siga as instruções até o ponto em que você seleciona sua conta e assinatura do Azure.
2. Execute o comando **azure config mode** para alternar para o modo do Gerenciador de Recursos, como mostrado abaixo.
   
        azure config mode arm
   
    Este é o resultado esperado para o comando descrito acima:
   
        info:    New mode is arm
3. Abra o [arquivo de parâmetro](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.parameters.json), selecione o seu conteúdo e salve-o em um arquivo em seu computador. Para este exemplo, salvamos o arquivo de parâmetros em *parameters.json*.
4. Execute o comando **azure group deployment create** para implantar o novo balanceador de carga interno usando o modelo e os arquivos de parâmetro baixados e modificados acima. A lista exibida após a saída explicar os parâmetros usados.
   
        azure group create -n TestRG -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json -e parameters.json

## Próximas etapas
[Configurar um modo de distribuição do balanceador de carga usando a afinidade de IP de origem](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite de TCP ocioso para o balanceador de carga](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0824_2016-->