<properties 
   pageTitle="Criar um balanceador de carga voltado para a Internet no Gerenciador de Recursos usando um modelo | Microsoft Azure"
   description="Saiba como criar um balanceador de carga para a Internet no Gerenciador de Recursos usando um modelo"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/21/2015"
   ms.author="joaoma" />

#Criar um balanceador de carga voltado para a Internet usando um modelo

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo aborda o modelo de implantação do Gerenciador de Recursos.

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## Implantar o modelo ARM usando clique para implantar

O modelo de exemplo disponível no repositório público usa um arquivo de parâmetro que contém os valores padrão usados para gerar o cenário descrito acima. Para implantar esse modelo usando a opção clique para implantar, execute [esse link](http://go.microsoft.com/fwlink/?LinkId=544801), clique em **Implantar no Azure**, substitua os valores de parâmetro padrão, se necessário, e siga as instruções no portal.

## Implantar o modelo ARM usando o PowerShell

Para implantar o modelo ARM baixado usando o PowerShell, siga as etapas abaixo.

1. Se você nunca usou o Azure PowerShell, veja [Como instalar e configurar o Azure PowerShell](powershell-install-configure.md) e siga as instruções até o fim para entrar no Azure e escolher sua assinatura.
2. Execute o cmdlet **Switch-AzureMode** para alternar para modo do Gerenciador de Recursos, como mostrado abaixo.

		Switch-AzureMode AzureResourceManager

	Este é o resultado esperado para o comando descrito acima:

		WARNING: The Switch-AzureMode cmdlet is deprecated and will be removed in a future release.

	>[AZURE.WARNING]O cmdlet Switch-AzureMode será preterido em breve. Quando isso acontecer, todos os cmdlets do Gerenciador de Recursos serão renomeados.

3. Execute o cmdlet **New-AzureResourceGroup** para criar um grupo de recursos usando o modelo.

		New-AzureResourceGroup -Name TestRG -Location uswest `
		    -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-natrules/azuredeploy.json' `
		    -TemplateParameterFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-natrules/azuredeploy.parameters.json'	

## Implantar o modelo ARM usando a CLI do Azure

Para implantar o modelo ARM usando a CLI do Azure, siga as etapas abaixo.

1. Se você nunca usou a CLI do Azure, veja [Instalar e configurar a CLI do Azure](xplat-cli.md) e siga as instruções até o ponto em que você seleciona sua conta e assinatura do Azure.
2. Execute o comando **azure config mode** para alternar para o modo do Gerenciador de Recursos, como mostrado abaixo.

		azure config mode arm

	Este é o resultado esperado para o comando descrito acima:

		info:    New mode is arm

3. No navegador, navegue até ****https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-natrules/azuredeploy.parameters.json**, copie o conteúdo do arquivo JSON e cole-o em um novo arquivo em seu computador. Para este cenário, copie os valores abaixo em um arquivo chamado **c:\\udr\\azuredeploy.parameters.json**.
4. Execute o cmdlet **azure group deployment create** para implantar o novo balanceador de carga usando o modelo e os arquivos de parâmetro acima que você baixou e modificou. A lista exibida após a saída explicar os parâmetros usados.

		azure group create -n TestRG -l westus -f 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-natrules/azuredeploy.json' -e 'c:\lb\azuredeploy.parameters.json'

<!---HONumber=Nov15_HO1-->