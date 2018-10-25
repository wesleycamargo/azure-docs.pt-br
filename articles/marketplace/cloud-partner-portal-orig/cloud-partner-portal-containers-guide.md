---
title: Contêineres | Microsoft Docs
description: Etapas para publicar uma imagem de contêiner.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 5653e4b2ec9aa5e21a107611b9d4142168630d4a
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805133"
---
<a name="publishing-a-container-image-in-the-cloud-partner-portal"></a>Publicação de uma imagem de contêiner no Portal do Cloud Partner
========================================================

Este artigo descreve como publicar uma nova imagem de contêiner no Portal do Cloud Partner.

Use as etapas a seguir para publicar uma nova imagem de contêiner.

1. Selecione **Nova oferta** e, em seguida, selecione **Contêineres**

    ![Selecione a opção Contêineres para a nova oferta](media/cpp-containers-guide/azure-container-offer.png)

2. Na guia Configuração da Oferta, em Identidade da Oferta, insira **ID da Oferta**, **ID do Editor** e **Nome**.

    ![Identidade da Oferta](media/cpp-containers-guide/containers-offer-settings.png)

3. Na guia SKUs, selecione **Nova SKU**.
    >[!NOTE]
    >Você pode adicionar mais de uma SKU.

    ![Prompt de Nova SKU](media/cpp-containers-guide/containers-sku-settings.png)

4. Forneça informações de SKU e o contêiner. Cada SKU corresponde a uma imagem de contêiner. Há duas partes em um SKU:

    -   Metadados da SKU
    -   Metadados do contêiner

    Os metadados da SKU contêm as informações de exibição para as imagens de contêiner.

    ![Metadados da SKU](media/cpp-containers-guide/containers-sku-details.png)

    Os metadados do contêiner têm informações de referência de seus detalhes de repositório da imagem dentro do ACR (registro de contêiner do Azure). O Azure Marketplace, em seguida, copia essa imagem no registro do marketplace público e é disponibilizado para os clientes após a certificação. Todas as solicitações do usuário do Azure para consumir uma imagem de contêiner são veiculadas do registro de contêiner do Marketplace.

   ![Metadados do contêiner](media/cpp-containers-guide/containers-image-repository.png)

    Os Detalhes do Repositório de Imagem na captura de tela anterior contêm os seguintes campos:

    -   **ID da Assinatura** – a ID da assinatura do Azure em que o registro do ACR está presente.
    -   **Nome do grupo de recursos** – o nome do grupo de recursos que contém o registro do ACR.
    -   **Nome do registro** – o nome do registro do ACR.
    -   **Nome do repositório** – o nome do repositório. Uma vez definido, esse valor não pode ser alterado posteriormente. Isso deve receber um nome exclusivo para que nenhuma outra oferta em sua conta tem o mesmo nome.
    -   **Nome de usuário** – o nome de usuário associado com o ACR (nome de usuário de administrador).
    -   **Senha** – a senha associada ao ACR.

    >[!NOTE]
    >O nome de usuário e senha são necessários para assegurar que os parceiros tenham acesso ao ACR mencionado no processo de publicação.

    Durante a publicação de uma imagem de contêiner, você também pode fornecer uma ou mais marcas de imagem. Além de uma marca de imagem, os parceiros também podem fornecer resumos do SHA. Adicione uma **marca de teste** à sua imagem para que você possa identificar a imagem durante o teste.

5. Na guia Marketplace, adicione o conteúdo de marketing específico.

    ![Informações do Marketplace](media/cpp-containers-guide/containers-marketplace-tab.png)

6. Na guia Suporte, insira seu contato de engenharia e informações de suporte ao cliente.

   ![Informações de suporte](media/cpp-containers-guide/containers-support-tab.png)

7. Selecione **Publicar** para publicar a oferta. Depois de selecionar Publicar, você verá uma linha do tempo que mostra as etapas envolvidas na publicação de sua imagem de contêiner.
