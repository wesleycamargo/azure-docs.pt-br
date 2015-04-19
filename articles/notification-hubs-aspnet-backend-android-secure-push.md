<properties 
	pageTitle="Push Seguro dos Hubs de Notificação do Azure" 
	description="Saiba como enviar notificações por push seguro para um aplicativo Android do Azure. Exemplos de códigos escritos em Java e c#." 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor="" 
	services="notification-hubs"/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="09/24/2014" 
	ms.author="ricksal"/>

#Push Seguro dos Hubs de Notificação do Azure

<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/pt-br/documentation/articles/notification-hubs-aspnet-backend-windows-dotnet-secure-push/" title="Windows Universal">Windows Universal</a><a href="/pt-br/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/" title="iOS">iOS</a>
		<a href="/pt-br/documentation/articles/notification-hubs-aspnet-backend-android-secure-push/" title="Android" class="current">Android</a>
</div>

O suporte à notificação por push no Microsoft Azure permite que você acesse uma infraestrutura de envio por push fácil de usar, multiplataforma e expansível que simplifica em muito a implementação de notificações por push para aplicativos de consumidor e empresariais para plataformas móveis. 

Devido a restrições regulatórias ou de segurança, às vezes, um aplicativo pode querer incluir algo na notificação que não pode ser transmitido por meio da infraestrutura de notificação por push padrão. Este tutorial descreve como obter a mesma experiência ao enviar informações confidenciais por meio de uma conexão segura e autenticada entre o dispositivo cliente e o back-end do aplicativo.

Em um nível superior, o fluxo é o seguinte:

1. O back-end do aplicativo:
	- Armazena uma carga segura no banco de dados de back-end.
	- Envia a ID dessa notificação ao dispositivo (nenhuma informação segura é enviada).
2. O dispositivo no aplicativo, ao receber a notificação:
	- O dispositivo entra em contato com o back-end solicitando a carga segura.
	- O aplicativo pode mostrar a carga como uma notificação no dispositivo.

É importante observar que no fluxo anterior (e neste tutorial), presumimos que o dispositivo armazena um token de autenticação em armazenamento local, após o usuário fazer logon. Isso garante uma experiência completamente ininterrupta, uma vez que o dispositivo pode recuperar a carga segura da notificação usando esse token. Se o seu aplicativo não armazenar tokens de autenticação no dispositivo, ou se esses tokens puderem expirar, o aplicativo do dispositivo, após receber a notificação, deve exibir uma notificação genérica solicitando que o usuário inicie o aplicativo. Dessa forma, o aplicativo autentica o usuário e mostra a carga de notificação.

Este tutorial de Push Seguro mostra como enviar uma notificação por push de maneira segura. O tutorial baseia-se no tutorial **Notificação de usuários**, por isso, você deve concluir as etapas nesse tutorial primeiro.

> [AZURE.NOTE] Este tutorial presume que você criou e configurou seu hub de notificação conforme descrito em [Introdução aos Hubs de Notificação (Android)](http://azure.microsoft.com/documentation/articles/notification-hubs-android-get-started/).

[AZURE.INCLUDE [notification-hubs-aspnet-backend-securepush](../includes/notification-hubs-aspnet-backend-securepush.md)]

## Modificar o projeto Android

Agora que você modificou o back-end do aplicativo para enviar apenas a  *id* de uma notificação, é preciso alterar o aplicativo iOS para manipular essa notificação e retornar a chamada do back-end para recuperar a mensagem segura a ser exibida.
Para atingir essa meta, você precisa certificar-se de que seu aplicativo Android Saiba como autenticar-se com o back-end ao receber as notificações de envio.

Agora, modificaremos o fluxo *login* para salvar o valor do cabeçalho de autenticação nas preferências compartilhadas de seu aplicativo. Mecanismos análogos podem ser usados para armazenar qualquer token de autenticação (por ex., tokens OAuth) que o aplicativo precisará usar sem solicitar as credenciais do usuário.

1. No projeto do aplicativo Android, adicione as seguintes constantes na parte superior da classe **MainActivity**:

		public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
		public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";

2. Ainda na classe **MainActivity**, atualize o método  `getAuthorizationHeader()` para conter o seguinte código:

		private String getAuthorizationHeader() throws UnsupportedEncodingException {
			EditText username = (EditText) findViewById(R.id.usernameText);
    		EditText password = (EditText) findViewById(R.id.passwordText);
    		String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
    		basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);
    	
    		SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
    		sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();
    	
    		return basicAuthHeader;
		}

3. Adicione as seguintes instruções  `import` na parte superior do arquivo **MainActivity**:

		import android.content.SharedPreferences;

Agora, alteraremos o manipulador que é chamado quando a notificação é recebida.

4. Na classe **MyHandler**, altere o método  `OnReceive()` para conter:

		public void onReceive(Context context, Bundle bundle) {
	    	ctx = context;   
	    	String secureMessageId = bundle.getString("secureId");
	    	retrieveNotification(secureMessageId);
		}

5. Em seguida, adicione o método  `retrieveNotification()`, substituindo o espaço reservado  `{back-end endpoint}` pelo ponto de extremidade do back-end obtido ao implantar seu back-end:

		private void retrieveNotification(final String secureMessageId) {
			SharedPreferences sp = ctx.getSharedPreferences(MainActivity.NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
    		final String authorizationHeader = sp.getString(MainActivity.AUTHORIZATION_HEADER_PROPERTY, null);
		
			new AsyncTask<Object, Object, Object>() {
				@Override
				protected Object doInBackground(Object... params) {
					try {
						HttpUriRequest request = new HttpGet("{back-end endpoint}/api/notifications/"+secureMessageId);
						request.addHeader("Authorization", "Basic "+authorizationHeader);
						HttpResponse response = new DefaultHttpClient().execute(request);
						if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
							Log.e("MainActivity", "Error retrieving secure notification" + response.getStatusLine().getStatusCode());
							throw new RuntimeException("Error retrieving secure notification");
						}
						String secureNotificationJSON = EntityUtils.toString(response.getEntity());
						JSONObject secureNotification = new JSONObject(secureNotificationJSON);
						sendNotification(secureNotification.getString("Payload"));
					} catch (Exception e) {
						Log.e("MainActivity", "Failed to retrieve secure notification - " + e.getMessage());
						return e;
					}
					return null;
				}
			}.execute(null, null, null);
		}
		

Esse método chama o back-end do seu aplicativo para recuperar o conteúdo da notificação usando as credenciais armazenadas nas preferências compartilhadas e o exibe como uma notificação normal. Para o usuário do aplicativo, a notificação tem exatamente a mesma aparência que qualquer outra notificação por push.

Observe que é preferível manipular os casos de propriedade de cabeçalho de autenticação ausente ou de rejeição por meio do back-end. A manipulação específica desses casos depende em grande parte da sua meta de experiência do usuário. Uma opção é exibir uma notificação com um aviso genérico para que o usuário realize a autenticação para recuperar a notificação real.

## Executar o aplicativo

Para executar o aplicativo, faça o seguinte:

1. Certifique-se de que o **AppBackend** esteja implantado no Azure. Se estiver usando o Visual Studio, execute o aplicativo da API Web **AppBackend**. Uma página da Web do ASP.NET é exibida.

2. No Eclipse, execute o aplicativo em um dispositivo Android físico ou no emulador.

3. Na interface do usuário do aplicativo Android, insira um nome de usuário e senha. Pode ser qualquer cadeia de caracteres, mas devem ter o mesmo valor.

4. Na interface do usuário do aplicativo Android, clique em **Logon**. Em seguida, clique em **Enviar push**.

<!--HONumber=45--> 
