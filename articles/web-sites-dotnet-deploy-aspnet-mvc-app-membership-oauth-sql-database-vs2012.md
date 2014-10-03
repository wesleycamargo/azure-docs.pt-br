<properties linkid="dev-net-tutorials-web-app-with-sql-azure" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title=" OAuth" authors="riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="riande"></tags>

# Implantar um aplicativo ASP.NET MVC seguro com associação, OAuth e Banco de Dados SQL em um site do Azure

***Atualizado em 15 de outubro de 2013.***

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/develop/net/tutorials/web-site-with-sql-database/" title="Visual Studio 2013">Visual Studio 2013</a><a href="/en-us/develop/net/tutorials/web-site-with-sql-database-vs2012/" title="Visual Studio 2012" class="current">Visual Studio 2012</a></div>

<div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong><p>Uma <a href="/en-us/develop/net/tutorials/web-site-with-sql-database/">vers&atilde;o mais recente deste tutorial</a> est&aacute; dispon&iacute;vel. Voc&ecirc; ainda pode seguir esta vers&atilde;o se desejar usar o Visual Studio 2012, mas a vers&atilde;o mais recente &eacute; significativamente mais f&aacute;cil de seguir.</p></div>

Este tutorial mostra como criar um aplicativo da web ASP.NET MVC 4 seguro que permite que os usuários façam logon com credenciais do Google, Facebook e Yahoo. Você também implantará o aplicativo no Azure.

É possível abrir uma conta do Azure gratuitamente e, se você ainda não tiver o Visual Studio 2012, o SDK instalará automaticamente o Visual Studio 2012 para o Web Express. É possível iniciar o desenvolvimento para o Azure de maneira gratuita.

Este tutorial pressupõe que você não tem nenhuma experiência anterior com o Azure. Ao concluir este tutorial, você terá um aplicativo da Web controlado por dados seguro e em funcionamento na nuvem usando um banco de dados em nuvem.

Você aprenderá a:

-   Como habilitar seu computador para desenvolvimento do Azure ao instalar o SDK do Azure.
-   Como criar um projeto ASP.NET MVC 4 seguro e publicá-lo em um site do Azure.
-   Como usar OAuth e o banco de dados de associação do ASP.NET para proteger seu aplicativo.
-   Como implantar um banco de dados de associação no Azure.
-   Como usar um banco de dados SQL para armazenar dados no Azure.
-   Como usar o Visual Studio para atualizar e gerenciar o banco de dados de associação.

Você criará um aplicativo Web de lista de contatos simples criado no ASP.NET MVC 4 e que usa o ADO.NET Entity Framework para acesso ao banco de dados. A seguinte ilustração mostra a página de logon do aplicativo concluído:

![página de logon][]

[WACOM.INCLUDE [create-account-and-websites-note][]]

Neste tutorial:

-   [Configurar o ambiente de desenvolvimento][]
-   [Configurar o ambiente do Azure][]
-   [Criar um aplicativo ASP.NET MVC 4][]
-   [Implantar o aplicativo no Azure][]
-   [Adicionar um banco de dados ao aplicativo][]
-   [Adicionar um provedor de OAuth][]
-   [Adicionar funções ao banco de dados de associação][]
-   [Criar um Script de implantação de dados][]
-   [Implantar o aplicativo no Azure][1]
-   [Atualizar o banco de dados de associação][]
-   [Próximas etapas][]

## <a name="bkmk_setupdevenv"></a>Configurar o ambiente de desenvolvimento

Para começar, defina o ambiente de desenvolvimento instalando o SDK do Azure para o .NET Framework.

1.  Para instalar o SDK do Azure para .NET, clique no link abaixo. Se você ainda não tiver o Visual Studio 2012 instalado, ele será instalado pelo link. Este tutorial exige o Visual Studio 2012.
    [SDK do Azure para o Visual Studio 2012][]
2.  Quando for solicitado que você execute ou salve o executável da instalação, clique em **Executar**.
3.  Na janela do Web Platform Installer, clique em **Instalar** e prossiga com a instalação.

![Web Platform Installer - SDK do Azure para .NET][]

Quando a instalação for concluída, você terá todo o necessário para iniciar o desenvolvimento.

## <a name="bkmk_setupwindowsazure"></a>Configurar o ambiente do Azure

Em seguida, configure o ambiente do Azure criando um site do Azure e um banco de dados SQL.

### Criar um site e um banco de dados SQL no Azure

O seu site do Azure será executado em um ambiente de hospedagem compartilhado, o que significa que ele é executado em máquinas virtuais (VMs) compartilhadas com outros clientes do Azure. Um ambiente de hospedagem compartilhado é uma maneira de começar na nuvem a um baixo custo. Mais tarde, se seu tráfego da Web aumentar, o aplicativo poderá ser dimensionado para atender à necessidade executando em VMs dedicadas. Se precisar de uma arquitetura mais complexa, você poderá migrar para um Serviço de Nuvem do Azure. Os serviços de nuvem são executados em VMs dedicadas que podem ser configuradas de acordo com suas necessidades.

O Banco de dados SQL no Azure é um serviço de banco de dados relacional baseado em que é fundamentado em tecnologias do SQL Server. As ferramentas e os aplicativos que funcionam com o SQL Server também funcionam com o Banco de dados SQL.

1.  No [Portal de Gerenciamento do Azure][], clique em **Sites** na guia esquerda, em seguida, clique em **Novo**.
![Novo botão no Portal de Gerenciamento][]

2.  Clique em **CRIAÇÃO PERSONALIZADA**.
    ![Criar com link de banco de dados no Portal de Gerenciamento][]
O assitente **Novo site - Criação personalizada** é aberta.
3.  Na etapa **Criar Site** do assistente, insira uma sequência de caracteres na caixa **URL** a ser usada como a URL exclusiva de seu aplicativo. A URL completa consistirá no que você digitar aqui mais o sufixo que você vê ao lado da caixa de texto. A ilustração mostra "contactmgr22", mas essa URL provavelmente já é utilizada e, portanto, você precisará escolher outra.

    ![Criar com link de banco de dados no Portal de Gerenciamento][2]

2.  Na lista suspensa **Banco de Dados**, escolha **Criar um novo banco de dados SQL**.

3.  Na lista suspensa **Região**, escolha a mesma região selecionada para o site. Essa configuração especifica qual centro de dados sua máquina virtual será executada em. No **NOME DA CADEIA DE CONEXÃO DO BANCO DE DADOS**, digite *connectionString1*.
	
    ![Etapa Criar um novo site de Novo Site - Criar com o assistente de banco de dados][2]

4.  Clique na seta que aponta para a direita na parte inferior da caixa. O assistente avança para a etapa **Configurações do Banco de Dados**.

5.  Na caixa **Nome**, digite *ContactDB*.

6.  Na caixa **Servidor**, selecione **Novo Servidor do Banco de Dados SQL**. Como alternativa, se tiver criado um banco de dados SQL Server, você poderá selecionar esse SQL Server no controle suspenso.

7.  Insira o **NOME DE LOGON** e a **SENHA** de um administrador. Se selecionou **Novo servidor do Banco de Dados SQL**, você não irá digitar um nome e uma senha existentes aqui; digite um novo nome e uma senha que você está definindo agora para usar mais tarde ao acessar o banco de dados. Se você tiver selecionado um SQL Server criado anteriormente, será solicitada a senha para o nome da conta do SQL Server anterior que você criou. Para este tutorial, não selecionamos a caixa **Avançado**. A caixa **Avançado** permite que você defina o tamanho do banco de dados (o padrão é 1 GB, mas você pode aumentar esse tamanho para 150 GB) e o agrupamento.

8.  Clique na marca de seleção na parte inferior da caixa para indicar que você concluiu.

    ![Etapa Configurações do banco de dados do Novo site - Criar com o assistente de banco de dados][]

    A imagem a seguir mostra o uso de um SQL Server e logon existentes.
    ![Etapa Configurações do Banco de Dados de um novo site - Assistente Criar com Banco de Dados][]

    O Portal de Gerenciamento do Azure retorna para a página Sites e a coluna **Status** mostra que o site está sendo criado. Depois de um tempo (normalmente menos de um minuto), a coluna **Status** mostra que o site foi criado com êxito. Na barra de navegação à esquerda, o número de sites presentes na sua conta é exibido próximo ao ícone **Sites**, enquanto o número de bancos de dados aparece ao lado do ícone **Bancos de Dados SQL**.

## <a name="bkmk_createmvc4app"></a>Criar um aplicativo ASP.NET MVC 4

Você criou um Site do Azure, mas ainda não há conteúdo nele. A próxima etapa é criar o projeto de aplicativo Web do Visual Studio que será publicado no Azure.

### Criar o projeto

1.  Inicie o Visual Studio 2012.
2.  No menu **Arquivo**, clique em **Novo Projeto**.
3.  Na caixa de diálogo **Novo Projeto**, expanda **C\#**, selecione **Web** em **Modelos instalados** e, em seguida, selecione **Aplicativo Web ASP.NET MVC 4**. Mantenha o padrão **.NET Framework 4.5**. Nomeie o aplicativo **ContactManager** e clique em **OK**.

    ![Caixa de diálogo Novo Projeto][]

4.  Na caixa de diálogo **Novo Projeto ASP.NET MVC 4**, selecione o modelo **Aplicativo de Internet**. Mantenha o **Mecanismo de Exibição** do Razor padrão e clique em **OK**.

    ![Caixa de diálogo Novo Projeto ASP.NET MVC 4][]

### Configurar o cabeçalho e o rodapé da página

1.  No **Gerenciador de Soluções**, expanda a pasta Views\\Shared e abra o arquivo \*\_Layout.cshtml\*.

    ![\_Layout.cshtml no Gerenciador de Soluções][]

2.  Substitua cada ocorrência do "Meu Aplicativo ASP.NET MVC" por "Gerenciador de Contatos".
3.  Substitua o "seu logotipo aqui" por "Demonstração de CM".

### Executar o aplicativo localmente

1.  Pressione CTRL+F5 para executar o aplicativo. A página inicial do aplicativo aparece no navegador padrão.
    ![To Do List home page][]

Isso é tudo o que você precisa fazer por enquanto para criar o aplicativo que você implantará no Azure. Posteriormente, você adicionará a funcionalidade do banco de dados.

## <a name="bkmk_deploytowindowsazure1"></a> Implantar o aplicativo no Azure

1.  Em seu navegador, abra o [de Gerenciamento do Azure][].

2.  Na guia **Sites**, clique no nome do site criado anteriormente.

    ![Entre em contato com o aplicativo de gerenciamento na guia Sites do Portal de Gerenciamento][]

3.  No lado direito da janela, clique em **Baixar perfil de publicação**.

    ![Guia de início rápido e o botão Baixar perfil de publicação][]

    Esta etapa baixa um arquivo que contém todas as configurações necessárias para implantar um aplicativo em seu site. Você importará esse arquivo para o Visual Studio de forma que não precise inserir essas informações manualmente.

4.  Salve o arquivo .*publishsettings* em uma pasta passiva de acesso pelo Visual Studio.

    ![salvando o arquivo .publishsettings][]

    [WACOM.INCLUDE [publishsettingsfilewarningchunk][]]

5.  No Visual Studio, clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Publicar** no menu de contexto.

    ![Publicar no menu de contexto do projeto][]

    O assistente de **Publicar Web** é aberto.

6.  Na guia **Perfil** do assistente de **Publicar Web**, clique em **Importar**.

    ![Importar configurações de publicação][]

    A caixa de diálogo **Importar Perfil de Publicação** é aberta.

7.  Se você ainda não adicionou a assinatura do Azure no Visual Studio, execute as etapas a seguir. Nestas etapas, adicione a sua assinatura para que a lista suspensa em **Importar de um site do Azure** inclua o seu site.

    a. Na caixa de diálogo **Perfil de Publicação de Importação**, clique em **Adicionar Assinatura do Azure**.

    ![adicionar ass do win az][]

    b. Na caixa de diálogo **Importar Assinaturas do Azure**, clique em **Baixar arquivo de assinatura**.

    ![baixar sub][]

    c. Na janela do navegador, salve o arquivo *.publishsettings*.

    ![baixar o arquivo pub][]

    > [WACOM.NOTE]
    > O arquivo .publishsettings contém suas credenciais (sem codificação) que são usadas para administrar suas assinaturas e serviços do Azure. A melhor prática de segurança para este arquivo é armazená-lo temporariamente fora dos diretórios de origem (por exemplo, na pasta Libraries\\Documents) e, em seguida, excluí-la após a conclusão da importação. Um usuário mal-intencionado que obtenha acesso ao arquivo .publishsettings pode editar, criar e excluir os serviços do Azure.

    d. Na caixa de diálogo **Importar Assinaturas do Azure**, clique na guia **Procurar** e navegue para o arquivo *.publishsettings*.

    ![baixar sub][]

    e. Clique em **Importar**.

    ![import][]

8.  Na caixa de diálogo **Importar o perfil de publicação**, selecione **Importar de um site do Azure**, selecione seu site na lista suspensa e, em seguida, clique em **OK**.

    ![Importar Perfil de Publicação][]

    O aplicativo que você criou agora está em execução na nuvem. Na próxima vez que você implantar o aplicativo, somente os arquivos alterados (ou novos) serão implantados.

    ![A home page da lista de tarefas pendentes em execução no Azure][]

## <a name="bkmk_addadatabase"></a>Adicionar um banco de dados ao aplicativo

Em seguida, você atualizará o aplicativo MVC para adicionar a habilidade de exibir e atualizar contatos e armazenar os dados em um banco de dados. O aplicativo usará o Entity Framework para criar o banco de dados e ler e atualizar dados no banco de dados.

### Adicionar classes de modelo de dados aos contatos

Você começa criando um modelo de dados simples no código.

1.  No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta Modelos, clique em **Adicionar**e, em seguida, em **Classe**.
![Adicionar Classe no menu de contexto da pasta Modelos][]

2.  Na caixa de diálogo **Adicionar Novo Item**, nomeie o novo arquivo de classe *Contact.cs* e clique em **Adicionar**.
![Caixa de diálogo Adicionar Novo Item][]

3.  Substitua o conteúdo do arquivo Contacts.cs pelo código a seguir.

        using System.ComponentModel.DataAnnotations;
        using System.Globalization;
        namespace ContactManager.Models
        {
            public class Contact
            {
                public int ContactId { get; set; }
                public string Name { get; set; }
                public string Address { get; set; }
                public string City { get; set; }
                public string State { get; set; }
                public string Zip { get; set; }
                [DataType(DataType.EmailAddress)]
                public string Email { get; set; }
            }
        }

A classe **Contatos** define os dados que você armazenará para cada contato, além de uma chave primária, *ContactID*, exigida pelo banco de dados.

### Criar as páginas da web que permitem que os usuários do aplicativo trabalhem com os contatos

O recurso scaffolding do ASP.NET MVC pode gerar automaticamente o código que executa as ações CRUD (criar, ler, atualizar e excluir).

## <a name="bkmk_addcontroller"></a>Adicionar um controlador e uma visualização para os dados

1.  Compile o projeto **(Ctrl + Shift + B)**. (Você deve compilar o projeto antes de usar o mecanismo de scaffolding.)

2.  No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta Controladores e clique em **Adicionar** e, em seguida, em **Controlador**.

    ![Adicionar Controlador no menu de contexto da pasta Controladores][]

3.  Na caixa de diálogo **Adicionar Controlador**, digite "HomeController" como o nome do seu controlador.
4.  Defina o modelo **Opções de Scaffolding** para o**Controlador MVC com ações e visualizações de leitura/gravação, usando o Entity Framework**.

5.  Selecione **Contato** como sua classe de modelo e **\<Novo contexto de dados... \>** como sua classe de contexto de dados.

    ![Caixa de diálogo Adicionar Controlador][]

6.  Na caixa de diálogo **Novo contexto de dados**, aceite o valor padrão *ContactManager.Models.ContactManagerContext*.
    ![\*][]

7.  Clique em **OK** e, em seguida, clique em **Adicionar** na caixa de diálogo **Adicionar Controlador**.
8.  Na caixa de diálogo de substituição **Adicionar Controlador**, verifique se todas as opções estão marcadas e clique em **OK**.

    ![Adicionar a caixa de mensagem do controlador][]

O Visual Studio cria métodos do controlador e modos de exibição para as operações CRUD do banco de dados para objetos **Contact**.

## Habilitar migrações, criar o banco de dados, adicionar dados de exemplo e um inicializador de dados

A próxima tarefa é habilitar o recurso [Codificar primeiras migrações (a página pode estar em inglês)][] para criar o banco de dados com base no modelo de dados criado.

1.  No menu **Ferramentas**, selecione **Gerenciador de Pacotes da Biblioteca** e **Console do Gerenciador de Pacotes**.

    ![Console do Gerenciador de Pacotes no menu Ferramentas][]

2.  Na janela **Console do Gerenciador de Pacotes**, digite o seguinte comando:

        enable-migrations -ContextTypeName ContactManagerContext

    ![enable-migrations][]
    Deve-se especificar o nome do tipo de contexto (**ContactManagerContext**), pois o projeto contém duas classes[DbContext][] derivadas, a **ContactManagerContext** recém adicionada e a **UsersContext**, que é usada para o banco de dados de associação. A classe **ContactManagerContext** foi adicionada pelo Assistente de scaffolding do Visual Studio.

    O comando **enable-migrations** cria a pasta *Migrations* e coloca nessa pasta um arquivo *Configuration.cs* que pode ser editado para configurar Migrações.

3.  Na janela **Console do Gerenciador de Pacotes**, digite o seguinte comando:

        add-migration Initial

    O comando **add-migration Initial** gera um arquivo chamado **\<date\_stamp\>Initial** na pasta *Migrações* que cria o banco de dados. O primeiro parâmetro ( **Initial** ) é arbitrário e é usado para criar o nome do arquivo. Você pode ver os novos arquivos de classe no **Gerenciador de Soluções**.

    Na classe **Initial**, o método **Up** cria a tabela Contatos, e o método **Down** (usado quando você deseja retornar ao estado anterior) a descarta.

4.  Abra o arquivo *Migrations\\Configuration.cs*.
5.  Adicione os seguintes namespaces.

         using ContactManager.Models;

6.  Substitua o método *Seed* pelo seguinte código:

        protected override void Seed(ContactManager.Models.ContactManagerContext context)
        {
            context.Contacts.AddOrUpdate(p => p.Name,
               new Contact
               {
                   Name = "Debra Garcia",
                   Address = "1234 Main St",
                   City = "Redmond",
                   State = "WA",
                   Zip = "10999",
                   Email = "debra@example.com",
               },
                new Contact
                {
                    Name = "Thorsten Weinrich",
                    Address = "5678 1st Ave W",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "thorsten@example.com",
                },
                new Contact
                {
                    Name = "Yuhong Li",
                    Address = "9012 State st",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "yuhong@example.com",
                },
                new Contact
                {
                    Name = "Jon Orton",
                    Address = "3456 Maple St",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "jon@example.com",
                },
                new Contact
                {
                    Name = "Diliana Alexieva-Bosseva",
                    Address = "7890 2nd Ave E",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "diliana@example.com",
                }
                );
        }

    O código acima inicializa o banco de dados com as informações de contato. Para obter mais informações sobre a propagação do banco de dados, consulte [Propagando e depurando BDs do Entity Framework (EF) (a página pode estar em inglês)][].

7.  No **Console do Gerenciador de Pacotes**, digite o comando:

        update-database

    ![Comandos do Console do Gerenciador de Pacotes][]

    O **update-database** executa a primeira migração que cria o banco de dados. Por padrão, o banco de dados é criado como um banco de dados LocalDB do SQL Server Express. (A menos que você tenha o SQL Server Express instalado, em cujo caso, o banco de dados é criado usando a instância do SQL Server Express.)

8.  Pressione CTRL+F5 para executar o aplicativo.

O aplicativo mostra os dados de propagação e fornece links de edição, detalhes e exclusão.

![Exibição do MVC de dados][]

## <a name="addOauth"></a><span class="short-header">OAuth</span>Adicionar um provedor de OAuth

[OAuth][] é um protocolo aberto que permite autorização segura em um método simples e padrão da web, aplicativos móveis e de área de trabalho. O modelo de internet do ASP.NET MVC usa OAuth para expor o Facebook, Twitter, Google, Yahoo e a Microsoft como provedores de autenticação. Embora este tutorial use apenas o Facebook, Google e Yahoo como o provedores de autenticação, você pode facilmente modificar o código para usar qualquer um dos provedores. As etapas para implementar outros provedores são muito semelhantes às etapas que você verá neste tutorial.

Além da autenticação, o tutorial também usará funções para implementar a autorização. Somente os usuários que você adicionar à função canEdit serão capazes de criar, editar e excluir contatos.

## Registrar-se com um provedor externo

Para autenticar os usuários com credenciais de alguns provedores externos, deve-se registrar seu site com o provedor e obter uma chave e uma conexão de segredo. Google e Yahoo não exigem que você se registre e obtenha chaves.

Este tutorial não mostra todas as etapas que você deve executar para registrar esses provedores. As etapas normalmente não são difíceis. Para registrar o seu site com êxito, siga as instruções fornecidas nesses sites. Para começar a registrar o site, consulte o site do desenvolvedor para:

-   [Facebook][]
-   [Microsoft][]
-   [Twitter][]

Navegue até a página [][]<https://developers.facebook.com/apps></a> e efetue logon, se necessário. Clique no botão **Registrar-se como um desenvolvedor** e conclua o processo de registro. Depois que você concluir o registro, clique em **Criar novo aplicativo**. Insira um nome para o aplicativo. Você não precisa inserir um espaço para nome do aplicativo.

![Criar novo aplicativo do FB][]

Digite localhost para o **Domínio de aplicativo** e <http://localhost/> para a **URL do Site**. Clique em **Habilitado** para o **Modo Sandbox**, em seguida clique em **Salvar alterações**.

Será necessário o **ID do aplicativo** e o **Segredo do aplicativo** para a implementação do OAuth neste aplicativo.
 ![Novo aplicativo FB][]

## Criando os usuários de teste

No painel esquerdo em **Configurações**, clique em **Funções de desenvolvedor**. Clique no link **Criar** na linha **Usuários de teste** (não na linha de **Testadores**).

![Testadores do FB][]

Clique no link **Modificar** para obter o e-mail dos usuários de teste (que você usará para fazer logon no aplicativo). Clique no link **Ver mais**, depois clique em **Editar** para definir a senha de usuários de teste.

## Adicionar a id do aplicativo e o segredo do provedor

Abrir o arquivo *App\_Start\\AuthConfig.cs*. Remova os caracteres do comentário do método *RegisterFacebookClient* e adicione a id do aplicativo e o segredo do aplicativo. Use os valores que você adquiriu, os valores mostrados abaixo não funcionarão. Remova os caracteres do comentário da chamada *OAuthWebSecurity.RegisterGoogleClient* e adicione *OAuthWebSecurity.RegisterYahooClient* como mostrado abaixo. Os provedores Google e Yahoo não exigem registro nem obtenção de chaves
Aviso: Proteja o seu ID do aplicativo e o segredo. Um usuário mal-intencionado que tenha a sua ID de aplicativo e o segredo pode fingir ser seu aplicativo.

     public static void RegisterAuth()
        {
            OAuthWebSecurity.RegisterFacebookClient(
                appId: "enter numeric key here",
                appSecret: "enter numeric secret here");

            OAuthWebSecurity.RegisterGoogleClient();
            OAuthWebSecurity.RegisterYahooClient();
        }

1.  Execute o aplicativo e clique no link **logon**.
2.  Clique no botão **Facebook**.
3.  Digite as suas credenciais do Facebook ou uma das credenciais do usuário de teste.
4.  Clique em **Okay** para permitir que o aplicativo acesse os recursos do Facebook.
5.  Você é redirecionado para a página Registrar. Se você estiver conectado usando uma conta de teste, você pode alterar o **nome de usuário** para algo mais curto, por exemplo "teste do FB Bil". Clique no botão **Registrar** que salvará o alias do nome e e-mail do usuário no banco de dados de associação.
6.  Registrar outro usuário. Atualmente um erro no log do sistema impede você de fazer logoff e logon como outro usuário usando o mesmo provedor (ou seja, você não pode fazer logoff da sua conta do Facebook e fazer logon novamente com uma conta diferente do Facebook). Para contornar isso, navegue até o site usando um navegador diferente e registre outro usuário. Um usuário será adicionado à função de gerente e ter acesso ao aplicativo de edição, o outro usuário somente terá acesso a métodos sem edição no site. Os usuários anônimos só terão acesso à home page.
7.  Registrar outro usuário usando um provedor diferente.
8.  **Opcional**: Também é possível criar uma conta local não associada a um dos provedores. Mais tarde no tutorial, removeremos a capacidade de criar contas locais. Para criar uma conta local, clique no link **Sair** (se você está conectado), em seguida, clique no link **Registrar**. Você pode querer criar uma conta local para fins de administração que não esteja associada com algum provedor de autenticação externa.

## <a name="mbrDB"></a><span class="short-header">DB da associação</span>Adicione as funções ao Banco de dados da associação

Nesta seção você irá adicionar a função *canEdit* ao banco de dados de associação. Somente os usuários na função canEdit poderão editar os dados. A melhor prática é nomear as funções pelas ações que podem executar, portanto, *canEdit* é preferível do que uma função chamada *admin*. Quando seu aplicativo evoluir, você poderá adicionar novas funções, como *canDeleteMembers*, em vez da menos descritiva *superAdmin*.

1.  No menu **Exibir**, clique em **Gerenciador de Servidores**.

2.  Em **Gerenciador de Servidores**, expanda **DefaultConnection** e, em seguida, expanda **Tabelas**.

3.  Clique com o botão direito do mouse em **UserProfile** e clique em **Mostrar dados da tabela**.

    ![Mostrar dados da tabela][]

4.  Registre o **UserId** para o usuário que terá a função canEdit. Na imagem abaixo, o usuário *ricka* com **UserId** 2 terá a função canEdit para o site.

    ![IDs de usuário][]

5.  Clique com o botão direito do mouse em **webpages\_Roles** e clique em **Mostrar dados da tabela**.
6.  Digite **canEdit** na célula **RoleName**. O **RoleId** será 1 se esta for a primeira vez que você adicionou uma função. Registro do RoleID. Certifique-se de que não haja um caractere de espaço sobrando, "canEdit" na tabela de função não corresponderá a "canEdit" no código do controlador.

    ![roleID][]

7.  Clique com o botão direito do mouse em **webpages UsersInRoles** e clique em **Mostrar dados da tabela**. Digite o **UserId** para o usuário que você deseja conceder o acesso *canEdit* e o **RoleId**.

    ![usr role ID tbl][]

A tabela **webpages\_OAuthMembership** contém o provedor OAuth, o UserID do provedor e o UserID para cada usuário OAuth registrado. <!-- Don't replace "-" with "_" or it won't validate -->A tabela **webpages-Membership** contém a tabela de associação ASP.NET. Você pode adicionar usuários a esta tabela usando o link de registro. É uma boa ideia adicionar um usuário com a função *canEdit* que não está associado com o Facebook ou outro provedor de autorização de terceiros para que você possa ter sempre o acesso *canEdit*, mesmo quando o provedor de autorização de terceiros não estiver disponível. Posteriormente no tutorial, desativaremos o registro de associação do ASP.NET.

## Proteger o aplicativo com o atributo Authorize

Nesta seção o atributo [Authorize][] será aplicado para restringir o acesso aos métodos de ação. Os usuários anônimos poderão visualizar a home page apenas. Os usuários registrados poderão ver detalhes do contato, a cerca de e as páginas de contatos. Somente usuários na função *canEdit* poderão acessar os métodos de ação que alteram dados.

1.  Adicionar os filtros [Authorize][] e [RequireHttps][] ao aplicativo. Uma abordagem alternativa é adicionar os atributos [Authorize][] e [RequireHttps][] a cada controlador, mas aplicá-los a todo o aplicativo é considerado uma melhor prática de segurança. Adicionando-os globalmente, cada novo controlador e método de ação que você adicionar será automaticamente protegido, você não precisará se lembrar de aplicá-los. Para obter mais informações, consulte [Protegendo seu aplicativo ASP.NET MVC 4 e o novo atributo AllowAnonymous][]. Abra o arquivo *App\_Start\\FilterConfig.cs* e substitua o método *RegisterGlobalFilters* por este:

        public static void
        RegisterGlobalFilters(GlobalFilterCollection filters)
        {
            filters.Add(new HandleErrorAttribute());
            filters.Add(new System.Web.Mvc.AuthorizeAttribute());
            filters.Add(new RequireHttpsAttribute());
        }

2.  Adicione o atributo [AllowAnonymous][Protegendo seu aplicativo ASP.NET MVC 4 e o novo atributo AllowAnonymous] ao método **Index**. O atributo [AllowAnonymous][Protegendo seu aplicativo ASP.NET MVC 4 e o novo atributo AllowAnonymous] permite criar a lista branca dos métodos para os quais você deseja recusar autorização.
3.  Adicione [Authorize(Roles = "canEdit")] para os métodos Get e Post que alteram os dados (Create, Edit e Delete).

4.  Adicione os métodos *About* e *Contact*. Uma parte do código concluído é mostrada abaixo.

        public class HomeController : Controller
        {
            private ContactManagerContext db = new ContactManagerContext();
            [AllowAnonymous]
            public ActionResult Index()
            {
                return View(db.Contacts.ToList());
            }

            public ActionResult About()
            {
                return View();
            }

            public ActionResult Contact()
            {
                return View();
            }

            [Authorize(Roles = "canEdit")]
            public ActionResult Create()
            {
                return View();
            }
            // Methods moved and omitted for clarity.
        }

5.  Remova o registro de associação do ASP.NET. O registro de associação ao ASP.NET atual no projeto não oferece suporte para a redefinição de senha e não verifica se o registro é de uma pessoa (por exemplo, com uma [CAPTCHA][]). Quando um usuário é autenticado usando um dos provedores de terceiros, ele pode se registrar. No AccountController, remova *[AllowAnonymous]* de GET e POST dos métodos *Registrar*. Isso evitará que bots e usuários anônimos se registrem.
6.  Em *Views\\Shared\_LoginPartial.cshtml*, remova o link de ação Registrar.

7.  Habilite o SSL. No Gerenciador de Soluções, clique no projeto **ContactManager** e clique em F4 para exibir a caixa de diálogo propriedades. Altere **SSL habilitado** para true. Copie a **URL do SSL**.

    ![habilitar SSL][]

8.  No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **Gerenciador de Contatos** e clique em **Propriedades**.
9.  Na guia à esquerda, clique em **Web**.

10. Altere a **Url do projeto** para usar a **URL de SSL**.

11. Clique em **Criar o diretório virtual**.

    ![habilitar SSL][3]

12. Pressione CTRL+F5 para executar o aplicativo. O navegador exibirá um aviso de certificado. Em nosso aplicativo, você pode clicar com segurança no link **Continuar neste site**. Verifique se apenas os usuários na função *canEdit* podem alterar os dados. Verifique se os usuários anônimos só podem exibir a home page.

    ![av. de cert.][]

    ![av. de cert.][4]

Os sites do Azure incluem um certificado de segurança válido, portanto, você não verá esse aviso quando implantar no Azure.

## <a name="ppd"></a><span class="short-header">Preparar o DB</span>Crie um script de implantação de dados

</p>
O banco de dados de associação não é gerenciado pelo Entity Framework Code First, assim você não pode usar as migrações para implantá-lo. Usaremos o provedor [dbDacFx][] para implantar o esquema de banco de dados, e configuraremos o perfil de publicação para executar um script que irá inserir dados da associação inicial em tabelas de associação.

Este tutorial usará o SQL Server Management Studio (SSMS) para criar scripts de implantação de dados.

Instale o SSMS do [Centro de Download do Microsoft SQL Server 2012 Express][]:

-   [ENU\\x64\\SQLManagementStudio\_x64\_ENU.exe][] para sistemas de 64 bits.
-   [ENU\\x86\\SQLManagementStudio\_x86\_ENU.exe][] para sistemas de 32 bits.

Se você escolher o caminho errado para seu sistema, ele não conseguirá instalar e você pode tentar o outro.

(Observe que se trata de um download de 600 megabytes. Pode levar muito tempo para instalar e pode exigir a reinicialização do computador.)

Na primeira página do Centro de instalação do SQL Server, clique em **Instalação autônoma do novo SQL Server ou adicione recursos a uma instalação existente** e siga as instruções, aceitando as opções padrão. A imagem a seguir mostra a etapa que instala o SSMS.

![Instalar o SQL][]

### Crie o script do banco de dados de desenvolvimento

1.  Executar o SSMS.

2.  Na caixa de diálogo **Conectar-se ao servidor**, digite *\*(localdb)\\v11.0* como nome do Servidor, deixe a **Autenticação** configurada como **Autenticação do Windows** e, em seguida, clique em **Conectar**. Se você tiver instalado o SQL Express, digite **.\\SQLEXPRESS**.

    ![con to srvr dlg][]

3.  Na janela **Gerenciados do objeto**, expanda **Bancos de dados**, clique com o botão direito do mouse em **aspnet-ContactManager**, clique em **Tarefas** e, em seguida, clique em **Gerar scripts**.

    ![Ger. scripts][]

4.  Na caixa de diálogo **Gerar e publicar scripts**, clique em **Configurar as opções de script**.
    É possível ignorar a etapa **Escolher objetos**, pois o padrão é o banco de dados de script inteiro e todos os objetos do banco de dados, que é o que você deseja.

5.  Clique em **Avançado**.

    ![Configurar as opções de script][]

6.  Na caixa de diálogo **Opções de script avançadas**, role para baixo até **Tipos de dados para o script** e clique na opção **Somente dados** na lista suspensa. (Consulte a imagem abaixo da próxima etapa.)

7.  Altere **Script USE DATABASE** para **Falso**. As instruções de uso não são válidas para o banco de dados do SQL do Azure e não são necessárias para a implantação do SQL Server Express no ambiente de teste.

    ![Configurar as opções de script][5]

8.  Clique em **OK**.
9.  Na caixa de diálogo **Gerar e publicar scripts**, a caixa **Nome do arquivo** especifica o local em que o script será criado. Altere o caminho para a pasta de solução (a pasta que contém o arquivo *Contacts.sln*) e altere o nome de arquivo para *aspnet-data-membership.sql*.

10. Clique em **Avançar** para ir para a guia **Resumo** e, em seguida, clique em **Avançar** novamente para criar o script.

    ![Salvar ou publicar][]

11. Clique em **Concluir**.

## <a name="bkmk_deploytowindowsazure11"></a>Implantar o aplicativo no Azure

1.  Abra o arquivo da raiz do aplicativo *Web.config*. Encontre a marcação *DefaultConnection* e, em seguida, copie e cole-a na linha de marcação *DefaultConnection*. Renomeie o elemento copiado *DefaultConnectionDeploy*. Esta cadeia de conexão é necessária para implantar os dados do usuário no banco de dados de associação.
    ![3 cons str][]

2.  Crie o aplicativo.
3.  No Visual Studio, clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione **Publicar** no menu de contexto.

    ![Publicar no menu de contexto do projeto][6]

O assistente de **Publicar Web** é aberto.

1.  Clique na guia **Configurações**. Clique no ícone **v** para selecionar a **Cadeia de conexão remota** para o **ContactManagerContext** e **DefaultConnectionDeploy**. Os três bancos de dados listados irão usar a mesma sequência de conexão. O banco de dados **ContactManagerContext** armazena os contatos, o **DefaultConnectionDeploy** é usado somente para implantar os dados da conta do usuário no banco de dados de associação e o banco de dados **UsersContext** é o banco de dados de associação.

    ![configurações][]

2.  Em **ContactManagerContext**, verifique **Executar as migrações iniciais de código**.

    ![configurações][7]

3.  Em **DefaultConnectionDeploy**, verifique **Atualizar o banco de dados** e clique no link**Configurar as atualizações do banco de dados**.

4.  Clique no link **Adicionar o script SQL** e navegue para o arquivo *aspnet-data-membership.sql*. Você só precisa fazer isso uma vez. A próxima implantação você pode desmarcar a **Atualização do banco de dados**, pois você não precisa adicionar os dados do usuário nas tabelas de associação.

    ![adicionar sql][]

5.  Clique em **Publicar**.

6.  Navegue até a página [][]<https://developers.facebook.com/apps></a> e altere as configurações dos **Domínios do aplicativo** e da **URL do site** para a URL do Azure.

7.  Testar o aplicativo. Verifique se apenas os usuários na função *canEdit* podem alterar os dados. Verifique se os usuários anônimos só podem exibir a home page. Verifique se os usuários autenticados podem navegar até todos os links que não alteram os dados.

8.  Na próxima vez que você publicar o aplicativo, certifique-se de desmarcar a **Atualização do banco de dados** em **DefaultConnectionDeploy**.

    ![configurações][7]

## <a name="ppd2"></a><span class="short-header">Atualizar o DB</span>Atualize o banco de dados da associação

Depois que o site for implantado no Azure e você tiver mais usuários registrados, talvez você queira tornar algumas delas membros da função *canEdit*. Nesta seção, usaremos o Visual Studio para se conectar ao banco de dados SQL e adicionar usuários à função *canEdit*.

![configurações][7]

1.  No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e clique em **Publicar**.
    ![Publicar][]

2.  Clique na guia **Configurações**.
3.  Copie a cadeia de conexão. Por exemplo, a cadeia de conexão usada neste exemplo é:
    Data Source=tcp:d015leqjqx.database.windows.net,1433;
    Initial Catalog=ContactDB2;User
    <script type="text/javascript">
    <!--
    h='&#100;&#48;&#x31;&#x35;&#108;&#120;&#x79;&#122;&#x65;&#x3b;&#80;&#x61;&#x73;&#x73;&#x77;&#x6f;&#114;&#100;&#x3d;&#120;&#x79;&#122;&#120;&#x79;&#122;';a='&#64;';n='&#x49;&#100;&#x3d;&#114;&#x69;&#x63;&#x6b;&#x61;&#48;';e=n+a+h;
    document.write('<a h'+'ref'+'="ma'+'ilto'+':'+e+'" clas'+'s="em' + 'ail">'+e+'<\/'+'a'+'>');
    // -->
    </script>
    <noscript>
    Id=ricka0 at d015lxyze;Password=xyzxyz
    </noscript>

4.  Feche o diálogo de publicação.

5.  No menu **Exibir**, clique em **Gerenciador de Servidores**.

6.  Clique no ícone **Conectar-se ao banco de dados**.

    ![Publicar][8]

7.  Se for solicitada a Fonte de Dados, clique em **Microsoft SQL Server**.
    ![Publicar][9]

8.  Copie e cole o **Nome do Servidor**, que começa com \**tcp* (consulte a imagem abaixo).
9.  Clique em **Usar a autenticação do SQL Server**

10. Digite o seu **Nome do usuário** e **Senha**, que estão na cadeia de conexão copiada.

11. Digite o nome do banco de dados (ContactDB, ou a sequência após "Initial Catalog=" no banco de dados, se você não o nomeou ContactDB.) Se você receber uma caixa de diálogo de erro, consulte a próxima seção.

12. Clique em **Testar conexão**. Se você receber uma caixa de diálogo de erro, consulte a próxima seção.
    ![add con dlg][]

## Erro de logon - Não é possível abrir servidor

Se você receber uma caixa de diálogo de erro informando que "Não é possível abrir o servidor", será necessário adicionar o endereço IP para o IPs permitidos.

![erro do firewall][]

1.  No Portal do Azure, selecione **Bancos de dados SQL** na guia esquerda.

    ![Selecionar SQL][]

2.  Selecione o banco de dados que você deseja abrir.

3.  Clique no link **Configurar regras de firewall do Azure para esse endereço IP**.

    ![regras de firewall][]

4.  Quando você receber o prompt "O endereço IP atual xxx.xxx.xxx.xxx não está incluído nas regras de firewall existentes. Deseja atualizar as regras de firewall?", clique em **Sim**. Normalmente não é suficiente adicionar esse endereço, você precisará adicionar um intervalo de endereços IP.

## Adicionando um intervalo de endereços IP permitidos

1.  No Portal do Azure, clique em **Bancos de Dados SQL**.

2.  Clique no **Servidor** que hospeda seu banco de dados.

    ![servidor do bd][]

3.  Clique em **Configurar** na parte superior da página.
4.  Adicione um nome de regra e endereços IP iniciais e finais
    ![ip range][]

5.  Na parte inferior da página, clique em **Salvar**.
6.  Agora é possível editar o banco de dados de associação usando as etapas mostradas anteriormente.

## <a name="nextsteps"></a><span class="short-header">Próximas etapas</span>Próximas etapas

Este tutorial e o aplicativo de exemplo foram escritos por [Rick Anderson][] (Twitter [@RickAndMSFT][]) com a assistência de Tom Dykstra, Tom FitzMacken e Barry Dorrans (Twitter [@blowdart][]).

Deixe comentários sobre o que você gostou ou do que você gostaria de ver melhorado, não apenas sobre o próprio tutorial, mas também sobre os produtos que ele demonstra. Seus comentários nos ajudarão a priorizar melhorias. Estamos especialmente interessados em saber quanto há de interesse em mais automação para o processo de configuração e implantação de banco de dados de associação.

Para obter os botões de registro do Facebook, Google e Yahoo coloridos, consulte a postagem no blog [Personalizar botões de Login externos no ASP.NET MVC 4][]. Para obter informações sobre como usar a autenticação do Windows, consulte o seguinte:

-   [Autenticação do Azure][]
-   [Como criar um Site da Intranet usando ASP.NET MVC][]

Outra maneira de armazenar dados em um aplicativo do Azure é usar o Armazenamento do Azure, que fornece armazenamento de dados não relacionais na forma de blobs e tabelas. Os links a seguir fornecem mais informações sobre a ASP.NET MVC e o Windows Azure.

-   [Aplicativo de várias camadas .NET usando Tabelas de armazenamento, Filas e Blobs][].
-   [Introdução ao ASP.NET MVC 4][]
-   [Introdução ao Entity Framework usando MVC][]
-   [OAuth 2.0 e registro único][]

Você viu como implantar um aplicativo Web em um site do Azure. Para saber mais sobre como configurar, gerenciar e reduzir os sites do Azure, consulte os tópicos de instruções na página [Tarefas comuns][].

Para saber como depurar os sites do Azure, consulte [Solução de problemas dos sites do Azure no Visual Studio][].

Para saber como implantar um aplicativo em um Serviço de Nuvem do Azure, consulte [A versão do Serviço de Nuvem deste tutorial][] e [Desenvolvendo aplicativos web com o Azure][]. Entre algumas razões para optar por executar um aplicativo Web ASP.NET em um Serviço de Nuvem do Azure e não um site do Azure, estão as seguintes:

-   Você deseja permissões de administrador no servidor Web em que o aplicativo é executado.
-   Você deseja usar a Conexão de Área de Trabalho Remota para acessar o servidor Web em que o aplicativo é executado.
-   O aplicativo é multicamada e você deseja distribuir o trabalho entre vários servidores virtuais (web e trabalhadores).

Para obter mais informações sobre o Banco de dados SQL e Armazenamento do Azure, consulte [Opções de Armazenamento de Dados no Azure][].

Para saber mais sobre como usar o banco de dados SQL, consulte [Trabalhando com o banco de dados do SQL Azure do mapa de conteúdo de acesso de dados ASP.NET][].

Para saber mais sobre o Entity Framework e Codificar Migrações Iniciais, consulte os seguintes recursos:

-   [Introdução ao Entity Framework usando MVC][]
-   [Codificar Migrações Iniciais][]

<!-- bookmarks --> <!-- links --> <!-- links from Tom's hopefully no collisions --> <!-- images-->

  [Visual Studio 2013]: /en-us/develop/net/tutorials/web-site-with-sql-database/ "Visual Studio 2013"
  [Visual Studio 2012]: /en-us/develop/net/tutorials/web-site-with-sql-database-vs2012/ "Visual Studio 2012"
  [versão mais recente deste tutorial]: /en-us/develop/net/tutorials/web-site-with-sql-database/
  [página de logon]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxb.png
  [create-account-and-websites-note]: ../includes/create-account-and-websites-note.md
  [Configurar o ambiente de desenvolvimento]: #bkmk_setupdevenv
  [Configurar o ambiente do Azure]: #bkmk_setupwindowsazure
  [Criar um aplicativo ASP.NET MVC 4]: #bkmk_createmvc4app
  [Implantar o aplicativo no Azure]: #bkmk_deploytowindowsazure1
  [Adicionar um banco de dados ao aplicativo]: #bkmk_addadatabase
  [Adicionar um provedor de OAuth]: #addOauth
  [Adicionar funções ao banco de dados de associação]: #mbrDB
  [Criar um Script de implantação de dados]: #ppd
  [1]: #bkmk_deploytowindowsazure11
  [Atualizar o banco de dados de associação]: #ppd2
  [Próximas etapas]: #nextsteps
  [SDK do Azure para o Visual Studio 2012]: http://go.microsoft.com/fwlink/?LinkId=254364
  [Web Platform Installer - SDK do Azure para .NET]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/WebPIAzureSdk20NetVS12.png
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com
  [Novo botão no Portal de Gerenciamento]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxWSnew2.png
  [Criar com link de banco de dados no Portal de Gerenciamento]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxCreateWSwithDB.png
  [2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxCreateWSwithDB_2.png
  [Etapa Configurações do banco de dados do Novo site - Criar com o assistente de banco de dados]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-setup-azure-site-004.png
  [Etapa Configurações do Banco de Dados de um novo site - Assistente Criar com Banco de Dados]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxPrevDB.png
  [Caixa de diálogo Novo Projeto]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-createapp-002.png
  [Caixa de diálogo Novo Projeto ASP.NET MVC 4]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxb2.png
  [\_Layout.cshtml no Gerenciador de Soluções]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-createapp-004.png
  [To Do List home page]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxa.png
  [de Gerenciamento do Azure]: http://manage.windowsazure.com "Portal"
  [Entre em contato com o aplicativo de gerenciamento na guia Sites do Portal de Gerenciamento]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-setup-azure-site-006.png
  [Guia de início rápido e o botão Baixar perfil de publicação]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-deploy1-download-profile.png
  [salvando o arquivo .publishsettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-deploy1-save-profile.png
  [publishsettingsfilewarningchunk]: ../includes/publishsettingsfilewarningchunk.md
  [Publicar no menu de contexto do projeto]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/PublishVSSolution.png
  [Importar configurações de publicação]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/ImportPublishSettings.png
  [adicionar ass do win az]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzAddWAsub.png
  [baixar sub]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzDownLoad.png
  [baixar o arquivo pub]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzDown2.png
  [import]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzImp.png
  [Importar Perfil de Publicação]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/ImportPublishProfile.png
  [A home page da lista de tarefas pendentes em execução no Azure]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/newapp005.png
  [Adicionar Classe no menu de contexto da pasta Modelos]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-adddatabase-001.png
  [Caixa de diálogo Adicionar Novo Item]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-adddatabase-002.png
  [Adicionar Controlador no menu de contexto da pasta Controladores]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-controller-add-context-menu.png
  [Caixa de diálogo Adicionar Controlador]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-controller-add-controller-dialog.png
  [\*]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxNewCtx.png
  [Adicionar a caixa de mensagem do controlador]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxOverwrite.png
  [Codificar primeiras migrações (a página pode estar em inglês)]: http://msdn.microsoft.com/library/hh770484.aspx
  [Console do Gerenciador de Pacotes no menu Ferramentas]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-migrations-package-manager-menu.png
  [enable-migrations]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxE.png
  [DbContext]: http://msdn.microsoft.com/en-us/library/system.data.entity.dbcontext(v=VS.103).aspx
  [Propagando e depurando BDs do Entity Framework (EF) (a página pode estar em inglês)]: http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx
  [Comandos do Console do Gerenciador de Pacotes]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-migrations-package-manager-console.png
  [Exibição do MVC de dados]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx2.png
  [OAuth]: http://oauth.net/ "http://oauth.net"
  [Facebook]: http://developers.facebook.com/
  [Microsoft]: http://go.microsoft.com/fwlink/?LinkID=144070
  [Twitter]: http://dev.twitter.com/
  []: https://developers.facebook.com/apps/
  [Criar novo aplicativo do FB]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxFBapp.png
  [Novo aplicativo FB]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxFB.png
  [Testadores do FB]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxFBt.png
  [Mostrar dados da tabela]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSTD.png
  [IDs de usuário]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxUid.png
  [roleID]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxRoleID.png
  [usr role ID tbl]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxUR.png
  [Authorize]: http://msdn.microsoft.com/en-us/library/system.web.mvc.authorizeattribute(v=vs.100).aspx
  [RequireHttps]: http://msdn.microsoft.com/en-us/library/system.web.mvc.requirehttpsattribute(v=vs.108).aspx
  [Protegendo seu aplicativo ASP.NET MVC 4 e o novo atributo AllowAnonymous]: http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx
  [CAPTCHA]: http://www.asp.net/web-pages/tutorials/security/16-adding-security-and-membership
  [habilitar SSL]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSSL.png
  [3]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxS2.png
  [av. de cert.]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxNOT.png
  [4]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxNOT2.png
  [dbDacFx]: http://msdn.microsoft.com/en-us/library/dd394698.aspx
  [Centro de Download do Microsoft SQL Server 2012 Express]: http://www.microsoft.com/en-us/download/details.aspx?id=29062
  [ENU\\x64\\SQLManagementStudio\_x64\_ENU.exe]: http://download.microsoft.com/download/8/D/D/8DD7BDBA-CEF7-4D8E-8C16-D9F69527F909/ENU/x64/SQLManagementStudio_x64_ENU.exe
  [ENU\\x86\\SQLManagementStudio\_x86\_ENU.exe]: http://download.microsoft.com/download/8/D/D/8DD7BDBA-CEF7-4D8E-8C16-D9F69527F909/ENU/x86/SQLManagementStudio_x86_ENU.exe
  [Instalar o SQL]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSS.png
  [con to srvr dlg]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxC2S.png
  [Ger. scripts]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxGenScripts.png
  [Configurar as opções de script]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx11.png
  [5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxAdv.png
  [Salvar ou publicar]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx1.png
  [3 cons str]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxd.png
  [6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-deploy1-publish-001.png
  [configurações]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxD2.png
  [7]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSettings.png
  [adicionar sql]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxAddSQL2.png
  [Publicar]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxP.png
  [8]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxc.png
  [9]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx3.png
  [add con dlg]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx4.png
  [erro do firewall]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx5.png
  [Selecionar SQL]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx6.png
  [regras de firewall]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx7.png
  [servidor do bd]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx8.png
  [ip range]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx9.png
  [Rick Anderson]: http://blogs.msdn.com/b/rickandy/
  [@RickAndMSFT]: https://twitter.com/RickAndMSFT
  [@blowdart]: https://twitter.com/blowdart
  [Personalizar botões de Login externos no ASP.NET MVC 4]: http://www.beabigrockstar.com/customizing-external-login-buttons-in-asp-net-mvc-4/
  [Autenticação do Azure]: http://www.asp.net/vnext/overview/fall-2012-update/windows-azure-authentication
  [Como criar um Site da Intranet usando ASP.NET MVC]: http://msdn.microsoft.com/en-us/library/gg703322(v=vs.98).aspx
  [Aplicativo de várias camadas .NET usando Tabelas de armazenamento, Filas e Blobs]: http://www.windowsazure.com/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/
  [Introdução ao ASP.NET MVC 4]: http://www.asp.net/mvc/tutorials/mvc-4/getting-started-with-aspnet-mvc4/intro-to-aspnet-mvc-4
  [Introdução ao Entity Framework usando MVC]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application
  [OAuth 2.0 e registro único]: http://blogs.msdn.com/b/vbertocci/archive/2013/01/02/oauth-2-0-and-sign-in.aspx
  [Tarefas comuns]: http://www.windowsazure.com/en-us/develop/net/common-tasks/
  [Solução de problemas dos sites do Azure no Visual Studio]: /en-us/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/
  [A versão do Serviço de Nuvem deste tutorial]: http://www.windowsazure.com/en-us/develop/net/tutorials/cloud-service-with-sql-database/
  [Desenvolvendo aplicativos web com o Azure]: http://msdn.microsoft.com/en-us/library/Hh674484
  [Opções de Armazenamento de Dados no Azure]: http://social.technet.microsoft.com/wiki/contents/articles/data-storage-offerings-on-the-windows-azure-platform.aspx
  [Trabalhando com o banco de dados do SQL Azure do mapa de conteúdo de acesso de dados ASP.NET]: http://go.microsoft.com/fwlink/p/?LinkId=282414#ssdb
  [Codificar Migrações Iniciais]: http://msdn.microsoft.com/en-us/library/hh770484
