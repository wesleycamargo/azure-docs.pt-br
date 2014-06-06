<properties linkid="manage-services-how-to-configure-websites" urlDisplayName="Como configurar" pageTitle="Como configurar sites - gerenciamento de serviços do Azure" metaKeywords="sites do Azure, configurando sites do Azure, Banco de Dados SQL do Azure, MySQL do Azure" description="Saiba como configurar sites no Azure, incluindo como configurar um site para usar um Banco de Dados SQL ou MySQL." metaCanonical="" services="web-sites" documentationCenter="" title="Como configurar sites" authors="timamm" solutions="" manager="" editor="mollybos" />





# Como configurar sites #
No Portal de Gerenciamento do Azure, você pode alterar as opções de configuração de sites e vincular um site a outros recursos do Azure. Por exemplo, você pode vincular sites ao Banco de Dados SQL para fornecer funcionalidade adicional. Você também pode configurar sites para usar um banco de dados MySQL novo ou existente.

## Sumário ##
- [Como alterar as opções de configuração de um site](#howtochangeconfig)
- [Como configurar um site para usar um banco de dados SQL](#howtoconfigSQL)
- [Como configurar um site para usar um banco de dados MySQL](#howtoconfigMySQL)
- [Como configurar um nome de domínio personalizado](#howtodomain)
- [Como configurar um site para usar SSL](#howtoconfigSSL)
- [Próximas etapas](#next)



##<a name="howtochangeconfig"></a>Como alterar as opções de configuração de um site

<!-- HOW TO: CHANGE CONFIGURATION OPTIONS FOR A WEB SITE -->

Siga estas etapa para alterar as opções de configuração de um site.

<ol>
	<li>No Portal de Gerenciamento, abra as páginas de gerenciamento do site.</li>
	<li>Clique na guia <strong>Configurar</strong> para abrir a página de gerenciamento <strong>Configurar</strong>.</li>
	<li>Defina as seguintes opções de configuração para o site conforme apropriado:
	<ul>

	<!-- GENERAL -->
	<li><strong>geral</strong>
<ul>
<li><strong>Versão do .NET Framework</strong> - Se o seu aplicativo web usar o .NET Framework, defina a versão da estrutura requerida pelo aplicativo web.</li>

<li><strong>Versão do PHP</strong> - Se o seu aplicativo web usar o PHP, defina a versão do PHP requerida pelo aplicativo web.</li>
<li><strong>Versão do Java</strong> - Selecione a versão exibida do Java para habilitá-lo para o seu aplicativo web, ou <strong>DESATIVADO</strong> para desativar o Java. Se você habilitar o Java para seu aplicativo web, a opção <strong>Contêiner da Web</strong> permitirá que você escolha entre versões do Tomcat e do Jetty.
<p><strong>Observação</strong>: por razões técnicas, a habilitação do Java para seu site desabilita as opções .NET, PHP e Python.</p>
</li>
<li><strong>Versão do Python</strong> - Mostra a versão do Python (não configurável).</li>
<li><strong>Modo de Pipeline Gerenciado</strong> - Das duas opções, <strong>Clássico</strong> e <strong>Integrado</strong>, o integrado é o padrão. Você deverá usar a opção Clássico somente se tiver sites herdados que são executados exclusivamente em versões anteriores do IIS.</li>

<li><strong>Plataforma</strong> - Para sites no modo Padrão, você pode escolher se deseja que o aplicativo seja executado em um ambiente de 32 ou de 64 bits. Sites nos modos Livre e Compartilhado são sempre executados em um ambiente de 32 bits.</li>

<li><strong>WebSockets</strong> - Selecione <strong>Ativado</strong> para habilitar o seu site para usar aplicativos padrão de solicitação, como o chat.</li>

<li><strong>Sempre Ativo</strong> - Por padrão, os sites serão descarregados se estiverem ociosos durante um determinado período de tempo. Isso permite que o sistema conservar os recursos. Você poderá habilitar a configuração <strong>Sempre Ativo</strong> de um site no modo Padrão se o site precisar ser carregado o tempo todo. Como os trabalhos da Web contínuos poderão não ser executados com confiança se <strong>Sempre Ativo</strong> estiver desativado, você deve ativar <strong>Sempre Ativo</strong> quando houver trabalhos da Web contínuos em execução no site.</li>

<li><strong>Editar no Visual Studio Online</strong> - Selecione <strong>Ativado</strong> para permitir a edição de código ativo com o Visual Studio Online. Depois que você salvar essa alteração de configuração, a guia <strong>Visão Rápida</strong> do PAINEL exibirá um link chamado <strong>Editar no Visual Studio Online</strong>. Clique no link para editar o seu site diretamente online. Se precisar autenticar, você poderá usar suas credenciais de implantação básica. 
<p><strong>Nota</strong>: se a "implantação do controle do código-fonte" estiver ativada, será possível que uma implantação substitua as alterações feitas no editor Visual Studio Online. Portanto, é aconselhável não usar "implantação do controle do código-fonte"' se você deseja editar o conteúdo do site diretamente com o Visual Studio Online.</p>
</li>



</ul></li>

<!-- CERTIFICATES -->
<li><strong>certificados</strong> - Somente no modo Padrão, você pode clicar em <strong>Carregar</strong> para carregar um certificado SSL para um domínio personalizado. Os certificados que você carrega estão listados aqui. Há suporte para certificados curinga (estrela) (certificados com um asterisco). Após carregar um certificado, você poderá atribuí-lo a qualquer site na sua assinatura e região. Um certificado estrela apenas tem de ser carregado uma vez, mas pode ser usado para qualquer site dentro do domínio para o qual ele é válido. Um certificado pode ser excluído somente se não há associações ativas para um determinado certificado.
<br /><strong>Observação:</strong>
Domínios personalizados estão disponíveis somente nos modos Padrão e Compartilhado, e o suporte SSL para domínios personalizados está disponível no modo Padrão somente. Para obter informações sobre a configuração SSL para um domínio personalizado no Azure, consulte <a href="http://www.windowsazure.com/pt-br/documentation/articles/web-sites-configure-ssl-certificate/">Habilitar HTTPS para um site do Azure (a página pode estar em inglês)</a>.
</li>

<!-- DOMAIN NAMES -->
<li><strong>nomes de domínio</strong> - Exiba ou adicione nomes de domínio ao site aqui. Você pode adicionar domínios personalizados clicando em <strong>Gerenciar Domínios</strong>. Domínios personalizados estão disponíveis somente nos modos <strong>Compartilhado</strong> e <strong>Padrão</strong>. Você pode alterar o modo de site na página de gerenciamento <strong>Escala</strong>. Esses domínios não estão disponíveis no modo Gratuito. Para obter mais informações sobre a configuração de domínios personalizados, consulte <a href="http://www.windowsazure.com/pt-br/documentation/articles/web-sites-custom-domain-name/">Configurando um nome de domínio personalizado para um site do Azure (a página pode estar em inglês)</a>.</li>

<!-- SSL BINDINGS -->
<li><strong>Associações SSL</strong> - As associações SSL aos domínios personalizados estão disponíveis somente no modo Padrão. Escolha um modo SSL (<strong>SNI</strong>, <strong>IP</strong> ou <strong>Sem SSL</strong>) para um nome de domínio específico. Se você escolher SNI ou IP, você pode especificar um certificado para o domínio de certificados que você carregou. Para obter informações sobre a configuração SSL para um domínio personalizado no Azure, consulte <a href="http://www.windowsazure.com/pt-br/documentation/articles/web-sites-configure-ssl-certificate/">Habilitar HTTPS para um site do Azure (a página pode estar em inglês)</a>. Para obter mais informações sobre SNI, consulte <a href="http://en.wikipedia.org/wiki/Server_Name_Indication">Indicação de nome do servidor</a>.</li>

<!-- DEPLOYMENTS  -->
<li><strong>implantações</strong> - Use essas configurações para definir implantações.
<ul>
<li><strong>URL do Git</strong> - Se você tiver criado um repositório Git no site do Azure, esta é a sua URL - o local ao qual você envia seu conteúdo por push.</li>
<li><strong>URL do Gatilho da Implantação</strong> - Esta URL pode ser definida em um GitHub, CodePlex, Bitbucket ou outro repositório para acionar a implantação quando uma confirmação é enviada por push para o repositório.</li>
<li><strong>Ramificação a ser implantada</strong> - Permite que você especifique a ramificação que será implantada ao enviar conteúdo por push.</li>
</ul>
</li>

<!-- APPLICATION DIAGNOSTICS  -->
- <li><strong>diagnóstico de aplicativo</strong> - Defina as opções para a coleta de rastreamentos de diagnóstico de um aplicativo web cujo código foi instrumentado com rastreamentos. As opções de log de diagnóstico do aplicativo incluem:
<ul>
<!-- APPLICATION LOGGING (FILE SYSTEM) -->
<li><strong>Log de Aplicativo (Sistema de Arquivos)</strong> - Escolha <strong>Ativado</strong> para ter os logs de aplicativos gravados no sistema de arquivos do site. Quando ativado, o log de sistema de arquivos dura por um período de 12 horas. Você pode acessar os logs do compartilhamento de FTP para o site. O link para o compartilhamento FTP pode ser encontrado em <strong>Painel</strong>. Em <strong>Visão Rápida</strong>, escolha <strong>Logs de Diagnóstico de FTP</strong> ou <strong>Logs de Diagnóstico de FTPS</strong>.</li>

<!-- APPLICATION LOGGING (STORAGE) -->
<li><strong>Log de Aplicativo (Armazenamento)</strong> - Escolha <strong>Ativado</strong> para ter os logs de aplicativos gravados em uma conta de armazenamento do Azure. O registro para uma conta de armazenamento não tem nenhum limite de tempo e permanece ativado até que você o desative. Por padrão, os logs são armazenados em uma tabela chamada WAWSAppLogTable.</li>
<li><strong>Nível de Log</strong> - Quando o registro estiver ativado, esta opção especifica a quantidade de informações que serão gravadas (Erro, Aviso, Informações ou Detalhado).</li>

<!-- DIAGNOSTIC STORAGE -->
<li><strong>Armazenamento de diagnóstico</strong> - Um clique em <strong>Gerenciar Conexão</strong> abre a caixa de diálogo <strong>Gerenciar armazenamento de diagnóstico</strong> com as seguintes opções para salvar os logs em sua conta de armazenamento do Azure:
<ul>
<li><strong>Nome da Conta de Armazenamento</strong> - Escolha a conta de armazenamento na qual você deseja salvar os logs.</li>
<li><strong>Chave de Acesso de Armazenamento</strong> - Exibe a chave da conta de armazenamento escolhida. Se você tiver regenerada a chave da conta de armazenamento, digite a nova chave manualmente ou use um dos botões <strong>Sincronizar</strong>. Os botões sincronizar estarão disponíveis somente se o usuário conectado no momento tem acesso à conta de armazenamento selecionado.
</li>
<li><strong>Sincronizar Chave Primária</strong> - Recupera a chave primária mais recente da sua conta de Armazenamento do Azure.
</li>
<li><strong>Sincronizar Chave Secundária</strong> - Recupera a chave secundária mais recente da sua conta de Armazenamento do Azure.
<br /><strong>Observação:</strong> para obter mais informações sobre chaves de acesso do Armazenamento do Azure, consulte <a href="http://www.windowsazure.com/pt-br/documentation/articles/storage-manage-storage-account/#regeneratestoragekeys">Como exibir, copiar e regenerar chaves de acesso de armazenamento (a página pode estar em inglês)</a>.
</li></ul></li></ul></li>

<!-- SITE DIAGNOSTICS  -->
<li><strong>diagnóstico de site</strong> - Defina as opções para a coleta de informações de diagnóstico 
	para seu site, incluindo:
<ul>
<!-- WEB SERVER LOGGING -->
	<li><strong>Log de Servidor Web</strong> - Especifique se deseja ativar o log de servidor web do site. Os logs de servidor Web são salvos no formato W3C estendido de arquivo de log. Você pode salvar os logs no sistema de arquivos ou no Armazenamento do Azure. 
<br /><strong>Dica</strong>: o tamanho máximo de armazenamento de log no sistema de arquivos é de 100 megabytes. Se for necessário manter mais histórico que isso, use o Armazenamento do Azure, que tem uma capacidade de armazenamento muito maior.
	<ul>
		<li>Para salvar os logs de servidor web em uma conta de Armazenamento do Azure, selecione <strong>Armazenamento</strong> e, em seguida, selecione <strong>gerenciar armazenamento</strong>. Na caixa de diálogo <strong>Gerenciar Armazenamento para Diagnóstico de Site</strong> use a opção <strong>Conta de Armazenamento</strong> para selecionar a conta de Armazenamento do Azure para o contêiner que conterá os logs. Use a opção <strong>Contêiner de Blob do Azure</strong> para selecionar o contêiner que manterá os logs, ou selecione <strong>Criar um novo contêiner de blobs</strong> para habilitar a caixa <strong>Nome do Blob</strong> na qual você poderá especificar um nome para o novo contêiner.
<br /><strong>Observação</strong>: se você ainda não tiver uma conta de armazenamento, acesse a seção <strong>Armazenamento</strong> do portal do Azure no qual você pode clicar em <strong>Novo</strong> para criar uma conta. 
</li>
		<li>Se você selecionar <strong>Sistema de Arquivos</strong>, os logs serão salvos no site FTP listado em <strong>Logs de Diagnóstico de FTP</strong> na página de gerenciamento Painel. Ao habilitar o armazenamento do Sistema de Arquivos, a caixa <strong>Cota</strong> também será habilitada, na qual você poderá definir a quantidade máxima de espaço em disco para os arquivos de log. O mínimo é 25 MB e o máximo é 100 MB. O padrão é 35MB. Quando a cota for atingida, os arquivos mais antigos serão substituídos sucessivamente pelos mais recentes.</li>
		<li>Por padrão, os logs de servidor web em uma conta de Armazenamento do Azure nunca são excluídos. Para especificar um período de tempo após o qual os logs serão excluídos automaticamente, selecione <strong>Definir Retenção</strong> e insira o número de dias que os logs serão mantidos na caixa <strong>Período de Retenção</strong>. Você também poderá usar a opção <strong>Definir Retenção</strong> para o armazenamento de sistema de arquivos.</li></ul>
</li>

<!-- DETAILED ERROR MESSAGES -->
	<li><strong>Mensagens de Erro Detalhadas</strong> - Especifique se deseja registrar mensagens de erro detalhadas do site em log. 
	Se habilitada, as mensagens de erro detalhadas serão salvas como arquivos .htm no site FTP listado em Logs de Diagnóstico de FTP na página de gerenciamento Painel. 
	Depois de se conectar ao site FTP especificado navegue até /Arquivos de Log/Erros Detalhados/ para recuperar os arquivos .htm que contêm mensagens de erro detalhadas.</li>

<!-- FAILED REQUEST TRACING -->
	<li><strong>Falha no Rastreamento de Solicitação</strong> - Especifique se deseja habilitar a falha no rastreamento de solicitação. Se estiver habilitado, 
	a saída de falha no rastreamento de solicitação é gravada nos arquivos XML e salva no site FTP listado em Logs de Diagnóstico de FTP na página de gerenciamento Painel. 
	Depois de se conectar ao site FTP especificado navegue até /Arquivos de Log/W3SVC######### (em que ######### representa um identificador exclusivo para o site) 
	para recuperar os arquivos XML que contêm a saída de falha no rastreamento de solicitação.<br /><strong>Importante</strong><br />A pasta /LogFiles/W3SVC#########/ 
	pasta contém um arquivo XSL e um ou mais arquivos XML. Certifique-se de baixar o arquivo XSL para o mesmo diretório dos arquivos XML porque 
	o arquivo XSL fornece funcionalidade para formatar e filtrar o conteúdo do(s) arquivo(s) XML quando visualizados no Internet Explorer.</li>

<!-- REMOTE DEBUGGING -->
<li><strong>Depuração Remota</strong> - Defina esta opção como <strong>Ativada</strong> para habilitar a depuração remota em sua opção de Visual Studio 2012 ou Visual Studio 2013. Quando habilitada, você poderá usar o depurador remoto no Visual Studio para conectar diretamente ao site do Azure.
<br />
<strong>Observação</strong>: A depuração remota só será ativada por 48 horas e não vai funcionar com o nome do site ou nome de usuário com mais de 20 caracteres. 
</li>
	</ul>
	</li>

<!-- MONITORING  -->
<li><strong>monitoramento</strong> - Para sites no modo Padrão, teste a disponibilidade de pontos de extremidade HTTP ou HTTPS. Você pode testar um ponto de extremidade a partir de até três locais geograficamente distribuídos. Um teste de monitoramento falhará se o código de resposta HTTP for maior ou igual a 400 ou se a resposta demorar mais de 30 segundos. Um ponto de extremidade será considerado disponível caso seus testes de monitoramento tenham êxito a partir de todos os locais especificados.
</li>

<!-- DEVELOPER ANALYTICS -->
<li><strong>análise para o desenvolvedor</strong> - Selecione <strong>Complemento</strong> para selecionar um complemento de análise de uma lista ou acesse a Azure Store para escolher um. Selecione <strong>Personalizado</strong> para selecionar um provedor de análise, como o New Relic de uma lista. Se usar um provedor personalizado, você deverá inserir a chave de licença na caixa<strong> Chave do Provedor</strong>. 
<br /> <strong>Observação</strong>: para obter mais informações sobre o uso do New Relic com Sites do Azure, consulte <a href="http://www.windowsazure.com/pt-br/documentation/articles/store-new-relic-web-sites-dotnet-application-performance-management/">Gerenciamento de desempenho do aplicativo New Relic nos Sites do Azure (a página pode estar em inglês)</a>.
</li>

<!-- APP SETTINGS -->
	<li><strong>configurações do aplicativo</strong> - Especifique os pares de nome/valor que serão carregados por seu aplicativo Web na inicialização. Para sites .NET, essas configurações serão 
	injetadas em sua configuração AppSettings do .NET em tempo de execução, substituindo as configurações existentes. Para sites PHP e de nó, essas configurações estarão 
	disponíveis como variáveis de ambiente em tempo de execução.</li>

<!-- CONNECTION STRINGS -->
	<li><strong>cadeias de conexão</strong> - Exiba cadeias de conexão para recursos vinculados. Para sites .NET, essas cadeias de conexão serão injetadas em suas configurações connectionStrings do .NET em tempo de execução substituindo as entradas existentes onde a chave é igual ao nome do banco de dados vinculado. Para sites PHP 
	e de nó essas configurações estarão disponíveis como variáveis de ambiente em tempo de execução, antecedidas com o tipo de conexão. Os prefixos das variáveis de ambiente são os seguintes: <br />
<ul><li>Servidor SQL: SQLCONNSTR_</li>
<li>MySQL: MYSQLCONNSTR_</li>
<li>Banco de Dados SQL: SQLAZURECONNSTR_</li>
<li>Personalizado: CUSTOMCONNSTR_</li></ul>Por exemplo, se uma cadeia de conexão MySql fosse nomeada connectionstring1, ela seria acessada pela variável de ambiente <code>MYSQLCONNSTR_connectionString1</code>.
	<br /><strong>Observação</strong>: as cadeias de conexão são criadas quando você vincula um recurso de banco de dados a um site e são somente leitura quando visualizadas na 
	página de gerenciamento de configuração.</li>

<!-- DEFAULT DOCUMENTS -->
	<li><strong>documentos padrão</strong> - Adicione o documento padrão do seu site a essa lista se ele ainda não estiver na lista. O documento padrão de um site 
	é a página da Web que é exibida quando um usuário navega para um site e não especifica uma página em particular no site. Portanto, dado o 
	site http://contoso.com, se o documento padrão fosse definido como default.htm, um usuário serio encaminhado para http://www.contoso.com/default.htm ao apontar 
	o navegador para http://www.contoso.com. Se o seu site contém mais de um dos arquivos da lista, em seguida, certifique-se de que o documento padrão do seu site 
	está na parte superior da lista alterando a ordem dos arquivos na lista.</li>

<!-- HANDLER MAPPINGS -->
<li><strong>mapeamentos de manipulador</strong> -  Adicione processadores de script personalizados para manipular solicitações de extensões de arquivo específicas. Especifique a extensão do arquivo a ser manipulada na caixa <strong>Extensão</strong> (por exemplo, *.php ou handler.fcgi). As solicitações para arquivos que correspondam a esse padrão serão processadas pelo processador de script especificado na caixa <strong>Caminho do Processador de Script</strong>. Um caminho absoluto é necessário para o processador de script (o caminho D:\home\site\wwwroot pode ser usado para se referir ao diretório raiz do seu site). Os argumentos de linha de comando opcionais para o processador de script podem ser especificados na caixa <strong>Argumentos Adicionais (Opcional)</strong>.
</li>

<!-- VIRTUAL APPLICATIONS AND DIRECTORIES -->
<li><strong>diretórios e aplicativos virtuais </strong> -  Para configurar diretórios e aplicativos virtuais associados ao seu site, especifique cada diretório virtual e seu caminho físico correspondente na raiz do site. Opcionalmente, você pode selecionar a caixa de seleção <strong>Aplicativo</strong> para marcar um diretório virtual como um aplicativo na configuração do site.
</li>

	
</ul></li>
<li>Clique em <strong>Salvar</strong> na parte inferior da página de gerenciamento <strong>Configurar</strong> para salvar as alterações de configuração.</li>
</ol>

<!-- HOW TO: CONFIGURE A WEB SITE TO USE A SQL DATABASE -->
##<a name="howtoconfigSQL"></a>Como configurar um site para usar um banco de dados SQL

Siga estas etapas para vincular um site a um Banco de Dados SQL:

1. No [Portal de Gerenciamento](http://manage.windowsazure.com), selecione **Sites da Web** para exibir a lista de sites criada por quem estiver conectado atualmente na conta.

2. Selecione um site na lista de sites para abrir as páginas **Gerenciamento** do site.

3. Clique na guia **Recursos Vinculados** e uma mensagem será exibida na página **Recursos Vinculados** indicando **Você não tem recursos vinculados**.

4. Clique em **Vincular um Recurso** para abrir o assistente **Vincular um Recurso**.

5. Clique em **Criar um novo recurso** para exibir uma lista de tipos de recursos que podem estar vinculados ao seu site.

6. Clique em **Banco de Dados SQL** para exibir o assistente **Vincular Banco de Dados**.

7. Conclua os campos obrigatórios nas páginas 3 e 4 do assistente **Vincular Banco de Dados** e, em seguida, clique na marca de seleção **Concluir** na página 4.

O Azure criará um Banco de Dados SQL com os parâmetros especificados e vinculará o banco de dados ao site.

<!-- HOW TO: CONFIGURE A WEB SITE TO USE A MYSQL DATABASE -->
##<a name="howtoconfigMySQL"></a>Como configurar um site para usar um banco de dados MySQL##
Para configurar um site para usar um banco de dados MySQL, siga as mesmas etapas de uso do banco de dados SQL, mas no assistente **Vincular um Recurso**, selecione **Banco de Dados MySQL** em vez de **Banco de Dados SQL**. 

Alternativamente, você pode criar o site com a opção **Criação Personalizada**. Na lista suspensa **Banco de Dados**, selecione **Criar um novo banco de dados MySQL** ou **Usar um banco de dados MySQL existente**. 

##<a name="howtodomain"></a>Como configurar um nome de domínio personalizado

Para obter informações sobre a configuração do seu site para usar um nome de domínio personalizado, consulte [Configurando um nome de domínio personalizado para Sites do Azure (a página pode estar em inglês)](http://www.windowsazure.com/pt-br/documentation/articles/web-sites-custom-domain-name/).

##<a name="howtoconfigSSL"></a>Como configurar um site para usar SSL##

Para obter informações sobre a configuração de SSL para um domínio personalizado no Azure, consulte [Habilitar HTTPS para um site do Azure (a página pode estar em inglês)](http://www.windowsazure.com/pt-br/documentation/articles/web-sites-configure-ssl-certificate/). 

##<a name="next"></a>Próximas etapas

* [Como dimensionar sites](http://www.windowsazure.com/pt-br/documentation/articles/web-sites-scale/)

* [Como monitorar Sites](http://www.windowsazure.com/pt-br/documentation/articles/web-sites-monitor/)


