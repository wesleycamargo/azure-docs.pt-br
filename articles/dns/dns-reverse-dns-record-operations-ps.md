<properties
   pageTitle="Como gerenciar registros DNS reversos para seus serviços usando o PowerShell no Gerenciador de Recursos | Microsoft Azure"
   description="Como gerenciar registros DNS reversos ou registros PTR para os serviços do Azure usando o PowerShell no Gerenciador de Recursos"
   services="DNS"
   documentationCenter="na"
   authors="s-malone"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
   ms.service="DNS"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/05/2016"
   ms.author="smalone" />

# Como gerenciar registros DNS reversos para seus serviços usando o PowerShell

[AZURE.INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)] <BR> [AZURE.INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)] <BR> [AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](dns-reverse-dns-record-operations-classic-ps.md).

## Validação de registros DNS reversos
Para garantir que um terceiro não crie registros DNS reversos que sejam mapeados para seus domínios DNS, o Azure permite apenas a criação de um registro DNS reverso, em que uma das seguintes opções é verdadeira:

- O “ReverseFqdn” é o mesmo que o “Fqdn” do recurso de Endereço IP Público para o qual foi especificado, ou o “Fqdn” de qualquer Endereço IP Público na mesma assinatura; por exemplo, “ReverseFqdn” é “contosoapp1.northus.cloudapp.azure.com.”.

- O encaminhamento de “ReverseFqdn” é resolvido no nome ou IP do Endereço IP Público para o qual foi especificado, ou como qualquer “Fqdn” ou IP do Endereço IP Público contido na mesma assinatura; por exemplo, “ReverseFqdn” é “app1.contoso.com.”, que é um alias de CName para “contosoapp1.northus.cloudapp.azure.com”.

Verificações de validação são executadas somente quando a propriedade de DNS reverso de um Endereço IP Público é definida ou modificada. Uma nova validação periódica não é executada.

## Adicionar o DNS reverso aos endereços IP Públicos existentes
É possível adicionar um DNS reverso a um Endereço IP Público existente usando o cmdlet "Set-AzureRmPublicIpAddress":

	PS C:\> $pip = Get-AzureRmPublicIpAddress -Name PublicIP -ResourceGroupName NRP-DemoRG-PS
	PS C:\> $pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
	PS C:\> Set-AzureRmPublicIpAddress -PublicIpAddress $pip

Se você quiser adicionar o DNS reverso a um Endereço IP Público existente que ainda não tenha um nome DNS, também deverá especificar um nome DNS. Você pode conseguir isso usando o cmdlet "Set-AzureRmPublicIpAddress":

	PS C:\> $pip = Get-AzureRmPublicIpAddress -Name PublicIP -ResourceGroupName NRP-DemoRG-PS
	PS C:\> $pip.DnsSettings = New-Object -TypeName Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings
	PS C:\> $pip.DnsSettings.DomainNameLabel = "contosoapp1"
	PS C:\> $pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
	PS C:\> Set-AzureRmPublicIpAddress -PublicIpAddress $pip

## Criar um Endereço IP Público com DNS reverso
É possível adicionar um novo Endereço IP Público com a propriedade de DNS reverso especificada usando o cmdlet "New-AzureRmPublicIpAddress":

	PS C:\> New-AzureRmPublicIpAddress -Name PublicIP2 -ResourceGroupName NRP-DemoRG-PS -Location WestUS -AllocationMethod Dynamic -DomainNameLabel "contosoapp2" -ReverseFqdn "contosoapp2.westus.cloudapp.azure.com."

## Exibir o DNS reverso dos Endereços IP Públicos existentes
É possível exibir o valor configurado para um Endereço IP Público existente usando o cmdlet "Get-AzureRmPublicIpAddress":

	PS C:\> Get-AzureRmPublicIpAddress -Name PublicIP2 -ResourceGroupName NRP-DemoRG-PS

## Remover o DNS reverso de Endereços IP Públicos existentes
É possível remover uma propriedade de DNS reverso de um Endereço IP Público existente usando o cmdlet “Set-AzureRmPublicIpAddress”. Isso é feito definindo o valor da propriedade ReverseFqdn como em branco:

	PS C:\> $pip = Get-AzureRmPublicIpAddress -Name PublicIP -ResourceGroupName NRP-DemoRG-PS
	PS C:\> $pip.DnsSettings.ReverseFqdn = ""
	PS C:\> Set-AzureRmPublicIpAddress -PublicIpAddress $pip

[AZURE.INCLUDE [PERGUNTAS FREQUENTES](../../includes/dns-reverse-dns-record-operations-faq-arm-include.md)]

<!---HONumber=AcomDC_0907_2016-->