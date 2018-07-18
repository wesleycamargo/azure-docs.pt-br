---
title: Registrar o aplicativo – Serviços Cognitivos do Azure | Microsoft Docs
description: Um guia passo a passo sobre como registrar um novo aplicativo no Serviço de Decisão Personalizada do Azure
services: cognitive-services
author: slivkins
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/09/2018
ms.author: slivkins
ms.reviewer: marcozo;alekh;marossi
ms.openlocfilehash: 2aa8fbe77c11df4434eefa4c92d8529d5ca1d885
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364396"
---
# <a name="register-your-application"></a>Registre seu aplicativo

Para usar o Serviço de Decisão Personalizada em seu aplicativo, registre-o no portal. Este artigo explica como fazer isso.

1. Acesse a [página frontal](https://ds.microsoft.com/) do Serviço de Decisão Personalizada. Na faixa de opções, clique em **Meu Portal**, conforme realçado na imagem:

    ![Meu Portal](./media/portal.png)

    Caso você ainda não esteja conectado, o portal solicitará que você entre com sua [conta Microsoft](https://account.microsoft.com/account). Depois que você se conecta, o portal exibe sua conta Microsoft no canto superior direito da página.

2. Para registrar seu aplicativo, clique no botão **Novo Aplicativo**.

3. Na caixa de diálogo, escolha uma ID do Aplicativo para seu aplicativo. O Serviço de Decisão Personalizada exige uma ID exclusiva para cada aplicativo. Se alguém já usou essa ID, o sistema solicita que você escolha outra.

4. Especifique uma API de Conjunto de Ações. Essa configuração é um RSS feed ou um feed Atom que comunica o conteúdo disponível para seu aplicativo ao Serviço de Decisão Personalizada. Insira um nome para o feed e insira a URL da qual ele é fornecido. Para executar essa etapa mais tarde, clique no botão **Feeds** e, em seguida, no botão **Novo feed**. Um exemplo que cria um RSS feed é descrito mais adiante.

5. Para registrar seu aplicativo, marque a caixa de seleção **Aplicativo Personalizado** no canto inferior esquerdo. Insira uma [cadeia de conexão](../../storage/common/storage-configure-connection-string.md) para a conta de armazenamento do Azure na qual os dados do aplicativo foram registrados. Para obter mais informações sobre como criar uma conta de armazenamento, confira [Como criar, gerenciar ou excluir uma conta de armazenamento](../../storage/common/storage-create-storage-account.md).

### <a name="next-steps"></a>Próximas etapas

* Comece a otimizar [uma página da Web](custom-decision-service-get-started-browser.md) ou [um aplicativo para smartphone](custom-decision-service-get-started-app.md).
* Acompanhe um [tutorial](custom-decision-service-tutorial-news.md) para obter um exemplo mais detalhado.
* Confira a [Referência de API](custom-decision-service-api-reference.md) para saber mais sobre a funcionalidade fornecida.