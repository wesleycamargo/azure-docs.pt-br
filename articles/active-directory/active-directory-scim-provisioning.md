---
title: Usando o SCIM para habilitar o provisionamento automático de usuários e grupos do Active Directory do Azure para aplicativos | Microsoft Docs
description: O Active Directory do Azure pode provisionar automaticamente usuários e grupos para qualquer repositório de identidades ou aplicativos que seja administrado por um serviço Web com a interface definida na especificação do protocolo SCIM.
services: active-directory
documentationcenter: ''
author: asmalser-msft
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2016
ms.author: asmalser-msft

---
# Usando o SCIM para habilitar o provisionamento automático de usuários e grupos do Active Directory do Azure para aplicativos
## Visão geral
O Active Directory do Azure pode provisionar automaticamente usuários e grupos para qualquer repositório de identidades ou aplicativos que seja administrado por um serviço Web com a interface definida na [especificação do protocolo SCIM 2.0](https://tools.ietf.org/html/draft-ietf-scim-api-19). O Active Directory do Azure pode enviar solicitações para criar, modificar e excluir usuários e grupos atribuídos a esse serviço Web, que poderá então converter essas solicitações em operações no repositório de identidades de destino.

![][1] *Figura: provisionamento do Active Directory do Azure para um repositório de identidades por meio de um serviço Web*

Esse recurso pode ser usado juntamente com o recurso "[traga seu próprio aplicativo](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)" no AD do Azure para habilitar o logon único e o provisionamento automático de usuário de aplicativos que forneçam ou comecem com um serviço Web SCIM.

Há dois casos de uso para SCIM no Active Directory do Azure:

* **Provisionamento de usuários e grupos para os aplicativos que dão suporte a SCIM**: aplicativos que dão suporte a SCIM 2.0 e usam tokens de portador OAuth para autenticação funcionarão com o Azure AD de imediato.
* **Criar a sua própria solução de provisionamento para aplicativos que dão suporte a outro provisionamento baseado em API**: para aplicativos não SCIM, você pode criar um ponto de extremidade SCIM para converter entre o ponto de extremidade SCIM do AD do Azure e qualquer API à qual o aplicativo dê suporte para provisionamento de usuários. Para ajudar no desenvolvimento de um ponto de extremidade SCIM, fornecemos bibliotecas CLI com exemplos de código que mostram como fornecer um ponto de extremidade SCIM e converter mensagens SCIM.

## Provisionando usuários e grupos para aplicativos que dão suporte a SCIM
O Active Directory do Azure pode ser configurado para provisionar automaticamente usuários e grupos atribuídos a aplicativos que implementam um serviço Web de [Sistema de Gerenciamento de Identidade entre Domínios 2 (SCIM)](https://tools.ietf.org/html/draft-ietf-scim-api-19) e aceitam tokens de portador OAuth para autenticação. Dentro da especificação SCIM 2.0, os aplicativos devem satisfazer estes requisitos:

* Dar suporte à criação de usuários e/ou grupos, de acordo com a seção 3.3 do protocolo SCIM.
* Dar suporte à modificação de usuários e/ou grupos com solicitações de patch, de acordo com a seção 3.5.2 do protocolo SCIM.
* Dar suporte à recuperação de um recurso conhecido, de acordo com a seção 3.4.1 do protocolo SCIM.
* Dar suporte a consultas de usuários e/ou grupos, de acordo com a seção 3.4.2 do protocolo SCIM. Por padrão, os usuários são consultados por externalId e os grupos são consultados por displayName.
* Dar suporte a consultas de usuário por ID e pelo gerenciador, de acordo com a seção 3.4.2 do protocolo SCIM.
* Dar suporte a consultas de grupos por ID e por membro, de acordo com a seção 3.4.2 do protocolo SCIM.
* Aceitar tokens de portador OAuth para autorização, de acordo com a seção 2.1 do protocolo SCIM.

Verifique com o seu provedor de aplicativo ou na documentação do seu provedor de aplicativo as declarações de compatibilidade com esses requisitos.

### Introdução
Os aplicativos que dão suporte ao perfil SCIM descrito acima podem ser conectados ao Active Directory do Azure usando o recurso de aplicativo "personalizado" na galeria de aplicativos do AD do Azure. Uma vez conectado, o AD do Azure executa um processo de sincronização a cada 5 minutos, em que ele consulta o ponto de extremidade SCIM do aplicativo para os usuários e grupos atribuídos e os cria ou modifica de acordo com os detalhes da atribuição.

**Para conectar um aplicativo que dê suporte a SCIM:**

1. Em um navegador da Web, inicie o portal de gerenciamento do Azure em https://manage.windowsazure.com.
2. Navegue até **Active Directory > Diretório > [seu diretório] > Aplicativos** e selecione **Adicionar > Adicionar um aplicativo da galeria**.
3. Selecione a guia **Personalizado** à esquerda, insira um nome para o seu aplicativo e clique no ícone de marca de seleção para criar um objeto de aplicativo.

![][2]

1. Na tela resultante, selecione o segundo botão **Configurar provisionamento da conta**.
2. No campo **URL de ponto de extremidade de provisionamento**, insira a URL do ponto de extremidade do SCIM do aplicativo.
3. Se o ponto de extremidade SCIM exigir um token de portador OAuth de um emissor diferente do Azure AD, copie o token de portador OAuth necessário para o campo **Token de Autenticação (opcional)**. Se esse campo for deixado em branco, o Azure AD incluirá um token de portador OAuth emitido do Azure AD com cada solicitação. Aplicativos que usam o Azure AD como um provedor de identidade podem validar esse token emitido pelo Azure AD.
4. Clique em **Avançar** e clique no botão **Iniciar Teste** para que o Active Directory do Azure tente conectar ao ponto de extremidade do SCIM. Se as tentativas falharem, serão exibidas informações de diagnóstico.
5. Se as tentativas de conexão com o aplicativo forem bem-sucedidas, clique em **Próximo** nas telas restantes e em **Concluir** para sair da caixa de diálogo.
6. Na tela resultante, selecione o terceiro botão **Atribuir Contas**. Na seção resultante Usuários e Grupos, atribua os usuários ou grupos que deseja provisionar ao aplicativo.
7. Depois de atribuir usuários e grupos, clique na guia **Configurar** próxima à parte superior da tela.
8. Em **Provisionamento de Conta**, confirme se o Status está definido como Ativado.
9. Em **Ferramentas**, clique em **Reiniciar provisionamento de conta** para iniciar o processo de provisionamento.

Observe que podem decorrer de 5 a 10 minutos antes que o processo de provisionamento comece a enviar solicitações ao ponto de extremidade SCIM. Um resumo das tentativas de conexão é fornecido na guia Painel do aplicativo, e um relatório da atividade de provisionamento e todos os erros de provisionamento podem ser baixados na guia Relatórios do diretório.

## Criando sua própria solução de provisionamento para qualquer aplicativo
Ao criar um serviço Web SCIM que interaja com o Active Directory do Azure, você poderá habilitar o logon único e o provisionamento de usuário automático para praticamente qualquer aplicativo que forneça uma API de provisionamento de usuário SOAP ou REST.

Veja como ele funciona:

1. O AD do Azure fornece uma biblioteca CLI denominada [Microsoft.SystemForCrossDomainIdentityManagement](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/). Os desenvolvedores e integradores de sistema podem usar essa biblioteca para criar e implantar um ponto de extremidade de serviço Web baseado em SCIM capaz de conectar o AD do Azure ao repositório de identidades de qualquer aplicativo.
2. Os mapeamentos são implementados no serviço Web para mapear o esquema de usuário padronizado para o esquema de usuário e o protocolo exigido pelo aplicativo.
3. A URL do ponto de extremidade é registrada no AD do Azure como parte de um aplicativo personalizado na galeria de aplicativos.
4. Os usuários e grupos são atribuídos a esse aplicativo no AD do Azure. Na atribuição, eles são colocados em uma fila para serem sincronizados com o aplicativo de destino. O processo de sincronização que trata a fila é executado a cada 5 minutos.

### Exemplos de código
Para facilitar esse processo, será fornecido um conjunto de [exemplos de código](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) que cria um ponto de extremidade de serviço Web SCIM e demonstra o provisionamento automático. Um dos exemplos é de um provedor que mantém um arquivo com linhas de valores separados por vírgula representando usuários e grupos. O outro é de um provedor que opera no serviço de Gerenciamento de Acesso e Identidade do Amazon Web Services.

**Pré-requisitos**

* Visual Studio 2013 ou posterior.
* [SDK do Azure para .NET](https://azure.microsoft.com/downloads/)
* Computador com Windows que ofereça suporte à estrutura ASP.NET 4.5 a ser usado como o ponto de extremidade SCIM. Esse computador deve poder ser acessado da nuvem
* [Uma assinatura do Azure com uma versão de avaliação ou licenciada do Azure AD Premium](https://azure.microsoft.com/services/active-directory/)
* O exemplo do Amazon AWS requer bibliotecas do [Kit de Ferramentas do AWS para Visual Studio](http://docs.aws.amazon.com/AWSToolkitVS/latest/UserGuide/tkv_setup.html). Consulte o arquivo LEIAME incluído no exemplo para obter mais detalhes

### Introdução
A maneira mais fácil de implementar um ponto de extremidade SCIM que possa aceitar solicitações de provisionamento do AD do Azure é criando e implantando o exemplo de código que gera os usuários provisionados em um arquivo CSV (valores separados por vírgula).

**Para criar um exemplo de ponto de extremidade SCIM:**

1. Baixe o pacote de exemplo de códigos de [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
2. Descompacte o pacote e coloque-o no seu computador com Windows em um local como C:\\AzureAD-BYOA-Provisioning-Samples.
3. Nessa pasta, inicie a solução FileProvisioningAgent no Visual Studio.
4. Selecione **Ferramentas > Gerenciador de Pacotes de Biblioteca > Console do Gerenciador de Pacotes** e execute os comandos abaixo para o projeto FileProvisioningAgent resolver as referências de solução:
   
   Install-Package Microsoft.SystemForCrossDomainIdentityManagement Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory Install-Package Microsoft.Owin.Diagnostics Install-Package Microsoft.Owin.Host.SystemWeb
5. Compile o projeto FileProvisioningAgent.
6. Inicie o aplicativo Prompt de Comando no Windows (como administrador) e use o comando **cd** para alterar o diretório para a sua pasta **\\AzureAD-BYOA-Provisioning-Samples\\ProvisioningAgent\\bin\\Debug**.
7. Execute o comando abaixo, substituindo <ip-address> pelo IP ou pelo nome de domínio do computador com Windows.
   
   FileAgnt.exe http://<endereço-ip>:9000 TargetFile.csv
8. No Windows, em **Configurações do Windows > Configurações de Rede e Internet**, selecione o **Firewall do Windows > Configurações Avançadas** e crie uma **Regra de Entrada** que permita acesso de entrada na porta 9000.
9. Se o computador com Windows estiver atrás de um roteador, precisará ser configurado para executar a Network Access Translation entre sua porta 9000 exposta à Internet e a porta 9000 no computador com Windows. Isso é necessário para que o AD do Azure possa acessar esse ponto de extremidade na nuvem.

**Para registrar o exemplo de ponto de extremidade SCIM no AD do Azure:**

1. Em um navegador da Web, inicie o portal de gerenciamento do Azure em https://manage.windowsazure.com.
2. Navegue até **Active Directory > Diretório > [seu diretório] > Aplicativos** e selecione **Adicionar > Adicionar um aplicativo da galeria**.
3. Selecione a guia **Personalizado** à esquerda, insira um nome, como "Aplicativo de teste do SCIM", e clique no ícone de marca de seleção para criar um objeto de aplicativo. Observe que o objeto de aplicativo criado destina-se a representar o aplicativo de destino para o qual você estará provisionando e implementando o logon único, e não apenas o ponto de extremidade SCIM.

![][2]

1. Na tela resultante, selecione o segundo botão **Configurar provisionamento da conta**.
2. Na caixa de diálogo, insira a URL exposta à Internet e a porta do seu ponto de extremidade SCIM. Isso seria algo como http://testmachine.contoso.com:9000 ou http://<ip-address>:9000/, em que <ip-address> é o endereço IP exposto na Internet.
3. Clique em **Avançar** e clique no botão **Iniciar Teste** para que o Active Directory do Azure tente conectar ao ponto de extremidade do SCIM. Se as tentativas falharem, serão exibidas informações de diagnóstico.
4. Se as tentativas de conexão com o serviço Web forem bem-sucedidas, clique em **Avançar** nas telas restantes e em **Concluir** para sair da caixa de diálogo.
5. Na tela resultante, selecione o terceiro botão **Atribuir Contas**. Na seção resultante Usuários e Grupos, atribua os usuários ou grupos que deseja provisionar ao aplicativo.
6. Depois de atribuir usuários e grupos, clique na guia **Configurar** próxima à parte superior da tela.
7. Em **Provisionamento de Conta**, confirme se o Status está definido como Ativado.
8. Em **Ferramentas**, clique em **Reiniciar provisionamento de conta** para iniciar o processo de provisionamento.

Observe que podem decorrer de 5 a 10 minutos antes que o processo de provisionamento comece a enviar solicitações ao ponto de extremidade SCIM. Um resumo das tentativas de conexão é fornecido na guia Painel do aplicativo, e um relatório da atividade de provisionamento e todos os erros de provisionamento podem ser baixados na guia Relatórios do diretório.

A etapa final da verificação do exemplo é abrir o arquivo TargetFile.csv da pasta \\AzureAD-BYOA-Provisioning-Samples\\ProvisioningAgent\\bin\\Debug no seu computador com Windows. Depois que o processo de provisionamento é executado, esse arquivo mostra os detalhes de todos os usuários e grupos provisionados e atribuídos.

### Bibliotecas de desenvolvimento
Para desenvolver seu próprio serviço Web em conformidade com a especificação do SCIM, em primeiro lugar, familiarize-se com as seguintes bibliotecas fornecidas pela Microsoft, que ajudam a acelerar o processo de desenvolvimento:

**1:** as bibliotecas Common Language Infrastructure são oferecidas para uso com linguagens que se baseiam na infraestrutura em questão, como C#. Uma dessas bibliotecas, [Microsoft.SystemForCrossDomainIdentityManagement.Service](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/), declara uma interface, Microsoft.SystemForCrossDomainIdentityManagement.IProvider, mostrada na figura abaixo. Um desenvolvedor que usa as bibliotecas implementa essa interface com uma classe que pode ser referenciada, de modo genérico, como um provedor. As bibliotecas permitem ao desenvolvedor implantar facilmente um serviço Web em conformidade com a especificação do SCIM, seja hospedado nos Serviços de Informações da Internet ou em qualquer assembly executável da Common Language Infrastructure. As solicitações feitas a esse serviço Web serão convertidas em chamadas aos métodos do provedor, que podem ser programadas pelo desenvolvedor para operar em algum repositório de identidades.

![][3]

**2:** os [Manipuladores de rota expressa](http://expressjs.com/guide/routing.html) estão disponíveis para análise de objetos de solicitação node.js que representam chamadas (como definido pela especificação do SCIM) feitas para um serviço Web node.js.

### Criando um ponto de extremidade SCIM personalizado
Usando as bibliotecas descritas acima, os desenvolvedores podem hospedar seus serviços em qualquer assembly executável da Common Language Infrastructure ou nos Serviços de Informações da Internet. Veja um exemplo de código para hospedar um serviço em um assembly executável no endereço http://localhost:9000:

    private static void Main(string[] arguments)
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
      new DevelopersMonitor();
    Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
      new DevelopersProvider(arguments[1]);
    Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
    try
    {
        webService = new WebService(monitor, provider);
        webService.Start("http://localhost:9000");

        Console.ReadKey(true);
    }
    finally
    {
        if (webService != null)
        {
            webService.Dispose();
            webService = null;
        }
    }
    }

    public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
    {
    private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
    private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

    public WebService(
      Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
      Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
    {
        this.monitor = monitoringBehavior;
        this.provider = providerBehavior;
    }

    public override IMonitor MonitoringBehavior
    {
        get
        {
            return this.monitor;
        }

        set
        {
            this.monitor = value;
        }
    }

    public override IProvider ProviderBehavior
    {
        get
        {
            return this.provider;
        }

        set
        {
            this.provider = value;
        }
    }
    }

É importante observar que esse serviço deve ter um endereço HTTP e um certificado de autenticação de servidor do qual a autoridade de certificação raiz é uma destas:

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* Verisign
* WoSign

Um certificado de autenticação de servidor pode ser associado a uma porta em um host do Windows usando o utilitário de shell de rede, da seguinte forma:

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  

Aqui, o valor fornecido para o argumento certhash é a impressão digital do certificado, enquanto o valor fornecido para o argumento appid é um identificador global exclusivo arbitrário.

Para hospedar o serviço nos Serviços de Informações da Internet, um desenvolvedor cria um assembly de biblioteca de códigos da Common Language Infrastructure com uma classe chamada Startup no namespace padrão do assembly. Veja um exemplo dessa classe:

    public class Startup
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

    public Startup()
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
          new DevelopersMonitor();
        Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
          new DevelopersProvider();
        this.starter = 
          new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
            provider, 
            monitor);
    }

    public void Configuration(
      Owin.IAppBuilder builder) // Defined in in Owin.dll.  
    {
        this.starter.ConfigureApplication(builder);
    }
    }

### Manipulando a autenticação do ponto de extremidade
As solicitações do Active Directory do Azure incluem um token de portador do OAuth 2.0. Qualquer serviço que recebe a solicitação deve autenticar o emissor como sendo o Active Directory do Azure em nome do locatário esperado do Active Directory do Azure, para acesso ao serviço Web Graph do Active Directory do Azure. No token, o emissor é identificado por uma declaração de iss, como "iss": "https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/". Neste exemplo, o endereço base do valor da declaração, https://sts.windows.net, identifica o Active Directory do Azure como o emissor, enquanto o segmento do endereço relativo, cbb1a5ac-f33b-45fa-9bf5-f37db0fed422, é um identificador exclusivo do locatário do Active Directory do Azure em nome do qual o token foi emitido. Se o token tiver sido emitido para acessar um serviço Web Graph do Active Directory do Azure, o identificador desse serviço, 00000002-0000-0000-c000-000000000000, deverá estar no valor da declaração aud do token.

Os desenvolvedores que usam as bibliotecas Common Language Infrastructure fornecidas pela Microsoft para criação de um serviço SCIM podem autenticar solicitações do Active Directory do Azure usando o pacote Microsoft.Owin.Security.ActiveDirectory seguindo estas etapas:

**1:** em um provedor, implemente a propriedade Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior fazendo com que ela retorne um método a ser chamado sempre que o serviço é iniciado:

    public override Action<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration> StartupBehavior
    {
      get
      {
        return this.OnServiceStartup;
      }
    }

    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
      System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
    {
    }

**2:** adicione o código a seguir ao método para que as solicitações a qualquer um dos pontos de extremidade do serviço sejam autenticados como sendo um token emitido pelo Active Directory do Azure em nome de um locatário especificado para acesso ao serviço Web Graph do Active Directory do Azure:

    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
      System.Web.Http.HttpConfiguration HttpConfiguration configuration)
    {
      // IFilter is defined in System.Web.Http.dll.  
      System.Web.Http.Filters.IFilter authorizationFilter = 
        new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

      // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
      // System.IdentityModel.Token.Jwt.dll.
      SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
        new TokenValidationParameters()
        {
          ValidAudience = "00000002-0000-0000-c000-000000000000"
        };

      // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
      // Microsoft.Owin.Security.ActiveDirectory.dll
      Microsoft.Owin.Security.ActiveDirectory.
      WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
        new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
        TokenValidationParameters = tokenValidationParameters,
        Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                      // identifier for this one.  
      };

      applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
    }

## Esquema de usuários e grupos
O Active Directory do Azure pode provisionar dois tipos de recurso aos serviços Web SCIM. Esses tipos de recurso são usuários e grupos.

Os recursos de usuário são identificados pelo identificador do esquema, urn:ietf:params:scim:schemas:extension:enterprise:2.0:User, que está incluído nesta especificação de protocolo: http://tools.ietf.org/html/draft-ietf-scim-core-schema. O mapeamento padrão dos atributos de usuários no Active Directory do Azure para os atributos dos recursos urn:ietf:params:scim:schemas:extension:enterprise:2.0:User é fornecido na tabela 1, abaixo.

Os recursos de grupo são identificados pelo identificador do esquema, http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group. A Tabela 2 abaixo mostra o mapeamento padrão dos atributos de grupos do Active Directory do Azure para os atributos dos recursos http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.

### Tabela 1: Mapeamento padrão de atributo do usuário
| Usuário do Active Directory do Azure | urn:ietf:params:scim:schemas:extension:enterprise:2.0:User |
| --- | --- |
| IsSoftDeleted |ativo |
| displayName |displayName |
| Facsimile-TelephoneNumber |phoneNumbers[type eq "fax"].value |
| givenName |name.givenName |
| jobTitle |título |
| mail |emails[type eq "work"].value |
| mailNickname |externalId |
| manager |manager |
| Serviço Móvel |phoneNumbers[type eq "mobile"].value |
| ID do objeto |ID |
| postalCode |addresses[type eq "work"].postalCode |
| proxy-Addresses |emails[type eq "other"].Value |
| physical-Delivery-OfficeName |addresses[type eq "other"].Formatted |
| streetAddress |addresses[type eq "work"].streetAddress |
| sobrenome |name.familyName |
| telephone-Number |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |

### Tabela 2: Mapeamento padrão de atributo do grupo
| Grupo do Active Directory do Azure | http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group |
| --- | --- |
| displayName |externalId |
| mail |emails[type eq "work"].value |
| mailNickname |displayName |
| membros |membros |
| ID do objeto |ID |
| proxyAddresses |emails[type eq "other"].Value |

## Provisionamento e desprovisionamento de usuários
A figura abaixo mostra as mensagens que o Active Directory do Azure enviará a um serviço SCIM para gerenciar o ciclo de vida de um usuário em outro repositório de identidades. O diagrama também mostra como um serviço SCIM implementado usando as bibliotecas Common Language Infrastructure fornecidas pela Microsoft para a criação de tais serviços converterá as solicitações em chamadas para os métodos de um provedor.

![][4]*Figura: sequência de provisionamento e desprovisionamento de usuário*

**1:** o Active Directory do Azure consultará o serviço para procurar um usuário com um valor de atributo externalId correspondente ao valor de atributo mailNickname de um usuário no Active Directory do Azure. A consulta será expressa como uma solicitação HTTP como a que se segue, na qual jyoung é o exemplo de um mailNickname de um usuário no Active Directory do Azure:

    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...

Se o serviço foi criado usando as bibliotecas Common Language Infrastructure fornecidas pela Microsoft para implementação de serviços SCIM, a solicitação será convertida em uma chamada ao método Query do provedor de serviços. Veja a assinatura desse método:

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
      Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
      string correlationIdentifier);

Veja a definição da interface Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters:

    public interface IQueryParameters: 
      Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
        System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
        { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
      system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
      { get; }
      System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
      { get; }
      string SchemaIdentifier 
      { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
          { get; set; }
        string AttributePath 
          { get; } 
        Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
          { get; }
        string ComparisonValue 
          { get; }
    }

    public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
    {
        Equals
    }

No caso do exemplo acima de uma consulta para um usuário com um valor fornecido para o atributo externalId, os valores dos argumentos passados para o método Query serão:

* parameters.AlternateFilters.Count: 1
* parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
* parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"
* correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin.RequestId"]

**2:** se a resposta a uma consulta ao serviço para procurar um usuário com um valor de atributo externalId correspondente ao valor de atributo mailNickname de um usuário no Active Directory do Azure não retornar nenhum usuário, o Active Directory do Azure solicitará que o serviço provisione um usuário correspondente ao usuário no Active Directory do Azure. Veja um exemplo de tal solicitação:

    POST https://.../scim/Users HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas":
      [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
      "externalId":"jyoung",
      "userName":"jyoung",
      "active":true,
      "addresses":null,
      "displayName":"Joy Young",
      "emails": [
        {
          "type":"work",
          "value":"jyoung@Contoso.com",
          "primary":true}],
      "meta": {
        "resourceType":"User"},
       "name":{
        "familyName":"Young",
        "givenName":"Joy"},
      "phoneNumbers":null,
      "preferredLanguage":null,
      "title":null,
      "department":null,
      "manager":null}

As bibliotecas Common Language Infrastructure fornecidas pela Microsoft para implementação dos serviços SCIM convertem essa solicitação em uma chamada ao método Create do provedor de serviços. O método Create tem essa assinatura:

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
      Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
      string correlationIdentifier);

No caso de uma solicitação para provisionar um usuário, o valor do argumento de recurso será uma instância da classe Microsoft.SystemForCrossDomainIdentityManagement. Core2EnterpriseUser, definida na biblioteca Microsoft.SystemForCrossDomainIdentityManagement.Schemas. Se a solicitação de provisionamento de usuário for bem-sucedida, a implementação do método deverá retornar uma instância da classe Microsoft.SystemForCrossDomainIdentityManagement. Classe Core2EnterpriseUser, com o valor da propriedade Identifier definido como o identificador exclusivo do usuário recentemente provisionado.

**3:** para atualizar um usuário conhecido que existe em um repositório de identidades administrado por um SCIM, o Active Directory do Azure prosseguirá solicitando o estado atual desse usuário no serviço com uma solicitação parecida com esta:

    GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...

Em um serviço criado usando as bibliotecas Common Language Infrastructure fornecidas pela Microsoft para implementação de serviços SCIM, a solicitação será convertida em uma chamada ao método Retrieve do provedor de serviços. Veja a assinatura do método Retrieve:

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource and 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
    // are defined in Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> 
       Retrieve(
         Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
           parameters, 
           string correlationIdentifier);

    public interface 
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
        IRetrievalParameters
        {
          Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
            ResourceIdentifier 
              { get; }
    }
    public interface Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier
    {
        string Identifier 
          { get; set; }
        string Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier 
          { get; set; }
    }

No caso do exemplo acima de uma solicitação para recuperar o estado atual de um usuário, os valores das propriedades do objeto fornecidos como o valor do argumento de parâmetros serão:

* Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

**4:** se um atributo de referência deve ser atualizado, o Active Directory do Azure consultará o serviço para determinar se o valor atual do atributo de referência no repositório de identidades administrado pelo serviço já corresponde ou não ao valor desse atributo no Active Directory do Azure. No caso de usuários, o único atributo no qual o valor atual será consultado dessa forma é o atributo de gerenciador. Veja o exemplo de uma solicitação para determinar se o atributo de gerenciador de um objeto de usuário específico atualmente tem um determinado valor:

    GET ~/scim/Users?filter=id eq 54D382A4-2050-4C03-94D1-E769F1D15682 and manager eq 2819c223-7f76-453a-919d-413861904646&attributes=id HTTP/1.1
    Authorization: Bearer ...

O valor do parâmetro de consulta de atributos, id, significa que, se existir um objeto de usuário que atenda à expressão fornecida como o valor do parâmetro de consulta de filtro, o serviço deverá responder com um recurso urn:ietf:params:scim:schemas:core:2.0:User ou urn:ietf:params:scim:schemas:extension:enterprise:2.0:User, incluindo apenas o valor do atributo id desse recurso. Obviamente, o valor do atributo id é conhecido ao solicitante — ele é incluído no valor do parâmetro de consulta de filtro; a finalidade de solicitá-lo, na verdade, é solicitar uma representação mínima de um recurso que atenda à expressão de filtro como uma indicação da existência ou não de tal objeto.

Se o serviço foi criado usando as bibliotecas Common Language Infrastructure fornecidas pela Microsoft para implementação de serviços SCIM, a solicitação será convertida em uma chamada ao método Query do provedor de serviços. O valor das propriedades do objeto fornecido como o valor do argumento de parâmetros será:

* parameters.AlternateFilters.Count: 2
* parameters.AlternateFilters.ElementAt(x).AttributePath: "id"
* parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
* parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(y).ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
* parameters.RequestedAttributePaths.ElementAt(0): "id"
* parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

Aqui, o valor do índice x pode ser 0 e o valor do índice y pode ser 1, ou o valor de x pode ser 1 e o valor de y pode ser 0, dependendo da ordem das expressões do parâmetro do filtro de consulta.

**5:** veja um exemplo de uma solicitação do Active Directory do Azure a um serviço SCIM para atualizar um usuário:

    PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas": 
      [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
      "Operations":
      [
        {
          "op":"Add",
          "path":"manager",
          "value":
            [
              {
                "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
                "value":"2819c223-7f76-453a-919d-413861904646"}]}]}

As bibliotecas Common Language Infrastructure fornecidas pela Microsoft para implementação dos serviços SCIM convertem a solicitação em uma chamada ao método Update do provedor de serviços. Veja a assinatura desse método:

    // System.Threading.Tasks.Tasks and 
    // System.Collections.Generic.IReadOnlyCollection<T>
    // are defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
    // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

    System.Threading.Tasks.Task Update(
      Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
      string correlationIdentifier);

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
    {
    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
      PatchRequest 
        { get; set; }
    Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
      ResourceIdentifier 
        { get; set; }        
    }

    public class PatchRequest2: 
      Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
    {
    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
        Operations
        { get;}

    public void AddOperation(
      Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
    }

    public class PatchOperation
    {
    public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
      Name
      { get; set; }

    public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
      Path
      { get; set; }

    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
      { get; }

    public void AddValue(
      Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
    }

    public enum OperationName
    {
      Add,
      Remove,
      Replace
    }

    public interface IPath
    {
      string AttributePath { get; }
      System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
      Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
    }

    public class OperationValue
    {
      public string Reference
      { get; set; }

      public string Value
      { get; set; }
    }



No caso do exemplo acima de uma solicitação para atualizar um usuário, o objeto fornecido como o valor do argumento de patch terá estes valores de propriedade:

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
* (PatchRequest as PatchRequest2).Operations.Count: 1
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).OperationName: OperationName.Add
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Path.AttributePath: "manager"
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.Count: 1
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Reference: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Value: 2819c223-7f76-453a-919d-413861904646

**6:** para desprovisionar um usuário de um repositório de identidades administrado por um serviço SCIM, o Active Directory do Azure enviará uma solicitação como esta:

    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...

Se o serviço tiver sido criado usando as bibliotecas Common Language Infrastructure fornecidas pela Microsoft para implementação de serviços SCIM, a solicitação será convertida em uma chamada ao método Delete do provedor de serviços. Esse método tem esta assinatura:

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
    System.Threading.Tasks.Task Delete(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
        resourceIdentifier, 
      string correlationIdentifier);

O objeto fornecido como o valor do argumento resourceIdentifier terá estes valores de propriedade, no caso do exemplo acima de uma solicitação para desprovisionar um usuário:

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## Provisionamento e desprovisionamento de grupo
A figura abaixo mostra as mensagens que o Active Directory do Azure enviará a um serviço SCIM para gerenciar o ciclo de vida de um grupo em outro repositório de identidades. Essas mensagens são diferentes das mensagens pertencentes aos usuários em três aspectos:

* O esquema de um recurso de grupo será identificado como http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.
* As solicitações para recuperar grupos vão estipular que o atributo de membros deve ser excluído de qualquer recurso fornecido em resposta à solicitação.
* As solicitações para determinar se um atributo de referência tem um determinado valor serão sobre o atributo de membros.

![][5]*Figura: sequência de provisionamento e desprovisionamento de grupo*

## Artigos relacionados
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)
* [Automatizar o provisionamento/desprovisionamento de usuários para aplicativos SaaS](active-directory-saas-app-provisioning.md)
* [Personalizando os mapeamentos de atributos para provisionamento de usuários](active-directory-saas-customizing-attribute-mappings.md)
* [Escrevendo expressões para mapeamentos de atributo](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Filtros de escopo para provisionamento de usuários](active-directory-saas-scoping-filters.md)
* [Notificações de provisionamento de conta](active-directory-saas-account-provisioning-notifications.md)
* [Lista de tutoriais sobre como integrar aplicativos SaaS](active-directory-saas-tutorial-list.md)

<!--Image references-->
[1]: ./media/active-directory-scim-provisioning/scim-figure-1.PNG
[2]: ./media/active-directory-scim-provisioning/scim-figure-2.PNG
[3]: ./media/active-directory-scim-provisioning/scim-figure-3.PNG
[4]: ./media/active-directory-scim-provisioning/scim-figure-4.PNG
[5]: ./media/active-directory-scim-provisioning/scim-figure-5.PNG

<!---HONumber=AcomDC_0928_2016-->