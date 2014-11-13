<properties linkid="develop-python-cloud-services-with-ptvs" urlDisplayName="Python Web and Worker Roles with Python Tools 2.1 for Visual Studio" pageTitle="Fun&ccedil;&otilde;es da Web e de Trabalho do Python com o Python Tools 2.1 para Visual Studio" metaKeywords="Azure python, web role, worker role, PTVS, cloud service" description="Vis&atilde;o geral do uso do Python Tools para Visual Studio para criar servi&ccedil;os de nuvem do Azure, incluindo fun&ccedil;&otilde;es Web e de Trabalho." metaCanonical="" services="" documentationCenter="Python" title="Fun&ccedil;&otilde;es da Web e de Trabalho do Python com o Python Tools 2.1 para Visual Studio" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />

# Funções da Web e de Trabalho do Python com o Python Tools 2.1 para Visual Studio

Este guia oferece uma visão geral do uso das funções Web e de Trabalho do Python por meio do [Python Tools para Visual Studio][Python Tools para Visual Studio].

-   [Pré-requisitos][Pré-requisitos]
-   [O que são funções Web e de Trabalho do Python?][O que são funções Web e de Trabalho do Python?]
-   [Criação do projeto][Criação do projeto]
-   [Executar localmente][Executar localmente]
-   [Publicar no Azure][Publicar no Azure]
-   [Próximas etapas][Próximas etapas]

## <a name="prerequisites"></a>Pré-requisitos

-   Visual Studio 2012 ou 2013
-   [Python Tools 2,1 para Visual Studio][Python Tools 2,1 para Visual Studio]
-   [Azure SDK Tools para VS 2013][Azure SDK Tools para VS 2013] ou [Azure SDK Tools para VS 2012][Azure SDK Tools para VS 2012]
-   [Python 2.7 de 32 bits][Python 2.7 de 32 bits] ou [Python 3.4 de 32 bits][Python 3.4 de 32 bits]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## <a name="what-are-python-web-and-worker-roles"></a>O que são funções Web e de Trabalho do Python?

O Azure fornece três modelos de computação para aplicações em execução: [Sites do Azure][Sites do Azure], [Máquinas Virtuais do Azure][Máquinas Virtuais do Azure], e [Serviços de Nuvem do Azure][Serviços de Nuvem do Azure] Todos os três modelos oferecem suporte ao Python. Os Serviços de Nuvem, que incluem as funções Web e de trabalho, fornecem a *PaaS (plataforma como serviço)*. Dentro de um serviço de nuvem, uma função Web fornece um servidor web dos Serviços de Informações da Internet (IIS) dedicado para hospedar aplicativos web de front-end, enquanto uma função de trabalho pode executar tarefas assíncronas, de longa execução ou perpétuas independentes de interação com o usuário ou de entrada.

Para obter mais informações, consulte [O que é um Serviço de Nuvem?][O que é um Serviço de Nuvem?].

<div class="dev-callout"><strong>Procurando desenvolver um site simples?</strong>
<p>Se o seu cen&aacute;rio envolve apenas um site de front-end simples, considere usar um Website do Azure leve. Voc&ecirc; pode atualizar facilmente para um servi&ccedil;o de nuvem conforme o site cresce e suas necessidades mudam. Consulte o <a href="/pt-br/develop/python/">Centro de Desenvolvedores do Python</a> para obter artigos que abordam o desenvolvimento de Websites do Azure.</p>
</div>

## <a name="project-creation"></a>Criação do projeto

No Visual Studio, você pode escolher o **Serviço de Nuvem do Azure** na caixa de diálogo **Novo Projeto**, em **Python**.

![Caixa de diálogo Novo Projeto][Caixa de diálogo Novo Projeto]

No assistente do Serviço de Nuvem do Azure, você pode selecionar para criar novas funções Web e de Trabalho.

![Caixa de diálogo de serviço de nuvem do Azure][Caixa de diálogo de serviço de nuvem do Azure]

O modelo de função de trabalho vem com o código de texto clichê para se conectar a uma conta de armazenamento ou barramento de serviço do Azure.

![Solução de serviço de nuvem][Solução de serviço de nuvem]

Você pode adicionar funções Web ou de Trabalho a um serviço de nuvem existente a qualquer momento. Você pode escolher adicionar projetos existentes em sua solução ou criar projetos novos.

![Adicionar comando de função][Adicionar comando de função]

Seu serviço de nuvem pode conter funções implementadas em diferentes idiomas. Por exemplo, uma função Web do Python pode ser implementada usando o Django, com funções de trabalho do Python e C#. Você pode se comunicar facilmente entre as funções usando filas do barramento de serviço ou filas de armazenamento.

## <a name="run-locally"></a>Executar localmente

Se você configurar seu projeto de serviço de nuvem como o projeto de inicialização e pressionar F5, o serviço de nuvem será executado no emulador do Azure local.

Embora o PTVS ofereça suporte à inicialização no emulador, a depuração (pontos de interrupção, etc.) não funcionará.

Para depurar suas funções Web e de Trabalho, você pode configurar o projeto de funções como o projeto de inicialização e depurá-lo. Você também pode definir vários projetos de inicialização. Clique com o botão direito do mouse na solução e selecione **Definir projetos de inicialização**.

![Propriedades do projeto de inicialização da solução][Propriedades do projeto de inicialização da solução]

## <a name="publish-to-azure"></a>Publicar no Azure

Para publicar, clique com o botão direito do mouse no projeto de serviço de nuvem na solução e selecione **Publicar**.

![Conexão de publicação no Microsoft Azure][Conexão de publicação no Microsoft Azure]

Na página de configurações, selecione o serviço em nuvem no qual deseja publicar.

![Configurações de publicação do Microsoft Azure][Configurações de publicação do Microsoft Azure]

Você pode criar um novo serviço de nuvem se ainda não tiver um disponível.

![Criar caixa de diálogo de serviço de nuvem][Criar caixa de diálogo de serviço de nuvem]

Também é útil habilitar as conexões à área de trabalho remota para a(s) máquina(s) para falhas de depuração.

![Caixa de diálogo de configuração da área de trabalho remota][Caixa de diálogo de configuração da área de trabalho remota]

Quando tiver concluído os ajustes de configurações, clique em **Publicar**.

Algum progresso aparecerá na janela de saída e, então, você verá a janela Log de atividade do Microsoft Azure.

![Janela Log de atividade do Microsoft Azure][Janela Log de atividade do Microsoft Azure]

A implantação levará alguns minutos para ser concluída; em seguida, suas funções Web e/ou de Trabalho serão executadas no Azure!

## <a name="next-steps"></a>Próximas etapas

Para obter informações mais detalhadas sobre como usar funções Web e de Trabalho no Python Tools para Visual Studio, veja a documentação do PTVS:

-   [Projetos de serviços de nuvem][Projetos de serviços de nuvem]

Para obter mais detalhes sobre o uso dos serviços do Azure por meio das funções Web e de Trabalho, como o uso do armazenamento ou barramento de serviço do Azure, veja os guias a seguir:

-   [Serviço Blob][Serviço Blob]
-   [Serviço Tabela][Serviço Tabela]
-   [Serviço Fila][Serviço Fila]
-   [Filas do Service Bus][Filas do Service Bus]
-   [Tópicos do Service Bus][Tópicos do Service Bus]

<!--Link references--> <!--External Link references-->

  [Python Tools para Visual Studio]: http://pytools.codeplex.com
  [Pré-requisitos]: #prerequisites
  [O que são funções Web e de Trabalho do Python?]: #what-are-python-web-and-worker-roles
  [Criação do projeto]: #project-creation
  [Executar localmente]: #run-locally
  [Publicar no Azure]: #publish-to-azure
  [Próximas etapas]: #next-steps
  [Python Tools 2,1 para Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
  [Azure SDK Tools para VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
  [Azure SDK Tools para VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
  [Python 2.7 de 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517190
  [Python 3.4 de 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517191
  [Sites do Azure]: /pt-br/documentation/articles/fundamentals-application-models/#WebSites
  [Máquinas Virtuais do Azure]: /pt-br/documentation/articles/fundamentals-application-models/#VMachine
  [Serviços de Nuvem do Azure]: /pt-br/documentation/articles/fundamentals-application-models/#CloudServices
  [O que é um Serviço de Nuvem?]: /pt-br/manage/services/cloud-services/what-is-a-cloud-service/
  [Caixa de diálogo Novo Projeto]: ./media/cloud-services-python-ptvs/new-project-cloud-service.png
  [Caixa de diálogo de serviço de nuvem do Azure]: ./media/cloud-services-python-ptvs/new-service-wizard.png
  [Solução de serviço de nuvem]: ./media/cloud-services-python-ptvs/worker.png
  [Adicionar comando de função]: ./media/cloud-services-python-ptvs/add-new-or-existing-role.png
  [Propriedades do projeto de inicialização da solução]: ./media/cloud-services-python-ptvs/startup.png
  [Conexão de publicação no Microsoft Azure]: ./media/cloud-services-python-ptvs/publish-sign-in.png
  [Configurações de publicação do Microsoft Azure]: ./media/cloud-services-python-ptvs/publish-settings.png
  [Criar caixa de diálogo de serviço de nuvem]: ./media/cloud-services-python-ptvs/publish-create-cloud-service.png
  [Caixa de diálogo de configuração da área de trabalho remota]: ./media/cloud-services-python-ptvs/publish-remote-desktop-configuration.png
  [Janela Log de atividade do Microsoft Azure]: ./media/cloud-services-python-ptvs/publish-activity-log.png
  [Projetos de serviços de nuvem]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
  [Serviço Blob]: /pt-br/documentation/articles/storage-python-how-to-use-blob-storage/
  [Serviço Tabela]: /pt-br/documentation/articles/storage-python-how-to-use-table-storage/
  [Serviço Fila]: /pt-br/documentation/articles/storage-python-how-to-use-queue-storage/
  [Filas do Service Bus]: /pt-br/documentation/articles/service-bus-python-how-to-use-queues/
  [Tópicos do Service Bus]: /pt-br/documentation/articles/service-bus-python-how-to-use-topics-subscriptions/
