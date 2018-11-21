---
title: Como usar o feed de alterações do Azure Cosmos DB com o Azure Functions
description: Usar o feed de alterações do Azure Cosmos DB com o Azure Functions
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: rimman
ms.openlocfilehash: 943cd79237290cd91d56cc1c51f5d773cdb16634
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636383"
---
# <a name="how-to-use-azure-cosmos-db-change-feed-with-azure-functions"></a>Como usar o feed de alterações do Azure Cosmos DB com o Azure Functions

Se estiver usando o Azure Functions, a maneira mais simples de conectar o feed de alterações é adicionar um [gatilho do Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger) ao aplicativo do Azure Functions. Quando você cria um gatilho do Cosmos DB em um aplicativo do Azure Functions, selecione o contêiner do Cosmos para conectar-se e a função será disparada sempre que alguma alteração for feita no contêiner.

Os gatilhos podem ser criados no portal do Azure Functions, no portal do Azure Cosmos DB ou programaticamente. Para obter mais informações, consulte [computação de banco de dados sem servidor usando o Azure Cosmos DB e o Azure Functions](serverless-computing-database.md).

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="how-can-i-configure-azure-functions-to-read-from-a-particular-region"></a>Como configurar o Azure Functions para ler de uma determinada região?

É possível definir os [PreferredLocations](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations?view=azure-dotnet#Microsoft_Azure_Documents_Client_ConnectionPolicy_PreferredLocations) ao usar o gatilho do Azure Cosmos DB para especificar uma lista de regiões. É o mesmo que personalizar o ConnectionPolicy para que o gatilho leia a partir de suas regiões preferenciais. O ideal é ler a partir da região mais próxima da qual o Azure Functions está implantado.

### <a name="what-is-the-default-size-of-batches-in-azure-functions"></a>Qual é o tamanho padrão dos lotes no Azure Functions?

O tamanho padrão é de 100 itens para cada chamada do Azure Functions. No entanto, esse número é configurável no arquivo function.json. Aqui está a [lista de opções de configuração](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration) completa. Se estiver desenvolvendo localmente, atualize as configurações do aplicativo no arquivo local.settings.json.

### <a name="i-am-monitoring-a-container-and-reading-its-change-feed-however-i-dont-get-all-the-inserted-documents-some-items-are-missing"></a>Estou monitorando um contêiner e lendo o feed de alterações, no entanto, não recebo todos os documentos inseridos, isso significa que alguns itens estão ausentes?

Certifique-se de que não há outro Azure Functions lendo o mesmo contêiner com o mesmo contêiner de concessão. Os documentos ausentes são processados por outro Azure Functions que também usa a mesma concessão.

Portanto, se você estiver criando vários Azure Functions para ler o mesmo feed de alterações, será necessário que usem diferentes contêineres de concessão ou usem a configuração "leasePrefix" para compartilhar o mesmo contêiner. No entanto, quando você usar a biblioteca do processador de feeds de alterações, poderá iniciar várias instâncias do Azure Functions e o SDK dividirá os documentos entre instâncias diferentes automaticamente para você.

### <a name="azure-cosmos-item-is-updated-every-second-and-i-dont-get-all-the-changes-in-azure-functions-listening-to-change-feed"></a>Por que o item do Azure Cosmos é atualizado a cada segundo e eu não estou obtenho todas as alterações no Azure Functions para o feed de alterações?

O Azure Functions pesquisa o feed de alterações continuamente com um atraso máximo padrão de 5 segundos. Se não houver alterações pendentes a serem lidas ou se houver alterações pendentes após o gatilho ser aplicado, o Functions lerá imediatamente. No entanto, se não houver alterações pendentes, o Functions aguardará 5 segundos e pesquisará mais alterações.

Se o documento receber várias alterações no mesmo intervalo que levou o Gatilho a pesquisar novas alterações, você poderá receber a última versão do documento, e não uma versão intermediária.

Se quiser pesquisar o feed de alterações por menos de 5 segundos, por exemplo, a cada segundo, você poderá configurar o tempo de sondagem "feedPollDelay", consulte [a configuração completa](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations?view=azure-dotnet#Microsoft_Azure_Documents_Client_ConnectionPolicy_PreferredLocations). Ele é definido em milissegundos com um padrão de 5000. A sondagem para menos de 1 segundo é possível, mas não é aconselhável porque começará a usar mais memória da CPU.

### <a name="can-multiple-azure-functions-read-one-containers-change-feed"></a>Vários Azure Functions podem ler o feed de alterações de um contêiner?

Sim. Vários Azure Functions podem ler o feed de alterações do mesmo contêiner. No entanto, é necessário que os Azure Functions tenham um "leaseCollectionPrefix" definido separadamente.

### <a name="if-i-am-processing-change-feed-by-using-azure-functions-in-a-batch-of-10-documents-and-i-get-an-error-at-seventh-document-in-that-case-the-last-three-documents-are-not-processed-how-can-i-start-processing-from-the-failed-document-ie-seventh-document-in-my-next-feed"></a>Se eu estiver processando o feed de alterações usando o Azure Functions, em um lote de 10 documentos, e obtiver um erro no sétimo Documento. Nesse caso, os três últimos documentos não serão processados. Como posso iniciar o processamento a partir do documento com falha (ou seja, sétimo documento) no meu próximo feed?

Para lidar com o erro, o padrão recomendado é envolver o código com o bloco try-catch e, se você estiver interagindo com a lista de documentos, coloque cada iteração em seu próprio bloco try-catch. Identifique o erro e coloque o documento em uma fila (mensagens mortas) e, em seguida, defina a lógica para lidar com os documentos que geraram o erro. Com esse método, se você tiver um lote de 200 documentos e apenas um documento falhar, não será necessário descartar o lote inteiro.

Em caso de erro, você não deverá retroceder o ponto de verificação de volta para o início, senão, você poderá continuar recebendo esses documentos de feed de alterações. Lembre-se de que o feed de alterações mantém o último instantâneo final dos documentos, por causa disso, você pode perder o instantâneo anterior no documento. feed de alterações mantém apenas uma última versão do documento e, no interim, outros processos podem vir e alterar o documento.

Conforme você continua a corrigir o código, logo não encontrará mais documentos na fila de mensagens mortas. O Azure Functions é chamado automaticamente pelo sistema de feed de alterações e o ponto de verificação é mantido internamente pelo Azure Functions. Se você quiser reverter o ponto de verificação e controlar todos os aspectos dele, deverá considerar o uso do SDK do processador de feed de alterações.

### <a name="are-there-any-extra-costs-for-using-the-azure-cosmos-db-trigger"></a>Há custos adicionais para usar o Gatilho do Azure Cosmos DB?

O Gatilho do Azure Cosmos DB usa a biblioteca do Processador de Feed de Alterações internamente. Como tal, requer uma coleção adicional, chamada de coleção de Concessões, para manter pontos de verificação parciais e de estado. Esse gerenciamento de estado é necessário para poder dimensionar e continuar dinamicamente, caso você queira interromper o Azure Functions e continuar processando posteriormente. Para saber mais, consulte [como trabalhar com a biblioteca do processador de feed de alterações](change-feed-processor.md).

## <a name="next-steps"></a>Próximas etapas

Agora, continue sabendo mais sobre o feed de alterações consultando os seguintes artigos:

* [Visão geral do feed de alterações](change-feed.md)
* [Maneiras de ler o feed de alterações](read-change-feed.md)
* [Usando a biblioteca do processador do feed de alterações](change-feed-processor.md)
* [Como trabalhar com a biblioteca do processador de feed de alterações](change-feed-processor.md)
* [Computação de banco de dados sem servidor usando o Azure Cosmos DB e o Azure Functions](serverless-computing-database.md)
