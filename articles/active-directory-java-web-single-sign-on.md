<properties linkid="develop-java-how-to-guides-web-sso" urlDisplayName="Web SSO" pageTitle="Single sign-on with Azure Active Directory (Java)" metaKeywords="Azure Java web app, Azure single sign-on, Azure Java Active Directory" description="Learn how to create a Java web application that uses single sign-on with Azure Active Directory." metaCanonical="" services="active-directory" documentationCenter="Java" title="Web Single Sign-On with Java and Azure Active Directory" authors="mbaldwin" solutions="" manager="mbaldwin" editor="" />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="mbaldwin" />

# Logon único da web com o Java e o Active Directory do Azure

## <a name="introduction"></a> Introdução

Este tutorial mostra aos desenvolvedores de Java como utilizar o Active Directory do Azure para habilitar o logon único para usuários de clientes do Office 365. Você saberá como:

-   Provisionar o aplicativo web em um locatário do cliente
-   Proteger o aplicativo usando o WS-Federation

### Pré-requisitos

Este tutorial usa um servidor de aplicativo específico, mas se você for um desenvolvedor experiente do Java, o processo descrito a seguir pode ser aplicado a outros ambientes também. Os seguintes pré-requisitos do ambiente de desenvolvimento são necessários para este tutorial:

-   [Código de exemplo de Java para o Active Directory do Azure][Código de exemplo de Java para o Active Directory do Azure]
-   [Java Runtime Environment 1.6][Java Runtime Environment 1.6]
-   [JBoss Application Server versão 7.1.1.Final][JBoss Application Server versão 7.1.1.Final]
-   [IDE do JBoss Developer Studio][IDE do JBoss Developer Studio]
-   Serviços de Informações da Internet (IIS) 7.5 com SSL habilitado
-   Windows PowerShell
-   [Cmdlets do PowerShell do Office 365][Cmdlets do PowerShell do Office 365]

### Sumário

-   [Introdução][Introdução]
-   [Etapa 1: Criar um aplicativo Java][Etapa 1: Criar um aplicativo Java]
-   [Etapa 2: Provisionar o aplicativo em um locatário do diretório da empresa][Etapa 2: Provisionar o aplicativo em um locatário do diretório da empresa]
-   [Etapa 3: Proteger o aplicativo usando o WS-Federation para entrada de funcionários][Etapa 3: Proteger o aplicativo usando o WS-Federation para entrada de funcionários]
-   [Resumo][Resumo]

## <a name="createapp"></a>Etapa 1: Criar um aplicativo Java

Esta etapa descreve como criar um aplicativo Java simples que representará um recurso protegido. O acesso a esse recurso será concedido por meio de autenticação federada gerenciada pelo STS da empresa, que é descrito mais adiante no tutorial.

1.  Abra uma nova instância do JBoss Developer Studio.
2.  No menu **Arquivo**, clique em **Novo** e, em seguida, clique em **Projeto...**.
3.  Na caixa de diálogo **Novo Projeto**, expanda a pasta **Maven**, clique em **Projeto Maven**, em seguida, clique em **Avançar**.
4.  Na caixa de diálogo **Novo Projeto Maven**, marque **Criar um projeto simples (ignorar seleção de arquétipo)** e, em seguida, clique em **Avançar**.
5.  Na próxima página da caixa de diálogo **Novo Projeto Maven**, digite *exemplo* nas caixas de texto **Id do grupo** e **Id do Artefato**. Em seguida, selecione **war** no menu suspenso **Empacotamento** e clique em **Concluir**.
6.  O novo projeto Maven será criado. No menu **Explorador de projeto** à esquerda, expanda o projeto **exemplo**, clique com o botão direito do mouse no arquivo **pom.xml**, clique em **Abrir com** e, em seguida, clique em **Editor de texto**.
7.  No arquivo **pom.xml**, adicione o seguinte XML na seção *\<project\>*:

        <repositories>
            <repository>
                <id>jboss-public-repository-group</id>
                <name>JBoss Public Maven Repository Group</name>
                <url>https://repository.jboss.org/nexus/content/groups/public-jboss/</url>
                <layout>default</layout>
                <releases>
                    <enabled>true</enabled>
                    <updatePolicy>never</updatePolicy>
                </releases>
                <snapshots>
                    <enabled>true</enabled>
                    <updatePolicy>never</updatePolicy>
                </snapshots>
            </repository>
        </repositories>
        <pluginRepositories>
            <pluginRepository>
                <id>jboss-public-repository-group</id>
                <name>JBoss Public Maven Repository Group</name>
                <url>https://repository.jboss.org/nexus/content/groups/public-jboss/</url>
                <layout>default</layout>
                <releases>
                    <enabled>true</enabled>
                    <updatePolicy>always</updatePolicy>
                </releases>
                <snapshots>
                    <enabled>true</enabled>
                    <updatePolicy>always</updatePolicy>
                </snapshots>
            </pluginRepository>
        </pluginRepositories>
        <build>
            <plugins>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>2.0.2</version>
                    <configuration>
                        <source>1.6</source>
                        <target>1.6</target>
                    </configuration>
                </plugin>
            </plugins>
        </build> 

    Depois que você tiver inserido esse XML, salve o arquivo **pom.xml**.

8.  Clique com botão direito do mouse no projeto **exemplo** e clique em **Maven**, em seguida, clique em **Atualizar Projeto Maven**. Na caixa de diálogo **Atualizar Projeto Maven**, clique em **OK**. Essa etapa irá atualizar seu projeto com as alterações em **pom.xml**.

9.  Clique com o botão direito do mouse no projeto **exemplo**, clique em **Novo** e, em seguida, clique em **Arquivo JSP**.

10. Na caixa de diálogo **Novo Arquivo JSP**, altere o caminho para o novo arquivo para *sample/src/main/webapp*. Em seguida, nomeie o arquivo **index.jsp** e clique em **Concluir**.

11. O novo arquivo **index.jsp** abrirá automaticamente. Substitua o código gerado automaticamente pelo seguinte e salve o arquivo.

        <%@ page language="java" contentType="text/html; charset=ISO-8859-1"  pageEncoding="ISO-8859-1"%>
        <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
        <html>
        <head>
            <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
            <title>Index</title>
        </head>
        <body>
            <h3>Index Page</h3>
        </body>
        </html> 

12. Clique com botão direito do mouse no projeto **exemplo** e clique em **Executar como** e, em seguida, clique em **Executar no Servidor**.

13. Na caixa de diálogo **Executar no Servidor**, verifique se **JBoss Enterprise Application Platform 6.x** está selecionado e clique em **Concluído**.

## <a name="provisionapp"></a>Etapa 2: Provisionar o aplicativo em um locatário do diretório da empresa

Esta etapa descreve como um administrador de um cliente do Active Directory do Azure provisiona o aplicativo Java em seu locatário e configura o logon único. Após essa etapa ser realizada, os funcionários da empresa podem autenticar-se no aplicativo web usando suas contas do Office 365.

O processo de provisionamento começa com a criação de uma nova Entidade de Serviço para o aplicativo. As Entidades de Serviço são usadas pelo Active Directory do Azure para registrar e autenticar aplicativos no diretório.

1.  Se ainda não tiver feito isso, baixe e instale os cmdlets do Office 365 PowerShell.
2.  No menu **Iniciar**, execute o console do **Módulo do Microsoft Online Services para Windows PowerShell**. Esse console fornece um ambiente de linha de comando para configurar atributos sobre locatários do Office 365, como criar e modificar Entidades de Serviço.
3.  Para importar o módulo **MSOnlineExtended** requerido, digite o seguinte comando e pressione Enter:

        Import-Module MSOnlineExtended -Force

4.  Para conectar-se a seu diretório do Office 365, você precisa fornecer as credenciais de administrador da empresa. Digite o seguinte comando e pressione Enter e, em seguida, digite suas credenciais no prompt:

        Connect-MsolService

5.  Agora você criará uma nova Entidade de Serviço para o aplicativo. Digite o comando a seguir e pressione Enter:

        New-MsolServicePrincipal -ServicePrincipalNames @("javaSample/localhost") -DisplayName "Federation Sample Website" -Type Symmetric -Usage Verify -StartDate "12/01/2012" -EndDate "12/01/2013" 

    Essa etapa retornará informações semelhantes às seguintes:

        The following symmetric key was created as one was not supplied qY+Drf20Zz+A4t2w e3PebCopoCugO76My+JMVsqNBFc=
        DisplayName           : Federation Sample Java Website
        ServicePrincipalNames : {javaSample/localhost}
        ObjectId              : 59cab09a-3f5d-4e86-999c-2e69f682d90d
        AppPrincipalId        : 7829c758-2bef-43df-a685-717089474505
        TrustedForDelegation  : False
        AccountEnabled        : True
        KeyType               : Symmetric
        KeyId                 : f1735cbe-aa46-421b-8a1c-03b8f9bb3565
        StartDate             : 12/01/2012 08:00:00 a.m.
        EndDate               : 12/01/2013 08:00:00 a.m.
        Usage                 : Verify 

    > [WACOM.NOTE]
    > Você deve salvar essa saída, principalmente a chave simétrica gerada. Essa chave só é revelada a você durante a criação da Entidade de Serviço, e você não pode recuperá-la no futuro. Os outros valores são necessários para usar a Graph API para ler e gravar informações no diretório.

6.  A etapa final define a URL de resposta para o seu aplicativo. A URL de resposta é onde as respostas são enviadas após tentativas de autenticação. Digite os comandos a seguir e pressione enter:

        $replyUrl = New-MsolServicePrincipalAddresses -Address "https://localhost:8443/sample" 

        Set-MsolServicePrincipal -AppPrincipalId "7829c758-2bef-43df-a685-717089474505" -Addresses $replyUrl 

O aplicativo da web agora foi provisionado no diretório e pode ser usado para logon único da web pelos funcionários da empresa.

## <a name="protectapp"></a>Etapa 3: Proteger o aplicativo usando o WS-Federation para entrada de funcionários

Esta etapa mostra como adicionar suporte para logon federado usando o Windows Identity Foundation (WIF) e a biblioteca **waad-federation** baixada como parte do pacote de código de exemplo nos pré-requisitos. Você também adicionará uma página de logon e configurará a relação de confiança entre o aplicativo e o locatário do diretório.

1.  No JBoss Developer Studio, clique em **Arquivo** e, em seguida, clique em **Importar**.

2.  Na caixa de diálogo **Importar**, expanda a pasta **Maven**, clique em **Projeto Maven Existente**, em seguida, clique em **Avançar**.

3.  Na caixa de diálogo **Importar Projetos Maven**, defina o caminho **Diretório Raiz** para o local onde você baixou a biblioteca **waad-federation** no código de exemplo. Em seguida, selecione a caixa de seleção ao lado do arquivo **pom.xml** no projeto **waad-federation** e clique em **Concluir**.

4.  Expanda o projeto **exemplo**, clique com o botão direito do mouse no arquivo **pom.xml**, clique em **Abrir com**, e clique em **Editor de Texto**.

5.  No arquivo **pom.xml**, adicione o seguinte XML na seção *\<project\>* e, em seguida, salve o arquivo:

        <dependencies>
            <dependency>
                <groupId>javax.servlet</groupId>
                <artifactId>servlet-api</artifactId>
                <version>3.0-alpha-1</version>
            </dependency>
            <dependency>
                <groupId>com.microsoft.samples.waad.federation</groupId>
                <artifactId>waad-federation</artifactId>
                <version>0.0.1-SNAPSHOT</version>
            </dependency>
        </dependencies> 

6.  Clique com botão direito do mouse no projeto **exemplo** e clique em **Maven**, em seguida, clique em **Atualizar Projeto**. Na caixa de diálogo **Atualizar Projeto Maven**, clique em **OK**. Essa etapa irá atualizar seu projeto com as alterações em **pom.xml**.

7.  Clique com o botão direito do mouse no projeto **exemplo**, clique em **Novo** e, em seguida, clique em **Filtro**.

8.  Na caixa de diálogo **Criar Filtro**, digitar *FederationFilter* na entrada **Nome de classe**, e clique em **Concluir**.

9.  O arquivo **FederationFilter.java** gerado automaticamente será aberto. Substitua o código do arquivo pelo seguinte e salve-o:

        import java.io.IOException;
        import javax.servlet.Filter;
        import javax.servlet.FilterChain;
        import javax.servlet.FilterConfig;
        import javax.servlet.ServletException;
        import javax.servlet.ServletRequest;
        import javax.servlet.ServletResponse;
        import javax.servlet.http.HttpServletRequest;
        import javax.servlet.http.HttpServletResponse;
        import java.util.regex.*;
        import com.microsoft.samples.federation.FederatedLoginManager; import com.microsoft.samples.federation.URLUTF8Encoder;

        public class FederationFilter implements Filter {
            private String loginPage;
            private String allowedRegex;
            public void init(FilterConfig config) throws ServletException {
                this.loginPage = config.getInitParameter("login-page-url");
                this.allowedRegex = config.getInitParameter("allowed-regex");
            }
            public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
                    if (request instanceof HttpServletRequest) { 
                        HttpServletRequest httpRequest = (HttpServletRequest) request;
                        if (!httpRequest.getRequestURL().toString().contains(this.loginPage)) {
                            FederatedLoginManager loginManager = FederatedLoginManager.fromRequest(httpRequest);
                            boolean allowedUrl = Pattern.compile(this.allowedRegex).matcher(httpRequest.getRequestURL().toString()).find();
                            if (!allowedUrl && !loginManager.isAuthenticated()) {
                                HttpServletResponse httpResponse = (HttpServletResponse) response;
                                String encodedReturnUrl = URLUTF8Encoder.encode(httpRequest.getRequestURL().toString());
                                httpResponse.setHeader("Location", this.loginPage + "?returnUrl=" + encodedReturnUrl);
                                httpResponse.setStatus(302);
                                return;
                            }
                        }
                    }
                chain.doFilter(request, response);
            }
            public void destroy() {
            }
        } 

10. No **Explorador de Projeto**, expanda o **src**, em seguida **principal**, em seguida, pastas **webapp** folders. Clique com o botão direito do mouse no arquivo **web.xml**, clique em **Abrir com** e clique no **Editor de Texto**.

11. No arquivo **web.xml**, você irá adicionar um filtro para manipular páginas seguras e não seguras, e ele também irá redirecionar usuários não autenticados para a página de logon (especificada como o parâmetro do filtro **login-page-url**). No entanto, se uma URL corresponder à regex especificada no parâmetro do filtro **allowed-regex**, ela não será filtrada. Adicione o seguinte XML na seção *\<web-app\>* e, em seguida, salve o arquivo **web.xml**.

        <filter>
            <filter-name>FederationFilter</filter-name>
            <filter-class>FederationFilter</filter-class>
            <init-param>
                <param-name>login-page-url</param-name>
                <param-value>/sample/login.jsp</param-value>
            </init-param>
            <init-param>
                <param-name>allowed-regex</param-name>
                <param-value>(\/sample\/login.jsp|\/sample\/wsfed-saml|\/sample\/oauth)</param-value>
            </init-param>
        </filter>
        <filter-mapping>
            <filter-name>FederationFilter</filter-name>
            <url-pattern>/*</url-pattern>
        </filter-mapping> 

12. Para criar uma página de logon, clique com o botão direito do mouse no projeto **exemplo**, clique em **Novo** e, em seguida, clique em **Arquivo JSP**.

13. Na caixa de diálogo **Novo Arquivo JSP**, altere o caminho para o novo arquivo para *sample/src/main/webapp*. Em seguida, nomeie o arquivo **login.jsp** e clique em **Concluir**.

14. O novo arquivo **login.jsp** abrirá automaticamente. Substitua o código gerado automaticamente pelo seguinte e salve o arquivo.

        <%@ page language="java" contentType="text/html; charset=ISO-8859-1"  pageEncoding="ISO-8859-1"%>
        <%@ page import="com.microsoft.samples.federation.*"%>
        <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
        <html>
        <head>
            <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1"> 
            <title>Login Page</title>
        </head>
        <body>
            <h3>Login Page</h3>
            <a href="<%=FederatedLoginManager.getFederatedLoginUrl(request.getParameter("returnUrl"))%>"><%=FederatedConfiguration.getInstance().getStsFriendlyName()%></a>
        </body>
        </html> 

15. No **Explorador de Projeto**, expanda a pasta **/src/main** do projeto **exemplo**. Clique com o botão direito do mouse na pasta **resources**, clique em **Novo** e, em seguida, clique em **Outro**.

16. Na caixa de diálogo **Novo**, expanda a pasta **JBoss Tools Web**, clique em **Arquivo de Propriedades** e, em seguida, clique em **Avançar**.

17. Na caixa de diálogo **Propriedades do Novo Arquivo**, nomeie o arquivo **federação** e, em seguida, clique em **Concluir**.

18. No **Explorador de Projeto**, expanda a pasta **src/main/resources** do projeto **exemplo**. Clique com o botão direito do mouse no arquivo **federation.properties**, clique em **Abrir com** e clique no **Editor de Texto**.

19. No arquivo **federation.properties**, inclua as seguintes entradas de configuração e salve o arquivo:

        federation.trustedissuers.issuer=https://accounts.accesscontrol.windows.net/v2/wsfederation
        federation.trustedissuers.thumbprint=qY+Drf20Zz+A4t2we3PebCopoCugO76My+JMVsqNBFc=
        federation.trustedissuers.friendlyname=Fabrikam
        federation.audienceuris=spn:7829c758-2bef-43df-a685-717089474505
        federation.realm=spn:7829c758-2bef-43df-a685-717089474505
        federation.reply=https://localhost:8443/sample/wsfed-saml 

    > [WACOM.NOTE]
    >  Os valores **audienceuris** e **realm** valores devem ser precedidos por "spn:".

20. Agora você precisa criar um novo Servlet. Clique com o botão direito do mouse no projeto **exemplo**, clique em **Novo**, clique em **Outro** e, em seguida, clique em **Servlet**.

21. Na caixa de diálogo **Criar Servlet**, forneça um **Nome de classe** de *FederationServlet* e clique em **Concluir**.

22. O arquivo **FederationServlet.java** é aberto automaticamente. Substitua seu conteúdo pelo código a seguir e salve-o:

        import java.io.IOException;
        import javax.servlet.ServletException;
        import javax.servlet.http.HttpServlet;
        import javax.servlet.http.HttpServletRequest;
        import javax.servlet.http.HttpServletResponse;
        import com.microsoft.samples.federation.FederatedAuthenticationListener;
        import com.microsoft.samples.federation.FederatedLoginManager;
        import com.microsoft.samples.federation.FederatedPrincipal;
        import com.microsoft.samples.federation.FederationException;

        public class FederationServlet extends HttpServlet {
            private static final long serialVersionUID = 1L;

            protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
                String token = request.getParameter("wresult").toString();

                if (token == null) {
                    response.sendError(400, "You were supposed to send a wresult parameter with a token");
                }
                FederatedLoginManager loginManager = FederatedLoginManager.fromRequest(request, new SampleAuthenticationListener());

                try {
                    loginManager.authenticate(token, response);
                } catch (FederationException e) {
                    response.sendError(500, "Oops! and error occurred.");
                }
            }

            private class SampleAuthenticationListener implements FederatedAuthenticationListener {
                @Override
                public void OnAuthenticationSucceed(FederatedPrincipal principal) {
                    // ***
                    // do whatever you want with the principal object that contains the token's claims
                    // ***
                }
            }
        } 

23. No **Explorador de Projeto**, expanda a pasta **src/main/webapp/WEB-INF**. Clique com o botão direito do mouse no arquivo **web.xml**, clique em **Abrir com** e clique no **Editor de Texto**.

24. No arquivo **web.xml**, substitua a configuração **/FederationServlet** na seção *\<url-pattern\>* com **/ws-saml**. Por exemplo:

        <servlet>
            <description></description>
            <display-name>FederationServlet</display-name>
            <servlet-name>FederationServlet</servlet-name>
            <servlet-class>FederationServlet</servlet-class>
        </servlet>
        <servlet-mapping>
            <servlet-name>FederationServlet</servlet-name>
            <url-pattern>/wsfed-saml</url-pattern>
        </servlet-mapping> 

25. No **Explorador de Projetos**, expanda a pasta **src/main/webapp**. Clique com o botão direito do mouse no arquivo **index.jsp**, clique em **Abrir com** e clique no **Editor de Texto**.

26. No arquivo **index.jsp**, substitua o código existente pelo código a seguir e salve o arquivo:

        <%@ page language="java" contentType="text/html; charset=ISO-8859-1" pageEncoding="ISO-8859-1"%>
        <%@ page import="com.microsoft.samples.federation.*"%>
        <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
        <html>
        <head>
            <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
            <title>Index Page</title>
        </head>
        <body>
            <h3>Welcome <strong><%=FederatedLoginManager.fromRequest(request).getPrincipal().getName()%></strong>!</h3>
            <h2>Claim list:</h2>
            <ul> 
                <% for (Claim claim : FederatedLoginManager.fromRequest(request).getClaims()) { %>
                <li><%= claim.toString()%></li>
                <%  } %>
            </ul>
        </body>
        </html> 

27. No **Explorador de Projeto**, expanda a pasta **src/main/webapp/WEB-INF**. Clique com o botão direito do mouse no arquivo **web.xml**, clique em **Abrir com** e clique no **Editor de Texto**.

28. Agora instalaremos o SSL para o aplicativo. No arquivo **web.xml**, insira a seguinte seção *\<security-constraint\>* na seção *\<web-app\>* e, em seguida, salve o arquivo:

        <security-constraint>
            <web-resource-collection>
                <web-resource-name>SSL Forwarding</web-resource-name>
                <url-pattern>/*</url-pattern>
                <http-method>POST</http-method>
                <http-method>GET</http-method>
            </web-resource-collection>
            <user-data-constraint>
                <transport-guarantee>CONFIDENTIAL</transport-guarantee>
            </user-data-constraint>
        </security-constraint> 

    > [WACOM.NOTE]
    > Antes de prosseguir, verifique se o JBoss Server já está configurado para oferecer suporte a SSL.

29. Agora estamos prontos para executar o aplicativo completo. Clique com botão direito do mouse no projeto **exemplo** e clique em **Executar como** e, em seguida, clique em **Executar no Servidor**. Aceite os valores que você especificou antes e, em seguida, clique em **Concluir**.

30. O navegador JBoss abrirá a página de logon. Se você clicar no link **Computadores Impressionantes**, você será redirecionado para a página Provedor de Identidade do Office 365, onde você poderá fazer logon usando suas credenciais de locatário do diretório. Por exemplo, <*john.doe@fabrikam.onmicrosoft.com*>.

31. Depois de fazer logon, você será redirecionado novamente para a página protegida (**sample/index.jsp**) como um usuário autenticado.

## <a name="summary"></a>Resumo

Este tutorial mostrou como criar e configurar um aplicativo Java de locatário único que usa os recursos de logon único do Active Directory do Azure.

  [Código de exemplo de Java para o Active Directory do Azure]: https://github.com/WindowsAzure/azure-sdk-for-java-samples/tree/master/WAAD.WebSSO.JAVA
  [Java Runtime Environment 1.6]: http://www.oracle.com/technetwork/java/javase/downloads/index.html
  [JBoss Application Server versão 7.1.1.Final]: http://www.jboss.org/jbossas/downloads/
  [IDE do JBoss Developer Studio]: https://devstudio.jboss.com/earlyaccess/
  [Cmdlets do PowerShell do Office 365]: http://onlinehelp.microsoft.com/pt-BR/office365-enterprises/ff652560.aspx
  [Introdução]: #introduction
  [Etapa 1: Criar um aplicativo Java]: #createapp
  [Etapa 2: Provisionar o aplicativo em um locatário do diretório da empresa]: #provisionapp
  [Etapa 3: Proteger o aplicativo usando o WS-Federation para entrada de funcionários]: #protectapp
  [Resumo]: #summary
