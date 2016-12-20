---
title: "Gerenciar registros DNS reversos para seus serviços do Azure usando a CLI do Azure | Microsoft Docs"
description: "Como gerenciar registros DNS reversos ou registros PTR para os serviços do Azure usando a CLI do Azure no Gerenciador de Recursos"
services: DNS
documentationcenter: na
author: s-malone
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: c655707e-1156-4893-b163-0b228ffd25d2
ms.service: DNS
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2016
ms.author: smalone
translationtype: Human Translation
ms.sourcegitcommit: 0f8bc125855bc5a5b67fde5b0b742c73b5da7610
ms.openlocfilehash: b7c91d7c13884a00bb06a7cfed55a59b357f7d18


---
# <a name="how-to-manage-reverse-dns-records-for-your-azure-services-using-the-azure-cli"></a>Como gerenciar registros DNS reversos para seus serviços do Azure usando a CLI do Azure

[!INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]


[!INCLUDE [dns-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]


[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]

Para obter mais informações sobre o modelo de implantação clássico, confira [Como gerenciar registros DNS reversos para seus serviços (clássicos) do Azure usando o Azure PowerShell](dns-reverse-dns-record-operations-classic-ps.md).

## <a name="validation-of-reverse-dns-records"></a>Validação de registros DNS reversos
Para garantir que um terceiro não crie registros DNS reversos que sejam mapeados para seus domínios DNS, o Azure permite apenas a criação de um registro DNS reverso, em que uma das seguintes opções é verdadeira:

* O “ReverseFqdn” é o mesmo que o “Fqdn” do recurso de Endereço IP Público para o qual foi especificado, ou o “Fqdn” de qualquer Endereço IP Público na mesma assinatura; por exemplo, “ReverseFqdn” é “contosoapp1.northus.cloudapp.azure.com.”.
* O encaminhamento de “ReverseFqdn” é resolvido no nome ou IP do Endereço IP Público para o qual foi especificado, ou como qualquer “Fqdn” ou IP do Endereço IP Público contido na mesma assinatura; por exemplo, “ReverseFqdn” é “app1.contoso.com.” que é um alias de CName para “contosoapp1.northus.cloudapp.azure.com”.

Verificações de validação são executadas somente quando a propriedade de DNS reverso de um Endereço IP Público é definida ou modificada. Uma nova validação periódica não é executada.

## <a name="add-reverse-dns-to-existing-public-ip-addresses"></a>Adicionar o DNS reverso aos endereços IP Públicos existentes
É possível adicionar um DNS reverso a um endereço IP público existente usando azure network public-ip set:

    azure network public-ip set -n PublicIp -g NRP-DemoRG-PS -f contosoapp1.westus.cloudapp.azure.com.

Se você quiser adicionar o DNS reverso a um Endereço IP Público existente que ainda não tenha um nome DNS, também deverá especificar um nome DNS. Você pode conseguir isso usando azure network public-ip set:

    azure network public-ip set -n PublicIp -g NRP-DemoRG-PS -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.

## <a name="create-a-public-ip-address-with-reverse-dns"></a>Criar um Endereço IP Público com DNS reverso
É possível adicionar um novo Endereço IP Público com a propriedade de DNS reverso especificada usando azure network public-ip create:

    azure network public-ip create -n PublicIp3 -g NRP-DemoRG-PS -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.

## <a name="view-reverse-dns-for-existing-public-ip-addresses"></a>Exibir o DNS reverso dos Endereços IP Públicos existentes
É possível exibir o valor configurado para um Endereço IP Público usando azure network public-ip show:

    azure network public-ip show -n PublicIp3 -g NRP-DemoRG-PS

## <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Remover o DNS reverso de Endereços IP Públicos existentes
É possível remover uma propriedade de DNS reverso de um Endereço IP Público existente usando azure network public-ip set. Isso é feito definindo o valor da propriedade ReverseFqdn como em branco:

    azure network public-ip set -n PublicIp3 -g NRP-DemoRG-PS –f “”

[!INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[!INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-arm-include.md)]




<!--HONumber=Nov16_HO3-->


