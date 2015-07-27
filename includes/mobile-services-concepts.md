## <a name="what-is"></a>O que são Serviços Móveis

Os Serviços Móveis do Azure são uma plataforma de desenvolvimento de aplicativos móveis altamente escalonável que permite adicionar funcionalidade aprimorada a seus aplicativos de dispositivos móveis usando o Azure.

Com os Serviços Móveis, você pode:

+ **Criar aplicativos nativos e de plataforma cruzada** - conecte aplicativos do iOS, Android, Windows, ou Xamarin ou Cordova (Phonegap) de plataforma cruzada a seu serviço móvel de back-end usando SDKs nativos.  
+ **Enviar notificações por push a seus usuários** - envie notificações por push aos usuários de seu aplicativo.
+ **Autenticar seus usuários** - tire proveito de provedores de identidade populares, como Facebook e Twitter, para autenticar os usuários de seu aplicativo.
+ **Armazenam dados na nuvem** - armazene dados de usuários em um Banco de Dados SQL (por padrão) ou em Mongo DB, DocumentDB, Tabelas do Azure ou Blobs do Azure. 
+ **Criar aplicativos prontos para offline com sincronização** - faça com que seus aplicativos funcionem offline e use os Serviços Móveis para sincronizar dados em segundo plano.
+ **Monitorar e dimensionar seus aplicativos** - monitorar o uso do aplicativo e dimensionar o back-end à medida que a demanda crescer. 

## <a name="concepts"> </a>Conceitos de Serviços de Mídia

A seguir, encontram-se importantes recursos e conceitos dos Serviços Móveis:

+ **Chave do aplicativo:** um valor exclusivo que é usado para limitar o acesso ao seu serviço móvel de clientes aleatórios; essa "chave" não é um token de segurança e não é usada para autenticar usuários de seu aplicativo.    
+ **Back-end:** a instância de serviço móvel que dá suporte a seu aplicativo. Um serviço móvel é implementado como um projeto de API Web do ASP.NET (*back-end .NET* ) ou um projeto do Node.js (* back-end JavaScript *).
+ **Provedor de identidade:** um serviço externo, confiável para os serviços móveis, que autentica os usuários de seu aplicativo. Os provedores com suporte incluem: Facebook, Twitter, Google, Conta da Microsoft e Active Directory do Azure. 
+ **Notificação por push:** mensagem iniciada pelo serviço que é enviada para um usuário ou dispositivo registrado, pelo uso de Hubs de Notificação do Azure.
+ **Escala:** a capacidade de adicionar, com um custo adicional, mais poder de processamento, desempenho e armazenamento, conforme seu aplicativo se torna mais popular.
+ **Trabalho agendado:** código personalizado que é executado em um cronograma predeterminado ou sob demanda.

<!---HONumber=July15_HO3-->