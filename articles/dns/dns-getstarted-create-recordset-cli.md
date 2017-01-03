---
title: Criar um conjunto de registros e registros para uma zona DNS usando a CLI | Microsoft Docs
description: "Como criar registros de host para o DNS do Azure. Configuração dos conjuntos de registros e registros usando a CLI"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 02b897d3-e83b-4257-b96d-5c29aa59e843
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/09/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: bfbffe7843bc178cdf289c999925c690ab82e922
ms.openlocfilehash: e377f176fe24a8e7e42d409f86d6b0093ce5e7c4

---

# <a name="create-dns-record-sets-and-records-by-using-cli"></a>Criar conjuntos de registros DNS e registros usando a CLI

> [!div class="op_single_selector"]
> * [Portal do Azure](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [CLI do Azure](dns-getstarted-create-recordset-cli.md)

Este artigo o guiará durante o processo de criação de registros e conjuntos de registros usando a CLI do Azure. Para fazer isso, primeiro você precisa entender os registros DNS e conjuntos de registros.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Esta seção descreve como criar registros DNS no Azure DNS. Os exemplos pressupõem que você já [instalou a CLI do Azure, iniciou uma sessão e criou uma zona DNS](dns-getstarted-create-dnszone-cli.md).

Todos os exemplos nesta página usam o tipo 'A' de registro de DNS. Para obter outros tipos de registro e mais detalhes sobre como gerenciar registros DNS e conjuntos de registros, consulte [Gerenciar registros DNS e conjuntos de registro usando a CLI do Azure](dns-operations-recordsets-cli.md).

## <a name="create-a-record-set-and-record"></a>Criar registro e um conjunto de registros

Nesta seção, mostraremos como criar registros e um conjunto de registros. Neste exemplo, você criará um conjunto de registros com o nome relativo “www” na zona DNS “contoso.com”. O nome totalmente qualificado do registro é “www.contoso.com”. O tipo de registro é “A” e o TTL é 60 segundos. Depois de concluir esta etapa, você criará um conjunto de registros vazio.

Para criar um conjunto de registros em vértices da zona (nesse caso, "contoso.com"), use o nome do registro "@",, incluindo as aspas. Isso é uma convenção comum do DNS.

### <a name="1-create-a-record-set"></a>1. Criar um conjunto de registros

Se o novo registro tem o mesmo nome e tipo de um registro existente, você precisa adicioná-lo ao conjunto de registros existente. Você pode pular essa etapa e ir para [Adicionar registros](#add-records) abaixo. Caso contrário, se o novo registro tem um nome e tipo diferente para todos os registros existentes, você precisa criar um novo conjunto de registros.

Você cria conjuntos de registros usando o comando `azure network dns record-set create`. Para obter ajuda, consulte `azure network dns record-set create -h`.  

Ao criar um conjunto de registros, você precisa especificar o nome do conjunto de registros, a zona, o TTL (vida útil) e o tipo de registro. 

```azurecli
azure network dns record-set create myresourcegroup contoso.com www A 60
```

Depois de concluir esta etapa, você terá um conjunto de registros "www" vazio. Para usar o conjunto de registros “www” recém-criado, você precisa, em primeiro lugar, adicionar registros a ele.

### <a name="2-add-records"></a>2. Adicionar registros

Você adiciona registros aos conjuntos de registros usando `azure network dns record-set add-record`. Para obter ajuda, consulte `azure network dns record-set add-record -h`.

Os parâmetros para adicionar registros a um conjunto de registros variam dependendo do tipo de conjunto de registros. Por exemplo, ao usar um conjunto de registros do tipo "A", você só pode especificar os registros com o parâmetro `-a <IPv4 address>`. Consulte `azure network dns record-set add-record -h` para obter uma lista dos parâmetros para outros tipos de registro.

Você pode adicionar um registro A ao conjunto de registros "www" usando o seguinte comando:

```azurecli
azure network dns record-set add-record myresourcegroup contoso.com  www A  -a 1.2.3.4
```

### <a name="verify-name-resolution"></a>Verificar a resolução de nome

Você pode testar se os registros DNS estão presentes nos servidores de nome DNS do Azure usando ferramentas de DNS, tais como nslookup, dig ou o [cmdlet Resolve-DnsName PowerShell](https://technet.microsoft.com/library/jj590781.aspx).

Se você ainda não delegou seu domínio para usar a nova zona no DNS do Azure, você precisa [direcionar a consulta DNS diretamente para um dos servidores de nome para a zona](dns-getstarted-create-dnszone.md#test-name-servers). Certifique-se de substituir os valores corretos para a zona dos seus registros no comando abaixo.

    nslookup
    > set type=A
    > server ns1-01.azure-dns.com
    > www.contoso.com

    Server:  ns1-01.azure-dns.com
    Address:  40.90.4.1

    Name:    www.contoso.com
    Address:  1.2.3.4

## <a name="next-steps"></a>Próximas etapas

Saiba como [delegar seu nome de domínio para os servidores de nome DNS do Azure](dns-domain-delegation.md)

Saiba como [gerenciar zonas DNS usando a CLI do Azure](dns-operations-dnszones-cli.md).

Saiba como [gerenciar registros DNS e conjuntos de registros usando a CLI do Azure](dns-operations-recordsets-cli.md).




<!--HONumber=Dec16_HO2-->


