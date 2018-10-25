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
ms.openlocfilehash: 7ff04789a4ba5e5a689b3d3815852bc0fbcdc6a7
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988306"
---
## <a name="register-your-application"></a>Registre seu aplicativo

Você pode registrar seu aplicativo de duas maneiras, conforme descrito nas duas seções a seguir.

### <a name="option-1-express"></a>Opção 1: Expresso

1. Vá até o [Portal de Registro de Aplicativos da Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=android&step=configure).
2. Em **Nome do Aplicativo**, insira um nome para o aplicativo.
3. Marque a caixa de seleção **Instalação Interativa** e, depois, selecione **Criar**.
4. Siga as instruções para obter a ID do aplicativo e cole-a em seu código.

### <a name="option-2-advanced"></a>Opção 2: Avançado

1. Vá até o [Portal de Registro de Aplicativos da Microsoft](https://apps.dev.microsoft.com/portal/register-app).
2. No campo **Nome do Aplicativo**, insira um nome para o seu aplicativo.
3. Desmarque a caixa de seleção **Instalação Interativa** e, depois, selecione **Criar**.
4. Selecione **Adicionar Plataforma**, selecione **Aplicativo Nativo** e, em seguida, selecione **Salvar**.
5. Em **aplicativo** > **java** > **{host}.{namespace}**, abra `MainActivity`. 
6. Substitua *[Inserir a ID do aplicativo aqui]* pela ID do Cliente/Aplicativo:

    ```java
    final static String CLIENT_ID = "[Enter the application Id here]";
    ```
<!-- Workaround for Docs conversion bug -->
7. Em **aplicativo** > **manifestos**, abra o arquivo *AndroidManifest.xml*.
8. Em `manifest\application`, adicione a atividade a seguir. A atividade `BrowserTabActivity` permite que a Microsoft faça uma chamada de retorno ao aplicativo após concluir a autenticação:

    ```xml
    <!--Intent filter to capture System Browser calling back to our app after sign-in-->
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
<!-- Workaround for Docs conversion bug -->
9. Em `BrowserTabActivity`, substitua `[Enter the application Id here]` pela ID do Cliente/Aplicativo.
