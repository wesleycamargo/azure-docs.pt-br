---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: fe2b02b2495b4f37cbc90e1ddbeaca43b41d008c
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48843027"
---
## <a name="add-the-applications-registration-to-your-code"></a>Adicionar o registro do aplicativo ao código

Nesta etapa, você precisa adicionar a ID do Cliente/Aplicativo ao projeto.

1.  Abra `MainActivity` (em `app` > `java` > *`{host}.{namespace}`*)
2.  Substitua a linha que começa com `final static String CLIENT_ID` por:
```java
final static String CLIENT_ID = "[Enter the application Id here]";
```
3. Abra: `app` > `manifests` > `AndroidManifest.xml`
4. Adicione a seguinte atividade para `manifest\application`. A atividade`BrowserTabActivity` permite que a Microsoft faça um retorno de chamada ao aplicativo após concluir a autenticação:

```xml
<!--Intent filter to capture System Browser calling back to our app after Sign In-->
<activity
    android:name="com.microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />

        <!--Add in your scheme/host from registered redirect URI-->
        <!--By default, the scheme should be similar to 'msal[appId]' -->
        <data android:scheme="msal[Enter the application Id here]"
            android:host="auth" />
    </intent-filter>
</activity>
```

