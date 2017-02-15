---
title: Criar registros DNS usando a CLI do Azure | Microsoft Docs
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
ms.date: 12/21/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 18a21cdc0f9641356dfaf6f6d93edfcac11af210
ms.openlocfilehash: 790af1544ed86155f5f864f3914b5fd1c4f42f4b

---

# <a name="create-dns-records-using-the-azure-cli"></a>Criar registros DNS usando a CLI do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [CLI do Azure](dns-getstarted-create-recordset-cli.md)

Este artigo guia você pelo processo de criação de registros e conjuntos de registros usando a CLI do Azure.

## <a name="introduction"></a>Introdução

Antes de criar registros DNS no DNS do Azure, primeiro você precisa entender como o DNS do Azure organiza registros DNS em conjuntos de registros DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Para obter mais informações sobre os registros DNS no DNS do Azure, confira [Zonas e registros DNS](dns-zones-records.md).

## <a name="create-a-record-set-and-record"></a>Criar registro e um conjunto de registros

Esta seção descreve como criar registros DNS no Azure DNS. Os exemplos pressupõem que você já [instalou a CLI do Azure, iniciou uma sessão e criou uma zona DNS](dns-getstarted-create-dnszone-cli.md).

Todos os exemplos nesta página usam o tipo 'A' de registro de DNS. Para obter outros tipos de registro e mais detalhes sobre como gerenciar registros DNS e conjuntos de registros, consulte [Gerenciar registros DNS e conjuntos de registro usando a CLI do Azure](dns-operations-recordsets-cli.md).

## <a name="create-a-dns-record"></a>Criar um registro DNS

Para criar um registro DNS, use o comando `azure network dns record-set add-record`. Para obter ajuda, consulte `azure network dns record-set add-record -h`.

Ao criar um registro, você precisa especificar o nome do grupo de recursos, o nome da zona, o nome do conjunto de registros, o tipo do registro e os detalhes do registro que está sendo criado.

Se o conjunto de registros não existir, este comando o criará para você. Se o conjunto de registros já existir, este comando adicionará o registro que você especifica para o conjunto de registros existente. 

Se um novo conjunto de registros for criado, um TTL padrão de 3600 será usado. Para obter instruções sobre como usar TTLs diferentes, veja [Gerenciar registros DNS no DNS do Azure usando a CLI do Azure](dns-operations-recordsets-cli.md).

O exemplo a seguir cria um registro A chamado *www* na zona *contoso.com* no grupo de recursos *MyResourceGroup*. O endereço IP do registro A é *1.2.3.4*.

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com www A -a 1.2.3.4
```

Para criar um conjunto de registros em vértices da zona (nesse caso, "contoso.com"), use o nome do registro "@",, incluindo as aspas:

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com "@" A -a 1.2.3.4
```

Os parâmetros usados para especificar os dados de registro variam dependendo do tipo de registro. Por exemplo, para um registro do tipo "A", você especifica o endereço IPv4 com o parâmetro `-a <IPv4 address>`. Consulte `azure network dns record-set add-record -h` para obter uma lista dos parâmetros para outros tipos de registro. Para obter exemplos de cada tipo de registro, veja [Gerenciar registros e conjuntos de registros DNS usando a CLI do Azure](dns-operations-recordsets-cli.md).


## <a name="verify-name-resolution"></a>Verificar a resolução de nome

Você pode testar se os registros DNS estão presentes nos servidores de nome DNS do Azure usando ferramentas de DNS, tais como nslookup, dig ou o [cmdlet Resolve-DnsName PowerShell](https://technet.microsoft.com/library/jj590781.aspx).

Se você ainda não delegou seu domínio para usar a nova zona no DNS do Azure, você precisa [direcionar a consulta DNS diretamente para um dos servidores de nome para a zona](dns-getstarted-create-dnszone.md#test-name-servers). Certifique-se de substituir os valores corretos para a zona dos seus registros no comando abaixo.

```
nslookup
> set type=A
> server ns1-01.azure-dns.com
> www.contoso.com

Server:  ns1-01.azure-dns.com
Address:  40.90.4.1

Name:    www.contoso.com
Address:  1.2.3.4
```

## <a name="next-steps"></a>Próximas etapas

Saiba como [delegar seu nome de domínio para os servidores de nome DNS do Azure](dns-domain-delegation.md)

Saiba como [gerenciar zonas DNS usando a CLI do Azure](dns-operations-dnszones-cli.md).

Saiba como [gerenciar registros DNS e conjuntos de registros usando a CLI do Azure](dns-operations-recordsets-cli.md).




<!--HONumber=Dec16_HO3-->


