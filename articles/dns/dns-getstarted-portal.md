---
title: "Introdução ao DNS do Azure usando o Portal do Azure | Microsoft Docs"
description: "Saiba como criar uma zona e registro DNS no DNS do Azure. Este é uma guia passo a passo para criar e gerenciar sua primeira zona e registro DNS usando o Portal do Azure."
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: jonatul
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 0fee02286f6d5dd4614a933590cdab43cc69237f
ms.lasthandoff: 03/11/2017

---

# <a name="get-started-with-azure-dns-using-the-azure-portal"></a>Introdução ao DNS do Azure usando o Portal do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](dns-getstarted-portal.md)
> * [PowerShell](dns-getstarted-powershell.md)
> * [CLI 1.0 do Azure](dns-getstarted-cli-nodejs.md)
> * [CLI 2.0 do Azure](dns-getstarted-cli.md)

Este artigo explica as etapas para criar sua primeira zona e registro DNS usando o Portal do Azure. Você também pode executar essas etapas usando o Azure PowerShell ou a CLI do Azure de plataforma cruzada.

Uma zona DNS é usada para hospedar os registros DNS para um domínio específico. Para iniciar a hospedagem do seu domínio no DNS do Azure, você precisará criar uma zona DNS para esse nome de domínio. Cada registro DNS para seu domínio é criado dentro dessa zona DNS. Por fim, para publicar sua zona DNS na Internet, você precisa configurar os servidores de nome para o domínio. Cada uma dessas etapas é descrita abaixo.

## <a name="create-a-dns-zone"></a>Criar uma zona DNS

1. Entrar no Portal do Azure
2. No menu Hub, clique em **Novo > Rede >**, em seguida, clique em **Zona DNS** para abrir a folha Criar zona DNS.

    ![Zona DNS](./media/dns-getstarted-portal/openzone650.png)

4. Na folha **Criar zona DNS** , dê um nome para sua zona DNS. Por exemplo, *contoso.com*.
 
    ![Criar zona](./media/dns-getstarted-portal/newzone250.png)

5. Em seguida, especifique o grupo de recursos que você quer usar. Você pode criar um novo grupo de recursos ou selecionar um que já existe. Se você optar por criar um novo grupo de recursos, use a lista suspensa **Localização** para especificar a localização do grupo de recursos. Observe que essa configuração se refere ao local do grupo de recursos e não tem impacto sobre o local da zona DNS. O local da zona DNS sempre é "global" e não é exibido.

6. É possível deixar a caixa de seleção **Fixar no painel** marcada se você quiser localizar facilmente a nova zona no painel. Em seguida, clique em **Criar**.

    ![Fixar no painel](./media/dns-getstarted-portal/pindashboard150.png)

7. Depois de clicar em Criar, você verá a nova zona sendo configurada no painel.

    ![Criando](./media/dns-getstarted-portal/creating150.png)

8. Após a criação da nova zona, a folha dela será aberta no painel.


## <a name="create-a-dns-record"></a>Criar um registro DNS

O exemplo a seguir explica o processo de criação de um novo registro 'A'. Para outros tipos de registro e para modificar os registros existentes, confira [Gerenciar registros DNS e conjuntos de registros usando o Portal do Azure](dns-operations-recordsets-portal.md). 


1. Na parte superior da folha **zona DNS**, selecione **+Conjunto de registros** para abrir a folha **Adicionar conjunto de registros**.

    ![Novo conjunto de registros](./media/dns-getstarted-portal/newrecordset500.png)

4. Na folha **Adicionar conjunto de registros** , nomeie o conjunto de registros. Por exemplo, você pode nomear o conjunto de registros “**www**”.

    ![Adicionar conjunto de registros](./media/dns-getstarted-portal/addrecordset500.png)

5. Selecione o tipo de registro que você deseja criar. Para este exemplo, selecione **A**.
6. Defina o **TTL**. A vida útil padrão é de uma hora.
7. Adicione o endereço IP do registro.
8. Selecione **OK** na parte inferior da folha para criar o registro DNS.


## <a name="view-records"></a>Exibir registros

Na parte inferior da folha Zona DNS, é possível ver os registros da zona DNS. Você deve ver os registros NS e SOA padrão, que são criados em cada zona, além de quaisquer registros novos que você criou.

![zona](./media/dns-getstarted-portal/viewzone500.png)


## <a name="update-name-servers"></a>Atualizar servidores de nome

Quando você estiver satisfeito com a configuração de sua zona e registros DNS, configure seu nome de domínio para usar os servidores de nome DNS do Azure. Isso permite que outros usuários na Internet encontrem os registros DNS.

Os servidores de nomes da zona são fornecidos no portal do Azure:

![zona](./media/dns-getstarted-portal/viewzonens500.png)

Esses servidores de nome devem ser configurados com o registrador de nome de domínio (onde você adquiriu o nome de domínio). Seu registrador oferecerá a opção de configurar os servidores de nome do domínio. Para saber mais, confira [Delegar um domínio ao DNS do Azure](dns-domain-delegation.md).


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o DNS do Azure, veja [Visão geral do DNS do Azure](dns-overview.md).

Para saber mais sobre como gerenciar registros DNS no DNS do Azure, confira [Gerenciar registros DNS e conjuntos de registros usando o Portal do Azure](dns-operations-recordsets-portal.md).


