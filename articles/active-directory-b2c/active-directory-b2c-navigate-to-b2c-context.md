---
title: "Azure Active Directory B2C: Alternar para um locatário B2C | Microsoft Docs"
description: "Como alternar para o contexto do seu locatário do Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: mtillman
editor: parakhj
ms.assetid: 0eb1b198-44d3-4065-9fae-16591a8d3eae
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 4/13/2017
ms.author: parakhj
ms.openlocfilehash: a9b9f71d0c11aee238661d57fd2e5246f1bf0842
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="switching-to-your-azure-ad-b2c-tenant"></a>Alternar para o locatário do Azure AD B2C

Para configurar o Azure AD B2C, você precisa estar no contexto do seu locatário Azure AD B2C.

## <a name="log-into-azure-ad-b2c-tenant"></a>Faça logon no locatário do Azure AD B2C

Para navegar até seu locatário do Azure AD B2C, você deve estar conectado ao portal do Azure como um administrador global do locatário do Azure AD B2C.

1. Faça logon no [Portal do Azure](http://portal.azure.com).
1. Alternar locatários clicando em seu endereço de email ou na imagem no canto superior direito.
1. Na lista `Directory` que aparece, selecione o locatário do Azure AD B2C que você deseja gerenciar.

O portal do Azure é atualizado.  Agora você está conectado ao portal do Azure no contexto do locatário do Azure AD B2C.

## <a name="navigate-to-the-b2c-features-pane"></a>Navegue até o painel de recursos do B2C

1. Clique em **Procurar** na barra de navegação à esquerda.
1. Clique em **Todos os serviços** e, em seguida, pesquise por `Azure AD B2C` no painel de navegação à esquerda.  (Para fixar ao seu quadro inicial à esquerda, clique na estrela à esquerda do Azure AD B2C)
1. Clique em **Azure AD B2C** para acessar o painel de recursos do B2C.
   
    ![Captura de tela de Procurar para o painel de recursos do B2C](./media/active-directory-b2c-get-started/b2c-browse.png)

> [!IMPORTANT]
> Você precisa ser um Administrador Global do locatário do B2C para poder acessar o painel de recursos do B2C. Um Administrador Global de qualquer outro locatário ou um Usuário de qualquer outro locatário não pode acessá-la.  Você pode alternar para o locatário B2C usando o seletor de locatário no canto superior direito do portal do Azure.
