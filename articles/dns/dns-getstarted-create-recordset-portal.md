---
title: Criar um conjunto de registros e registros para uma zona DNS usando o portal do Azure | Microsoft Docs
description: Como criar registros de host para o DNS do Azure e criar conjuntos de registros e registros usando o portal do Azure
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f93905f4-e82e-45db-b490-878d318e6aba
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: bfbffe7843bc178cdf289c999925c690ab82e922
ms.openlocfilehash: b3951106fe2e8607e65bd0ae47fa2ea3346b8ca5

---

# <a name="create-dns-record-sets-and-records-by-using-the-azure-portal"></a>Criar registros e conjuntos de registros DNS usando o portal do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [CLI do Azure](dns-getstarted-create-recordset-cli.md)

Este artigo o guiará durante o processo de criação de registros e conjuntos de registros usando o portal do Azure. Para fazer isso, primeiro você precisa entender os registros DNS e conjuntos de registros.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Todos os exemplos nesta página usam o tipo 'A' de registro de DNS. O processo para outros tipos de registro é semelhante.

Se o novo registro tiver o mesmo nome e tipo que um registro existente, você precisará adicioná-lo ao conjunto de registros existente&mdash;consulte [Gerenciar registros e conjuntos de registros DNS usando o portal do Azure](dns-operations-recordsets-portal.md). Se o novo registro tiver um nome e tipo diferente para todos os registros existentes, você precisará criar um novo conjunto de registros, conforme explicado abaixo.

## <a name="create-records-in-a-new-record-set"></a>Criar registros em um novo conjunto de registros

O exemplo a seguir explica o processo de criação de um registro e conjunto de registros usando o portal do Azure.

1. Entre no portal.
2. Vá para a folha da **zona DNS** na qual você deseja criar um conjunto de registros.
3. Na parte superior da folha **zona DNS**, selecione **+Conjunto de registros** para abrir a folha **Adicionar conjunto de registros**.

    ![Novo conjunto de registros](./media/dns-getstarted-create-recordset-portal/newrecordset500.png)

4. Na folha **Adicionar conjunto de registros** , nomeie o conjunto de registros. Por exemplo, você pode nomear o conjunto de registros “**www**”.

    ![Adicionar conjunto de registros](./media/dns-getstarted-create-recordset-portal/addrecordset500.png)

5. Selecione o tipo de registro que você deseja criar. Por exemplo, selecione **A**.
6. Defina o **TTL**. A vida útil padrão no portal é de uma hora.
7. Adicione os detalhes de cada registro no conjunto de registros. Nesse caso, como o tipo de registro é “A”, você precisa adicionar os endereços IP do registro A, um endereço IP por linha.
8. Depois de concluir a inclusão de endereços IP, selecione **OK** na parte inferior da folha. O conjunto de registros DNS será criado.

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

Para gerenciar seus registros e seu conjunto de registros, confira [Gerenciar registros DNS e conjuntos de registros usando o portal do Azure](dns-operations-recordsets-portal.md).



<!--HONumber=Dec16_HO2-->


