---
title: Registrar um aplicativo com o ponto de extremidade do Azure AD v2.0 usando o portal | Microsoft Docs
description: Como registrar um aplicativo na Microsoft para habilitar a entrada e acessar os serviços da Microsoft usando o ponto de extremidade v2.0
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: bb2f701f-3bc3-4759-94a5-8b9d53a8a0b6
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2018
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: 8ab4e6b5b2813a216b6dd6f0fc108a09239ca9a6
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157836"
---
# <a name="how-to-register-an-app-with-the-v20-endpoint"></a>Como registrar um aplicativo com o ponto de extremidade v2.0
Para criar um aplicativo que aceite login pessoal da conta da Microsoft (MSA) e da conta de trabalho ou escola (Azure AD), você primeiro precisará registrar um aplicativo na Microsoft. Você não poderá usar nenhum dos seus aplicativos existentes com o Azure AD ou MSA, será necessário criar um novo.

> [!NOTE]
> Nem todos os recursos e cenários do Azure Active Directory têm suporte no ponto de extremidade v2.0. Para determinar se você deve usar o ponto de extremidade v 2.0, leia sobre o [limitações v 2.0](active-directory-v2-limitations.md).


## <a name="visit-the-microsoft-app-registration-portal"></a>Visite o Portal de Registro de Aplicativos da Microsoft
Primeiro, navegue até o portal de registro de aplicativos da Microsoft em [https://apps.dev.microsoft.com/](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). 

Entrar com uma conta da Microsoft pessoal, profissional ou escolar. Se você não tiver uma, inscreva-se para uma nova conta pessoal.

Pronto? Você deve agora estar olhando a lista de aplicativos da Microsoft, que provavelmente está vazia. Vamos mudar isso.

Clique em **Adicionar um aplicativo**e dê um nome a ele. O portal atribuirá ao seu aplicativo uma ID de aplicativo globalmente exclusiva que você usará posteriormente em seu código. Caso seu aplicativo inclua um componente do servidor que precisa de tokens de acesso para chamar APIs (tais como: Office, Azure ou sua própria API Web), também convém criar um **Segredo de Aplicativo** aqui.

Em seguida, adicione as Plataformas que seu aplicativo usará.

* Para aplicativos baseados na Web, forneça um **URI de Redirecionamento** em que é possível enviar mensagens de entrada.
* Para aplicativos móveis, copie o URI de redirecionamento criado automaticamente para você.
* Para APIs da Web, um escopo padrão para acessar a API da Web será criado automaticamente para você. Você pode optar por incluir escopos adicionais usando o botão **Adicionar escopo**. Você também pode adicionar qualquer aplicativo que já esteja pré-autorizado a usar sua API da Web usando o formulário **Aplicativos pré-autorizados**. 

Opcionalmente, você pode personalizar a aparência de sua página de entrada na Seção do perfil. Certifique-se de clicar em **Salvar** antes de continuar.

> [!NOTE]
> Quando você cria um aplicativo usando [https://apps.dev.microsoft.com/](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), ele será registrado no locatário inicial da conta que você usa para entrar no portal. Isso significa que não é possível registrar um aplicativo no seu locatário do Azure AD usando uma conta pessoal da Microsoft. Se você quiser registrar um aplicativo em um determinado locatário explicitamente, entre com uma conta criada originalmente no locatário em questão.


## <a name="build-a-quickstart-app"></a>Compilar um aplicativo de início rápido
Agora que você tem um aplicativo da Microsoft, poderá concluir um dos nossos tutoriais de início rápido da v2.0. Aqui estão algumas recomendações:

[!INCLUDE [active-directory-v2-quickstart-table](../../../includes/active-directory-v2-quickstart-table.md)]

