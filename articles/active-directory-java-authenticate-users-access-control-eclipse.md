<properties linkid="develop-java-how-to-guides-access-control" urlDisplayName="Controle de Acesso" pageTitle="Como usar o Controle de Acesso (Java) - Guia do recurso do Azure" metaKeywords="" description="Saiba como desenvolver e usar o Controle de Acesso com o Java no Azure." metaCanonical="" services="active-directory" documentationCenter="Java" title="Como autenticar usuários da Web com o Access Control Service do Azure usando o Eclipse" videoId="" scriptId="" authors="waltpo" solutions="" manager="keboyd" editor="mollybos" />

# Como autenticar usuários da Web com o Access Control Service do Azure usando o Eclipse

Este guia mostrará como usar o ACS (Access Control Service) do Azure dentro do plug-in para Eclipse com o Java (da Microsoft Open Technologies). Para obter mais informações sobre o ACS, consulte a seção [Próximas etapas](#next_steps).

> [WACOM.NOTE]
> O Filtro de Controle dos Serviços de Acesso do Azure (da Microsoft Open Technologies) é uma visualização de tecnologia da comunidade. Como software de pré-lançamento, ele não é formalmente suportado pela Microsoft Open Technologies, Inc. nem pela Microsoft.

## Sumário

-   [O que é ACS?][]
-   [Conceitos][]
-   [Pré-requisitos][]
-   [Criar um namespace do ACS][]
-   [Adicionar provedores de identidade][]
-   [Adicionar um aplicativo de terceira parte confiável][]
-   [Criar regras][]
-   [Carregar um certificado para seu namespace do ACS][]
-   [Revisar a página Integração de Aplicativos][]
-   [Criar um aplicativo web Java][]
-   [Adicionar a Biblioteca de Filtros do ACS ao seu aplicativo][]
-   [Implantar no emulador de computação][]
-   [Implantar no Azure][]
-   [Próximas etapas][]

## <a name="what-is"></a>O que é o ACS?

A maioria dos desenvolvedores não são especialistas em identidade e geralmente não querem gastar tempo desenvolvendo mecanismos de autenticação e autorização para seus aplicativos e serviços. O ACS é um serviço do Azure que fornece uma maneira fácil de autenticar os usuários que precisam acessar seus aplicativos e serviços da web sem a necessidade de fatorar lógica de autenticação complexa em seu código.

Os seguintes recursos estão disponíveis no ACS:

-   Integração com o Windows Identity Foundation (WIF).
-   Suporte para IPs (provedores de identidade) populares da Web incluindo o Windows Live ID, o Google, o Yahoo e o Facebook.
-   Suporte para os Serviços de Federação do Active Directory (AD FS) 2.0.
-   Um serviço de gerenciamento baseado em Open Data Protocol (OData) que fornece acesso programático para configurações do ACS.
-   Um Portal de Gerenciamento que permite o acesso administrativo às configurações do ACS.

Para obter mais informações sobre o ACS, consulte [Access Control Service 2.0][].

## <a name="concepts"></a>Conceitos

O ACS do Azure baseia-se nas entidades de identidade baseada em declarações - uma abordagem consistente para a criação de mecanismos de autenticação para aplicativos executados localmente ou na nuvem. A identidade baseada em declarações fornece uma maneira comum para aplicativos e serviços adquirirem as informações de identidade necessárias sobre os usuários em sua organização, em outras organizações e na Internet.

Para concluir as tarefas deste guia, você precisa compreender os seguintes conceitos:

**Cliente** - No contexto deste guia de instruções, esse é um navegador que está tentando obter acesso ao seu aplicativo web.

**Aplicativo de RP (terceira parte confiável)** - um aplicativo RP é um site ou um serviço web que terceiriza a autenticação para uma autoridade externa. No jargão de identidade, dizemos que o RP confia nessa autoridade. Este guia explica como configurar seu aplicativo para confiar no ACS.

**Token** - um token é uma coleção de dados de segurança que geralmente são emitidos após a autenticação bem-sucedida de um usuário. Ele contém um conjunto de *declarações*, atributos do usuário autenticado. Uma declaração pode representar um nome de usuário, um identificador de uma função à qual um usuário pertence, a idade de um usuário e assim por diante. Um token é geralmente assinado digitalmente, o que significa que pode sempre ser originado de volta ao seu emissor e seu conteúdo não pode ser violado. Um usuário obtém acesso a um aplicativo RP apresentando um token válido emitido por uma autoridade na qual o aplicativo RP confia.

**IP (Provedor de Identidade)** - um IP é uma autoridade que autentica as identidades dos usuários e emite tokens de segurança. O trabalho real de emissão de tokens é implementado por meio de um serviço especial chamado STS (Serviço de Token de Segurança). Exemplos típicos de IPs incluem o Windows Live ID, o Facebook, os repositórios de usuários de negócios (como o Active Directory) e assim por diante.
Quando o ACS está configurado para confiar em um IP, o sistema irá aceitar e validar os tokens emitidos por esse IP. O ACS pode confiar em vários IPs ao mesmo tempo, o que significa que quando o seu aplicativo confia no ACS, você pode oferecer instantaneamente seu aplicativo a todos os usuários autenticados de todos os IPs nos quais o ACS confia em seu nome.

**FP (Provedor de Federação)** - os IPs têm conhecimento direto dos usuários e os autenticam usando suas credenciais e emitem declarações sobre o que conhecem sobre eles. Um FP (Provedor de Federação) é um tipo diferente de autoridade: em vez de autenticar usuários diretamente, ele atua como um intermediário e agencia a autenticação entre um RP e um ou mais IPs. IPs e FPs emitem tokens de segurança, por isso ambos usam os STS (Serviços de Segurança de Token) O ACS é um FP.

**Mecanismo de Regras do ACS** - a lógica usada para transformar o tokens recebidos de IPs confiáveis em tokens com o objetivo de serem consumidos pelo RP é codificada no formulário de regras de transformação de declarações simples. O ACS apresenta um mecanismo de regra que se encarrega de aplicar qualquer lógica de transformação que você especifique para seu RP.

**Namespace do ACS** - um namespace é uma partição de nível superior do ACS que você usa para organizar suas configurações. Um namespace espera uma lista de IPs que você confia, os aplicativos de RPs a serem atendidos, as regras que você espera que o mecanismo de regra aplique ao processar tokens recebidos e assim por diante. Um namespace expõe vários pontos de extremidade que serão usados pelo aplicativo e pelo desenvolvedor para fazer o ACS executar sua função.

A figura a seguir mostra como a autenticação do ACS funciona com um aplicativo Web:

![Diagrama de fluxo do ACS][acs_flow]

1.  O cliente (neste caso um navegador) solicita uma página do RP.
2.  Como a solicitação ainda não foi autenticada, o RP redireciona o usuário para a autoridade que ele confia, que é o ACS. O ACS apresenta ao usuário a opção de IPs que foram especificados para esse RP. O usuário seleciona o IP apropriado.
3.  O cliente navega até a página de autenticação do IP e solicita ao usuário para fazer logon.
4.  Depois que o cliente é autenticado (por exemplo, as credenciais de identidade foram inseridas), o IP emite um token de segurança.
5.  Depois de emitir um token de segurança, o IP redireciona o cliente para o ACS, e o cliente envia o token de segurança emitido pelo IP ao ACS.
6.  O ACS valida o token de segurança emitido pelo IP, insere a declaração de identidade nesse token no mecanismo de regras do ACS, calcula as declarações de identidade de saída e emite um novo token de segurança que contém essas declarações de saída.
7.  O ACS redireciona o cliente para o RP. O cliente envia o novo token de segurança emitido pelo ACS ao RP. O RP valida a assinatura no token de segurança emitido pelo ACS, valida as declarações nesse token e retorna a página que foi solicitada originalmente.

## <a name="pre"></a>Pré-requisitos

Para concluir as tarefas deste guia, você precisará do seguinte:

- Um Java Developer Kit (JDK) v 1.6 ou posterior.
- Um IDE do Eclipse para desenvolvedores do Java EE, Indigo ou posterior. Isso pode ser baixado em <http://www.eclipse.org/downloads/>. 
- Uma distribuição de um servidor web baseado em Java ou servidor de aplicativo, como o Apache Tomcat, o GlassFish, o Servidor de Aplicativo JBoss ou o Jetty.
- Uma assinatura do Azure, que pode ser adquirida em <http://www.microsoft.com/windowsazure/offers/>.
- O plug-in do Azure para Eclipse com Java (da Microsoft Open Technologies) - CTP de agosto de 2012. Para obter mais informações, consulte [Instalando o plug-in do Azure para Eclipse com Java (da Microsoft Open Technologies (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/windowsazure/hh690946.aspx).
- Um certificado X.509 para uso com o aplicativo. Você precisará desse certificado no certificado público (.cer) e no formato Troca de Informações Pessoais (.PFX). (As opções para criação desse certificado serão descritas posteriormente neste tutorial).
- Familiaridade com emulador de computação do Azure e com as técnicas de implantação discutidas em [Criando um aplicativo Hello World para o Azure no Eclipse (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/windowsazure/hh690944.aspx).

## <a name="create-namespace"></a>Criar um Namespace do ACS

Para começar a usar o ACS (Access Control Service) no Azure, você deve criar um namespace do ACS. O namespace fornece um escopo exclusivo para resolver os recursos do ACS a partir do seu aplicativo.

1. Faça logon no [Portal de Gerenciamento do Azure][].
2. Clique em **Active Directory**. 
3. Para criar um novo namespace do Controle de Acesso, clique em **Novo**, em **Serviços do Aplicativo**, em **Controle de Acesso** e, em seguida, clique em **Criação Rápida**. 
4. Insira um nome para o namespace. O Azure verifica se o nome é exclusivo.
5. Selecione a região na qual o namespace é usado. Para obter o melhor desempenho, use a região na qual você está implantando o seu aplicativo.
6. Se você tiver mais de uma assinatura, selecione a assinatura que você deseja usar para o namespace do ACS.
7. Clique em **Criar**.

O Azure cria e ativa o namespace. Aguarde até que o status do novo namespace seja **Ativo** para continuar. 

## <a name="add-IP"></a>Adicionar provedores de identidade

Nesta tarefa, você adicionará os IPs a serem usados com seu aplicativo RP para autenticação. Para fins de demonstração, esta tarefa mostra como adicionar o Windows Live como um IP, mas você poderá usar qualquer um dos IPs listados no Portal de Gerenciamento do ACS.


1.  No [Portal de Gerenciamento do Azure][], clique em **Active Directory**, selecione um namespace de Controle de Acesso e, em seguida, clique em **Gerenciar**. O Portal de Gerenciamento do ACS é aberto.
2.  No painel de navegação esquerdo do Portal de Gerenciamento do ACS, clique em **Provedores de Identidade**.
3.  O Windows Live ID é habilitado por padrão e não pode ser excluído. Para fins deste tutorial, apenas o Windows Live ID é usado. Nessa tela, no entanto, é onde você poderia adicionar outros IPs, clicando no botão **Adicionar**.

O Windows Live ID agora está habilitado como um IP para o seu namespace do ACS. Em seguida, especifique seu aplicativo web Java (a ser criado mais tarde) como um RP.

## <a name="add-RP"></a>Adicionar um aplicativo de terceira parte confiável

Nesta tarefa, você configura o ACS para reconhecer seu aplicativo web Java como um aplicativo RP válido.

1.  No Portal de Gerenciamento do ACS, clique em **Aplicativos de terceiras partes confiáveis**.
2.  Na página **Aplicativos de terceiras partes confiáveis**, clique em **Adicionar**.
3.  Na página **Adicionar aplicativos de terceiras partes confiáveis**, faça o seguinte:
    1.  Em **Nome**, digite o nome do RP. Para fins desse tutorial, digite **Aplicativo Web do Azure**.
    2.  Em **Modo**, selecione **Inserir as configurações manualmente**.
    3.  Em **Realm**, digite o URI ao qual o token de segurança emitido pelo ACS se aplica. Para esta tarefa, digite **http://localhost:8080/**.
        ![Realm de terceira parte confiável para uso no emulador de computação][relying_party_realm_emulator]
    4.  Em **URL de Retorno**, digite a URL para a qual o ACS retorna o token de segurança. Para esta tarefa, digite **http://localhost:8080/MyACSHelloWorld/index.jsp**
        ![A terceira parte confiável retorna a URL para uso no emulador de computação][relying_party_return_url_emulator]
    5.  Aceite os valores padrão no restante dos campos.

4.  Clique em **Salvar**.

Você agora configurou com êxito seu aplicativo web Java, quando ele é executado no emulador de computação do Azure (em http://localhost:8080/), para ser um RP em seu namespace do ACS. Em seguida, crie as regras que o ACS usa para processar declarações para o RP.

## <a name="create-rules"></a>Criar regras

Nesta tarefa, você define as regras que orientam como as declarações são passadas dos IPs para seu RP. Para o objetivo deste guia, iremos configurar o ACS simplesmente para copiar os tipos e valores de declarações de entrada diretamente no token de saída, sem filtrá-los ou modificá-los.

1.  Na página principal do Portal de Gerenciamento do ACS, clique em **Grupos de Regras**.
2.  Na página **Grupos de Regras**, clique em **Grupo de Regras Padrão para o Aplicativo Web do Azure**.
3.  Na página **Editar Grupo de Regras**, clique em **Gerar**.
4.  Na página **Gerar Regras: Grupo de Regra Padrão para o Aplicativo Web do Azure**, verifique se Windows Live ID está selecionado e, em seguida, clique em **Gerar**.
5.  Na página **Editar Grupo de Regras**, clique em **Salvar**.

## <a name="upload-certificate"></a>Carregar um certificado para seu namespace do ACS

Nesta tarefa, você carrega um certificado PFX que será usado para assinar solicitações de token criadas pelo seu namespace do ACS.

1.  Na página principal do Portal de Gerenciamento do ACS, clique em **Certificados e Chaves**.
2.  Na página **Certificados e Chaves**, clique em **Adicionar** acima de **Autenticação de Token**.
3.  Na página **Adicionar Certificado ou Chave de Autenticação de Token**:
    1. Na seção **Usado para**, clique em **Aplicativo de Terceira Parte Confiável** e selecione **Aplicativo Web do Azure** (que você configurou previamente com o nome do seu aplicativo de terceira parte confiável).
    2. Na seção **Tipo**, selecione **Certificado X.509**.
    3. Na seção **Certificado**, clique no botão procurar e navegue até o arquivo de certificado X.509 que você deseja usar. Esse será um arquivo .PFX. Selecione o arquivo, clique em **Abrir** e, em seguida, digite a senha do certificado na caixa de texto **Senha**. Observe que, para fins de teste, você deve usar um certificado autoassinado. Para criar um certificado autoassinado, use o botão **Novo** na caixa de diálogo **Biblioteca de Filtros do ACS** (descrita posteriormente) ou use o utilitário **encutil.exe** no [site do projeto][] do Kit de Início do Azure para Java (da Microsoft Open Technologies).
    4. Verifique se **Tornar Primário** está marcado. Sua página **Adicionar Certificado ou Chave de Autenticação de Token** deve ser semelhante à seguinte.
        ![Adicionar certificado de autenticação de token][add_token_signing_cert]
    5. Clique em **Salvar** para salvar suas configurações e fechar a página **Adicionar Certificado ou Chave de Autenticação de Token**.

Em seguida, revise as informações na página Integração de Aplicativos e copie o URI do qual você precisará para configurar seu aplicativo Web Java para usar o ACS.

## <a name="review-app-int"></a>Revisar a página Integração de Aplicativos

Você pode encontrar todas as informações e o código necessário para configurar seu aplicativo Web Java (o aplicativo RP) para trabalhar com o ACS na página Integração de Aplicativos do Portal de Gerenciamento do ACS. Você precisará dessas informações ao configurar seu aplicativo web Java para autenticação federada.

1.  No Portal de Gerenciamento do ACS, clique em **Integração do Aplicativo**.
2.  Na página **Integração de Aplicativos**, clique em **Páginas de Logon**.
3.  Na página **Integração da Página de Logon**, clique em **Aplicativo Web do Azure**.

Na página **Integração da Página de Logon: Aplicativo Web do Azure**, a URL listada na **Opção 1: Link para uma página de logon hospedada pelo ACS** será usada em seu aplicativo web Java. Você precisará desse valor ao adicionar a biblioteca de Filtros de Serviços do Controle de Acesso para seu aplicativo Java.

## <a name="create-java-app"></a>Criar um aplicativo web Java
1. No Eclipse, no menu, clique em **Arquivo**, clique em **Novo** e, em seguida, clique em **Projeto Web Dinâmico**. (Se você não vir o **Projeto Web Dinâmico** listado como um projeto disponível depois de clicar em **Arquivo**, **Novo**, faça o seguinte: clique em **Arquivo**, clique em **Novo**, clique em **Projeto**, expanda **Web**, clique em **Projeto Web Dinâmico** e clique em **Avançar**.) Para fins deste tutorial, nomeie o projeto **MyACSHelloWorld**. (Certifique-se de usar esse nome, as etapas subsequentes deste tutorial esperam que seu arquivo WAR seja nomeado MyACSHelloWorld). Sua tela será semelhante à seguinte:

    ![Criar um Projeto Hello World para exemplo do ACS][create_acs_hello_world]

    Clique em **Concluir**.
2. Na exibição do Explorador de Projeto do Eclipse, expanda **MyACSHelloWorld**. Clique com o botão direito do mouse em **WebContent**, clique em **Novo** e, em seguida, clique em **Arquivo JSP**.
3. Na caixa de diálogo **Novo Arquivo JSP**, nomeie o arquivo **index.jsp**. Mantenha a pasta pai como MyACSHelloWorld/WebContent, conforme mostrado a seguir:

    ![Adicionar um arquivo JSP para exemplo do ACS][add_jsp_file_acs]

    Clique em **Avançar**.

4. Na caixa de diálogo **Selecionar Modelo JSP**, selecione **Novo Arquivo JSP (html)** e clique em **Concluir**.
5. Quando o arquivo index.jsp for aberto no Eclipse, adicione o texto para exibir **Hello ACS World!** com o elemento `<body>` existente. Seu conteúdo `<body>` atualizado deve ser semelhantes ao seguinte:

        <body>
          <b><% out.println("Hello ACS World!"); %></b>
        </body>
    
    Salve o index.jsp.
  
## <a name="add_acs_filter_library"></a>Adicionar a Biblioteca de Filtros do ACS ao seu aplicativo

1. No Explorador de Projeto do Eclipse, clique com o botão direito do mouse em **MyACSHelloWorld**, clique em **Caminho de Compilação** e, em seguida, clique em **Configurar Caminho de Compilação**.
2. Na caixa de diálogo **Caminho de Compilação do Java**, clique na guia **Bibliotecas**.
3. Clique em **Adicionar Biblioteca**.
4. Clique em **Filtro do Access Control Services do Azure (da MS Open Tech)** e, em seguida, clique em **Avançar**. A caixa de diálogo **Filtro do Access Control Services do Azure** é exibida.  (O campo **Localização** pode ter um caminho diferente, dependendo de onde você instalou o Eclipse, e o número da versão pode ser diferente, dependendo das atualizações do software.)

    ![Adicionar a biblioteca de filtros do ACS][add_acs_filter_lib]

5. Usando um navegador aberto na página **Integração da Página de Logon** do Portal de Gerenciamento, copie a URL listada no campo **Opção 1: Link para uma página de logon hospedada pelo ACS** e cole-a no campo **Ponto de Extremidade de Autenticação do ACS** na caixa de diálogo Eclipse.
6. Usando um navegador aberto na página **Editar Aplicativo de Terceira Parte Confiável** do Portal de Gerenciamento, copie a URL listada no campo **Realm** e cole-a no campo **Realm de Terceira Parte Confiável** da caixa de diálogo Eclipse.
7. Na seção **Segurança** da caixa de diálogo Eclipse, se desejar usar um certificado existente, clique em **Procurar**, navegue até o certificado que você deseja usar, selecione-o e clique em **Abrir**. Ou, se você desejar criar um novo certificado, clique em **Novo** para exibir a caixa de diálogo **Novo Certificado**, em seguida, especifique a senha, o nome do arquivo .cer e o nome do arquivo .pfx para o novo certificado.
8. Marque **Inserir o certificado no arquivo WAR**. A incorporação do certificado dessa maneira o inclui em sua implantação sem que você precise adicioná-lo manualmente como um componente. (Caso contrário, se você precisar armazenar seu certificado externamente ao arquivo WAR, você poderá adicionar o certificado como um componente da função e desmarcar **Inserir o certificado no arquivo WAR**.)
9. [Opcional] Mantenha a opção **Requer conexões HTTPS** marcada. Se você definir essa opção, precisará acessar seu aplicativo usando o protocolo HTTPS. Se você não desejar requerer conexões HTTPS, desmarque essa opção.
10. Para uma implantação no emulador de computação, suas configurações do **Filtro do ACS do Azure** serão semelhantes ao seguinte.

    ![Configurações do Filtro do ACS do Azure para uma implantação no emulador de computação][add_acs_filter_lib_emulator]

11. Clique em **Concluir**.
12. Clique em **Sim** quando for apresentada uma caixa de diálogo informando que será criado um arquivo web.xml.
13. Clique em **OK** para fechar a caixa de diálogo **Caminho de Compilação Java**.

## <a name="deploy_compute_emulator"></a>Implantar no emulador de computação

1. No Explorador de Projeto do Eclipse, clique com o botão direito do mouse em **MyACSHelloWorld**, clique em **Azure** e, em seguida, clique em **Pacote para Azure**.
2. Para o **Nome do Projeto**, digite **MyAzureACSProject** e clique em **Avançar**.
3. Selecione um JDK e o servidor de aplicativo. (Essas etapas são abordadas detalhadamente no tutorial [Criando um aplicativo Hello World para o Azure no Eclipse (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/windowsazure/hh690944.aspx)).
4. Clique em **Concluir**.
5. Clique no botão **Executar no Emulador do Azure**.
6. Depois de iniciar seu aplicativo web Java no emulador de computação, feche todas as instâncias do seu navegador (para que nenhuma sessão atual do navegador interfira em seu teste de logon do ACS).
7. Execute seu aplicativo abrindo <http://localhost:8080/MyACSHelloWorld/> em seu navegador (ou <https://localhost:8080/MyACSHelloWorld/> se você marcou **Requer Conexões HTTPS**). Um logon do Windows Live ID deve ser solicitado, e você deverá ser legado para a URL de retorno especificada para seu aplicativo de terceira parte confiável.
99.  Ao concluir a exibição do aplicativo, clique no botão **Redefinir Emulador do Azure**.

## <a name="deploy_azure"></a>Implantar no Azure

Para implantar no Azure, você precisará alterar o realm da terceira parte confiável e retornar a URL para seu namespace do ACS.

1. No Portal de Gerenciamento do Azure, na página **Editar Aplicativo da Terceira Parte Confiável**, modifique **Realm** para ser a URL de seu site implantado. Substitua **exemplo** pelo nome DNS especificado para sua implantação.

    ![Realm de terceira parte confiável para uso na produção][relying_party_realm_production]

2. Modifique a **URL de retorno** para ser URL do seu aplicativo. Substitua **exemplo** pelo nome DNS especificado para sua implantação.

    ![URL de retorno da terceira parte confiável para uso na produção][relying_party_return_url_production]

3. Clique em **Salvar** para salvar o realm da terceira parte confiável atualizado e retornar as alterações da URL.
4. Mantenha a página **Integração da Página de Logon** aberta no navegador, você precisará copiá-la em breve.
5. No Explorador de Projeto do Eclipse, clique com o botão direito do mouse em **MyACSHelloWorld**, clique em **Caminho de Compilação** e, em seguida, clique em **Configurar Caminho de Compilação**.
6. Clique na guia **Bibliotecas**, clique em **Filtro do Access Control Services do Azure** e, em seguida, clique em **Editar**.
7. Usando um navegador aberto na página **Integração da Página de Logon** do Portal de Gerenciamento, copie a URL listada no campo **Opção 1: Link para uma página de logon hospedada pelo ACS** e cole-a no campo **Ponto de Extremidade de Autenticação do ACS** na caixa de diálogo Eclipse.
8. Usando um navegador aberto na página **Editar Aplicativo de Terceira Parte Confiável** do Portal de Gerenciamento, copie a URL listada no campo **Realm** e cole-a no campo **Realm de Terceira Parte Confiável** da caixa de diálogo Eclipse.
9. Na seção **Segurança** da caixa de diálogo Eclipse, se desejar usar um certificado existente, clique em **Procurar**, navegue até o certificado que você deseja usar, selecione-o e clique em **Abrir**. Ou, se você desejar criar um novo certificado, clique em **Novo** para exibir a caixa de diálogo **Novo Certificado**, em seguida, especifique a senha, o nome do arquivo .cer e o nome do arquivo .pfx para o novo certificado.
10. Mantenha a opção **Incorporar o certificado no arquivo WAR** marcada, supondo que você deseja incorporar o certificado no arquivo WAR.
11. [Opcional] Mantenha a opção **Requer conexões HTTPS** marcada. Se você definir essa opção, precisará acessar seu aplicativo usando o protocolo HTTPS. Se você não desejar requerer conexões HTTPS, desmarque essa opção.
12. Para uma implantação no Azure, suas configurações do Filtro do ACS do Azure serão semelhantes ao seguinte.

    ![Configurações do Filtro do ACS do Azure para uma implantação de produção][add_acs_filter_lib_production]

13. Clique em **Concluir** para fechar a caixa de diálogo **Editar Biblioteca**.
14. Clique em **OK** para fechar a caixa de diálogo **Propriedades de MyACSHelloWorld**.
15. No Eclipse, clique no botão **Publicar na Nuvem do Azure**. Responda às solicitações, de maneira semelhante ao que foi feito na seção **Para implantar seu aplicativo no Azure** do tópico [Criando um aplicativo Hello World para o Azure no Eclipse](http://msdn.microsoft.com/pt-br/library/windowsazure/hh690944.aspx). 

Após a implantação do seu aplicativo web, feche todas as sessões abertas do navegador e execute seu aplicativo web. Você deverá ser solicitado a entrar com as credenciais do Windows Live ID que, em seguida, serão enviadas para a URL de retorno de seu aplicativo de terceira parte confiável.

Quando você terminar de usar seu aplicativo Hello World do ACS, lembre-se de excluir a implantação (você pode aprender como excluir uma implantação no tópico [Criando um aplicativo Hello World para o Azure no Eclipse](http://msdn.microsoft.com/pt-br/library/windowsazure/hh690944.aspx)).


## <a name="next_steps"></a>Próximas etapas

Para obter um exame da marcação linguagem SAML (Security Assertion) retornada pelo ACS para seu aplicativo, consulte [Como exibir SAML retornado pelo Access Control Service do Azure][]. Para explorar melhor a funcionalidade do ACS e experimentar mais cenários sofisticados, consulte [Access Control Service 2.0][].

Além disso, esse exemplo usou a opção **Incorporar o certificado no arquivo WAR**. Essa opção simplifica a implantação do certificado. Se, em vez disso, você desejar manter seu certificado de autenticação separado do seu arquivo WAR, você poderá usar a seguinte técnica:

1. Na seção **Segurança** da caixa de diálogo **Filtro dos Access Control Services do Azure**, digite **${env.JAVA_HOME}/mycert.cer** e desmarque a opção **Incorporar o certificado no arquivo WAR**. (Ajuste mycert.cer se o nome do arquivo do certificado for diferente). Clique em **Concluir** para fechar a caixa de diálogo.
2. Copie o certificado como um componente em sua implantação: no Explorador de Projeto do Eclipse, expanda **MyAzureACSProject**, clique com o botão direito do mouse em **WorkerRole1**, clique em **Propriedades**, expanda **Função do Azure** e clique em **Componentes**.
3. Clique em **Adicionar**.
4. Na caixa de diálogo **Adicionar Componente**:
    1. Na seção **Importar**:
        1. Use o botão **Arquivo** para navegar para o certificado que você deseja usar.
        2. Para **Método**, selecione **cópia**.
    2. Para **Como Nome**, clique na caixa de texto e aceite o nome padrão.
    3. Na seção **Implantar**:
        1. Para **Método**, selecione **cópia**.
        2. Para **Para o diretório**, digite **%JAVA_HOME%**.
    4. Sua caixa de diálogo **Adicionar Componente** deverá ser semelhante à seguinte.

        ![Adicionar componente de certificado][add_cert_component]

    5. Clique em **OK**.

Neste ponto, seu certificado seria incluído em sua implantação. Observe que, independentemente de você incorporar o certificado no arquivo WAR ou adicioná-lo como um componente à sua implantação, você precisa carregar o certificado em seu namespace, conforme descrito na seção [Carregar um certificado para seu namespace do ACS][].

[O que é ACS?]: #what-is
[Conceitos]: #concepts
[Pré-requisitos]: #pre
[Criar um aplicativo web Java]: #create-java-app
[Criar um Namespace do ACS]: #create-namespace
[Adicionar provedores de identidade]: #add-IP
[Adicionar um aplicativo de terceira parte confiável]: #add-RP
[Criar regras]: #create-rules
[Carregar um certificado para seu namespace do ACS]: #upload-certificate
[Revisar a página Integração de Aplicativos]: #review-app-int
[Configurar a relação de confiança entre o ACS e seu aplicativo Web ASP.NET]: #config-trust
[Adicionar a Biblioteca de Filtros do ACS ao seu aplicativo]: #add_acs_filter_library
[Implantar no emulador de computação]: #deploy_compute_emulator
[Implantar no Azure]: #deploy_azure
[Próximas etapas]: #next_steps
[site do projeto]: http://wastarterkit4java.codeplex.com/releases/view/61026
[Como exibir o SAML retornado pelo Access Control Service do Azure]: /pt-br/develop/java/how-to-guides/view-saml-returned-by-acs/
[Access Control Service 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[Windows Identity Foundation]: http://www.microsoft.com/download/en/details.aspx?id=17331
[SDK do Windows Identity Foundation]: http://www.microsoft.com/download/en/details.aspx?id=4451
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com
[acs_flow]: ./media/active-directory-java-authenticate-users-access-control-eclipse/ACSFlow.png

<!-- Eclipse-specific -->
[add_acs_filter_lib]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibrary.png
[add_acs_filter_lib_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryEmulator.png
[add_acs_filter_lib_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryProduction.png

[relying_party_realm_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmEmulator.png
[relying_party_return_url_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLEmulator.png
[relying_party_realm_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmProduction.png
[relying_party_return_url_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLProduction.png
[add_cert_component]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddCertificateComponent.png
[add_jsp_file_acs]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddJSPFileACS.png
[create_acs_hello_world]: ./media/active-directory-java-authenticate-users-access-control-eclipse/CreateACSHelloWorld.png
[add_token_signing_cert]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddTokenSigningCertificate.png

