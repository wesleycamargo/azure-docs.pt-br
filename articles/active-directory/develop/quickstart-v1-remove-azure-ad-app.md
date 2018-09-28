---
title: Remover um aplicativo do Azure Active Directory
description: Saiba como remover um aplicativo no Azure AD (Azure Active Directory).
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.openlocfilehash: 2cd77ee6df52940a800733209b7e384d72d98103
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46962123"
---
# <a name="quickstart-remove-an-application-from-azure-active-directory"></a>Início Rápido: remover um aplicativo do Azure Active Directory

Os desenvolvedores corporativos e provedores de SaaS (software como serviço) que registraram aplicativos com o Azure AD (Azure Active Directory) podem precisar remover um registro de aplicativo do seu locatário do Azure AD.

Neste início rápido, você aprenderá a:

* [Remover um aplicativo criado pela sua organização](#removing-an-application-authored-by-your-organization)
* [Remover um aplicativo multilocatário autorizado por outra organização](#removing-a-multi-tenant-application-authorized-by-another-organization)

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará de um locatário do Azure AD que tenha aplicativos registrados.

* Se você ainda não tiver um locatário [saiba como obter um](quickstart-create-new-tenant.md).
* Para saber como adicionar e registrar aplicativos em seu locatário, consulte [Adicionar um aplicativo ao Azure AD](quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="removing-an-application-authored-by-your-organization"></a>Removendo um aplicativo autorizado pela sua organização

Aplicativos que sua organização tiver registrado aparecem sob o filtro **Meus aplicativos** na página do principal **Registros de aplicativo** do locatário. Esses aplicativos são aqueles que você registrou manualmente pelo portal do Azure ou programaticamente pelo PowerShell ou pela API do Microsoft Graph. Mais especificamente, esses aplicativos são representados por um objeto de aplicativo e entidade de serviço em seu locatário. Para saber mais sobre esses objetos, confira [Objetos de aplicativo e de entidade de serviço](app-objects-and-service-principals.md).

### <a name="to-remove-a-single-tenant-application-from-your-directory"></a>Para remover um aplicativo de locatário único do diretório

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Se a sua conta der acesso a mais de um, selecione sua conta no canto superior direito e defina a sessão do portal para o locatário do Azure AD desejado.
1. No painel de navegação esquerdo, clique no serviço **Azure Active Directory**, selecione **Registros de aplicativo**, então localize e selecione o aplicativo que você deseja configurar.
    Você será direcionado à página de registro principal do aplicativo, que abre a página **Configurações** para o aplicativo.
1. Na página de registro principal do aplicativo, selecione **Excluir**.
1. Selecione **Sim** para confirmar que você deseja excluir o aplicativo.

### <a name="to-remove-a-multi-tenant-application-from-its-home-directory"></a>Para remover um aplicativo multilocatário do diretório inicial

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Se a sua conta der acesso a mais de um, selecione sua conta no canto superior direito e defina a sessão do portal para o locatário do Azure AD desejado.
1. No painel de navegação esquerdo, clique no serviço **Azure Active Directory**, selecione **Registros de aplicativo**, então localize e selecione o aplicativo que você deseja configurar.
    Você será direcionado à página de registro principal do aplicativo, que abre a página **Configurações** para o aplicativo.
1. Na página **Configurações**, escolha **Propriedades** e altere a opção **Multilocatário** para **Não** para primeiro alterar seu aplicativo para locatário único e, em seguida, selecione **Salvar**.
    Os objetos da entidade de serviço do aplicativo permanecem em locatários de todas as organizações que já tiverem consentido com isso.
1. Selecione **Excluir** na página de registro principal do aplicativo.
1. Selecione **Sim** para confirmar que você deseja excluir o aplicativo.

## <a name="removing-a-multi-tenant-application-authorized-by-another-organization"></a>Removendo um aplicativo multilocatário autorizado por outra organização

Um subconjunto dos aplicativos que aparecem sob o filtro **Todos os aplicativos** (excluindo os registros de **Meus aplicativos**), na página principal de **Registros de aplicativo** do seu locatário, é composto por aplicativos multilocatário.

Em termos de técnicos, esses aplicativos multilocatários são de outro locatário e foram registrados no seu locatário durante o processo de consentimento. Mais especificamente, eles são representados apenas por um objeto de entidade de serviço em seu locatário, sem objeto de aplicativo correspondente. Para saber mais sobre as diferenças entre os objetos de aplicativo e entidade de serviço, confira [Objetos de aplicativo e entidade de serviço no Azure AD](app-objects-and-service-principals.md).

Para remover o acesso de um aplicativo multilocatário ao seu diretório (depois de ter dado consentimento), o administrador da empresa deve remover a entidade de serviço do aplicativo. O administrador deve ter acesso de administrador global e pode removê-lo por meio do portal do Azure ou usar os Cmdlets [ do Azure AD PowerShell ](http://go.microsoft.com/fwlink/?LinkId=294151).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre esses outros inícios rápidos de gerenciamento de aplicativos relacionados para aplicativos que usam o ponto de extremidade do Azure AD v1.0:

- [Adicionar um aplicativo ao Azure AD](quickstart-v1-integrate-apps-with-azure-ad.md)
- [Atualizar um aplicativo no Azure AD](quickstart-v1-update-azure-ad-app.md)
