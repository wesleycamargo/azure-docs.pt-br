1.  Abra o arquivo do projeto mainpage.xaml.cs e adicione o seguinte snippet de código à classe MainPage:

        private MobileServiceUser user;
        private async System.Threading.Tasks.Task Authenticate()
        {
            while (user == null)
            {
                string message;
                try
                {
                    user = await App.MobileService
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                    message =
                        string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }

                MessageBox.Show(message);
            }
        }

    Isso cria uma variável de membro para armazenar o usuário atual e um método para manipular o processo de autenticação. O usuário é autenticado usando um logon do Facebook.

    > [WACOM.NOTE]Se você estiver usando um provedor de identidade além do Facebook, altere o valor **MobileServiceAuthenticationProvider** acima para o valor de seu provedor.
    >
    > </div>
    > </p>

2.  Exclua o método **OnNavigatedTo** existente, ou remova seu comentário, e substitua-o pelo método a seguir que trata o evento **Loaded** para a página.

        async void MainPage_Loaded(object sender, RoutedEventArgs e)
        {
            await Authenticate();
            RefreshTodoItems();
        }

    Esse método chamará o novo método **Authenticate**.

3.  Substitua o construtor MainPage por este código:

        // Constructor
        public MainPage()
        {
            InitializeComponent();
            this.Loaded += MainPage_Loaded;
        }

    Esse construtor também registrará o manipulador para o evento Loaded.

4.  Pressione a tecla F5 para executar o aplicativo e entrar no aplicativo com seu provedor de identidade escolhido.

    Ao entrar com êxito, o aplicativo deve ser executado sem erros, e você deve ser capaz de consultar os Serviços Móveis e fazer atualizações de dados.


