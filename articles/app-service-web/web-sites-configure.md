<properties 
	pageTitle="Configurar aplicativos Web no Serviço de Aplicativo do Azure" 
	description="Como configurar um aplicativo Web nos Serviços de Aplicativos do Azure" 
	services="app-service\web" 
	documentationCenter="" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/11/2016" 
	ms.author="robmcm"/>

# Configurar aplicativos Web no Serviço de Aplicativo do Azure #

Este tópico explica como configurar um aplicativo Web usando o [Portal do Azure].

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Configurações do aplicativo

1. No [Portal do Azure], abra a folha do aplicativo Web.
2. Clique em **Todas as Configurações**.
3. Clique em **Configurações do Aplicativo**.

![Configurações do aplicativo][configure01]

A folha **Configurações do aplicativo** tem configurações agrupadas em várias categorias.

### Configurações gerais

**Versão do Framework**. Configurar essas opções se seu aplicativo usa qualquer um desses frameworks:

- **.NET Framework**: configure a versão do .NET framework.
- **PHP**: defina a versão PHP ou **DESATIVADO** para desabilitar o PHP.
- **Java**: selecione a versão do Java ou **DESATIVADO** para desabilitar o Java. Use a opção **Contêiner da Web** para escolher entre as versões do Tomcat e Jetty.
- **Python**: selecione a versão do Python ou **DESATIVADO** para desabilitar o Python.

Por razões técnicas, a habilitação do Java para seu aplicativo desabilita as opções .NET, PHP e Python.

<a name="platform"></a> **Plataforma**. Seleciona se o seu aplicativo é executado em ambiente de 32 ou 64 bits. O ambiente de 64-bit requere o modo Básico ou Padrão. Modos Livre e Compartilhado são sempre executados em um ambiente de 32 bits.

**Web Sockets**. Defina a opção como **ATIVADO** para habilitar o protocolo WebSocket; por exemplo, se seu aplicativo Web utiliza [ASP.NET SignalR] ou [socket.io].

<a name="alwayson"></a> **Sempre ativo**. Por padrão, os aplicativos Web serão descarregados se estiverem ociosos por um determinado período de tempo. Isso permite ao sistema conservar recursos. No modo Básico ou Padrão, você pode habilitar a opção **Sempre Ativado** para manter o aplicativo carregado o tempo todo. Se seu aplicativo executar continuamente trabalhos Web, você deve habilitar a opção **Sempre Ativado**; caso contrário, é possível que os trabalhos Web não sejam executados de modo seguro.

**Versão do Pipeline Gerenciado**. Configurar o IIS [modo de pipeline]. Deixar este conjunto como Integrado (o padrão), a menos que você tenha um aplicativo herdado que exija uma versão anterior do IIS.

**Troca Automática**. Se você habilitar a Troca Automática para um slot de implantação, o Serviço de Aplicativo alternará automaticamente o aplicativo Web em produção quando você enviar uma atualização por push para esse slot. Para saber mais, consulte [Implantar em slots de preparo para aplicativos Web no Serviço de Aplicativo do Azure](web-sites-staged-publishing.md).

### Depurando

**Depuração Remota**. Habilita a depuração remota. Quando habilitado, você pode usar o depurador remoto no Visual Studio para conectar-se diretamente com seu aplicativo Web. A depuração remota permanecerá habilitada por 48 horas.

### Configurações do aplicativo

Esta seção contém pares de nome/valor que seu aplicativo Web carregará na inicialização.

- Para aplicativos .NET, essas configurações serão injetadas em sua configuração `AppSettings` em tempo de execução, substituindo as configurações existentes.

- Os aplicativos PHP, Python, Java e Nó podem acessar essas configurações como variáveis do ambiente em tempo de execução. Para cada configuração do aplicativo, são criadas duas variáveis; uma com o nome especificado pela entrada de configuração do aplicativo, e outra com um prefixo de APPSETTING\_. Ambas contêm o mesmo valor.

### Cadeias de conexão

Cadeia de conexão para recursos vinculados.

Para aplicativos .NET, essas cadeias de conexão serão injetadas em suas definições do `connectionStrings` das configurações do .NET no tempo de execução, substituindo as entradas existentes cujas chaves são iguais ao nome do banco de dados vinculado.

Para aplicativos PHP, Python, Java e Nó essas configurações estarão disponíveis como variáveis de ambiente em tempo de execução, antecedidas com o tipo de conexão. Os prefixos das variáveis de ambiente são os seguintes:

- SQL Server: `SQLCONNSTR_`
- MySQL: `MYSQLCONNSTR_`
- Banco de Dados SQL: `SQLAZURECONNSTR_`
- Personalizado: `CUSTOMCONNSTR_`

Por exemplo, se uma cadeia de conexão MySql fosse nomeado `connectionstring1`, ela seria acessada pela variável de ambiente `MYSQLCONNSTR_connectionString1`.

### Documentos padrão

O documento padrão é a página da Web exibida na URL raiz de um site. O primeiro arquivo correspondente na lista é usado.

Os aplicativos Web podem utilizar módulos que rotearão na URL, em vez de atender ao conteúdo estático, nesse caso, não há um documento padrão como tal.

### Mapeamentos de manipulador

Use essa área para adicionar processadores de script personalizados para manipular solicitações de extensões de arquivo específicas.

- **Extensão**. A extensão do arquivo a ser manipulada, como *.php ou handler.fcgi.
- **Caminho do Processador de script**. O caminho absoluto do processador de script. As solicitações para arquivos que correspondam a extensão do arquivo serão processadas pelo processador de script. Use o caminho `D:\home\site\wwwroot` para se referir ao diretório raiz do seu aplicativo.
- **Argumentos adicionais**. Argumentos da linha de comando opcionais para o processador de script


### Aplicativos e diretórios virtuais 
 
Para configurar diretórios e aplicativos virtuais, especifique cada diretório virtual e seu caminho físico correspondente com relação à raiz do site. Opcionalmente, você pode marcar a caixa de seleção **Aplicativo** para marcar um diretório virtual como um aplicativo.


## Habilitar logs de diagnóstico

Para habilitar logs de diagnóstico:

1. Na folha do seu aplicativo Web, clique em **Todas as configurações**.
2. Clique em **Logs de diagnóstico**.

Opções para gravar os logs de diagnóstico de um aplicativo web que ofereça suporte ao registro em log:

- **Registro do Aplicativo**. Grava logs de aplicativos no sistema de arquivos. O registro em log dura por 12 horas.

**Nível**. Quando o registro de aplicativo em log estiver habilitado, esta opção especifica a quantidade de informações que serão gravadas (Erro, Aviso, Informações ou Detalhado).

**Log do servidor Web**. Os logs são salvos no formato W3C estendido de arquivo de log.

**Mensagens de erro detalhadas**. Salva arquivos .htm de mensagens de erro detalhadas. Os arquivos são salvos em /LogFiles/DetailedErrors.

**Rastreamento de solicitações com falha**. Registra solicitações malsucedidas em arquivos XML. Os arquivos são salvos em /LogFiles/W3SVC*xxx*, onde xxx é um identificador único. Esta pasta contém um arquivo XSL e um ou mais arquivos XML. Certifique-se de baixar o arquivo XSL, porque ele fornece funcionalidade para formatar e filtrar os conteúdos dos arquivos XMl.

Para exibir os arquivos de log, você deve criar credenciais FTP da seguinte maneira:

1. Na folha do seu aplicativo Web, clique em **Todas as configurações**.
2. Clique em **Credenciais de implantação**.
3. Digite um nome de usuário e uma senha.
4. Clique em **Salvar**.

![Definir credenciais de implantação][configure03]

O nome de usuário complete do FTP é “app\\nomedousuário” onde *app* é o nome do seu aplicativo Web. O nome de usuário é listado no aplicativo Web, em **Essentials**.

![Credenciais de implantação FTP][configure02]

## Outras tarefas de configuração

### SSL 

no modo Básico ou Padrão, você pode carregar certificados SSL para domínios personalizados. Para obter mais informações, veja [Habilitar HTTPS para um aplicativo Web].

Para exibir os certificados carregados, clique em **Todas as Configurações** > **Domínios personalizados e SSL**.

### Nomes de domínio

Adicione nomes de domínio personalizados para seu aplicativo Web. Para obter mais informações, veja [Configurar um nome de domínio personalizado para um aplicativo Web no Serviço de Aplicativo do Azure].

Para exibir seus nomes de domínio, clique em **Todas as Configurações** > **Domínios personalizados e SSL**.

### Implantações

- Configure a implantação contínua. Consulte [Usando Git para implantar aplicativos Web no Serviço de Aplicativo do Azure]
- Slots de implantação. Veja [Implantar em ambientes de preparo para aplicativos Web no Serviço de Aplicativo do Azure].

Para exibir seus slots de implantação, clique em **Todas as Configurações** > **Slots de implantação**.

### Monitoramento

No modo Básico ou Padrão, você pode testar a disponibilidade dos pontos de extremidade HTTP ou HTTPs, de até três locais geograficamente distribuídos. Um teste de monitoramento falhará se o código de resposta HTTP for um erro (4xx ou 5xx) ou se a resposta demorar mais de 30 segundos. Um ponto de extremidade será considerado disponível caso os testes de monitoramento tenham êxito a partir de todos os locais especificados.

Para saber mais, consulte [Como monitorar o status de pontos de extremidade da Web].

>[AZURE.NOTE] Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar o Serviço de Aplicativo], em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## Próximas etapas

- [Configurar um nome de domínio personalizado no Serviço de Aplicativo do Azure]
- [Habilitar HTTPS para um aplicativo no Serviço de Aplicativo do Azure]
- [Dimensionar um aplicativo Web no Serviço de Aplicativo do Azure]
- [Conceitos básicos de monitoramento para aplicativos Web no Serviço de Aplicativo do Azure]

<!-- URL List -->

[ASP.NET SignalR]: http://www.asp.net/signalr
[Portal do Azure]: https://portal.azure.com/
[Configurar um nome de domínio personalizado no Serviço de Aplicativo do Azure]: ./web-sites-custom-domain-name.md
[Implantar em ambientes de preparo para aplicativos Web no Serviço de Aplicativo do Azure]: ./web-sites-staged-publishing.md
[Habilitar HTTPS para um aplicativo no Serviço de Aplicativo do Azure]: ./web-sites-configure-ssl-certificate.md
[Como monitorar o status de pontos de extremidade da Web]: http://go.microsoft.com/fwLink/?LinkID=279906
[Conceitos básicos de monitoramento para aplicativos Web no Serviço de Aplicativo do Azure]: ./web-sites-monitor.md
[modo de pipeline]: http://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Dimensionar um aplicativo Web no Serviço de Aplicativo do Azure]: ./web-sites-scale.md
[socket.io]: ./web-sites-nodejs-chat-app-socketio.md
[Experimentar o Serviço de Aplicativo]: http://go.microsoft.com/fwlink/?LinkId=523751

<!-- IMG List -->

[configure01]: ./media/web-sites-configure/configure01.png
[configure02]: ./media/web-sites-configure/configure02.png
[configure03]: ./media/web-sites-configure/configure03.png

<!---HONumber=AcomDC_0817_2016-->