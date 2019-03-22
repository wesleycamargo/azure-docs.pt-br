---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: 17c0213d63879687e9c6d5f8dca06b9113c44af8
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2019
ms.locfileid: "58113866"
---
Se quiser apenas habilitar a inscrição no aplicativo, use um fluxo de usuário de **inscrição**. Esse fluxo de usuário descreve as experiências pelas quais os clientes passam durante a inscrição e o conteúdo dos tokens que o aplicativo recebe em inscrições com êxito.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

Em **Gerenciar**, selecione **Fluxos dos usuários**.

Clique em +**Novo fluxo de usuário** na parte superior da folha.

Em **Selecionar um tipo de fluxo de usuário**, selecione **Todos** e, em seguida, selecione a versão da **Inscrição** que quer usar.

O **Nome** determina o nome do fluxo de usuário de inscrição usado pelo aplicativo. Por exemplo, insira **SiUp**.

Em **Provedores de identidade**, selecione **Inscrição por email**. Opcionalmente, você também pode selecionar provedores de identidade social, se já configurado.

Em **Atributos de usuário e declarações**, clique em **Mostrar mais**.

Na coluna **Coletar atributo**, escolha os atributos que você quer coletar do cliente durante a inscrição. Por exemplo, selecione **País/Região**, **Nome de Exibição** e **CEP**.

Na coluna **Retornar declaração**, escolha as declarações que você quer retornar nos tokens enviados de volta ao aplicativo após uma experiência de inscrição com êxito. Por exemplo, selecione **Nome de exibição**, **Provedor de identidade**, **CEP**, **Novo usuário** e **ID de objeto do usuário**.

Clique em **OK**.

Clique em **Criar**. O fluxo de usuário criado aparece como **B2C_1_SiUp** (o fragmento **B2C\_1\_** é adicionado automaticamente).

Clique em **Executar fluxo de usuário**.

Selecione **Aplicativo Contoso B2C** na lista suspensa **Aplicativo** e `https://localhost:44321/` na lista suspensa **URL de Resposta**.

Clique em **Executar fluxo de usuário**. Uma nova guia do navegador se abre e você poderá percorrer a experiência do consumidor de inscrição no aplicativo.

> [!NOTE]
> Leva até um minuto para que a criação do fluxo de usuário e as atualizações entrem em vigor.
>