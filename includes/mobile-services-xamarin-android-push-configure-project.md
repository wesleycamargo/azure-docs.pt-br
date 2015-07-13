
1. No modo de exibição Solução, expanda a pasta **Componentes** no aplicativo Xamarin.Android e verifique se o pacote Serviços Móveis do Azure está instalado. 

2. Clique com o botão direito do mouse na pasta **Componentes**, clique em **Obter Mais Componentes...**, procure o componente **Cliente do Google Cloud Messaging** e adicione-o ao projeto.

1. Abra o arquivo de projeto ToDoActivity.cs e adicione a instrução using a seguir à classe:

		using Gcm.Client;

2. Na classe **ToDoActivity**, adicione o seguinte código novo:

        // Create a new instance field for this activity.
        static ToDoActivity instance = new ToDoActivity();

        // Return the current activity instance.
        public static ToDoActivity CurrentActivity
        {
            get
            {
                return instance;
            }
        }
        // Return the Mobile Services client.
        public MobileServiceClient CurrentClient
        {
            get
            {
                return client;
            }
        }

	Isso permite que você acesse a instância do cliente dos Serviços Móveis do processo de serviço.

3. Altere a declaração de cliente dos Serviços Móveis para pública, da seguinte maneira:

		public MobileServiceClient client { get; private set; }

4.	Adicione o código a seguir ao método **OnCreate**, após a criação do **MobileServiceClient**:

        // Set the current instance of TodoActivity.
        instance = this;

        // Make sure the GCM client is set up correctly.
        GcmClient.CheckDevice(this);
        GcmClient.CheckManifest(this);

        // Register the app for push notifications.
        GcmClient.Register(this, ToDoBroadcastReceiver.senderIDs);

Sua **ToDoActivity** agora está preparada para adicionar notificações por push.

<!---HONumber=62-->