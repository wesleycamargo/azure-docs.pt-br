---
title: Redefinição de senha de autoatendimento no Azure Active Directory B2C | Microsoft Docs
description: Demonstra como configurar a redefinição de senha de autoatendimento para seus clientes no Azure Active Directory B2C
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 3612e10df12e2b18f32caae55bdd83b12a4e24a6
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448716"
---
# <a name="set-up-self-service-password-reset-for-your-customers"></a>Configure a redefinição de senha de autoatendimento para seus clientes
Com o recurso de redefinição de senha de autoatendimento, seus clientes que tenham se registrado com contas locais poderão redefinir as respectivas senhas por conta própria. Isso reduz significativamente a sobrecarga da equipe de suporte, principalmente se milhões de clientes usarem seu aplicativo regularmente. Atualmente, usar um endereço de email verificado é o único método de recuperação com suporte.

> [!NOTE]
> Este artigo se aplica ao autoatendimento de redefinição de senha usado no contexto de uma política de entrada. Se precisar de políticas de redefinição de senha totalmente personalizáveis invocadas por meio de seu aplicativo, veja [este artigo](active-directory-b2c-reference-policies.md#create-a-password-reset-policy).
> 
> 

Por padrão, o diretório não tem a redefinição de senha de autoatendimento ativada. Use as etapas a seguir para ativá-la:

1. Entre no [portal do Azure](https://portal.azure.com/) como o Administrador da Assinatura. Essa é a mesma conta corporativa, de estudante ou da Microsoft que você usou para criar o diretório.
2. Abra o **Azure Active Directory** (na barra de navegação à esquerda).
4. Configure a **Redefinição de senha de autoatendimento habilitada** como **Tudo**. 
5. Clique em **Salvar** na parte superior da página. Pronto!

Para testar, use o recurso "Executar agora" em qualquer política de entrada que tenha contas locais como um provedor de identidade. Na página de entrada da conta local (na qual você insere um endereço de email e a senha ou um nome de usuário e a senha), clique em **Não consegue acessar sua conta?** para verificar a experiência do cliente.

> [!NOTE]
> As páginas do autoatendimento de redefinição de senha podem ser personalizadas usando o [recurso de identidade visual da empresa](../active-directory/fundamentals/customize-branding.md).
> 
> 

