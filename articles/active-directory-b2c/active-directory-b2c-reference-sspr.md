---
title: 'Azure Active Directory B2C: autoatendimento de redefinição de senha | Microsoft Docs'
description: Um tópico que demonstra como configurar a redefinição de senha por autoatendimento para seus consumidores no Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 12/06/2016
ms.author: davidmu
ms.openlocfilehash: f38473989f90bfe6d35bffb17a02a892ad08cf5e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-set-up-self-service-password-reset-for-your-consumers"></a>Azure Active Directory B2C: configurar a redefinição de senha por autoatendimento para seus consumidores
Com o recurso de redefinição de senha por autoatendimento, seus consumidores (que tenham se registrado com contas locais) poderão redefinir as respectivas senhas por conta própria. Isso reduz significativamente a sobrecarga da equipe de suporte, principalmente se milhões de consumidores usarem seu aplicativo regularmente. Atualmente, só há suporte para usar um endereço de email verificado como um método de recuperação. Vamos incluir mais métodos de recuperação (número de telefone verificado, perguntas de segurança, etc.) futuramente.

> [!NOTE]
> Este artigo se aplica ao autoatendimento de redefinição de senha usado no contexto de uma política de entrada. Se precisar de políticas de redefinição de senha totalmente personalizáveis invocadas por meio de seu aplicativo, veja [este artigo](active-directory-b2c-reference-policies.md#create-a-password-reset-policy).
> 
> 

Por padrão, o diretório não terá a redefinição de senha por autoatendimento ativada. Use as etapas a seguir para ativá-la:

1. Entre no [portal do Azure](https://portal.azure.com/) como o Administrador da Assinatura. Essa é a mesma conta corporativa, de estudante ou da Microsoft que você usou para criar o diretório.
2. Abra o Active Directory (na barra de navegação à esquerda).
3. Selecione **Propriedades**.
4. Role para baixo até a seção **redefinição de senha por autoatendimento habilitada** e ative para **Todos**. 
5. Clique em **Salvar** na parte superior da página. Pronto!

Para testar, use o recurso "Executar agora" em qualquer política de entrada que tenha contas locais como um provedor de identidade. Na página de entrada da conta local (na qual você insere um endereço de email e a senha ou um nome de usuário e a senha), clique em **Não consegue acessar sua conta?** para verificar a experiência do consumidor.

> [!NOTE]
> As páginas do autoatendimento de redefinição de senha podem ser personalizadas usando o [recurso de identidade visual da empresa](../active-directory/customize-branding.md).
> 
> 

