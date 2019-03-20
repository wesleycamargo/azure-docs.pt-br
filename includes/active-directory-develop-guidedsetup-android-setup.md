---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: dadobali
ms.custom: include file
ms.openlocfilehash: f55bdc774437d280db51fb69f060bea7dc579a26
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203674"
---
## <a name="set-up-your-project"></a>Configurar o seu projeto

Você deseja baixar este projeto do Android Studio de exemplo? [Baixe um projeto](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip) e vá para a [Etapa de configuração](#register-your-application) para configurar o exemplo de código antes de executá-lo.

### <a name="create-a-new-project"></a>Criar um novo projeto

1. Abra o Android Studio e, em seguida, selecione **Arquivo** > **Novo** > **Novo projeto**.
2. Nomeie o seu aplicativo e, em seguida, selecione **Avançar**.
3. Selecione **API 21 ou mais nova (Android 5.0)** e clique em **Avançar**.
4. Deixe **Atividade vazia** como está, selecione **Seguinte**e, em seguida, selecione **Concluir**.

### <a name="add-msal-to-your-project"></a>Adicionar MSAL ao seu projeto

1. No Android Studio, selecione **Gradle Scripts** > **build.gradle (Módulo: aplicativo)**.
2. Em **Dependências**, cole o seguinte código:

    ```gradle  
    compile ('com.microsoft.identity.client:msal:0.1.+') {
        exclude group: 'com.android.support', module: 'appcompat-v7'
    }
    compile 'com.android.volley:volley:1.0.0'
    ```

<!--start-collapse-->
### <a name="about-this-package"></a>Sobre este pacote

O pacote no código anterior instala a Biblioteca de Autenticação da Microsoft (MSAL), que trata todas as operações de token, incluindo aquisição, cache, atualização e exclusão. Os tokens são necessários para acessar as APIs protegidas pela plataforma de identidade da Microsoft.
<!--end-collapse-->

## <a name="create-the-apps-ui"></a>Criar a interface do usuário do aplicativo

1. Vá para **res** > **layout**e, em seguida, abra **activity_main.xml**.
2. Altere o layout de atividade de `android.support.constraint.ConstraintLayout` ou outro para `LinearLayout`.
3. Adicionar o propriedade `android:orientation="vertical"` ao nó `LinearLayout`.
4. Cole o seguinte código no nó `LinearLayout`, substituindo o conteúdo atual:

    ```xml
    <TextView
        android:text="Welcome, "
        android:textColor="#3f3f3f"
        android:textSize="50px"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginLeft="10dp"
        android:layout_marginTop="15dp"
        android:id="@+id/welcome"
        android:visibility="invisible"/>

    <Button
        android:id="@+id/callGraph"
        android:text="Call Microsoft Graph"
        android:textColor="#FFFFFF"
        android:background="#00a1f1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="200dp"
        android:textAllCaps="false" />

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dip"
        android:layout_weight="1"
        android:gravity="center|bottom"
        android:orientation="vertical" >

        <Button
            android:text="Sign Out"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="15dp"
            android:textColor="#FFFFFF"
            android:background="#00a1f1"
            android:textAllCaps="false"
            android:id="@+id/clearCache"
            android:visibility="invisible" />
    </LinearLayout>
    ```
