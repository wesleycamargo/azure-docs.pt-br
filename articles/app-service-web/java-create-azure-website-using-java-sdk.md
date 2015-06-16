<properties 
	pageTitle="Criar um aplicativo Web no Serviço de Aplicativo do Azure usando o SDK do Azure para Java" 
	description="Aprenda a criar um aplicativo Web no Serviço de Aplicativo do Azure programaticamente, usando o SDK do Azure para Java." 
	tags="azure-classic-portal"
	services="app-service\web" 
	documentationCenter="Java" 
	authors="donntrenton" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="04/24/2015" 
	ms.author="v-donntr"/>


# Criar um aplicativo Web no Serviço de Aplicativo do Azure usando o SDK do Azure para Java

<!-- Azure Active Directory workflow is not yet available on the Azure Portal -->

## Visão geral

Este passo a passo mostra como criar um SDK do Azure para um aplicativo Java que cria um aplicativo Web no [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) e, em seguida, implanta nele um aplicativo. Ele consiste em duas partes:

- A parte 1 demonstra como compilar um aplicativo Java que cria um aplicativo da Web.
- A parte 2 demonstra como criar um aplicativo JSP simples "Olá mundo" e então usar um cliente FTP para implantar o código no Serviço de Aplicativo.


## Pré-requisitos

### Instalações de software

O código do aplicativo AzureWebDemo neste artigo foi escrito usando o Java SDK do Azure 0.7.0, que pode ser instalado usando o [WebPI (Web Platform Installer)](http://go.microsoft.com/fwlink/?LinkID=252838). Além disso, certifique-se de usar a versão mais recente do [Kit de Ferramentas do Azure para Eclipse](https://msdn.microsoft.com/library/azure/hh690946.aspx). Depois de instalar o SDK, atualize as dependências em seu projeto do Eclipse executando **Atualizar Índice** em **Repositórios Maven**, então adicione novamente a versão mais recente de cada pacote na janela **Dependências**. Você pode verificar a versão do software instalado no Eclipse, clicando em **Ajuda > Detalhes da instalação**; você deve ter pelo menos as seguintes versões:

- Pacote para Bibliotecas do Microsoft Azure para Java 0.7.0.20150309
- Eclipse IDE para desenvolvedores de Java EE 4.4.2.20150219


### Criar e configurar recursos de nuvem no Azure

Antes de iniciar este procedimento, você precisa ter uma assinatura ativa do Azure e configurar um AD (Active Directory) padrão no Azure.


### Criar um AD (Active Directory) no Azure

Se você ainda não tem um AD (Active Directory) na sua assinatura do Azure, faça logon no [portal clássico do Azure](https://manage.windowsazure.com) com sua conta da Microsoft. Se você tiver várias assinaturas, clique em **Assinaturas** e selecione o diretório padrão para a assinatura que deseja usar para este projeto. Em seguida, clique em **Aplicar** para alternar para esse modo de exibição de assinatura.

1. Selecione **Active Directory** no menu à esquerda. **Clique em Novo > Diretório > Criação Personalizada**.

2. Em **Adicionar Diretório**, selecione **Criar Novo Diretório**.

3. Em **Nome**, insira um nome de diretório.

4. Em **Domínio**, insira um nome de domínio. Este é um nome de domínio básico que é incluído por padrão com seu diretório; ele tem a forma `<domain_name>.onmicrosoft.com`. Você pode nomeá-com base no nome do diretório ou outro nome de domínio que você possua. Posteriormente, você pode adicionar outro nome de domínio que sua organização já utilize.

5. Em **País ou região**, selecione sua localidade.

Para obter mais informações sobre o AD, consulte [O que é um diretório do AD do Azure?](http://technet.microsoft.com/library/jj573650.aspx).


### Criar um Certificado de Gerenciamento do Azure

O SDK do Azure para Java usa certificados de gerenciamento para autenticar com as assinaturas do Azure. Estes são certificados X.509 v3 que você usa para autenticar um aplicativo cliente que usa a API de gerenciamento de serviços para atuar em nome do proprietário da assinatura para gerenciar os recursos de assinatura.

O código neste procedimento usa um certificado autoassinado para autenticar com o Azure. Para este procedimento, você precisa criar um certificado e carregá-lo no [portal clássico do Azure](https://manage.windowsazure.com) com antecedência. Isso envolve as seguintes etapas:

- Gere um arquivo PFX que represente seu certificado de cliente e salve-o localmente.
- Gere um certificado de gerenciamento (arquivo CER) por meio do arquivo PFX.
- Carregue o arquivo CER em sua assinatura do Azure.
- Converta o arquivo PFX em JKS, já que o Java usa esse formato para autenticar usando certificados.
- Escreva um código de autenticação para o aplicativo, que se refira ao arquivo JKS local.

Quando você concluir este procedimento, o certificado CER residirá na sua assinatura do Azure e o certificado JKS residirá no disco local. Para obter informações sobre gerenciamento de certificados, consulte [Criar e Carregar um Certificado de Gerenciamento para o Azure](http://msdn.microsoft.com/library/azure/gg551722.aspx).


#### Criar um certificado

Para criar seu próprio certificado autoassinado, abra um console de comando em seu sistema operacional e execute os comandos a seguir.

> **Observação:** o computador em que você executar esse comando deve ter o JDK instalado. Além disso, o caminho para o keytool depende do local em que você instala o JDK. Para obter mais informações, consulte [Chave e Ferramenta de Gerenciamento de Certificado (keytool)](http://docs.oracle.com/javase/6/docs/technotes/tools/windows/keytool.html) nos documentos online do Java.

Para criar o arquivo .pfx:

    <java-install-dir>/bin/keytool -genkey -alias <keystore-id>
     -keystore <cert-store-dir>/<cert-file-name>.pfx -storepass <password>
     -validity 3650 -keyalg RSA -keysize 2048 -storetype pkcs12
     -dname "CN=Self Signed Certificate 20141118170652"

Para criar o arquivo .cer:

    <java-install-dir>/bin/keytool -export -alias <keystore-id>
     -storetype pkcs12 -keystore <cert-store-dir>/<cert-file-name>.pfx
     -storepass <password> -rfc -file <cert-store-dir>/<cert-file-name>.cer

onde:

- `<java-install-dir>` é o caminho para o diretório no qual você instalou o Java.
- `<keystore-id>` é o identificador de entrada de armazenamento de chaves (por exemplo, `AzureRemoteAccess`).
- `<cert-store-dir>` é o caminho para o diretório no qual você deseja armazenar certificados (por exemplo, `C:/Certificates`).
- `<cert-file-name>` é o nome do arquivo do certificado (por exemplo, `AzureWebDemoCert`).
- `<password>` é a senha que você escolher por proteger o certificado; ela deve ter pelo menos 6 caracteres. Embora isso não seja recomendado, você optar por não inserir nenhuma senha.
- `<dname>` é o Nome Diferenciado X.500 a ser associado com o alias, e é usado como os campos “emissor” e “assunto” no certificado autoassinado.

Para obter mais informações, consulte [Criar e carregar um certificado de gerenciamento para o Azure](http://msdn.microsoft.com/library/azure/gg551722.aspx).


#### Carregar o certificado

Para carregar um certificado autoassinado para o Azure, vá para a página **Configurações** no portal clássico, então clique na guia **Certificados de Gerenciamento**. Clique em **Carregar** na parte inferior da página e navegue até o local do arquivo CER que você criou.


#### Converter o arquivo PFX em JKS

No Prompt de comando do Windows (executando como administrador), vá até o diretório que contém os certificados e execute o comando a seguir, em que `<java-install-dir>` é o diretório em que você instalou o Java em seu computador:

    <java-install-dir>/bin/keytool.exe -importkeystore
     -srckeystore <cert-store-dir>/<cert-file-name>.pfx
     -destkeystore <cert-store-dir>/<cert-file-name>.jks
     -srcstoretype pkcs12 -deststoretype JKS

1. Quando solicitado, digite a senha de chave de armazenamento de destino; esta será a senha para o arquivo JKS.

2. Quando solicitado, digite a senha da chave de armazenamento de origem; esta é a senha que você especificou para o arquivo PFX.

As duas senhas não precisam ser iguais. Embora isso não seja recomendado, você optar por não inserir nenhuma senha.


## Compilar um aplicativo de criação de aplicativos Web

### Criar o espaço de trabalho do Eclipse e o projeto Maven

Nesta seção, você cria um espaço de trabalho e um projeto Maven para o aplicativo de criação de aplicativos Web, chamado AzureWebDemo.

1. Crie um novo projeto Maven. Clique em **Arquivo > Novo > Projeto Maven**. Em **Novo Projeto Maven**, selecione **Criar um Projeto Simples** e **Usar local padrão de espaço de trabalho**.

2. Na segunda página do **Novo Projeto Maven**, especifique o seguinte:

    - ID do Grupo: `com.<username>.azure.webdemo`
    - ID do Artefato: AzureWebDemo
    - Versão: 0.0.1-SNAPSHOT
    - Empacotamento: jar
    - Nome: AzureWebDemo

    Clique em **Concluir**.

3. Abra o arquivo pom.xml do novo projeto no Gerenciador de Projetos. Selecione a guia **Dependências**. Já que esse é um novo projeto, nenhum pacote está listado ainda.

4. Abra a exibição Repositórios Maven. **Clique em Janela > Mostrar Exibição > Outros > Maven > Repositórios Maven** e clique em **OK**. A exibição **Repositórios Maven** aparecerá na parte inferior do IDE.

5. Abra **repositórios globais**, clique com o botão direito do mouse no repositório **central** e selecione **Recompilar Índice**.

    ![][1]
    
    Esta etapa pode levar vários minutos dependendo da velocidade da sua conexão. Quando o índice for recompilado, você deverá ver os pacotes do Microsoft Azure no Repositório Maven **central**.

6. Em **Dependências**, clique em **Adicionar**. Em **Inserir ID do Grupo...** digite `azure-management`. Selecione os pacotes de gerenciamento base e gerenciamento de Aplicativos Web do Serviço de Aplicativo:

        com.microsoft.azure  azure-management
        com.microsoft.azure  azure-management-websites

    > **Observação:** se você estiver atualizando as dependências após o lançamento de uma nova versão, você precisa adicionar novamente cada uma das dependências nessa lista. Depois de você clicar em **Adicionar** e selecionar cada dependência, ela será exibida com o novo número de versão na lista de **Dependências**.

Clique em **OK**. Os pacotes do Azure aparecem na lista de **Dependências**.


### Escrever código Java para criar um aplicativo Web chamando o SDK do Azure

Em seguida, escreva o código que chama as APIs no SDK do Azure para Java para criar o aplicativo Web do Serviço de Aplicativo.

1. Crie uma classe Java para conter o código de ponto de entrada principal. No Gerenciador de Projetos, clique com botão direito do mouse no nó do projeto e selecione **Novo > Classe**.

2. Em **Nova Classe Java**, nomeie a classe `WebCreator` e verifique a caixa de seleção **public static void main**. As seleções devem aparecer da seguinte maneira:

    ![][2]

3. Clique em **Concluir**. O arquivo WebCreator.java aparece no Gerenciador de Projetos.


### Chamar a API do Azure para criar um aplicativo Web do Serviço de Aplicativo


#### Adicionar as importações necessárias

Em WebCreator.java, adicione as importações a seguir; essas importações fornecem acesso a classes nas bibliotecas de gerenciamento para o consumo de APIs do Azure:

    // General imports
    import java.net.URI;
    import java.util.ArrayList;
    
    // Imports for Exceptions
    import java.io.IOException;
    import java.net.URISyntaxException;
    import javax.xml.parsers.ParserConfigurationException;
    import com.microsoft.windowsazure.exception.ServiceException;
    import org.xml.sax.SAXException;
    
    // Imports for Azure App Service management configuration
    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.management.configuration.ManagementConfiguration;
    
    // Service management imports for App Service Web Apps creation
    import com.microsoft.windowsazure.management.websites.*;
    import com.microsoft.windowsazure.management.websites.models.*;
    
    // Imports for authentication
    import com.microsoft.windowsazure.core.utils.KeyStoreType;


#### Definir a classe de ponto de entrada principal

Como a finalidade do aplicativo AzureWebDemo é criar um aplicativo Web do Serviço de Aplicativo, nomeie a classe principal para esse aplicativo como `WebAppCreator`. Essa classe fornece o código de ponto de entrada principal que chama a API de gerenciamento de serviços do Azure para criar o aplicativo Web.

Adicione as seguintes definições de parâmetro para o aplicativo Web e espaço Web. Você precisará fornecer suas próprias informações de certificado e ID de assinatura do Azure.

    public class WebAppCreator {
    
        // Parameter definitions used for authentication.
        private static String uri = "https://management.core.windows.net/";
        private static String subscriptionId = "<subscription-id>";
        private static String keyStoreLocation = "<certificate-store-path>";
        private static String keyStorePassword = "<certificate-password>";
    
        // Define web app parameter values.
        private static String webAppName = "WebDemoWebApp";
        private static String domainName = ".azurewebsites.net";
        private static String webSpaceName = WebSpaceNames.WESTUSWEBSPACE;
        private static String appServicePlanName = "WebDemoAppServicePlan";

onde:

- `<subscription-id>` é a ID da assinatura do Azure na qual você deseja criar o recurso.
- `<certificate-store-path>` é o caminho e o nome do arquivo para o arquivo JKS em seu diretório de repositório de certificados local. Por exemplo, `C:/Certificates/CertificateName.jks` para Linux e `C:\Certificates\CertificateName.jks` para Windows.
- `<certificate-password>` é a senha que você especificou quando criou seu certificado JKS.
- `webAppName` pode ser qualquer nome que você escolher; este procedimento usa o nome `WebDemoWebApp`. O nome de domínio completo é o `webAppName` com o `domainName` anexado, portanto, nesse caso o domínio completo é `webdemowebapp.azurewebsites.net`.
- `domainName` deve ser especificado conforme mostrado acima.
- `webSpaceName` deve ser um dos valores definidos na classe [WebSpaceNames](http://dl.windowsazure.com/javadoc/com/microsoft/windowsazure/management/websites/models/WebSpaceNames.html).
- `appServicePlanName` deve ser especificado conforme mostrado acima.

> **Observação:** cada vez que você executa esse aplicativo, precisa alterar o valor de `webAppName` e `appServicePlanName` (ou excluir o aplicativo Web no Portal do Azure) antes de executar o aplicativo novamente. Caso contrário, a execução falhará porque o mesmo recurso já existe no Azure.


#### Definir o método de criação na Web

Em seguida, defina um método para criar o aplicativo Web. Esse método, `createWebApp`, especifica os parâmetros do aplicativo Web e espaço Web. Ele também cria e configura o cliente de gerenciamento de Aplicativos Web do Serviço de Aplicativo, que é definido pelo objeto [WebSiteManagementClient](http://dl.windowsazure.com/javadoc/com/microsoft/windowsazure/management/websites/WebSiteManagementClient.html). O cliente de gerenciamento é essencial para a criação de aplicativos Web. Ele fornece serviços Web RESTful, que permitem que aplicativos gerenciem aplicativos Web (executando operações como criação, atualização e exclusão) chamando a API de gerenciamento de serviço.

    private static void createWebApp() throws Exception {

        // Specify configuration settings for the App Service management client.
        Configuration config = ManagementConfiguration.configure(
            new URI(uri),
            subscriptionId,
            keyStoreLocation,  // Path to the JKS file
            keyStorePassword,  // Password for the JKS file
            KeyStoreType.jks   // Flag that you are using a JKS keystore
        );

        // Create the App Service Web Apps management client to call Azure APIs
        // and pass it the App Service management configuration object.
        WebSiteManagementClient webAppManagementClient = WebSiteManagementService.create(config);

        // Create an App Service plan for the web app with the specified parameters.
        WebHostingPlanCreateParameters appServicePlanParams = new WebHostingPlanCreateParameters();
        appServicePlanParams.setName(appServicePlanName);
        appServicePlanParams.setSKU(SkuOptions.Free);
        webAppManagementClient.getWebHostingPlansOperations().create(webSpaceName, appServicePlanParams);

        // Set webspace parameters.
        WebSiteCreateParameters.WebSpaceDetails webSpaceDetails = new WebSiteCreateParameters.WebSpaceDetails();
        webSpaceDetails.setGeoRegion(GeoRegionNames.WESTUS);
        webSpaceDetails.setPlan(WebSpacePlanNames.VIRTUALDEDICATEDPLAN);
        webSpaceDetails.setName(webSpaceName);

        // Set web app parameters.
        // Note that the server farm name takes the Azure App Service plan name.
        WebSiteCreateParameters webAppCreateParameters = new WebSiteCreateParameters();
        webAppCreateParameters.setName(webAppName);
        webAppCreateParameters.setServerFarm(appServicePlanName);
        webAppCreateParameters.setWebSpace(webSpaceDetails);

        // Set usage metrics attributes.
        WebSiteGetUsageMetricsResponse.UsageMetric usageMetric = new WebSiteGetUsageMetricsResponse.UsageMetric();
        usageMetric.setSiteMode(WebSiteMode.Basic);
        usageMetric.setComputeMode(WebSiteComputeMode.Shared);

        // Define the web app object.
        ArrayList<String> fullWebAppName = new ArrayList<String>();
        fullWebAppName.add(webAppName + domainName);
        WebSite webApp = new WebSite();
        webApp.setHostNames(fullWebAppName);

        // Create the web app.
        WebSiteCreateResponse webAppCreateResponse = webAppManagementClient.getWebSitesOperations().create(webSpaceName, webAppCreateParameters);

        // Output the HTTP status code of the response; 200 indicates the request succeeded; 4xx indicates failure.
        System.out.println("----------");
        System.out.println("Web app created - HTTP response " + webAppCreateResponse.getStatusCode() + "\n");

        // Output the name of the web app that this application created.
        String shinyNewWebAppName = webAppCreateResponse.getWebSite().getName();
        System.out.println("----------\n");
        System.out.println("Name of web app created: " + shinyNewWebAppName + "\n");
        System.out.println("----------\n");
    }

O código produzirá como saída o status HTTP da resposta, indicando êxito ou falha e, se for bem-sucedido, retornará o nome do aplicativo Web criado.


#### Definir o método main()

Forneça o código do método Main () que chama createWebApp() para criar o aplicativo Web.

Finalmente, chame `createWebApp` em `main`:

        public static void main(String[] args)
            throws IOException, URISyntaxException, ServiceException,
            ParserConfigurationException, SAXException, Exception {

            // Create web app
            createWebApp();

        }  // end of main()

    }  // end of WebAppCreator class


#### Executar o aplicativo e verifique a criação de aplicativos Web

Para verificar se seu aplicativo é executado, clique em **Executar > Executar**. Quando a execução do aplicativo for concluída, você verá a seguinte saída no console do Eclipse:

    ----------
    Web app created - HTTP response 200
    
    ----------
    
    Name of web app created: WebDemoWebApp
    
    ----------

Faça logon no portal clássico do Azure e clique em **Aplicativos Web**. O novo aplicativo Web deve aparecer na lista de Aplicativos Web em alguns minutos.


## Implantando um aplicativo para o Aplicativo Web

Depois que você executou AzureWebDemo e criou um novo aplicativo Web, faça logon no portal clássico, clique em **Aplicativos Web** e selecione **WebDemoWebApp** na lista **Aplicativos Web**. Na página do painel do aplicativo Web, clique em **Procurar** (ou clique na URL `webdemowebapp.azurewebsites.net`) para navegar até ele. Você verá uma página de alocador de espaço em branco, pois nenhum conteúdo foi publicado ainda para o aplicativo Web.

Em seguida, você criará um aplicativo "Olá Mundo" e implantá-lo para o aplicativo Web.


### Criar um aplicativo Olá Mundo

#### Criar o aplicativo

Para demonstrar como implantar um aplicativo para a Web, o procedimento a seguir mostra como criar um aplicativo Java "Olá Mundo" simples e carregá-lo para o aplicativo Web do Serviço de Aplicativo que o seu aplicativo criou.

1. Clique em **Arquivo > Novo > Projeto Web Dinâmico**. Nomeie-o `JSPHello`. Você não precisa alterar nenhuma outra configuração nesta caixa de diálogo. Clique em **Concluir**.

    ![][3]

2. No Gerenciador de Projetos, expanda o projeto **JSPHello**, clique com botão direito do mouse em **WebContent** e, em seguida, clique em **Novo > Arquivo JSP**. Na caixa de diálogo Novo Arquivo JSP, nomeie o arquivo `index.jsp`. Clique em **Próximo**.

3. Na caixa de diálogo **Selecionar Modelo JSP**, selecione **Novo Arquivo JSP (html)** e clique em **Concluir**.

4. No index.jsp, adicione o seguinte código nas seções de marca `<head>` e `<body>`:

        <head>
          ...
          java.util.Date date = new java.util.Date();
        </head>
    
        <body>
          Hello, the time is <%= date %> 
        </body>


#### Executar o aplicativo Olá Mundo no localhost

Antes de executar este aplicativo, você precisa configurar algumas propriedades.

1. Clique com o botão direito do mouse no projeto **JSPHello** e selecione **Propriedades**.

2. Na caixa de diálogo **Propriedades**: selecione **Caminho de Compilação de Java**, selecione a guia **Ordenar e Exportar**, marque a opção **Biblioteca do Sistema JRE** e, em seguida, clique em **Subir** para movê-lo até o topo da lista.

    ![][4]

3. Também na caixa de diálogo **Propriedades**: selecione **Tempos de Execução Direcionados** e clique em **Novo**.

4. Na caixa de diálogo **Novo Ambiente de Tempo de Execução do Servidor**, selecione um servidor como o **Apache Tomcat versão 7.0** e clique em **Avançar**. Na caixa de diálogo **Servidor Tomcat**, defina **Nome** como `Apache Tomcat v7.0` e defina **Diretório de Instalação do Tomcat** para o diretório no qual você instalou a versão de servidor Tomcat que você deseja usar.

    ![][5]

    Clique em **Concluir**.

5. Você retorna então à página **Tempos de Execução Direcionados** da caixa de diálogo **Propriedades**. Selecione **Apache Tomcat versão 7.0**, em seguida, clique em **OK**.

    ![][6]

6. No menu **Executar** do Eclipse, clique em **Executar**. Na caixa de diálogo **Executar Como**, selecione **Executar no Servidor**. Na caixa de diálogo **Executar no Servidor**, selecione **Servidor Tomcat v7.0**:

    ![][7]

    Clique em **Concluir**.

7. Quando o aplicativo é executado, você deve ver a página **JSPHello** em uma janela de localhost no Eclipse (`http://localhost:8080/JSPHello/`), exibindo a seguinte mensagem:

    `Hello World, the time is Tue Mar 24 23:21:10 GMT 2015`


#### Exportar seu aplicativo como um WAR (arquivo da Web)

Exporte os arquivos de projeto Web como um WAR (arquivo Web) para implantá-lo para o aplicativo Web. Os seguintes arquivos de projeto Web residem na pasta WebContent:

    META-INF
    WEB-INF
    index.jsp

1. Clique na pasta WebContent e selecione **Exportar**.

2. Na caixa de diálogo **Selecionar Exportação**, clique no arquivo **Web > WAR** e clique em **Avançar**.

3. Na caixa de diálogo **Exportar WAR**, selecione o diretório de origem no projeto atual e inclua o nome do arquivo WAR no final. Por exemplo:

    `<project-path>/JSPHello/src/JSPHello.war`

Para obter mais informações sobre como implantar arquivos WAR, consulte [Adicionar um aplicativo Java a Aplicativos Web do Serviço de Aplicativo do Azure](web-sites-java-add-app.md).


### Implantação do aplicativo Olá Mundo usando FTP

Selecione um cliente FTP de terceiros para publicar o aplicativo. Este procedimento descreve duas opções: o console Kudu integrado ao Azure e o FileZilla, uma ferramenta conhecida com uma interface do usuário gráfica e prática.

> **Observação:** o plug-in do Azure para Eclipse com Java 2.4 dá suporte à implantação de contas de armazenamento e serviços de nuvem, mas não dá suporte à implantação de aplicativos Web. Você pode implantar contas de armazenamento e serviços de nuvem usando um projeto de implantação do Azure conforme descrito em [Criando um Aplicativo Olá Mundo do Azure no Eclipse](http://msdn.microsoft.com/library/azure/hh690944.aspx), mas não para aplicativos Web. Use outros métodos como FTP ou GitHub para transferir arquivos para seu aplicativo Web.

> **Observação:** não é recomendável usar FTP no prompt de comando do Windows (o utilitário de linha de comando FTP.EXE que acompanha o Windows). Os clientes FTP que usam FTP ativo, como FTP.EXE, muitas vezes não funcionam através de firewalls. O FTP ativo especifica um endereço interno baseado em LAN, ao qual a conexão por meio de um servidor FTP provavelmente falhará.

Para obter mais informações sobre a implantação de um aplicativo Web de Serviço de Aplicativo usando o FTP, consulte os tópicos a seguir:

- [Gerenciar aplicativos Web usando o Portal do Azure: Implantação](web-sites-manage.md/#deployment)
- [Implantar usando um utilitário FTP](web-sites-deploy.md)


#### Configurar credenciais de implantação

Verifique se você executou o aplicativo **AzureWebDemo** para criar um aplicativo Web. Você transferirá arquivos para este local.

1. Faça logon no portal clássico e clique em **Aplicativos Web**. Certifique-se de **WebDemoWebApp** aparece na lista de aplicativos Web e certifique-se de que ele esteja em execução. Clique em **WebDemoWebApp** para abrir sua página **Painel**.

2. Na página **Painel**, em **Visualização rápida**, clique em **Configurar suas credenciais de implantação** (se você já tem credenciais de implantação, isso aparece como **Redefinir suas credenciais de implantação**).

    As credenciais de implantação estão associadas com uma conta da Microsoft. Você precisa especificar um nome de usuário e senha que você possa usar para implantar usando Git e FTP. Você pode usar essas credenciais para implantar em qualquer aplicativo Web, em todas as assinaturas do Azure associadas à sua conta da Microsoft. Forneça as credenciais de implantação de FTP e Git na caixa de diálogo, e registre o nome de usuário e senha para uso futuro.


#### Obter informações de conexão de FTP

Para usar o FTP para implantar arquivos de aplicativo para o aplicativo Web recém-criado, você precisa obter as informações de conexão. Há duas maneiras de obter as informações de conexão. Uma maneira é visitar a página **Painel** do aplicativo Web; a outra maneira é baixar o perfil de publicação do aplicativo Web. O perfil de publicação é um arquivo XML que fornece informações como as credenciais de logon e o nome de host FTP para seus aplicativos Web, no Serviço de Aplicativo do Azure. Você pode usar esse nome de usuário e senha para implantar em qualquer aplicativo Web em todas as assinaturas associadas à conta do Azure, não apenas esta aqui.

Para obter informações de conexão FTP por meio da folha do aplicativo Web no [Portal do Azure](https://portal.azure.com):

1. Em **Essentials**, localize e copie o **Nome do host FTP**. Isso é um URI similar a `ftp://waws-prod-bay-NNN.ftp.azurewebsites.windows.net`.

2. Em **Essentials**, localize e copie o **nome de usuário de implantação/FTP**. Ele terá o formato *nomedoaplicativoWeb\\nomedeusuário-implantação*; por exemplo, `WebDemoWebApp\deployer77`.

Para obter informações de conexão de FTP por meio do perfil de publicação:

1. Na folha do aplicativo Web, clique em **Perfil de publicação Get**. Isso baixará um arquivo .publishsettings para sua unidade local.

2. Abra o arquivo .publishsettings em um editor de XML ou editor de texto e localize o elemento `<publishProfile>` que contém `publishMethod="FTP"`. Sua tela deverá ter a seguinte aparência:

        <publishProfile
            profileName="WebDemoWebApp - FTP"
            publishMethod="FTP"
            publishUrl="ftp://waws-prod-bay-NNN.ftp.azurewebsites.windows.net/site/wwwroot"
            ftpPassiveMode="True"
            userName="WebDemoWebApp\$WebDemoWebApp"
            userPWD="<deployment-password>"
            ...
        </publishProfile>

3. Observe que as configurações `publishProfile` do aplicativo Web são mapeadas para as configurações do Gerenciador de Sites FileZilla da seguinte maneira:

- `publishUrl` é o mesmo que **nome do host FTP**, o valor que você definiu em **Host**.
- `publishMethod="FTP"` significa que você define **Protocolo** como **FTP - Protocolo de Transferência de Arquivos**, e **Criptografia** como **Usar FTP simples**.
- `userName` e `userPWD` são as chaves para os valores reais de nome de usuário e de senha especificados quando você redefine as credenciais de implantação. `userName` é o mesmo que **Implantação/usuário de FTP**. Elas são mapeadas para **Usuário** e **Senha** no FileZilla.
- `ftpPassiveMode="True"` significa que o site FTP usa transferência por FTP passiva; selecione **Passiva** na guia **Configurações de Transferência**.


#### Configurar o aplicativo Web para hospedar um aplicativo Java

Antes de publicar o aplicativo, você precisa alterar algumas configurações para que o aplicativo Web possa hospedar um aplicativo Java.

1. No portal clássico, vá para a página **Painel** do aplicativo Web e clique em **Configurar**. Na página **Configurar**, especifique as configurações a seguir.

2. Em **Versão do Java**, o padrão é **Desativado**; selecione a versão de Java que seu aplicativo busca; por exemplo, 1.7.0_51. Depois de fazer isso, verifique também que **Contêiner da Web** esteja definido para uma versão do Servidor Tomcat.

3. Em **Documentos Padrão**, adicione index.jsp e mova-o para a parte superior da lista. (O arquivo padrão para aplicativos Web é hostingstart.html.)

4. Clique em **Salvar**.


#### Publicar seu aplicativo usando Kudu

Uma maneira de publicar o aplicativo é usar o console de depuração Kudu integrado ao Azure. O Kudu é conhecido por ser estável e consistente com Aplicativos Web do Serviço de Aplicativo e servidor Tomcat. Você pode acessar o console para o aplicativo Web navegando até uma URL no seguinte formato:

`https://<webappname>.scm.azurewebsites.net/DebugConsole`

1. Neste procedimento, o console Kudu está localizado na URL a seguir; navegue até o local:

    `https://webdemowebapp.scm.azurewebsites.net/DebugConsole`

2. No menu superior, selecione **Console de Depuração > CMD**.

3. Na linha de comando do console, navegue até `/site/wwwroot` (ou clique em `site`, depois em `wwwroot` na exibição do diretório, na parte superior da página):

    `cd /site/wwwroot`

4. Depois de especificar a **Versão do Java**, o servidor Tomcat deve criar uma pasta webapps. Na linha de comando de console, navegue até a pasta webapps:

    `mkdir webapps`

    `cd webapps`

5. Arraste JSPHello.war em `<project-path>/JSPHello/src/` e solte-o no modo de exibição de pasta do Kudu em `/site/wwwroot/webapps`. Não arraste-o para a área "Arraste aqui para carregar e compactar", porque o Tomcat vai descompactá-lo.

  ![][8]

Primeiro, o JSPHello.war aparece na área da pasta sozinho:

  ![][9]

Em um curto período de tempo (provavelmente menos de 5 minutos), o servidor Tomcat descompactará o arquivo WAR em um diretório JSPHello. Clique no diretório raiz para ver se index.jsp foi descompactado e copiado para esse local. Se isso ocorreu, navegue até a pasta webapps para ver se a pasta JSPHello descompactada foi criado ou não. Se você não ver esses itens, aguarde e repita.

  ![][10]


#### Publicar seu aplicativo usando o FileZilla (opcional)

Outra ferramenta que você pode usar para publicar o aplicativo é o FileZilla, um cliente FTP de terceiros popular, com uma interface do usuário gráfica e prática. Você pode baixar e instalar o FileZilla de [http://filezilla-project.org/](http://filezilla-project.org/) se você ainda não o tiver. Para obter mais informações sobre como usar o cliente, consulte a [Documentação do FileZilla](https://wiki.filezilla-project.org/Documentation) e esta entrada de blog em [Clientes FTP - parte 4: FileZilla](http://blogs.msdn.com/b/robert_mcmurray/archive/2008/12/17/ftp-clients-part-4-filezilla.aspx).

1. No FileZilla, clique em **Arquivo > Gerenciador de Site**.
2. Na caixa de diálogo **Gerenciador de Sites**, clique em **Novo Site**. Um novo site FTP em branco aparecerá em **Selecionar Entrada**, solicitando que você forneça um nome. Para este procedimento, nomeie-o como `AzureWebDemo-FTP`.

    Na guia **Geral**, especifique as seguintes configurações: - **Host:** insira o **nome do Host FTP** que você copiou do painel. - **Porta:** (deixe isso em branco, já que se trata de uma transferência passiva e o servidor determinará a porta a ser usada.) - **Protocolo:** protocolo de transferência de arquivos FTP - **Criptografia:** usar FTP simples - **Tipo de Logon:** normal - **Usuário:** insira o usuário de implantação/FTP que você copiou do painel. Isso é o nome de usuário FTP completo, que tem o formato *nomedoaplicativoWeb\\nomedeusuário*. - **Senha:** digite a senha que você especificou quando definiu as credenciais de implantação.

    Na guia **Configurações de Transferência**, selecione **Passiva**.

3. Clique em **Conectar**. Se a conexão for bem-sucedida, o console do FileZilla exibirá uma mensagem `Status: Connected` e emitirá um comando `LIST` para listar o conteúdo da pasta.

4. No painel do site **Local**, selecione o diretório de origem no qual reside o arquivo JSPHello.war; o caminho será semelhante ao seguinte:

    `<project-path>/JSPHello/src/`

5. No painel do site **Remoto**, selecione a pasta de destino. Você implantará o arquivo WAR no diretório `webapps` na raiz do aplicativo Web. Navegue até `/site/wwwroot`, clique em `wwwroot` e selecione **Criar diretório**. Nomeie o diretório como `webapps` e acesse esse diretório.

6. Transfira JSPHello.war para `/site/wwwroot/webapps`. Selecione JSPHello.war na lista de arquivos **Local**, clique com botão direito do mouse nele e selecione **Carregar**. Você deverá vê-lo aparecer em `/site/wwwroot/webapps`.

7. Depois de copiar JSPHello.war para a pasta webapps, o Servidor Tomcat descompactará automaticamente os arquivos contidos no arquivo WAR. Embora o servidor Tomcat comece a desempacotar quase imediatamente, pode levar um longo tempo (possivelmente horas) para que os arquivos sejam exibidos no cliente FTP.


#### Executar o aplicativo Olá Mundo no aplicativo Web

1. Depois de ter carregado o arquivo WAR e verificado que o servidor Tomcat criou um diretório `JSPHello` descompactado, navegue até `http://webdemowebapp.azurewebsites.net/JSPHello` para executar o aplicativo.

    > **Observação:** se você clicar em **Procurar** no portal clássico, você poderá obter a página da Web padrão, dizendo "Este aplicativo Web baseado em Java foi criado com êxito." Você talvez precise atualizar a página da Web para exibir a saída do aplicativo, em vez da página da Web padrão.

2. Quando o aplicativo for executado, você deverá ver uma página da Web com a seguinte saída:

    `Hello World, the time is Tue Mar 24 23:21:10 GMT 2015`


#### Limpar recursos do Azure

Este procedimento cria um aplicativo Web do Serviço de Aplicativo. Você será cobrado pelo recurso enquanto ele existir. A menos que você planeje continuar usando o aplicativo Web para teste ou desenvolvimento, você deve considerar interromper sua execução ou excluí-lo. Um aplicativo Web cuja execução foi interrompida ainda incorrerá em um pequeno encargo, mas você poderá reiniciá-lo a qualquer momento. A exclusão de um aplicativo Web apaga todos os dados que você carregou nele.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

[1]: ./media/java-create-azure-website-using-java-sdk/eclipse-maven-repositories-rebuild-index.png
[2]: ./media/java-create-azure-website-using-java-sdk/eclipse-new-java-class.png
[3]: ./media/java-create-azure-website-using-java-sdk/eclipse-new-dynamic-web-project.png
[4]: ./media/java-create-azure-website-using-java-sdk/eclipse-java-build-path.png
[5]: ./media/java-create-azure-website-using-java-sdk/eclipse-targeted-runtimes-tomcat-server.png
[6]: ./media/java-create-azure-website-using-java-sdk/eclipse-targeted-runtimes-properties-page.png
[7]: ./media/java-create-azure-website-using-java-sdk/eclipse-run-on-server.png
[8]: ./media/java-create-azure-website-using-java-sdk/kudu-console-drag-drop.png
[9]: ./media/java-create-azure-website-using-java-sdk/kudu-console-jsphello-war-1.png
[10]: ./media/java-create-azure-website-using-java-sdk/kudu-console-jsphello-war-2.png

<!--HONumber=54--> 