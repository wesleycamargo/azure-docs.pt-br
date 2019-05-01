---
title: Configurar tokens - Azure Active Directory B2C | Microsoft Docs
description: Saiba como configurar as configurações de compatibilidade e o tempo de vida do token no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 69a6284ea7b8905bb5efdb1f4c9f26027bd6f9d9
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64689603"
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

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como [usar tokens de acesso](active-directory-b2c-access-tokens.md).



