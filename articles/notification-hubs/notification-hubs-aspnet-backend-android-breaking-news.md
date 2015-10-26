<properties
	pageTitle="Tutorial de últimas notícias de Hubs de Notificação - Android"
	description="Aprenda a usar Hubs de notificação do barramento de serviço do Azure para enviar notificações de últimas notícias a dispositivos com Android."
	services="notification-hubs"
	documentationCenter="android"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="09/08/2015" 
	ms.author="wesmc"/>


# Usar hubs de notificação para enviar notícias recentes

[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

##Visão geral

Este tópico mostra como usar os Hubs de Notificação do Azure para transmitir notificações de últimas notícias para um aplicativo Android. Ao concluir, você poderá se registrar nas categorias de últimas notícias que desejar e receber notificações por push apenas para essas categorias. Esse cenário é um padrão comum para muitos aplicativos nos quais as notificações precisam ser enviadas para grupos de usuários que tenham anteriormente expressado seu interesse por elas; por ex., leitor de RSS, aplicativos para fãs de música, etc.

Os cenários de transmissão são habilitados por meio da inclusão de um ou mais _rótulos_ durante a criação de um registro no hub de notificação. Quando as notificações são enviadas para um rótulo, todos os dispositivos que foram registrados para o rótulo receberão a notificação. Como os rótulos são simplesmente cadeias de caracteres, eles não precisam ser provisionados com antecedência. Para obter mais informações sobre rótulos, consulte [Diretrizes dos Hubs de Notificação].


##Pré-requisitos

Este tópico se baseia no aplicativo criado em [Introdução aos Hubs de Notificação][get-started]. Antes de iniciar o tutorial, você deve primeiro concluir a [Introdução aos Hubs de Notificação][get-started].

##Adicionar a seleção de categorias ao aplicativo

A primeira etapa é adicionar os elementos da interface do usuário na atividade principal existente, o que permite ao usuário selecionar categorias para o registro. As categorias selecionadas por um usuário são armazenadas no dispositivo. Quando o aplicativo é iniciado, o registro do dispositivo é criado no seu hub de notificação com as categorias selecionadas como rótulos.

1. Abra o arquivo res/layout/activity\_main.xml e substitua o conteúdo pelo seguinte:

		<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
		    xmlns:tools="http://schemas.android.com/tools"
		    android:layout_width="match_parent"
		    android:layout_height="match_parent"
		    android:paddingBottom="@dimen/activity_vertical_margin"
		    android:paddingLeft="@dimen/activity_horizontal_margin"
		    android:paddingRight="@dimen/activity_horizontal_margin"
		    android:paddingTop="@dimen/activity_vertical_margin"
		    tools:context="com.example.breakingnews.MainActivity"
		    android:orientation="vertical">

		        <CheckBox
		            android:id="@+id/worldBox"
		            android:layout_width="wrap_content"
		            android:layout_height="wrap_content"
		            android:text="@string/label_world" />
		        <CheckBox
		            android:id="@+id/politicsBox"
		            android:layout_width="wrap_content"
		            android:layout_height="wrap_content"
		            android:text="@string/label_politics" />
		        <CheckBox
		            android:id="@+id/businessBox"
		            android:layout_width="wrap_content"
		            android:layout_height="wrap_content"
		            android:text="@string/label_business" />
		        <CheckBox
		            android:id="@+id/technologyBox"
		            android:layout_width="wrap_content"
		            android:layout_height="wrap_content"
		            android:text="@string/label_technology" />
		        <CheckBox
		            android:id="@+id/scienceBox"
		            android:layout_width="wrap_content"
		            android:layout_height="wrap_content"
		            android:text="@string/label_science" />
		        <CheckBox
		            android:id="@+id/sportsBox"
		            android:layout_width="wrap_content"
		            android:layout_height="wrap_content"
		            android:text="@string/label_sports" />
			    <Button
			        android:layout_width="wrap_content"
			        android:layout_height="wrap_content"
			        android:onClick="subscribe"
			        android:text="@string/button_subscribe" />
		</LinearLayout>

2. Abra o arquivo res/values/strings.xml e adicione as seguintes linhas:

	    <string name="button_subscribe">Subscribe</string>
	    <string name="label_world">World</string>
	    <string name="label_politics">Politics</string>
	    <string name="label_business">Business</string>
	    <string name="label_technology">Technology</string>
	    <string name="label_science">Science</string>
	    <string name="label_sports">Sports</string>

	O layout gráfico do main\_activity.xml deve ter esta aparência:

	![][A1]

3. Agora, crie uma classe **Notificações** no mesmo pacote que a classe **MainActivity**.

		import java.util.HashSet;
		import java.util.Set;

		import android.content.Context;
		import android.content.SharedPreferences;
		import android.os.AsyncTask;
		import android.util.Log;
		import android.widget.Toast;

		import com.google.android.gms.gcm.GoogleCloudMessaging;
		import com.microsoft.windowsazure.messaging.NotificationHub;

		public class Notifications {
			private static final String PREFS_NAME = "BreakingNewsCategories";
			private GoogleCloudMessaging gcm;
			private NotificationHub hub;
			private Context context;
			private String senderId;

			public Notifications(Context context, String senderId) {
				this.context = context;
				this.senderId = senderId;

				gcm = GoogleCloudMessaging.getInstance(context);
		        hub = new NotificationHub(<hub name>, <connection string with listen access>, context);
			}

			public void storeCategoriesAndSubscribe(Set<String> categories)
			{
				SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
			    settings.edit().putStringSet("categories", categories).commit();
			    subscribeToCategories(categories);
			}

			public void subscribeToCategories(final Set<String> categories) {
				new AsyncTask<Object, Object, Object>() {
					@Override
					protected Object doInBackground(Object... params) {
						try {
							String regid = gcm.register(senderId);
					        hub.register(regid, categories.toArray(new String[categories.size()]));
						} catch (Exception e) {
							Log.e("MainActivity", "Failed to register - " + e.getMessage());
							return e;
						}
						return null;
					}

					protected void onPostExecute(Object result) {
						String message = "Subscribed for categories: "
								+ categories.toString();
						Toast.makeText(context, message,
								Toast.LENGTH_LONG).show();
					}
				}.execute(null, null, null);
			}

		}

	Essa classe usa o armazenamento local para armazenar as categorias de notícias que este dispositivo deverá receber. Ela também contém métodos para se registrar nessas categorias.

4. No código acima, substitua os espaços reservados `<hub name>` e `<connection string with listen access>` pelo nome de seu hub de notificação e a cadeia de conexão para *DefaultListenSharedAccessSignature* obtidos anteriormente.

	> [AZURE.NOTE]Como as credenciais que são distribuídas com um aplicativo cliente não são geralmente seguras, você só deve distribuir a chave para acesso de escuta com o aplicativo cliente. O acesso de escuta permite que seu aplicativo se registre para receber notificações, mas os registros existentes não podem ser modificados e as notificações não podem ser enviadas. A chave de acesso completa é usada em um serviço de back-end seguro para enviar notificações e alterar os registros existentes.

4. Na classe **MainActivity**, remova os campos particulares para **NotificationHub** e **GoogleCloudMessaging** e adicione um campo para **Notificações**:

		// private GoogleCloudMessaging gcm;
		// private NotificationHub hub;
		private Notifications notifications;

5. Em seguida, no método **onCreate**, remova a inicialização do campo **hub** e o método **registerWithNotificationHubs**. Depois, adicione as linhas a seguir, que inicializam uma instância da classe **Notificações**. O método deve conter as linhas a seguir:

		@Override
		protected void onCreate(Bundle savedInstanceState) {
			super.onCreate(savedInstanceState);
			setContentView(R.layout.activity_main);

			NotificationsManager.handleNotifications(this, SENDER_ID,
					MyHandler.class);

			notifications = new Notifications(this, SENDER_ID);
		}

6. Em seguida, adicione o seguinte método:

	    public void subscribe(View sender) {
			final Set<String> categories = new HashSet<String>();

			CheckBox world = (CheckBox) findViewById(R.id.worldBox);
			if (world.isChecked())
				categories.add("world");
			CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
			if (politics.isChecked())
				categories.add("politics");
			CheckBox business = (CheckBox) findViewById(R.id.businessBox);
			if (business.isChecked())
				categories.add("business");
			CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
			if (technology.isChecked())
				categories.add("technology");
			CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
			if (science.isChecked())
				categories.add("science");
			CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
			if (sports.isChecked())
				categories.add("sports");

			notifications.storeCategoriesAndSubscribe(categories);
	    }

	Esse método cria uma lista de categorias e usa a classe **Notificações** para armazenar a lista no armazenamento local e registrar os rótulos correspondentes com o hub de notificação. Quando as categorias são alteradas, o registro é recriado com as novas categorias.

Seu aplicativo agora é capaz de armazenar um conjunto de categorias no armazenamento local do dispositivo e registrar com o hub de notificação, sempre que o usuário alterar a seleção de categorias.

##Registrar-se para receber notificações

Estas etapas registram com o hub de notificação na inicialização, usando as categorias que foram armazenadas no armazenamento local.

> [AZURE.NOTE]Como o registrationId atribuído pelo Google Cloud Messaging (GCM) pode ser alterado a qualquer momento, você deve se registrar para receber notificações com frequência para evitar falhas de notificação. Este exemplo registra a notificação a cada vez que o aplicativo é iniciado. Para os aplicativos que são executados com frequência, mais de uma vez por dia, é possível ignorar o registro para preservar a largura de banda se tiver passado menos de um dia desde o registro anterior.

1. Adicione o seguinte código à classe **Notificações**:

		public Set<String> retrieveCategories() {
			SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
			return settings.getStringSet("categories", new HashSet<String>());
		}

	Esse procedimento retorna as categorias definidas na classe.

2. Agora, adicione este código ao final do método **onCreate** na classe **MainActivity**:

		notifications.subscribeToCategories(notifications.retrieveCategories());

	Isso garante que o aplicativo recupere as categorias do armazenamento local e solicite um registro para essas categorias toda vez que ele for iniciado. O método **InitNotificationsAsync** foi criado como parte do tutorial [Introdução aos Hubs de Notificação], mas ele não é necessário neste tópico.

3. Em seguida, adicione o seguinte método a **MainActivity**:

		@Override
		protected void onStart() {
			super.onStart();

			Set<String> categories = notifications.retrieveCategories();

			CheckBox world = (CheckBox) findViewById(R.id.worldBox);
			world.setChecked(categories.contains("world"));
			CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
			politics.setChecked(categories.contains("politics"));
			CheckBox business = (CheckBox) findViewById(R.id.businessBox);
			business.setChecked(categories.contains("business"));
			CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
			technology.setChecked(categories.contains("technology"));
			CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
			science.setChecked(categories.contains("science"));
			CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
			sports.setChecked(categories.contains("sports"));
		}

	Isso atualiza a atividade principal com base no status de categorias salvas anteriormente.

O aplicativo agora está completo e pode armazenar um conjunto de categorias no armazenamento local do dispositivo utilizado para registrá-las com o hub de notificação, sempre que o usuário alterar a seleção de categorias. Em seguida, definiremos um back-end que possa enviar notificações de categoria para esse aplicativo.

##Enviar notificações de seu back-end

[AZURE.INCLUDE [notification-hubs-back-end](../../includes/notification-hubs-back-end.md)]

##Executar o aplicativo e gerar notificações

1. No Eclipse, crie o aplicativo e o inicie em um dispositivo ou emulador.

	Observe que a interface do usuário do aplicativo fornece um conjunto de alternâncias que permite escolher as categorias nas quais você poderá assinar.

2. Habilite uma ou mais alternâncias de categorias e depois clique em **Assinar**.

	O aplicativo converte as categorias selecionadas em rótulos e solicita um novo registro do dispositivo para os rótulos selecionados do hub de notificação. As categorias registradas são retornadas e exibidas em uma caixa de diálogo.

4. Envie uma nova notificação do back-end usando uma das seguintes maneiras:

	+ **Aplicativo Console .NET:** inicie o aplicativo de console.

	+ **Java/PHP:** execute seu aplicativo/script.

	As notificações para as categorias selecionadas são exibidas como notificações do sistema.

##Próximas etapas

Neste tutorial, aprendemos a enviar as últimas notícias por categoria. Considere a conclusão de um dos seguintes tutoriais que destacam outros cenários avançados de Hubs de Notificação:

+ [Usar os Hubs de Notificação para transmitir as últimas notícias localizadas]

	Saiba como expandir o aplicativo das últimas notícias para habilitar o envio de notificações localizadas.

+ [Notificar usuários com Hubs de Notificação]

	Saiba como enviar notificações por push a usuários autenticados específicos. É uma boa solução enviar notificações somente a usuários específicos.




<!-- Images. -->
[A1]: ./media/notification-hubs-aspnet-backend-android-breaking-news/android-breaking-news1.PNG

<!-- URLs.-->
[get-started]: notification-hubs-android-get-started.md
[Usar os Hubs de Notificação para transmitir as últimas notícias localizadas]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Notificar usuários com Hubs de Notificação]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Diretrizes dos Hubs de Notificação]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Azure Management Portal]: https://manage.windowsazure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591

<!---HONumber=Oct15_HO3-->