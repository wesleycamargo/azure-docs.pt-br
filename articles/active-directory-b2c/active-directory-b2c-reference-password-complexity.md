---
title: Complexidade de senha - Azure Active Directory B2C | Microsoft Docs
description: Como configurar os requisitos de complexidade de senhas fornecidas pelos consumidores no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 1d7874d7c8d04e3d3565cdfe2e52e49c538b3091
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2019
ms.locfileid: "59009794"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>Configurar os requisitos de complexidade de senhas fornecidas no Azure Active Directory B2C

O Azure Active Directory (Azure AD) B2C oferece suporte à alteração de requisitos de complexidade para senhas fornecidas por um usuário final ao criar uma conta. Por padrão, o Azure AD B2C usa senhas `Strong`. O Azure AD B2C também oferece suporte a opções de configuração para controlar a complexidade de senhas que os clientes podem usar.

## <a name="password-rule-enforcement"></a>Imposição de regras de senha

Durante a inscrição ou redefinição de senha, um usuário final deve fornecer uma senha que atenda às regras de complexidade. Regras de complexidade de senha são impostas pelo fluxo de usuários. É possível ter um fluxo de usuário exigir um pin de quatro dígitos e o outro fluxo de usuário requer uma cadeia de caracteres de oito caracteres durante a inscrição. Por exemplo, você pode usar um fluxo de usuários com diferente complexidade de senha para adultos e crianças.

A complexidade de senha nunca é aplicada durante a inscrição. Nunca será solicitado que os usuários mudem sua senha ao entrar por ela não atender aos requisitos de complexidade atual.

A complexidade da senha pode ser configurada nos seguintes tipos de fluxos de usuário:

- Fluxo de usuário de inscrição ou entrada
- Fluxo de usuário de redefinição de senha

Se estiver usando políticas personalizadas, você poderá ([configurar a complexidade da senha em uma política personalizada](active-directory-b2c-reference-password-complexity-custom.md)).

## <a name="configure-password-complexity"></a>Configurar a complexidade de senha

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C clicando no **filtro Diretório e assinatura** no menu superior e escolhendo o diretório que contém seu locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
4. Escolha **Fluxos de usuário**.
2. Selecione um fluxo de usuário e clique em **Propriedades**.
3. Em **Complexidade da senha**, altere a complexidade da senha para esse fluxo de usuário para **Simples**, **Forte** ou **Personalizada**.

### <a name="comparison-chart"></a>Gráfico de comparação

| Complexidade | DESCRIÇÃO |
| --- | --- |
| Simples | Uma senha que tenha 8 a 64 caracteres. |
| Strong | Uma senha que tenha 8 a 64 caracteres. São necessários de 3 a 4 caracteres minúsculos, maiúsculos, números ou símbolos. |
| Personalizado | Essa opção fornece mais controle sobre as regras de complexidade de senha.  Ela permite configurar um tamanho personalizado.  E permite aceitar somente senhas numéricas (pins). |

## <a name="custom-options"></a>Opções personalizadas

### <a name="character-set"></a>Conjunto de caracteres

Permite que você aceite somente dígitos (pins) ou o conjunto completo de caracteres.

- **Somente números** permite somente dígitos (0-9) ao digitar uma senha.
- **Todos** permite qualquer letra, número ou símbolo.

### <a name="length"></a>Comprimento

Permite que você controle as exigências de comprimento da senha.

- **Comprimento mínimo** deve ser pelo menos 4.
- **Comprimento máximo** deve ser igual ou maior que o mínimo, mas não pode ultrapassar 64 caracteres.

### <a name="character-classes"></a>Classes de caracteres

Permite que você controle os diferentes tipos de caracteres usados na senha.

- **2 de 4: caracteres maiúsculos, minúsculos, números (0-9) ou símbolos** garante que a senha contenha, no mínimo, dois tipos de caracteres. Por exemplo, um número e um caractere minúsculo.
- **3 de 4: caracteres maiúsculos, minúsculos, números (0-9) ou símbolos** garante que a senha contenha, no mínimo, dois tipos de caracteres. Por exemplo, um número, um caractere minúsculo e um caractere maiúsculos.
- **4 de 4: caracteres maiúsculos, minúsculos, números (0-9) ou símbolos** garante que a senha contenha todos os quatro tipos de caracteres.

    > [!NOTE]
    > Exigir **4 de 4** pode resultar em frustração do usuário final. Alguns estudos mostraram que esse requisito não melhora a entropia de senha. Confira as [Diretrizes de senha NIST](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)
