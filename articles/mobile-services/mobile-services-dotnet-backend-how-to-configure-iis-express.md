<properties 
	pageTitle="Configurar o IIS Express para teste local do Serviço Móvel" 
	description="Saiba como configurar o IIS Express para permitir conexões com um projeto de serviço móvel local para testes." 
	authors="ggailey777" 
	manager="dwrede" 
	services="mobile-services" 
	documentationCenter="" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="05/20/2015" 
	ms.author="glenga"/>

# Configurar o servidor web local para permitir conexões a um Serviço Móvel local 

Os Serviços Móveis do Azure permitem criar seu Serviço Móvel no Visual Studio usando uma das linguagens .NET com suporte e, em seguida, publicá-lo no Azure. Um dos principais benefícios de usar um back-end do .NET para seu Serviço Móvel é a capacidade de executar, testar e depurar o Serviço Móvel no computador local ou na máquina virtual antes de publicá-lo no Azure.

Para testar um serviço móvel localmente com clientes que executam em um emulador, máquina virtual ou em uma estação de trabalho separada, você precisa configurar o servidor Web local e o computador host para permitir conexões ao endereço IP e à porta da estação de trabalho. Este tópico mostra como configurar o IIS Express para habilitar conexões ao serviço móvel hospedado localmente.

[AZURE.INCLUDE [mobile-services-how-to-configure-iis-express](../../includes/mobile-services-how-to-configure-iis-express.md)]

 

<!---HONumber=July15_HO3-->