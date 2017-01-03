---
title: "Azure Active Directory: criar tópico de suporte ao locatário | Microsoft Docs"
description: "Criando um locatário do Azure Active Directory ou um locatário do Azure Active Directory B2C: problemas e resoluções"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: msmbaldwin
editor: bryanla
ms.assetid: 7ba4c6b2-161b-45b5-b3bd-ccb662f5d7a0
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: 4b13c040a15bef2f04d2cd2126e2270d061898bd
ms.openlocfilehash: 42c00632fee16b61f502b5a8ac15825788403ab8


---
# <a name="creating-an-azure-active-directory-azure-ad-tenant-or-azure-ad-b2c-tenant-issues-and-resolutions"></a>Criando um locatário do Azure AD (Azure Active Directory) ou um locatário do Azure AD B2C: problemas e resoluções
## <a name="creating-an-azure-ad-tenant"></a>Criando um locatário do Azure AD
Se você não conseguir criar um locatário do AD do Azure na primeira vez, tente novamente. Se o problema persistir, contate o Suporte.

## <a name="creating-an-azure-ad-b2c-tenant"></a>Criando um Locatário do Azure AD B2C
Se encontrar problemas durante a [criação de um locatário do AD B2C do Azure](active-directory-b2c-get-started.md), tente o seguinte:

* Se o locatário do AD B2C do Azure não aparecer na lista de locatários, tente novamente.
* Se o locatário do Azure AD B2C aparecer na lista de locatários, mas você receber uma mensagem de erro ("Não foi possível concluir a criação do locatário B2C 'contosob2c'. Visite este [link](http://go.microsoft.com/fwlink/?LinkID=624192&clcid=0x409) para obter mais diretrizes."), exclua o locatário que acabou de criar e tente novamente.
* Observe que há problemas conhecidos quando você exclui um locatário B2C existente e o recria com o mesmo nome de domínio. Você precisa criar um locatário B2C com um nome de domínio diferente.
* Se essas resoluções não funcionarem, entre em contato com o Suporte. Saiba mais sobre [como arquivar solicitações de suporte para o Azure AD B2C](active-directory-b2c-support.md).




<!--HONumber=Dec16_HO5-->


