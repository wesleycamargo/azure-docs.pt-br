---
title: Configurar aplicativos Web – Serviço de Aplicativo do Azure
description: Como configurar um aplicativo Web nos Serviços de Aplicativos do Azure
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 4286aa9cbaf07743c1d420fb1f5caace91bab7ee
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53269423"
---
# <a name="configure-web-apps-in-azure-app-service"></a>Configurar aplicativos Web no Serviço de Aplicativo do Azure

Este tópico explica como configurar um aplicativo Web usando o [Portal do Azure].

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="application-settings"></a>Configurações do aplicativo
1. No [Portal do Azure], abra a folha do aplicativo Web.
3. Clique em **Configurações do aplicativo**.

![Configurações do aplicativo][configure01]

A folha **Configurações do aplicativo** tem configurações agrupadas em várias categorias.

### <a name="general-settings"></a>Configurações gerais
**Versão do Framework**. Configurar essas opções se seu aplicativo usa qualquer um desses frameworks: 

* **.NET Framework**: Defina a versão do .NET framework. 
* **PHP**: defina a versão do PHP ou **DESLIGADO** para desabilitar o PHP. 
* **Java**: selecione a versão do Java ou **DESLIGADO** para desabilitar o Java. Use a opção **Contêiner da Web** para escolher entre as versões do Tomcat e Jetty.
* **Python**: selecione a versão do Python ou **DESLIGADO** para desabilitar o Python.

Por razões técnicas, a habilitação do Java para seu aplicativo desabilita as opções .NET, PHP e Python.

<a name="platform"></a>
**Plataforma**. Seleciona se o seu aplicativo é executado em ambiente de 32 ou 64 bits. O ambiente de 64 bits exige camada Básico ou Standard. A camada Gratuita e Compartilhada sempre é executada em um ambiente de 32 bits.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

**Web Sockets**. Defina a opção como **ATIVADO** para habilitar o protocolo WebSocket; por exemplo, se seu aplicativo Web usar [ASP.NET SignalR] ou [socket.io](https://socket.io/).

<a name="alwayson"></a>
**Sempre ativado**. Por padrão, os aplicativos Web serão descarregados se estiverem ociosos por um determinado período de tempo. Isso permite ao sistema conservar recursos. No modo Básico ou Padrão, você pode habilitar a opção **Sempre Ativado** para manter o aplicativo carregado o tempo todo. Se o aplicativo executar WebJobs contínuos ou executar WebJobs disparados com uma expressão CRON, você deverá habilitar a opção **Sempre ativo** ou os trabalhos Web poderão não ser executados de forma confiável.

**Versão do Pipeline Gerenciado**. Configurar o IIS [modo de pipeline]. Deixar este conjunto como Integrado (o padrão), a menos que você tenha um aplicativo herdado que exija uma versão anterior do IIS.

**Versão HTTP**. Defina como **2.0** para habilitar suporte para o protocolo [HTTPS/2](https://wikipedia.org/wiki/HTTP/2). 

> [!NOTE]
> A maioria dos navegadores modernos dá suporte para protocolo HTTP/2 somente em TLS, enquanto o tráfego não criptografado continua usando HTTP / 1.1. Para garantir que os navegadores do cliente conectam o aplicativo com HTTP/2, [compre um Certificado do Serviço de Aplicativo](web-sites-purchase-ssl-web-site.md) para o domínio personalizado do aplicativo, ou [associe um certificado de terceiros](app-service-web-tutorial-custom-ssl.md).

**Afinidade ARR**. Em um aplicativo escalado horizontalmente para várias instâncias de VM, os cookies Afinidade ARR garantem que o cliente seja roteado para a mesma instância durante a vida útil da sessão. Para melhorar o desempenho de aplicativos sem estado, defina essa opção para **Off**.   

**Troca Automática**. Se você habilitar a Troca Automática para um slot de implantação, o Serviço de Aplicativo alternará automaticamente o aplicativo Web em produção quando você enviar uma atualização por push para esse slot. Para saber mais, consulte [Implantar em slots de preparo para aplicativos Web no Serviço de Aplicativo do Azure](web-sites-staged-publishing.md).

### <a name="debugging"></a>Depurando
**Depuração Remota**. Habilita a depuração remota. Quando habilitado, você pode usar o depurador remoto no Visual Studio para conectar-se diretamente com seu aplicativo Web. A depuração remota permanecerá habilitada por 48 horas. 

### <a name="app-settings"></a>Configurações do aplicativo
Esta seção contém pares de nome/valor que seu aplicativo Web carregará na inicialização. 

* Para aplicativos .NET, essas configurações serão injetadas em sua configuração `AppSettings` em tempo de execução, substituindo as configurações existentes. 
* Para o Serviço de Aplicativo do Azure no Linux ou no Aplicativo Web para Contêineres, se você tiver aninhado a estrutura de chave json em seu nome, como `ApplicationInsights:InstrumentationKey` você precisará ter `ApplicationInsights__InstrumentationKey` como nome da chave. Portanto, observe que quaisquer `:` devem ser substituídos por `__` (ou seja, um sublinhado duplo).
* Os aplicativos PHP, Python, Java e Nó podem acessar essas configurações como variáveis do ambiente em tempo de execução. Para cada configuração do aplicativo, são criadas duas variáveis; uma com o nome especificado pela entrada de configuração do aplicativo, e outra com um prefixo de APPSETTING_. Ambas contêm o mesmo valor.

As configurações do aplicativo são sempre criptografadas quando armazenadas (criptografadas em repouso).

As configurações do aplicativo podem ser resolvidas do Key Vault usando [Referências de Key Vault](app-service-key-vault-references.md).

### <a name="connection-strings"></a>Cadeias de conexão
Cadeia de conexão para recursos vinculados. 

Para aplicativos .NET, essas cadeias de conexão serão injetadas em suas definições do `connectionStrings` das configurações do .NET no tempo de execução, substituindo as entradas existentes cujas chaves são iguais ao nome do banco de dados vinculado. 

Para aplicativos PHP, Python, Java e Nó essas configurações estarão disponíveis como variáveis de ambiente em tempo de execução, antecedidas com o tipo de conexão. Os prefixos das variáveis de ambiente são os seguintes: 

* SQL Server: `SQLCONNSTR_`
* MySQL: `MYSQLCONNSTR_`
* Banco de Dados SQL: `SQLAZURECONNSTR_`
* Personalizado: `CUSTOMCONNSTR_`

Por exemplo, se uma cadeia de conexão MySql fosse nomeado `connectionstring1`, ela seria acessada pela variável de ambiente `MYSQLCONNSTR_connectionString1`.

As cadeias de conexão são sempre criptografadas quando armazenadas (criptografadas em repouso).

As cadeias de conexão podem ser resolvidas a partir do Key Vault usando as [Referências de Key Vault](app-service-key-vault-references.md).

### <a name="default-documents"></a>Documentos padrão
O documento padrão é a página da Web exibida na URL raiz de um site.  O primeiro arquivo correspondente na lista é usado. 

Os aplicativos Web podem utilizar módulos que rotearão na URL, em vez de atender ao conteúdo estático, nesse caso, não há um documento padrão como tal.    

### <a name="handler-mappings"></a>Mapeamentos de manipulador
Use essa área para adicionar processadores de script personalizados para manipular solicitações de extensões de arquivo específicas. 

* **Extensão**. A extensão do arquivo a ser manipulada, como *.php ou handler.fcgi. 
* **Caminho do Processador de script**. O caminho absoluto do processador de script. As solicitações para arquivos que correspondam a extensão do arquivo serão processadas pelo processador de script. Use o caminho `D:\home\site\wwwroot` para se referir ao diretório raiz do seu aplicativo.
* **Argumentos adicionais**. Argumentos da linha de comando opcionais para o processador de script 

### <a name="virtual-applications-and-directories"></a>Aplicativos e diretórios virtuais
Para configurar diretórios e aplicativos virtuais, especifique cada diretório virtual e seu caminho físico correspondente com relação à raiz do site. Opcionalmente, você pode marcar a caixa de seleção **Aplicativo** para marcar um diretório virtual como um aplicativo.

## <a name="enabling-diagnostic-logs"></a>Habilitar logs de diagnóstico
Para habilitar logs de diagnóstico:

1. Na folha do seu aplicativo Web, clique em **Todas as configurações**.
2. Clique em **Logs de diagnóstico**. 

Opções para gravar os logs de diagnóstico de um aplicativo web que ofereça suporte ao registro em log: 

* **Registro do Aplicativo**. Grava logs de aplicativos no sistema de arquivos. O registro em log dura por 12 horas. 

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

O nome de usuário completo do FTP é "app\nomedousuário", onde *app* é o nome do seu aplicativo Web. O nome de usuário é listado no aplicativo Web, em **Essentials**.

![Credenciais de implantação FTP][configure02]

## <a name="other-configuration-tasks"></a>Outras tarefas de configuração
### <a name="ssl"></a>SSL
no modo Básico ou Padrão, você pode carregar certificados SSL para domínios personalizados. Para saber mais, confira [Habilitar HTTPS para um aplicativo Web](app-service-web-tutorial-custom-ssl.md). 

Para exibir os certificados carregados, clique em **Todas as Configurações** > **Domínios personalizados e SSL**.

### <a name="domain-names"></a>Nomes de domínio
Adicione nomes de domínio personalizados para seu aplicativo Web. Para saber mais, confira [Configurar um nome de domínio personalizado para um aplicativo Web no Serviço de Aplicativo do Azure](app-service-web-tutorial-custom-domain.md).

Para exibir seus nomes de domínio, clique em **Todas as Configurações** > **Domínios personalizados e SSL**.

### <a name="deployments"></a>Implantações
* Configure a implantação contínua. Confira [Usando Git para implantar aplicativos Web no Serviço de Aplicativo do Azure](app-service-deploy-local-git.md).
* Slots de implantação. Veja [Implantar em ambientes de preparo para aplicativos Web no Serviço de Aplicativo do Azure].

Para exibir seus slots de implantação, clique em **Todas as Configurações** > **Slots de implantação**.

### <a name="monitoring"></a>Monitoramento
No modo Básico ou Standard, você pode testar a disponibilidade dos pontos de extremidade HTTP ou HTTPS, de até três locais geograficamente distribuídos. Um teste de monitoramento falhará se o código de resposta HTTP for um erro (4xx ou 5xx) ou se a resposta demorar mais de 30 segundos. Um ponto de extremidade será considerado disponível caso os testes de monitoramento tenham êxito a partir de todos os locais especificados. 

Para obter mais informações, confira [Como monitorar o status de ponto de extremidade da Web].

> [!NOTE]
> Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar o Serviço de Aplicativo], em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.
> 
> 

## <a name="next-steps"></a>Próximas etapas
* [Configurar um nome de domínio personalizado no Serviço de Aplicativo do Azure]
* [Habilitar HTTPS para um aplicativo no Serviço de Aplicativo do Azure]
* [Dimensionar um aplicativo Web no Serviço de Aplicativo do Azure]
* [Conceitos básicos de monitoramento para aplicativos Web no Serviço de Aplicativo do Azure]

<!-- URL List -->

[ASP.NET SignalR]: https://www.asp.net/signalr
[Portal do Azure]: https://portal.azure.com/
[Configurar um nome de domínio personalizado no Serviço de Aplicativo do Azure]: ./app-service-web-tutorial-custom-domain.md
[Implantar em ambientes de preparo para aplicativos Web no Serviço de Aplicativo do Azure]: ./web-sites-staged-publishing.md
[Habilitar HTTPS para um aplicativo no Serviço de Aplicativo do Azure]: ./app-service-web-tutorial-custom-ssl.md
[Como monitorar o status de ponto de extremidade da Web]: https://go.microsoft.com/fwLink/?LinkID=279906
[Conceitos básicos de monitoramento para aplicativos Web no Serviço de Aplicativo do Azure]: ./web-sites-monitor.md
[modo de pipeline]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Dimensionar um aplicativo Web no Serviço de Aplicativo do Azure]: ./web-sites-scale.md
[Experimentar o Serviço de Aplicativo]: https://azure.microsoft.com/try/app-service/

<!-- IMG List -->

[configure01]: ./media/web-sites-configure/configure01.png
[configure02]: ./media/web-sites-configure/configure02.png
[configure03]: ./media/web-sites-configure/configure03.png
