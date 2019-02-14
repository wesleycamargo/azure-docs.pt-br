---
title: Adicionar um aplicativo cliente nativo – Azure Active Directory B2C | Microsoft Docs
description: Saiba como adicionar um aplicativo cliente nativo ao seu locatário do Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: ee3323ec23b37318dbd80c85d6dd7515ce1ce06b
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55757288"
---
# <a name="add-a-native-client-application-to-your-azure-active-directory-b2c-tenant"></a>Adicionar um aplicativo cliente nativo ao seu locatário do Azure Active Directory B2C

Os recursos de cliente nativos precisam estar registrados em seu locatário antes de o aplicativo poder se comunicar com o Azure Active Directory B2C.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C clicando no **filtro Diretório e assinatura** no menu superior e escolhendo o diretório que contém seu locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Selecione **Aplicativos** e, em seguida, selecione **Adicionar**.
2. Insira um nome para o aplicativo. Por exemplo, *nativeapp1*.
3. Para **incluir o aplicativo web / API web**, selecione **não**.
4. Para **incluir cliente nativo**, selecione **Sim**.
5. Para **URI de redirecionamento**, insira um URI de redirecionamento válido com um esquema personalizado. Há duas considerações importantes ao escolher um URI de redirecionamento:

    - **Exclusivo** – o esquema do URI de redirecionamento deve ser exclusivo para cada aplicativo. No exemplo `com.onmicrosoft.contoso.appname://redirect/path`, `com.onmicrosoft.contoso.appname` é o esquema. Esse padrão deve ser seguido. Se dois aplicativos compartilharem o mesmo esquema, o usuário terá a opção de escolher um aplicativo. Se o usuário fizer uma escolha incorreta, ocorrerá falha na conexão.
    - **Completo** – o URI de redirecionamento deve ter um esquema e um caminho. O caminho deve conter pelo menos uma barra após o domínio. Por exemplo, `//contoso/` funciona e `//contoso` falha. Certifique-se de que o URI de redirecionamento não inclua caracteres especiais, como sublinhados.

6. Clique em **Criar**.
7. Na página de propriedades, registre a ID do aplicativo que você usará ao configurar o aplicativo cliente nativo.
