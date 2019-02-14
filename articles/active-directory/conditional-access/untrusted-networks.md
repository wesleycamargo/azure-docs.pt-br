---
title: Como exigir o MFA (autenticação multifator) para acesso em redes não confiáveis com o acesso condicional do Azure AD (Azure Active Directory) | Microsoft Docs
description: Saiba como configurar uma política de acesso condicional no Azure AD (Azure Active Directory) para tentativas de acesso de redes não confiáveis.
services: active-directory
keywords: acesso condicional para aplicativos, acesso condicional com o Azure AD, acesso seguro aos recursos da empresa, políticas de acesso condicional
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.subservice: conditional-access
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2018
ms.author: markvi
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4391264dfa0b85ef8139478c3bbbbd8b828d368
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56217302"
---
# <a name="how-to-require-mfa-for-access-from-untrusted-networks-with-conditional-access"></a>Como: Exigir o MFA para acesso em redes não confiáveis com o acesso condicional   

O Azure AD (Azure Active Directory) permite o logon único em dispositivos, aplicativos e serviços em qualquer lugar. Os usuários podem acessar seus aplicativos na nuvem não apenas na rede de sua organização, mas também em qualquer localização não confiável da Internet. Uma melhor prática comum para acesso em redes não confiáveis é exigir o MFA (autenticação multifator).

Este artigo fornece as informações necessárias para configurar uma política de acesso condicional que exige o MFA para acesso em redes não confiáveis. 

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você esteja familiarizado com: 

- Os [conceitos básicos](overview.md) de acesso condicional do Azure AD 
- As [melhores práticas](best-practices.md) para configurar políticas de acesso condicional no portal do Azure



## <a name="scenario-description"></a>Descrição do cenário

Para dominar o equilíbrio entre segurança e produtividade, pode ser suficiente que você apenas exija uma senha para entradas na rede de sua organização. No entanto, para o acesso em uma localização de rede não confiável, há um risco maior de que as entradas não sejam feitas por usuários legítimos. Para resolver esse problema, você pode bloquear o acesso em redes não confiáveis. Como alternativa, também é possível exigir MFA (autenticação multifator) para obter garantia adicional de que uma tentativa foi feita pelo proprietário legítimo da conta. 

Com o acesso condicional do Azure AD, você pode endereçar esse requisito com uma única política que concede acesso: 

- Para aplicativos na nuvem selecionados

- Para usuários e grupos selecionados  

- Exigir autenticação multifator 

- Quando o acesso é originado de: 

    - Um local que não é confiável


## <a name="implementation"></a>Implementação

O desafio desse cenário é converter o *acesso em uma localização de rede não confiável* em uma condição de acesso condicional. Em uma política de acesso condicional, você pode configurar a [condição de locais](location-condition.md) para abordar cenários relacionados a locais de rede. A condição de locais permite que você selecione locais nomeados, que são agrupamentos lógicos de intervalos de endereços IP, países e regiões.  

Normalmente, a organização possui um ou mais intervalos de endereços, por exemplo, 199.30.16.0 - 199.30.16.24.
É possível configurar um local nomeado por:

- Especificando este intervalo (199.30.16.0/24) 

- Atribuindo um nome descritivo, como **Rede Corporativa** 


Em vez de tentar definir quais são todos os locais confiáveis ou não confiáveis, é possível:

- Incluir qualquer localização 

    ![Acesso condicional](./media/untrusted-networks/02.png)

- Excluir todos os locais confiáveis 

    ![Acesso condicional](./media/untrusted-networks/01.png)



## <a name="policy-deployment"></a>Implantação de política

Com a abordagem descrita neste artigo, agora você pode configurar uma política de acesso condicional para locais não confiáveis. Para garantir que sua política funcione conforme o esperado, a melhor prática é testá-la antes de distribuí-la em produção. O ideal é usar um locatário de teste para verificar se a nova política funciona conforme o esperado. Para obter mais informações, confira [Como implantar uma nova política](best-practices.md#how-should-you-deploy-a-new-policy). 



## <a name="next-steps"></a>Próximas etapas

Se você quiser saber mais sobre acesso condicional, consulte [O que é acesso condicional no Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)
