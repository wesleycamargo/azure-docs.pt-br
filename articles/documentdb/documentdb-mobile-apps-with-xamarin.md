---
title: "Criar aplicativos móveis com o Xamarin e o Azure DocumentDB | Microsoft Docs"
description: "Um tutorial que cria um aplicativo iOS, Android ou Forms do Xamarin usando o Azure DocumentDB. O DocumentDB é um banco de dados de nuvem para aplicativos móveis rápido e em escala mundial."
services: documentdb
documentationcenter: .net
author: arramac
manager: monicar
editor: 
ms.assetid: ff97881a-b41a-499d-b7ab-4f394df0e153
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2017
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 3e058505f7c17d19d2cebca053badb3505a00f13
ms.lasthandoff: 03/28/2017


---
# <a name="build-mobile-applications-with-xamarin-and-azure-documentdb"></a>Criar aplicativos móveis com o Xamarin e o Azure DocumentDB
A maioria dos aplicativos móveis precisa armazenar dados na nuvem e o Azure DocumentDB é um banco de dados de nuvem para aplicativos móveis. Ele tem tudo o que um desenvolvedor móvel precisa. Ele é um banco de dados como serviço NoSQL totalmente gerenciado que é dimensionado sob demanda. Ele pode trazer seus dados para seu aplicativo de modo transparente, independentemente de onde os usuários estejam localizados em todo o mundo. Usando o [SDK do .NET Core do Azure DocumentDB](documentdb-sdk-dotnet-core.md), você pode permitir que aplicativos móveis do Xamarin interajam diretamente com o DocumentDB, sem uma camada intermediária.

Este artigo fornece um tutorial para a criação de aplicativos móveis com o Xamarin e o DocumentDB. Você encontrará o código-fonte completo para o tutorial em [Xamarin e DocumentDB no GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin), incluindo como gerenciar usuários e permissões.

## <a name="documentdb-capabilities-for-mobile-apps"></a>Recursos do DocumentDB para aplicativos móveis
O DocumentDB fornece os seguintes recursos principais para desenvolvedores de aplicativos móveis:

![Recursos do DocumentDB para aplicativos móveis](media/documentdb-mobile-apps-with-xamarin/documentdb-for-mobile.png)

* Consultas avançados sobre o repositório de dados. O DocumentDB armazena dados como documentos JSON sem esquema em coleções heterogêneas. Ele oferece [consultas rápidas e avançadas](documentdb-sql-query.md) sem a necessidade de se preocupar com esquemas ou índices.
* Taxa de transferência rápida. Leva apenas alguns milissegundos para ler e gravar documentos com o DocumentDB. Os desenvolvedores podem especificar a taxa de transferência de que precisam e o DocumentDB a respeitará com SLAs de 99,99%.
* Escala ilimitada. As coleções do DocumentDB [aumentam à medida que o aplicativo aumenta](documentdb-partition-data.md). Você pode iniciar com tamanho de dados pequeno e taxa de transferência de centenas de solicitações por segundo. As coleções podem crescer até petabytes de dados e uma taxa de transferência arbitrariamente grande com centenas de milhões de solicitações por segundo.
* Distribuído globalmente. Os usuários de aplicativos móveis costumam ser em outro lugar, em todo o mundo. O DocumentDB é um [banco de dados globalmente distribuído](documentdb-distribute-data-globally.md). Clique no mapa para disponibilizar os dados para seus usuários.
* Autorização interna avançada. Com o DocumentDB, você pode implementar facilmente padrões populares como [dados por usuário](https://aka.ms/documentdb-xamarin-todouser) ou dados compartilhados por vários usuários sem código de autorização complexo personalizado.
* Consultas geoespaciais. Hoje, muitos aplicativos móveis oferecem experiências geográficas contextuais. Com o suporte de primeira classe para [tipos geoespaciais](documentdb-geospatial.md), o DocumentDB facilita a criação dessas experiências.
* Anexos binários. Os dados de aplicativo geralmente incluem blobs binários. O suporte nativo para anexos facilita o uso do DocumentDB como serviço único para seus dados de aplicativo.

## <a name="documentdb-and-xamarin-tutorial"></a>Tutorial do Xamarin e do DocumentDB
O tutorial a seguir mostra como criar um aplicativo móvel usando o Xamarin e o DocumentDB. Você encontrará o código-fonte completo para o tutorial em [Xamarin e DocumentDB no GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin).

### <a name="get-started"></a>Introdução
É fácil começar a usar o DocumentDB. Vá até o Portal do Azure e crie uma nova conta do DocumentDB. Clique na guia **Início rápido**. Baixe o exemplo de lista de tarefas pendentes de Formulários do Xamarin que já está conectado à sua conta do DocumentDB. 

![Início rápido do DocumentDB para aplicativos móveis](media/documentdb-mobile-apps-with-xamarin/documentdb-quickstart.png)

Ou então, se você tiver um aplicativo existente, você poderá adicionar o [pacote do NuGet do DocumentDB](documentdb-sdk-dotnet-core.md). O DocumentDB oferece suporte a bibliotecas compartilhadas do Xamarin.Android e do Xamarin Forms.

### <a name="work-with-data"></a>Trabalhar com dados
Os registros de dados são armazenados no DocumentDB como documentos JSON sem esquema nas coleções heterogêneas. Você pode armazenar documentos com estruturas diferentes na mesma coleção:

```cs
    var result = await client.CreateDocumentAsync(collectionLink, todoItem);
```

No seus projetos do Xamarin, você pode usar consultas integradas à linguagem para dados sem esquema:

```cs
    var query = await client.CreateDocumentQuery<ToDoItem>(collectionLink)
                    .Where(todoItem => todoItem.Complete == false)
                    .AsDocumentQuery();

    Items = new List<TodoItem>();
    while (query.HasMoreResults) {
        Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
    }
```
### <a name="add-users"></a>Adicionar usuários
Assim como em muitas amostras de introdução, a amostra do DocumentDB que você baixou autentica no serviço usando a chave mestra codificada no código do aplicativo. Esse padrão não é uma boa prática para um aplicativo que você pretende executar em qualquer lugar, exceto o emulador local. Se um usuário não autorizado obtiver um bloqueio da chave mestra, todos os dados da sua conta do DocumentDB poderão ser comprometidos. Em vez disso, você quer que seu aplicativo acesse somente os registros para o usuário conectado. O DocumentDB permite aos desenvolvedores conceder permissão de leitura/gravação ou de leitura de aplicativo a uma coleção, a um conjunto de documentos agrupados por uma chave de partição ou a um documento específico. 

Siga estas etapas para modificar o aplicativo de lista de tarefas pendentes para um aplicativo de lista de tarefas pendentes multiusuário: 

  1. Adicione logon ao seu aplicativo usando o Facebook, o Active Directory ou qualquer outro provedor.

  2. Crie uma coleção UserItems do DocumentDB com **/userId** como uma chave de partição. Especificar chave de partição para a coleção permite que o DocumentDB seja infinitamente dimensionado à medida que o número de usuários do aplicativo aumente e, simultaneamente, oferece consultas rápidas.

  3. Adicione o Agente de Token de Recurso do DocumentDB. Essa API Web simples autentica usuários e emite tokens de curta duração para usuários conectados, com acesso exclusivamente aos documentos na partição do usuário conectado. Neste exemplo, o Agente de Token de Recurso é hospedado no Serviço de Aplicativo.

  4. Modifique o aplicativo para autenticar o recurso Agente de Token com o Facebook e solicite os tokens de recurso para os usuários conectados do Facebook. Você pode os dados deles na coleção UserItems.  

Você pode encontrar um exemplo de código completo desse padrão em [Agente de Token de Recurso no GitHub](http://aka.ms/documentdb-xamarin-todouser). Este diagrama ilustra a solução:

![Usuários do DocumentDB e agente de permissões](media/documentdb-mobile-apps-with-xamarin/documentdb-resource-token-broker.png)

Se você desejar que dois usuários tenham acesso à mesma lista de tarefas pendentes, você poderá adicionar permissões adicionais para o token de acesso no Agente de Token de Recurso.

### <a name="scale-on-demand"></a>Dimensionar sob demanda
O DocumentDB é um banco de dados gerenciado como um serviço. Conforme cresce a sua base de usuários, você não precisa se preocupar sobre o provisionamento de máquinas virtuais ou aumentando núcleos. Tudo o que você precisa informar ao DocumentDB é de quantas operações por segundo (taxa de transferência) seu aplicativo precisa. Você pode especificar a taxa de transferência por meio da guia **Escala** usando uma medida de taxa de transferência chamada RUs (unidades de solicitação) por segundo. Por exemplo, uma operação de leitura em um documento de 1 KB exige 1 RU. Você pode também adicionar alertas à métrica **Taxa de transferência** para monitorar o aumento do tráfego e alterar a taxa de transferência programaticamente conforme os alertas são disparados.

![Taxa de transferência de escala do DocumentDB sob demanda](media/documentdb-mobile-apps-with-xamarin/documentdb-scale.png)

### <a name="go-planet-scale"></a>Escala mundial
À medida que o aplicativo ganhar popularidade, talvez você ganhe usuários em todo o mundo. Ou talvez você queira estar preparado para eventos inesperados. Vá até o Portal do Azure e abra sua conta do DocumentDB. Clique no mapa para fazer com que seus dados sejam replicados continuamente para qualquer número de regiões em todo o mundo. Essa funcionalidade torna os dados disponíveis, onde quer que os usuários estejam. Você também pode adicionar políticas de failover para estar preparado para contingências.

![Escala do DocumentDB entre regiões geográficas](media/documentdb-mobile-apps-with-xamarin/documentdb-replicate-globally.png)

Parabéns. Você concluiu a solução e tem um aplicativo móvel com o Xamarin e o DocumentDB. Siga etapas semelhantes para criar aplicativos Cordova usando o SDK do JavaScript do DocumentDB e aplicativos iOS/Android nativos usando APIs REST do DocumentDB.

## <a name="next-steps"></a>Próximas etapas
* Exiba o código-fonte para [Xamarin e DocumentDB no GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin).
* Baixe o [SDK do .NET Core do DocumentDB](documentdb-sdk-dotnet-core.md).
* Encontre mais exemplos de código para [aplicativos .NET](documentdb-dotnet-samples.md).
* Saiba mais sobre os [recursos de consulta avançada do DocumentDB](documentdb-sql-query.md).
* Saiba mais sobre [suporte geoespacial no DocumentDB](documentdb-geospatial.md).




