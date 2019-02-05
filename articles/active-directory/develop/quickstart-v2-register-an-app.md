---
title: Registrar um aplicativo no ponto de extremidade v2.0 do Azure AD | Microsoft Docs
description: Saiba como registrar um aplicativo na Microsoft para habilitar a conexão e acessar os serviços da Microsoft usando o ponto de extremidade v2.0 do Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: bb2f701f-3bc3-4759-94a5-8b9d53a8a0b6
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/02/2018
ms.author: celested
ms.reviewer: lenalepa
ms.custom: aaddev
ms.openlocfilehash: 20e19664963f66954f9d46a1b596a34b7d744f48
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2019
ms.locfileid: "55093216"
---
# <a name="quickstart-register-an-app-with-the-azure-active-directory-v20-endpoint"></a>Início Rápido: Registrar um aplicativo com o ponto de extremidade v2.0 do Azure Active Directory

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Para criar um aplicativo que aceite conexão na conta Microsoft pessoal (MSA) e na conta corporativa ou de estudante (Azure AD), você primeiro precisará registrar um aplicativo no ponto de extremidade v2.0 do Azure AD (Azure Active Directory). Você não poderá usar nenhum dos seus aplicativos existentes com o Azure AD ou MSA, será necessário criar um novo.

Nem todos os recursos e cenários do Azure Active Directory têm suporte no ponto de extremidade v2.0. Para determinar se você deve usar o ponto de extremidade v 2.0, leia sobre o [limitações v 2.0](active-directory-v2-limitations.md).

> [!NOTE]
> Registrando um novo aplicativo? Experimente o novo **Registros de aplicativos (Versão prévia)** no portal do Azure. Consulte [Registrar um aplicativo (Versão prévia)](quickstart-register-app.md) para começar a usar.

## <a name="step-1-sign-in-to-the-microsoft-application-registration-portal"></a>Etapa 1: Entrar no portal de registro de aplicativo da Microsoft

1. Navegue até o portal de registro de aplicativos da Microsoft em [https://apps.dev.microsoft.com/](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).
1. Entrar com uma conta da Microsoft pessoal, profissional ou escolar. Se você não tiver uma, inscreva-se para uma nova conta pessoal.
1. Pronto? Você deve agora estar olhando a lista de aplicativos da Microsoft, que provavelmente está vazia. Vamos mudar isso.

## <a name="step-2-register-an-app"></a>Etapa 2: Registrar um aplicativo

1. Selecione **Adicionar um aplicativo** e dê um nome a ele.
    O portal atribuirá ao seu aplicativo uma ID de aplicativo globalmente exclusiva que você usará posteriormente em seu código. Se o aplicativo inclui um componente do lado do servidor que precisa de tokens de acesso para chamar as APIs (pense em: Office, Azure ou sua própria API Web), é recomendável criar um **Segredo de Aplicativo** aqui também.
1. Em seguida, adicione as Plataformas que seu aplicativo usará.
    * Para aplicativos baseados na Web, forneça um **URI de Redirecionamento** em que é possível enviar mensagens de entrada.
    * Para aplicativos móveis, copie o URI de redirecionamento criado automaticamente para você.
    * Para APIs da Web, um escopo padrão para acessar a API da Web será criado automaticamente para você.
        Você pode adicionar escopos adicionais usando o botão **Adicionar escopo**. Você também pode adicionar qualquer aplicativo que já esteja pré-autorizado a usar sua API da Web usando o formulário **Aplicativos pré-autorizados**.
1. Como opção, personalize a aparência de sua página de entrada na seção **Perfil**. 
1. **Salve** suas alterações antes de prosseguir.

> [!NOTE]
> Quando você registra um aplicativo usando [https://apps.dev.microsoft.com/](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), ele será registrado no locatário inicial da conta que você usa para entrar no portal. Isso significa que não é possível registrar um aplicativo no seu locatário do Azure AD usando uma conta pessoal da Microsoft. Se você quiser registrar um aplicativo em um determinado locatário explicitamente, entre com uma conta criada originalmente no locatário em questão.

## <a name="next-steps"></a>Próximas etapas

Agora que você tem um aplicativo da Microsoft, poderá concluir um dos nossos guias de início rápido 2 da v2.0. Aqui estão algumas recomendações:

[!INCLUDE [active-directory-v2-quickstart-table](../../../includes/active-directory-v2-quickstart-table.md)]
