<properties
   pageTitle="Criar um namespace do Barramento de Serviço usando o portal do Azure | Microsoft Azure"
   description="Para começar a usar o Barramento de Serviço, você precisará de um namespace. Aqui está como criar um usando o portal do Azure."
   services="service-bus"
   documentationCenter=".net"
   authors="jtaubensee"
   manager="timlt"
   editor="sethmanheim"/>

<tags
   ms.service="service-bus"
   ms.devlang="tbd"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="dotnet"
   ms.workload="na"
   ms.date="06/07/2016"
   ms.author="jotaub@microsoft.com"/>

#Criando um namespace de Barramento de Serviço usando o portal do Azure
O namespace é um contêiner comum para todos os componentes de mensagem. Várias filas e tópicos podem residir em um único namespace e os namespaces geralmente servem como contêineres de aplicativos. Atualmente, existem duas maneiras diferentes de criar namespaces do Barramento de Serviço.

1.	Portal do Azure (este artigo)

2.	[Modelos de ARM][create-namespace-using-arm]

##Criando um namespace no portal do Azure
[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Parabéns! Agora, você criou um Namespace do Barramento de Serviço.

##Próximas etapas

Verifique nosso repositório GitHub com exemplos que mostram alguns dos recursos mais avançados das Mensagens do Barramento de Serviço do Azure. [https://github.com/Azure-Samples/azure-servicebus-messaging-samples][github-samples]

[create-namespace-using-arm]: ./service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

<!---HONumber=AcomDC_0608_2016-->