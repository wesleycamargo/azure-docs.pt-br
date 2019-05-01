---
title: SKUs disponíveis para uma imagem de contêineres do Azure | O Azure Marketplace
description: Configurar SKUs para um contêiner do Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 6953329bfabe99fc4bb28f2494cb412ba9cbbba0
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942923"
---
# <a name="container-skus-tab"></a>Guia de SKUs de contêiner

A guia **SKUs** da página **Nova Oferta** permite criar uma ou mais SKUs e associá-las à sua nova oferta.  É possível usar diferentes SKUs para diferenciar uma solução por conjuntos de recursos, modelos de cobrança ou outra característica.

## <a name="sku-settings"></a>Configurações de SKU

Quando você começa a criar uma nova oferta, não há SKUs associados à oferta. Para criar uma nova SKU, siga estas etapas:

1. Na guia SKUs, selecione **Nova SKU**

   ![Prompt de Nova SKU](./media/containers-sku-settings.png)

2. Forneça as informações de SKU e o contêiner exigidas. Cada SKU corresponde a uma imagem de contêiner. Há duas partes em um SKU:

    -   Metadados da SKU
    -   Metadados do contêiner


### <a name="sku-metadata"></a>Metadados da SKU

Os metadados de SKU contêm informações de exibição de vitrine para a listagem de contêiner.

![Metadados da SKU](./media/containers-sku-details.png)


### <a name="container-metadata"></a>Metadados do contêiner

Os metadados do contêiner têm informações de referência de seus detalhes de repositório da imagem dentro do ACR (Registro de Contêiner do Azure). O Azure Marketplace copia essa imagem para um registro público específico do Marketplace e, em seguida, disponibiliza a imagem para os clientes após a certificação. Todas as solicitações do usuário do Azure para consumir uma imagem do contêiner do Azure Marketplace são exibidas no registro público do Marketplace, não no ACR.

![Metadados do contêiner](./media/containers-image-repository.png)
    
O **detalhes do repositório de imagem** na tela anterior captura contém os campos a seguir.  Os campos obrigatórios são indicados por um asterisco (*).

-   **ID da assinatura\***  -ID da assinatura do Azure em que o ACR está presente.
-   **Nome do grupo de recursos\***  -o nome do grupo de recursos do ACR.
-   **Nome do registro\***  -nome do ACR.
-   **Nome do repositório\***  -o nome do repositório. Após a definição do nome, esse valor não pode ser alterado. Use um nome exclusivo para evitar um conflito com outras ofertas em sua conta.
-   **Nome de usuário\***  -o nome de usuário (nome de usuário de administrador) associado à imagem ACR.
-   **Senha\***  -a senha associada com a imagem ACR.

    >[!NOTE]
    >O nome de usuário e senha são necessários para assegurar que os parceiros tenham acesso ao ACR mencionado no processo de publicação.


### <a name="image-version"></a>Versão da imagem

Ao publicar uma imagem de contêiner, você pode fornecer uma ou mais marcas de imagem e resumos do SHA.

**Marca de imagem\* ou Digest**
 
- Essa marca ou resumo deve incluir pelo menos uma marca `latest` e uma marca de versão (por exemplo, começando com `xx.xx.xx-` em que xx é um número). Devem ser [marcas de manifesto](https://github.com/estesp/manifest-tool) para segmentar várias plataformas. Todas as marcas referenciadas por uma marca de manifesto também deverão ser adicionadas para que possamos enviá-las. 
- Você pode adicionar várias versões de um contêiner usando marcas. Todas as marcas de manifesto (exceto `latest`) devem começar com `X.Y-` ou `X.Y.Z-` em que X, Y, Z são inteiros. <br/> Por exemplo, se uma marca `latest` apontar para `1.0.1-linux-x64`, `1.0.1-linux-arm32` e `1.0.1-windows-arm32`, essas marcas deverão ser adicionadas aqui.

>[!NOTE]
>Lembre-se de adicionar uma **marca de teste** à sua imagem para que você possa identificar a imagem durante o teste.


## <a name="next-steps"></a>Próximas etapas

Use a [guia Marketplace](./cpp-marketplace-tab.md) para criar uma descrição do Marketplace para sua oferta. 
