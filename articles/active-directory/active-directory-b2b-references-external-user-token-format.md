---
title: Formato do token de usuário externo para a visualização da colaboração B2B do Active Directory do Azure | Microsoft Docs
description: A B2B do Active Directory do Azure dá suporte a relações entre empresas, permitindo que os parceiros de negócios acessem de maneira seletiva seus aplicativos corporativos
services: active-directory
documentationcenter: ''
author: viv-liu
manager: cliffdi
editor: ''
tags: ''

ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/09/2016
ms.author: viviali

---
# Visualização da colaboração B2B do AD do Azure: formato de token de usuário externo
As declarações de um token padrão do AD do Azure são descritas no artigo [Tipos de declaração e token com suporte](active-directory-token-and-claims.md) em azure.microsoft.com.

As declarações que são diferentes para um usuário externo autenticado da colaboração B2B são as seguintes:<br/>

* **OID:** a ID do objeto do locatário do recurso<br/>
* **TID:** a ID do locatário do recurso<br/>
* **Emissor**: o locatário do recurso<br/>
* **IDP**: o locatário inicial do usuário<br/>
* **AltSecId**: a ID de segurança alternativa, que é opaca para você<br/>

## Artigos relacionados
Procure nossos outros artigos sobre a colaboração B2B do AD do Azure:

* [O que é a colaboração B2B do AD do Azure?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Como funciona](active-directory-b2b-how-it-works.md)
* [Passo a passo detalhado](active-directory-b2b-detailed-walkthrough.md)
* [Referência do formato de arquivo CSV](active-directory-b2b-references-csv-file-format.md)
* [Alterações de atributo do objeto de usuário externo](active-directory-b2b-references-external-user-object-attribute-changes.md)
* [Limitações atuais da visualização](active-directory-b2b-current-preview-limitations.md)
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)

<!---HONumber=AcomDC_0511_2016-->