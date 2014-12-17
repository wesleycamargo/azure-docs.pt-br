#Guia de segurança do Azure

##Resumo

Ao desenvolver aplicativos do Azure, identidade e
acesso são as principais preocupações sobre segurança que você precisa ter em mente.
Este tópico explica as questões de segurança relacionadas a identidades e acesso na nuvem e como você
pode proteger melhor os seus aplicativos em nuvem.

##Visão geral

A segurança do aplicativo é uma função de sua superfície. Quanto mais superfície
o aplicativo expõe, maiores as preocupações com segurança. Por
exemplo, um aplicativo que é executado como um processo em lote autônomo expõe
menos, de uma perspectiva de segurança, que um site disponível publicamente.

Ao mover para a nuvem, você obtém uma determinada paz de espírito em relação à
infraestrutura e rede, pois eles são gerenciados em data centers
com práticas recomendadas de segurança de classe mundial, ferramentas, tecnologia. Por outro
lado, a nuvem intrinsecamente expõe mais área de superfície para o
aplicativo que pode ser potencialmente explorado por invasores. Isso ocorre
porque muitas tecnologias de nuvem e serviços são expostos como pontos de extremidade
em relação aos componentes de memória. Armazenamento do Azure, barramento de serviço, SQL
Banco de dados (antigo SQL Azure) e muitos outros serviços estão disponíveis
por meio de seus pontos de extremidade durante a transmissão.

Em aplicativos de nuvem, há mais responsabilidade para os
desenvolvedores de aplicativos para projetar, desenvolver e manter seus aplicativos
na nuvem com padrões de alta segurança para manter os invasores afastados.
Considere o seguinte diagrama (da [Segurança do Azure de J.D. Meier
Observações em PDF](http://blogs.msdn.com/b/jmeier/archive/2010/08/03/now-available-azure-security-notes-pdf.aspx)): Observe como a parte de infraestrutura está sendo abordada pelo
provedor de nuvem ― no nosso caso, o Azure ― deixando mais trabalho de segurança
para os desenvolvedores de aplicativo:

![Securing the application][01]

A boa notícia é todas as práticas desenvolvimento de segurança,
princípios e técnicas que você já conhece ainda se aplicam ao desenvolver
aplicativos em nuvem. Considere as seguintes áreas principais que devem ser
abordadas:

-   Toda entrada é validada quanto ao tipo, tamanho, intervalo e padrões de cadeia de caracteres
    para evitar ataques de injeção, e todos os dados que seu aplicativo retorna são
    depurados adequadamente.
-   Os dados confidenciais devem ser protegidos em repouso e durante a transmissão para
    atenuar as ameaças de violação de dados e divulgação de informações.
-   A auditoria e o log devem ser corretamente implementados para atenuar as ameaças
    de não repúdio.
-   A autenticação e a autorização devem ser implementadas usando mecanismos
    comprovados oferecidos pela plataforma para evitar falsificação de identidade e
    aumento de ameaças de privilégios.

Para obter uma lista completa de ameaças, ataques, vulnerabilidades e
medidas, consulte os padrões e práticas da [Planilha de trapaça: Web
Quadro de segurança de aplicativo](http://msdn.microsoft.com/pt-br/library/ff649461.aspx) e [Orientação de segurança para índice de aplicativos](http://msdn.microsoft.com/pt-br/library/ff650760.aspx).

Na nuvem, mecanismos de controle de acesso e autenticação são muito
diferentes daqueles disponíveis para aplicativos locais. Há
muito mais opções de autenticação e acesso oferecidas para os aplicativos de nuvem
que podem causar confusão e, como resultado, a implementações
com falhas. Acontece mais confusão quando se define o que é um aplicativo
em nuvem. Por exemplo, o aplicativo poderia ser implantado na nuvem mesmo que
seu mecanismo de autenticação pudesse ser fornecido pelo Active Directory. Por
outro lado, o aplicativo pode ser implantado no local, mas com
mecanismos de autenticação na nuvem (por exemplo, pelo Azure
Controle de Acesso do Active Directory (anteriormente conhecido como Serviço de Controle de Acesso
ou ACS)).

##Ameaças, vulnerabilidades e ataques

Uma ameaça é um resultado potencial incorreto que você deseja evitar, como a
divulgação de informações confidenciais ou um serviço se tornando indisponível.
É uma prática comum classificar as ameaças usando o acrônimo "STRIDE":

-   **F**alsificação ou roubo de identidade
-   **M**anipulação de dados
-   **R**epúdio de ações
-   **R**evelação de informações
-   **N**egação de serviço
-   **E**elevação de privilégios

Vulnerabilidades são erros que nós, como desenvolvedores, inserimos no código
que tornam um aplicativo passível de ser explorado por invasores. Por exemplo, enviar
dados confidenciais em texto torna possível uma ameaça de divulgação de informações
por um ataque de detecção de tráfego.

Os ataques são a exploração dessas vulnerabilidades para causar danos
a um aplicativo. Por exemplo, um script entre sites ou XSS, é um
ataque que explora saída não corrigida. Outro exemplo é
espionagem de rede para capturar as credenciais enviadas em formato de texto não criptografado. Esses
ataques podem levar à realização de uma ameaça de falsificação de identidade. Para
simplificar, considere ameaças, vulnerabilidades e ataques como coisas ruins.
Considere os diagramas a seguir como um modo de exibição das coisas ruins
relacionados a um aplicativo Web implantado no Azure (
do [PDF de observações de segurança do Azure](http://blogs.msdn.com/b/jmeier/archive/2010/08/03/now-available-azure-security-notes-pdf.aspx) de J.D. Meier):

![Threats Vulnerabilities and Attacks][02]

Você, como desenvolvedor, tem controle sobre as vulnerabilidades. Quanto menos
vulnerabilidades você inserir, menos opções restam para que o
invasor explore.

Vulnerabilidades relacionadas a identidade e acesso deixam você aberto a todas as
ameaças no modelo STRIDE. Por exemplo, um mecanismo de autenticação implementado de forma imprópria
pode levar a uma falsificação da identidade e, como resultado,
divulgação de informações, violação de dados, operações de privilégios elevados
ou até mesmo encerrar completamente o serviço. Considere as seguintes
perguntas que podem apontar para possíveis vulnerabilidades na sua implementação
de acesso e identidade do aplicativo na nuvem:

-   Você está enviando credenciais em formato de texto não criptografado durante a transmissão
    para os serviços do Azure?
-   Você armazena credenciais de serviços do Azure em formato de texto não criptografado?
-   Suas credenciais de serviços do Azure seguem as diretivas
    de senha forte?
-   Você utiliza o Azure para verificar credenciais versus
    uso de mecanismos de verificação personalizada?
-   Você limita as sessões de autenticação de serviços do Azure ou tempo de vida do token
    para uma janela de tempo razoável?
-   Você verifica permissões para cada ponto de entrada do Azure do seu
    aplicativo em nuvem distribuído?
-   Os mecanismos de autorização falham com segurança sem expor
    informações confidenciais, ou sem permitir acesso ilimitado?

##Contramedidas

A melhor medida contra um ataque é o uso de mecanismos de identidade e
acesso oferecidos pela plataforma em vez de implementar sua
própria. Considere as seguintes tecnologias de acesso e identidades de destaque:

**Windows Identity Foundation (WIF).** O WIF é uma biblioteca de tempo de execução do .NET
incluído com o .NET Framework 4.5 (ele também está disponível como download separado
do .NET 3.5/4.0). O WIF faz o trabalho pesado para o tratamento
de protocolos como Web Services Federation e WS-Trust e manipulação de tokens, como
Security Assertion Markup Language (SAML) para que você não precise escrever
códigos muito complexos relacionados à segurança em seu aplicativo. Os seguintes
recursos fornecem informações detalhadas sobre o WIF:

-   [Exemplos do Windows Identity Foundation 4.5](http://code.msdn.microsoft.com/site/search?f%5B0%5D.Type=SearchText&f%5B0%5D.Value=wif&f%5B1%5D.Type=Topic&f%5B1%5D.Value=claims-based%20authentication) na MSDN Code Gallery.
-   [Ferramentas do Windows Identity Foundation 4.5 para o Visual Studio 11 Beta](http://visualstudiogallery.msdn.microsoft.com/e21bf653-dfe1-4d81-b3d3-795cb104066e) na
    MSDN Code Gallery.
-   [Tempo de execução do Windows Identity Foundation (.Net 3.5/4.0)](http://www.microsoft.com/pt-br/download/details.aspx?id=17331) no MSDN.
-   [Exemplos do Windows Identity Foundation 3.5/4.0 e modelos do Visual Studio
    2008/2010](http://www.microsoft.com/pt-br/download/details.aspx?displaylang=en&id=4451) no MSDN.

**Serviço de Controle de Acesso do Azure AD(anteriormente conhecido como ACS)**. 
O Serviço de Controle de Acesso do Azure AD é um serviço de nuvem que fornece um serviço de token
de segurança (STS) e permite a federação com diferentes provedores de identidade
(IdPs) como um Active Directory corporativo ou IdPs de Internet, como
Windows Live ID/conta da Microsoft, Facebook, Google, Yahoo! e
provedores de identidade do Open ID 2.0. Os seguintes recursos fornecem informações detalhadas
sobre o Serviço de Controle de Acesso do Azure AD:

-   [Serviço de Controle de Acesso 2.0](http://msdn.microsoft.com/library/gg429786.aspx) 
-   [Cenários e soluções usando o ACS](http://msdn.microsoft.com/pt-br/library/gg185920.aspx)
-   [Instruções do ACS](http://msdn.microsoft.com/pt-br/library/windowsazure/gg185939.aspx)
-   [Um guia para Identidade baseada em declarações e Access Control (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/ff423674.aspx)
-   [Kit de treinamento de identidade para desenvolvedores (a página pode estar em inglês)](http://www.microsoft.com/pt-br/download/details.aspx?id=14347)
-   [Curso de treinamento de identidade para desenvolvedores hospedado pelo MSDN (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/IdentityTrainingCourse)

**Serviços de Federação do Active Directory(AD FS).**Serviços de Federação do
Active Directory (AD FS) 2.0 dá suporte para soluções de identidade
de reconhecimento de declarações que envolvam o Windows Server e a tecnologia do Active Directory
. O AD FS 2.0 dá suporte ao WS-Trust, Web Services Federation e protocolos
SAML. Os recursos a seguir fornecem informações detalhadas sobre o AD
FS:

-   [Mapa de conteúdo do AD FS 2.0 (a página pode estar em inglês)](http://social.technet.microsoft.com/wiki/contents/articles/2735.ad-fs-2-0-content-map.aspx)
-   [Web SSO Design][Web SSO Design]
-   [Federated Web SSO Design][Federated Web SSO Design]

**Assinaturas de Acesso Compartilhado do Azure.** As Assinaturas de Acesso Compartilhado
permitem ajustar o acesso a um recurso de contêiner ou blob. Os
recursos a seguir fornecem informações detalhadas sobre as Assinaturas
de Acesso Compartilhado.

-   [Gerenciando acesso a Blobs e Contêineres](http://msdn.microsoft.com/pt-br/library/ee393343.aspx)
-   [Recurso de Novo Armazenamento: Assinaturas de Acesso Compartilhado](http://blog.smarx.com/posts/new-storage-feature-signed-access-signatures)
-   [Assinaturas de Acesso Compartilhado são fáceis hoje em dia (a página pode estar em inglês)](http://blog.smarx.com/posts/shared-access-signatures-are-easy-these-days)

##Mapa de cenários

Esta seção descreve brevemente os principais cenários abordados neste tópico.
Use-o como um mapa para determinar a solução de identidade certa para seu
aplicativo.

-   **Aplicativo Web Form do ASP.NET com Autenticação Federada.** Nesse
    cenário você controla o acesso ao seu aplicativo Web Forms do ASP.NET usando
    uma identidade de Internet, como o Live ID/conta da Microsoft, ou
    identidade corporativa gerenciada no Active Directory do Windows Server.
-   **Serviço do WCF (SOAP) com autenticação federada.**Nesse
    cenário, você controla o acesso ao serviço WCF (SOAP) usando as Identidades de
    Serviço gerenciadas pelo Serviço de Controle de Acesso do Azure AD.
-   **Serviço do WCF (SOAP) com autenticação federada. Identidades no
    Active Directory.** Neste cenário, você controla o acesso ao serviço Web do
    WCF (SOAP) usando identidades gerenciadas pelo Active Directory do Windows
    Server corporativo.
-   **Serviço do WCF (REST) com autenticação federada.**Nesse
    cenário, você controla o acesso ao serviço WCF (REST) usando as Identidades de
    Serviço gerenciadas pelo Serviço de Controle de Acesso do Azure AD.
-   **Serviço WCF (REST) com Live ID/conta da Microsoft, Facebook,
    Google, Yahoo!, Open ID.** Neste cenário, você controla o acesso ao
    seu serviço WCF (REST) usando a identidade da Internet, como o Live ID/
    conta da Microsoft.
-   **Aplicativo Web ASP.NET para Serviço REST com WCF usando Token SWT Compartilhado.** Nesse
    cenário, você distribuiu aplicativos com o aplicativo Web
    ASP.NET de front-end e serviço REST de downstream e deseja finalizar o fluxo
    do contexto do usuário por meio de níveis físicos.
-   **Autorização de Controle de Acesso Baseado em Função (RBAC) nos
    Aplicativos e Serviços de Reconhecimento de Declarações.** Neste cenário, você deseja implantar
    lógica de autorização em seu aplicativo com base em funções.
-   **Autorização Baseada em Declaração em Serviços e Aplicativos com Reconhecimento de Declaração
    .** Neste cenário, você deseja implantar
    lógica de autorização em seu aplicativo com base nas regras de autorização complexas.
-   **Identidade do serviço de armazenamento do Azure e cenários de acesso.**Nesse
    cenário, você precisa compartilhar com segurança o acesso aos
    blobs de armazenamento e contêineres do Azure.
-   **Cenários de acesso e identidade do Banco de Dados SQL do Azure**O 
    Banco de dados SQL só dá suporte a autenticação do SQL Server. A autenticação do Windows
    (segurança integrada) não tem suporte. Os usuários devem
    fornecer credenciais (logon e senha) toda vez que se conectam ao
    Banco de dados SQL.
-   **Identidade do Barramento de Serviço do Azure e Cenários de Acesso.**Nesse
    cenário, você precisa acessar com segurança as filas do barramento de serviço do Azure.
-   **Cenários de acesso e identidade de cache na memória. **Neste cenário
    você precisa acessar com segurança dados gerenciados pelo cache de memória.
-   **Cenários de acesso e identidade do Azure Marketplace.**Nesse
    cenário, você precisa acessar com segurança os conjuntos de dados do Azure Marketplace
    .

##Cenários de acesso e identidade do Azure

Esta seção descreve os cenários comuns de acesso e identidade para arquiteturas de aplicativos
diferentes. Use o Mapa de Cenários para uma orientação
rápida.

###Aplicativo Web Form do ASP.NET com Autenticação Federada

Neste cenário de arquitetura de aplicativo, seu aplicativo Web pode ser
implantado no Azure ou no local. O aplicativo requer que
os usuários sejam autenticados pelo Active Directory corporativo ou
provedores de identidade da Internet.

Para resolver esse cenário, use o Serviço de Controle de Acesso do Azure AD e o Windows
Identity Foundation.

![Azure Active Directory Access control][03]

Consulte os recursos a seguir para implementar este cenário:

-   [Como: criar meu primeiro aplicativo ASP.NET com reconhecimento de declarações usando o ACS](http://msdn.microsoft.com/pt-br/library/gg429779.aspx)
-   [Como: hospedar páginas de logon no seu aplicativo da web ASP.NET](http://msdn.microsoft.com/pt-br/library/gg185926.aspx)
-   [Como: Implantar a autorização de declarações em um aplicativo ASP.NET com reconhecimento de declarações usando WIF e ACS](http://msdn.microsoft.com/pt-br/library/gg185907.aspx)    
-   [Como: Implantar Controle de Acesso Baseado em Função em um Reconhecimento de Declarações
    Aplicativo ASP.NET usando o WIF e ACS](http://msdn.microsoft.com/pt-br/library/gg185914.aspx)
-   [Como: Configurar confiança entre o ACS e aplicativos da Web ASP.NET
    Usando certificados X.509](http://msdn.microsoft.com/pt-br/library/gg185947.aspx)
-   [Exemplo de código: Formulários ASP.NET simples](http://msdn.microsoft.com/pt-br/library/gg185938.aspx)

###Serviço do WCF (SOAP) com identidade do serviço

Neste cenário de arquitetura de aplicativo, seu serviço WCF (SOAP) pode ser
implantado no Azure ou no local. O serviço está sendo acessado
como um serviço downstream por um aplicativo Web ou até mesmo por outro serviço
web. Você precisa controlar o acesso a ele usando a identidade específica
do aplicativo. Pense em termos de tipo de conta de pool de aplicativos que você
usou no IIS: Embora a tecnologia seja diferente, as abordagens são
semelhantes no sentido de que o serviço é acessado usando uma conta do escopo do aplicativo
versus a conta de usuário final.

Use o recurso de Identidade do Serviço no Serviço de Controle de Acesso do Azure AD.
Isso é semelhante à conta do pool de aplicativos IIS que você estava usando ao
implantar seus aplicativos no Windows Server e IIS. Configurar o Azure
Controle de acesso do AD para emitir tokens SAML que serão manipulados pelo WIF no
serviço WCF (SOAP).

![WCF (SOAP) Service][04]


Consulte os recursos a seguir para implementar este cenário:

-   [Como: Adicionar identidades de serviço com um certificado X.509, senha
    ou chave simétrica](http://msdn.microsoft.com/pt-br/library/gg185924.aspx)
-   [Como: Autenticar com um certificado de cliente a um serviço WCF
    protegido pelo ACS](http://msdn.microsoft.com/pt-br/library/hh289316.aspx)
-   [Como: Autenticar com um nome de usuário e senha para um serviço WCF
    protegido pelo ACS](http://msdn.microsoft.com/pt-br/library/gg185954.aspx)
-   [Exemplo de código: Autenticação de certificado do WCF](http://msdn.microsoft.com/pt-br/library/gg185952.aspx)
-   [Exemplo de código: Autenticação de nome de usuário do WCF](http://msdn.microsoft.com/pt-br/library/gg185927.aspx)

###Serviço do WCF (SOAP) com Autenticação Federada, Identidades no Active Directory.

Neste cenário de arquitetura de aplicativo, seu serviço WCF (SOAP) pode ser
implantado no Azure ou no local. Você precisa controlar o acesso a isso
usando uma identidade que é gerenciada por um Active Directory (AD) do
Windows Server corporativo.

Usar o Serviço de Controle de Acesso do Azure AD configurado para federação com
Windows Server AD FS. Nesse caso, não é necessário configurar a identidade de
serviço com o Serviço de Controle de Acesso do Azure AD. O agente que precisa
acessar o serviço WCF (SOAP) fornece credenciais, como o AD FS e, na
autenticação bem-sucedida, o token é emitido pelo AD FS. O token é
enviado ao Serviço de Controle de Acesso do Azure AD e reemitido para
o agente. O agente usa o token para enviar solicitação para o serviço WCF (SOAP)
.

![WCF (SOAP) Service With AD][05]

Consulte os recursos a seguir para implementar este cenário:

-   [Como: Adicionar identidades de serviço com um certificado X.509, senha
    ou chave simétrica](http://msdn.microsoft.com/pt-br/library/gg185924.aspx)
-   [Como: configurar o AD FS 2.0 como Provedor de identidade](http://msdn.microsoft.com/pt-br/library/gg185961.aspx)
-   [Como: Usar o serviço de gerenciamento para configurar o AD FS 2.0 como um
    provedor de identidade empresarial](http://msdn.microsoft.com/pt-br/library/gg185905.aspx)
-   [Exemplo de código: Autenticação WCF federada com o AD FS 2.0
](http://msdn.microsoft.com/pt-br/library/hh127796.aspx)

###Serviço do WCF (REST) com Identidades do Serviço

Nesse cenário, seu serviço WCF (REST) pode ser implantado no 
Azure ou no local. O serviço é acessado como um serviço downstream por
um aplicativo Web ou por outro serviço Web. Você precisa controlar o acesso
a ele usando uma identidade específica do aplicativo. Pense nisso em termos do
tipo de conta de pool de aplicativo que você usou no IIS. Embora a tecnologia
seja diferente, as abordagens são semelhantes no sentido de que o serviço é acessado
usando uma conta de escopo de aplicativo versus a conta de usuário final.

Use o recurso de Identidade do Serviço no Serviço de Controle de Acesso do Azure AD.
Configuração do Serviço de Controle de Acesso do Azure AD para emitir tokens
Simple Web Token (SWT). Para lidar com o token SWT no lado do serviço REST, você pode
implantar um manipulador de token personalizado e conectá-lo no pipeline do WIF
, ou você pode analisá-lo "manualmente" sem usar a infraestrutura do WIF
.

Considere o diagrama a seguir (o WIF é opcional):

![REST Service][06]

Consulte os recursos a seguir para implementar este cenário:

-   [Como: Configurar a confiança entre o ACS e o serviço WCF usando chaves
    simétricas](http://msdn.microsoft.com/pt-br/library/gg185958.aspx)
-   [Como: Autenticar para um Serviço REST WCF implantado no Azure
    usando o ACS](http://msdn.microsoft.com/pt-br/library/hh289317.aspx)
-   [Exemplo de código: Serviço Web do ASP.NET](http://msdn.microsoft.com/pt-br/library/gg983271.aspx)
-   [Exemplo de código: Aplicativo do Windows Phone 7](http://msdn.microsoft.com/pt-br/library/gg983271.aspx)
-   [WCF REST com Token SWT emitido pelo Serviço de Controle de Acesso do Azure
    (ACS)](http://code.msdn.microsoft.com/REST-WCF-With-SWT-Token-123d93c0)

###Serviço do WCF (REST) com Live ID/Conta da Microsoft, Facebook, Google, Yahoo!, Open ID

Nesse cenário, seu serviço WCF (REST) pode ser implantado no 
Azure ou no local. Você precisa controlar o acesso a ele usando uma
identidade pública da Internet, como o Live ID/conta da Microsoft ou Facebook.

Uso do Serviço de Controle de Acesso do Azure AD para emitir tokens SWT. Para lidar com o
token SWT no lado do serviço REST, você pode implantar um manipulador de token personalizado
e conectá-lo a um pipeline WIF ou analisá-lo "manualmente"
sem usar a infraestrutura WIF.

É importante observar que, para implantar este cenário, o
aplicativo precisa usar o controle de navegador da Web para coletar as credenciais
do usuário final. Isso o torna inadequado para cenários nos quais o serviço REST
é acessado de um aplicativo web ASP.NET. Ele só é adequado para
cenários nos quais o serviço REST está sendo acessado pelo aplicativo cliente
do usuário, como um aplicativo do Windows Phone 7 ou um cliente de área de trabalho avançada
. O motivo principal para exibir o controle do navegador da Web é que as
identidades de Internet não oferecem suporte nativo a cenários de perfil ativo (cenário
de serviços web). As identidades de Internet oferecem, principalmente, suporte a cenários de perfil passivo
(aplicativos web) que dependem de redirecionamentos do navegador: É assim que o controle do
navegador web é útil.

Considere o diagrama a seguir (o WIF é opcional, por isso não foi exibido):

![WIF is optional][07]

Consulte os recursos a seguir para implementar este cenário:

-   [Como: autenticar para um Serviço REST WCF implantado no Azure usando o ACS](http://msdn.microsoft.com/pt-br/library/hh289317.aspx)
-   [Como: configurar o Google como um provedor de identidade](http://msdn.microsoft.com/pt-br/library/gg185976.aspx)
-   [Como: configurar o Facebook como um provedor de identidade](http://msdn.microsoft.com/pt-br/library/gg185919.aspx)
-   [Como: configurar o Yahoo! como um provedor de identidade](http://msdn.microsoft.com/pt-br/library/gg185977.aspx)
-  [Exemplo de código: Aplicativo do Windows Phone 7](http://msdn.microsoft.com/pt-br/library/gg983271.aspx)
-   [WCF REST com Token SWT emitido pelo Serviço de Controle de Acesso do Azure
    (ACS)](http://code.msdn.microsoft.com/REST-WCF-With-SWT-Token-123d93c0)


###Aplicativo Web ASP.NET para Serviço REST com WCF usando Token SWT Compartilhado

Nesse cenário, você tem um aplicativo distribuído com um aplicativo web
do ASP.NET de front-end e um serviço REST de downstream e você deseja manter
o contexto do usuário final em camadas físicas. Isso, às vezes, é necessário
ao implantar lógica de autorização ou registro em log com base na identidade do usuário final
no serviço REST de downstream.

Configuração do Serviço de Controle de Acesso do Azure AD para emitir token SWT. O token SWT
é emitido ao aplicativo web ASP.NET de front-end e compartilhado com
o serviço REST de downstream. Nesse caso, há apenas uma parte de confiança
configurada no Serviço de Controle de Acesso do Azure AD. No entanto, há
várias advertências:

-   Como o WIF não fornece um manipulador de token SWT pronto para uso, você
    precisa implantar um manipulador de token personalizado que será usado com o aplicativo Web
    ASP.NET Você deve confiar no processamento mais pesado que o WIF faz para
    suporte ao protocolo Web Services Federation que depende de redirecionamentos do navegador
    versus implementá-lo você mesmo.
-   Ao implementar um manipulador de token SWT personalizado, verifique se o
    token de inicialização está sendo abordado para certificar-se de que ele é mantido.
    Caso contrário, não será possível compartilhá-lo e enviá-lo para o serviço
    REST de downstream.
-   Você não precisa usar o WIF em um serviço REST. Em vez disso, você pode analisar
    o token "manualmente", pois não há necessidade de tratar redirecionamentos
    neste caso.

![ASP.NET Web Application][08]

Consulte os recursos a seguir para implementar este cenário:

-   [Como: configurar o Google como um provedor de identidade](http://msdn.microsoft.com/pt-br/library/gg185976.aspx)
-   [Como: configurar o Facebook como um provedor de identidade](http://msdn.microsoft.com/pt-br/library/gg185919.aspx)
-   [Como: configurar o Yahoo! como um provedor de identidade](http://msdn.microsoft.com/pt-br/library/gg185977.aspx)
-   [Aplicativo Web ASP.NET para Delegação de Serviço WCF REST usando SWT Compartilhado
    Token](http://code.msdn.microsoft.com/ASPNET-Web-App-To-REST-WCF-b2b95f82)

###Controle de acesso baseado em função (RBAC) em serviços e aplicativos com reconhecimento de declaração

Nesse cenário, você precisa implantar a autorização em seu
aplicativo web ou serviço com base em funções de usuário: usuário funções exigidas obtém
acesso, e os que não tem funções exigidas, não obtêm. Em outras palavras,
seu aplicativo precisa responder à pergunta simples: o usuário está na função
X?

Há várias maneiras de se resolver essa situação. Você pode usar o Controle de Acesso do
Azure AD, Gerenciador de autenticação de declarações WIF,
mapeamento de samlSecurityTokenRequirement ou o Gerenciador de funções do cliente.

O WIF é usado em todos os casos. O WIF dá suporte ao método IPrincipal.IsInRole("MyRole")
. Na maioria dos casos, a chave é certificar-se de que essa declaração de tipo de função
com URI de
http://schemas.microsoft.com/ws/2008/06/identity/claims/role no
token para que o WIF possa verificar com êxito a associação de função quando o
método IsInRole for chamado.

**Access Control do AD do Azure**. Nessa implementação do
Serviço de Controle de Acesso do Azure AD, o mecanismo de regra de transformação de declarações é usado. Ao usar
as regras do mecanismo de regras de transformação de declarações, é possível transformar qualquer
declaração de entrada em uma declaração de tipo de função, de modo que quando o token atinge um
aplicativo ou serviço WIF, ele pode analisar essa declaração de função para certificar-se de que a
chamada de método IsInRole foi bem-sucedida.

![][09]

**WIF ClaimsAuthenticationManager**. Nessa implementação, use
ClaimsAuthenticationManager como ponto de extensibilidade do WIF. Usando essa
abordagem, é possível transformar quaisquer declarações arbitrárias de entrada em um tipo de declaração de função
no aplicativo. A complexidade da transformação é apenas
limitado pelo código que você escreve.

![][10]

**mapeamento de samlSecurityTokenRequriement** Nessa implementação, use
a configuração samlSecurityTokenRequirement no web.config para informar ao WIF
quais tipos de declaração se comportam como se fossem tipos de declaração de função. Por exemplo, 
se o token executa uma declaração de tipo de grupo, você pode mapeá-lo para o tipo de declaração de função
. Você pode obter apenas os mapeamentos simples usando essa abordagem.

![][11]

**RoleManager personalizado.** Nesta implementação, você implementa o RoleManger personalizado
. O WIF é usado para inspecionar as declarações de entrada ao implementar
métodos de interface RoleManager personalizados como GetAllRoles().

![][12]

Consulte os recursos a seguir para implementar este cenário:

-   [Como: Implantar Controle de Acesso Baseado em Função em um Reconhecimento de Declarações
    Aplicativo ASP.NET usando o WIF e ACS](http://msdn.microsoft.com/pt-br/library/gg185914.aspx)
-   [Como: implementar a lógica de transformação do token usando regras](http://msdn.microsoft.com/pt-br/library/gg185955.aspx)
-   [Autorização com RoleManager para aplicativos Web ASP.NET com reconhecimento de declarações (WIF)
    .](http://blogs.msdn.com/b/alikl/archive/2010/11/18/authorization-with-rolemanager-for-claims-aware-wif-asp-net-web-applications.aspx)
-   Exemplo de código: Usando declarações em IsInRole no SDK [Windows Identity Foundation
    .](http://www.microsoft.com/downloads/details.aspx?FamilyID=c148b2df-c7af-46bb-9162-2c9422208504)

###Autorização Baseada em Declaração em Serviços e Aplicativos com Reconhecimento de Declaração

Nesse cenário, você precisa implementar a lógica de autorização complexa em
seu aplicativo Web ou serviço e o método IsInRole () não é
satisfatório para suas necessidades de autorização. Se sua abordagem de autorização
se baseia nas funções, considere a implementação de controle de acesso baseado em função
descrito na seção anterior.

Use ClaimsAuthorizationManager como o ponto de extensibilidade WIF.
O ClaimsAuthorizationManager permite chamadas de verificação de acesso externo para que
seu código do aplicativo seja mais limpo e mais passível de manutenção quando
verificações de acesso forem implementadas no código do aplicativo.

![][13]

Consulte os recursos a seguir para implementar este cenário:

-   [Como: implementar a lógica de transformação do token usando regras](http://msdn.microsoft.com/pt-br/library/gg185955.aspx)
-   [Como: Implementar autorização de declarações em um ASP.NET com reconhecimento de declarações
    Aplicativo usando o WIF e ACS](http://msdn.microsoft.com/pt-br/library/gg185907.aspx)
-   Exemplo de código: Autorização baseada em declaração no SDK do [Windows Identity
    Foundation](http://www.microsoft.com/downloads/details.aspx?FamilyID=c148b2df-c7af-46bb-9162-2c9422208504)


##Identidade do serviço de armazenamento do Azure e cenários de acesso

Nesse cenário, você precisa compartilhar com segurança o acesso aos
blobs de armazenamento e contêineres do Azure.

Uso de Assinaturas de Acesso Compartilhado. Para acessar sua conta de serviço de armazenamento
no aplicativo, use o hash compartilhado disponível por meio do
portal do Azure ao configurar e gerenciar as contas do serviço de armazenamento
. Quando desejar conceder a alguém mais acesso aos blobs e
contêineres na conta de serviço de armazenamento, use URLs de assinaturas de acesso compartilhado
.

Preste atenção especial no gerenciamento seguro de informações para evitar sua
exposição. Além disso, preste especial atenção ao tempo de vida das assinaturas de acesso compartilhado
.

![][14]

Consulte os recursos a seguir para resolver este cenário

-   [Gerenciando acesso a Blobs e Contêineres](http://msdn.microsoft.com/pt-br/library/ee393343.aspx)
-   [Recurso de Novo Armazenamento: Assinaturas de Acesso Compartilhado](http://blog.smarx.com/posts/new-storage-feature-signed-access-signatures)
-   [Assinaturas de Acesso Compartilhado são fáceis hoje em dia (a página pode estar em inglês)](http://blog.smarx.com/posts/shared-access-signatures-are-easy-these-days)


##Cenários de acesso e identidade do Banco de Dados SQL do Azure

O Banco de dados SQL somente dá suporte para a autenticação do SQL Server. A autenticação do Windows
(segurança integrada) não tem suporte. Os usuários devem
fornecer credenciais (logon e senha) toda vez que se conectam ao
Banco de dados SQL. Preste atenção especial ao gerenciar seu nome de usuário e
senha para evitar a divulgação de informações.

![][15]

Consulte os recursos a seguir para resolver este cenário:

-   [Diretrizes e limitações de segurança (Banco de dados SQL do Azure)](http://msdn.microsoft.com/pt-br/library/windowsazure/ff394108.aspx#authentication)
-   [Como: conectar-se ao Banco de Dados SQL do Azure usando o sqlcmd](http://msdn.microsoft.com/pt-br/library/windowsazure/ee336280.aspx)
-   [Como: conectar-se ao Banco de Dados SQL do Azure usando o ADO.NET](http://msdn.microsoft.com/pt-br/library/windowsazure/ee336243.aspx)
-   [Como: conectar-se ao Banco de Dados SQL do Azure por meio do ASP.NET](http://msdn.microsoft.com/pt-br/library/windowsazure/ee621781.aspx)
-   [Como: conectar-se ao Banco de dados SQL do Azure através do WCF Data Services](http://msdn.microsoft.com/pt-br/library/windowsazure/ee621789.aspx)
-  [Como: conectar-se ao Banco de Dados SQL do Azure usando o PHP](http://msdn.microsoft.com/pt-br/library/windowsazure/ff394110.aspx)
-   [Como: conectar-se ao Banco de Dados SQL do Azure usando o JDBC](http://msdn.microsoft.com/pt-br/library/windowsazure/gg715284.aspx)
-   [Como: conectar-se ao Banco de dados SQL do Azure usando o ADO.NET Entity Framework](http://msdn.microsoft.com/pt-br/library/windowsazure/ff951633.aspx)

##Identidade do Serviço de Barramento do Azure e cenários de acesso

O barramento de serviço e o Serviço de Controle de Acesso do Azure AD têm um relacionamento especial
no sentido de que cada namespace do Barramento de Serviço é associado a um
namespace de Serviço de Controle de Acesso correspondente com o mesmo nome, com o
sufixo "-sb". A razão para essa relação especial está na forma em que
o controle de acesso e o barramento de serviço gerenciam seu relacionamento de confiança mútua
e os segredos de criptografia associados. Consulte os
recursos listados abaixo para obter mais detalhes.

![][16]

Consulte os recursos a seguir para resolver este cenário:

-   [Proteção do Barramento de Serviço com o ACS](http://channel9.msdn.com/posts/Securing-Service-Bus-with-ACS) (vídeo)
-   [Proteção do Barramento de Serviço com ACS](https://skydrive.live.com/view.aspx?cid=123CCD2A7AB10107&resid=123CCD2A7AB10107%211849) (Slides)
-   [Autenticação e autorização do Barramento de Serviço com o Serviço de Controle de
    Acesso](http://msdn.microsoft.com/pt-br/library/hh403962.aspx)

##Cenários de acesso e identidade do Cache em memória

O cache na memória (anteriormente conhecido como Cache do Azure) depende do
Serviço de Controle de Acesso do Azure AD para autenticação. Ele usa chaves compartilhadas
disponíveis por meio do portal de gerenciamento. Use as teclas em seu código ou
arquivos de configuração ao acessar o cache. Certifique-se de armazenar as chaves
com segurança para evitar a divulgação de informações.

![][17]


Consulte os recursos a seguir para resolver este cenário:

-   [Como: Configurar um cliente de cache de forma programática para o caching do Azure
    Caching](http://msdn.microsoft.com/pt-br/library/windowsazure/gg618003.aspx)
-   [Como: Configurar um cliente de cache usando o arquivo de configuração do aplicativo
    para Cache do Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/gg278346.aspx)
-   [Exemplos de Cache e Barramento de Serviço do Azure](http://msdn.microsoft.com/pt-br/library/ee706741.aspx) (Seção de exemplos de
    caching)

##Cenários de Acesso e identidade de Marketplace do Azure.

Cada acesso a um conjunto de dados do Azure Marketplace, seja gratuito ou
pago, deve autenticar o usuário para obtenção de acesso. Quando você
cria um aplicativo, o processo de autenticação deve ser incluído no
seu código. Considere os seguintes cenários comuns:

###Eu acesso o meu conjunto de dados

Nesse cenário, você está criando um aplicativo que consome os conjuntos de dados
em sua assinatura do Marketplace. Você é o usuário do aplicativo.
O aplicativo pode ser implantado no Azure, local ou no Marketplace.

Use a chave compartilhada que está disponível por meio de sua assinatura do Marketplace
. Obtenha a chave compartilhada usando o portal do Marketplace.

![][18]

Consulte os recursos a seguir para resolver este cenário:

-   [Uso de HTTP Basic Auth no seu aplicativo do Marketplace (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/gg193417.aspx)

###Os usuários acessam os meus conjuntos de dados

Nesse cenário, você está criando um aplicativo que permite aos usuários
acesso ao conjunto de dados. O aplicativo pode ser implantado no Azure,
no local ou no Marketplace.

Para resolver esse cenário, use a delegação OAuth. Os usuários serão solicitados a
fornecer seu Live ID/credenciais de conta da Microsoft e, em seguida, eles
serão colocados no processo de consentimento.

![][19]

Consulte os recursos a seguir para resolver este cenário:

-   [Exemplo de cliente Web OAuth (a página pode estar em inglês)](http://go.microsoft.com/fwlink/?LinkId=219162)
-   [Exemplo de cliente OAuth avançado (a página pode estar em inglês)](http://go.microsoft.com/fwlink/?LinkId=219163)

###API do Marketplace de acesso do aplicativo

Nesse cenário, você está criando um aplicativo que consome a
API do Marketplace. A API do Marketplace exige autenticação para
executar com êxito as chamadas a ele. O aplicativo é implantado no
Azure Marketplace.

![][20]

Consulte o kit de publicação do Marketplace para obter detalhes sobre a
implementação de autenticação.

Consulte os recursos a seguir para resolver este cenário:

-   [Baixar o Kit de publicação de aplicativo](http://go.microsoft.com/fwlink/?LinkId=221323)
-   [Introdução ao Azure Marketplace para aplicativos](https://datamarket.azure.com/)

##Botões de segurança

Esta seção descreve os botões de segurança para o Windows Identity Foundation e
Serviço de Controle de Acesso do Azure AD. Use-a como uma lista de verificação de segurança básica
para essas tecnologias ao projetar e implantar seu aplicativo.

###Windows Identity Foundation

A seguir estão os principais botões de segurança de WIF. As informações a seguir são um
resumo de [considerações de design do WIF](http://msdn.microsoft.com/pt-br/library/ee517298.aspx) e do [Windows Identity Foundation
(WIF) Security para aplicações web do ASP.NET - Ameaças e medidas](http://blogs.msdn.com/b/alikl/archive/2010/12/02/windows-identity-foundation-wif-security-for-asp-net-web-applications-threats-amp-countermeasures.aspx)
.

-   **IssuerNameRegistry**. Especifica os serviços de token de segurança confiáveis
    (STSs). Certifique-se de que apenas STS confiáveis estão listados.
-   **cookieHandler requireSsl="true"**. Especifica se os cookies da sessão
    são transferidos através do protocolo SSL.
-   **wsFederation's requireHttps="true"**. Especifica se a
    comunicação de protocolo de federação com o provedor de identidade é executada
    através do protocolo SSL.
-   **tokenReplayDetection enabled="true"**. Especifica se o
    recurso de detecção de reprodução de token está habilitado. Lembre-se que esse recurso
    cria a afinidade do servidor que gerencia cópias locais dos tokens usados.
-   **audienceUris**. Especifica o público-alvo do token. Se seu
    aplicativo recebe um token que não foi destinado ao seu aplicativo, ele
    será rejeitado pelo WIF.
-   **requestValidation** e **httpRuntime requestValidationType**.
    Ativa/desativa o recurso de validação do ASP.NET. Consulte orientações conforme
    descritas em [Windows Identity Foundation (WIF): Uma solicitação potencialmente
    perigosa. O valor do formulário foi detectado do cliente](http://social.technet.microsoft.com/wiki/contents/articles/1725.windows-identity-foundation-wif-a-potentially-dangerous-request-form-value-was-detected-from-the-client-wresult-t-requestsecurityto.aspx)

###Access Control do AD do Azure.

Considere os seguintes botões de segurança na implantação do Serviço de Controle de Acesso do Azure AD
. As informações a seguir são um resumo das [Diretrizes de segurança do ACS
ACS](http://msdn.microsoft.com/pt-br/library/gg185962.aspx) e [certificados e diretrizes de gerenciamento de chaves](http://msdn.microsoft.com/pt-br/library/hh204521.aspx).

-   **Expiração de tokens do STS**. Use o portal de gerenciamento do Serviço de Controle de Acesso do Azure AD
    para definir a expiração agressiva do token.
-   **Validação de dados ao usar o recurso URL de Erro**. Azure
    O recurso de URL de erro de controle de acesso do AD requer acesso anônimo à
    página do aplicativo na qual ele envia mensagens de erro. Suponha que todos os dados que chegam a
    esta página são perigosos e são de fonte não confiável.
-   **Criptografia de tokens para cenários altamente confidenciais**. Para reduzir
    ameaças de divulgação de informações que estão disponível no token
    considere criptografar os tokens.
-   **Criptografia de cookies usando RSA ao implantar o Azure**.
    O WIF criptografa cookies usando DPAPI por padrão. Ele cria a afinidade do servidor
    e pode resultar em exceções quando implantado em um farm da web e
    ambientes do Azure. Use RSA em vez de farm web e
    cenários do Azure.
-   **Certificado de assinatura de tokens**. Renove os certificados de autenticação de tokens
    periodicamente para evitar a negação de serviço. O Controle de Acesso do Azure AD
    assina todos os tokens de segurança que emite. Os certificados X.509 são
    usados para autenticação quando você cria um aplicativo que consome tokens SAML
    emitidos pelo ACS. Quando os certificados de assinatura expirarem, você
    receberá erros ao tentar solicitar um token.
-   **Chaves de assinatura de tokens**. Renove periodicamente as chaves de assinatura de token para
    evitar a negação de serviço. O Serviço de Controle de Acesso do Azure AD assina todos
    os tokens de segurança que ele emite. As chaves de autenticação simétricas de 256 bits são usadas
    quando você cria um aplicativo que consome tokens SWT emitidos pelo
    ACS. Quando as chaves de assinatura expirarem, você receberá erros ao tentar
    solicitar um token.
-   **Certificado de criptografia de tokens**. Renove os certificados de criptografia do token
    periodicamente para evitar a negação de serviço. A criptografia do token
    é necessária se um aplicativo de terceira parte confiável for um serviço web
    usando tokens de prova de posse através do protocolo WS-Trust.
    Em outros casos, a criptografia do token é opcional. Quando os certificados de criptografia
    expirarem, você receberá erros ao tentar solicitar um
    token.
-   **Certificados de descriptografia de tokens**. Renove os certificados de descriptografia do token
    periodicamente para evitar a negação de serviço. Azure
    O Controle de Acesso do AD pode aceitar tokens criptografados de provedores de identidade Web Services Federation
    (por exemplo, AD FS 2.0). Um certificado X.509
    hospedado no Serviço de Controle de Acesso do Azure AD é usado para descriptografia.
    Quando os certificados de descriptografia expirarem, você receberá erros quando
    tentar solicitar um token.
-   **Credenciais de identidade do serviço**. Renove as credenciais de identidade do serviço
    periodicamente para evitar a negação de serviço. Identidades de serviço usam
    credenciais configuradas globalmente para o namespace do Controle de Acesso do Azure AD
    que permite que aplicativos ou clientes sejam
    autenticados diretamente com o Serviço de Controle de Acesso do Azure AD e
    recebam um token. Há três tipos de credenciais aos quais a identidade do serviço de Controle de Acesso do Azure AD
    podem ser associada: Chave simétrica
     , senha e certificado X.509. Você começará a receber
    uma exceção quando as credenciais estiverem expiradas.
-   **Credenciais da conta do Serviço de Gerenciamento do Serviço de Controle de Acesso do Azure AD
**. Renove as credenciais de serviço de gerenciamento periodicamente para
    evitar a negação de serviço. O Serviço de Controle de Acesso do Azure AD
    O serviço de gerenciamento é um componente-chave que permite que você
    gerencie programaticamente e defina as configurações para seu 
    namespace do Serviço de Controle de Acesso do Azure AD. Há três tipos de credenciais
    aos quais a conta de serviço de gerenciamento pode ser associada. Os tipos
    são: chave simétrica, senha e certificado X.509. Você
    começará a receber uma exceção quando as credenciais estiverem expiradas.
-   **Assinatura de provedor de identidade do Web Services Federation e certificados de criptografia
**. Consulte a validade do certificado do provedor de identidade do Web Services Federation
....para evitar a negação de serviço. O certificado do provedor de identidade do Web Services Federation
    está disponível por meio de seus metadados.
    Ao configurar o provedor de identidade do Web Services Federation, como o AD FS, o
    certificado de autenticação do Web Services Federation é configurado por meio
    dos metadados do Web Services Federation disponíveis via URL ou como um arquivo. Depois disso,
    o provedor de identidade do Web Services Federation configurado usa o serviço de gerenciamento do Controle de Acesso do Azure AD
    para consultá-lo quanto a validade de seus certificados
. Quando os certificados expirarem, você começará a receber
    exceções.

##Hospedagem compartilhada usando Sites do Azure

Todos os cenários e soluções descritos neste tópico são válidos quando o
aplicativo é hospedado em sites do Azure.

##Máquinas Virtuais do Azure

Todos os cenários e soluções descritos neste tópico são válidos quando o
aplicativo é hospedado em máquinas virtuais do Azure.

##Recursos

-   [Kit de treinamento de identidade para desenvolvedores (a página pode estar em inglês)](http://go.microsoft.com/fwlink/?LinkId=214555)
-   [Curso de treinamento de identidade para desenvolvedores hospedado pelo MSDN (a página pode estar em inglês)](http://go.microsoft.com/fwlink/?LinkId=214561)
-   [Um guia para Identidade baseada em declarações e Access Control (a página pode estar em inglês)](http://go.microsoft.com/fwlink/?LinkId=214562)
-   [Access Control Service](http://msdn.microsoft.com/pt-br/library/windowsazure/gg429786.aspx)
-   [Instruções do ACS](http://msdn.microsoft.com/pt-br/library/windowsazure/gg185939.aspx)
-   [Proteção de aplicativo web ASP.NET com função web do Azure usando o Access Control Service v2.0 (a página pode estar em inglês)](http://social.technet.microsoft.com/wiki/contents/articles/2590.aspx)
-   [Academy Videos do Access Control Service (ACS) do AD do Azure (a página pode estar em inglês)](http://social.technet.microsoft.com/wiki/contents/articles/2777.aspx)
-   [Ciclo de vida de desenvolvimento de segurança da Microsoft](http://www.microsoft.com/security/sdl/default.aspx)
-   [Ferramenta de modelagem de ameaças do SDL 3.1.8 (a página pode estar em inglês)](http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=2955)
-   [Blogs sobre segurança e privacidade](http://www.microsoft.com/about/twc/en/us/blogs.aspx)
-   [Security Response Center](http://www.microsoft.com/security/msrc/default.aspx)
-   [Relatório de inteligência de segurança](http://www.microsoft.com/security/sir/)
-   [Ciclo de vida de desenvolvimento de segurança](http://www.microsoft.com/security/sdl/default.aspx)
-   [Security Developer Center (MSDN)](http://msdn.microsoft.com/security/)


[01]:./media/SecurityRX/01_SecuringTheApplication.gif
[02]:./media/SecurityRX/02_ThreatsVulnerabilitiesandAttacks.gif
[03]:./media/SecurityRX/03_WindowsAzureADAccesscontrol.gif
[04]:./media/SecurityRX/04_WCF(SOAP)Service.gif
[05]:./media/SecurityRX/05_AzureADAccessControl.gif
[06]:./media/SecurityRX/06_RESTService.gif
[07]:./media/SecurityRX/07_WIFisOptional.gif
[08]:./media/SecurityRX/08_ASPNETWebApptoREST.gif
[09]:./media/SecurityRX/09_RBAC.gif
[10]:./media/SecurityRX/10_WIFClaimsAuthenticationManager.gif
[11]:./media/SecurityRX/11_SecurityTokenRequriementmapping.gif
[12]:./media/SecurityRX/12_CustomRoleManager.gif
[13]:./media/SecurityRX/13_ClaimsAuthorizationManager.gif
[14]:./media/SecurityRX/14_WindowsAzurestorage.gif
[15]:./media/SecurityRX/15_SQLAzureIdentityandAccessScenarios.gif
[16]:./media/SecurityRX/16_WindowsAzureServiceBusIdentity.gif
[17]:./media/SecurityRX/17_WindowsAzureCacheIdentity.gif
[18]:./media/SecurityRX/18_IAccessMyDataset.gif
[19]:./media/SecurityRX/19_UsersAccessMyDatasets.gif
[20]:./media/SecurityRX/20_ApplicationAccessMarketplaceAPI.gif

[Design de SSO Web]: http://technet.microsoft.com/pt-br/library/dd807033(WS.10).aspx
[Design de SSO Web federado]: http://technet.microsoft.com/pt-br/library/dd807050(WS.10).aspx
