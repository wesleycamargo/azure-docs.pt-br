<properties 
	pageTitle="Funções Web e de Trabalho do Python com o Python Tools 2.1 para Visual Studio" 
	description="Visão geral do uso do Python Tools para Visual Studio para criar serviços de nuvem do Azure, incluindo funções Web e de Trabalho." 
	services="" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="02/09/2015" 
	ms.author="huvalo"/>




# Funções Web e de Trabalho do Python com o Python Tools 2.1 para Visual Studio

Este guia oferece uma visão geral do uso das funções Web e de trabalho do Python por meio do [Python Tools para Visual Studio][].

## Pré-requisitos

 - Visual Studio 2012 ou 2013
 - [Python Tools 2.1 para Visual Studio][]
 - [Ferramentas do SDK do Azure para VS 2013][] ou [Ferramentas do SDK do Azure para VS 2012][]
 - [Python 2.7 de 32 bits][] ou [Python 3.4 de 32 bits][]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## O que são funções Web e de trabalho do Python?

O Azure fornece três modelos de computação para execução de aplicativos: [Os][sites da Web do modelo de execução] dos sites da Web do Azure, [máquinas virtuais do Azure][execução do modelo vms], e [serviços de nuvem do Azure][serviços de nuvem do modelo de execução]. Todos os três modelos dão suporte ao Python. Os serviços de nuvem, que incluem funções da Web e de trabalho, fornecem *Platform as a Service (PaaS)*. Dentro de um serviço de nuvem, uma função web fornece um servidor Web dos Serviços de Informações da Internet (IIS) dedicado para hospedar aplicativos web de front-end, enquanto uma função de trabalho pode executar tarefas assíncronas, de longa execução ou perpétuas independentes de interação com o usuário ou de entrada.

Para obter mais informações, consulte [O que é um Serviço de Nuvem?].

> [AZURE.NOTE] **Procurando desenvolver um site simples?**
Se o seu cenário envolve apenas um site de front-end simples, considere usar um Site do Azure leve. Você pode atualizar facilmente para um Serviço de Nuvem conforme o site cresce e suas necessidades mudam. Consulte no <a href="/pt-br/develop/python/">Python Developer Center</a> artigos que tratam do desenvolvimento de Sites do Azure.
<br />


## Criação do projeto

No Visual Studio, você pode escolher o **Serviço de Nuvem do Azure** na caixa de diálogo **Novo Projeto**, em **Python**. 

![New Project Dialog](./media/cloud-services-python-ptvs/new-project-cloud-service.png)

No assistente do Serviço de Nuvem do Azure, você pode selecionar para criar novas funções Web e de trabalho.

![Azure Cloud Service Dialog](./media/cloud-services-python-ptvs/new-service-wizard.png)

O modelo de função de trabalho vem com o código de texto clichê para se conectar a uma conta de armazenamento ou barramento de serviço do Azure.

![Cloud Service Solution](./media/cloud-services-python-ptvs/worker.png)

Você pode adicionar funções Web ou de trabalho a um serviço de nuvem existente a qualquer momento.  Você pode optar por adicionar projetos existentes `a sua solução ou criar projetos novos. 

![Add Role Command](./media/cloud-services-python-ptvs/add-new-or-existing-role.png)

Seu serviço de nuvem pode conter funções implementadas em diferentes idiomas.  Por exemplo, uma função web do Python pode ser implementada usando o Django, com funções de trabalho do Python e C#.  Você pode se comunicar facilmente entre as funções usando filas do barramento de serviço ou filas de armazenamento.

## Executar localmente

Se você configurar seu projeto de serviço de nuvem como o projeto de inicialização e pressionar F5, o serviço de nuvem será executado no emulador do Azure local.

Embora o PTVS ofereça suporte à inicialização no emulador, a depuração (pontos de interrupção, etc.) não funcionará.

Para depurar suas funções Web e de trabalho, você pode configurar o projeto de funções como o projeto de inicialização e depurá-lo.  Você também pode definir vários projetos de inicialização.  Clique com o botão direito do mouse na solução e selecione **Definir projetos de inicialização**.

![Solution Startup Project Properties](./media/cloud-services-python-ptvs/startup.png)

## Publicar no Azure

Para publicar, clique com o botão direito do mouse no projeto de serviço de nuvem na solução e selecione **Publicar**.

![Microsoft Azure Publish Sign In](./media/cloud-services-python-ptvs/publish-sign-in.png)

Na página de configurações, selecione o serviço de nuvem no qual deseja publicar.

![Microsoft Azure Publish Settings](./media/cloud-services-python-ptvs/publish-settings.png)

Você pode criar um novo serviço de nuvem se ainda não tiver um disponível.

![Create Cloud Service Dialog](./media/cloud-services-python-ptvs/publish-create-cloud-service.png)

Também é útil habilitar as conexões à área de trabalho remota para o(s) computador(es) para falhas de depuração.

![Remote Desktop Configuration Dialog](./media/cloud-services-python-ptvs/publish-remote-desktop-configuration.png)

Quando tiver concluído os ajustes de configurações, clique em **Publicar**.

Algum progresso aparecerá na janela de saída e, então, você verá a janela Log de atividade do Microsoft Azure.

![Microsoft Azure Activity Log Window](./media/cloud-services-python-ptvs/publish-activity-log.png)

A implantação levará alguns minutos para ser concluída; em seguida, suas funções Web e/ou de Trabalho serão executadas no Azure!

## Próximas etapas

Para obter informações mais detalhadas sobre como usar funções Web e de trabalho no Python Tools para Visual Studio, consulte a documentação do PTVS:

- [Projetos de serviço de nuvem][]

Para obter mais detalhes sobre o uso dos serviços do Azure por meio das funções Web e de trabalho, como o uso do armazenamento ou barramento de serviço do Azure, veja os guias a seguir:
 
- [Serviço Blob][]
- [Serviço Tabela][]
- [Serviço Fila][]
- [Filas de barramento de serviço][]
- [Tópicos de barramento de serviço][]


<!--Link references-->

[O que é um Serviço de Nuvem?]: /pt-br/manage/services/cloud-services/what-is-a-cloud-service/
[sites da Web do modelo de execução]: ../fundamentals-application-models/#WebSites
[execução do modelo vms]: ../fundamentals-application-models/#VMachine
[serviços de nuvem do modelo de execução]: ../fundamentals-application-models/#CloudServices
[Python Developer Center]: /pt-br/develop/python/

[Serviço Blob]: ../storage-python-how-to-use-blob-storage/
[Serviço Fila]: ../storage-python-how-to-use-queue-storage/
[Serviço Tabela]: ../storage-python-how-to-use-table-storage/
[Filas de barramento de serviço]: ../service-bus-python-how-to-use-queues/
[Tópicos de barramento de serviço]: ../service-bus-python-how-to-use-topics-subscriptions/


<!--External Link references-->

[Python Tools para Visual Studio]: http://aka.ms/ptvs
[Documentação do Python Tools para Visual Studio]: http://pytools.codeplex.com/documentation 
[Projetos de serviço de nuvem]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project

[Python Tools 2.1 para Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Ferramentas do SDK do Azure para VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Ferramentas do SDK do Azure para VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 de 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python 3.4 de 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517191

<!--HONumber=45--> 
