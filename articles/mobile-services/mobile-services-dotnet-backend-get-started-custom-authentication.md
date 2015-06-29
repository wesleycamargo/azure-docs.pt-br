<properties 
	pageTitle="Introdução à autenticação personalizada | Mobile Dev Center" 
	description="Saiba como autenticar usuários com um nome de usuário e senha." 
	documentationCenter="Mobile" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="06/09/2015" 
	ms.author="mahender"/>

# Introdução à autenticação personalizada

## Visão geral
Este tópico mostra como autenticar usuários no back-end .NET dos Serviços Móveis do Azure emitindo seu próprio token de autenticação dos Serviços Móveis. Neste tutorial, você adiciona autenticação ao projeto de Início rápido usando um nome de usuário e senha personalizados para seu aplicativo.

>[AZURE.NOTE]Este tutorial demonstra um método avançado de autenticação de seus Serviços Móveis com credenciais personalizadas. Muitos aplicativos serão melhor ajustados para, em vez de usarem os provedores de identidade social integrados, permitirem que os usuários façam logon via Facebook, Twitter, Google, conta da Microsoft e Azure Active Directory. Se essa for a sua primeira experiência com autenticação nos Serviços Móveis, consulte o tutorial [Adicionar autenticação ao seu aplicativo].

Este tutorial baseia-se no quickstart dos Serviços Móveis. Você também deve primeiro concluir o tutorial [Introdução aos Serviços Móveis].

>[AZURE.IMPORTANT]A finalidade deste tutorial é mostrar como emitir um token de autenticação para Serviços Móveis. Ele não deve ser usado como diretrizes de segurança. Ao desenvolver seu aplicativo, é necessário estar atento às implicações de segurança do armazenamento de senha, e pode ser necessário ter uma estratégia para gerenciar ataques de força bruta.

## Configurar a tabela de contas

Como você está usando autenticação personalizada e não conta com outro provedor de identidade, será necessário armazenar as informações de entrada dos usuários. Nesta seção, você criará uma tabela para suas contas e configurará os mecanismos de segurança básicos. A tabela de contas incluirá os nomes de usuário e as senhas salt e hash, e você também poderá incluir informações adicionais de usuário, se necessário.

1. Na pasta **DataObjects** do seu projeto de back-end, crie uma nova entidade chamada `Account`.

2. Adicione a instrução `using` a seguir:

		using Microsoft.WindowsAzure.Mobile.Service;  

3. Substitua a definição da classe pelo seguinte código:

	    public class Account : EntityData
	    {
	        public string Username { get; set; }
	        public byte[] Salt { get; set; }
	        public byte[] SaltedAndHashedPassword { get; set; }
	    }
    
    Isso representa uma linha em uma nova tabela Conta, que contém o nome de usuário, valor de sal do usuário, e a senha armazenada com segurança.

2. Na pasta **Modelos** você encontrará uma classe **DbContext**, que leva o nome do seu serviço móvel. Abra seu contexto e adicione a tabela de contas em seu modelo de dados, incluindo o seguinte:

        public DbSet<Account> Accounts { get; set; }

	>[AZURE.NOTE]Os trechos de código neste tutorial usam `todoContext` como nome de contexto. Você deve atualizar os trechos de código para o contexto do projeto. &nbsp; Em seguida, você configurará as funções de segurança para trabalhar com esses dados.
 
5. Crie uma classe chamada `CustomLoginProviderUtils` e adicione a seguinte instrução `using`:

		using System.Security.Cryptography;

6. Adicione os seguintes métodos de código à nova classe:


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

	Isso permite gerar um novo valor de sal longo, adiciona a capacidade de criar hash em uma senha de sal e fornece uma maneira segura de comparar dois hashes.

## Criar o ponto de extremidade do registro

Nesse ponto, você tem todo o necessário para começar a criar contas do usuário. Nesta seção, você configurará um ponto de extremidade de registro para tratar das novas solicitações de registro. Aqui você poderá reforçar as novas políticas de nome de usuário e senha e assegurar que o nome de usuário não seja roubado. Em seguida, você armazenará com segurança as informações do usuário em seu banco de dados.

1. Crie a seguinte nova classe para representar uma tentativa de registro de entrada:

        public class RegistrationRequest
        {
            public String username { get; set; }
            public String password { get; set; }
        }

    Se você precisar coletar e armazenar outras informações durante o registro, deve fazer isso aqui.

2. No projeto de back-end do serviço móvel, clique com o botão direito em **Controladores**, clique em **Adicionar** e em **Controlador**, crie um novo **Controlador Personalizado dos Serviços Móveis do Microsoft Azure** chamado `CustomRegistrationController` e, em seguida, adicione as seguintes instruções `using`:

		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using System.Text.RegularExpressions;
		using <my_project_namespace>.DataObjects;
		using <my_project_namespace>.Models;

	No código acima, substitua o espaço reservado com o namespace do seu projeto.
 
4. Substitua a definição da classe pelo seguinte código:

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
	                return this.Request.CreateResponse(HttpStatusCode.BadRequest, "That username already exists.");
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

    Lembre-se de substituir a variável *todoContext* com o nome de **DbContext** do seu projeto. Observe que este controlador usa o seguinte atributo para permitir tráfego para este ponto de extremidade:

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]

>[AZURE.IMPORTANT]Este ponto de extremidade de registro pode ser acessado por qualquer cliente através de HTTP. Antes de publicar este serviço em um ambiente de produção, você deverá implementar algum tipo de esquema para validar registros, como um SMS ou a verificação baseada em email. Isso pode ajudar a impedir que um usuário mal-intencionado crie registros fraudulentos.

## Criar o LoginProvider

Uma das construções fundamentais no pipeline de autenticação dos Serviços Móveis é o **LoginProvider**. Nesta seção, você criará seu próprio `CustomLoginProvider`. Ele não será conectado ao pipeline como os provedores integrados, mas fornecerá a você uma funcionalidade conveniente.

1. Crie uma nova classe, `CustomLoginProvider`, que é derivada de **LoginProvider**, e adicione as seguintes instruções `using`:

	    using Microsoft.WindowsAzure.Mobile.Service;
		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using Newtonsoft.Json.Linq;
		using Owin;
		using System.Security.Claims;
 
3. substitua a definição da classe **CustomLoginProvider** pelo seguinte código:

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

       Se você tentar compilar o projeto agora, a compilação falhará. O `LoginProvider` tem três métodos abstratos que precisam ser implementados, o que você fará mais tarde.

2. Crie uma nova classe denominada `CustomLoginProviderCredentials` no mesmo arquivo de código.

        public class CustomLoginProviderCredentials : ProviderCredentials
        {
            public CustomLoginProviderCredentials()
                : base(CustomLoginProvider.ProviderName)
            {
            }
        }

	Isso representa informações sobre o seu usuário e ficará disponível para você no back-end através de [GetIdentitiesAsync](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.security.serviceuser.getidentitiesasync.aspx). Se estiver adicionando declarações personalizadas, certifique-se de que elas sejam capturadas nesse objeto.

3. Adicione a seguinte implementação do método abstrato `ConfigureMiddleware` a **CustomLoginProvider**.

        public override void ConfigureMiddleware(IAppBuilder appBuilder, ServiceSettingsDictionary settings)
        {
            // Not Applicable - used for federated identity flows
            return;
        }

	Este método não é implementado, pois **CustomLoginProvider** não é a integração com o pipeline de autenticação.

4. Adicione a seguinte implementação do método abstrato `ParseCredentials` a **CustomLoginProvider**.

        public override ProviderCredentials ParseCredentials(JObject serialized)
        {
            if (serialized == null)
            {
                throw new ArgumentNullException("serialized");
            }

            return serialized.ToObject<CustomLoginProviderCredentials>();
        }

	Esse método permitirá que o back-end desserialize as informações do usuário vindas do token de autenticação de entrada.

5. Adicione a seguinte implementação do método abstrato `CreateCredentials` a **CustomLoginProvider**.

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

	Este método converte um [ClaimsIdentity] em um objeto [ProviderCredentials] que é usado na fase de emissão do token de autenticação. Novamente, você pode desejar capturar declarações adicionais neste método.
	
6. Abra o arquivo de projeto WebApiConfig.cs na pasta App_Start e a seguinte linha de código após a **ConfigOptions** é criada:
		
		options.LoginProviders.Add(typeof(CustomLoginProvider));

	

## Criar o ponto de extremidade de entrada

Em seguida, você pode criar um ponto de extremidade de entrada para os seus usuários. O nome de usuário e a senha recebidos serão verificados em relação ao banco de dados aplicando primeiro o valor de sal do usuário, criando um hash na senha e garantindo que o valor recebido corresponde ao armazenado no banco de dados. Se corresponder, você poderá criar um [ClaimsIdentity] e transmiti-lo ao **CustomLoginProvider**. O aplicativo cliente receberá uma ID de usuário e um token de autenticação para obter mais acesso ao seu serviço móvel.

1. No projeto de back-end do serviço móvel, crie a seguinte nova classe `LoginRequest`:

        public class LoginRequest
        {
            public String username { get; set; }
            public String password { get; set; }
        }

	Esta classe representa uma tentativa de sinal de entrada.

2. Crie a seguinte nova classe `CustomLoginResult`:

	    public class CustomLoginResult
	    {
	        public string UserId { get; set; }
	        public string MobileServiceAuthenticationToken { get; set; }
	
	    }

	Esta classe representa um logon com êxito com a ID de usuário e o token de autenticação. Observe que esta classe tem a mesma forma que a classe MobileServiceUser no cliente, o que facilita a distribuir resposta de logon em um cliente com linguagem de tipagem forte.

2. Clique com o botão direito em **Controladores**, clique em **Adicionar** e em **Controlador**, crie um novo **Controlador Personalizado dos Serviços Móveis do Microsoft Azure** chamado `CustomLoginController` e, em seguida, adicione as seguintes instruções `using`:

		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using System.Security.Claims;
		using <my_project_namespace>.DataObjects;
		using <my_project_namespace>.Models;

3. Substitua a definição da classe **CustomLoginController** pelo seguinte código:
 
	    [AuthorizeLevel(AuthorizationLevel.Anonymous)]
	    public class CustomLoginController : ApiController
	    {
	        public ApiServices Services { get; set; }
	        public IServiceTokenHandler handler { get; set; }
	
	        // POST api/CustomLogin
	        public HttpResponseMessage Post(LoginRequest loginRequest)
	        {
	            todoContext context = new todoContext();
	            Account account = context.Accounts
	                .Where(a => a.Username == loginRequest.username).SingleOrDefault();
	            if (account != null)
	            {
	                byte[] incoming = CustomLoginProviderUtils
	                    .hash(loginRequest.password, account.Salt);
	
	                if (CustomLoginProviderUtils.slowEquals(incoming, account.SaltedAndHashedPassword))
	                {
	                    ClaimsIdentity claimsIdentity = new ClaimsIdentity();
	                    claimsIdentity.AddClaim(new Claim(ClaimTypes.NameIdentifier, loginRequest.username));
	                    LoginResult loginResult = new CustomLoginProvider(handler)
	                        .CreateLoginResult(claimsIdentity, Services.Settings.MasterKey);
	                    var customLoginResult = new CustomLoginResult()
	                    {
	                        UserId = loginResult.User.UserId,
	                        MobileServiceAuthenticationToken = loginResult.AuthenticationToken
	                    };
	                    return this.Request.CreateResponse(HttpStatusCode.OK, customLoginResult);
	                }
	            }
	            return this.Request.CreateResponse(HttpStatusCode.Unauthorized,
	                "Invalid username or password");
	        }
	    }

       Lembre-se de substituir a variável *todoContext* com o nome de **DbContext** do seu projeto. Observe que este controlador usa o seguinte atributo para permitir tráfego para este ponto de extremidade:

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]

>[AZURE.IMPORTANT]O seu `CustomLoginController` para uso na produção também deve conter uma estratégia de detecção de força bruta. Caso contrário, a sua solução de entrada poderá estar vulnerável a ataques.

## Configurar o serviço móvel para requerer autenticação

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]


## Testar o fluxo de entrada usando o cliente de teste

No seu aplicativo cliente, será necessário desenvolver uma tela de entrada personalizada que obtenha os nomes de usuário e senhas e os envie a uma carga JSON para seus pontos de extremidade de registro e entrada. Para concluir este tutorial, será necessário apenas usar o cliente de teste integrado para o back-end .NET dos Serviços Móveis.

1. No Visual Studio, clique com o botão direito no projeto do serviço móvel e, em seguida, clique em **Depurar** e em **Iniciar Nova Instância**.  

	Isso inicia uma nova instância de depuração do projeto de back-end do serviço móvel. Após o serviço ser iniciado com êxito, você verá uma página inicial afirmando que **Este serviço móvel está em execução**.

2. Na página de início do serviço, clique em **Experimentar** e, em seguida, digite a senha que você definiu para a configuração de aplicativo **MS_ApplicationKey** no arquivo web.config com um nome de usuário em branco na caixa de diálogo de autenticação.

3. Na página de ajuda, clique no ponto de extremidade **CustomRegistration** e, em seguida, clique em **Experimentar isso**.

    ![][2]

4. No corpo, substitua as cadeias de caracteres de exemplo pelo nome de usuário e senha que atendem aos critérios que você especificou anteriormente e clique em **Enviar**.

    ![][3]

	A resposta deve ser **201/Criado**.

5. Clique no botão Voltar do navegador e repita as etapas 2 e 3 para o ponto de extremidade **CustomLogin** usando o mesmo nome de usuário e senha que você registrou na etapa anterior.

    ![][4]

	Você deve receber a mensagem de resposta com um corpo que contém um objeto JSON **user** com *userId* e um *authenticationToken*, que é o token de autenticação dos Serviços Móveis gerado pela sua autenticação personalizada. Esse token é suficiente para conceder acesso de aplicativo de cliente ao ponto de extremidade TodoItem.

	Faça uma cópia do valor de *authenticationToken*. Você o usará para acessar o ponto de extremidade TodoItem restrito.

6. Clique no botão Voltar do navegador e, na página de documentação de API, clique em **GetTables** e em **Experimentar isso**.

7. Na caixa de diálogo de solicitação GET, clique no sinal de adição ao lado de **Cabeçalhos**, digite o valor `X-ZUMO-AUTH` na caixa à esquerda, cole o valor de *authenticationToken* copiado na caixa à direita e clique em **Enviar**.

 	![](./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-access-endpoint.png)

	O serviço móvel deve conceder acesso para ao ponto de extremidade e retornar um status **200/OK** juntamente com uma lista de TodoItems na tabela.

 	![](./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-access-success.png)

>[AZURE.IMPORTANT]Se você optar por publicar também esse projeto de serviço móvel no Azure para testes, lembre-se de que seus provedores de entrada e autenticação estarão vulneráveis a ataques. Certifique-se de que eles estão adequadamente protegidos ou de que os dados de teste que estão sendo protegidos não são importantes para você. Tome muito cuidado antes de usar um esquema de autenticação para proteger um serviço de produção.

## Entrar usando autenticação personalizada do cliente

Esta seção descreve as etapas necessárias para acessar os pontos de extremidade de autenticação personalizada do cliente para obter o token de autenticação necessário para acessar o serviço móvel. Como o código de cliente específico necessário depende do seu cliente, as orientações fornecidas aqui são independentes de plataforma.

>[AZURE.NOTE]As bibliotecas cliente dos Serviços Móveis comunicam com o serviço através de HTTPS. Como esta solução requer que você envie senhas como texto sem formatação, você deve garantir que usa HTTPS ao chamar esses pontos de extremidade usando solicitações REST diretas.

1. Crie os elementos de interface de usuário necessários no aplicativo cliente para permitir que os usuários insiram um nome de usuário e uma senha.

2. Use método **invokeApi** apropriado no **MobileServiceClient** na biblioteca de cliente para chamar o ponto de extremidade **CustomRegistration**, passando o nome de usuário fornecido pelo tempo de execução e a senha no corpo da mensagem.

	Só é necessário chamar o ponto de extremidade **CustomRegistration** uma vez para criar uma conta para um determinado usuário, desde que você mantenha as informações de logon do usuário na tabela Contas. Para obter exemplos de como chamar uma API personalizada em várias plataformas de cliente com suporte, consulte o artigo [API Personalizada nos Serviços Móveis do Azure – SDKs cliente](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx).
	 
	> [AZURE.IMPORTANT]Como esta etapa de provisionamento de usuário ocorre apenas uma vez, você deve considerar criar a conta de usuário usando um método diferente. Em um ponto de extremidade de registro público, você também deve considerar implementar um processo de verificação baseado em email ou SMS ou alguma outra proteção para evitar a geração de contas fraudulentas. Você pode usar o Twilio para enviar mensagens SMS a partir dos Serviços Móveis. Para saber mais, consulte [Como enviar uma mensagem SMS](partner-twilio-mobile-services-how-to-use-voice-sms.md#howto_send_sms). Você também pode usar o SendGrid para enviar emails a partir do Mobile Services. Para saber mais, consulte [Enviar email a partir dos Serviços Móveis com o SendGrid](store-sendgrid-mobile-services-send-email-scripts.md).
	
3. Use método **invokeApi** apropriado novamente, desta vez para chamar o ponto de extremidade **CustomRegistration**, passando o nome de usuário fornecido pelo tempo de execução e a senha no corpo da mensagem.

	Desta vez, você deve capturar os valores *userId* e *authenticationToken* retornados no objeto de resposta após um logon bem-sucedido.
	
4. Use os valores *userId* e *authenticationToken* retornados para criar um novo objeto **MobileServiceUser** e defina-o como o usuário atual da sua instância de **MobileServiceClient**, conforme mostrado no tópico [Adicionar autenticação a um aplicativo existente](mobile-services-dotnet-backend-ios-get-started-users.md). Como o resultado de CustomLogin tem a mesma forma que o objeto **MobileServiceUser**, você deve ser capaz de fazer uma conversão direta do resultado.

Assim, concluímos este tutorial.


<!-- Anchors. -->


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-debug-start.png
[1]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-try-out.png
[2]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-test-client.png
[3]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-send-register.png
[4]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-login-result.png


<!-- URLs. -->
[Adicionar autenticação ao seu aplicativo]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[Introdução aos Serviços Móveis]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md

[ClaimsIdentity]: https://msdn.microsoft.com/library/system.security.claims.claimsidentity(v=vs.110).aspx
[ProviderCredentials]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.security.providercredentials.aspx
 

<!---HONumber=58_postMigration-->