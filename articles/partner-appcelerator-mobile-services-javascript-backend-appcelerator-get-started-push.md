<properties pageTitle="Get started with push notifications (Appcelerator) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Appcelerator app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="Appcelerator team;mahender" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-appcelerator" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="Appcelerator="" team;mahender" />

# Introdução às notificações por push nos Serviços Móveis (push herdado)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/pt-br/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="C# da Windows Store">C# da Windows Store</a>
    <a href="/pt-br/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="JavaScript da Windows Store">JavaScript da Windows Store</a>
    <a href="/pt-br/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
    <a href="/pt-br/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
    <a href="/pt-br/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>     <a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
    <a href="/pt-br/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator" class="current">Appcelerator</a>
</div>

Este tópico mostra como usar os Serviços Móveis do Microsoft Azure para enviar notificações por push para aplicativos iOS e Android desenvolvidos por meio do Appcelerator Titanium Studio. Neste tutorial, você adicionará notificações por push usando o Serviço de Notificação por Push da Apple (APNS) e o sistemas de Mensagens em Nuvem do Google para o projeto do QuickStart. Ao concluir, seu serviço móvel enviará uma notificação por push sempre que um registro for inserido.

> [WACOM.NOTE] Os Serviços Móveis integram-se aos Hubs de Notificação do Azure para oferecer suporte à funcionalidade de notificação por push adicional, como modelos, várias plataformas e escala aprimorada. Este tópico oferece suporte os serviços móveis existentes que ainda não foram atualizados para usar a integração dos Hubs de Notificação. Ao criar um novo serviço móvel, essa funcionalidade integrada é habilitada automaticamente. Você deve atualizar seu serviço para usar os Hubs de Notificação quando possível. **Trabalharemos para disponibilizar um tutorial para push dos Hubs de Notificação com o Appcelerator em breve.**

1.  [Gerar a solicitação de assinatura de certificado][Gerar a solicitação de assinatura de certificado]
2.  [Registrar seu aplicativo e habilitar as notificações por push][Registrar seu aplicativo e habilitar as notificações por push]
3.  [Criar um perfil de provisionamento para o aplicativo][Criar um perfil de provisionamento para o aplicativo]
4.  [Habilitar o sistema de mensagens em nuvem do Google][Habilitar o sistema de mensagens em nuvem do Google]
5.  [Criar o módulo do GCM para Titanium][Criar o módulo do GCM para Titanium]
6.  [Configurar os Serviços Móveis][Configurar os Serviços Móveis]
7.  [Adicionar notificações por push ao aplicativo][Adicionar notificações por push ao aplicativo]
8.  [Atualizar scripts para enviar notificações por push][Atualizar scripts para enviar notificações por push]
9.  [Inserir dados para receber notificações][Inserir dados para receber notificações]

Este tutorial exige o seguinte:

-   Módulo de Serviços Móveis do Azure para Appcelerator
-   Appcelerator Titanium Studio 3.2.1 ou posterior
-   Um dispositivo compatível com iOS 7.0 (ou versão posterior) e Android 4.3 (ou versão posterior)
-   Associação no Programa de Desenvolvedores de iOS
-   Uma conta ativa do Google

> [WACOM.NOTE] Devido aos requisitos de configuração das notificações por push, você deve implantar e testar as notificações por push em um dispositivo compatível com o iOS (iPhone ou iPad), em vez de usar o emulador.

Este tutorial baseia-se no quickstart dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis][Introdução aos Serviços Móveis].

[WACOM.INCLUDE [Habilitar Notificações por Push para Apple](../includes/enable-apple-push-notifications.md)]

## <a name="register-gcm"></a>Habilitar o sistema de mensagens em nuvem do Google

> [WACOM.NOTE]Para concluir este procedimento, você deve ter uma conta do Google com um endereço de email verificado. Para criar uma nova conta do Google, vá para [accounts.google.com][accounts.google.com].

[WACOM.INCLUDE [Habilitar GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

## <a name="gcm-module"></a>Criar o módulo do GCM para Titanium

### Preparando o Appcelerator Titanium Studio para criação de módulo

Se você for criar módulos para Android, precisará instalar suporte a Java no Appcelerator Titanium Studio. Consulte [Instalando as ferramentas de desenvolvimento de Java][Instalando as ferramentas de desenvolvimento de Java] do Appcelerator para obter etapas rápidas caso ainda não tenha feito isso.

Você precisará instalar o NDK do Android. Baixe o arquivo .zip adequado em [][]<http://developer.android.com/sdk/ndk/index.html></a> e o extraia para o mesmo local no disco. Lembre-se desse local.

### Criando um novo módulo

1.  Abra o Appcelerator Titanium Studio.

2.  Clique em Arquivo -\> Novo -\> Projeto de Módulo Móvel.

    ![][0]

3.  Na janela seguinte, insira os dados das configurações do projeto:

    -   **Nome do projeto:** em nosso caso, usamos "notificationhub" (pode ser o mesmo).

    -   **ID do Módulo:** em nosso caso, usamos "com.winwire.notificationhub". Ele também deve corresponder ao “ID do aplicativo".

    -   **Metas de Desenvolvimento:** nesse caso, selecionamos Android.

    > [WACOM.NOTE] É importante que o nome do WorkSpace não contenha espaços, caso contrário, teremos problemas ao criar a compilação.

    ![][1]

4.  Pressione Avançar e preencha as informações para o módulo.

    ![][2]

5.  Por fim, pressione Concluir.

6.  Abra o arquivo timodule.xml. E adicione as mudanças a seguir na marca do Android.

        <manifest package="com.winwire.notificationhub" android:versionCode="1" android:versionName="1.0">
        <supports-screens android:anyDensity="true"/>
        <uses-sdk android:minSdkVersion="8"/>
        <uses-permission android:name= "com.google.android.c2dm.permission.RECEIVE"/>
        <permission android:name= "${tiapp.properties['id']}.permission.C2D_MESSAGE" android:protectionLevel="signature"/>
        <uses-permission android:name= "${tiapp.properties['id']}.permission.C2D_MESSAGE"/>
        <uses-permission android:name= "android.permission.WAKE_LOCK"/>
        <uses-permission android:name= "android.permission.VIBRATE"/>
        <uses-permission android:name= "android.permission.INTERNET"/>
        <uses-permission android:name= "android.permission.GET_ACCOUNTS"/>
        <uses-permission android:name= "android.permission.USE_CREDENTIALS"/>
        <uses-permission android:name= "android.permission.ACCESS_NETWORK_STATE"/>
        <application>
        <service android:name= "com.winwire.notificationhub.GCMIntentService" />
        <receiver android:name= "com.google.android.gcm.GCMBroadcastReceiver" android:permission= "com.google.android.c2dm.permission.SEND">
        <!-- Start receiver on boot -->
        <intent-filter>
        <action android:name= "android.intent.action.BOOT_COMPLETED"/>
        <category android:name= "android.intent.category.HOME"/>
        </intent-filter>
        <!-- Receive the actual message -->
        <intent-filter>
        <action android:name= "com.google.android.c2dm.intent.RECEIVE" />
        <category android:name= "${tiapp.properties['id']}" />
        </intent-filter>
        <!-- Receive the registration id -->
        <intent-filter>
        <action android:name= "com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name= "${tiapp.properties['id']}" />
        </intent-filter>
        </receiver>
        </application>
        </manifest>

     > [WACOM.NOTE] No código acima, você deve substituir todas as instâncias do texto *com.winwire.notificationhub* pelo nome do pacote do seu aplicativo (ID do módulo).

1.  No módulo do Hub de Notificação, clique com o botão direito do mouse na pasta "src" e vá para "Novo" e selecione "Pasta". Insira o nome da pasta como com.google.android.gcm.

     > [WACOM.NOTE] Se não conseguir ver a opção "Pasta" em “Novo", selecione “Outro”, expanda Geral e selecione "Pasta".

1.  Agora, baixe os arquivos ".java" (gcm.zip) daqui e copie esses arquivos na pasta recém-criada (com.google.android.gcm).

2.  Encontre a pasta com o mesmo nome de seu ID de módulo e a expanda. Nessa pasta, você pode ver a lista de arquivos ".java". Nesses arquivos, abra o arquivo com o nome do projeto+module.java (por exemplo, se seu nome de projeto for notificationhub, o nome do arquivo será "NotificationhubModule.java") e adicione as linhas de código abaixo na parte superior.

        private static NotificationhubModule _THIS;
        private KrollFunction successCallback;
        private KrollFunction errorCallback;
        private KrollFunction messageCallback;

3.  No mesmo arquivo, localize o construtor e o substitua pelo código abaixo.

        public NotificationhubModule() {
            super();
            _THIS = this;
        }

4.  No mesmo arquivo, adicione as linhas de código na parte inferior.

        @Kroll.method
        public void registerC2dm(HashMap options) {
            successCallback = (KrollFunction) options.get("success");
            errorCallback = (KrollFunction) options.get("error");
            messageCallback = (KrollFunction) options.get("callback");
            String registrationId = getRegistrationId();
            if (registrationId != null && registrationId.length() > 0) {
                sendSuccess(registrationId);
            } else {
                GCMRegistrar.register(TiApplication.getInstance(), getSenderId());
            }
        }
        @Kroll.method
        public void unregister() {
            GCMRegistrar.unregister(TiApplication.getInstance());
        }
        @Kroll.method
        public String getRegistrationId() {
            return GCMRegistrar.getRegistrationId(TiApplication.getInstance());
        }
        @Kroll.method
        public String getSenderId() {
            return TiApplication.getInstance().getAppProperties()
            .getString("com.winwire.notificationhub.sender_id", "");
        }

        public void sendSuccess(String registrationId) {
            if (successCallback != null) {
                HashMap data = new HashMap();
                data.put("registrationId", registrationId);
                successCallback.callAsync(getKrollObject(), data);
            }
        }

        public void sendError(String error) {
            if (errorCallback != null) {
                HashMap data = new HashMap();
                data.put("error", error);
                errorCallback.callAsync(getKrollObject(), data);
            }
        }

        public void sendMessage(HashMap messageData) {
            if (messageCallback != null) {
                HashMap data = new HashMap();
                data.put("data", messageData);
                messageCallback.call(getKrollObject(), data);
            }
        }
        public static NotificationhubModule getInstance() {
            return _THIS;
        }

5.  Agora, baixe module.zip e copie os arquivos na pasta com a ID do módulo como seu mesmo nome.

    > [WACOM.NOTE] Nos arquivos acima, você deve substituir todas as instâncias do texto *com.winwire.notificationhub* pelo nome do pacote do seu aplicativo (ID do módulo). Além disso, substitua "NotificationhubModule" por ProjectName+Module (p. ex., "NotificationhubModule").

### Criando/empacotando um módulo

Escolha **Implantar \> Pacote - Módulo Android**. Você não pode criar um módulo para BlackBerry usando o Studio; use as ferramentas de CLI do NDK para BlackBerry ou o IDE do Momentics.

![][3]

Você pode então escolher implantar o módulo para todos os projetos ou para um projeto específico. No entanto, isso segue as regras de instalação conforme observado em [Usando Módulos do Titanium][Usando Módulos do Titanium], resumindo:

-   Para todos os projetos: o arquivo module .zip é solto na raiz do local de instalação do SDK do Titanium.

-   Para um projeto específico: o arquivo .zip do módulo é solto na raiz do seu projeto.

## <a name="configure"></a>Configurar Serviços Móveis para enviar solicitações de push

[WACOM.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]

1.  Insira o valor da Chave de API obtido do GCM no procedimento anterior e clique em Salvar.

    ![][4]

Agora, seu serviço móvel está configurado para funcionar com o APNS e o GCM.

## <a name="add-push"></a>Adicionar notificações de push para seu aplicativo

1.  Em tiapp.xml, selecione a guia tiapp.xml (essa guia pode ser encontrada na parte inferior próxima à guia "Visão Geral") e encontre a marca `<android>`. Abaixo da marca, adicione o seguinte código:

        <modules>
            <module platform="android">ModuleId</module>
        </modules>
        <property name="ApplicationId.sender_id" type="string">Provide your GCM sender ID</property>
        <property name="ApplicationId.icon type="int">2130837504</property>
        <property name="ApplicationId.component" type="string">ApplicationId/ApplicationId.AppNameActivity</property>

     > [WACOM.NOTE] No código acima, você precisa substituir **ModuleId** e **ApplicationId**. Seu ID do Módulo, o qual você forneceu no momento da criação do Módulo do GCM, e a ID do Aplicativo, que é inserido no momento da criação do projeto. Além disso, assegure que **ModuleId** e **ApplicationId** sejam iguais. Também é preciso alterar **ApplicationId.AppNameActivity**. Ele deve ser parecido com com.winwire.notificationhub/ com.winwire.notificationhub.NotificationhubActivity.

1.  Copie o módulo do GCM criado anteriormente e coloque-o no local abaixo.

    <table>
    <tr>
    <td>
    OSX

    </td>
    <td>
    /Library/Application Support/Titanium ou ~/Library/Application Support/Titanium

    </td>
    </tr>
    <td>
    Windows 7

    </td>
    <td>
    C:\\Users\\username\\AppData\\Roaming (ou C:\\ProgramData\\Titanium no Titanium Studio 1.0.1 e em versões anteriores)

    </td>
    </tr>
    <td>
    Windows XP

    </td>
    <td>
    C:\\Documents and Settings\\username\\Application Data (ou C:\\Documents and Settings\\All Users\\Application Data\\Titanium no Titanium Studio 1.0.1 e em versões anteriores)

    </td>
    </tr>
    <td>
    Linux

    </td>
    <td>
    ~/.titanium

    </td>
    </tr>
    </tr>
    </table>

     > [WACOM.NOTE] No Mac OS, a Biblioteca é uma pasta oculta. Para torná-la visível, você precisa executar o comando a seguir e abrir o Localizador novamente: `**defaults write com.apple.finder AppleShowAllFiles TRUE**`

1.  No Appcelerator Titanium Studio, abra o arquivo index.js e adicione o código a seguir na parte inferior. Esse código registrará seu dispositivo para notificações por push.

            Alloy.Globals.tempRegId = '';
                if (OS_ANDROID) {
                var gcm = require('com.winwire.notificationhub');
            gcm.registerC2dm({
            success : function(e) {
            var regId = e.registrationId;
            Alloy.Globals.tempRegId = regId;
            },
            error : function(e) {
            alert("Error during registration : " + e.error);
            var message;
            if (e.error == "ACCOUNT_MISSING") {
            message = "No Google account found; you will need to add on in order to activate notifications";
            }
            Titanium.UI.createAlertDialog({
            title : 'Push Notification Setup',
            message : message,
            buttonNames : ['OK']
            }).show();
            },
            callback : function(e)// called when a push notification is received
            {
            var data = JSON.stringify(e.data);
            var intent = Ti.Android.createIntent({
            action : Ti.Android.ACTION_MAIN,
            className : 'com.winwire.notificationhub. WindowsAzureActivity',
            flags : Ti.Android.FLAG_ACTIVITY_RESET_TASK_IF_NEEDED | Ti.Android.FLAG_ACTIVITY_SINGLE_TOP
            });
            intent.addCategory(Titanium.Android.CATEGORY_LAUNCHER);
            var NotificationClickAction = Ti.Android.createPendingIntent({
            activity : Ti.Android.currentActivity,
            intent : intent,
            flags : Ti.Android.FLAG_UPDATE_CURRENT,
            type : Ti.Android.PENDING_INTENT_FOR_ACTIVITY
            });
            var NotificationMembers = {
            contentTitle : 'Notification Hub Demo',
            contentText : e.data.message,
            defaults : Titanium.Android.NotificationManager.DEFAULT_SOUND,
            tickerText : 'Notification Hub Demo',
            icon : Ti.App.Android.R.drawable.appicon,
            when : new Date(),
            flags : Ti.Android.FLAG_AUTO_CANCEL,
            contentIntent : NotificationClickAction
            };
            Ti.Android.NotificationManager.notify(1, .Android.createNotification(NotificationMembers));
            var toast = Titanium.UI.createNotification({
            duration : 2000,
            message : e.data.message
            });
            toast.show();
            alert(e.data.message);
            }
            });
            } else  if (OS_IOS) {
            Titanium.Network.registerForPushNotifications({
            types : [Titanium.Network.NOTIFICATION_TYPE_BADGE, .Network.NOTIFICATION_TYPE_ALERT, .Network.NOTIFICATION_TYPE_SOUND],
            success : function(e) {
            deviceToken = e.deviceToken;
            Alloy.Globals.tempRegId = deviceToken;
            },
            error : function(e) {
            Ti.API.info("Error during registration: " + e.error);
            },
            callback : function(e) {
            var data = e.data.inAppMessage;
            if (data != null && data != '') {
            alert(data);
            }
            }
            });
            }

2.  Agora, abra o arquivo TableData.js e localize as linhas a seguir na função **addOrUpdateDataFromAndroid**

        var request = {
            'text' : alertTextField.getValue(),
            'complete' : false
        };

    Substitua o código acima somente na condição else (para inserir o novo item).

3.  Para Android, substitua o código acima pelo seguinte código:

        var request = {
        'text' : alertTextField.getValue(),
        'complete' : false,
        'handle' : Alloy.Globals.tempRegId
        };

4.  Agora, encontre as linhas a seguir na função **updateOrAddData**

           var request = {
            'text' : alertTextField.getValue(),
            'complete' : false
        };

    Substitua o código acima somente na condição else (para inserir o novo item).

5.  Para iOS, substitua o código acima pelo seguinte código:

        var request = {
        'text' : alertTextField.getValue(),
        'complete' : false,
        'deviceToken' : Alloy.Globals.tempRegId
        };

Seu aplicativo foi atualizado para oferecer suporte a notificações por push nas plataformas iOS e Android.

## <a name="update-scripts"></a>Atualizar o script de inserção registrados no Portal de Gerenciamento

1.  No Portal de Gerenciamento, clique na guia Dados e clique na tabela TodoItem.

    ![][5]

2.  Em todoitem, clique na guia Script e selecione Inserir.

    ![][6]

    Isso exibe a função que é chamada quando ocorre uma inserção na tabela TodoItem.

3.  Substitua a função de inserção com o seguinte código e **Salvar**:

**Para iOS:**

        function insert(item, user, request) { 
            request.execute();
            // Set timeout to delay the notification, to provide time for the  
            // app to be closed on the device to demonstrate toast notifications 
            setTimeout(function() { 
                push.apns.send(item.deviceToken, { 
                    alert: "Toast: " + item.text, 
                    payload: { 
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'" 
                    }
                 }); 
            }, 2500);
        }

    > [WACOM.NOTE] This script delays sending the notification to give you time to close the app to receive a push notification.  

**Para Android:**

          function insert(item, user, request) {
            request.execute({ 
                success: function() {  
                    // Write to the response and then send the notification in the background 
                    request.respond();  
                    push.gcm.send(item.handle, item.text, {
                        success: function(response) { 
                            console.log('Push notification sent: ', response);
                        }, error: function(error)   { 
                            console.log('Error sending push notification: ', error);      
                            }      
                   }); 
                }  
          });  
        }

Isso registra um novo script de inserção, o qual usa o [objeto de push dos Serviços Móveis][objeto de push dos Serviços Móveis] para enviar uma notificação por push (o texto inserido) ao dispositivo fornecido na solicitação de inserção.





  [Gerar a solicitação de assinatura de certificado]: #certificates
  [Registrar seu aplicativo e habilitar as notificações por push]: #register
  [Criar um perfil de provisionamento para o aplicativo]: #profile
  [Habilitar o sistema de mensagens em nuvem do Google]: #register-gcm
  [Criar o módulo do GCM para Titanium]: #gcm-module
  [Configurar os Serviços Móveis]: #configure
  [Adicionar notificações por push ao aplicativo]: #add-push
  [Atualizar scripts para enviar notificações por push]: #update-scripts
  [Inserir dados para receber notificações]: #test
  [Introdução aos Serviços Móveis]: /pt-br/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started
  [accounts.google.com]: http://go.microsoft.com/fwlink/p/?LinkId=268302
  [Instalando as ferramentas de desenvolvimento de Java]: http://docs.appcelerator.com/titanium/latest/#!/guide/Installing_the_Java_Development_Tools
  [0]: http://developer.android.com/sdk/ndk/index.html
  [0]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image0011.png
  [1]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image0031.png
  [2]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image0041.png
  [3]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image0061.png
  [Usando Módulos do Titanium]: http://docs.appcelerator.com/titanium/latest/#!/guide/Using_Titanium_Modules
  [4]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image062.png
  [5]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image064.png
  [6]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image066.png
  [objeto de push dos Serviços Móveis]: http://go.microsoft.com/fwlink/p/?linkid=272333&clcid=0x409
