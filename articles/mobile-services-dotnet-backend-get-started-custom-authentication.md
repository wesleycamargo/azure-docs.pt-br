<properties linkid="mobile-services-dotnet-backend-get-started-custom-authentication" urlDisplayName="Get started with custom authentication" pageTitle="Get started with custom authentication | Mobile Dev Center" metaKeywords="" description="Learn how to authenticate users with a username and password." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with custom authentication" authors="mahender" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="mahender" />

# Introdução à autenticação personalizada

Este tópico mostra como autenticar usuários no back-end .NET dos Serviços Móveis do Azure emitindo seu próprio token de autenticação dos Serviços Móveis. Neste tutorial, você adiciona autenticação ao projeto de Início rápido usando um nome de usuário e senha personalizados para seu aplicativo.

> [WACOM.NOTE] Este tutorial demonstra um método avançado de autenticação de seus Serviços Móveis com credenciais personalizadas. Muitos aplicativos serão melhor ajustados para, em vez de usarem os provedores de identidade social integrados, permitirem que os usuários façam logon via Facebook, Twitter, Google, conta da Microsoft e Azure Active Directory. Se essa for a sua primeira experiência com autenticação em Serviços Móveis, consulte o tutorial [Introdução aos usuários][Introdução aos usuários].

Este tutorial apresenta e explica as etapas básicas para habilitar a autenticação em seu aplicativo:

1.  [Configurar a tabela de contas][Configurar a tabela de contas]
2.  [Criar o ponto de extremidade do registro][Criar o ponto de extremidade do registro]
3.  [Criar o LoginProvider][Criar o LoginProvider]
4.  [Criar o ponto de extremidade de logon][Criar o ponto de extremidade de logon]
5.  [Configurar o serviço móvel para requerer autenticação][Configurar o serviço móvel para requerer autenticação]
6.  [Testar o fluxo de logon usando o cliente de teste][Testar o fluxo de logon usando o cliente de teste]

Este tutorial baseia-se no quickstart dos Serviços Móveis. Você também deve primeiro concluir o tutorial [Introdução aos Serviços Móveis][Introdução aos Serviços Móveis].

> [WACOM.NOTE] A finalidade deste tutorial é mostrar como emitir um token de autenticação para Serviços Móveis. Ele não deve ser usado como diretrizes de segurança. Ao desenvolver seu aplicativo, é necessário estar atento às implicações de segurança do armazenamento de senha, e pode ser necessário ter uma estratégia para gerenciar ataques de força bruta.

## <a name="table-setup"></a>Configurar a tabela de contas

Como você está usando autenticação personalizada e não conta com outro provedor de identidade, será necessário armazenar suas informações de logon dos usuários. Nesta seção, você criará uma tabela para suas contas e configurará os mecanismos de segurança básicos. A tabela de contas incluirá os nomes de usuário e as senhas salt e hash, e você também poderá incluir informações adicionais de usuário, se necessário.

1.  Na pasta `DataObjects` de seu projeto de back-end, crie uma nova entidade chamada `Account`:

            public class Account : EntityData
            {
                public string Username { get; set; }
                public byte[] Salt { get; set; }
                public byte[] SaltedAndHashedPassword { get; set; }
            }

    Essa entidade representará uma linha em nossa nova tabela, e terá o nome de usuário, aquele valor salt do usuário e a senha armazenada com segurança.

2.  Na pasta `Models`, você encontrará uma classe `DbContext` denominada após seu Serviço Móvel. O restante deste tutorial usará `todoContext` como um exemplo, e você precisará atualizar os trechos do código adequadamente. Abra seu contexto e adicione a tabela de contas em seu modelo de dados, incluindo o seguinte:

        public DbSet<Account> Accounts { get; set; }

3.  Em seguida, você configurará as funções de segurança para trabalhar com esses dados. Será necessário gerar um novo valor salt longo, a capacidade de criar hash em uma senha salt e uma maneira segura de comparar dois hashes. Criar uma classe chamada `CustomLoginProviderUtils` e adicionar os seguintes métodos a ela:

        public static byte[] hash(string plaintext, byte[] salt)
        {
            SHA512Cng hashFunc = new SHA512Cng();
            byte[] plainBytes = System.Text.Encoding.ASCII.GetBytes(plaintext);
            byte[] toHash = new byte[plainBytes.Length + salt.Length];
            plainBytes.CopyTo(toHash,0);
            salt.CopyTo(toHash, plainBytes.Length);
            return hashFunc.ComputeHash(toHash);
        }

        public static byte[] generateSalt()
        {
            RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider();
            byte[] salt = new byte[256];
            rng.GetBytes(salt);
            return salt;
        }

        public static bool slowEquals(byte[] a, byte[] b)
        {
            int diff = a.Length ^ b.Length;
            for (int i = 0; i < a.Length && i < b.Length; i++)
            {
                diff |= a[i] ^ b[i];
            }
            return diff == 0;
        }

## <a name="register-endpoint"></a>Criar o ponto de extremidade do registro

Nesse ponto, você tem todo o necessário para começar a criar contas do usuário. Nesta seção, você configurará um ponto de extremidade de registro para tratar das novas solicitações de registro. Aqui você poderá reforçar as novas políticas de nome de usuário e senha e assegurar que o nome de usuário não seja roubado. Em seguida, você armazenará com segurança as informações do usuário em seu banco de dados.

1.  Crie um objeto para representar uma tentativa de registro de entrada:

        public class RegistrationRequest
        {
            public String username { get; set; }
            public String password { get; set; }
        }

    Se desejar coletar outras informações no momento do registro, pode incluí-las aqui.

2.  Em seu projeto de back-end dos Serviços Móveis, adicione um novo controlador personalizado chamado CustomRegistrationController e cole no seguinte:

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]
        public class CustomRegistrationController : ApiController
        {
            public ApiServices Services { get; set; }

            // POST api/CustomRegistration
            public HttpResponseMessage Post(RegistrationRequest registrationRequest)
            {
                if (!Regex.IsMatch(registrationRequest.username, "^[a-zA-Z0-9]{4,}$"))
                {
                    return this.Request.CreateResponse(HttpStatusCode.BadRequest, "Invalid username (at least 4 chars, alphanumeric only)");
                }
                else if (registrationRequest.password.Length < 8)
                {
                    return this.Request.CreateResponse(HttpStatusCode.BadRequest, "Invalid password (at least 8 chars required)");
                }

                todoContext context = new todoContext();
                Account account = context.Accounts.Where(a => a.Username == registrationRequest.username).SingleOrDefault();
                if (account != null)
                {
                    return this.Request.CreateResponse(HttpStatusCode.BadRequest, "Username already exists");
                }
                else
                {
                    byte[] salt = CustomLoginProviderUtils.generateSalt();
                    Account newAccount = new Account
                    {
                        Id = Guid.NewGuid().ToString(),
                        Username = registrationRequest.username,
                        Salt = salt,
                        SaltedAndHashedPassword = CustomLoginProviderUtils.hash(registrationRequest.password, salt)
                    };
                    context.Accounts.Add(newAccount);
                    context.SaveChanges();
                    return this.Request.CreateResponse(HttpStatusCode.Created);
                }
            }
        }   

    Certifique-se de permitir todo o tráfego nesse ponto de extremidade decorando o controlador com:

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]

## <a name="login-provider"></a>Criar o LoginProvider

Uma das construções fundamentais no pipeline de autenticação dos Serviços Móveis é o `LoginProvider`. Nesta seção, você criará seu próprio `CustomLoginProvider`. Ele não será conectado ao pipeline como os provedores integrados, mas fornecerá a você uma funcionalidade conveniente.

1.  Crie uma nova classe, `CustomLoginProvider`, que deriva de `LoginProvider`:

        public class CustomLoginProvider : LoginProvider
        {
            public const string ProviderName = "custom";

            public override string Name
            {
                get { return ProviderName; }
            }

            public CustomLoginProvider(IServiceTokenHandler tokenHandler)
                : base(tokenHandler)
            {
                this.TokenLifetime = new TimeSpan(30, 0, 0, 0);
            }

        }

    O `LoginProvider` possui outros três métodos abstratos que você implementará mais tarde.

2.  Crie uma nova classe chamada `CustomLoginProviderCredentials`. Ela representa as informações sobre o seu usuário e ficará disponível para você no back-end via `ServiceUser.getIdentitiesAsync()`. Se estiver adicionando declarações personalizadas, certifique-se de que elas sejam capturadas nesse objeto.

        public class CustomLoginProviderCredentials : ProviderCredentials
        {
            public CustomLoginProviderCredentials()
                : base(CustomLoginProvider.ProviderName)
            {
            }
        }

3.  Adicione a seguinte implementação do método abstrato `ConfigureMiddleware` a `CustomLoginProvider`. Esse método é um no-op aqui, já que `CustomLoginProvider` não está se integrando com o pipeline de autenticação.

        public override void ConfigureMiddleware(IAppBuilder appBuilder, ServiceSettingsDictionary settings)
        {
            // Not Applicable - used for federated identity flows
            return;
        }

4.  Adicione a seguinte implementação do método abstrato `ParseCredentials` a `CustomLoginProvider`. Esse método permitirá que o back-end desserialize as informações do usuário vindas do token de autenticação de entrada.

        public override ProviderCredentials ParseCredentials(JObject serialized)
        {
            if (serialized == null)
            {
                throw new ArgumentNullException("serialized");
            }

            return serialized.ToObject<CustomLoginProviderCredentials>();
        }

5.  Adicione a seguinte implementação do método abstrato `CreateCredentials` a `CustomLoginProvider`. Esse método converte um `ClaimsIdentity` em um objeto `ProviderCredentials` que é usado na fase de emissão do token de autenticação. Novamente, você pode desejar capturar declarações adicionais aqui.

        public override ProviderCredentials CreateCredentials(ClaimsIdentity claimsIdentity)
        {
            if (claimsIdentity == null)
            {
                throw new ArgumentNullException("claimsIdentity");
            }

            string username = claimsIdentity.FindFirst(ClaimTypes.NameIdentifier).Value;
            CustomLoginProviderCredentials credentials = new CustomLoginProviderCredentials
            {
                UserId = this.TokenHandler.CreateUserId(this.Name, username)
            };

            return credentials;
        }

## <a name="login-endpoint"></a>Criar o ponto de extremidade de logon

Em seguida, você criará um ponto de extremidade para seus usuários fazerem logon. O nome de usuário e a senha que você receber serão verificados com relação ao banco de dados, aplicando primeiro a senha salt e hash do usuário e verificando se o valor recebido corresponde ao armazenado no banco de dados. Se corresponder, você poderá criar um `ClaimsIdentity` e transmiti-lo para o `CustomLoginProvider`. O aplicativo cliente receberá uma ID de usuário e um token de autenticação para outro processo em seu serviço móvel.

1.  Em seu projeto de back-end de Serviços Móveis, crie um objeto para representar uma tentativa de logon:

        public class LoginRequest
        {
            public String username { get; set; }
            public String password { get; set; }
        }

2.  Adicione um novo controlador personalizado chamado `CustomLoginController` e cole-o no seguinte:

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]
        public class CustomLoginController : ApiController
        {
            public ApiServices Services { get; set; }
            public IServiceTokenHandler handler { get; set; }

            // POST api/CustomLogin
            public HttpResponseMessage Post(LoginRequest loginRequest)
            {
                todoContext context = new todoContext();
                Account account = context.Accounts.Where(a => a.Username == loginRequest.username).SingleOrDefault();
                if (account != null)
                {
                    byte[] incoming = CustomLoginProviderUtils.hash(loginRequest.password, account.Salt);

                    if (CustomLoginProviderUtils.slowEquals(incoming, account.SaltedAndHashedPassword))
                    {
                        ClaimsIdentity claimsIdentity = new ClaimsIdentity();
                        claimsIdentity.AddClaim(new Claim(ClaimTypes.NameIdentifier, loginRequest.username));
                        LoginResult loginResult = new CustomLoginProvider(handler).CreateLoginResult(claimsIdentity, Services.Settings.MasterKey);
                        return this.Request.CreateResponse(HttpStatusCode.OK, loginResult);
                    }
                }
                return this.Request.CreateResponse(HttpStatusCode.Unauthorized, "Invalid username or password");
            }
        }

    Certifique-se de permitir todo o tráfego nesse ponto de extremidade decorando o controlador com:

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]

> [WACOM.NOTE] Seu `CustomLoginController` para uso na produção também deve conter uma estratégia de detecção de força bruta. Caso contrário, sua solução de logon poderá estar vulnerável a ataques.

## <a name="require-authentication"></a>Configurar o serviço móvel para requerer autenticação

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

## <a name="test-login"></a>Testar o fluxo de logon usando o cliente de teste

Em seu aplicativo cliente, será necessário desenvolver uma tela de logon personalizada que obtenha os nomes de usuário e senhas e os envie a uma carga JSON para seus pontos de extremidade de registro e logon. Para concluir este tutorial, será necessário apenas usar o cliente de teste integrado para o back-end .NET dos Serviços Móveis.

> [WACOM.NOTE] Os SDKs de Serviços Móveis se comunicarão com o serviço por HTTPS. Se você pretende acessar esse ponto de extremidade via chamada REST direta, deve certificar-se de usar HTTPS para chamar seu serviço móvel, já que as senhas estão sendo enviadas como texto não criptografado.

1.  No Visual Studio, inicie uma nova instância de depuração de seu projeto de back-end dos Serviços Móveis clicando com o botão direito do mouse no projeto e selecionando **Depurar-\>Iniciar nova instância**

    ![][0]

2.  Clique em **Experimentar**

    ![][1]

3.  Selecione seu ponto de extremidade de registro. Você pode ver alguma documentação básica para sua API. Clique em **Experimentar**.

    ![][2]

4.  No corpo, substitua as cadeias de caracteres de exemplo pelo nome de usuário e senha que atendem aos critérios que você especificou antes. Em seguida, clique em **Enviar**. A resposta deve ser **201/Criado**.

    ![][3]

5.  Repita esse processo para seu ponto de extremidade de logon. Depois de enviar o mesmo nome de usuário e senha que você registrou antes, você deve receber a ID de usuário e um token de autenticação.

    ![][4]

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [Introdução aos usuários]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users
  [Configurar a tabela de contas]: #table-setup
  [Criar o ponto de extremidade do registro]: #register-endpoint
  [Criar o LoginProvider]: #login-provider
  [Criar o ponto de extremidade de logon]: #login-endpoint
  [Configurar o serviço móvel para requerer autenticação]: #require-authentication
  [Testar o fluxo de logon usando o cliente de teste]: #test-login
  [Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started
  [mobile-services-restrict-permissions-dotnet-backend]: ../includes/mobile-services-restrict-permissions-dotnet-backend.md
  [0]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-debug-start.png
  [1]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-try-out.png
  [2]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-test-client.png
  [3]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-send-register.png
  [4]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-login-result.png
