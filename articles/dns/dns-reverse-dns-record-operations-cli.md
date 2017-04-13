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
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: smalone
translationtype: Human Translation
ms.sourcegitcommit: 1481fcb070f383d158c5a6ae32504e498de4a66b
ms.openlocfilehash: 1ac7bc232082100cd0f0fe3e337930a6c5e9ede9
ms.lasthandoff: 03/01/2017


---
# <a name="how-to-manage-reverse-dns-records-for-your-azure-services-using-the-azure-cli"></a>Como gerenciar registros DNS reversos para seus serviços do Azure usando a CLI do Azure

[!INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]


[!INCLUDE [dns-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]

## <a name="cli-versions-to-complete-the-task"></a>Versões da CLI para concluir a tarefa

Você pode concluir a tarefa usando uma das seguintes versões da CLI:

* [CLI do Azure 1.0](dns-reverse-dns-record-operations-cli-nodejs.md) – nossa CLI para os modelos de implantação clássico e do resource manager.
* [CLI do Azure 2.0](dns-reverse-dns-record-operations-cli.md) – nossa próxima geração de CLI para o modelo de implantação do resource manager.

## <a name="introduction"></a>Introdução

[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]

Para obter mais informações sobre o modelo de implantação clássico, confira [Como gerenciar registros DNS reversos para seus serviços (clássicos) do Azure usando o Azure PowerShell](dns-reverse-dns-record-operations-classic-ps.md).


## <a name="validation-of-reverse-dns-records"></a>Validação de registros DNS reversos
Para garantir que um terceiro não crie registros DNS reversos que sejam mapeados para seus domínios DNS, o Azure permite apenas a criação de um registro DNS reverso, em que uma das seguintes opções é verdadeira:

* O “ReverseFqdn” é o mesmo que o “Fqdn” do recurso de Endereço IP Público para o qual foi especificado, ou o “Fqdn” de qualquer Endereço IP Público na mesma assinatura; por exemplo, “ReverseFqdn” é “contosoapp1.northus.cloudapp.azure.com.”.
* O encaminhamento de “ReverseFqdn” é resolvido no nome ou IP do Endereço IP Público para o qual foi especificado, ou como qualquer “Fqdn” ou IP do Endereço IP Público contido na mesma assinatura; por exemplo, “ReverseFqdn” é “app1.contoso.com.” que é um alias de CName para “contosoapp1.northus.cloudapp.azure.com”.

Verificações de validação são executadas somente quando a propriedade de DNS reverso de um Endereço IP Público é definida ou modificada. Uma nova validação periódica não é executada.

## <a name="add-reverse-dns-to-existing-public-ip-addresses"></a>Adicionar o DNS reverso aos endereços IP Públicos existentes
É possível adicionar um DNS reverso a um endereço IP público existente usando azure network public-ip set:

```azurecli
az network public-ip update --resource-group NRP-DemoRG-PS --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com.
```

Se você quiser adicionar o DNS reverso a um Endereço IP Público existente que ainda não tenha um nome DNS, também deverá especificar um nome DNS. Você pode conseguir isso usando azure network public-ip set:

```azurecli
az network public-ip update --resource-group NRP-DemoRG-PS --name PublicIp --reverse-fqdn contosoapp1.westus.cloudapp.azure.com --dns-name contosoapp1
```

## <a name="create-a-public-ip-address-with-reverse-dns"></a>Criar um Endereço IP Público com DNS reverso
É possível adicionar um novo Endereço IP Público com a propriedade de DNS reverso especificada usando azure network public-ip create:

```azurecli
az network public-ip create --name PublicIp --resource-group NRP-DemoRG-PS --location westcentralus --dns-name contosoapp1 --reverse-fqdn contosoapp1.westcentralus.cloudapp.azure.com
```

## <a name="view-reverse-dns-for-existing-public-ip-addresses"></a>Exibir o DNS reverso dos Endereços IP Públicos existentes
É possível exibir o valor configurado para um Endereço IP Público usando azure network public-ip show:

```azurecli
 az network public-ip show --name PublicIp --resource-group NRP-DemoRG-PS
```

## <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Remover o DNS reverso de Endereços IP Públicos existentes
É possível remover uma propriedade de DNS reverso de um Endereço IP Público existente usando azure network public-ip set. Isso é feito definindo o valor da propriedade ReverseFqdn como em branco:

```azurecli
az network public-ip update --resource-group NRP-DemoRG-PS --name PublicIp --reverse-fqdn ""
```

[!INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[!INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-arm-include.md)]


