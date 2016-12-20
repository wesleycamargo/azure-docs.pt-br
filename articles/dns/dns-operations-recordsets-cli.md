---
title: Gerenciar conjuntos de registros DNS e registros no DNS do Azure usando a CLI do Azure | Microsoft Docs
description: "Gerenciando conjuntos de registros DNS e registros no DNS do Azure ao hospedar seu domínio no DNS do Azure. Todos os comandos da CLI para operações em conjuntos de registros e registros."
services: dns
documentationcenter: na
author: jtuliani
manager: carmonm
ms.assetid: 5356a3a5-8dec-44ac-9709-0c2b707f6cb5
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2016
ms.author: jonatul
translationtype: Human Translation
ms.sourcegitcommit: 02d720a04fdc0fa302c2cb29b0af35ee92c14b3b
ms.openlocfilehash: 2bc18d618cf8838209bea9f8a2d323e3b1042709

---

# <a name="manage-dns-records-and-record-sets-by-using-cli"></a>Gerenciar registros DNS e conjuntos de registros usando a CLI

> [!div class="op_single_selector"]
> * [Portal do Azure](dns-operations-recordsets-portal.md)
> * [CLI do Azure](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Este artigo mostra como gerenciar conjuntos de registros e registros da zona DNS usando a CLI (interface de linha de comando) do Azure de plataforma cruzada.

É importante compreender a diferença entre os conjuntos de registros DNS e registros DNS individuais. Um conjunto de registros é uma coleção de registros em uma zona que tem o mesmo nome e o mesmo tipo. Para saber mais, confira [Noções básicas sobre conjuntos de registros e registros](dns-getstarted-create-recordset-cli.md).

## <a name="configure-the-cross-platform-azure-cli"></a>Configurar a CLI do Azure de plataforma cruzada

O Azure DNS é um serviço somente do Gerenciador de Recursos do Azure. Ele não tem uma API de Gerenciamento de Serviços do Azure. Verifique se a CLI do Azure está configurada para usar o modo do Gerenciador de Recursos usando o comando `azure config mode arm` .

Se você vir o **Erro: 'dns' não é um comando do Azure**, isso provavelmente significará que você está usando a CLI do Azure no modo do Gerenciamento de Serviços, não no modo do Gerenciador de Recursos.

## <a name="create-a-new-record-set-and-record"></a>Criar registro e um novo conjunto de registros

Para criar um novo conjunto de registros no portal do Azure, confira [Criar registros e um conjunto de registros](dns-getstarted-create-recordset-cli.md).

## <a name="retrieve-a-record-set"></a>Recuperar um conjunto de registros

Para recuperar um conjunto de registros existente, use `azure network dns record-set show`. Especifique o grupo de recursos, o nome da zona, o nome relativo do conjunto de registros e o tipo de registro. Use o exemplo abaixo, substituindo os valores existentes pelos seus.

```azurecli
azure network dns record-set show myresourcegroup contoso.com www A
```

## <a name="list-record-sets"></a>Listar os conjuntos de registros

Você pode listar todos os registros em uma zona DNS com o comando `azure network dns record-set list` . Você precisa especificar o nome do grupo de recursos e o nome da zona.

### <a name="to-list-all-record-sets"></a>Para listar todos os conjuntos de registros

Este exemplo retorna todos os conjuntos de registros, independentemente do nome ou do tipo de registro:

```azurecli
azure network dns record-set list myresourcegroup contoso.com
```

### <a name="to-list-record-sets-of-a-given-type"></a>Para listar conjuntos de registros de um tipo específico

Este exemplo retorna todos os conjuntos de registros correspondentes ao tipo de registro determinado (nesse caso, registros “A”):

```azurecli
azure network dns record-set list myresourcegroup contoso.com A
```

## <a name="add-a-record-to-a-record-set"></a>Adicionar um registro a um conjunto de registros

Você adiciona registros ao conjuntos de registros com o comando `azure network dns record-set add-record`. Os parâmetros para adicionar registros a um conjunto de registros variam dependendo do tipo do registro definido. Por exemplo, quando você usa um conjunto de registros do tipo “A”, é possível apenas especificar os registros com o parâmetro `-a <IPv4 address>`.

Para criar um conjunto de registros, use o comando `azure network dns record-set create`. Especifique o grupo de recursos, o nome da zona, o nome relativo do conjunto de registros, o tipo de registro e a TTL (vida útil). Se o parâmetro `--ttl` não for definido, o valor usará o padrão de quatro (em segundos).

```azurecli
azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300
```

Depois de criar o conjunto de registros “A”, adicione o endereço IPv4 usando o comando `azure network dns record-set add-record`.

```azurecli
azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1
```

Os exemplos a seguir mostram como criar um conjunto de registros de cada tipo de registro. Cada conjunto de registros contém um único registro.

[!INCLUDE [dns-add-record-cli-include](../../includes/dns-add-record-cli-include.md)]

## <a name="update-a-record-in-a-record-set"></a>Atualizar um registro em um conjunto de registros

### <a name="to-add-another-ip-address-1234-to-an-existing-a-record-set-www"></a>Para adicionar outro endereço IP (1.2.3.4) a um conjunto de registros “A” (“www”) existente:

    azure network dns record-set add-record  myresourcegroup contoso.com  A
    -a 1.2.3.4
    info:    Executing command network dns record-set add-record
    Record set name: www
    + Looking up the dns zone "contoso.com"
    + Looking up the DNS record set "www"
    + Updating DNS record set "www"
    data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/a/www
    data:    Name                            : www
    data:    Type                            : Microsoft.Network/dnszones/a
    data:    Location                        : global
    data:    TTL                             : 4
    data:    A records:
    data:        IPv4 address                : 192.168.1.1
    data:        IPv4 address                : 1.2.3.4
    data:
    info:    network dns record-set add-record command OK

### <a name="to-remove-an-existing-value-from-a-record-set"></a>Para remover um valor existente de um conjunto de registros

Use `azure network dns record-set delete-record`.

    azure network dns record-set delete-record myresourcegroup contoso.com www A -a 1.2.3.4
    info:    Executing command network dns record-set delete-record
    + Looking up the DNS record set "www"
    Delete DNS record? [y/n] y
    + Updating DNS record set "www"
    data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/A/www
    data:    Name                            : www
    data:    Type                            : Microsoft.Network/dnszones/A
    data:    Location                        : global
    data:    TTL                             : 4
    data:    A records:
    data:    IPv4 address                    : 192.168.1.1
    data:
    info:    network dns record-set delete-record command OK

## <a name="remove-a-record-from-a-record-set"></a>Remover um registro de um conjunto de registros

Os registros podem ser removidos de um conjunto de registros com `azure network dns record-set delete-record`. O registro que está sendo removido deve ser uma correspondência exata com um registro existente, em todos os parâmetros.

Remover o último registro de um conjunto de registros não exclui o conjunto de registros. Para obter mais informações, confira a seção deste artigo intitulada [Excluir um conjunto de registros](#delete).

```azurecli
azure network dns record-set delete-record myresourcegroup contoso.com www A -a 192.168.1.1

azure network dns record-set delete myresourcegroup contoso.com www A
```

### <a name="remove-an-aaaa-record-from-a-record-set"></a>Remover um registro AAAA de um conjunto de registros

```azurecli
azure network dns record-set delete-record myresourcegroup contoso.com test-aaaa  AAAA -b "2607:f8b0:4009:1803::1005"
```

### <a name="remove-a-cname-record-from-a-record-set"></a>Remover um registro CNAME de um conjunto de registros

```azurecli
azure network dns record-set delete-record myresourcegroup contoso.com test-cname CNAME -c www.contoso.com
```

### <a name="remove-an-mx-record-from-a-record-set"></a>Remover um registro MX de um conjunto de registros

```azurecli
azure network dns record-set delete-record myresourcegroup contoso.com "@" MX -e "mail.contoso.com" -f 5
```

### <a name="remove-an-ns-record-from-record-set"></a>Remover um registro NS de um conjunto de registros

```azurecli
azure network dns record-set delete-record myresourcegroup contoso.com  "test-ns" NS -d "ns1.contoso.com"
```

### <a name="remove-a-ptr-record-from-a-record-set"></a>Remover um registro PTR de um conjunto de registros

Nesse caso, “my-arpa-zone.com” representa a zona ARPA que representa o intervalo de IP.  Cada registro PTR definido nesta zona corresponde a um endereço IP nesse intervalo de IP.

```azurecli
azure network dns record-set delete-record myresourcegroup my-arpa-zone.com "10" PTR -P "myservice.contoso.com"
```

### <a name="remove-an-srv-record-from-a-record-set"></a>Remover um registro SRV de um conjunto de registros

```azurecli
azure network dns record-set delete-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 -w 5 -o 8080 -u "sip.contoso.com"
```

### <a name="remove-a-txt-record-from-a-record-set"></a>Remover um registro TXT de um conjunto de registros

```azurecli
azure network dns record-set delete-record myresourcegroup contoso.com  "test-TXT" TXT -x "this is a TXT record"
```

## <a name="a-namedeleteadelete-a-record-set"></a><a name="delete"></a>Excluir um conjunto de registros

Os conjuntos de registros podem ser excluídos usando o cmdlet `Remove-AzureRmDnsRecordSet` . Não é possível excluir os conjuntos de registros SOA e NS no apex da zona (nome = "@") que foram criados automaticamente quando a zona foi criada. Eles serão excluídos automaticamente se a zona for excluída.

No exemplo a seguir, o conjunto de registros “A” “test-a” será removido da zona DNS “contoso.com”:

```azurecli
azure network dns record-set delete myresourcegroup contoso.com  "test-a" A
```

A opção `-q` opcional pode ser usada para suprimir o aviso de confirmação.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o DNS do Azure, consulte [Visão geral do DNS do Azure](dns-overview.md). Para obter informações sobre como automatizar o DNS, confira [Criando zonas DNS e conjuntos de registros usando o SDK do .NET](dns-sdk.md).

Se você quiser trabalhar com registros DNS reversos, veja [Como gerenciar registros DNS reversos para seus serviços usando a CLI do Azure](dns-reverse-dns-record-operations-cli.md).



<!--HONumber=Nov16_HO3-->


