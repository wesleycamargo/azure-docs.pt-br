---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: 9e232c5a8f1c2e38c04ea2236691554952f443e8
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752432"
---
## <a name="create-a-spatial-anchors-resource"></a>Criar um recurso Âncoras Espaciais

1. Navegue até o <a href="https://portal.azure.com" target="_blank">Portal do Azure</a>.

2. No menu esquerdo do portal do Azure, selecione **Criar um recurso**.

3. Pesquise "Âncoras Espaciais" na barra de pesquisa.

   ![Pesquisar Âncoras Espaciais](./media/spatial-anchors-get-started-create-resource/portal-search.png)

4. Selecione **Âncoras Espaciais (versão prévia)** para abrir uma caixa de diálogo e selecione **Criar**.

5. No formulário **Conta de Âncoras Espaciais**:

   1. Especifique um nome de recurso exclusivo.
   2. Selecione a assinatura para anexar o recurso.
   3. Criar um grupo de recursos selecionando **Criar novo** e dê ao grupo de recursos o nome **myResourceGroup** e selecione **OK**.
      [!INCLUDE [resource group intro text](resource-group.md)]
   4. Selecione um local (região) em que o recurso será colocado.
   5. Selecione **Novo** para começar a criar o recurso.

   ![Criar um recurso](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

6. Após a criação de recursos ser concluída com êxito, as propriedades do recurso poderão ser exibidas. Anote o valor da **ID da Conta** do recurso, uma vez que ele será necessário mais tarde.

   ![Exibir propriedades do recurso](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

7. Em **Configurações**, selecione **Chave** e anote o valor da **Chave primária**. Esse valor é o `Account Key` e será usado mais tarde.

   ![Exibir chave de conta](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
