---
title: Como obter um locatário do Azure AD| Microsoft Docs
description: Como obter um locatário do Active Directory do Azure para registrar e criar aplicativos.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 1f4b24eb-ab4d-4baa-a717-2a0e5b8d27cd
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/23/2018
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: 824d7c44488e382aef9fd0640e6c46e9f4672898
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/22/2018
ms.locfileid: "36317496"
---
# <a name="how-to-get-an-azure-active-directory-tenant"></a>Como obter um locatário do Active Directory do Azure

No AD do Azure, um [locatário](https://msdn.microsoft.com/library/azure/jj573650.aspx#Anchor_0) é representativo de uma organização. Trata-se de uma instância dedicada do serviço do Azure Active Directory que uma organização recebe e detém como sua propriedade ao criar um relacionamento com a Microsoft, como ao se inscrever em um serviço de nuvem da Microsoft, como o Azure, o Microsoft Intune ou o Office 365. Cada locatário do AD do Azure é distinto e separado de outros diretórios do AD do Azure. 

Um locatário acomoda os usuários em uma empresa e as informações sobre eles - suas senhas, dados de perfil do usuário, permissões e assim por diante. Ele também contém grupos, aplicativos e outras informações referentes a uma organização e à sua segurança.

Para permitir que os usuários do AD do Azure entrem no aplicativo, você deve registrá-lo em um locatário de sua propriedade. A criação de um locatário do Azure Active Directory e a publicação de um aplicativo nele são **totalmente gratuitas** (embora você possa optar por pagar pelos recursos premium em seu locatário). Na verdade, muitos desenvolvedores criam vários locatários e aplicativos para fins de experimentação, teste, desenvolvimento e preparo.

## <a name="use-an-existing-azure-ad-tenant"></a>Usar um locatário existente do Azure Active Directory

Muitos desenvolvedores já contam com locatários por meio de serviços ou assinaturas que estão vinculadas a locatários do Azure Active Directory como assinaturas do Office 365 ou do Azure. Para verificar se você já tem um locatário, entre no [Portal do Azure](https://portal.azure.com) com a conta que deseja usar para gerenciar o aplicativo e verifique o canto superior direito, no qual são exibidas as informações da conta. Se tiver um locatário, você será conectado a ele automaticamente e verá o nome do locatário diretamente abaixo do nome da conta. Se você passar o mouse sobre o nome da conta no canto superior direito do portal do Azure, você verá seu nome, email, diretório e ID de locatário (um GUID) e seu domínio. Se sua conta estiver associada a vários locatários, você pode selecionar o nome da sua conta para abrir um menu no qual você pode alternar entre locatários. Cada locatário tem sua própria ID exclusiva.

> [!TIP]
> Se você precisar localizar a ID de locatário, há várias maneiras de localizar essas informações. Você pode passar o mouse sobre o nome da conta para obter a ID de locatário ou você pode selecionar **Microsoft Azure Active Directory > Propriedades > ID de Diretório** no portal do Azure.

Se não tiver um locatário existente associado à sua conta, você verá um GUID abaixo do nome da conta e não poderá executar ações como registrar aplicativos até [criar um novo locatário](#create-a-new-azure-ad-tenant).

## <a name="create-a-new-azure-ad-tenant"></a>Criar um novo locatário do Azure Active Directory

Se ainda não tiver um locatário do Azure Active Directory ou deseje criar um novo, é possível fazer isso usando a [experiência de criação de diretório](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) no [Portal do Azure](https://portal.azure.com). O processo leva cerca de um minuto e, no final, você será solicitado a navegar até o seu locatário recém-criado.
