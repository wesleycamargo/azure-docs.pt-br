---
title: Criar um conjunto de registros e registros para uma zona DNS usando a CLI | Microsoft Docs
description: "Como criar registros de host para o DNS do Azure. Configuração dos conjuntos de registros e registros usando a CLI"
services: dns
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: 
ms.assetid: 02b897d3-e83b-4257-b96d-5c29aa59e843
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 40accee35eca32eefd4afc2315c70d51e0edcdcd


---
# <a name="create-dns-record-sets-and-records-by-using-cli"></a>Criar conjuntos de registros DNS e registros usando a CLI
> [!div class="op_single_selector"]
> * [Portal do Azure](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [CLI do Azure](dns-getstarted-create-recordset-cli.md)
> 
> 

Este artigo o guiará durante o processo de criação de registros e conjuntos de registros usando a CLI do Azure. Depois de criar a zona DNS, você precisa adicionar os registros DNS para seu domínio. Para fazer isso, primeiro você precisa entender os registros DNS e conjuntos de registros.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

## <a name="create-a-record-set-and-record"></a>Criar registro e um conjunto de registros
Nesta seção, mostraremos como criar registros e um conjunto de registros. Neste exemplo, você criará um conjunto de registros com o nome relativo “www” na zona DNS “contoso.com”. O nome totalmente qualificado do registro é “www.contoso.com”. O tipo de registro é “A” e o TTL é 60 segundos. Depois de concluir esta etapa, você criará um conjunto de registros vazio.

Para criar um conjunto de registros em vértices da zona (nesse caso, "contoso.com"), use o nome do registro "@",, incluindo as aspas. Isso é uma convenção comum do DNS.

### <a name="1-create-a-record-set"></a>1. Criar um conjunto de registros
Para criar conjunto de registros, use `azure network dns record-set create`. Especifique o grupo de recursos, o nome da zona, o nome relativo do conjunto de registros, o tipo de registro e o TTL. Se o parâmetro `--ttl` não for definido, o valor usará o padrão quatro (em segundos). Depois de concluir esta etapa, você terá um conjunto de registros "www" vazio.

*Uso: network dns record-set create <grupo-de-recursos> <nome-da-zona-dns> <name> <type> <ttl>*

    azure network dns record-set create myresourcegroup  contoso.com  www A  60

### <a name="2-add-records"></a>2. Adicionar registros
Para usar o conjunto de registros “www” recém-criado, você precisa adicionar registros a ele. Você adiciona registros aos conjuntos de registros usando `azure network dns record-set add-record`.

Os parâmetros para adicionar registros a um conjunto de registros variam dependendo do tipo de conjunto de registros. Por exemplo, ao usar um conjunto de registros do tipo “A”, você só poderá especificar os registros com o parâmetro `-a <IPv4 address>`.

Você pode adicionar os registros do IPv4 *A* ao conjunto de registros usando o seguinte comando:

*Uso: network dns record-set add-record <grupo-de-recursos> <nome-da-zona-dns> <nome-do-conjunto-de-recursos> <type>*

    azure network dns record-set add-record myresourcegroup contoso.com  www A  -a 134.170.185.46

## <a name="additional-record-type-examples"></a>Exemplos adicionais de tipo de registro
Os exemplos a seguir mostram como criar um conjunto de registros de cada tipo de registro. Cada conjunto de registros contém um único registro.

[!INCLUDE [dns-add-record-cli-include](../../includes/dns-add-record-cli-include.md)]

## <a name="next-steps"></a>Próximas etapas
Para gerenciar seus registros e conjunto de registros, confira [Gerenciar registros DNS e conjuntos de registros usando a CLI](dns-operations-recordsets-portal.md).

Para saber mais sobre o Azure DNS, confira [Visão geral do Azure DNS](dns-overview.md).




<!--HONumber=Nov16_HO2-->


