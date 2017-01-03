---
title: "Alterações de atributo do objeto de usuário externo para a visualização de colaboração do Azure Active Directory B2B | Microsoft Docs"
description: "A B2B do Active Directory do Azure dá suporte a relações entre empresas, permitindo que os parceiros de negócios acessem de maneira seletiva seus aplicativos corporativos"
services: active-directory
documentationcenter: 
author: viv-liu
manager: cliffdi
editor: 
tags: 
ms.assetid: 4ede9c7b-3830-42f2-b73d-cbecbcc7db64
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/09/2016
ms.author: viviali
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d57fd7d6dcd186d1e79528c716d8bf81ad868ae5


---
# <a name="azure-ad-b2b-collaboration-preview-external-user-object-attribute-changes"></a>Visualização da colaboração B2B do AD do Azure: alterações de atributo de objeto de usuário
Cada usuário em um diretório do AD do Azure é representado por um objeto de usuário. O objeto de usuário no AD do Azure passa por alterações de atributo em vários estágios do fluxo de convite-resgate da colaboração B2B. O objeto de usuário que representa o usuário do parceiro no diretório tem atributos que são alterados no momento do resgate, quando o usuário do parceiro clica no link no email de convite. Especificamente:

* Os atributos **SignInName** e **AltSecId** são preenchidos
* O atributo **DisplayName** muda do Nome Principal do Usuário (user_fabrikam.com#EXT#@contoso.com) para o nome de entrada (user@fabrikam.com)

O acompanhamento desses atributos no AD do Azure pode ajudar a solucionar problemas referentes a se um usuário do parceiro resgatou ou não seu convite da colaboração B2B.

## <a name="related-articles"></a>Artigos relacionados
Procure nossos outros artigos sobre a colaboração B2B do AD do Azure:

* [O que é a colaboração B2B do AD do Azure?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Como funciona](active-directory-b2b-how-it-works.md)
* [Passo a passo detalhado](active-directory-b2b-detailed-walkthrough.md)
* [Referência do formato de arquivo CSV](active-directory-b2b-references-csv-file-format.md)
* [Formato do token de usuário externo](active-directory-b2b-references-external-user-token-format.md)
* [Limitações atuais da visualização](active-directory-b2b-current-preview-limitations.md)
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)




<!--HONumber=Dec16_HO5-->


