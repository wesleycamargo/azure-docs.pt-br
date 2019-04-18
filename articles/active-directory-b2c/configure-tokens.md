---
title: Configurar tokens - Azure Active Directory B2C | Microsoft Docs
description: Saiba como configurar as configurações de compatibilidade e o tempo de vida do token no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 2455b8c12ed042af3a06a158a4e5c60a0aee748b
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59685698"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Configurar tokens no Azure Active Directory B2C

Neste artigo, você aprenderá a configurar o [tempo de vida e a compatibilidade de um token](active-directory-b2c-reference-tokens.md) no Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Pré-requisitos

[Criar um fluxo de usuário](tutorial-create-user-flows.md) para permitir que os usuários se registrem e entrem no seu aplicativo.

## <a name="configure-token-lifetime"></a>Configurar o tempo de vida do token

Você pode configurar o tempo de vida de token em qualquer fluxo de usuário.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Verifique se que você estiver usando o diretório que contém o seu locatário do Azure AD B2C. Selecione o **filtro de diretório e assinatura** no menu superior e escolha o diretório que contém o seu locatário do Azure AD B2C.
3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
4. Selecione **fluxos de usuário (diretivas)**.
5. Abra o fluxo de usuário que você criou anteriormente. 
6. Selecione **Propriedades**.
7. Sob **tempo de vida de Token**, ajustar as propriedades a seguir para as necessidades do seu aplicativo:

    ![Configurar o tempo de vida do token](./media/configure-tokens/token-lifetime.png)

8. Clique em **Salvar**.

## <a name="configure-token-compatibility"></a>Configurar a compatibilidade de token

1. Selecione **fluxos de usuário (diretivas)**.
2. Abra o fluxo de usuário que você criou anteriormente. 
3. Selecione **Propriedades**.
4. Sob **configurações de compatibilidade de Token**, ajustar as propriedades a seguir para as necessidades do seu aplicativo:

    ![Configurar a compatibilidade de token](./media/configure-tokens/token-compatibility.png)

5. Clique em **Salvar**.

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre como [usar tokens de acesso](active-directory-b2c-access-tokens.md).



