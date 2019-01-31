---
title: Solucionar problemas de criação de locatários no Azure Active Directory B2C | Microsoft Docs
description: Problemas e resoluções para criar um Azure Active Directory ou um locatário do Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: b8075e084481a9951c13896fa4db41613adc388f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55160348"
---
# <a name="troubleshoot-creating-an-azure-active-directory-or-azure-active-directory-b2c-tenant"></a>Problemas e resoluções para criar um Azure Active Directory ou um locatário do Azure Active Directory B2C 

## <a name="create-an-azure-ad-tenant"></a>Criar um locatário do Azure AD
Caso não consiga criar um locatário do Azure Active Directory (Azure AD) na primeira tentativa, tente novamente. Se o problema persistir, contate o Suporte do Azure.

## <a name="create-an-azure-ad-b2c-tenant"></a>Criar um locatário do Azure AD B2C
Se você encontrar problemas ao [criar um locatário do Azure Active Directory B2C (Azure AD B2C)](active-directory-b2c-get-started.md), tente as seguintes opções:

* Se o locatário do Azure AD B2C não aparecer na lista de locatários, tente criá-lo novamente.
* Se o locatário do Azure AD B2C aparecer na sua lista de locatários e você vir a seguinte mensagem de erro, exclua o locatário e crie-o novamente:

    "Não foi possível concluir a criação do locatário B2C 'contosob2c'. Acesse este [link](https://go.microsoft.com/fwlink/?LinkID=624192&clcid=0x409) para obter mais diretrizes".
* Há problemas conhecidos quando você exclui um locatário do Azure AD B2C existente e o recria com o mesmo nome de domínio. Quando você criar um novo locatário do Azure AD B2C, você deverá usar um nome de domínio diferente.
* Se essas resoluções não funcionarem, entre em contato com o Suporte do Azure. Para obter mais informações, consulte [Arquivar solicitações de suporte para o Azure AD B2C](active-directory-b2c-support.md).

