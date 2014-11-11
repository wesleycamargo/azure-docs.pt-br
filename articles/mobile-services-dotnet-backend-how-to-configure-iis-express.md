<properties pageTitle="Configure IIS Express for local testing of Mobile Service" metaKeywords="Azure Mobile Services, .NET Backend, IIS Express" description="Learn how to configure IIS Express to allow connections to a local mobile service project for testing." authors="glenga" title="Configure the local web server to allow connections to a local mobile service" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Configurar o servidor web local para permitir conexões a um Serviço Móvel local

Os Serviços Móveis do Azure permitem criar seu Serviço Móvel no Visual Studio usando uma das linguagens .NET com suporte e, em seguida, publicá-lo no Azure. Um dos principais benefícios de usar um back-end do .NET para seu Serviço Móvel é a capacidade de executar, testar e depurar o Serviço Móvel no computador local ou na máquina virtual antes de publicá-lo no Azure.

Para testar um serviço móvel localmente com clientes que executam em um emulador, máquina virtual ou em uma estação de trabalho separada, você precisa configurar o servidor Web local e o computador host para permitir conexões ao endereço IP e à porta da estação de trabalho. Este tópico mostra como configurar o IIS Express para habilitar conexões ao serviço móvel hospedado localmente.

[WACOM.INCLUDE [mobile-services-how-to-configure-iis-express](../includes/mobile-services-how-to-configure-iis-express.md)]

  [mobile-services-how-to-configure-iis-express]: ../includes/mobile-services-how-to-configure-iis-express.md
