---
title: Como atribuir usuários a aplicativos | Microsoft Docs
description: Compreenda como os usuários são atribuídos a um aplicativo em seu locatário
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: be36bfeb0991dc4a4b7fce723cadb6b508c0d6a3
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56198714"
---
# <a name="how-to-assign-users-to-applications"></a>Como atribuir usuários a aplicativos

Este artigo o ajudará a compreender como usuários são atribuídos a um aplicativo em seu locatário.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Como os usuários são atribuídos a um aplicativo no Azure AD?

Para acessar um aplicativo, o usuário precisa primeiro ser atribuído a ele de alguma forma. A atribuição pode ser feita por um administrador, um representante de negócios ou, às vezes, o próprio usuário. Abaixo, descrevemos as formas como os usuários podem ser atribuídos a aplicativos:

1.  Um administrador [atribui um usuário](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) diretamente ao aplicativo

2.  Um administrador [atribui um grupo](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) do qual o usuário é membro ao aplicativo, incluindo:

    * Um grupo que foi sincronizado do local

    * Um grupo de segurança estático criado na nuvem

    * Um [grupo de segurança dinâmico](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal) criado na nuvem

    * Um grupo do Office 365 criado na nuvem

    * O grupo [Todos os Usuários](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-dedicated-groups)

3.  Um administrador habilita o [Acesso de aplicativo de autoatendimento](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) para permitir que um usuário adicione um aplicativo usando o recurso [Adicionar Aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) do **Painel de Acesso do Aplicativo** **sem aprovação de negócios**

4.  Um administrador habilita o [Acesso de aplicativo de autoatendimento](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) para permitir que um usuário adicione um aplicativo usando o recurso [Adicionar Aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) do **Painel de Acesso do Aplicativo**, mas apenas **com aprovação anterior de um conjunto selecionado de aprovadores de negócios**

5.  Um administrador habilita o [Gerenciamento de Grupo de Autoatendimento](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) para permitir que um usuário ingresse em um grupo a que um aplicativo está atribuído **sem aprovação de negócios**

6.  Um administrador habilita o [Gerenciamento de Grupo de Autoatendimento](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) para permitir que um usuário ingresse em um grupo a que um aplicativo está atribuído, mas apenas **com aprovação anterior de um conjunto selecionado de aprovadores de negócios**

7.  Um administrador atribui uma licença diretamente a um usuário para um aplicativo interno, como o [Microsoft Office 365](https://products.office.com/)

8.  Um administrador atribui uma licença a um grupo do qual o usuário é membro para um aplicativo interno, como o [Microsoft Office 365](https://products.office.com/)

9.  Um [administrador dá consentimento para um aplicativo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) ser usado por todos os usuários e, em seguida, um usuário entra no aplicativo

10. O próprio usuário [dá consentimento para um aplicativo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) entrando no aplicativo

## <a name="next-steps"></a>Próximas etapas
[Gerenciando aplicativos com o Azure Active Directory](what-is-application-management.md)
