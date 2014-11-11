<properties linkid="develop-notificationhubs-tutorials-send-breaking-news-ios" urlDisplayName="Breaking News" pageTitle="Notification Hubs Breaking News Tutorial - iOS" metaKeywords="" description="Learn how to use Azure Service Bus Notification Hubs to send breaking news notifications to iOS devices." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Use Notification Hubs to send breaking news" authors="elioda" solutions="" manager="" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="java" ms.topic="article" ms.date="01/01/1900" ms.author="elioda" />

# Usar Hubs de Notificação para enviar notícias de última hora

<div class="dev-center-tutorial-selector sublanding">       
    <a href="/pt-br/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/" title="Windows Universal" >Windows Universal</a><a href="/pt-br/documentation/articles/notification-hubs-windows-phone-send-breaking-news/" title="Windows Phone">Windows Phone</a><a href="/pt-br/documentation/articles/notification-hubs-ios-send-breaking-news/" title="iOS">iOS</a>
    <a href="/pt-br/documentation/articles/notification-hubs-aspnet-backend-android-breaking-news/" title="Android" class="current">Android</a>
</div>

Este tópico mostra como usar os Hubs de Notificação do Azure para transmitir notícias de última hora a um aplicativo Android. Ao concluir, você poderá se registrar nas categorias de últimas notícias que desejar e receber notificações por push apenas para essas categorias. Esse cenário é um padrão comum para muitos aplicativos nos quais as notificações precisam ser enviadas para grupos de usuários que tenham anteriormente expressado seu interesse por elas; por ex., leitor de RSS, aplicativos para fãs de música, etc.

Os cenários de transmissão são habilitados por meio da inclusão de um ou mais *rótulos* durante a criação de um registro no hub de notificação. Quando as notificações são enviadas para um rótulo, todos os dispositivos que foram registrados para o rótulo receberão a notificação. Como os rótulos são simplesmente cadeias de caracteres, eles não precisam ser provisionados com antecedência. Para obter mais informações sobre rótulos, consulte [Diretrizes dos Hubs de Notificação][Diretrizes dos Hubs de Notificação].

Este tutorial explicará estas etapas básicas a serem seguidas para habilitar este cenário:

1.  [Adicionar a seleção de categorias ao aplicativo][Adicionar a seleção de categorias ao aplicativo]
2.  [Registrar-se para receber notificações][Registrar-se para receber notificações]
3.  [Enviar notificações de seu back-end][Enviar notificações de seu back-end]
4.  [Executar o aplicativo e gerar notificações][Executar o aplicativo e gerar notificações]

Este tópico se baseia no aplicativo criado em [Introdução aos Hubs de Notificação][Introdução aos Hubs de Notificação]. Antes de iniciar o tutorial, você deve primeiro concluir a [Introdução aos Hubs de Notificação][Introdução aos Hubs de Notificação].

## <a name="adding-categories"></a>Adicionar a seleção de categorias ao aplicativo

A primeira etapa é adicionar os elementos da interface do usuário na atividade principal existente, o que permite ao usuário selecionar categorias para o registro. As categorias selecionadas por um usuário são armazenadas no dispositivo. Quando o aplicativo é iniciado, o registro do dispositivo é criado no seu hub de notificação com as categorias selecionadas como rótulos.

1.  Abra o arquivo res/layout/activity\_main.xml e substitua o conteúdo pelo seguinte:

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

2.  Abra o arquivo res/values/strings.xml e adicione as seguintes linhas:

        <string name="button_subscribe">Subscribe</string>
        <string name="label_world">World</string>
        <string name="label_politics">Politics</string>
        <string name="label_business">Business</string>
        <string name="label_technology">Technology</string>
        <string name="label_science">Science</string>
        <string name="label_sports">Sports</string>

    O layout gráfico do main\_activity.xml deve ter esta aparência:

    ![][0]

3.  Agora, crie uma classe **Notificações** no mesmo pacote que a classe **MainActivity**.

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

4.  No código acima, substitua os espaços reservados `<hub name>` e `<connection string with listen access>` pelo nome de hub da notificação e a cadeia de conexão pelo *DefaultListenSharedAccessSignature* que você tiver obtido anteriormente.

    <div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong> 
    <p>Como as credenciais que s&atilde;o distribu&iacute;das com um aplicativo cliente geralmente n&atilde;o s&atilde;o seguras, voc&ecirc; s&oacute; deve distribuir a chave para acesso de escuta com o aplicativo cliente. O acesso de escuta permite que seu aplicativo se registre para receber notifica&ccedil;&otilde;es, mas os registros existentes n&atilde;o podem ser modificados e as notifica&ccedil;&otilde;es n&atilde;o podem ser enviadas. A chave de acesso completa &eacute; usada em um servi&ccedil;o de back-end seguro para enviar notifica&ccedil;&otilde;es e alterar os registros existentes.</p>
</div>

5.  Na classe **MainActivity**, remova os campos particulares para **NotificationHub** e **GoogleCloudMessaging** e adicione um campo para **Notificações**:

        // private GoogleCloudMessaging gcm;
        // private NotificationHub hub;
        private Notifications notifications;

6.  Em seguida, no método **onCreate**, remova a inicialização do campo **hub** e o método **registerWithNotificationHubs**. Depois, adicione as linhas a seguir, que inicializam uma instância da classe **Notificações**. O método deve conter as linhas a seguir:

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            NotificationsManager.handleNotifications(this, SENDER_ID,
                    MyHandler.class);

            notifications = new Notifications(this, SENDER_ID);
        }

7.  Em seguida, adicione o seguinte método:

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

## <a name="register"></a>Registrar-se para receber notificações

Estas etapas registram com o hub de notificação na inicialização, usando as categorias que foram armazenadas no armazenamento local.

<div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong> 
    <p>Como o registrationId atribu&iacute;do pelo Google Cloud Messaging (GCM) pode ser alterado a qualquer momento, voc&ecirc; deve se registrar para receber notifica&ccedil;&otilde;es com frequ&ecirc;ncia para evitar falhas de notifica&ccedil;&atilde;o. Este exemplo registra a notifica&ccedil;&atilde;o a cada vez que o aplicativo &eacute; iniciado. Para os aplicativos que s&atilde;o executados com frequ&ecirc;ncia, mais de uma vez por dia, provavelmente &eacute; poss&iacute;vel ignorar o registro para preservar a largura de banda se tiver passado menos de um dia desde o registro anterior.</p>
</div>

1.  Adicione o seguinte código à classe **Notificações**:

        public Set<String> retrieveCategories() {
            SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
            return settings.getStringSet("categories", new HashSet<String>());
        }

    Esse procedimento retorna as categorias definidas na classe.

2.  Agora, adicione este código ao final do método **onCreate** na classe **MainActivity**:

        notifications.subscribeToCategories(notifications.retrieveCategories());

    Isso garante que o aplicativo recupere as categorias do armazenamento local e solicite um registro para essas categorias toda vez que ele for iniciado. O método **InitNotificationsAsync** foi criado como parte do tutorial [Introdução aos Hubs de Notificação], mas ele não é necessário neste tópico.

3.  Em seguida, adicione o seguinte método a **MainActivity**:

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

## <a name="send"></a><span class="short-header">Enviar notificações</span>Enviar notificações por meio de seu back-end

[WACOM.INCLUDE [notification-hubs-back-end](../includes/notification-hubs-back-end.md)]

## <a name="test-app"></a>Executar o aplicativo e gerar notificações

1.  No Eclipse, crie o aplicativo e o inicie em um dispositivo ou emulador.

    Observe que a interface do usuário do aplicativo fornece um conjunto de alternâncias que permite escolher as categorias nas quais você poderá assinar.

2.  Habilite uma ou mais alternâncias de categorias e depois clique em **Assinar**.

    O aplicativo converte as categorias selecionadas em rótulos e solicita um novo registro do dispositivo para os rótulos selecionados do hub de notificação. As categorias registradas são retornadas e exibidas em uma caixa de diálogo.

3.  Envie uma nova notificação do back-end usando uma das seguintes maneiras:

    -   **Aplicativo .NET Console:** inicie o aplicativo do console.

    -   **Java/PHP:** execute o aplicativo/script.

    As notificações para as categorias selecionadas são exibidas como notificações do sistema.

## <a name="next-steps"> </a>Próximas etapas

Neste tutorial, aprendemos como enviar as notícias mais recentes por categoria. Considere a conclusão de um dos seguintes tutoriais que destacam outros cenários avançados de Hubs de Notificação:

-   [Usar os Hubs de Notificação para transmitir as últimas notícias localizadas][Usar os Hubs de Notificação para transmitir as últimas notícias localizadas]

    Saiba como expandir o aplicativo das últimas notícias para habilitar o envio de notificações localizadas.

-   [Notificar usuários com Hubs de Notificação][Notificar usuários com Hubs de Notificação]

    Saiba como enviar notificações por push a usuários autenticados específicos. É uma boa solução enviar notificações somente a usuários específicos.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs.-->

  [Diretrizes dos Hubs de Notificação]: http://msdn.microsoft.com/pt-br/library/jj927170.aspx
  [Adicionar a seleção de categorias ao aplicativo]: #adding-categories
  [Registrar-se para receber notificações]: #register
  [Enviar notificações de seu back-end]: #send
  [Executar o aplicativo e gerar notificações]: #test-app
  [Introdução aos Hubs de Notificação]: /pt-br/documentation/articles/notification-hubs-android-get-started/
  [0]: ./media/notification-hubs-aspnet-backend-android-breaking-news/android-breaking-news1.PNG
  [notification-hubs-back-end]: ../includes/notification-hubs-back-end.md
  [Usar os Hubs de Notificação para transmitir as últimas notícias localizadas]: /pt-br/manage/services/notification-hubs/breaking-news-localized-dotnet/
  [Notificar usuários com Hubs de Notificação]: /pt-br/manage/services/notification-hubs/notify-users
