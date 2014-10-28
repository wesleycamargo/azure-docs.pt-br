<properties linkid="manage-services-how-to-configure-websites" urlDisplayName="How to configure" pageTitle="How to configure websites - Azure service management" metaKeywords="Azure websites, configuring Azure websites, Azure SQL database, Azure MySQL" description="Learn how to configure websites in Azure, including how to configure a website to use a SQL Database or MySQL database." metaCanonical="" services="web-sites" documentationCenter="" title="How to Configure Websites" authors="mwasson" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/18/2014" ms.author="mwasson"></tags>

# Como configurar sites

No Portal de Gerenciamento do Azure, você pode alterar as opções de configuração para sites e vincular para outros recursos do Azure, como um banco de dados.

## Sumário

-   [Como: alterar as opções de configuração de um site][Como: alterar as opções de configuração de um site]
-   [Como: configurar um site para usar um banco de dados SQL][Como: configurar um site para usar um banco de dados SQL]
-   [Como: configurar um site para usar um banco de dados MySQL][Como: configurar um site para usar um banco de dados MySQL]
-   [Como: Configurar um nome de domínio personalizado][Como: Configurar um nome de domínio personalizado]
-   [Como: configurar um site para usar SSL][Como: configurar um site para usar SSL]
-   [Próximas etapas][Próximas etapas]

## <a name="howtochangeconfig"></a>Como: alterar as opções de configuração de um site

<!-- HOW TO: CHANGE CONFIGURATION OPTIONS FOR A WEBSITE -->

Para configurar as opções de configuração de um site:

1.  No [Portal de Gerenciamento][Portal de Gerenciamento], abra as páginas de gerenciamento do site.
2.  Clique na guia **Configurar**.

A guia **Configurar** tem as seguintes seções:

### Geral

**Versão do Framework**. Configurar essas opções se seu aplicativo usa qualquer um desses frameworks:

-   **.NET Framework**: configurar a versão do .NET framework.
-   **PHP**: configurar a versão PHP, ou **DESATIVADO** para desabilitar o PHP.
-   **Java**: selecionar a versão exibida para habilitar o Java, ou **DESATIVADO** para desabilitar o Java.
-   Se você habilitar o Java, use a opção **Contêiner da web** para escolher entre versões Tomcat e Jetty.
-   **Python**: selecionar a versão Python, ou **DESATIVADO** para desabilitar o Python.

Por razões técnicas, a habilitação do Java para seu site desabilita as opções .NET, PHP e Python.

**Modo de Pipeline Gerenciado**. Configurar o IIS [modo de pipeline][modo de pipeline]. Deixar este conjunto como Integrado (o padrão), a menos que você tenha um site de legado que requeira uma versão anterior do IIS.

**Plataforma**. Seleciona se seu aplicativo é executado no ambiente de 32-bit ou 64 bit. O ambiente de 64-bit requere o modo Básico ou Padrão. Modos Livre e Compartilhado são sempre executados em um ambiente de 32 bits.

**Web Sockets**. Configurar **ATIVADO** para habilitar o protocolo WebSocket; por exemplo, se seu site usa [ASP.NET SignalR][ASP.NET SignalR] ou [socket.io][socket.io].

**Sempre Ativado**. Por padrão, os sites serão descarregados se estiverem ociosos durante um determinado período de tempo. Isso permite que o sistema conservar os recursos. No modo Básico ou Padrão, você pode habilitar **Sempre Ativado** para manter o site carregado o tempo todo. Se seu site executar continuamente trabalhos web, você deve habilitar **Sempre Ativado**, ou é possível que os trabalhos web não sejam executados de forma segura.

**Editar no Visual Studio Online**. Habilita a edição do código ativo com o Visual Studio Online. Se for habilitado, a guia do Painel exibirá um link chamado **Editar no Visual Studio Online**, na seção **Visão Rápida**. Clique neste link para editar o seu site diretamente online. Se precisar autenticar, você poderá usar suas credenciais de implantação básica.

Observação: se você habilitar a implantação do controle do código-fonte, será possível que uma implantação substitua as alterações feitas no editor Visual Studio Online.

### Certificados

no modo Básico ou Padrão, você pode carregar certificados SSL para domínios personalizados. Para obter mais informações, consulte [Habilitar HTTPS para um site do Azure][Habilitar HTTPS para um site do Azure].

Seus certificados carregados estão listados aqui. Após carregar um certificado, você poderá atribuí-lo a qualquer site na sua assinatura e região. Os certificados Wildcard podem ser usados para qualquer site dentro do domínio para o qual for válido. Um certificado pode ser excluído apenas se não houver associações para esse certificado.

### Nomes de domínio

Visualizar ou adicionar nomes de domínio para o site. Para obter mais informações, consulte [Configurando um nome de domínio personalizado para um sites do Azure][Configurando um nome de domínio personalizado para um sites do Azure].

### Associações SSL

Se você tiver carregado certificados SSL, você pode associá-los para nomes personalizados de domínio. Para obter mais informações, consulte [Habilitar HTTPS para um site do Azure][Habilitar HTTPS para um site do Azure].

### Implantações

Esta seção é exibida somente se você tiver habilitado a implantação no controle de origem. Use essas configurações para definir implantações.

-   **URL do Git**. Se você tiver criado um repositório do Git para seu site do Azure, esta é a URL onde você envia seu conteúdo por push.
-   **URL do gatilho da implantação**. Esta URL pode ser definida em um GitHub, CodePlex, Bitbucket ou outro repositório para acionar a implantação quando uma confirmação é enviada para o repositório.
-   **Ramificação a ser implantada**. Especifica a ramificação que será implantada quando você envie seu conteúdo por push.

Para configurar a implantação do controle de origem, visualize a guia **Painel**, e clique em **Configurar a implantação do controle de origem**.

### Diagnóstico de aplicativo

Opções para gravar os logs de diagnóstico de um aplicativo web que ofereça suporte ao registro em log:

-   **Sistema de arquivos**. Grava os logs ao sistema de arquivos do site. O registro em log de sistema de arquivos dura por um período de 12 horas. Você pode acessar os logs do compartilhamento de FTP para o site. (Consulte [Credenciais FTP][Credenciais FTP]).
-   **Armazenamento de tabela**. Grava logs no armazenamento da tabela do Azure. Não há limite de tempo, e o registro em log fica habilitado até que você o desabilite.
-   **Armazenamento de Blob**. Grava logs no armazenamento de blob do Azure. Não há limite de tempo, e o registro em log fica habilitado até que você o desabilite.

**Nível de log**. Quando o registro estiver ativado, esta opção especifica a quantidade de informações que serão gravadas (erro, aviso, informações ou detalhado).

**Gerenciar armazenamento da tabela**. Quando o armazenamento de tabela for habilitado, clique neste botão para configurar a conta de armazenamento e o nome da tabela.

**Gerenciar armazenamento do blob.** Quando o armazenamento de blob for habilitado, clique neste botão para configurar a conta de armazenamento e o nome do blob.

### Diagnóstico de site

Opções para coleta de informações de diagnóstico para seu site.

**Logs de servidor da Web**. Habilita o log de servidor web. Os logs são salvos no formato W3C estendido de arquivo de log. Você pode salvar os logs no sistema de arquivos do site ou no Armazenamento do Azure.

-   Se você selecionar **Sistema de Arquivos**, os logs serão salvos no site FTP listado em "Logs de Diagnóstico de FTP" na página Painel. (Consulte [Credenciais FTP][Credenciais FTP])
-   Se você escolher **Sistema de arquivos**, use a caixa **Cota** para definir a quantidade máxima de espaço do disco para os arquivos log. O mínimo é 25 MB e o máximo é 100 MB. O padrão é 35MB. Quando a cota for atingida, os arquivos mais antigos serão substituídos sucessivamente pelos mais recentes. Se for necessário manter mais histórico que 100MB, use o Armazenamento do Azure, que tem uma capacidade de armazenamento muito maior.
-   Opcionalmente, clique em **Definir Retenção** para excluir automaticamente os arquivos após um período de tempo. Por padrão, os logs nunca são excluídos.

**Mensagens Detalhados de Erro**. Se estiver habilitado, as mensagens detalhadas de erro serão salvas como arquivos .htm. Para visualizar os arquivos, vá para o site FTP listado em "Logs de diagnóstico FTP" na página do Painel. Os arquivos são salvos em /LogFiles/DetailedErrors no site FTP. (Consulte [Credenciais FTP][Credenciais FTP])

**Rastreamento de Solicitação Falha**. Se estiver habilitada, as solicitações com falha são armazenadas em log em arquivos XML. Para visualizar os arquivos, vá para o site FTP listado em "Logs de diagnóstico FTP" na página do Painel. (Consulte [Credenciais FTP][Credenciais FTP]) Os arquivos são salvos em /LogFiles/W3SVC*xxx*, onde xxx é um identificador único. Esta pasta contém um arquivo XSL e um ou mais arquivos XML. Certifique-se de baixar o arquivo XSL, porque ele fornece funcionalidade para formatar e filtrar os conteúdos dos arquivos XMl.

**Depuração remota** Habilita a depuração remota. Quando estiver habilitado, você pode usar o depurador remoto no Visual Studio para conectar-se diretamente com seu site do Azure. A depuração remota permanecerá habilitada por 48 horas.

**Observação**: A depuração remota não funciona com um nome de site ou um nome de usuário que seja maior que 20 caracteres.

### Monitoramento

No modo Básico ou Padrão, você pode testar a disponibilidade dos pontos de extremidade HTTP ou HTTPs, de até três locais geograficamente distribuídos. Um teste de monitoramento falhará se o código de resposta HTTP for um erro (4xx ou 5xx) ou se a resposta demorar mais de 30 segundos. Um ponto de extremidade será considerado disponível caso os testes de monitoramento tenham êxito a partir de todos os locais especificados.

Para obter mais informações, consulte [Como monitorar o status de ponto de extremidade da Web][Como monitorar o status de ponto de extremidade da Web].

### Análise para o desenvolvedor

Selecione **Complemento** para selecionar um complemento de análise de uma lista ou acesse a Azure Store para escolher um. Selecione **Personalizado** para selecionar um provedor de análise, como o New Relic de uma lista. Se usar um provedor personalizado, você deverá inserir a chave de licença na caixa **Chave do Provedor**.

para obter mais informações sobre o uso do New Relic com Sites do Azure, consulte [Gerenciamento de desempenho do aplicativo New Relic nos Sites do Azure (a página pode estar em inglês)][Gerenciamento de desempenho do aplicativo New Relic nos Sites do Azure (a página pode estar em inglês)].

### Configurações do aplicativo

Especifique pares nome/valor que serão carregados pelo seu aplicativo Web na inicialização.

-   Para sites .NET, essas configurações serão injetadas em sua configuração AppSettings do .NET em tempo de execução, substituindo as configurações existentes.

-   Os aplicativos PHP, Python, Java e Nó podem acessar essas configurações como variáveis do ambiente em tempo de execução. Para cada configuração do aplicativo, são criadas duas variáveis; uma com o nome especificado pela entrada de configuração do aplicativo, e outra com um prefixo de APPSETTING\_. Ambas contêm o mesmo valor.

### Cadeias de conexão

Cadeia de conexão para recursos vinculados.

Para sites .NET, essas cadeias de conexão serão injetadas em suas configurações connectionStrings do .NET em tempo de execução substituindo as entradas existentes onde a chave é igual ao nome do banco de dados vinculado.

Para aplicativos PHP, Python, Java e Nó essas configurações estarão disponíveis como variáveis de ambiente em tempo de execução, antecedidas com o tipo de conexão. Os prefixos das variáveis de ambiente são os seguintes:

-   SQL Server: SQLCONNSTR\_
-   MySQL: MYSQLCONNSTR\_
-   Banco de Dados SQL: SQLAZURECONNSTR\_
-   Personalizado: CUSTOMCONNSTR\_

Por exemplo, se uma cadeia de conexão MySql fosse nomeado connectionstring1, ela seria acessada pela variável de ambiente `MYSQLCONNSTR_connectionString1`.

**Observação**: As cadeias de conexão também são criadas quando você vincula um recurso de banco de dados a um site. As cadeias de conexão criadas desta maneira são lidas apenas quando são visualizadas na
página de gerenciamento de configuração.

### Documentos padrão

Um documento padrão de um site é a página web que é exibida e não especifica uma página em particular no site. Se seu site contiver mais de um dos arquivos na lista, certifique-se de colocar seu documento padrão no topo da lista.

Os aplicativos web podem usar módulos que rotearão na URL, em vez de atender ao conteúdo estático, nesse caso, não há um documento padrão como tal.

### Mapeamentos de manipulador

Use essa área para adicionar processadores de script personalizados para manipular solicitações de extensões de arquivo específicas.

-   **Extensão**. A extensão do arquivo a ser manipulada, como \*.php ou handler.fcgi.
-   **Caminho do Processador de script**. O caminho absoluto do processador de script. As solicitações para arquivos que correspondam a extensão do arquivo serão processadas pelo processador de script. Usar o caminho `D:\home\site\wwwroot` para se referir ao diretório raiz do seu site.
-   **Argumentos adicionais**. Argumentos da linha de comando opcionais para o processador de script

### Aplicativos e diretórios virtuais

Para configurar os aplicativos e diretórios virtuais associados com seu site, especificar cada diretório virtual e seu caminho físico correspondente relativo à raiz do site. Opcionalmente, você pode selecionar a caixa de seleção **Aplicativo** para marcar um diretório virtual como um aplicativo na configuração do site.

<!-- HOW TO: CONFIGURE A WEBSITE TO USE A SQL DATABASE -->

## <a name="howtoconfigSQL"></a>Como: configurar um site para usar um banco de dados SQL

Siga estas etapas para vincular um site a um Banco de Dados SQL:

1.  No [Portal de Gerenciamento][1], selecione **Sites** para exibir a lista de sites criada por quem estiver conectado atualmente na conta.

2.  Selecione um site da lista de sites para abrir as páginas de **Gerenciamento** do site.

3.  Clique na guia **Recursos Vinculados** e uma mensagem será exibida na página **Recursos Vinculados** indicando **Você não tem recursos vinculados**.

4.  Clique em **Vincular um Recurso** para abrir o assistente **Vincular um Recurso**.

5.  Clique em **Criar um novo recurso** para exibir uma lista de tipos de recursos que podem estar vinculados ao seu site.

6.  Clique em **Banco de Dados SQL** para exibir o assistente **Vincular Banco de Dados**.

7.  Conclua os campos obrigatórios nas páginas 3 e 4 do assistente **Vincular Banco de Dados** e, em seguida, clique na marca de seleção **Concluir** na página 4.

O Azure criará um Banco de Dados SQL com os parâmetros especificados e vinculará o banco de dados ao site.

<!-- HOW TO: CONFIGURE A WEBSITE TO USE A MYSQL DATABASE -->

## <a name="howtoconfigMySQL"></a>Como: configurar um site para usar um banco de dados MySQL

Para configurar um site para usar um banco de dados MySQL, siga as mesmas etapas de uso do banco de dados SQL, mas no assistente **Vincular um Recurso**, selecione **Banco de Dados MySQL** em vez de **Banco de Dados SQL**.

Alternativamente, você pode criar o site com a opção **Criação Personalizada**. Na lista suspensa **Banco de Dados**, selecione **Criar um novo banco de dados MySQL** ou **Usar um banco de dados MySQL existente**.

## <a name="howtodomain"></a>Como: Configurar um nome de domínio personalizado

Para obter informações sobre a configuração do seu site para usar um nome de domínio personalizado, consulte [Configurando um nome de domínio personalizado para Sites do Azure (a página pode estar em inglês)][Configurando um nome de domínio personalizado para um sites do Azure].

## <a name="howtoconfigSSL"></a>Como: configurar um site para usar SSL

Para obter informações sobre a configuração de SSL para um domínio personalizado no Azure, consulte [Habilitar HTTPS para um site do Azure (a página pode estar em inglês)][Habilitar HTTPS para um site do Azure (a página pode estar em inglês)].

## <a name="next"></a>Próximas etapas

-   [Como dimensionar sites][Como dimensionar sites]

-   [Como monitorar Sites][Como monitorar Sites]

  [Como: alterar as opções de configuração de um site]: #howtochangeconfig
  [Como: configurar um site para usar um banco de dados SQL]: #howtoconfigSQL
  [Como: configurar um site para usar um banco de dados MySQL]: #howtoconfigMySQL
  [Como: Configurar um nome de domínio personalizado]: #howtodomain
  [Como: configurar um site para usar SSL]: #howtoconfigSSL
  [Próximas etapas]: #next
  [Portal de Gerenciamento]: https://manage.windowsazure.com/
  [modo de pipeline]: http://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
  [ASP.NET SignalR]: http://www.asp.net/signalr
  [socket.io]: http://azure.microsoft.com/pt-br/documentation/articles/web-sites-nodejs-chat-app-socketio/
  [Habilitar HTTPS para um site do Azure]: href="http://www.windowsazure.com/pt-br/documentation/articles/web-sites-configure-ssl-certificate/
  [Configurando um nome de domínio personalizado para um sites do Azure]: http://www.windowsazure.com/pt-br/documentation/articles/web-sites-custom-domain-name/
  [Credenciais FTP]: http://azure.microsoft.com/pt-br/documentation/articles/web-sites-manage#ftp-credentials
  [Como monitorar o status de ponto de extremidade da Web]: http://go.microsoft.com/fwLink/?LinkID=279906&clcid=0x409
  [Gerenciamento de desempenho do aplicativo New Relic nos Sites do Azure (a página pode estar em inglês)]: http://www.windowsazure.com/pt-br/documentation/articles/store-new-relic-web-sites-dotnet-application-performance-management/
  [1]: http://manage.windowsazure.com
  [Habilitar HTTPS para um site do Azure (a página pode estar em inglês)]: http://www.windowsazure.com/pt-br/documentation/articles/web-sites-configure-ssl-certificate/
  [Como dimensionar sites]: http://www.windowsazure.com/pt-br/documentation/articles/web-sites-scale/
  [Como monitorar Sites]: http://www.windowsazure.com/pt-br/documentation/articles/web-sites-monitor/
