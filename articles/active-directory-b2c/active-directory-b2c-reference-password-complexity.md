---
title: Complexidade de senha no Azure Active Directory B2C | Microsoft Docs
description: Como configurar os requisitos de complexidade de senhas fornecidas pelos consumidores no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e728fb037895908882bbe6dc7e8414457ccf273f
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54845547"
---
# <a name="azure-ad-b2c-configure-complexity-requirements-for-passwords"></a>Azure AD B2C: como configurar os requisitos de complexidade de senhas

> [!NOTE]
> **Esse recurso está em uma versão prévia.**

O Azure Active Directory B2C (Azure AD B2C) oferece suporte à alteração de requisitos de complexidade para senhas fornecidas por um usuário final ao criar uma conta.  Por padrão, o Azure AD B2C usa senhas `Strong`.  O Azure AD B2C também oferece suporte a opções de configuração para controlar a complexidade de senhas que os clientes podem usar.

## <a name="when-password-rules-are-enforced"></a>Quando as regras de senha são aplicadas

Durante a inscrição ou redefinição de senha, um usuário final deve fornecer uma senha que atenda às regras de complexidade.  Regras de complexidade de senha são impostas pelo fluxo de usuários.  É possível ter um fluxo de usuários que exija uma senha de quatro dígitos, e outro que solicite uma cadeia de oito caracteres durante a inscrição.  Por exemplo, você pode usar um fluxo de usuários com diferente complexidade de senha para adultos e crianças.

A complexidade de senha nunca é aplicada durante a inscrição.  Nunca será solicitado que os usuários mudem sua senha ao entrar por ela não atender aos requisitos de complexidade atual.

Esses são os tipos de fluxos de usuários nos quais a complexidade de senha pode ser configurada:

* Fluxo de usuário de inscrição ou entrada
* Fluxo de usuário de redefinição de senha
* Política personalizada ([Como configurar a complexidade de senha na política personalizada](active-directory-b2c-reference-password-complexity-custom.md))

## <a name="how-to-configure-password-complexity"></a>Como configurar a complexidade de senha

1. Abra **Fluxos dos Usuários**.
2. Selecione um fluxo de usuário e clique em **Propriedades**.
3. Em **Complexidade da senha**, altere a complexidade da senha para esse fluxo de usuário para **Simples**, **Forte** ou **Personalizada**.

### <a name="comparison-chart"></a>Gráfico de comparação

| Complexidade | DESCRIÇÃO |
| --- | --- |
| Simples | Uma senha que tenha 8 a 64 caracteres. |
| Strong | Uma senha que tenha 8 a 64 caracteres. São necessários de 3 a 4 caracteres minúsculos, maiúsculos, números ou símbolos. |
| Personalizado | Essa opção fornece mais controle sobre as regras de complexidade de senha.  Ela permite configurar um tamanho personalizado.  E permite aceitar somente senhas numéricas (pins). |

## <a name="options-available-under-custom"></a>Opções disponíveis de personalização

### <a name="character-set"></a>Conjunto de caracteres

Permite que você aceite somente dígitos (pins) ou o conjunto completo de caracteres.

* **Somente números** permite somente dígitos (0-9) ao digitar uma senha.
* **Todos** permite qualquer letra, número ou símbolo.

### <a name="length"></a>Comprimento

Permite que você controle as exigências de comprimento da senha.

* **Comprimento mínimo** deve ser pelo menos 4.
* **Comprimento máximo** deve ser igual ou maior que o mínimo, mas não pode ultrapassar 64 caracteres.

### <a name="character-classes"></a>Classes de caracteres

Permite que você controle os diferentes tipos de caracteres usados na senha.

* **2 de 4: caracteres maiúsculos, minúsculos, números (0-9) ou símbolos** garante que a senha contenha, no mínimo, dois tipos de caracteres. Por exemplo, um número e um caractere minúsculo.
* **3 de 4: caracteres maiúsculos, minúsculos, números (0-9) ou símbolos** garante que a senha contenha, no mínimo, dois tipos de caracteres. Por exemplo, um número, um caractere minúsculo e um caractere maiúsculos.
* **4 de 4: caracteres maiúsculos, minúsculos, números (0-9) ou símbolos** garante que a senha contenha todos os quatro tipos de caracteres.

    > [!NOTE]
    > Exigir **4 de 4** pode resultar em frustração do usuário final. Alguns estudos mostraram que esse requisito não melhora a entropia de senha. Confira as [Diretrizes de senha NIST](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)
