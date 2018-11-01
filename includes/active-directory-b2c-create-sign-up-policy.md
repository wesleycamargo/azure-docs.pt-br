---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/03/2016
ms.author: patricka
ms.openlocfilehash: 511b05e6cae769a5b39ae81a3e67efd05d374511
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134098"
---
Se quiser apenas habilitar a inscrição no aplicativo, use uma política de **inscrição**. Essa política descreve as experiências pelas quais os clientes passam durante a inscrição e o conteúdo dos tokens que o aplicativo recebe em inscrições com êxito.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

Clique em **Políticas de inscrição**.

Clique em **+Adicionar** , na parte superior da folha.

O **Nome** determina o nome da política de inscrição usado pelo seu aplicativo. Por exemplo, insira **SiUp**.

Clique em **Provedores de identidade** e selecione **Inscrição de email**. Opcionalmente, você também pode selecionar provedores de identidade social, se já configurado. Clique em **OK**.

Clique em **Atributos de inscrição**. Escolha aqui os atributos do consumidor que você deseja coletar durante a inscrição. Por exemplo, selecione **País/Região**, **Nome de Exibição** e **CEP**. Clique em **OK**.

Clique em **Declarações do aplicativo**. Escolha aqui as declarações que você deseja que sejam retornadas nos tokens enviados ao aplicativo após uma experiência de inscrição bem-sucedida. Por exemplo, selecione **Nome de exibição**, **Provedor de identidade**, **CEP**, **Novo usuário** e **ID de objeto do usuário**.

Clique em **Criar**. A política criada aparece como **B2C_1_SiUp** (o fragmento **B2C\_1\_** é adicionado automaticamente) na folha **Políticas de inscrição**.

Abra a política clicando em **B2C_1_SiUp**.

Selecione **Aplicativo Contoso B2C** na lista suspensa **Aplicativos** e `https://localhost:44321/` na lista suspensa **URL de Resposta/URI de Redirecionamento**.

Clique em **Executar agora**. Uma nova guia do navegador se abre e você poderá percorrer a experiência do consumidor de inscrição no aplicativo.

> [!NOTE]
> Leva até um minuto para a criação de políticas e as atualizações entrem em vigor.
>