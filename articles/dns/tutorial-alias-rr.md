---
title: Tutorial - Crie um registro de alias do DNS do Azure para se referir a um registro de recurso na zona.
description: Este tutorial mostra como configurar um registro de alias do DNS do Azure para fazer referência a um registro de recurso na zona.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 09c1768602fede51d7ff2b23f48278a1d0b0cd2a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990834"
---
# <a name="tutorial-create-an-alias-record-to-refer-to-a-zone-resource-record"></a>Tutorial: criar um registro de alias para se referir a um registro de recurso de zona

Registros de alias podem referenciar outros conjuntos de registros do mesmo tipo. Por exemplo, você pode ter um conjunto de registros DNS CNAME como um alias para outro conjunto de registros CNAME do mesmo tipo. Isso é útil se você quiser ter alguns conjuntos de registros como aliases e alguns como não-aliases em termos de comportamento.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um registro de alias para um registro de recurso na zona
> * Testar o registro de alias


Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
Você deve ter um nome de domínio disponível para hospedar no DNS do Azure para testar. Você deve ter controle total sobre esse domínio, incluindo a capacidade de definir os registros do servidor de nomes (NS) para o domínio.

Para obter instruções para hospedar seu domínio no DNS do Azure, consulte [Tutorial: hospede seu domínio no DNS do Azure](dns-delegate-domain-azure-dns.md).


## <a name="create-an-alias-record"></a>Criar um registro de alias

Crie um registro de alias que aponte para um registro de recurso na zona.

### <a name="create-the-target-resource-record"></a>Criar o registro de recurso de destino
1. Clique em sua zona DNS do Azure para abrir a zona.
2. Clique em **conjunto de registros**.
3. No **nome** tipo de caixa de texto **server**.
4. Para o **tipo**, selecione **um**.
5. No **endereço IP** caixa de texto, digite **10.10.10.10**.
6. Clique em **OK**.

### <a name="create-the-alias-record"></a>Crie o registro de alias
1. Clique em sua zona DNS do Azure para abrir a zona.
2. Clique em **conjunto de registros**.
3. No **nome** tipo de caixa de texto **testar**.
4. Para o **tipo**, selecione **um**.
5. Clique em **Yes** na **Alias do conjunto de registros** caixa de seleção e selecione o **conjunto de registros da zona** opção.
6. Para o **conjunto de registros da zona**, selecione o **server** registro.
7. Clique em **OK**.

## <a name="test-the-alias-record"></a>Testar o registro de alias

1. Comece sua ferramenta nslookup favorita. Navegue até [https://network-tools.com/nslook](https://network-tools.com/nslook).
2. Defina o tipo de consulta para o teste A de registros e pesquisa **. \<seu nome de domínio \>**. Você deve obter **10.10.10.10** da resposta.
3. No portal do Azure, altere o **server** um registro **10.11.11.11**.
4. Aguarde alguns minutos e use o nslookup novamente para o **teste**.
5. Você deve obter **10.11.11.11** da resposta.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você pode excluir o **server** e **testar** registros de recursos em sua zona.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um registro de alias para se referir a um registro de recurso dentro da zona. Para saber mais sobre os aplicativos Web e o DNS do Azure, continue com o tutorial para aplicativos Web.

> [!div class="nextstepaction"]
> [Criar registros DNS para um aplicativo Web em um domínio personalizado](./dns-web-sites-custom-domain.md)
