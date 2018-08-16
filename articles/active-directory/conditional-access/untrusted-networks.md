---
title: Como - Configurar políticas de acesso condicional do Azure Active Directory para tentativas de acesso de redes não confiáveis | Microsoft Docs
description: Saiba como configurar uma política de acesso condicional no Azure AD (Azure Active Directory) para tentativas de acesso de redes não confiáveis.
services: active-directory
keywords: acesso condicional para aplicativos, acesso condicional com o Azure AD, acesso seguro aos recursos da empresa, políticas de acesso condicional
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.component: conditional-access
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 5ddde65b2a68e71d86af6ce3dcd2847736cf5823
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627178"
---
# <a name="how-to-configure-conditional-access-policies-for-access-attempts-from-untrusted-networks"></a>Como: Configurar políticas de acesso condicional para tentativas de acesso de redes não confiáveis   

Em um mundo que prioriza os dispositivos móveis e a nuvem, o Azure AD (Azure Active Directory) permite o logon único em dispositivos, aplicativos e serviços de qualquer lugar. Como resultado disso, seus usuários podem acessar os aplicativos na nuvem não apenas da rede da organização, mas também de qualquer local da Internet não confiável. Com o [acesso condicional do Azure AD (Azure Active Directory)](../active-directory-conditional-access-azure-portal.md), você pode controlar como os usuários autorizados podem acessar seus aplicativos na nuvem. Um requisito comum nesse contexto é controlar tentativas de acesso iniciadas a partir de redes não confiáveis. Este artigo fornece as informações necessárias para configurar uma política de acesso condicional que lida com esse requisito. 

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você esteja familiarizado com: 

- Os conceitos básicos de acesso condicional do Azure AD 
- Configurar políticas de acesso condicional no portal do Azure

Consulte:

- [O que é acesso condicional no Azure Active Directory](../active-directory-conditional-access-azure-portal.md) - para obter uma visão geral do acesso condicional 

- [Início rápido: Exigir MFA para aplicativos específicos com acesso condicional do Azure Active Directory](app-based-mfa.md) - para obter alguma experiência com a configuração de políticas de acesso condicional. 


## <a name="scenario-description"></a>Descrição do cenário

Para dominar o equilíbrio entre segurança e produtividade, pode ser suficiente que você apenas exija que o usuário seja autenticado usando uma senha. No entanto, quando uma tentativa de acesso é feita a partir de um local de rede não confiável, haverá um risco maior de que as entradas não sejam executadas por usuários legítimos. Para resolver esse problema, é possível bloquear tentativas de acesso de redes não confiáveis. Como alternativa, também é possível exigir MFA (autenticação multifator) para obter garantia adicional de que uma tentativa foi feita pelo proprietário legítimo da conta. 

Com o acesso condicional do Azure AD, você pode endereçar esse requisito com uma única política que concede acesso: 

- Para aplicativos na nuvem selecionados

- Para usuários e grupos selecionados  

- Exigir autenticação multifator 

- Quando uma tentativa de acesso for feita a partir de: 

    - Um local que não é confiável


## <a name="considerations"></a>Considerações

O desafio deste cenário é traduzir *quando uma tentativa de acesso for feita a partir de um local que não é confiável*  em uma condição de acesso condicional. Em uma política de acesso condicional, você pode configurar a [condição de locais](location-condition.md) para abordar cenários relacionados a locais de rede. A condição locais permitem que você selecione locais nomeados, que representam agrupamentos lógicos de intervalos de endereços IP, países e regiões.  

Normalmente, a organização possui um ou mais intervalos de endereços, por exemplo, 199.30.16.0 - 199.30.16.24.
É possível configurar um local nomeado por:

- Especificando este intervalo (199.30.16.0/24) 

- Atribuindo um nome descritivo, como **Rede Corporativa** 


Em vez de tentar definir quais são todos os locais confiáveis ou não confiáveis, é possível:

- Incluir 

    ![Acesso condicional](./media/untrusted-networks/02.png)

- Excluir todos os locais confiáveis 

    ![Acesso condicional](./media/untrusted-networks/01.png)



## <a name="implementation"></a>Implementação

Com a abordagem descrita neste artigo, agora você pode configurar uma política de acesso condicional para locais não confiáveis. É necessário sempre testar a política antes de implementá-la para garantir que ela funciona conforme o esperado. Idealmente, você deve fazer os testes iniciais em um locatário de teste, se possível. Para obter mais informações, consulte [Como você deve implantar uma nova política](best-practices.md#how-should-you-deploy-a-new-policy). 



## <a name="next-steps"></a>Próximas etapas

Se você quiser saber mais sobre acesso condicional, consulte [O que é acesso condicional no Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)