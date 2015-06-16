<properties 
	pageTitle="Recursos de Trabalhos Web do Azure" 
	description="Recursos recomendados para aprender a usar os WebJobs do Azure e o SDK WebJobs do Azure." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/03/2015" 
	ms.author="tdykstra"/>

# Recursos de Trabalhos Web do Azure

## Visão geral

Este tópico fornece links para recursos de documentação sobre como usar WebJobs do Azure e o SDK de WebJobs do Azure. Trabalhos Web do Azure fornece uma maneira fácil de executar scripts ou programas como processos em segundo plano em [Aplicativos Web do Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714). Você pode carregar e executar um arquivo executável, como cmd, bat, exe (.NET), ps1, sh, php, py, js e jar. Esses programas são executados como WebJobs em uma agenda (cron) ou continuamente.

O SDK de WebJobs torna mais fácil de usar o armazenamento do Azure. O SDK de WebJobs tem uma um sistema de disparo e associação que funciona com o os blobs de armazenamento do Microsoft Azure, filas e tabelas, bem como filas do barramento de serviço.

Criar, implantar e gerenciar WebJobs é fácil com ferramentas integradas no Visual Studio. Você pode criar WebJobs de modelos, publicar e gerenciá-los (executar/parar/monitor/depurar).

O painel de Trabalhos Web no portal de gerenciamento do Azure fornece recursos de gerenciamento poderosos que lhe dão controle total sobre a execução de Trabalhos Web, incluindo a capacidade de invocar funções individuais dentro de Trabalhos Web. O painel também exibe a saída de log e tempos de execução de função.

##<a name="getstarted"></a>Guia de Introdução a Trabalhos Web e ao SDK dos Trabalhos Web

* [Introdução aos Trabalhos Web do Azure](http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx)
* [Recursos de Trabalhos Web do Azure](/blog/2014/10/22/webjobs-goes-into-full-production/)
* [O que é o SDK de Trabalhos Web do Azure](websites-dotnet-webjobs-sdk.md)
* [Diretrizes de trabalhos em segundo plano pelos Padrões e Práticas da Microsoft](https://github.com/mspnp/azure-guidance/blob/master/Background-Jobs.md)
* [Anunciando o RTM 1.0.0 do SDK de Trabalhos Web do Microsoft Azure](/blog/2014/10/25/announcing-the-1-0-0-rtm-of-microsoft-azure-webjobs-sdk/)
* [Introdução ao SDK de Trabalhos Web do Azure](websites-dotnet-webjobs-sdk-get-started.md)
* [Como usar o armazenamento de fila do Azure com o SDK de Trabalhos Web](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)
* [Como usar o armazenamento de blob do Azure com o SDK de Trabalhos Web](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)
* [Como usar o armazenamento de tabela do Azure com o SDK de Trabalhos Web](websites-dotnet-webjobs-sdk-storage-tables-how-to.md)
* [Como usar o barramento de serviço do Azure com o SDK de Trabalhos Web](websites-dotnet-webjobs-sdk-service-bus.md)
* [Guia de referência rápida do SDK de Trabalhos Web do Azure (download do PDF)](http://go.microsoft.com/fwlink/?LinkID=524028&clcid=0x409)
* Vídeos
	* [WebJobs e SDK de Trabalhos Web](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-153-WebJobs-with-Pranav-Rastogi?utm_source=dlvr.it&utm_medium=twitter)
	* [Série de vídeos de Trabalhos Web do Azure no Channel 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)
	* [Apresentando as ferramentas de Trabalhos Web do Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
	* [Depuração remota e ferramentas de Trabalhos Web](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster)

Consulte também as seguintes seções sobre [Implantação de Trabalhos Web](#deploy) e [Teste e depuração de Trabalhos Web](#debug).

##<a name="deploy"></a>Implantando Trabalhos Web

* [Como implantar Trabalhos Web do Azure usando o Visual Studio.](websites-dotnet-deploy-webjobs.md)
* [Como implantar Trabalhos Web usando o Portal de Gerenciamento do Azure](web-sites-create-web-jobs.md)
* [Habilitando entrega de linha de comando ou contínua dos Trabalhos Web do Azure](http://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)
* [Implantando por Git um aplicativo de console .NET no Azure usando Trabalhos Web](http://blog.amitapple.com/post/73574681678/git-deploy-console-app/) 
* Vídeos
	* [Apresentando as ferramentas de Trabalhos Web do Visual Studio](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
	* [Depuração remota e ferramentas de Trabalhos Web](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="schedule"></a>Agendando Trabalhos Web

* [A caixa de diálogo Adicionar Trabalho Web do Azure](websites-dotnet-deploy-webjobs.md#configure)
* [Criar um Trabalho Web agendado no Portal de Gerenciamento do Azure](web-sites-create-web-jobs.md#CreateScheduled)

##<a name="debug"></a>Testando e depurando Trabalhos Web

* [Novos recursos de desenvolvedor e de depuração para Trabalhos Web do Azure no Visual Studio](http://blogs.msdn.com/b/webdev/archive/2014/11/12/new-developer-and-debugging-features-for-azure-webjobs-in-visual-studio.aspx)
* [Exibir o painel Trabalhos Web](websites-dotnet-webjobs-sdk-get-started.md#view-the-webjobs-sdk-dashboard)
* [Como gravar logs usando o SDK de Trabalhos Web e exibi-los no painel](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs)
* [Depuração remota de Trabalhos Web](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebugwj)
* [Quem criou esse blob?](http://blogs.msdn.com/b/jmstall/archive/2014/02/19/who-wrote-that-blob.aspx) 
* [Hospedando código interativo na nuvem](http://blogs.msdn.com/b/jmstall/archive/2014/04/26/hosting-interactive-code-in-the-cloud.aspx)
* [Obtendo um painel para o desenvolvimento local com o SDK de Trabalhos Web](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx)
* [Adicionando rastreamento para sites do Azure e Trabalhos Web](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)
* [Monitoramento, diagnóstico e solução de problemas de Armazenamento do Microsoft Azure](../storage-monitoring-diagnosing-troubleshooting/)
* Vídeos
	* [Depuração remota e ferramentas de Trabalhos Web](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="scale"></a>Dimensionamento de Trabalhos Web

* [Dimensionando seu aplicativo Web com sites do Azure](http://msdn.microsoft.com/magazine/dn786914.aspx)
* [Serviço de aplicativo do Azure: Arquitetura de aplicativos Web prontos para empresa de grande escala](https://channel9.msdn.com/Events/Build/2014/3-626). Aborda o dimensionamento de aplicativos Web com Trabalhos Web, incluindo o SDK de Trabalhos Web.
* Vídeos
	* [Expandindo Trabalhos Web](http://channel9.msdn.com/Shows/Azure-Friday/Azure-WebJobs-105-Scaling-out-Web-Jobs)

##<a name="additional"></a>Recursos adicionais de Trabalhos Web

* [Postagem no blog WebJobs GA do Azure de Magnus Mårtensson](http://magnusmartensson.com/azure-webjobs-ga)
* [Documentação de configurações de Trabalhos Web no GitHub](https://github.com/projectkudu/kudu/wiki/Web-jobs).
* [Executando Trabalhos Web do Powershell no Serviço de Aplicativo do Azure](http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx)
* [Seja notificado quando os Trabalhos Web disparados pelo Azure forem concluídos](http://blog.amitapple.com/post/2014/03/webjobs-notification/)
* [Política de retenção de backup de site simples com Trabalhos Web](http://azure.microsoft.com/blog/2014/04/28/simple-web-site-backup-retention-policy-with-webjobs/)
* [Sites do Azure e Serviços de Nuvem lentos na primeira solicitação](http://wp.sjkp.dk/windows-azure-websites-and-cloud-services-slow-on-first-request/). Mostra como usar Trabalhos Web para simular o recurso AlwaysOn que só está disponível para a camada de preços Padrão.
* [Desligamento normal dos Trabalhos Web](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.U72Il_5OWUl). Para o desligamento normal do SDK de Trabalhos Web, consulte [Desligamento normal](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#graceful).)
* [Automatizando backups com Trabalhos Web do Azure e AzCopy](http://markjbrown.com/azure-webjobs-azcopy/)
* Vídeos
	* [Vídeos de Trabalhos Web do Azure, de Magnus Mårtensson](https://www.youtube.com/playlist?list=PLqp1ZOYYUSd81yEzMYLTw8cz91wx_LU9r)
	* [Série de vídeos de Trabalhos Web do Azure no Channel 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="additionalsdk"></a>Recursos adicionais do SDK de Trabalhos Web

* [Código-fonte do SDK de Trabalhos Web](https://github.com/Azure/azure-webjobs-sdk)
* [Parâmetros disparadores, de associações e de rota em AzureJobs](http://blogs.msdn.com/b/jmstall/archive/2014/01/28/trigger-bindings-and-route-parameters-in-azurejobs.aspx) 
* Associações do armazenamento do Azure
	* [Blobs](http://blogs.msdn.com/b/jmstall/archive/2014/02/18/azure-storage-bindings-part-1-blobs.aspx)
	* [Filas](http://blogs.msdn.com/b/jmstall/archive/2014/02/18/azure-storage-bindings-part-2-queues.aspx)
	* [Tabelas](http://blogs.msdn.com/b/jmstall/archive/2014/03/06/azure-storage-bindings-part-3-tables.aspx)
* [Como funciona o [BlobTrigger]?](http://blogs.msdn.com/b/jmstall/archive/2014/04/17/how-does-blobinput-work.aspx) 
* [Associações avançadas com o SDK de Trabalhos Web do Azure](http://victorhurdugaci.com/advanced-bindings-with-the-windows-azure-web-jobs-sdk/)
* [Trabalho Web para carregar arquivos FREB no armazenamento do Azure usando o SDK de Trabalhos Web](http://thenextdoorgeek.com/post/WAWS-WebJob-to-upload-FREB-files-to-Azure-Storage-using-the-WebJobs-SDK)
* [Hospedando Trabalhos Web do Azure fora do Azure com os benefícios de log do Trabalho Web hospedado no Azure](http://bypassion.dk/?p=510)
* [Criando uma ferramenta de importação de dados com Trabalhos Web do Azure](http://www.freshconsulting.com/building-data-import-tool-azure-webjobs/)
* Vídeos
	* [Série de vídeos de Trabalhos Web do Azure no Channel 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="samples"></a>Aplicativos de Trabalhos Web de exemplo

* [Aplicativos de exemplo fornecidos pela equipe de Trabalhos Web no GitHub](https://github.com/azure/azure-webjobs-sdk-samples)
* [Site do Azure simples com back-end de Trabalhos Web usando o SDK de WebJobs](http://code.msdn.microsoft.com/Simple-Azure-Website-with-b4391eeb)
* [SiteMonitR](http://code.msdn.microsoft.com/SiteMonitR-dd4fcf77). Demonstra o uso de WebJobs agendados e controlados por evento. Confira a postagem de blog [Recriando o SiteMonitR usando o SDK de Trabalhos Web do Azure](http://www.bradygaster.com/post/rebuilding-the-sitemonitr-using-windows-azure-webjobs).

##<a name="blogs"></a>Blogs

* [Blog do Azure](/blog)
* [Blog de Amit Apple](http://blog.amitapple.com/). Concentra-se em Trabalhos Web (não no SDK).
* [Blog de Magnus Mårtensson](http://magnusmartensson.com/)

##<a name="gethelp"></a>Obtendo ajuda com Trabalhos Web

* [StackOverflow para Trabalhos Web](http://stackoverflow.com/questions/tagged/azure-webjobs)
* [StackOverflow para o SDK de Trabalhos Web](http://stackoverflow.com/questions/tagged/azure-webjobssdk)
* [Fórum do Azure e ASP.NET](http://forums.asp.net/1247.aspx)
* [Fórum de aplicativos Web do Serviço de Aplicativo do Azure](http://social.msdn.microsoft.com/Forums/azure/home?forum=windowsazurewebsitespreview)
* [Site de voz do usuário de Aplicativos Web do Azure](http://feedback.azure.com/forums/169385-websites)
* [Twitter](http://twitter.com/). Use a hashtag #AzureWebJobs.

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia sobre a alteração do portal antigo para o novo portal, consulte: [Referência para navegar no portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)

<!--HONumber=54--> 