---
title: "Recursos de documentação do Azure Webjobs"
description: Recursos recomendados para aprender a usar os WebJobs do Azure e o SDK WebJobs do Azure.
services: app-service
documentationcenter: .net
author: tdykstra
manager: erikre
editor: jimbe
ms.assetid: ed005e56-4334-4641-a5e5-15435c2be36b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/28/2016
ms.author: tdykstra
translationtype: Human Translation
ms.sourcegitcommit: 80627139ee253bb6a01241e460781519dac69e71
ms.openlocfilehash: a8541d5bd5e5becb25e8460b6ca774ec6637563a
ms.lasthandoff: 01/31/2017


---
# <a name="azure-webjobs-documentation-resources"></a>Recursos de documentação do Azure Webjobs
## <a name="overview"></a>Visão geral
Este tópico fornece links para recursos de documentação sobre como usar WebJobs do Azure e o SDK de WebJobs do Azure. O Azure WebJobs fornece uma maneira fácil de executar scripts ou programas como processos em segundo plano no contexto de um [Aplicativo Web do Serviço de Aplicativo, aplicativo de API ou aplicativo móvel](../app-service/app-service-value-prop-what-is.md). Você pode carregar e executar um arquivo executável, como cmd, bat, exe (.NET), ps1, sh, php, py, js e jar. Esses programas são executados como WebJobs em uma agenda (cron) ou continuamente.

A finalidade do [SDK do WebJobs](websites-webjobs-resources.md) é simplificar o código escrito para tarefas comuns que um WebJob pode executar, como o processamento de imagens, o processamento de filas, a agregação de RSS, a manutenção de arquivos e o envio de emails. O SDK do WebJobs tem recursos internos para trabalhar com o Armazenamento do Azure e o Barramento de Serviço, para o agendamento de tarefas e o tratamento de erros e de muitos outros cenários comuns. Além disso, ele foi projetado para ser extensível e há um [repositório de software livre para extensões](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview). [Azure Functions](../azure-functions/functions-overview.md) (atualmente em preview) se baseia em uma versão do SDK do WebJobs que funciona com C# script, Node.js e outras linguagens. 

Criar, implantar e gerenciar WebJobs é fácil com ferramentas integradas no Visual Studio. Você pode criar WebJobs de modelos, publicar e gerenciá-los (executar/parar/monitor/depurar). 

O painel de Trabalhos Web no portal de gerenciamento do Azure fornece recursos de gerenciamento poderosos que lhe dão controle total sobre a execução de Trabalhos Web, incluindo a capacidade de invocar funções individuais dentro de Trabalhos Web. O painel também exibe a saída de log e tempos de execução de função. 

## <a name="a-namegetstartedagetting-started-with-webjobs-and-the-webjobs-sdk"></a><a name="getstarted"></a>Guia de Introdução a Trabalhos Web e ao SDK dos Trabalhos Web
* [Introdução aos Trabalhos Web do Azure](http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx)
* [O Azure Webjobs é impressionante e você deve começar a usá-lo agora mesmo!](http://www.troyhunt.com/2015/01/azure-webjobs-are-awesome-and-you.html) (Postagem no blog por Troy Hunt).
* [Recursos de Trabalhos Web do Azure](https://azure.microsoft.com/blog/2014/10/22/webjobs-goes-into-full-production/)
* [O que é o SDK de Trabalhos Web do Azure](websites-dotnet-webjobs-sdk.md)
* [Diretrizes de trabalhos em segundo plano pelos Padrões e Práticas da Microsoft](/documentation/articles/best-practices-background-jobs/)
* [Anunciando o RTM 1.1.0 do SDK de WebJobs do Microsoft Azure](https://azure.microsoft.com/blog/azure-webjobs-sdk-1-1-0-rtm/)
* [Introdução ao SDK de Trabalhos Web do Azure](websites-dotnet-webjobs-sdk-get-started.md)
* [Como usar o armazenamento de fila do Azure com o SDK de Trabalhos Web](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)
* [Como usar o armazenamento de blob do Azure com o SDK de Trabalhos Web](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)
* [Como usar o armazenamento de tabela do Azure com o SDK de Trabalhos Web](websites-dotnet-webjobs-sdk-storage-tables-how-to.md)
* [Como usar o barramento de serviço do Azure com o SDK de Trabalhos Web](websites-dotnet-webjobs-sdk-service-bus.md)
* [Guia de referência rápida do SDK de Trabalhos Web do Azure (download do PDF)](http://go.microsoft.com/fwlink/?LinkID=524028&clcid=0x409)
* [Documentação de configurações de Trabalhos Web no GitHub](https://github.com/projectkudu/kudu/wiki/Web-jobs).
* Vídeos
  * [WebJobs e SDK de Trabalhos Web](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-153-WebJobs-with-Pranav-Rastogi?utm_source=dlvr.it&utm_medium=twitter)
  * [Série de vídeos de Trabalhos Web do Azure no Channel 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)
  * [Apresentando as ferramentas de Trabalhos Web do Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
  * [Depuração remota e ferramentas de Trabalhos Web](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster)
  * [Atualização do Azure WebJobs com Pranav Rastogi - extensibilidade na versão 1.1](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-183-Azure-WebJobs-Update-with-Pranav-Rastogi)

Consulte também as seguintes seções sobre [Implantação de WebJobs](#deploy) e [Teste e depuração de WebJobs](#debug).

## <a name="a-namedeployadeploying-webjobs"></a><a name="deploy"></a>Implantando Trabalhos Web
* [Como implantar Trabalhos Web do Azure usando o Visual Studio.](websites-dotnet-deploy-webjobs.md)
* [Como implantar Trabalhos Web usando o Portal do Azure](web-sites-create-web-jobs.md)
* [Habilitando entrega de linha de comando ou contínua dos Trabalhos Web do Azure](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)
* [Implantando por Git um aplicativo de console .NET no Azure usando Trabalhos Web](http://blog.amitapple.com/post/73574681678/git-deploy-console-app/)
* [Implantando um WebJob F# para o Azure](http://blogs.msdn.com/b/dave_crooks_dev_blog/archive/2015/02/18/deploying-f-web-job-to-azure.aspx)
* [Implantando serviços personalizados como Azure WebJobs](http://withouttheloop.com/articles/2015-06-23-deploying-custom-services-as-azure-webjobs/)
* Vídeos
  * [Apresentando as ferramentas de Trabalhos Web do Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
  * [Depuração remota e ferramentas de Trabalhos Web](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

## <a name="a-namescheduleascheduling-webjobs"></a><a name="schedule"></a>Agendando Trabalhos Web
* [A caixa de diálogo Adicionar Trabalho Web do Azure](websites-dotnet-deploy-webjobs.md#configure)
* [Criar um Trabalho Web agendado no Portal do Azure](web-sites-create-web-jobs.md#CreateScheduled)
* [Vinculando um trabalho do agendador a um WebJob](http://blog.davidebbo.com/2015/05/scheduled-webjob.html)
* [Agendando Azure WebJobs com expressões cron](http://blog.amitapple.com/post/2015/06/scheduling-azure-webjobs/)
* [Agendamento de funções individuais do WebJob usando o TimerTrigger do SDK do WebJobs](websites-dotnet-webjobs-sdk.md#schedule)

## <a name="a-namedebugatesting-and-debugging-webjobs"></a><a name="debug"></a>Testando e depurando Trabalhos Web
* [Novos recursos de desenvolvedor e de depuração para Trabalhos Web do Azure no Visual Studio](http://blogs.msdn.com/b/webdev/archive/2014/11/12/new-developer-and-debugging-features-for-azure-webjobs-in-visual-studio.aspx)
* [Exibir o painel Trabalhos Web](websites-dotnet-webjobs-sdk-get-started.md#view-the-webjobs-sdk-dashboard)
* [Como gravar logs usando o SDK de Trabalhos Web e exibi-los no painel](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs)
* [Depuração remota de Trabalhos Web](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebugwj)
* [Quem criou esse blob?](http://blogs.msdn.com/b/jmstall/archive/2014/02/19/who-wrote-that-blob.aspx) 
* [Hospedando código interativo na nuvem](http://blogs.msdn.com/b/jmstall/archive/2014/04/26/hosting-interactive-code-in-the-cloud.aspx)
* [Adicionando rastreamento para sites do Azure e Trabalhos Web](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)
* [Monitoramento, diagnóstico e solução de problemas de Armazenamento do Microsoft Azure](../storage/storage-monitoring-diagnosing-troubleshooting.md)
* Vídeos
  * [Depuração remota e ferramentas de Trabalhos Web](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

## <a name="a-namescaleascaling-webjobs"></a><a name="scale"></a>Dimensionando de WebJobs
* [Dimensionando seu aplicativo Web com sites do Azure](http://msdn.microsoft.com/magazine/dn786914.aspx)
* [Serviço de aplicativo do Azure: Arquitetura de aplicativos Web prontos para empresa de grande escala](https://channel9.msdn.com/Events/Build/2014/3-626). Aborda o dimensionamento de aplicativos Web com Trabalhos Web, incluindo o SDK de Trabalhos Web.
* Vídeos
  * [Expandindo Trabalhos Web](http://channel9.msdn.com/Shows/Azure-Friday/Azure-WebJobs-105-Scaling-out-Web-Jobs)

## <a name="a-nameadditionalaadditional-webjobs-resources"></a><a name="additional"></a>Recursos adicionais de Trabalhos Web
* [Postagem no blog WebJobs GA do Azure de Magnus Mårtensson](http://magnusmartensson.com/azure-webjobs-ga)
* [Executando Trabalhos Web do Powershell no Serviço de Aplicativo do Azure](http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx)
* [Seja notificado quando os Trabalhos Web disparados pelo Azure forem concluídos](http://blog.amitapple.com/post/2014/03/webjobs-notification/)
* [Política de retenção de backup de site simples com Trabalhos Web](https://azure.microsoft.com/blog/2014/04/28/simple-web-site-backup-retention-policy-with-webjobs/)
* [Sites do Azure e Serviços de Nuvem lentos na primeira solicitação](http://wp.sjkp.dk/windows-azure-websites-and-cloud-services-slow-on-first-request/). Mostra como usar Trabalhos Web para simular o recurso AlwaysOn que só está disponível para a camada de preços Padrão.
* [Desligamento normal dos Trabalhos Web](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.U72Il_5OWUl). Para o desligamento normal do SDK de Trabalhos Web, consulte [Desligamento normal](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#graceful).)
* [Automatizando Backups com WebJobs do Azure e AzCopy](http://markjbrown.com/azure-webjobs-azcopy/)
* Vídeos
  * [Vídeos de Trabalhos Web do Azure, de Magnus Mårtensson](https://www.youtube.com/playlist?list=PLqp1ZOYYUSd81yEzMYLTw8cz91wx_LU9r)
  * [Série de vídeos de Trabalhos Web do Azure no Channel 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

## <a name="a-nameadditionalsdkaadditional-webjobs-sdk-resources"></a><a name="additionalsdk"></a>Recursos adicionais do SDK de Trabalhos Web
* [Notas de lançamento do SDK do WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki/Release-Notes)
* [Código-fonte do SDK de Trabalhos Web](https://github.com/Azure/azure-webjobs-sdk)
* [Código-fonte das extensões de SDK de WebJobs](https://github.com/Azure/azure-webjobs-sdk-extensions), com [guia detalhado do modelo de extensibilidade](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview).  
* [Código-fonte do script do SDK de WebJobs](https://github.com/Azure/azure-webjobs-sdk-script/) (usado para o [Azure Functions](../azure-functions/functions-overview.md))
* [Trabalho Web para carregar arquivos FREB no armazenamento do Azure usando o SDK de Trabalhos Web](http://thenextdoorgeek.com/post/WAWS-WebJob-to-upload-FREB-files-to-Azure-Storage-using-the-WebJobs-SDK)
* [Hospedando Trabalhos Web do Azure fora do Azure com os benefícios de log do Trabalho Web hospedado no Azure](http://bypassion.dk/?p=510)
* [Criando uma ferramenta de importação de dados com Trabalhos Web do Azure](http://www.freshconsulting.com/building-data-import-tool-azure-webjobs/)
* [Visão geral das Funções do Azure](../azure-functions/functions-overview.md)
* Vídeos
  * [Série de vídeos de Trabalhos Web do Azure no Channel 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

## <a name="a-namesamplesasample-webjob-applications"></a><a name="samples"></a>Aplicativos de Trabalhos Web de exemplo
* [Aplicativos de exemplo fornecidos pela equipe de Trabalhos Web no GitHub](https://github.com/azure/azure-webjobs-sdk-samples)
* [Site do Azure simples com back-end de Trabalhos Web usando o SDK de WebJobs](http://code.msdn.microsoft.com/Simple-Azure-Website-with-b4391eeb)
* [SiteMonitR](http://code.msdn.microsoft.com/SiteMonitR-dd4fcf77). Demonstra o uso de WebJobs agendados e controlados por evento. Confira a postagem de blog [Recriando o SiteMonitR usando o SDK de Trabalhos Web do Azure](http://www.bradygaster.com/post/rebuilding-the-sitemonitr-using-windows-azure-webjobs).

## <a name="a-nameblogsablogs"></a><a name="blogs"></a>Blogs
* [Blog do Azure](/blog)
* [Blog de Amit Apple](http://blog.amitapple.com/). Concentra-se em Trabalhos Web (não no SDK).
* [Blog de Magnus Mårtensson](http://magnusmartensson.com/)

## <a name="a-namegethelpagetting-help-with-webjobs"></a><a name="gethelp"></a>Obtendo ajuda com Trabalhos Web
* [StackOverflow para Trabalhos Web](http://stackoverflow.com/questions/tagged/azure-webjobs)
* [StackOverflow para o SDK de Trabalhos Web](http://stackoverflow.com/questions/tagged/azure-webjobssdk)
* [StackOverflow para Azure Functions](http://stackoverflow.com/questions/tagged/azure-functions)
* [Fórum do Azure e ASP.NET](http://forums.asp.net/1247.aspx)
* [Fórum de aplicativos Web do Serviço de Aplicativo do Azure](http://social.msdn.microsoft.com/Forums/azure/home?forum=windowsazurewebsitespreview)
* [Site de voz do usuário de Aplicativos Web do Azure](https://feedback.azure.com/forums/169385-websites/)
* [Twitter](http://twitter.com/). Use a hashtag #AzureWebJobs.
* [Relatar um problema ou bug do WebJobs](https://github.com/projectkudu/kudu/wiki/Reporting-WebJobs-issues)


