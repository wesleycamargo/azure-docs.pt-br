---
title: Notificações por push para aplicativos Kindle usando Hubs de Notificação do Azure | Microsoft Docs
description: Neste tutorial, você aprende a usar os Hubs de Notificação do Azure para enviar notificações por push a um aplicativo Kindle.
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 346fc8e5-294b-4e4f-9f27-7a82d9626e93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-kindle
ms.devlang: Java
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 7dc969fdcf8b819f5223bdbff3cc1b9d4439c370
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2019
ms.locfileid: "57729126"
---
# <a name="get-started-with-notification-hubs-for-kindle-apps"></a>Introdução aos Hubs de Notificação para aplicativos do Kindle

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Este tutorial mostra como usar os Hubs de Notificação do Azure para enviar notificações por push para um aplicativo Kindle. Você deve criar um aplicativo Kindle em branco que recebe notificações por push usando Amazon Device Messaging (ADM).

Neste tutorial, você cria/atualiza o código para executar as seguintes tarefas:

> [!div class="checklist"]
> * Adicionar um novo aplicativo ao portal do desenvolvedor
> * Criar uma chave para a API
> * Adicionar credenciais ao hub
> * Configurar o aplicativo
> * Crie o manipulador de mensagens do ADM:
> * Adicione a chave de API ao seu aplicativo
> * Execute o aplicativo
> * Enviar uma notificação de teste

## <a name="prerequisites"></a>Pré-requisitos

* Obtenha o SDK do Android (supondo que você esteja usando o Eclipse) no [portal do Desenvolvedor do Android](https://go.microsoft.com/fwlink/?LinkId=389797).
* Siga as etapas em [Configurar seu ambiente de desenvolvimento](https://developer.amazon.com/docs/fire-tablets/ft-set-up-your-development-environment.html) para configurar o ambiente de desenvolvimento para Kindle.

## <a name="add-a-new-app-to-the-developer-portal"></a>Adicionar um novo aplicativo ao portal do desenvolvedor

1. Primeiro, crie um aplicativo no [portal do desenvolvedor da Amazon].

    ![][0]

2. Copie a **Chave do Aplicativo**.

    ![][1]

3. No portal, clique no nome do aplicativo e, em seguida, clique na guia **Mensagens de Dispositivos** .

    ![][2]

4. Clique em **Criar um Novo Perfil de Segurança** e crie um novo perfil de segurança (por exemplo, **Perfil de segurança TestAdm**). Em seguida, clique em **Salvar**.

    ![][3]

5. Clique em **Perfis de Segurança** para exibir o perfil de segurança que você criou. Copie os valores de **ID do cliente** e **Segredo do Cliente** para uso posterior.

    ![][4]

## <a name="create-an-api-key"></a>Criar uma chave para a API

1. Abra uma prompt de comando com privilégios de administrador.
2. Navegue até a pasta Android SDK.
3. Digite o seguinte comando:

    ```shell
    keytool -list -v -alias androiddebugkey -keystore ./debug.keystore
    ```

    ![][5]

4. Para a senha do **keystore**, digite **android**.
5. Copie a impressão digital **MD5** .
6. De volta no portal do desenvolvedor, na guia **Mensagens**, clique em **Android/Kindle** e digite o nome do pacote para seu aplicativo (por exemplo, **com.sample.notificationhubtest**), o valor de **MD5** e clique em **Gerar a Chave de API**.

## <a name="add-credentials-to-the-hub"></a>Adicionar credenciais ao hub

No portal, adicione o segredo do cliente e a ID do cliente à guia **Configurar** de seu hub de notificação.

## <a name="set-up-your-application"></a>Configurar o aplicativo

> [!NOTE]
> Ao criar um aplicativo, use pelo menos API de nível 17.

Adicione as bibliotecas do ADM ao seu projeto Eclipse.

1. Para obter a biblioteca do ADM, [baixar o SDK]. Extraia o arquivo zip do SDK.
2. No Eclipse, clique com o botão direito do mouse em seu projeto e clique em **Propriedades**. Selecione o **Caminho de Build de Java** à esquerda e selecione a guia **Bibliotecas** na parte superior. Clique em **Adicionar Jar Externo** e selecione o arquivo `\SDK\Android\DeviceMessaging\lib\amazon-device-messaging-*.jar` do diretório no qual você extraiu o SDK da Amazon.
3. Baixe o SDK do Android NotificationHubs (link).
4. Descompacte o pacote e, em seguida, arraste o arquivo `notification-hubs-sdk.jar` na pasta `libs`no Eclipse.

Edite seu manifesto de aplicativo para oferecer suporte ao ADM:

1. Adicione o namespace Amazon no elemento raiz do manifesto:

    ```xml
    xmlns:amazon="http://schemas.amazon.com/apk/res/android"
    ```

2. Adicione permissões como o primeiro elemento sob o elemento manifest. Substitua **[YOUR PACKAGE NAME]** pelo pacote usado para criar seu aplicativo.

    ```xml
    <permission
        android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE"
        android:protectionLevel="signature" />

    <uses-permission android:name="android.permission.INTERNET"/>

    <uses-permission android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE" />

    <!-- This permission allows your app access to receive push notifications
    from ADM. -->
    <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE" />

    <!-- ADM uses WAKE_LOCK to keep the processor from sleeping when a message is received. -->
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    ```
3. Insira o seguinte elemento como o primeiro filho do elemento application. Lembre-se de substituir **YOUR SERVICE NAME** pelo nome do seu manipulador de mensagens do ADM que você criará na próxima seção (incluindo o pacote) e substitua **[YOUR PACKAGE NAME]** pelo nome do pacote com o qual você criou o seu aplicativo.

    ```xml
    <amazon:enable-feature
        android:name="com.amazon.device.messaging"
        android:required="true"/>
    <service
        android:name="[YOUR SERVICE NAME]"
        android:exported="false" />

    <receiver
        android:name="[YOUR SERVICE NAME]$Receiver" />

        <!-- This permission ensures that only ADM can send your app registration broadcasts. -->
        android:permission="com.amazon.device.messaging.permission.SEND" >

        <!-- To interact with ADM, your app must listen for the following intents. -->
        <intent-filter>
        <action android:name="com.amazon.device.messaging.intent.REGISTRATION" />
        <action android:name="com.amazon.device.messaging.intent.RECEIVE" />

        <!-- Replace the name in the category tag with your app's package name. -->
        <category android:name="[YOUR PACKAGE NAME]" />
        </intent-filter>
    </receiver>
    ```

## <a name="create-your-adm-message-handler"></a>Crie o manipulador de mensagens do ADM:

1. Crie uma classe nova que herda do `com.amazon.device.messaging.ADMMessageHandlerBase` e nomeie-a `MyADMMessageHandler`, conforme mostrado na seguinte figura:

    ![][6]

2. Adicione as seguintes declarações de `import` :

    ```java
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.support.v4.app.NotificationCompat;
    import com.amazon.device.messaging.ADMMessageReceiver;
    import com.microsoft.windowsazure.messaging.NotificationHub
    ```

3. Adicione o seguinte código à classe criada. Lembre-se de substituir o nome e cadeia de conexão do hub (escuta):

    ```java
    public static final int NOTIFICATION_ID = 1;
    private NotificationManager mNotificationManager;
    NotificationCompat.Builder builder;
        private static NotificationHub hub;
    public static NotificationHub getNotificationHub(Context context) {
        Log.v("com.wa.hellokindlefire", "getNotificationHub");
        if (hub == null) {
            hub = new NotificationHub("[hub name]", "[listen connection string]", context);
        }
        return hub;
    }

    public MyADMMessageHandler() {
            super("MyADMMessageHandler");
        }

        public static class Receiver extends ADMMessageReceiver
        {
            public Receiver()
            {
                super(MyADMMessageHandler.class);
            }
        }

        private void sendNotification(String msg) {
            Context ctx = getApplicationContext();

            mNotificationManager = (NotificationManager)
                ctx.getSystemService(Context.NOTIFICATION_SERVICE);

        PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                new Intent(ctx, MainActivity.class), 0);

        NotificationCompat.Builder mBuilder =
                new NotificationCompat.Builder(ctx)
                .setSmallIcon(R.mipmap.ic_launcher)
                .setContentTitle("Notification Hub Demo")
                .setStyle(new NotificationCompat.BigTextStyle()
                        .bigText(msg))
                .setContentText(msg);

            mBuilder.setContentIntent(contentIntent);
            mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
    }
    ```
4. Adicione o seguinte código ao método `OnMessage()` :

    ```java
    String nhMessage = intent.getExtras().getString("msg");
    sendNotification(nhMessage);
    ```
5. Adicione o seguinte código ao método `OnRegistered` :

    ```java
    try {
        getNotificationHub(getApplicationContext()).register(registrationId);
    } catch (Exception e) {
        Log.e("[your package name]", "Fail onRegister: " + e.getMessage(), e);
    }
    ```
6. Adicione o seguinte código ao método `OnUnregistered` :

    ```java
    try {
        getNotificationHub(getApplicationContext()).unregister();
    } catch (Exception e) {
        Log.e("[your package name]", "Fail onUnregister: " + e.getMessage(), e);
    }
    ```
7. No método `MainActivity` , adicione a seguinte instrução de importação:

    ```java
    import com.amazon.device.messaging.ADM;
    ```
8. Adicione o código a seguir no final do método `OnCreate` :

    ```java
    final ADM adm = new ADM(this);
    if (adm.getRegistrationId() == null)
    {
        adm.startRegister();
    } else {
        new AsyncTask() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {
                        MyADMMessageHandler.getNotificationHub(getApplicationContext()).register(adm.getRegistrationId());
                    } catch (Exception e) {
                        Log.e("com.wa.hellokindlefire", "Failed registration with hub", e);
                        return e;
                    }
                    return null;
                }
            }.execute(null, null, null);
    }
    ```

## <a name="add-your-api-key-to-your-app"></a>Adicione a chave de API ao seu aplicativo

1. No Eclipse, crie um arquivo chamado `api_key.txt` nos ativos de diretório de seu projeto.
2. Abra o arquivo e copie Chave da API gerada no portal do desenvolvedor da Amazon.

## <a name="run-the-app"></a>Execute o aplicativo

1. Inicie o emulador.
2. No emulador, passe o dedo de cima para baixo e clique em **Configurações**, em seguida, clique em **Minha conta** e registre-se com uma conta válida da Amazon.
3. No Eclipse, execute o aplicativo.

> [!NOTE]
> Se ocorrer um problema, verifique a hora do emulador (ou dispositivo). O valor de hora deve ser preciso. Para alterar a hora do emulador do Kindle, você pode executar o seguinte comando no diretório de ferramentas da plataforma do SDK do Android:

```shell
adb shell  date -s "yyyymmdd.hhmmss"
```

## <a name="send-a-notification-message"></a>Enviar uma mensagem de notificação

Para enviar uma mensagem usando o .NET:

```csharp
static void Main(string[] args)
{
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("[conn string]", "[hub name]");

    hub.SendAdmNativeNotificationAsync("{\"data\":{\"msg\" : \"Hello from .NET!\"}}").Wait();
}
```

![][7]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você envia notificações de transmissão para todos os seus dispositivos Kindle registrados com o back-end. Para saber como enviar notificações por push a dispositivos Kindle específicos, vá para o tutorial a seguir:  o tutorial a seguir mostra como enviar notificações por push para dispositivos Android específicos, mas você pode usar a mesma lógica para enviar notificações para dispositivos Kindle específicos.

> [!div class="nextstepaction"]
>[Notificações por push para especificar dispositivos](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)

<!-- URLs. -->
[portal do desenvolvedor da Amazon]: https://developer.amazon.com/home.html
[baixar o SDK]: https://developer.amazon.com/public/resources/development-tools/sdk
[0]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal1.png
[1]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal2.png
[2]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal3.png
[3]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal4.png
[4]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal5.png
[5]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-cmd-window.png
[6]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-new-java-class.png
[7]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-notification.png
