---
title: "Criar aplicativos móveis com o Xamarin e o Azure DocumentDB | Microsoft Docs"
description: "Um tutorial que cria um aplicativo iOS, Android ou Forms do Xamarin usando o Azure DocumentDB. O DocumentDB é um banco de dados de nuvem para aplicativos móveis incrivelmente rápido, em escala mundial."
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
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 4a783397b038b7545bbbfc45813db3d9973c5e19
ms.lasthandoff: 03/22/2017


---
# <a name="build-mobile-applications-with-xamarin-and-azure-documentdb"></a>Criar aplicativos móveis com o Xamarin e o Azure DocumentDB
A maioria dos aplicativos móveis precisa armazenar dados na nuvem, e o Azure DocumentDB é um banco de dados de nuvem para aplicativos móveis. Ele tem tudo o que um desenvolvedor móvel precisa. Ele é um banco de dados NoSQL totalmente gerenciado como um serviço que é dimensionada sob demanda e pode colocar seus dados onde os usuários forem em todo o mundo, transparente para seu aplicativo. Usando o [SDK do .NET Core do Azure DocumentDB](documentdb-sdk-dotnet-core.md), você pode permitir que aplicativos móveis do Xamarin interajam diretamente com o DocumentDB, sem uma camada intermediária.

Neste artigo, nós fornecemos um tutorial para a criação de aplicativos móveis com o Xamarin e o DocumentDB. Você encontrará o código-fonte completo para o tutorial [Xamarin e DocumentDB no Github](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin), incluindo como gerenciar usuários e permissões.

## <a name="documentdb-capabilities-for-mobile-apps"></a>Recursos do DocumentDB para aplicativos móveis
O DocumentDB fornece os seguintes recursos principais para desenvolvedores de aplicativos móveis:

![Recursos do DocumentDB para aplicativos móveis](media/documentdb-mobile-apps-with-xamarin/documentdb-for-mobile.png)

* Consultas avançados sobre o repositório de dados. O DocumentDB armazena dados como documentos JSON sem esquema nas coleções heterogêneas e oferece [consultas rápidas e avançadas](documentdb-sql-query.md) sem a necessidade de se preocupar sobre esquema ou índices.
* Rápido. Garantido. Leva apenas alguns milissegundos para ler e gravar documentos com o DocumentDB. Os desenvolvedores podem especificar a taxa de transferência de que precisam e o DocumentDB a respeitará com SLAs de 99,99%.
* Escala ilimitada. As coleções do DocumentDB [aumentam à medida que o aplicativo aumenta](documentdb-partition-data.md). Você pode iniciar com tamanho de dados pequeno e solicitações de 100s por segundo e aumentar até taxas de transferência arbitrariamente grandes, 10s e 100 milhões de solicitações por segundo e petabytes de dados.
* Distribuído globalmente. Os usuários de aplicativos móveis costumam ser em outro lugar, em todo o mundo. O DocumentDB é um [banco de dados distribuído globalmente](documentdb-distribute-data-globally.md) e, com apenas um clique em um mapa, ele exibirá os dados estejam onde estiverem os usuários.
* Autorização interna avançada. Com o DocumentDB, você pode implementar facilmente padrões populares como [dados por usuário](https://aka.ms/documentdb-xamarin-todouser), ou dados compartilhados por vários usuários sem código de autorização complexo personalizado.
* Consultas geoespaciais. Hoje, muitos aplicativos móveis oferecem experiências geográficas contextuais. Com o suporte de primeira classe para [tipos geoespaciais](documentdb-geospatial.md), o DocumentDB facilita a realização dessa experiência.
* Anexos binários. Os dados de aplicativo geralmente incluem blobs binários. O suporte nativo para anexos facilita o uso do DocumentDB como loja de dados para seu aplicativo.

## <a name="documentdb-and-xamarin-tutorial"></a>Tutorial do Xamarin e do DocumentDB
O tutorial a seguir mostra como criar um aplicativo móvel usando o Xamarin e o DocumentDB em cinco etapas fáceis. Você encontrará o código-fonte completo para o tutorial em [Xamarin e DocumentDB no Github](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin).

### <a name="get-started"></a>Introdução
É fácil começar a usar o DocumentDB. Vá para o portal do Azure, crie uma nova conta do DocumentDB, vá para a guia Início Rápido e baixe um exemplo "to do list" do Xamarin Forms, já conectado à sua conta do DocumentDB. 

![Início rápido do DocumentDB para aplicativos móveis](media/documentdb-mobile-apps-with-xamarin/documentdb-quickstart.png)

Ou se você tiver um aplicativo existente, basta adicionar esse [pacote do NuGet do DocumentDB](documentdb-sdk-dotnet-core.md). O DocumentDB oferece suporte a bibliotecas compartilhadas do Xamarin.Android e do Xamarin Forms.

### <a name="work-with-data"></a>Trabalhar com dados
Os registros de dados são armazenados no DocumentDB como documentos JSON sem esquema nas coleções heterogêneas. Você pode armazenar documentos com estruturas diferentes na mesma coleção.

```cs
    var result = await client.CreateDocumentAsync(collectionLink, todoItem);
```

No seu Xamarin projetos que você pode usar a linguagem integrado consultas sobre o repositório de dados:

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
Como muitos obtém amostras de Introdução, o exemplo do DocumentDB que você baixou autentica no serviço usando a chave mestra codificada no código do aplicativo. Essa não é uma boa ideia para um aplicativo que você pretende executar em qualquer lugar, exceto o emulador local. Se um invasor obtiver um bloqueio da chave mestra, todos os dados da sua conta do DocumentDB serão comprometidos. Em vez disso, queremos que nosso aplicativo só tenha acesso aos registros para o usuário conectado. O DocumentDB permite aos desenvolvedores conceder acesso de leitura/gravação ou de leitura de aplicativo a uma coleção, a um conjunto de documentos agrupados por uma chave de partição ou a um documento específico. 

Por exemplo, eis como modificar nosso aplicativo de lista de tarefas pendentes em um aplicativo de vários usuários "para fazer a lista": 

* Adicione logon ao seu aplicativo, usando o Facebook, o Active Directory ou qualquer outro provedor.
* Crie uma coleção UserItems do DocumentDB com /userId como uma chave de partição. Especificar chave de partição para a coleção permite que o DocumentDB seja infinitamente dimensionado à medida que o número de usuários do aplicativo aumente, oferecendo consultas rápidas ao mesmo tempo.
* Adicione o Agente de Token de Recurso do DocumentDB, uma API Web simples que autentica usuários e emite tokens de curta duração para usuários com acesso somente para os documentos na partição do usuário conectado. Neste exemplo, podemos hospedar o Agente de Token de Recurso no Serviço de Aplicativo.
* Modifique o aplicativo para autenticar o recurso Agente de Token com o Facebook e solicite os tokens de recurso para o usuário conectado do Facebook e então acesse os dados de usuários na coleção UserItems.  

Você pode encontrar um exemplo de código completo desse padrão em [recurso Agente de Token no Github](http://aka.ms/documentdb-xamarin-todouser). Este diagrama ilustra a solução:

![Usuários do DocumentDB e agente de permissões](media/documentdb-mobile-apps-with-xamarin/documentdb-resource-token-broker.png)

Agora se quisermos que dois usuários acessam o mesmo "para listar", adicionamos permissões adicionais para o token de acesso no agente de Token de recurso.

### <a name="scale-on-demand"></a>Dimensionar sob demanda
O DocumentDB é um banco de dados gerenciado como um serviço. Conforme cresce a sua base de usuários, você não precisa se preocupar sobre o provisionamento de máquinas virtuais ou aumentando núcleos. Tudo o que você precisa informar ao DocumentDB é de quantas operações por segundo (taxa de transferência) seu aplicativo precisa. Você pode especificar a taxa de transferência por meio do portal guia escala usando uma medida de taxa de transferência chamada unidades de solicitação por segundo (RUs). Por exemplo, uma operação de leitura em um documento de 1 KB exige 1 RU. Você pode também adicionar alertas de métrica de "Taxa de transferência" monitorar o aumento do tráfego e alterar a taxa de transferência, alertas incêndio programaticamente.

![Taxa de transferência de escala do DocumentDB sob demanda](media/documentdb-mobile-apps-with-xamarin/documentdb-scale.png)

### <a name="go-planet-scale"></a>Escala mundial
À medida que o aplicativo ganhar popularidade, talvez você ganhe usuários em todo o mundo. OU talvez você não queira ser pego desprevenido caso um meteorito caia sobre os data centers do Azure onde você criou sua coleção do DocumentDB. Vá para o portal do Azure, sua conta do DocumentDB e, com um clique em um mapa, faça seus dados serem continuamente replicados para qualquer número de regiões do mundo. Isso garante que os dados estão disponíveis, onde os usuários, e você pode adicionar diretivas de failover para estar preparado para o dia de chuva.

![Escala do DocumentDB entre regiões geográficas](media/documentdb-mobile-apps-with-xamarin/documentdb-replicate-globally.png)

Parabéns! Você concluiu a solução e tem um aplicativo móvel com o Xamarin e o DocumentDB. Um padrão semelhante pode ser usado em aplicativos Cordova usando o SDK do JavaScript do DocumentDB e aplicativos iOS/Android nativos usando APIs REST do DocumentDB.

## <a name="next-steps"></a>Próximas etapas
* Exiba o código-fonte para [Xamarin e DocumentDB no Github](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin).
* Baixe o [SDK do .NET Core do DocumentDB](documentdb-sdk-dotnet-core.md).
* Encontre mais exemplos de código para [aplicativos .NET](documentdb-dotnet-samples.md).
* Saiba mais sobre os [recursos de consulta avançada do DocumentDB](documentdb-sql-query.md).
* Saiba mais sobre [suporte geoespacial no DocumentDB](documentdb-geospatial.md).




