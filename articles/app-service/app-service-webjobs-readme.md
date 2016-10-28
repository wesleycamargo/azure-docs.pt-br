<properties
	pageTitle="WebJobs no Serviço de Aplicativo do Azure"
	description="Saiba como criar trabalhos Web para executar testes em segundo plano, interagir com serviços como Armazenamento e Barramento de Serviço e criar tarefas agendadas."
	services="app-service"
	documentationCenter=""
	authors="christopheranderson"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/10/2015"
	ms.author="chrande"/>

# Usando WebJobs no Serviço de Aplicativo do Azure

Este tópico fornece links para recursos de documentação sobre como usar o Azure WebJobs e o SDK do Azure WebJobs. Trabalhos Web do Azure fornece uma maneira fácil de executar scripts ou programas como processos em segundo plano em [Aplicativos Web do Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=529714). Você pode carregar e executar um arquivo executável, como cmd, bat, exe (.NET), ps1, sh, php, py, js e jar. Esses programas são executados como WebJobs em uma agenda (cron) ou continuamente.

O SDK de WebJobs torna mais fácil de usar o armazenamento do Azure. O SDK de WebJobs tem uma um sistema de disparo e associação que funciona com o os blobs de armazenamento do Microsoft Azure, filas e tabelas, bem como filas do barramento de serviço.

Criar, implantar e gerenciar WebJobs é fácil com ferramentas integradas no Visual Studio. Você pode criar WebJobs de modelos, publicar e gerenciá-los (executar/parar/monitor/depurar).

O painel de Trabalhos Web no portal de gerenciamento do Azure fornece recursos de gerenciamento poderosos que lhe dão controle total sobre a execução de Trabalhos Web, incluindo a capacidade de invocar funções individuais dentro de Trabalhos Web. O painel também exibe a saída de log e tempos de execução de função.

[AZURE.INCLUDE [app-service-blueprint-webjobs](../../includes/app-service-blueprint-webjobs.md)]

<!---HONumber=AcomDC_0413_2016-->