---
title: "Gerenciar registros DNS reversos para seus serviços do Azure (clássico) usando o PowerShell | Microsoft Docs"
description: "Como gerenciar registros DNS reversos ou registros PTR para os serviços do Azure usando o PowerShell no modelo de implantação clássica. "
services: DNS
documentationcenter: na
author: s-malone
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 9c24d176-6bce-4277-a14c-80fe44a20a87
ms.service: DNS
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2016
ms.author: smalone
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: fe5c9cfe0edd769635301fdcb40fc1f71d430165


---
# <a name="how-to-manage-reverse-dns-records-for-your-azure-services-classic-using-azure-powershell"></a>Como gerenciar registros DNS reversos para seus serviços do Azure (clássicos) usando o Azure PowerShell

[!INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]


[!INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]


> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../azure-resource-manager/resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação Clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos. Saiba como [executar estas etapas usando o modelo do Resource Manager](dns-reverse-dns-record-operations-ps.md).

## <a name="validation-of-reverse-dns-records"></a>Validação de registros DNS reversos
Para garantir que um terceiro não crie registros DNS reversos que sejam mapeados para seus domínios DNS, o Azure permite apenas a criação de um registro DNS reverso, em que uma das seguintes opções é verdadeira:

* O FQDN do DNS reverso é o nome do Serviço de Nuvem para o qual foi especificado, ou qualquer nome do Serviço de Nuvem contido na mesma assinatura; por exemplo, o DNS reverso é “contosoapp1.cloudapp.net.”.
* O encaminhamento FQDN do DNS reverso é resolvido no nome ou IP do Serviço de Nuvem para o qual foi especificado, ou como qualquer nome ou IP do Serviço de Nuvem contido na mesma assinatura; por exemplo, o DNS reverso é “app1.contoso.com.” que é um alias de CName para contosoapp1.cloudapp.net.

Verificações de validação são executadas somente quando a propriedade de DNS reverso de um Serviço de Nuvem é definida ou modificada. Uma nova validação periódica não é executada.

## <a name="add-reverse-dns-to-existing-cloud-services"></a>Adicionar DNS reverso aos Serviços de Nuvem existentes
É possível adicionar um registro de DNS reverso a um Serviço de Nuvem existente usando o cmdlet "Set-AzureService":

    PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## <a name="create-a-cloud-service-with-reverse-dns"></a>Criar um Serviço de Nuvem com DNS reverso
É possível adicionar um novo Serviço de Nuvem com a propriedade de DNS reverso especificada usando o cmdlet “Set-AzureService”:

    PS C:\> New-AzureService –ServiceName “contosoapp1” –Location “West US” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## <a name="view-reverse-dns-for-existing-cloud-services"></a>Exibir o DNS reverso dos Serviços de Nuvem existentes
É possível exibir o valor configurado para um Serviço de Nuvem existente usando o cmdlet "Get-AzureService":

    PS C:\> Get-AzureService "contosoapp1"

## <a name="remove-reverse-dns-from-existing-cloud-services"></a>Remover o DNS reverso dos Serviços de Nuvem existentes
É possível remover uma propriedade de DNS reverso de um Serviço de Nuvem existente usando o cmdlet “Set-AzureService”. Isso é feito definindo o valor da propriedade do DNS reverso como em branco:

    PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “”

[!INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[!INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-asm-include.md)]




<!--HONumber=Dec16_HO2-->


