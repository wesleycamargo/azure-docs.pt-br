---
title: Como criar e gerenciar uma zona DNS no portal do Azure | Microsoft Docs
description: "Saiba como criar zonas DNS no DNS do Azure. Este é uma guia passo a passo para criar e gerenciar sua primeira zona DNS usando o Portal do Azure."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f44c5ea1-4c85-469e-888e-5f5b34451664
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 36fa9cd757b27347c08f80657bab8a06789a3c2f
ms.openlocfilehash: 7119ce18e04b427c268d4d8636b1a5fa02ed0cc3
ms.lasthandoff: 02/27/2017

---

# <a name="create-a-dns-zone-in-the-azure-portal"></a>Criar uma zona DNS no portal do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [CLI 1.0 do Azure](dns-getstarted-create-dnszone-cli-nodejs.md)
> * [CLI 2.0 do Azure](dns-getstarted-create-dnszone-cli.md)

Este artigo explica as etapas para criar uma zona DNS usando o Portal do Azure. Você também pode criar uma zona DNS usando o PowerShell ou a CLI.

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="create-a-dns-zone"></a>Criar uma zona DNS

1. Entrar no Portal do Azure
2. No menu Hub, clique em **Novo > Rede >**, em seguida, clique em **Zona DNS** para abrir a folha Criar zona DNS.

    ![Zona DNS](./media/dns-getstarted-create-dnszone-portal/openzone650.png)

4. Na folha **Criar zona DNS** , dê um nome para sua zona DNS. Por exemplo, *contoso.com*.
 
    ![Criar zona](./media/dns-getstarted-create-dnszone-portal/newzone250.png)

5. Em seguida, especifique o grupo de recursos que você quer usar. Você pode criar um novo grupo de recursos ou selecionar um que já existe. Se você optar por criar um novo grupo de recursos, use a lista suspensa **Localização** para especificar a localização do grupo de recursos. Observe que essa configuração se refere ao local do grupo de recursos e não tem impacto sobre o local da zona DNS. O local da zona DNS sempre é "global" e não é exibido.

6. É possível deixar a caixa de seleção **Fixar no painel** marcada se você quiser localizar facilmente a nova zona no painel. Em seguida, clique em **Criar**.

    ![Fixar no painel](./media/dns-getstarted-create-dnszone-portal/pindashboard150.png)

7. Depois de clicar em Criar, você verá a nova zona sendo configurada no painel.

    ![Criando](./media/dns-getstarted-create-dnszone-portal/creating150.png)

8. Quando a nova zona tiver sido criada, a folha dela será aberta no painel.

## <a name="view-records"></a>Exibir registros

Criar uma zona DNS também cria os seguintes registros:

* O registro SOA (Início de Autoridade). O SOA está presente na raiz de cada zona DNS.
* Os registros NS (name server, servidor de nomes) autoritativos. Eles mostram quais servidores de nome estão hospedando a zona. DNS do Azure usa um pool de servidores de nomes; dessa forma servidores de nomes diferentes podem ser atribuídos a diferentes zonas no DNS do Azure. Consulte [Delegar um domínio ao DNS do Azure](dns-domain-delegation.md) para obter mais informações.

Na parte inferior da folha Zona DNS, é possível ver os conjuntos de registros para a zona DNS.

![zona](./media/dns-getstarted-create-dnszone-portal/viewzone500.png)

## <a name="test-name-servers"></a>Testar servidores de nome

Você pode testar se sua zona DNS está presente nos servidores de nome DNS do Azure usando ferramentas de DNS, tais como nslookup, dig ou o [cmdlet Resolve-DnsName do PowerShell](https://technet.microsoft.com/library/jj590781.aspx).

Se você ainda não delegou seu domínio para usar a nova zona no DNS do Azure, você precisa direcionar a consulta DNS diretamente para um dos servidores de nome para a zona. Os servidores de nomes da zona são fornecidos no portal do Azure:
    
![zona](./media/dns-getstarted-create-dnszone-portal/viewzonens500.png)

Certifique-se de substituir o servidor de nome correto de sua zona para o comando a seguir.

    nslookup
    > set type=SOA
    > server ns1-01.azure-dns.com
    > contoso.com

    Server: ns1-01.azure-dns.com
    Address:  208.76.47.1

    contoso.com
            primary name server = ns1-01.azure-dns.com
            responsible mail addr = azuredns-hostmaster.microsoft.com
            serial  = 1
            refresh = 3600 (1 hour)
            retry   = 300 (5 mins)
            expire  = 2419200 (28 days)
            default TTL = 300 (5 mins)

## <a name="next-steps"></a>Próximas etapas

Após criar uma zona DNS, [crie conjuntos de registros e registros](dns-getstarted-create-recordset-portal.md) para criar registros DNS para seu domínio da Internet.


