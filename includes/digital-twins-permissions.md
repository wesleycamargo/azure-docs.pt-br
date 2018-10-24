---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: azure-digital-twins
author: alinamstanciu
ms.service: azure-digital-twins
ms.topic: include
ms.date: 09/19/2018
ms.author: alinast
ms.custom: include file
ms.openlocfilehash: d08b7a1256a26d1d7d39481ba15a8637339063ea
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49322688"
---
1. No [portal do Azure](https://portal.azure.com), abra **Azure Active Directory** no painel de navegação à esquerda e, em seguida, abra o painel **Propriedades**. Cópia da **ID de diretório** em um arquivo temporário. Você usará esse valor para configurar o aplicativo de exemplo na seção a seguir.

    ![ID do diretório do Azure Active Directory](./media/digital-twins-permissions/aad-app-reg-tenant.png)

1. Abra o painel **Registros de aplicativo** e, em seguida, clique no botão **Novo registro de aplicativo**.
    
    ![Novo registro de aplicativo do Azure Active Directory](./media/digital-twins-permissions/aad-app-reg-start.png)

1. Dê um nome amigável para esse registro de aplicativo no campo **Nome**. Escolha **tipo de aplicativo** como **_Nativo_** e **URI de redirecionamento** como **_https://microsoft.com_**. Clique em **Criar**.

    ![Criar novo registro de aplicativo do Azure Active Directory](./media/digital-twins-permissions/aad-app-reg-create.png)

1. Abra o aplicativo registrado e copie o valor do campo **ID do aplicativo** em um arquivo temporário; esse valor identifica seu aplicativo do Azure Active Directory. Você usará esse ID do aplicativo para configurar o aplicativo de exemplo nas seções a seguir.

    ![ID do aplicativo do Azure Active Directory](./media/digital-twins-permissions/aad-app-reg-app-id.png)

1. Abra o painel de registro de aplicativo e clique em **Configurações** > **Permissões necessárias**:
    - Clique em **Adicionar** na parte superior esquerda para abrir o painel **Adicionar acesso à API**.
    - Clique em **Selecionar uma API** e pesquise por **Gêmeos Digitais do Azure**. Se sua pesquisa não localizar a API, pesquise por **Espaços Inteligentes do Azure** em vez disso.
    - Selecione a opção **Gêmeos Digitais do Azure (Serviço de Espaços Inteligentes do Azure)** e clique em **Selecionar**.
    - Clique em **Selecionar permissões**. Marque a caixa de permissões delegadas de **Acesso de leitura/gravação** e clique em **Selecionar**.
    - Clique em **Concluído** no painel **Adicionar acesso à API**.
    - No painel **Permissões necessárias**, clique no botão **Conceder permissões** e, em seguida, aceite a confirmação que aparece.

       ![Adicionar API de registro de aplicativo do Azure Active Directory](./media/digital-twins-permissions/aad-app-req-permissions.png)
