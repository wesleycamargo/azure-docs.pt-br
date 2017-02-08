---
title: Gerenciar registros DNS reversos no DNS do Azure usando o PowerShell | Microsoft Docs
description: "O DNS do Azure permite gerenciar registros DNS reversos ou registros PTR para os serviços do Azure usando o PowerShell no Gerenciador de Recursos"
services: DNS
documentationcenter: na
author: s-malone
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: b95703c5-e94e-4009-ab37-0c3f7908783c
ms.service: DNS
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2016
ms.author: smalone
translationtype: Human Translation
ms.sourcegitcommit: efa52b5f30cab16bfde4202dbfe2c95f4464e2c4
ms.openlocfilehash: 730321ccacb211ec82e69f8ebc69ee84cbf46a01


---
# <a name="manage-reverse-dns-records-for-your-azure-services-using-powershell"></a>Gerenciar registros DNS reversos para seus serviços do Azure usando o PowerShell

[!INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]


[!INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]


[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]

Para obter mais informações sobre o modelo de implantação clássico, confira [Como gerenciar registros DNS reversos para seus serviços (clássicos) do Azure usando o Azure PowerShell](dns-reverse-dns-record-operations-classic-ps.md).

## <a name="validation-of-reverse-dns-records"></a>Validação de registros DNS reversos
Para garantir que um terceiro não crie registros DNS reversos que sejam mapeados para seus domínios DNS, o Azure permite apenas a criação de um registro DNS reverso, em que uma das seguintes opções é verdadeira:

* O “ReverseFqdn” é o mesmo que o “Fqdn” do recurso de Endereço IP Público para o qual foi especificado, ou o “Fqdn” de qualquer Endereço IP Público na mesma assinatura; por exemplo, “ReverseFqdn” é “contosoapp1.northus.cloudapp.azure.com.”.
* O encaminhamento de “ReverseFqdn” é resolvido no nome ou IP do Endereço IP Público para o qual foi especificado, ou como qualquer “Fqdn” ou IP do Endereço IP Público contido na mesma assinatura; por exemplo, “ReverseFqdn” é “app1.contoso.com.” que é um alias de CName para “contosoapp1.northus.cloudapp.azure.com”.

Verificações de validação são executadas somente quando a propriedade de DNS reverso de um Endereço IP Público é definida ou modificada. Uma nova validação periódica não é executada.

## <a name="add-reverse-dns-to-existing-public-ip-addresses"></a>Adicionar o DNS reverso aos endereços IP Públicos existentes
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

## <a name="create-a-public-ip-address-with-reverse-dns"></a>Criar um Endereço IP Público com DNS reverso
É possível adicionar um novo Endereço IP Público com a propriedade de DNS reverso especificada usando o cmdlet "New-AzureRmPublicIpAddress":

    PS C:\> New-AzureRmPublicIpAddress -Name PublicIP2 -ResourceGroupName NRP-DemoRG-PS -Location WestUS -AllocationMethod Dynamic -DomainNameLabel "contosoapp2" -ReverseFqdn "contosoapp2.westus.cloudapp.azure.com."

## <a name="view-reverse-dns-for-existing-public-ip-addresses"></a>Exibir o DNS reverso dos Endereços IP Públicos existentes
É possível exibir o valor configurado para um Endereço IP Público existente usando o cmdlet "Get-AzureRmPublicIpAddress":

    PS C:\> Get-AzureRmPublicIpAddress -Name PublicIP2 -ResourceGroupName NRP-DemoRG-PS

## <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Remover o DNS reverso de Endereços IP Públicos existentes
É possível remover uma propriedade de DNS reverso de um Endereço IP Público existente usando o cmdlet “Set-AzureRmPublicIpAddress”. Isso é feito definindo o valor da propriedade ReverseFqdn como em branco:

    PS C:\> $pip = Get-AzureRmPublicIpAddress -Name PublicIP -ResourceGroupName NRP-DemoRG-PS
    PS C:\> $pip.DnsSettings.ReverseFqdn = ""
    PS C:\> Set-AzureRmPublicIpAddress -PublicIpAddress $pip


[!INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[!INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-arm-include.md)]




<!--HONumber=Jan17_HO4-->


