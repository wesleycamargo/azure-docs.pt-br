<properties 
	pageTitle="Recursos recomendados de Trabalhos Web do Azure" 
	description="Recursos recomendados para aprender a usar os WebJobs do Azure e o SDK WebJobs do Azure." 
	services="web-sites, storage" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/27/2014" 
	ms.author="tdykstra"/>

#Recursos recomendados de Trabalhos Web do Azure

Este tópico fornece links para recursos de documentação sobre como usar WebJobs do Azure e o SDK de WebJobs do Azure. Os WebJobs do Azure fornecem uma maneira fácil de executar scripts ou programas como processos em segundo plano em sites do Azure. Você pode carregar e executar um arquivo executável, como cmd, bat, exe (.NET), ps1, sh, php, py, js e jar. Esses programas são executados como WebJobs em uma agenda (cron) ou continuamente.

O SDK de WebJobs torna mais fácil de usar o armazenamento do Azure. O SDK de WebJobs tem uma um sistema de disparo e associação que funciona com o os blobs de armazenamento do Microsoft Azure, filas e tabelas, bem como filas do barramento de serviço.

Criar, implantar e gerenciar WebJobs é fácil com ferramentas integradas no Visual Studio. Você pode criar WebJobs de modelos, publicar e gerenciá-los (executar/parar/monitor/depurar). 

O painel de WebJobs no portal de gerenciamento do Azure fornece recursos de gerenciamento poderosos que lhe dão controle total sobre a execução de WebJobs, incluindo a capacidade de invocar funções individuais dentro de WebJobs. O painel também exibe a saída de log e tempos de execução de função. 

## Sumário

* [Guia de introdução dos WebJobs](#getstarted)
* [Implantação dos WebJobs](#deploy)
* [Testando e depurando os WebJobs](#debug)
* [Recursos adicionais de WebJobs](#additional)
* [Recursos adicionais do SDK dos WebJobs](#additionalsdk)
* [Aplicativos de WebJobs de exemplo](#samples)
* [Blogs](#blogs) 
* [Obtendo ajuda com WebJobs](#gethelp)

##<a name="getstarted"></a>Guia de introdução dos WebJobs

* [Introdução aos WebJobs do Azure](http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx)
* [Recursos de WebJobs do Azure](/blog/2014/10/22/webjobs-goes-into-full-production/)
* [O que é o SDK de WebJobs do Azure](../websites-dotnet-webjobs-sdk/)
* [Anunciando o RTM 1.0.0 do SDK de WebJobs do Microsoft Azure](/blog/2014/10/25/announcing-the-1-0-0-rtm-of-microsoft-azure-webjobs-sdk/)
* [Introdução ao SDK de Trabalhos Web do Azure](../websites-dotnet-webjobs-sdk-get-started/)
* [Como usar o armazenamento de fila do Azure com o SDK do WebJobs](../websites-dotnet-webjobs-sdk-storage-queues-how-to)
* [Como usar o armazenamento de blobs do Azure com o SDK do WebJobs](../websites-dotnet-webjobs-sdk-storage-blobs-how-to)
* [Como usar o armazenamento de tabela do Azure com o SDK do WebJobs](../websites-dotnet-webjobs-sdk-storage-tables-how-to)
* [Como usar o barramento de serviço do Azure com o SDK do WebJobs](../websites-dotnet-webjobs-sdk-service-bus)
* [Referência rápida do SDK do WebJobs do Azure (download do PDF)](http://go.microsoft.com/fwlink/?LinkID=524028&clcid=0x409)
* Vídeos
	* [WebJobs e SDK de WebJobs](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-153-WebJobs-with-Pranav-Rastogi?utm_source=dlvr.it&utm_medium=twitter)
	* [Série de vídeos de WebJobs do Azure no Channel 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)
	* [Apresentando as ferramentas de WebJobs do Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
	* [Ferramentas de WebJobs e depuração remota](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster)

Consulte também as seguintes seções em [Implantação de WebJobs](#deploy) e [teste e depuração de WebJobs](#debug).

##<a name="deploy"></a>Implantação dos WebJobs

* [Como implantar Trabalhos Web do Azure em Sites do Azure](../websites-dotnet-deploy-webjobs/)
* [Como implantar WebJobs usando o Portal de Gerenciamento do Azure](../web-sites-create-web-jobs/)
* [Ativar entrega de linha de comando ou contínua dos WebJobs do Azure](http://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)
* [Implantando por Git um aplicativo de console .NET no Azure usando WebJobs](http://blog.amitapple.com/post/73574681678/git-deploy-console-app/) 
* Vídeos
	* [Apresentando as ferramentas de WebJobs do Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
	* [Ferramentas de WebJobs e depuração remota](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="debug"></a>Testando e depurando os WebJobs

* [Novo desenvolvedor e recursos de depuração para WebJobs do Azure no Visual Studio](http://blogs.msdn.com/b/webdev/archive/2014/11/12/new-developer-and-debugging-features-for-azure-webjobs-in-visual-studio.aspx)
* [Solucionando problemas de sites do Azure no Visual Studio](../web-sites-dotnet-troubleshoot-visual-studio/)
* [Quem criou esse blob?](http://blogs.msdn.com/b/jmstall/archive/2014/02/19/who-wrote-that-blob.aspx) 
* [Código de hospedagem interativo na nuvem](http://blogs.msdn.com/b/jmstall/archive/2014/04/26/hosting-interactive-code-in-the-cloud.aspx)
* [Obtendo um painel para o desenvolvimento local com o SDK de Trabalhos Web](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx)
* [Adição de rastreamento para sites do Azure e WebJobs](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)
* [Monitoramento, diagnóstico e solução de problemas de Armazenamento do Microsoft Azure](../storage-monitoring-diagnosing-troubleshooting/)
* Vídeos
	* [Ferramentas de WebJobs e depuração remota](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="additional"></a>Recursos adicionais de WebJobs

* [Postagem no blog WebJobs GA do Azure de Magnus Mårtensson](http://magnusmartensson.com/azure-webjobs-ga)
* [Documentação de configurações de WebJobs no GitHub](https://github.com/projectkudu/kudu/wiki/Web-jobs).
* [Executando trabalhos Web do Powershell em sites do Azure](http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx)
* [Seja notificado quando os WebJobs acionados pelo Azure forem concluídos](http://blog.amitapple.com/post/2014/03/webjobs-notification/)
* [Backups de sites do Azure: Arquitetura de aplicativos da Web prontos para empresa de grande escala](https://channel9.msdn.com/Events/Build/2014/3-626). Aborda o dimensionamento dos sites do Azure com WebJobs, incluindo o SDK de WebJobs.
* [Política de retenção de backup de site simples com WebJobs](http://azure.microsoft.com/blog/2014/04/28/simple-web-site-backup-retention-policy-with-webjobs/)
* [Sites do Microsoft Azure e serviços de nuvem lentos na primeira solicitação](http://wp.sjkp.dk/windows-azure-websites-and-cloud-services-slow-on-first-request/). Mostra como usar WebJobs para simular o recurso AlwaysOn que só está disponível para a camada de sites padrão.
* [Desligamento normal dos WebJobs](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.U72Il_5OWUl). Desligamento normal para o SDK de WebJobs, consulte [Desligamento normal](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#graceful).)
* [Dimensionamento de seu aplicativo Web com sites do Azure](http://msdn.microsoft.com/pt-br/magazine/dn786914.aspx)
* Vídeos
	* [Expansão de WebJobs](http://channel9.msdn.com/Shows/Azure-Friday/Azure-WebJobs-105-Scaling-out-Web-Jobs)
	* [Vídeos de WebJobs do Azure, de Magnus Mårtensson](https://www.youtube.com/playlist?list=PLqp1ZOYYUSd81yEzMYLTw8cz91wx_LU9r)
	* [Série de vídeos de WebJobs do Azure no Channel 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="additionalsdk"></a>Recursos adicionais do SDK dos WebJobs

* [Parâmetros disparadores, de associações e de rota em AzureJobs](http://blogs.msdn.com/b/jmstall/archive/2014/01/28/trigger-bindings-and-route-parameters-in-azurejobs.aspx) 
* Associações do armazenamento do Azure
	* [Blobs](http://blogs.msdn.com/b/jmstall/archive/2014/02/18/azure-storage-bindings-part-1-blobs.aspx)
	* [Filas](http://blogs.msdn.com/b/jmstall/archive/2014/02/18/azure-storage-bindings-part-2-queues.aspx)
	* [Tabelas](http://blogs.msdn.com/b/jmstall/archive/2014/03/06/azure-storage-bindings-part-3-tables.aspx)
* [Como funciona o [BlobTrigger]?](http://blogs.msdn.com/b/jmstall/archive/2014/04/17/how-does-blobinput-work.aspx)
* [Associações avançadas com o SDK de trabalhos Web do Azure](http://victorhurdugaci.com/advanced-bindings-with-the-windows-azure-web-jobs-sdk/)
* [WebJob para carregar arquivos FREB no armazenamento do Azure usando o SDK de WebJobs](http://thenextdoorgeek.com/post/WAWS-WebJob-to-upload-FREB-files-to-Azure-Storage-using-the-WebJobs-SDK)
* [Hospedando webjobs do Azure fora do Azure com os benefícios de log do webjob hospedado no Azure](http://bypassion.dk/?p=510)
* [Criando uma ferramenta de importação de dados com WebJobs do Azure](http://www.freshconsulting.com/building-data-import-tool-azure-webjobs/)
* Vídeos
	* [Série de vídeos de WebJobs do Azure no Channel 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="samples"></a>Aplicativos de WebJob de exemplo

* [Aplicativos de exemplo fornecidos pela equipe de WebJobs no GitHub](https://github.com/azure/azure-webjobs-sdk-samples)
* [Site do Azure simples com back-end de WebJobs usando o SDK de WebJobs](http://code.msdn.microsoft.com/Simple-Azure-Website-with-b4391eeb)
* [SiteMonitR](http://code.msdn.microsoft.com/SiteMonitR-dd4fcf77). Demonstra o uso de WebJobs agendados e controlados por evento. Confira a postagem [Recriando o SiteMonitR usando o SDK de WebJobs do Azure](http://www.bradygaster.com/post/rebuilding-the-sitemonitr-using-windows-azure-webjobs).

##<a name="blogs"></a>Blogs

* [Blog do Azure](/blog)
* [Blog de Amit da Apple](http://blog.amitapple.com/). Foco em WebJobs (não o SDK).
* [Blog de Magnus Mårtensson](http://magnusmartensson.com/)

##<a name="gethelp"></a>Obtendo ajuda com WebJobs

* [StackOverflow para WebJobs](http://stackoverflow.com/questions/tagged/azure-webjobs)
* [StackOverflow para o SDK de WebJobs](http://stackoverflow.com/questions/tagged/azure-webjobssdk)
* [Fórum do Azure e ASP.NET](http://forums.asp.net/1247.aspx)
* [Fórum de sites do Azure](http://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=windowsazurewebsitespreview)
* [Site de voz do usuário de sites do Azure](http://feedback.azure.com/forums/169385-websites)
* [Twitter](http://twitter.com/). Use a hashtag #AzureWebJobs.


<!--HONumber=42-->
