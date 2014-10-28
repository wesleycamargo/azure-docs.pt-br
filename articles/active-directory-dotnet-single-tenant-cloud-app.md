<properties urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Developing Single Tenant Applications with Azure Active Directory" authors="terrylan" solutions="" manager="terrylan" editor="" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="terrylan"></tags>

# Desenvolvendo aplicativos de locatário único com o Active Directory do Azure

## <a name="introduction"></a> Introdução

Este tutorial mostra como utilizar o Active Directory do Azure para autenticar usuários de diretório e ler seus dados no diretório. Você saberá como:

-   Provisionar o aplicativo Web em um locatário do cliente
-   Proteger o aplicativo usando o WS-Federation
-   Acessar o diretório usando a Graph API

### Pré-requisitos

Os seguintes pré-requisitos do ambiente de desenvolvimento são necessários para esta explicação passo a passo:

-   Visual Studio 2010 SP1
-   Microsoft .NET Framework 4.0
-   [ASP.NET MVC 3][ASP.NET MVC 3]
-   [Tempo de execução do Windows Identity Foundation 1.0][Tempo de execução do Windows Identity Foundation 1.0]
-   [SDK do Windows Identity Foundation 3.5][SDK do Windows Identity Foundation 3.5]
-   [WCF Data Services para OData V3][WCF Data Services para OData V3]
-   Serviços de Informações da Internet (IIS) 7.5 com SSL habilitado
-   Windows PowerShell
-   [Cmdlets do PowerShell do Office 365][Cmdlets do PowerShell do Office 365]

### Sumário

-   [Introdução][Introdução]
-   [Etapa 1: Criar um aplicativo ASP.NET MVC][Etapa 1: Criar um aplicativo ASP.NET MVC]
-   [Etapa 2: Provisionar o aplicativo em um locatário do diretório da empresa][Etapa 2: Provisionar o aplicativo em um locatário do diretório da empresa]
-   [Etapa 3: Proteger o aplicativo usando o WS-Federation para entrada de funcionários][Etapa 3: Proteger o aplicativo usando o WS-Federation para entrada de funcionários]
-   [Etapa 4: Ler os dados do diretório usando a Graph API][Etapa 4: Ler os dados do diretório usando a Graph API]
-   [Resumo][Resumo]

## <a name="createapp"></a>Etapa 1: Criar um aplicativo ASP.NET MVC

Esta etapa descreve como criar um aplicativo ASP.NET MVC simples que representará um recurso protegido. O acesso a esse recurso será concedido por meio de autenticação federada gerenciada pelo STS da empresa, que é descrito mais adiante no tutorial.

1.  Abra o Visual Studio como administrador.
2.  No menu **Arquivo**, clique em **Novo Projeto**.
3.  No menu modelo à esquerda, selecione **Web** e clique **Aplicativo Web ASP.NET MVC 3**. Nomeie o projeto *OrgIdFederationSample*.
4.  Na caixa de diálogo **Novo Projeto ASP.NET MVC 3**, selecione o modelo **Vazio**, e clique em **OK**.
5.  Depois que o Visual Studio gerar o novo projeto, clique com o botão direito na pasta **Controladores** no **Gerenciador de soluções**, clique em **Adicionar** e, em seguida, em **Controlador**.
6.  Na caixa de diálogo **Adicionar Controlador**, nomeie o novo controlador *HomeController.cs* e clique em **Adicionar**.
7.  O arquivo **HomeController.cs** será criado e aberto. No arquivo de código, clique com o botão direito do mouse no método ***Index()*** e clique em **Adicionar Modo de Exibição...**.
8.  Na caixa de diálogo **Adicionar Modo de Exibição**, clique em **OK**. Um arquivo **Index.cshtml** será criado em uma nova pasta chamada **Home**.
9.  Clique com o botão direito no seu projeto **OrgIdFederationSample** no **Gerenciador de Soluções** e clique em **Propriedades**.
10. Na janela Propriedades, clique em **Web** no menu à esquerda e selecione **Usar servidor Web do IIS Local**. Uma caixa de diálogo é exibida solicitando que você crie um diretório virtual para o projeto. Clique em **Sim** na caixa de diálogo.
11. Compile e execute o aplicativo. A página Index de seu controlador Home será exibida.

## <a name="provisionapp"></a>Etapa 2: Provisionar o aplicativo em um locatário do diretório da empresa

Esta etapa descreve como um administrador de um cliente do Azure Active Directory provisiona o aplicativo MVC em seu inquilino e configura o logon único. Após essa etapa ser realizada, os funcionários da empresa podem autenticar-se no aplicativo web usando suas contas do Office 365.

O processo de provisionamento começa com a criação de uma nova Entidade de Serviço para o aplicativo. As Entidades de Serviço são usadas pelo Active Directory do Azure para registrar e autenticar aplicativos no diretório.

1.  Se ainda não tiver feito isso, baixe e instale os cmdlets do Office 365 PowerShell.
2.  No menu **Iniciar**, execute o console do **Módulo do Microsoft Online Services para Windows PowerShell**. Esse console fornece um ambiente de linha de comando para configurar atributos sobre locatários do Office 365, como criar e modificar Entidades de Serviço.
3.  Para importar o módulo **MSOnlineExtended** requerido, digite o seguinte comando e pressione Enter:

        Import-Module MSOnlineExtended -Force

4.  Para conectar-se a seu diretório do Office 365, você precisa fornecer as credenciais de administrador da empresa. Digite o seguinte comando e pressione Enter e, em seguida, digite suas credenciais no prompt:

        Connect-MsolService

5.  Agora você criará uma nova Entidade de Serviço para o aplicativo. Digite o comando a seguir e pressione Enter:

        New-MsolServicePrincipal -ServicePrincipalNames @("OrgIdFederationSample/localhost") -DisplayName "Federation Sample Website" -Type Symmetric -Usage Verify -StartDate "12/01/2012" -EndDate "12/01/2013" 

    Essa etapa retornará informações semelhantes às seguintes:

        The following symmetric key was created as one was not supplied qY+Drf20Zz+A4t2w e3PebCopoCugO76My+JMVsqNBFc=
        DisplayName           : Federation Sample Website
        ServicePrincipalNames : {OrgIdFederationSample/localhost}
        ObjectId              : 59cab09a-3f5d-4e86-999c-2e69f682d90d
        AppPrincipalId        : 7829c758-2bef-43df-a685-717089474505
        TrustedForDelegation  : False
        AccountEnabled        : True
        KeyType               : Symmetric
        KeyId                 : f1735cbe-aa46-421b-8a1c-03b8f9bb3565
        StartDate             : 12/01/2012 08:00:00 a.m.
        EndDate               : 12/01/2013 08:00:00 a.m.
        Usage                 : Verify 

    <div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong><p>Voc&ecirc; deve salvar essa sa&iacute;da, principalmente a chave sim&eacute;trica gerada. Essa chave s&oacute; &eacute; revelada a voc&ecirc; durante a cria&ccedil;&atilde;o da Entidade de Servi&ccedil;o, e voc&ecirc; n&atilde;o pode recuper&aacute;-la no futuro. Os outros valores s&atilde;o necess&aacute;rios para usar a Graph API para ler e gravar informa&ccedil;&otilde;es no diret&oacute;rio.</p></div>

6.  A etapa final define a URL de resposta para o seu aplicativo. A URL de resposta é onde as respostas são enviadas após tentativas de autenticação. Digite os comandos a seguir e pressione enter:

        $replyUrl = New-MsolServicePrincipalAddresses -Address "https://localhost/OrgIdFederationSample" 

        Set-MsolServicePrincipal -AppPrincipalId "7829c758-2bef-43df-a685-717089474505" -Addresses $replyUrl 

O aplicativo da web agora foi provisionado no diretório e pode ser usado para logon único da web pelos funcionários da empresa.

## <a name="protectapp"></a>Etapa 3: Proteger o aplicativo usando o WS-Federation para entrada de funcionários

Esta etapa mostra como adicionar suporte para logon federado usando o Windows Identity Foundation (WIF). Você também adicionará uma página de logon e configurará a relação de confiança entre o aplicativo e o locatário do diretório.

1.  No Visual Studio, clique com o botão direito no projeto **OrgIdFederationSample** e selecione **"Adicionar referência STS..."**. Essa opção do menu de contexto foi adicionada quando você instalou o SDK do WIF.
2.  Na caixa de diálogo **Utilitário de Federação** em **URI do aplicativo**, você precisa fornecer o URL no formato a seguir:

        spn:<Your AppPrincipalId>@<Your Directory Domain>

    **spn** especifica que o URI é um nome de Entidade Principal, **Seu AppPrincpalId** é o valor de GUID do **AppPrincipalId** gerado quando você criou uma Entidade de Serviço e **Seu Domínio de Diretório** é o domínio \*.onmicrosoft.com do locatário do diretório. Para este aplicativo de exemplo, um valor do URI do aplicativo completo é especificado, conforme mostrado abaixo:

        spn:7829c758-2bef-43df-a685-717089474505@awesomecomputers.onmicrosoft.com

    Depois que você tiver inserido o URI do aplicativo, clique em **Avançar**.

3.  Uma caixa de diálogo de aviso será exibida informando que "O aplicativo não está hospedado em uma conexão https segura". Isso é causado porque o Utilitário de Federação não reconhece o formato **spn:**, mas o aplicativo ainda usará uma conexão https segura de qualquer maneira. Clique em **Sim** para continuar.

4.  Na próxima página do Utilitário de Federação, selecione **Usar um STS existente**, em seguida, em **Local do documento de metadados do WS-Federation do STS**, digite a URL para o documento de metadados do WS-Federation. Essa URL é especificada no seguinte formato:

        https://accounts.accesscontrol.windows.net/<Domain Name or Tenant ID>/FederationMetadata/2007-06/FederationMetadata.xml 

    Para este aplicativo, o local dos metadados do WS-Federation é especificado conforme mostrado abaixo:

        https://accounts.accesscontrol.windows.net/fabrikam.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml 

    Depois que você tiver inserido o local dos metadados, clique em **Avançar**.

5.  Na próxima página do Utilitário de Federação, selecione **Desabilitar a validação de cadeia de certificados** e clique em **Avançar**.

6.  Na próxima página do Utilitário de Federação, selecione **Sem criptografia** e clique em **Avançar**.

7.  A próxima página do Utilitário de Federação exibe as declarações fornecidas pelo STS. Examine as declarações e clique em **Avançar**.

8.  Em seguida, você configurará os Serviços de Informações da Internet (IIS) para oferecer suporte a SSL para seu ambiente de desenvolvimento. Para abrir o Gerenciador do IIS, você pode digitar *inetmgr* no prompt **Executar**.

9.  No Gerenciador IIS, expanda a pasta **Sites** no menu à esquerda, em seguida, clique em **Home page do Site Padrão**. No menu **Ações** à direita, clique em **Associações...**.

10. Na caixa de diálogo **Ligações do Site**, clique em **Adicionar**. Na caixa de diálogo **Adicionar Ligação do Site**, altere o menu suspenso **Tipo** para ***https*** e, em seguida, em **Certificado SSL**, selecione **Certificado de Desenvolvimento do IIS Express**. Clique em **OK**.

11. No Gerenciador IIS, clique em **Pools do Aplicativo** no menu à esquerda, selecione a entrada **ASP.NET v4.0** na lista e clique em **Configurações Avançadas** no menu **Ações** à direita.

12. Na caixa de diálogo **Configurações Avançadas**, defina a propriedade **Carregar Perfil do Usuário** como **true**. Clique em **OK**.

13. No Visual Studio, abra o arquivo **Web.config** no **Gerenciador de Soluções** na raiz do seu projeto.

14. No arquivo **Web.config**, localize a seção **wsFederation** e adicione um atributo **reply** com o mesmo valor que a variável **$replyUrl** especificada ao criar a Entidade de Serviço. Por exemplo:

        <wsFederation passiveRedirectEnabled="true" issuer="https://accounts.accesscontrol.windows.net/v2/wsfederation" realm="spn: 7829c758-2bef-43df-a685-717089474505" requireHttps="false" reply="https://localhost/OrgIdFederationSample" /> 

15. Adicione um nó **httpRuntime** na seção **system.web** com o atributo **requestValidationMode** definido como **2.0**. Por exemplo:

        <system.web>
            <httpRuntime requestValidationMode="2.0" /> 
            ...

    Salve o arquivo **Web.config**.

16. Agora que você habilitou a autenticação para o aplicativo web, a página **Index** deve ser modificada para apresentar as declarações de um usuário autenticado. Abra o arquivo **Index.cshtml** localizado na pasta **Home** da pasta **Views**.

17. Adicione o seguinte trecho de código ao arquivo **Index.cshtml** e o salve:

        <p>
            @if (User.Identity.IsAuthenticated)
            {
            <ul>
                @foreach (string claim in ((Microsoft.IdentityModel.Claims.IClaimsIdentity)this.User.Identity).Claims.Select(c => c.ClaimType + " : " + c.Value))
                {
                    <li>@claim</li>
                }
            </ul>
            }
        </p> 

18. Depois de salvar as alterações no arquivo **Index.cshtml**, pressione **F5** para executar o aplicativo. Você será redirecionado para a página do Provedor de Identidade do Office 365, onde você pode fazer logon usando suas credenciais de locatário do diretório. Por exemplo, <*john.doe@awesomecomputers.onmicrosoft.com*>.

19. Depois de entrar usando suas credenciais, você será redirecionado para a página Índice do seu controlador de Página Inicial, as declarações de sua conta serão exibidas. Isso demonstra uma autenticação bem-sucedida do usuário no aplicativo usando o logon único fornecido pelo Azure Active Directory.

## <a name="readdata"></a>Etapa 4: Ler os dados do diretório usando a Graph API

Esta etapa mostra como usar a Graph API para conectar-se a seu locatário do Active Directory do Azure e ler os dados. Para ajudar a começar com a Graph API, baixe o seguinte aplicativo ASP.NET: [exemplo de aplicativo com suporte para gravação para a Graph API][exemplo de aplicativo com suporte para gravação para a Graph API]. Esse aplicativo contém métodos auxiliares que facilitam a autenticação e as solicitações na Graph API.

Você também adicionará permissões para a Entidade de Serviço do aplicativo que você criou na Etapa 2. Para adicionar essas permissões, você precisará do valor de AppPrincipalId.

1.  Baixe e extraia o aplicativo de exemplo na pasta desejada.
2.  Para usar o código de exemplo, você deve fornecer permissões adicionais para a Entidade de Serviço. Essas permissões possibilitarão a leitura dos dados pela Entidade de Serviço usando a Graph API. No menu **Iniciar**, execute o console do **Módulo do Microsoft Online Services para Windows PowerShell**.
3.  Você atribuirá permissões de leitura à Entidade de Serviço adicionando-a à função Administrador de Suporte a Serviço. Para obter mais informações sobre como atribuir funções à Entidade de Serviço, consulte [Controle de Acesso baseado em função para Graph API][Controle de Acesso baseado em função para Graph API]. Digite o comando a seguir e pressione Enter:

        Add-MsolRoleMember -RoleMemberType "ServicePrincipal" -RoleName "Service Support Administrator" -RoleMemberObjectId $appPrincipal.ObjectId 

4.  A variável **$appPrincipal.ObjectId** é o objectId de sua Entidade de Serviço que pode ser obtida digitando o comando a seguir e pressionando Enter.

        Get-MsolServicePrincipal -AppPrincipalId 7829c758-2bef-43df-a685-717089474505 

    <div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong><p>O valor de **AppPrincipalId acima foi retornado quando voc&ecirc; criou a Entidade de Servi&ccedil;o na Etapa 2.</p></div>

5.  Após adicionar a função à sua Entidade de Serviço, inicie o Visual Studio e abra o arquivo **Web.config** raiz do aplicativo exemplo.

6.  Localizar a seção *\<configuration\>* do **Web.config**, e navegue até a seção *\<appSettings\>*. Altere os valores das chaves *TenantDomainName*, *AppPrincipalId* e *SymmetricKey* para os valores de sua Entidade de Serviço. Por exemplo:

        <appSettings> 
            ...
            <add key="TenantDomainName" value="awesomecomputers.onmicrosoft.com"/> 
            ...
            <add key="AppPrincipalId" value="7829c758-2bef-43df-a685-717089474505"/>     
            ...
            <add key="SymmetricKey" value="qY+Drf20Zz+A4t2w e3PebCopoCugO76My+JMVsqNBFc=" /> 
            ...
        </appSettings>

7.  Salve o arquivo **Web.config** e pressione **F5** para executar o aplicativo.

8.  O aplicativo de exemplo exibirá um menu para acessar todos os seus usuários, administradores de empresa e muito mais. Um clique em um dos links retornará as informações armazenadas em seu locatário usando a Graph API.

## <a name="summary"></a>Resumo

Este tutorial mostrou como criar e configurar um aplicativo de inquilino único que usa os recursos de logon único do Azure Active Directory. Além disso, você acessou os dados do diretório do locatário usando a Graph API. É recomendável explorar o aplicativo de exemplo para compreender como tirar proveito da Graph API em seu próprio aplicativo.

Para saber mais sobre a Graph API, [você pode ler mais sobre ela no MSDN][você pode ler mais sobre ela no MSDN]. Você também pode criar aplicativos de multilocatários para o Active Directory do Azure lendo o tutorial a seguir: [Desenvolvendo aplicativos de nuvem de multilocatários com o Active Directory do Azure][Desenvolvendo aplicativos de nuvem de multilocatários com o Active Directory do Azure].

  [ASP.NET MVC 3]: http://www.microsoft.com/pt-BR/download/details.aspx?id=4211
  [Tempo de execução do Windows Identity Foundation 1.0]: http://www.microsoft.com/pt-BR/download/details.aspx?id=17331
  [SDK do Windows Identity Foundation 3.5]: http://www.microsoft.com/pt-BR/download/details.aspx?id=4451
  [WCF Data Services para OData V3]: http://www.microsoft.com/download/en/details.aspx?id=29306
  [Cmdlets do PowerShell do Office 365]: http://onlinehelp.microsoft.com/pt-BR/office365-enterprises/ff652560.aspx
  [Introdução]: #introduction
  [Etapa 1: Criar um aplicativo ASP.NET MVC]: #createapp
  [Etapa 2: Provisionar o aplicativo em um locatário do diretório da empresa]: #provisionapp
  [Etapa 3: Proteger o aplicativo usando o WS-Federation para entrada de funcionários]: #protectapp
  [Etapa 4: Ler os dados do diretório usando a Graph API]: #readdata
  [Resumo]: #summary
  [exemplo de aplicativo com suporte para gravação para a Graph API]: http://code.msdn.microsoft.com/Write-Sample-App-for-79e55502
  [Controle de Acesso baseado em função para Graph API]: http://msdn.microsoft.com/pt-BR/library/hh974466.aspx
  [você pode ler mais sobre ela no MSDN]: http://msdn.microsoft.com/pt-BR/library/hh974476.aspx
  [Desenvolvendo aplicativos de nuvem de multilocatários com o Active Directory do Azure]: http://g.microsoftonline.com/0AX00en/121
