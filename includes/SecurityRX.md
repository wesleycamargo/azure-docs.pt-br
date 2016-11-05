# Guia de segurança do Azure
## Resumo
Ao desenvolver aplicativos para o Azure, identidade e acesso são as principais preocupações sobre segurança que você precisa ter em mente. Este tópico explica as questões de segurança relacionadas a identidades e acesso na nuvem e como você pode proteger melhor seus aplicativos em nuvem.

## Visão geral
A segurança do aplicativo é uma função de sua superfície. Quanto mais superfície o aplicativo expõe, maiores as preocupações com a segurança. Por exemplo, um aplicativo executado como um processo em lote autônomo fica menos exposto, de uma perspectiva de segurança, do que um site disponível publicamente.

Quando você vai para a nuvem, você ganha uma determinada tranquilidade sobre a infraestrutura e a rede, já que esses itens são gerenciados em datacenters com práticas de segurança de classe mundial, ferramentas e tecnologia. Por outro lado, a nuvem intrinsecamente expõe mais área de superfície para o seu aplicativo, que pode ser potencialmente explorado por usuários mal-intencionados. Isso ocorre porque muitas tecnologias em nuvem e os serviços são expostos como pontos de extremidade versus componentes de memória. O armazenamento do Azure, o Barramento de Serviço, o Banco de Dados SQL (antigo SQL Azure) e muitos outros serviços podem ser acessados por meio de seus pontos de extremidade durante a transmissão.

Em aplicativos de nuvem existe mais responsabilidade sobre os ombros dos desenvolvedores de aplicativos para criar, desenvolver e manter seus aplicativos em nuvem de acordo com padrões de alta segurança para manter os atacantes afastados. Considere o diagrama a seguir (de J.D. Meier no [PDF de observações de segurança do Azure](http://blogs.msdn.com/b/jmeier/archive/2010/08/03/now-available-azure-security-notes-pdf.aspx)): observe como a parte de infraestrutura está sendo abordada pelo provedor de nuvem - em nosso caso, o Azure, deixando mais trabalho de segurança para desenvolvedores de aplicativos:

![Protegendo o aplicativo][01]

A boa notícia é que todas as práticas de desenvolvimento de segurança, princípios e técnicas que você já sabe ainda se aplicam ao desenvolver aplicativos em nuvem. Considere as seguintes áreas principais que devem ser atendidas:

* Toda entrada é validada para tipo, comprimento, intervalo e padrões de cadeia de caracteres para evitar ataques de injeção e todos os dados que seu aplicativo ecoa são limpos adequadamente.
* Dados confidenciais devem ser protegidos em repouso e durante a transmissão para atenuar as ameaças de violação de dados e divulgação de informações.
* A auditoria e o log devem ser corretamente implementados para atenuar as ameaças de não repúdio.
* Autenticação e autorização devem ser implementadas usando mecanismos comprovados oferecidos pela plataforma para evitar falsificação de identidade e a elevação de ameaças de privilégios.

Para obter uma lista completa de ameaças, ataques, vulnerabilidades e contramedidas, consulte a [Folha de consulta: Quadro de segurança de aplicativo da Web](http://msdn.microsoft.com/library/ff649461.aspx) e [Guia de Segurança para Índice de Aplicativos](http://msdn.microsoft.com/library/ff650760.aspx) dos padrões e práticas.

Na nuvem, autenticação e mecanismos de controle de acesso são muito diferentes daqueles disponíveis para aplicativos locais. Há muito mais opções de autenticação e acesso oferecidas para aplicativos em nuvem que podem levar a uma confusão e, consequentemente, a implementações defeituosas. Acontece mais confusão quando se define o que é um aplicativo em nuvem. Por exemplo, o aplicativo pode ser implantado na nuvem, mas seu mecanismo de autenticação pode ser fornecido pelo Active Directory. Por outro lado, o aplicativo pode ser implantado no local, mas com mecanismos de autenticação na nuvem (por exemplo, pelo Controle de Acesso do Active Directory do Azure (anteriormente conhecido como Serviço do Controle de Acesso ou ACS)).

## Ameaças, vulnerabilidades e ataques
Uma ameaça é um resultado potencial incorreto que você deseja evitar, como a divulgação de informações confidenciais ou a indisponibilidade de um serviço. É uma prática comum classificar as ameaças usando o acrônimo "STRIDE":

* **S**poofing (falsificação) ou roubo de identidade
* **T**ampering (manipulação) de dados
* **R**epudiation (repúdio) de ações
* **I**nformation disclosure (Revelação de informações)
* **D**enial (negação) de serviço
* **E**levation (elevação) de privilégios

Vulnerabilidades são bugs que nós, como desenvolvedores, inserimos no código, fazendo com que um aplicativo possa ser explorado por invasores. Por exemplo, enviar dados confidenciais em formato de texto não criptografado possibilita uma ameaça de divulgação de informações por um ataque de detecção de tráfego.

Os ataques são a exploração dessas vulnerabilidades de causar danos a um aplicativo. Por exemplo, um script entre sites, ou XSS, é um ataque que explora saída não corrigida. Outro exemplo é espionagem de rede para capturar as credenciais enviadas em formato de texto não criptografado. Esses ataques podem levar à realização de uma ameaça de falsificação de identidade. Para simplificar, considere ameaças, vulnerabilidades e ataques como coisas ruins. Considere os diagramas a seguir como um modo de exibição geral das coisas ruins relacionadas a uma aplicação Web implantada em Microsoft Azure (do [PDF de observações de segurança do Azure](http://blogs.msdn.com/b/jmeier/archive/2010/08/03/now-available-azure-security-notes-pdf.aspx) de J.D. Meier):

![Ameaças, vulnerabilidades e ataques][02]

Você, como desenvolvedor, tem controle sobre as vulnerabilidades. Quanto menos vulnerabilidades você inserir, menos opções os invasores têm para explorar.

Vulnerabilidades relacionadas a acesso e identidade o tornam suscetível a todas as ameaças do modelo STRIDE. Por exemplo, um mecanismo de autenticação implementado de forma imprópria pode levar a uma falsificação da identidade e, como consequência, divulgação de informações, violação de dados, operações de privilégios elevados ou até mesmo encerrar completamente o serviço. Considere as seguintes questões que podem apontar para possíveis vulnerabilidades na identidade do seu aplicativo em nuvem e na implementação do acesso:

* Você está enviando credenciais em formato de texto não criptografado durante a transmissão para os serviços do Azure?
* Você armazena credenciais de serviços do Azure em formato de texto não criptografado?
* Suas credenciais de serviços do Azure seguem as diretivas de senha forte?
* Você utiliza o Azure para verificar credenciais versus o uso de mecanismos de verificação personalizada?
* Você limita a vida útil dos tokens ou as sessões autenticação de serviços do Azure em um período de tempo razoável?
* Você verifica as permissões de cada ponto de entrada do Azure do seu aplicativo em nuvem distribuído?
* Seus mecanismos de autorização falham com segurança sem expor informações confidenciais ou sem permitir acesso ilimitado?

## Contramedidas
A melhor contramedida contra um ataque é usar os mecanismos de acesso e identidades oferecidos pela plataforma em vez de implementar o seu próprio. Considere as seguintes tecnologias de acesso e identidades de destaque:

**Windows Identity Foundation (WIF).** O WIF é uma biblioteca de tempo de execução .NET incluída com o .NET Framework 4.5 (também disponível como download separado para .NET 3.5/4.0). O WIF faz o trabalho pesado de lidar com protocolos como WS-Federation e WS-Trust e tokens de manipulação como o SAML (Security Assertion Markup Language) para que você não precise escrever códigos muito complexos relacionados à segurança em seu aplicativo. Os recursos a seguir fornecem informações detalhadas sobre o WIF:

* [Exemplos do Windows Identity Foundation 4.5](http://code.msdn.microsoft.com/site/search?f%5B0%5D.Type=SearchText&f%5B0%5D.Value=wif&f%5B1%5D.Type=Topic&f%5B1%5D.Value=claims-based%20authentication) na MSDN Code Gallery (as páginas podem estar em inglês).
* [Ferramentas do Windows Identity Foundation 4.5 para o Visual Studio 11 Beta](http://visualstudiogallery.msdn.microsoft.com/e21bf653-dfe1-4d81-b3d3-795cb104066e) na MSDN Code Gallery.
* [Tempo de execução do Windows Identity Foundation (.Net 3.5/4.0)](http://www.microsoft.com/download/details.aspx?id=17331) no MSDN.
* [Exemplos do Windows Identity Foundation 3.5/4.0 e modelos do Visual Studio 2008/2010](http://www.microsoft.com/download/details.aspx?displaylang=en&id=4451) no MSDN.

**Controle de Acesso do AD do Azure (anteriormente conhecido como ACS)**. O Controle de Acesso do AD do Azure é um serviço em nuvem que oferece serviço STS (Security Token) e permite a federação com diferentes provedores de identidade (IdPs) como uma empresa do Active Directory ou IdPs de Internet, como o Windows Live ID/Conta da Microsoft, Facebook, Google, Yahoo! e provedores de identidade Open ID 2.0. Os recursos a seguir fornecem informações detalhadas sobre o Controle de Acesso do AD do Azure:

* [Serviço de Controle de Acesso 2.0](http://msdn.microsoft.com/library/gg429786.aspx) 
* [Cenários e soluções usando o ACS](http://msdn.microsoft.com/library/gg185920.aspx)
* [Instruções do ACS](http://msdn.microsoft.com/library/windowsazure/gg185939.aspx)
* [Um guia para identidade baseada em declarações e controle de acesso](http://msdn.microsoft.com/library/ff423674.aspx)
* [Kit de treinamento de identidade para desenvolvedores](http://www.microsoft.com/download/details.aspx?id=14347)
* [Curso de treinamento de identidade para desenvolvedores hospedado pelo MSDN](http://msdn.microsoft.com/IdentityTrainingCourse)

**Serviços de Federação do Active Directory (AD FS).**Os serviços de Federação do Active Directory (AD FS) 2.0 oferecem suporte para soluções de reconhecimento de declaração de identidade que envolvam Windows Server?? e tecnologia do Active Directory. O AD FS 2.0 oferece suporte ao WS-Trust, WS-Federation e protocolos SAML. Os recursos a seguir fornecem informações detalhadas sobre o AD FS:

* [Mapa de conteúdo do AD FS 2.0](http://social.technet.microsoft.com/wiki/contents/articles/2735.ad-fs-2-0-content-map.aspx)
* [Design de SSO Web][Web SSO Design]
* [Design de SSO Web federado][Federated Web SSO Design]

**Assinaturas de Acesso Compartilhado do Azure.** Assinaturas de Acesso Compartilhado permitem ajustar o acesso a um recurso de contêiner ou blob. Os recursos a seguir fornecem informações detalhadas sobre as assinaturas de Acesso Compartilhado.

* [Gerenciando o acesso a blobs e contêineres](http://msdn.microsoft.com/library/ee393343.aspx)
* [Novo recurso de armazenamento: Assinaturas de Acesso Compartilhado](http://blog.smarx.com/posts/new-storage-feature-signed-access-signatures)
* [Assinaturas de Acesso Compartilhado são fáceis hoje em dia](http://blog.smarx.com/posts/shared-access-signatures-are-easy-these-days)

## Mapa de cenários
Esta seção descreve brevemente os principais cenários abordados neste tópico. Use-o como um mapa para determinar a solução de identidade certa para o aplicativo.

* **Aplicativo Web Form do ASP.NET com Autenticação Federada.** Neste cenário você controla o acesso ao seu aplicativo ASP.NET Web Forms usando uma das identidades de Internet como o Live ID/Conta da Microsoft ou identidade corporativa gerenciada no Active Directory do Windows Server.
* **Serviço do WCF (SOAP) com Autenticação Federada.**Neste cenário você controla o acesso ao serviço WCF (SOAP) usando o Serviço de Identidades gerenciadas pelo Controle de Acesso do AD do Azure.
* **Serviço do WCF (SOAP) com Autenticação Federada, Identidades no Active Directory.** Neste cenário você controla o acesso ao serviço da web WCF (SOAP) usando identidades gerenciadas pelo Active Directory corporativo do Windows Server.
* **Serviço do WCF (REST) com Autenticação Federada.**Neste cenário você controla o acesso ao seu serviço WCF (REST) usando o Serviço de Identidades gerenciadas pelo Controle de Acesso do AD do Azure.
* **Serviço do WCF (REST) com Live ID/Conta da Microsoft, Facebook, Google, Yahoo!, Open ID.** Neste cenário você controla o acesso ao serviço WCF (REST) usando identidade da Internet, como o Live ID/Conta da Microsoft.
* **Aplicativo Web ASP.NET para serviço REST com WCF usando Token SWT compartilhado.** Nesse cenário, você tem um aplicativo distribuído com um aplicativo Web ASP.NET front-end e serviço REST downstream e você deseja o fluxo do contexto do usuário final por meio de níveis físicos.
* **Autorização de controle de acesso baseado em função (RBAC) em serviços e aplicativos com reconhecimento de declaração.** Nesse cenário, você deseja implementar a lógica de autorização em seu aplicativo com base em funções.
* **Autorização baseada em declaração em serviços e aplicativos com reconhecimento de declaração.** Nesse cenário, você deseja implementar a lógica de autorização em seu aplicativo com base em funções de autorização complexas.
* **Identidade de serviço de armazenamento do Azure e cenários de acesso.**Nesse cenário, você precisa compartilhar o acesso de forma segura com os blobs e contêineres de armazenamento do Azure.
* **Identidade do banco de dados SQL do Azure e cenários de acesso.**O Banco de Dados SQL oferece suporte apenas a Autenticação do SQL Server. Não há suporte para a autenticação do Windows (segurança integrada). Os usuários devem fornecer credenciais (login e senha) toda vez que se conectarem ao Banco de Dados SQL.
* **Cenários de acesso e identidade do barramento de serviço do Azure.**Nesse cenário, você precisa acessar com segurança filas do Barramento de Serviço do Azure.
* **Cenários de acesso e identidade de cache em memória.**Nesse cenário, você precisa acessar com segurança os dados gerenciados pelo cache em memória.
* **Cenários de acesso e identidade de Marketplace do Azure.**Nesse cenário, você precisa acessar com segurança conjuntos de dados do Marketplace do Azure.

## Cenários de acesso e identidade do Azure
Esta seção descreve os cenários comuns de acesso e identidade para arquiteturas de aplicativos diferentes. Use o Mapa de Cenários para uma rápida orientação.

### Aplicativo Web Form do ASP.NET com Autenticação Federada
Nesse cenário de arquitetura de aplicativo seu aplicativo Web pode ser implantado no Azure ou no local. O aplicativo requer que os usuários sejam autenticados ou pelo Active Directory corporativo ou pelos provedores de identidade da Internet.

Para resolver esse cenários, use o Controle de Acesso do AD do Azure e o Windows Identity Foundation.

![Access Control do Active Directory do Azure][03]

Consulte os recursos a seguir para implementar este cenário:

* [Como: Criar meu primeiro aplicativo ASP.NET com reconhecimento de declarações usando o ACS](http://msdn.microsoft.com/library/gg429779.aspx)
* [Como: Hospedar páginas de logon no seu aplicativo da web ASP.NET](http://msdn.microsoft.com/library/gg185926.aspx)
* [Como: implementar a autorização de declarações em um aplicativo ASP.NET com reconhecimento de declarações usando WIF e ACS](http://msdn.microsoft.com/library/gg185907.aspx)    
* [Como: implementar o controle de acesso baseado em função (RBAC) em um aplicativo ASP.NET com reconhecimento de declarações, usando WIF e ACS](http://msdn.microsoft.com/library/gg185914.aspx)
* [Como: Configurar a confiança entre o ACS e os aplicativos da web ASP.NET usando os certificados X.509](http://msdn.microsoft.com/library/gg185947.aspx)
* [Exemplo de código: formulários simples do ASP.NET](http://msdn.microsoft.com/library/gg185938.aspx)

### Serviço do WCF (SOAP) com identidade do serviço
Nesse cenário de arquitetura de aplicativo seu serviço WCF (SOAP) pode ser implantado para Azure ou no local. O serviço está sendo acessado como um serviço downstream por uma aplicação web ou até mesmo por outro serviço web. Você precisa controlar o acesso a ele usando a identidade específica do aplicativo. Pense em termos de tipo de conta de pool de aplicativo que você usou no IIS: embora a tecnologia seja diferente, as abordagens são semelhantes no fato de que o serviço é acessado por meio de uma conta de escopo do aplicativo ou conta de usuário final.

Use o recurso de Identidade do Serviço no Serviço de Controle de Acesso do Azure AD. Isso é semelhante à conta do pool de aplicativos IIS que você estava usando ao implantar seus aplicativos para o Windows Server e o IIS. Configure o Access Control do AD do Azure para emitir tokens SAML que serão manipulados pelo WIF no serviço WCF (SOAP).

![Serviço WCF (SOAP)][04]

Consulte os recursos a seguir para implementar este cenário:

* [Como: Adicionar identidades de serviço com um certificado, senha ou chave simétrica do X.509](http://msdn.microsoft.com/library/gg185924.aspx)
* [Como: Autenticar com um certificado cliente em um serviço WCF protegido pelo ACS](http://msdn.microsoft.com/library/hh289316.aspx)
* [Como: Autenticar com nome de usuário e senha em um serviço WCF protegido pelo ACS](http://msdn.microsoft.com/library/gg185954.aspx)
* [Exemplo de código: autenticação do certificado WCF](http://msdn.microsoft.com/library/gg185952.aspx)
* [Exemplo de código: autenticação do nome de usuário WCF](http://msdn.microsoft.com/library/gg185927.aspx)

### Serviço do WCF (SOAP) com Autenticação Federada, Identidades no Active Directory
Nesse cenário de arquitetura de aplicativo seu serviço WCF (SOAP) pode ser implantado para Azure ou no local. Você precisa controlar o acesso a ele usando uma identidade que é gerenciada por Active Directory (AD) do Windows Server corporativo.

Use o Access Control do AD do Azure configurado para federação com o AD FS do Windows Server. Nesse caso não é necessário configurar a identidade do Serviço com o controle de acesso do AD do Azure. O agente que precisa acessar o serviço WCF (SOAP) fornece credenciais para AD FS e após a autenticação bem-sucedida o token é emitido pelo AD FS. O token então é enviado ao Controle de Acesso do AD do Azure AD e emitido novamente de volta para o agente. O agente usa o token para enviar solicitação para o serviço WCF (SOAP).

![Serviço WCF (SOAP) com AD][05]

Consulte os recursos a seguir para implementar este cenário:

* [Como: Adicionar identidades de serviço com um certificado, senha ou chave simétrica do X.509](http://msdn.microsoft.com/library/gg185924.aspx)
* [Como: Configurar o AD FS 2.0 como um provedor de identidade](http://msdn.microsoft.com/library/gg185961.aspx)
* [Como: Usar o serviço de gerenciamento para configurar o AD FS 2.0 como Provedor de identidade empresarial](http://msdn.microsoft.com/library/gg185905.aspx)
* [Exemplo de código: autenticação federada WCF com AD FS 2.0](http://msdn.microsoft.com/library/hh127796.aspx)

### Serviço do WCF (REST) com Identidades do Serviço
Nesse cenário de arquitetura de aplicativo seu serviço WCF (REST) pode ser implantado para Azure ou no local. O serviço é acessado como um serviço downstream por um aplicativo web ou até mesmo por outro serviço web. Você precisa controlar o acesso a ele usando uma identidade específica para aplicativos. Pense em termos de tipo de conta de pool de aplicativo que você usou no IIS: embora a tecnologia seja diferente, as abordagens são semelhantes no fato de que o serviço é acessado por meio de uma conta de escopo do aplicativo ou conta de usuário final.

Use o recurso de Identidade do Serviço no Serviço de Controle de Acesso do Azure AD. Configure o Controle de Acesso do AD do Azure para emitir tokens de Simple Web Token (SWT). Para lidar com o token SWT no lado do serviço REST, você pode implementar um manipulador de token personalizado e conectá-lo ao pipeline WIF ou analisá-lo "manualmente", sem usar a infraestrutura WIF.

Considere o diagrama a seguir (o WIF é opcional):

![Serviço REST][06]

Consulte os recursos a seguir para implementar este cenário:

* [Como: Configurar a confiança entre o ACS e o serviço WCF usando chaves simétricas](http://msdn.microsoft.com/library/gg185958.aspx)
* [Como: Autenticar para um serviço REST WCF implantado no Azure usando o ACS](http://msdn.microsoft.com/library/hh289317.aspx)
* [Exemplo de código: serviço web do ASP.NET](http://msdn.microsoft.com/library/gg983271.aspx)
* [Exemplo de código: aplicativo do Windows Phone 7](http://msdn.microsoft.com/library/gg983271.aspx)
* [WCF REST com Token SWT emitido pelo Serviço de Controle de Acesso (ACS) do Azure](http://code.msdn.microsoft.com/REST-WCF-With-SWT-Token-123d93c0)

### Serviço do WCF (REST) com Live ID/Conta da Microsoft, Facebook, Google, Yahoo!, Open ID
Nesse cenário de arquitetura de aplicativo seu serviço WCF (REST) pode ser implantado para Azure ou no local. Você precisa controlar o acesso a ele usando uma identidade pública da Internet, como o Live ID/Conta da Microsoft ou Facebook.

Uso do Access Control do AD do Azure para emitir tokens SWT Para lidar com o token SWT no lado do serviço REST, você pode implementar um manipulador de token personalizado e conectá-lo ao pipeline WIF ou analisá-lo "manualmente", sem usar a infraestrutura WIF.

É importante observar que, para implementar este cenário, o aplicativo precisa usar o controle de navegador da web para coletar credenciais de usuário final. Isso torna-o inadequado para cenários nos quais o serviço REST é acessado por meio de um aplicativo da web ASP.NET. Ele só é adequado para cenários nos quais o serviço REST está sendo acessado pelo aplicativo cliente do usuário, como um aplicativo do Windows Phone 7 ou um cliente avançado da área de trabalho. O motivo principal para exibir o controle de navegador da web é que as identidades de Internet não oferecem suporte nativo a cenários de perfil ativo (cenário de serviços web). As identidades de Internet oferecem suporte principalmente a cenários de perfil passivo (aplicativos web) que dependem de redirecionamentos do navegador: onde o controle de navegador da web é útil.

Considere o diagrama a seguir (o WIF é opcional, por isso não foi exibido):

![O WIF é opcional.][07]

Consulte os recursos a seguir para implementar este cenário:

* [Como: Autenticar para um serviço REST WCF implantado no Azure usando o ACS](http://msdn.microsoft.com/library/hh289317.aspx)
* [Como: Configurar o Google como um provedor de identidade](http://msdn.microsoft.com/library/gg185976.aspx)
* [Como: Configurar o Facebook como um provedor de identidade](http://msdn.microsoft.com/library/gg185919.aspx)
* [Como: Configurar o Yahoo! como um provedor de identidade](http://msdn.microsoft.com/library/gg185977.aspx)
* [Código de exemplo: Aplicativo do Windows Phone 7](http://msdn.microsoft.com/library/gg983271.aspx)
* [WCF REST com Token SWT emitido pelo Serviço de Controle de Acesso (ACS) do Azure](http://code.msdn.microsoft.com/REST-WCF-With-SWT-Token-123d93c0)

### Aplicativo Web ASP.NET para Serviço REST com WCF usando Token SWT Compartilhado
Neste cenário, você tem um aplicativo distribuído com um aplicativo Web ASP.NET front-end e serviço REST downstream e você deseja manter o contexto do usuário final por meio de níveis físicos. Isso às vezes é necessário ao implementar lógica de autorização ou o registro com base na identidade do usuário final no serviço REST downstream.

Configuração do Controle de Acesso do Azure AD para emitir token SWT. O token SWT é emitido para o aplicativo web ASP.NET front-end e, em seguida, compartilhado com o serviço REST downstream. Nesse caso, existe apenas uma parte dependente configurada no Controle de Acesso do AD do Azure. No entanto, existem várias advertências:

* Como o WIF não fornece um identificador de token SWT pronto para o uso, você precisa implementar um manipulador de token personalizado para ser usado com o aplicativo web ASP.NET. Você deve contar com o trabalho mais pesado que o WIF faz para oferecer suporte ao protocolo de WS-Federation que depende de redirecionamentos do navegador versus implementá-lo por conta própria.
* Ao implementar um manipulador de token SWT personalizado, verifique seque o token de inicialização está sendo abordado para certificar-se de que ele foi mantido. Caso contrário, não será possível compartilhá-lo e enviá-lo para o serviço REST downstream.
* Você não precisa usar o WIF em um serviço REST; em vez disso, você pode analisar o token "manualmente" porque não é necessário para manipular redirecionamentos nesse caso.

![Aplicativo Web do ASP.NET][08]

Consulte os recursos a seguir para implementar este cenário:

* [Como: Configurar o Google como um provedor de identidade](http://msdn.microsoft.com/library/gg185976.aspx)
* [Como: Configurar o Facebook como um provedor de identidade](http://msdn.microsoft.com/library/gg185919.aspx)
* [Como: Configurar o Yahoo! como um provedor de identidade](http://msdn.microsoft.com/library/gg185977.aspx)
* [Aplicativo Web ASP.NET para Serviço REST com WCF usando Token SWT Compartilhado](http://code.msdn.microsoft.com/ASPNET-Web-App-To-REST-WCF-b2b95f82)

### Controle de acesso baseado em função (RBAC) em serviços e aplicativos com reconhecimento de declaração
Nesse cenário, você precisa implementar a autorização em seu aplicativo web ou serviço com base nas funções do usuário: usuário com as funções exigidas obtêm acesso e aqueles que não possuem as funções exigidas obtêm acesso negado. Em outras palavras, o aplicativo precisa responder à simples pergunta: o usuário está na função X?

Há várias maneiras de se resolver essa situação. Você pode usar o Controle de Acesso do AD do Azure, Gerenciador de Autenticação de Declarações WIF, o mapeamento de samlSecurityTokenRequirement ou o Gerenciador de Funções do Cliente.

O WIF é usado em todos os casos. O WIF oferece suporte ao método IPrincipal.IsInRole("MyRole"). Na maioria dos casos a chave é certificar-se de que há declaração de tipo de função com URI de http://schemas.microsoft.com/ws/2008/06/identity/claims/role no token para que esse WIF possa verificar com êxito a participação em funções quando o método IsInRole é chamado.

**Access Control do AD do Azure**. Nesta implementação, o mecanismo de regra de transformação de declarações do Controle de Acesso do AD do Azure é usado. Usando as regras do mecanismo de regra de transformação de declarações você pode transformar qualquer declaração de entrada em uma declaração de tipo de função para que quando o token chegue ao aplicativo ou a um serviço o WIF possa analisar essa declaração de função para certificar-se de que a chamada do método IsInRole foi bem-sucedida.

![][09]

**WIF ClaimsAuthenticationManager**. Nesta implementação use o ClaimsAuthenticationManager como ponto de extensibilidade do WIF. Usando essa abordagem você transformar quaisquer declarações arbitrárias de entrada em um tipo de declaração de função no aplicativo. A complexidade da transformação é limitada apenas pelo código que você escreve.

![][10]

**mapeamento de samlSecurityTokenRequriement** Nesta implementação, você usa a configuração samlSecurityTokenRequirement no web.config para informar ao WIF que tipos de declaração se comportam como se fossem tipos de declaração de função. Por exemplo, se o token executa uma declaração de tipo de grupo, você pode mapeá-lo para o tipo de declaração de função. Você pode obter apenas os mapeamentos simples usando essa abordagem.

![][11]

**RoleManager personalizado.** Nesta implementação você implementa o RoleManager personalizado. O WIF é usado para examinar as declarações de entrada ao implementar métodos de interface RoleManager personalizados como GetAllRoles().

![][12]

Consulte os recursos a seguir para implementar este cenário:

* [Como: implementar o controle de acesso baseado em função (RBAC) em um aplicativo ASP.NET com reconhecimento de declarações, usando WIF e ACS](http://msdn.microsoft.com/library/gg185914.aspx)
* [Como: implementar a lógica de transformação do token usando regras](http://msdn.microsoft.com/library/gg185955.aspx)
* [Autorização com RoleManager para aplicativos Web ASP.NET com reconhecimento de declarações (WIF)](http://blogs.msdn.com/b/alikl/archive/2010/11/18/authorization-with-rolemanager-for-claims-aware-wif-asp-net-web-applications.aspx)
* Código de exemplo: usando declarações em IsInRole no [SDK do Windows Identity Foundation](http://www.microsoft.com/downloads/details.aspx?FamilyID=c148b2df-c7af-46bb-9162-2c9422208504)

### Autorização Baseada em Declaração em Serviços e Aplicativos com Reconhecimento de Declaração
Nesse cenário, você precisa implementar a lógica de autorização complexa em seu serviço ou aplicativo web e o método IsInRole() não satisfaz suas necessidades de autorização. Se a abordagem da autorização se baseia nas funções, considere implementar controle de acesso baseado em função descrito na seção anterior.

Use ClaimsAuthorizationManager como o ponto de extensibilidade WIF. O ClaimsAuthorizationManager permite que o acesso externo verifique as chamadas para que o código do aplicativo seja mais limpo e mais passível de manutenção do que quando as verificações de acesso são implementadas no código do aplicativo.

![][13]

Consulte os recursos a seguir para implementar este cenário:

* [Como: implementar a lógica de transformação do token usando regras](http://msdn.microsoft.com/library/gg185955.aspx)
* [Como: implementar a autorização de declarações em um aplicativo ASP.NET com reconhecimento de declarações usando WIF e ACS](http://msdn.microsoft.com/library/gg185907.aspx)
* Código de exemplo: Autorização com base em declarações no [SDK do Windows Identity Foundation](http://www.microsoft.com/downloads/details.aspx?FamilyID=c148b2df-c7af-46bb-9162-2c9422208504)

## Identidade do serviço de armazenamento do Azure e cenários de acesso
Nesse cenário, você precisa compartilhar o acesso com segurança aos blobs e contêineres de armazenamento do Azure.

Uso de Assinaturas de Acesso Compartilhado. Para acessar sua conta de serviço de armazenamento de seu próprio aplicativo, use o hash compartilhado disponível por meio do portal do Azure ao configurar e gerenciar suas contas de serviço de armazenamento. Quando você desejar conceder a alguém mais acesso aos blobs e contêineres da sua conta de serviço de armazenamento, use as URLs de Assinaturas de Acesso Compartilhado.

Preste atenção especial para o gerenciamento seguro de informações para evitar a exposição. Além disso, preste especial atenção à vida útil das Assinaturas de Acesso Compartilhado.

![][14]

Consulte os recursos a seguir para resolver este cenário

* [Gerenciando o acesso a blobs e contêineres](http://msdn.microsoft.com/library/ee393343.aspx)
* [Novo recurso de armazenamento: Assinaturas de Acesso Compartilhado](http://blog.smarx.com/posts/new-storage-feature-signed-access-signatures)
* [Assinaturas de Acesso Compartilhado são fáceis hoje em dia](http://blog.smarx.com/posts/shared-access-signatures-are-easy-these-days)

## Cenários de acesso e identidade do Banco de Dados SQL do Azure
O Banco de dados SQL somente dá suporte para a autenticação do SQL Server. Não há suporte para a autenticação do Windows (segurança integrada). Os usuários devem fornecer credenciais (login e senha) toda vez que se conectarem ao Banco de Dados SQL. Preste atenção especial ao gerenciar seu nome de usuário e senha para evitar a divulgação de informações.

![][15]

Para resolver esse cenário, consulte o seguinte tópico da Ajuda:<br/>[Desenvolvimento de Banco de Dados SQL do Azure: tópicos de instrução](http://msdn.microsoft.com/library/azure/ee621787.aspx)

Ou consulte um dos seus diversos tópicos secundários, entre eles:

* [Como: Conectar-se ao Banco de Dados SQL do Microsoft Azure usando o sqlcmd](http://msdn.microsoft.com/library/azure/ee336280.aspx)
* [Exemplo de código: repetir a lógica para se conectar ao Banco de Dados SQL do Azure com o ADO.NET](http://msdn.microsoft.com/library/azure/ee336243.aspx)
* [Como conectar-se ao Banco de Dados SQL usando PHP](http://msdn.microsoft.com/library/azure/ff394110.aspx)
* [Como: Conectar-se ao Banco de dados SQL do Microsoft Azure usando o JDBC](http://msdn.microsoft.com/library/azure/gg715284.aspx)

Ou consulte:<br/> [Limitações e diretrizes de segurança do Banco de Dados SQL do Azure](http://msdn.microsoft.com/library/azure/ff394108.aspx#authentication)

## Identidade do Serviço de Barramento do Azure e cenários de acesso
O Barramento de Serviço e o Controle de Acesso do AD do Azure possuem uma relação especial em que cada namespace do Barramento de Serviço é associado a um namespace de serviço do Controle de Acesso correspondente de mesmo nome, com o sufixo "-sb". A razão para esta relação especial está na maneira como o Barramento de Serviço do Controle de Acesso gerencia sua relação de confiança mútua e os segredos de criptografia associados. Consulte os recursos listados abaixo para obter mais detalhes.

![][16]

Consulte os recursos a seguir para resolver este cenário:

* [Proteção do Service Bus com o ACS (a página pode estar em inglês)](http://channel9.msdn.com/posts/Securing-Service-Bus-with-ACS) (vídeo)
* [Proteção do Service Bus com ACS (a página pode estar em inglês)](https://skydrive.live.com/view.aspx?cid=123CCD2A7AB10107&resid=123CCD2A7AB10107%211849) (Slides)
* [Autenticação e autorização do barramento de serviço com o serviço de controle de acesso](http://msdn.microsoft.com/library/hh403962.aspx)

## Cenários de acesso e identidade do Cache em memória
O cache em memória (anteriormente conhecido como Caching do Azure) depende do Controle de Acesso do AD do Azure para autenticação. Ele usa chaves compartilhadas disponíveis por meio do portal de gerenciamento. Use as chaves em seus arquivos de configuração ou código ao acessar o cache. Certifique-se de armazenar as chaves de segurança para evitar a divulgação de informações.

![][17]

Consulte os recursos a seguir para resolver este cenário:

* [Como: Configurar um cliente de cache de forma programática para o cache do Azure](http://msdn.microsoft.com/library/windowsazure/gg618003.aspx)
* [Como: Configurar um cliente de cache usando o arquivo de configuração de aplicativo do cache do Azure](http://msdn.microsoft.com/library/windowsazure/gg278346.aspx)
* [Exemplos de cache e barramento de serviço do Azure](http://msdn.microsoft.com/library/ee706741.aspx) (seção de exemplos de cache)

## Cenários de Acesso e identidade de Marketplace do Azure
Cada acesso a um conjunto de dados do Azure Marketplace, seja gratuito ou pago, deve autenticar o usuário antes de obter acesso. Quando você cria um aplicativo, o processo de autenticação deve ser incluído em seu código. Considere os seguintes cenários comuns:

### Eu acesso o meu conjunto de dados
Nesse cenário, você está criando um aplicativo que consome os conjuntos de dados na sua assinatura do Marketplace. Você é o usuário do aplicativo. O aplicativo pode ser implantado no Azure, local ou no Marketplace.

Use a chave compartilhada que está disponível através de sua assinatura do Marketplace. Obtenha a chave compartilhada usando o portal do Marketplace.

![][18]

Consulte os recursos a seguir para resolver este cenário:

* [Uso de HTTP Basic Auth no seu aplicativo do Marketplace](http://msdn.microsoft.com/library/gg193417.aspx)

### Os usuários acessam os meus conjuntos de dados
Nesse cenário, você está criando um aplicativo que permite que os usuários acessem o seu conjunto de dados. O aplicativo pode ser implantado no Azure, no local ou no Marketplace.

Para resolver esse cenário, use delegação OAuth. Os usuários serão solicitados a fornecer seu Live ID/credenciais da conta da Microsoft e, em seguida, serão colocados no processo de consentimento.

![][19]

Consulte os recursos a seguir para resolver este cenário:

* [Exemplo de cliente Web OAuth](http://go.microsoft.com/fwlink/?LinkId=219162)
* [Exemplo de cliente OAuth avançado](http://go.microsoft.com/fwlink/?LinkId=219163)

### API do Marketplace de acesso do aplicativo
Nesse cenário, você está criando um aplicativo que acessa a API do Marketplace. A API do mercado exige autenticação para realizar com êxito chamadas para esse aplicativo. O aplicativo está implantado no Marketplace do Azure.

![][20]

Consulte o mercado kit de publicação no Marketplace para obter detalhes sobre a implementação da autenticação.

Consulte os recursos a seguir para resolver este cenário:

* [Baixar o Kit de publicação de aplicativo](http://go.microsoft.com/fwlink/?LinkId=221323)
* [Introdução ao Azure Marketplace para aplicativos](https://datamarket.azure.com/)

## Botões de segurança
Esta seção descreve os botões de segurança do Windows Identity Foundation e do Controle de Acesso do AD do Azure. Use-a como uma lista de verificação de segurança básica para essas tecnologias ao projetar e implantar seu aplicativo.

### Windows Identity Foundation
A seguir estão os principais botões de segurança de WIF. As informações a seguir são um resumo de [Considerações sobre design do WIF](http://msdn.microsoft.com/library/ee517298.aspx) e [Segurança do Windows Identity Foundation (WIF) para aplicativos Web ASP.NET - ameaças e contramedidas](http://blogs.msdn.com/b/alikl/archive/2010/12/02/windows-identity-foundation-wif-security-for-asp-net-web-applications-threats-amp-countermeasures.aspx)

* **IssuerNameRegistry**. Especifica os Serviços de Token de Segurança (STS) confiáveis. Certifique-se de que apenas STS confiáveis estão listados.
* **cookieHandler requireSsl="true"**. Especifica se os cookies de sessão são transferidos através do protocolo SSL.
* **wsFederation's requireHttps="true"**. Especifica se a comunicação de protocolo de federação com o provedor de identidade é realizada através do protocolo SSL.
* **tokenReplayDetection enabled="true"**. Especifica se o recurso de detecção de replay de token está habilitado. Lembre-se de que esse recurso cria afinidade do servidor devido ao fato de ele gerenciar cópias locais dos tokens usados.
* **audienceUris**. Especifica o público-alvo do token. Se seu aplicativo recebe um token que não foi destinado para seu aplicativo, ele será rejeitado pelo WIF.
* **requestValidation** e **httpRuntime requestValidationType**. Ativa/desativa o recurso de validação do ASP.NET. Consulte orientações conforme descrito em [Windows Identity Foundation (WIF): um valor Request.Form potencialmente perigoso foi detectado no cliente](http://social.technet.microsoft.com/wiki/contents/articles/1725.windows-identity-foundation-wif-a-potentially-dangerous-request-form-value-was-detected-from-the-client-wresult-t-requestsecurityto.aspx)

### Controle de Acesso do AD do Azure
Considere os seguintes botões de segurança na implantação do Access Control do AD do Azure. As informações a seguir são um resumo de [Diretrizes de segurança do ACS](http://msdn.microsoft.com/library/gg185962.aspx) e de [Diretrizes de gerenciamento de certificados e chaves](http://msdn.microsoft.com/library/hh204521.aspx).

* **Expiração de tokens do STS**. Use o portal de gerenciamento do Controle de Acesso do AD do Azure para definir expiração agressiva do token.
* **Validação de dados ao usar o recurso URL de Erro**. O recurso de URL de Erro do Controle de Acesso do AD do Azure requer acesso anônimo à página do aplicativo para onde ele envia as mensagens de erro. Suponha que todos os dados que chegam a esta página são perigosos e de fonte não confiável.
* **Criptografia de tokens para cenários altamente confidenciais**. Para atenuar a ameaça de divulgação de informações disponíveis no token, recomenda-se criptografar os tokens.
* **Criptografia de cookies usando RSA ao implantar o Azure**. O WIF criptografa cookies usando DPAPI por padrão. Ele cria a afinidade do servidor e pode resultar em exceções quando implantado em web farms e ambientes do Azure. Use RSA em cenários do Azure e web farm.
* **Certificado de assinatura de tokens**. Renove certificados de assinatura de tokens periodicamente para evitar a negação de serviço. O Controle de Acesso do AD do Azure assina todos os tokens de segurança que ele emite. Os certificados X.509 são usados para autenticação quando você cria um aplicativo que consome tokens SAML emitidos pelo ACS. Quando os certificados de assinatura expirarem, você receberá erros ao tentar solicitar um token.
* **Chaves de assinatura de tokens**. Renove as chaves de assinatura de tokens periodicamente para evitar a negação de serviço. O Controle de Acesso do AD do Azure assina todos os tokens de segurança que ele emite. Chaves de autenticação simétricas de 256 bits são usadas quando você cria um aplicativo que consome tokens SWT emitidos pelo ACS. Quando as chaves de assinatura expirarem, você receberá erros ao tentar solicitar um token.
* **Certificado de criptografia de tokens**. Renove certificados de criptografia de tokens periodicamente para evitar a negação de serviço. A criptografia do tokens será necessária se um aplicativo de uma terceira parte confiável for um serviço web que usa tokens de prova de posse através do protocolo WS-Trust. Em outros casos, a criptografia do token é opcional. Quando os certificados de criptografia expirarem, você receberá erros ao tentar solicitar um token.
* **Certificados de descriptografia de tokens**. Renove certificados de descriptografia de tokens periodicamente para evitar a negação de serviço. O Controle de Acesso do AD do Azure AD pode aceitar tokens criptografados de provedores de identidade de WS-Federation (por exemplo, AD FS 2.0). Um certificado X.509 hospedado no Controle de Acesso do AD do Azure é usado na descriptografia. Quando os certificados de descriptografia expirarem, você receberá erros ao tentar solicitar um token.
* **Credenciais de identidade do serviço**. Renove as credenciais de Identidade do Serviço periodicamente para evitar a negação de serviço. As identidades de serviço usam credenciais configurados globalmente para o seu namespace do Controle de Acesso do AD do Azure que permitem que aplicativos ou clientes autentiquem diretamente com o Controle de Acesso do AD do Azure e recebam um token. Existem três tipos de credenciais com as quais a identidade do serviço do Access Control do AD do Microsoft Azure pode ser associada: chave simétrica, senha e certificado X.509. Você começará a receber exceção quando as credenciais estiverem expiradas.
* **Credenciais da conta do Serviço de Gerenciamento do Controle de Acesso do AD do Azure**. Renove as credenciais do serviço de Gerenciamento periodicamente para evitar a negação de serviço. O Serviço de Gerenciamento do Controle de Acesso do AD do Azure é um componente-chave que permite gerenciar e configurar as definições do seu namespace do Controle de Acesso do AD do Azure de forma programática. Existem três tipos de credenciais com as quais a conta do serviço de Gerenciamento pode ser associada. Os tipos são chave simétrica, senha e certificado X.509. Você começará a receber exceção quando as credenciais estiverem expiradas.
* **Assinatura de provedor de identidade do WS-Federation e certificados de criptografia**. Consulte a validade do certificado do provedor de identidade do WS-Federation evitar a negação de serviço. O Certificado do provedor de identidade do WS-Federation está disponível por meio de seus metadados. Ao configurar o provedor de identidade do WS-Federation, como o AD FS, o certificado de assinatura do WS-Federation é configurado por meio de metadados do WS-Federation disponíveis via URL ou como um arquivo. Depois de configurar o provedor de identidade do WS-Federation, use o serviço de gerenciamento do Controle de Acesso do AD do Azure para consultá-lo e verificar a validade dos seus certificados. Quando os certificados expirarem, você começará a receber exceções.

## Hospedagem compartilhada usando Sites do Azure
Todos os cenários e as soluções descritos neste tópico são válidos quando o aplicativo é hospedado nos Sites do Azure.

## Máquinas Virtuais do Azure
Todos os cenários e as soluções descritos neste tópico são válidos quando o aplicativo é hospedado nas Máquinas Virtuais do Azure.

## Recursos
* [Kit de treinamento de identidade para desenvolvedores](http://go.microsoft.com/fwlink/?LinkId=214555)
* [Curso de treinamento de identidade para desenvolvedores hospedado pelo MSDN](http://go.microsoft.com/fwlink/?LinkId=214561)
* [Um guia para identidade baseada em declarações e Controle de Acesso](http://go.microsoft.com/fwlink/?LinkId=214562)
* [Serviço de Controle de Acesso](http://msdn.microsoft.com/library/windowsazure/gg429786.aspx)
* [Instruções do ACS](http://msdn.microsoft.com/library/windowsazure/gg185939.aspx)
* [Proteção de aplicativo web ASP.NET com função web do Azure usando o Access Control Service v2.0](http://social.technet.microsoft.com/wiki/contents/articles/2590.aspx)
* [Academy Videos do Access Control Service (ACS) do AD do Azure](http://social.technet.microsoft.com/wiki/contents/articles/2777.aspx)
* [Ciclo de vida de desenvolvimento de segurança da Microsoft](http://www.microsoft.com/security/sdl/default.aspx)
* [Ferramenta de modelagem de ameaças do SDL 3.1.8](http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=2955)
* [Blogs de privacidade e segurança](http://www.microsoft.com/about/twc/en/us/blogs.aspx)
* [Security Response Center](http://www.microsoft.com/security/msrc/default.aspx)
* [Relatório de inteligência de segurança](http://www.microsoft.com/security/sir/)
* [Ciclo de vida de desenvolvimento de segurança](http://www.microsoft.com/security/sdl/default.aspx)
* [Security Developer Center (MSDN)](http://msdn.microsoft.com/security/)

[01]: ./media/SecurityRX/01_SecuringTheApplication.gif
[02]: ./media/SecurityRX/02_ThreatsVulnerabilitiesandAttacks.gif
[03]: ./media/SecurityRX/03_WindowsAzureADAccesscontrol.gif
[04]: ./media/SecurityRX/04_WCF(SOAP)Service.gif
[05]: ./media/SecurityRX/05_AzureADAccessControl.gif
[06]: ./media/SecurityRX/06_RESTService.gif
[07]: ./media/SecurityRX/07_WIFisOptional.gif
[08]: ./media/SecurityRX/08_ASPNETWebApptoREST.gif
[09]: ./media/SecurityRX/09_RBAC.gif
[10]: ./media/SecurityRX/10_WIFClaimsAuthenticationManager.gif
[11]: ./media/SecurityRX/11_SecurityTokenRequriementmapping.gif
[12]: ./media/SecurityRX/12_CustomRoleManager.gif
[13]: ./media/SecurityRX/13_ClaimsAuthorizationManager.gif
[14]: ./media/SecurityRX/14_WindowsAzurestorage.gif
[15]: ./media/SecurityRX/15_SQLAzureIdentityandAccessScenarios.gif
[16]: ./media/SecurityRX/16_WindowsAzureServiceBusIdentity.gif
[17]: ./media/SecurityRX/17_WindowsAzureCacheIdentity.gif
[18]: ./media/SecurityRX/18_IAccessMyDataset.gif
[19]: ./media/SecurityRX/19_UsersAccessMyDatasets.gif
[20]: ./media/SecurityRX/20_ApplicationAccessMarketplaceAPI.gif

[Web SSO Design]: http://technet.microsoft.com/library/dd807033(WS.10).aspx
[Federated Web SSO Design]: http://technet.microsoft.com/library/dd807050(WS.10).aspx

<!---HONumber=Oct15_HO3-->