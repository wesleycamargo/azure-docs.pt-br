<properties 
   pageTitle="Depurando serviços de nuvem do Azure | Microsoft Azure"
   description="Depurando serviços de nuvem do Azure"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="patshea123"
   manager="douge"
   editor="tlee" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/14/2015"
   ms.author="patshea" />

# Depurando serviços de nuvem

Você pode usar diferentes abordagens para depurar um aplicativo do Azure usando as ferramentas do Azure para Microsoft Visual Studio e o SDK do Azure:

- É possível depurar um aplicativo do Azure no Visual Studio durante o desenvolvimento dele, assim como você faria com qualquer aplicativo do Visual C# ou do Visual Basic. Para saber mais, consulte [Depurar o serviço de nuvem no computador local](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-your-cloud-service-on-your-local-computer)

- Você pode usar o Diagnóstico do Azure para registrar em log informações detalhadas do código em execução nas funções, estejam as funções em execução no ambiente de desenvolvimento ou no Azure. Para saber mais, consulte [Coletando dados de log usando o Diagnóstico do Azure](http://go.microsoft.com/fwlink/p/?LinkId=400450).

- Se estiver usando o Visual Studio Enterprise para escrever funções destinadas ao .NET Framework 4 ou .NET Framework 4.5, você poderá habilitar o IntelliTrace no momento em que implantar um serviço de nuvem do Azure usando o Visual Studio. O IntelliTrace fornece um log que você pode usar com o Visual Studio para depurar seu aplicativo, como se ele estivesse em execução no Azure. Para saber mais, consulte [Depurando um serviço de nuvem publicado com o IntelliTrace e o Visual Studio](http://go.microsoft.com/fwlink/p/?LinkId=623016).

- Você pode habilitar a depuração remota nos serviços de nuvem no momento em que implanta o serviço de nuvem usando o Visual Studio. Se você optar por habilitar a depuração remota para uma implantação, os serviços de depuração remota serão instalados nas máquinas virtuais que executam cada instância de função. Esses serviços, como msvsmon.exe, não afetam o desempenho nem resultam em custos extras. Para saber mais, consulte [Depurar o serviço de nuvem no Azure](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-a-cloud-service-in-azure)

<!---HONumber=Oct15_HO2-->