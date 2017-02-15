---
title: Gerenciar zonas DNS usando a CLI | Microsoft Docs
description: "Você pode gerenciar zonas DNS usando a CLI do Azure. Como atualizar, excluir e criar zonas DNS no DNS do Azure"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 8ab63bc4-5135-4ed8-8c0b-5f0712b9afed
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/07/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: bfbffe7843bc178cdf289c999925c690ab82e922
ms.openlocfilehash: 927503bb18a63db1da2c92e034dbccb7707afab4

---

# <a name="how-to-manage-dns-zones-using-cli"></a>Como gerenciar as zonas DNS usando a CLI

> [!div class="op_single_selector"]
> * [CLI do Azure](dns-operations-dnszones-cli.md)
> * [PowerShell](dns-operations-dnszones.md)

Este guia mostra como gerenciar seus recursos de zona DNS usando a CLI do Azure de plataforma cruzada.

Essas instruções usam a CLI do Microsoft Azure. Certifique-se de atualizar para a versão mais recente da CLI do Azure para usar esses comandos do DNS do Azure. Você pode instalar a CLI do Azure para Windows, Linux ou Mac. Mais informações estão disponíveis em [Instalar a CLI do Azure](../xplat-cli-install.md).

O Azure DNS é um serviço somente do Gerenciador de Recursos do Azure. Ele não tem um modelo de implantação “clássico”. Você precisa garantir que a CLI do Azure esteja configurada para usar o modo do Resource Manager. Você pode fazer isso usando o comando `azure config mode arm`.

Se você vir a mensagem “*Erro: 'dns' não é um comando do Azure*”, isso provavelmente significará que você está usando a CLI do Azure no modo de Gerenciamento de Serviços do Azure, não no modo do Resource Manager.

Todos os comandos da CLI relacionadas ao DNS do Azure começam com `azure network dns`. A ajuda está disponível para cada comando usando a opção `--help` (forma abreviada `-h`).  Por exemplo:

```azurecli
azure network dns -h
azure network dns zone -h
azure network dns zone create -h
```

## <a name="create-a-dns-zone"></a>Criar uma zona DNS

Uma zona DNS é criada usando o comando `azure network dns zone create` . Para obter ajuda, consulte `azure network dns zone create -h`.

Os exemplos a seguir mostram como criar uma zona DNS com ou sem [marcas do Azure Resource Manager](dns-zones-records.md#tags).

### <a name="to-create-a-dns-zone"></a>Para criar uma zona DNS

O exemplo a seguir cria uma zona DNS chamada *contoso.com* no grupo de recursos chamado *MyResourceGroup*. Use o exemplo para criar sua zona DNS, substituindo os valores pelos seus próprios.

```azurecli
azure network dns zone create MyResourceGroup contoso.com
```

### <a name="to-create-a-dns-zone-with-tags"></a>Para criar uma zona DNS com marcas.

O exemplo a seguir mostra como criar uma zona DNS com duas marcas, *project = demo* e *env = test*, usando o parâmetro `--tags` (forma abreviada `-t`).

```azurecli
azure network dns zone create MyResourceGroup contoso.com -t "project=demo";"env=test"
```

## <a name="get-a-dns-zone"></a>Obter uma zona DNS

Para recuperar uma zona DNS, use `azure network dns zone show`. Para obter ajuda, consulte `azure network dns zone show -h`.

O exemplo a seguir retorna a zona DNS *contoso.com* e seus dados associados do grupo de recursos *MyResourceGroup*. 

```azurecli
azure network dns zone show MyResourceGroup contoso.com

info:    Executing command network dns zone show
+ Looking up the dns zone "contoso.com"
data:    Id                              : /subscriptions/.../contoso.com
data:    Name                            : contoso.com
data:    Type                            : Microsoft.Network/dnszones
data:    Location                        : global
data:    Number of record sets           : 2
data:    Max number of record sets       : 5000
data:    Name servers:
data:        ns1-01.azure-dns.com.
data:        ns2-01.azure-dns.net.
data:        ns3-01.azure-dns.org.
data:        ns4-01.azure-dns.info.
data:    Tags                            : project=demo;env=test
info:    network dns zone show command OK
```

Observe que registros DNS não são retornados por `azure network dns zone show`. Para listar registros DNS, use `azure network dns record-set list`.


## <a name="list-dns-zones"></a>Listar as zonas DNS

Para enumerar zonas DNS, use `azure network dns zone list`. Para obter ajuda, consulte `azure network dns zone list -h`.

Especificar o grupo de recursos lista apenas as zonas dentro do grupo de recursos:

```azurecli
azure network dns zone list MyResourceGroup
```

Omitir o grupo de recursos lista todas as zonas na assinatura:

```azurecli
azure network dns zone list 
```

## <a name="update-a-dns-zone"></a>Atualizar uma zona DNS

As alterações a um recurso da zona DNS podem ser feitas usando o `azure network dns zone set`. Para obter ajuda, consulte `azure network dns zone set -h`.

Esse comando não atualiza nenhum dos conjuntos de registros DNS dentro da zona (consulte [Como gerenciar registros DNS](dns-operations-recordsets.md)). Ele só é usado para atualizar as propriedades do recurso da zona em si. Atualmente, essas propriedades são limitadas às [“marcas” do Azure Resource Manager](dns-zones-records.md#tags) para o recurso de zona.

O exemplo a seguir mostra como atualizar as marcas em uma zona DNS. As marcas existentes são substituídas pelo valor especificado.

```azurecli
azure network dns zone set MyResourceGroup contoso.com -t "team=support"
```

## <a name="delete-a-dns-zone"></a>Excluir uma zona DNS

As zonas DNS podem ser excluídas por meio do `azure network dns zone delete`. Para obter ajuda, consulte `azure network dns zone delete -h`.

> [!NOTE]
> Excluir uma zona DNS também excluirá todos os registros DNS dentro da zona. Essa operação não pode ser desfeita. Se a zona DNS estiver em uso, serviços que usam a zona falharão quando a zona for excluída.
>
>Para se proteger contra a exclusão acidental da zona, consulte [Proteger registros e zonas DNS](dns-protect-zones-recordsets.md).

Esse comando solicita uma confirmação. A opção `--quiet` (forma abreviada `-q`) suprime esse prompt.

O exemplo a seguir mostra como excluir a zona *contoso.com* de grupo de recursos *MyResourceGroup*.

```azurecli
azure network dns zone delete MyResourceGroup contoso.com
```

## <a name="next-steps"></a>Próximas etapas

Saiba como [gerenciar conjuntos de registros e registros](dns-getstarted-create-recordset-cli.md) em sua zona DNS.
<br>
Saiba como [delegar seu domínio ao DNS do Azure](dns-domain-delegation.md).




<!--HONumber=Dec16_HO2-->


