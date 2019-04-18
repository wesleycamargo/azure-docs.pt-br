---
title: Diagnosticar e solucionar problemas ao usar o gatilho do Azure Cosmos DB no Azure Functions
description: Problemas comuns, soluções alternativas e etapas de diagnóstico, ao usar o gatilho do Azure Cosmos DB com o Azure Functions
author: ealsur
ms.service: cosmos-db
ms.date: 04/16/2019
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 5416c576e6392b7c5714ef83f152453aeff8d964
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59685694"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-trigger-in-azure-functions"></a>Diagnosticar e solucionar problemas ao usar o gatilho do Azure Cosmos DB no Azure Functions

Este artigo aborda problemas comuns, soluções alternativas e etapas de diagnóstico, quando você usa o [gatilho do Azure Cosmos DB](change-feed-functions.md) com o Azure Functions.

## <a name="dependencies"></a>Dependências

O gatilho do Azure Cosmos DB e associações dependem os pacotes de extensão ao longo do tempo de execução do Azure Functions base. Sempre mantenha esses pacotes atualizadas, pois eles podem incluir correções e novos recursos que podem resolver problemas potenciais que podem ser encontrados:

* Para o Azure Functions V2, consulte [cosmosdb](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB).
* Para o Azure Functions V1, consulte [Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB).

Este artigo sempre fará referência ao Azure Functions V2 sempre que o tempo de execução é mencionado, a menos que explicitamente especificado.

## <a name="consuming-the-cosmos-db-sdk-separately-from-the-trigger-and-bindings"></a>Consumindo o SDK do Cosmos DB separadamente do gatilho e associações

A principal funcionalidade do pacote de extensão é fornecer suporte para o gatilho do Azure Cosmos DB e associações. Ele também inclui o [SDK do .NET do Azure Cosmos DB](sql-api-sdk-dotnet-core.md), que é útil se você deseja interagir com o Azure Cosmos DB programaticamente sem usar o gatilho e associações.

Se quiser para usar o SDK do Azure Cosmos DB, certifique-se de que você não adicionar ao seu projeto outra referência de pacote do NuGet. Em vez disso, **permitem que a referência SDK resolver por meio do pacote de extensão do Azure Functions**.

Além disso, se você estiver criando manualmente sua própria instância das [client SDK do Azure Cosmos DB](./sql-api-sdk-dotnet-core.md), você deve seguir o padrão de ter apenas uma instância do cliente [usando uma abordagem de padrão de Singleton](../azure-functions/manage-connections.md#documentclient-code-example-c) . Esse processo irá evitar os problemas potenciais de soquete em suas operações.

## <a name="common-known-scenarios-and-workarounds"></a>Soluções alternativas e cenários comuns de conhecidos

### <a name="azure-function-fails-with-error-message-either-the-source-collection-collection-name-in-database-database-name-or-the-lease-collection-collection2-name-in-database-database2-name-does-not-exist-both-collections-must-exist-before-the-listener-starts-to-automatically-create-the-lease-collection-set-createleasecollectionifnotexists-to-true"></a>Função do Azure falha com mensagem de erro "a coleção de origem 'collection-name' (no banco de dados 'nome do banco de dados') ou a coleção de concessão 'collection2-name' (no banco de dados 'database2-name') não existe. Ambas as coleções devem existir antes de inicia o ouvinte. Para criar automaticamente a coleção de concessão, defina 'CreateLeaseCollectionIfNotExists' como 'true' "

Isso significa que um ou ambos os contêineres do Cosmos do Azure necessários para o gatilho funcionar não existem ou não estão acessíveis para a função do Azure. **O próprio erro lhe dirá qual banco de dados Cosmos do Azure e contêineres é o gatilho procurando** com base em sua configuração.

1. Verifique se o `ConnectionStringSetting` atributo e que ele **faz referência a uma configuração que existe em seu aplicativo de funções do Azure**. O valor nesse atributo não deve ser a cadeia de caracteres de Conexão em si, mas o nome da definição de configuração.
2. Verifique se que o `databaseName` e `collectionName` existir em sua conta do Cosmos do Azure. Se você estiver usando a substituição de valor automático (usando `%settingName%` padrões), verifique se o nome da configuração existir em seu aplicativo de funções do Azure.
3. Se você não especificar um `LeaseCollectionName/leaseCollectionName`, o padrão é "concessões". Verifique se que o contêiner existe. Opcionalmente, você pode definir as `CreateLeaseCollectionIfNotExists` atributo em seu gatilho para `true` para criá-lo automaticamente.
4. Verifique se sua [configuração do Firewall da conta do Azure Cosmos](how-to-configure-firewall.md) para ver se não está, ele não está bloqueando a função do Azure.

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Função do Azure falhar para iniciar com "coleta de taxa de transferência compartilhada deve ter uma chave de partição"

As versões anteriores da extensão do Azure Cosmos DB não oferecia suporte usando um contêiner de concessões que foi criado dentro de um [banco de dados de taxa de transferência compartilhada](./set-throughput.md#set-throughput-on-a-database). Para resolver esse problema, atualize o [cosmosdb](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) extensão para obter a versão mais recente.

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>Função do Azure falhar para iniciar com "a coleção de concessão se particionada, deve ter igual à id da chave de partição."

Esse erro significa que seu contêiner de concessões atual estiver particionada, mas o caminho da chave de partição não é `/id`. Para resolver esse problema, você precisa recriar o contêiner de concessões com `/id` como a chave de partição.

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>Você verá um "valor não pode ser nulo. Nome do parâmetro: o "em seus logs do Azure Functions quando você tenta executar o gatilho

Esse problema será exibida se você estiver usando o portal do Azure e tente selecionar o **executar** botão na tela ao inspecionar uma função do Azure que usa o gatilho. O gatilho não requer para que você selecione Executar para começar, ele será iniciado automaticamente quando a função do Azure é implantada. Se você quiser verificar o fluxo de log da função do Azure no portal do Azure, basta ir para seu contêiner monitorado e inserir alguns novos itens, você verá automaticamente a execução do gatilho.

### <a name="my-changes-take-too-long-be-received"></a>Minha alterações demorarem demais ser recebida

Esse cenário pode ter várias causas e todas elas devem ser verificadas:

1. Sua função do Azure é implantada na mesma região que sua conta do Cosmos do Azure? Latência de rede ideal, a função do Azure e sua conta do Azure Cosmos devem ser colocados na mesma região do Azure.
2. As alterações estão ocorrendo em seu contêiner do Azure Cosmos contínua ou esporádico?
Se for a última opção, pode haver algum atraso entre as alterações que está sendo armazenadas e função do Azure, separando-os. Isso ocorre porque, internamente, quando o gatilho verifica se há alterações em seu contêiner do Azure Cosmos e localiza nenhum pendente a ser lido, ele permanecerá inativo por um período configurável de tempo (5 segundos, por padrão) para verificar se há novas alterações (evitar o alto consumo de RU). Você pode configurar esse tempo de suspensão por meio de `FeedPollDelay/feedPollDelay` definindo na [configuração](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration) do seu gatilho (o valor deve ser em milissegundos).
3. Talvez seu contêiner do Azure Cosmos [taxa limitada](./request-units.md).
4. Você pode usar o `PreferredLocations` atributo em seu gatilho para especificar uma lista separada por vírgulas de regiões do Azure para definir uma ordem de conexão preferencial personalizado.

### <a name="some-changes-are-missing-in-my-trigger"></a>Algumas alterações estão ausentes no meu gatilho

Se você achar que algumas das alterações que ocorreram em seu contêiner do Azure Cosmos não são sendo escolhidas pela função do Azure, há uma etapa de investigação inicial que precisa ser feita.

Quando sua função do Azure recebe as alterações, ele geralmente processa e pode, opcionalmente, enviar o resultado para outro destino. Quando você está investigando alterações ausentes, verifique se você **medida que as alterações que estão sendo recebidas no ponto de ingestão** (quando a função do Azure é iniciado), não no destino.

Se algumas alterações estiverem falta no destino, isso pode significar que é algum erro acontecendo durante a execução da função do Azure depois que as alterações foram recebidas.

Nesse cenário, o melhor curso de ação é adicionar `try/catch blocks` no seu código e dentro dos loops que talvez estivesse processando as alterações, para detectar qualquer falha para um determinado subconjunto de itens e tratá-los adequadamente (enviá-los para outro armazenamento adicional análise ou repetição). 

> **Gatilho do Azure Cosmos DB, por padrão, não repita um lote de alterações se houve uma exceção sem tratamento** durante a execução de seu código. Isso significa que o motivo pelo qual as alterações não chegaram ao destino é porque você não conseguir processá-las.

Se você descobrir que algumas alterações não foram recebidas em todos os pelo seu gatilho, o cenário mais comum é que há **outro em execução de função do Azure**. Ele pode ser outra função do Azure implantado no Azure ou uma função do Azure em execução localmente no computador de um desenvolvedor que tem **exatamente a mesma configuração** (mesmo monitorados e contêineres de concessão), e essa função do Azure é aquele que rouba um subconjunto das alterações que você esperaria que sua função do Azure para processar.

Além disso, o cenário pode ser validado, se você souber quantas instâncias do aplicativo de funções do Azure que você tem em execução. Se você inspecionar o contêiner de concessões e contar o número de itens de concessão dentro, os valores distintos do `Owner` propriedade nelas deve ser igual ao número de instâncias do seu aplicativo de funções. Se houver mais de proprietários que as instâncias do aplicativo de funções do Azure conhecidas, isso significa que esses proprietários extras são um "roubando" as alterações.

Uma maneira fácil para solucionar essa situação é aplicar um `LeaseCollectionPrefix/leaseCollectionPrefix` para sua função com um valor novo/diferente ou, Alternativamente, teste com um novo contêiner de concessões.

## <a name="next-steps"></a>Próximas etapas

* [Habilitar o monitoramento para o Azure Functions](../azure-functions/functions-monitoring.md)
* [Azure Cosmos DB .NET SDK de solução de problemas](./troubleshoot-dot-net-sdk.md)