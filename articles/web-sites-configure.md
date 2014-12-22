<properties urlDisplayName="How to configure" pageTitle="Como configurar sites - gerenciamento de serviços do Azure" metaKeywords="sites do Azure, configurando sites do Azure, banco de dados SQL do Azure, MySQL do Azure" description="Learn how to configure websites in Azure, including how to configure a website to use a SQL Database or MySQL database." metaCanonical="" services="web-sites" documentationCenter="" title="How to Configure Websites" authors="mwasson" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/18/2014" ms.author="mwasson" />


# Como configurar sites #
No Portal de Gerenciamento do Azure, você pode alterar as opções de configuração para sites e vincular para outros recursos do Azure, como um banco de dados.

## Sumário ##
- [Como: alterar as opções de configuração de um site](#howtochangeconfig)
- [Como: configurar um site para usar um banco de dados SQL](#howtoconfigSQL)
- [Como: configurar um site para usar um banco de dados MySQL](#howtoconfigMySQL)
- [Como: Configurar um nome de domínio personalizado](#howtodomain)
- [Como: configurar um site para usar SSL](#howtoconfigSSL)
- [Próximas etapas](#next)


##<a name="howtochangeconfig"></a>Como: alterar as opções de configuração de um site

<!-- HOW TO: CHANGE CONFIGURATION OPTIONS FOR A WEBSITE -->

Para configurar as opções de configuração de um site:

1. No [Portal de Gerenciamento](https://manage.windowsazure.com/), abra as páginas de gerenciamento do site.
1. Clique na guia <strong>Configurar</strong>.

A guia **Configurar** tem as seguintes seções:

### Geral

**Versão do Framework**. Configurar essas opções se seu aplicativo usa qualquer um desses frameworks: 

- **.NET Framework**: configurar a versão do .NET framework. 
- **PHP**: configurar a versão PHP, ou **DESATIVADO** para desabilitar o PHP. 
- **Java**: selecionar a versão exibida para habilitar o Java, ou <strong>DESATIVADO</strong> para desabilitar o Java. 
- Se você habilitar o Java, utilize a opção <strong>Contêiner da Web</strong> para escolher entre as versões Tomcat e Jetty.
- **Python**: selecionar a versão Python ou **DESATIVADO** para desabilitar o Python.

Por razões técnicas, a habilitação do Java para seu site desabilita as opções .NET, PHP e Python.

<strong>Modo de Pipeline Gerenciado</strong>. Configurar o IIS [modo de pipeline](http://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application). Deixar este conjunto como Integrado (o padrão), a menos que você tenha um site de legado que requeira uma versão anterior do IIS.

<strong>Plataforma</strong>. Seleciona se seu aplicativo é executado no ambiente de 32-bit ou 64 bit. O ambiente de 64-bit requere o modo Básico ou Padrão. Modos Livre e Compartilhado são sempre executados em um ambiente de 32 bits.

<strong>Web Sockets</strong>. Configurar **ATIVADO** para habilitar o protocolo WebSocket; por exemplo, se seu site utiliza [ASP.NET SignalR](http://www.asp.net/signalr) ou [socket.io](http://azure.microsoft.com/pt-br/documentation/articles/web-sites-nodejs-chat-app-socketio/).

<strong>Sempre Ativado</strong>. Por padrão, os sites serão descarregados se estiverem ociosos durante um determinado período de tempo. Isso permite que o sistema conservar os recursos. No modo Básico ou Padrão, você pode habilitar <strong>Sempre Ativado</strong> para manter o site carregado o tempo todo. Se seu site executar continuamente Trabalhos Web, você deve habilitar **Sempre Ativado** ou é possível que os Trabalhos Web não sejam executados de forma segura

<strong>Editar no Visual Studio Online</strong>. Habilita a edição do código ativo com o Visual Studio Online. Se for habilitado, a guia do Painel exibirá um link chamado <strong>Editar no Visual Studio Online</strong>, na seção <strong>Visão Rápida</strong>. Clique neste link para editar o seu site diretamente online. Se você precisar autenticar, poderá utilizar suas credenciais de implantação básica.

Observação: se você habilitar a implantação do controle do código-fonte, será possível que uma implantação substitua as alterações feitas no editor Visual Studio Online. 


### Certificados

No modo Básico ou Padrão, é possível carregar certificados SSL para domínios personalizados. Para obter mais informações, consulte [Habilitar HTTPS para um site do Azure](href="http://www.windowsazure.com/pt-br/documentation/articles/web-sites-configure-ssl-certificate/). 

Seus certificados carregados estão listados aqui. Após carregar um certificado, você poderá atribuí-lo a qualquer site na sua assinatura e região. Os certificados Wildcard podem ser usados para qualquer site dentro do domínio para o qual for válido. Um certificado pode ser excluído apenas se não houver associações para esse certificado.

### Nomes de domínio

Exibir ou adicionar nomes de domínio para o site. Para obter mais informações, consulte [Configurando um nome de domínio personalizado para um site do Azure](http://www.windowsazure.com/pt-br/documentation/articles/web-sites-custom-domain-name/).

### Associações SSL

Se você tiver carregado certificados SSL, é possível associá-los para nomes personalizados de domínio. Para obter mais informações, consulte [Habilitar HTTPS para um site do Azure](href="http://www.windowsazure.com/pt-br/documentation/articles/web-sites-configure-ssl-certificate/)

### Implantações

Esta seção é exibida somente se você tiver habilitado a implantação do controle do código-fonte. Utilize essas configurações para definir implantações.

- <strong>URL do Git</strong>. Se você tiver criado um repositório do Git para seu site do Azure, esta é a URL para onde enviar seu conteúdo por push.
- <strong>URL do gatilho da implantação</strong>. Esta URL pode ser definida em um GitHub, CodePlex, Bitbucket ou outro repositório para disparar a implantação quando uma confirmação é enviada para o repositório.
- <strong>Ramificação a ser implantada</strong>. Especifica a ramificação que será implantada quando você enviar o conteúdo por push.

Para configurar a implantação do controle do código-fonte, exiba a guia **Painel** e clique em **Configurar a implantação do controle de origem**. 

### Diagnóstico de aplicativo

Opções para gravar os logs de diagnóstico de um aplicativo Web que forneça suporte ao registro em log: 

- <strong>Sistema de arquivos</strong>. Grava os logs ao sistema de arquivos do site. O registro em log de sistema de arquivos dura por um período de 12 horas. Você pode acessar os logs do compartilhamento de FTP para o site. (Consulte [Credenciais FTP](http://azure.microsoft.com/pt-br/documentation/articles/web-sites-manage#ftp-credentials)).
- <strong>Armazenamento de tabela</strong>. Grava logs no armazenamento da tabela do Azure. Não há limite de tempo, e o registro em log fica habilitado até que você o desabilite. 
- <strong>Armazenamento de Blob</strong>. Grava logs no armazenamento de blob do Azure. Não há limite de tempo, e o registro em log fica habilitado até que você o desabilite.

<strong>Nível de log</strong>. Quando o registro estiver habilitado, esta opção especifica a quantidade de informações que serão gravadas (erro, aviso, informações ou detalhado).

**Gerenciar armazenamento da tabela**. Quando o armazenamento de tabela for habilitado, clique neste botão para configurar a conta de armazenamento e o nome da tabela.

**Gerenciar armazenamento do blob.** Quando o armazenamento de blob for habilitado, clique neste botão para configurar a conta de armazenamento e o nome do blob.

### Diagnóstico de site

Opções para coleta de informações de diagnóstico para seu site.

<strong>Logs de servidor da Web</strong>. Habilita o log de servidor web. Os logs são salvos no formato W3C estendido de arquivo de log. É possível salvar os logs no sistema de arquivos do site ou no armazenamento do Azure.
 
- Se você selecionar <strong>Sistema de arquivos</strong>, os logs serão salvos no site FTP listado em "Logs de diagnóstico de FTP" na página Painel. (Consulte [Credenciais FTP](http://azure.microsoft.com/pt-br/documentation/articles/web-sites-manage#ftp-credentials)) 
- Se você escolher **Sistema de arquivos**, utilize a caixa <strong>Cota</strong> para definir a quantidade máxima de espaço em disco para os arquivos log. O mínimo é 25 MB e o máximo é 100 MB. O padrão é 35MB. Quando a cota for atingida, os arquivos mais antigos serão substituídos sucessivamente pelos mais recentes. Se for necessário manter mais histórico que 100 MB, utilize o Armazenamento do Azure, que tem uma capacidade de armazenamento muito maior.
- Opcionalmente, clique em <strong>Definir retenção</strong> para excluir automaticamente os arquivos após um período de tempo. Por padrão, os logs nunca são excluídos.   

<strong>Mensagens Detalhados de Erro</strong>. Se estiver habilitado, as mensagens detalhadas de erro serão salvas como arquivos .htm. Para visualizar os arquivos, vá para o site FTP listado em "Logs de diagnóstico FTP" na página do Painel. Os arquivos são salvos em /LogFiles/DetailedErrors no site FTP. (Consulte [Credenciais FTP](http://azure.microsoft.com/pt-br/documentation/articles/web-sites-manage#ftp-credentials).)

<strong>Rastreamento de Solicitação Falha</strong>. Se estiver habilitada, as solicitações com falha são armazenadas em log em arquivos XML. Para visualizar os arquivos, vá para o site FTP listado em "Logs de diagnóstico FTP" na página do Painel. (Consulte [Credenciais FTP](http://azure.microsoft.com/pt-br/documentation/articles/web-sites-manage#ftp-credentials)) Os arquivos são salvos em /LogFiles/W3SVC*xxx*, onde xxx é um identificador único. Esta pasta contém um arquivo XSL e um ou mais arquivos XML. Certifique-se de baixar o arquivo XSL, porque ele fornece funcionalidade para formatar e filtrar os conteúdos dos arquivos XMl.

<strong>Depuração remota</strong> Habilita a depuração remota. Quando estiver habilitado, você pode usar o depurador remoto no Visual Studio para conectar-se diretamente com seu site do Azure. A depuração remota permanecerá habilitada por 48 horas.

**Observação**: A depuração remota não funciona com um nome de site ou um nome de usuário que seja maior que 20 caracteres. 

### Monitoramento

No modo Básico ou Padrão, você pode testar a disponibilidade dos pontos de extremidade HTTP ou HTTPs, de até três locais geograficamente distribuídos. Um teste de monitoramento falhará se o código de resposta HTTP for um erro (4xx ou 5xx) ou se a resposta demorar mais de 30 segundos. Um ponto de extremidade será considerado disponível caso os testes de monitoramento tenham êxito a partir de todos os locais especificados. 

 Para obter mais informações, consulte [Como: monitorar o status de ponto de extremidade da Web](http://go.microsoft.com/fwLink/?LinkID=279906&clcid=0x409).


### Análise para o desenvolvedor

Selecione <strong>Complemento</strong> para selecionar um complemento de análise de uma lista ou acesse a Azure Store para escolher um. Selecione <strong>Personalizado</strong> para selecionar um provedor de análise, como o New Relic de uma lista. Se usar um provedor personalizado, você deverá inserir a chave de licença na caixa<strong> Chave do Provedor</strong>. 

Para obter mais informações sobre o uso do New Relic com Sites do Azure, consulte <a href="http://www.windowsazure.com/pt-br/documentation/articles/store-new-relic-web-sites-dotnet-application-performance-management/">Gerenciamento de desempenho do aplicativo New Relic nos Sites do Azure</a>.

### Configurações do aplicativo

Especifique pares de nome/valor que serão carregados pelo seu aplicativo Web na inicialização. 

- Para sites .NET, essas configurações serão injetadas em sua configuração AppSettings do .NET em tempo de execução, substituindo as configurações existentes. 

- Os aplicativos PHP, Python, Java e Nó podem acessar essas configurações como variáveis do ambiente em tempo de execução. Para cada configuração do aplicativo, são criadas duas variáveis; uma com o nome especificado pela entrada de configuração do aplicativo, e outra com um prefixo de APPSETTING_. Ambas contêm o mesmo valor.

### Cadeias de conexão

Cadeia de conexão para recursos vinculados. 

Para sites .NET, essas cadeias de conexão serão injetadas em suas configurações connectionStrings do .NET em tempo de execução substituindo as entradas existentes onde a chave é igual ao nome do banco de dados vinculado. 

Para aplicativos PHP, Python, Java e Nó essas configurações estarão disponíveis como variáveis de ambiente em tempo de execução, antecedidas com o tipo de conexão. Os prefixos das variáveis de ambiente são os seguintes:  

- SQL Server: SQLCONNSTR_
- MySQL: MYSQLCONNSTR_
- Banco de dados SQL: SQLAZURECONNSTR_
- Personalizado: CUSTOMCONNSTR_

Por exemplo, se uma cadeia de conexão MySql fosse nomeada connectionstring1, ela seria acessada pela variável de ambiente <code>MYSQLCONNSTR_connectionString1</code>.

<strong>Observação</strong>: As cadeias de conexão também são criadas quando você vincula um recurso de banco de dados a um site. As cadeias de conexão criadas desta maneira são lidas apenas quando são visualizadas na 
página de gerenciamento de configuração.

### Documentos padrão

Um documento padrão de um site é a página da Web que é exibida e não especifica uma página em particular no site. Se seu site contiver mais de um dos arquivos na lista, certifique-se de colocar seu documento padrão no topo da lista.

Os aplicativos Web podem utilizar módulos que rotearão na URL, em vez de atender ao conteúdo estático, nesse caso, não há um documento padrão como tal.   

### Mapeamentos de manipulador

Utilize essa área para adicionar processadores de script personalizados para manipular solicitações de extensões de arquivo específicas. 

- **Extensões**. A extensão do arquivo a ser manipulada, como *.php ou handler.fcgi. 
- **Caminho do Processador de script**. O caminho absoluto do processador de script. As solicitações para arquivos que correspondam a extensão do arquivo serão processadas pelo processador de script. Use o caminho<code>D:\home\site\wwwroot</code> para se referir ao diretório raiz do site.
- **Argumentos adicionais**. Argumentos da linha de comando opcionais para o processador de script 


### Aplicativos e diretórios virtuais 

Para configurar os aplicativos e diretórios virtuais associados com seu site, especificar cada diretório virtual e seu caminho físico correspondente relativo à raiz do site. Opcionalmente, é possível selecionar a caixa de seleção <strong>Aplicativo</strong> para marcar um diretório virtual como um aplicativo na configuração do site.

	

<!-- HOW TO: CONFIGURE A WEBSITE TO USE A SQL DATABASE -->
##<a name="howtoconfigSQL"></a>Como: configurar um site para utilizar um banco de dados SQL

Siga estas etapas para vincular um site a um Banco de Dados SQL:

1. No [Portal de gerenciamento](http://manage.windowsazure.com), selecione **Sites** para exibir a lista de sites criada por quem estiver conectado atualmente na conta.

2. Selecione um site da lista de sites para abrir as páginas de **Gerenciamento** do site.

3. Clique na guia **Recursos vinculados** e uma mensagem será exibida na página **Recursos vinculados** indicando **Você não tem recursos vinculados**.

4. Clique em **Vincular um recurso** para abrir o assistente **Vincular um recurso**.

5. Clique em **Criar um novo recurso** para exibir uma lista de tipos de recursos que podem ser vinculados ao seu site.

6. Clique em **Banco de dados SQL** para exibir o assistente **Vincular banco de dados**.

7. Conclua os campos obrigatórios nas páginas 3 e 4 do assistente **Vincular banco de dados** e, em seguida, clique na marca de seleção **Concluir** na página 4.

O Azure criará um Banco de Dados SQL com os parâmetros especificados e vinculará o banco de dados ao site.

<!-- HOW TO: CONFIGURE A WEBSITE TO USE A MYSQL DATABASE -->
##<a name="howtoconfigMySQL"></a>Como: configurar um site para utilizar um banco de dados MySQL##
Para configurar um site para utilizar um banco de dados MySQL, siga as mesmas etapas de uso do banco de dados SQL, mas no assistente **Vincular um recurso**, selecione **Banco de dados MySQL** em vez de **Banco de dados SQL**. 

Alternativamente, é possível criar o site com a opção **Criação personalizada**. Na lista suspensa **Banco de Dados**, selecione **Criar um novo banco de dados MySQL** ou **Usar um banco de dados MySQL existente**. 

##<a name="howtodomain"></a>Como: Configurar um nome de domínio personalizado

Para obter informações sobre a configuração do seu site para utilizar um nome de domínio personalizado, consulte [Configurando um nome de domínio personalizado para Sites do Azure ](http://www.windowsazure.com/pt-br/documentation/articles/web-sites-custom-domain-name/).

##<a name="howtoconfigSSL"></a>Como: configurar um site para utilizar SSL##

Para obter informações sobre a configuração de SSL para um domínio personalizado no Azure, consulte [Habilitar HTTPS para um site do Azure](http://www.windowsazure.com/pt-br/documentation/articles/web-sites-configure-ssl-certificate/). 

##<a name="next"></a>Próximas etapas

* [Como dimensionar sites](http://www.windowsazure.com/pt-br/documentation/articles/web-sites-scale/)

* [Como monitorar Sites](http://www.windowsazure.com/pt-br/documentation/articles/web-sites-monitor/)

