---
title: Limitações atuais da visualização para a colaboração B2B do Active Directory do Azure | Microsoft Docs
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
ms.workload: identity
ms.date: 05/09/2016
ms.author: viviali

---
# Visualização da colaboração do B2B do AD do Azure: limitações da visualização atual
* O MFA (autenticação multifator) não tem suporte em usuários externos. Por exemplo, se a Contoso tiver o MFA, mas a Organização do Parceiro não, os usuários da Organização do Parceiro não podem ter permissão ao MFA por meio da colaboração B2B.
* Convites são possíveis apenas por meio de CSV; não há suporte ao acesso de API e a convites individuais.
* Somente Administradores Globais do AD do Azure podem carregar arquivos .csv.
* Atualmente, não há suporte a convites para endereços de email do consumidor (como hotmail.com, Gmail.com ou comcast.net).
* O acesso de usuário externo aos aplicativos locais não é testado.
* Os usuários externos não são automaticamente limpos quando o usuário real é excluído de seu diretório.
* Não há suporte para convites para listas de distribuição.
* Um máximo de 2.000 registros pode ser carregado por meio do CSV.

## Artigos relacionados
Procure nossos outros artigos sobre a colaboração B2B do AD do Azure:

* [O que é a colaboração B2B do AD do Azure?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Como funciona](active-directory-b2b-how-it-works.md)
* [Passo a passo detalhado](active-directory-b2b-detailed-walkthrough.md)
* [Referência do formato de arquivo CSV](active-directory-b2b-references-csv-file-format.md)
* [Formato do token de usuário externo](active-directory-b2b-references-external-user-token-format.md)
* [Alterações de atributo do objeto de usuário externo](active-directory-b2b-references-external-user-object-attribute-changes.md)
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)

<!---HONumber=AcomDC_0706_2016-->