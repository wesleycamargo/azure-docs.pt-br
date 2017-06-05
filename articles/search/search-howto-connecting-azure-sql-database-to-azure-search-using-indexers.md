---
title: Conectando o Banco de Dados SQL do Azure ao Azure Search usando indexadores | Microsoft Docs
description: "Saiba como extrair dados do Banco de Dados SQL do Azure para um índice de Pesquisa do Azure usando indexadores."
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: e9bbf352-dfff-4872-9b17-b1351aae519f
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 02/15/2017
ms.author: eugenesh
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 54b8e16504e1170058dd021f7f7e2fba7b99bba7
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017

---

# <a name="connecting-azure-sql-database-to-azure-search-using-indexers"></a>Conectando o Banco de Dados do SQL do Azure à Pesquisa do Azure usando indexadores
O serviço da Pesquisa do Azure é um serviço de pesquisa de nuvem hospedado que facilita o fornecimento de uma excelente experiência de pesquisa. Antes de pesquisar, você precisa popular um índice da Pesquisa do Azure com seus dados. Se os dados estiverem em um Banco de Dados SQL do Azure, o novo **indexador do Azure Search para o Banco de Dados SQL do Azure** (ou **indexador do SQL Azure** para resumir) poderá automatizar o processo de indexação. Isso significa menos código para escrever e menos infraestrutura para se preocupar.

Este artigo aborda a mecânica do uso de indexadores, mas também descreve os recursos disponíveis apenas com os bancos de dados SQL do Azure (por exemplo, controle integrado de alterações). O Azure Search também dá suporte a outras fontes de dados, como Azure Cosmos DB, armazenamento de blobs e armazenamento de tabelas. Se você quiser suporte para outras fontes de dados, forneça seus comentários no [Fórum de comentários do Azure Search](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="indexers-and-data-sources"></a>Indexadores e fontes de dados
Você pode definir e configurar um indexador do SQL Azure usando:

* Assistente para Importação de Dados no [portal do Azure](https://portal.azure.com)
* [SDK do .NET](https://msdn.microsoft.com/library/azure/dn951165.aspx) do Azure Search
* [API REST](http://go.microsoft.com/fwlink/p/?LinkID=528173) do Azure Search

Neste artigo, vamos usar a API REST para mostrar a você como criar e gerenciar **indexadores** e **fontes de dados**.

Uma **fonte de dados** especifica quais dados indexar, as credenciais necessárias para acessar os dados e as políticas que identificam com eficiência as alterações nos dados (linhas novas, modificadas ou excluídas). Ela é definida como um recurso independente para que possa ser usada por vários indexadores.

Um **indexador** é um recurso que conecta fontes de dados a índices de pesquisa de destino. Um indexador é usado para as seguintes finalidades:

* Executar uma cópia única dos dados para preenchimento de um índice.
* Atualizar um índice com as alterações feitas na fonte de dados com base em uma agenda.
* Executar sob demanda para atualização de um índice, conforme necessário.

## <a name="when-to-use-azure-sql-indexer"></a>Quando usar o indexador do SQL Azure
O uso do indexador do SQL Azure pode ou não ser apropriado dependendo de vários fatores relacionados aos seus dados. Se os dados atenderem aos requisitos a seguir, você poderá usar o indexador do SQL Azure:

* Todos os dados são provenientes de uma única tabela ou exibição
  * Se os dados estiverem espalhados em várias tabelas, você pode criar uma exibição e usar essa exibição com o indexador. No entanto, se você usar uma exibição, não será possível usar a detecção integrada de alterações do SQL Server. Para saber mais, consulte [esta seção](#CaptureChangedRows).
* Os tipos de dados usados na fonte de dados têm suporte do indexador. A maioria dos tipos de SQL tem suporte, mas não todos. Para mais detalhes, confira [Mapeamento dos tipos de dados na Pesquisa do Azure](http://go.microsoft.com/fwlink/p/?LinkID=528105).
* Você não precisa de atualizações quase em tempo real no índice quando uma linha é alterada.
  * O indexador pode reindexar sua tabela no máximo a cada 5 minutos. Se os dados forem alterados com frequência e as alterações precisarem ser refletidas no índice em questão de segundos ou minutos, recomendamos o uso direto da [API do Índice de Pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn798930.aspx) .
* Se você tiver um conjunto grande de dados e planeja executar o indexador com base em uma agenda, o esquema permitirá a identificação eficiente de linhas alteradas (e excluídas, se for aplicável). Para mais detalhes, confira "Captura de linhas alteradas e excluídas" abaixo.
* O tamanho dos campos indexados em uma linha não excede o tamanho máximo de uma solicitação de indexação de Pesquisa do Azure, que é de 16 MB.

## <a name="create-and-use-an-azure-sql-indexer"></a>Criar e usar um indexador do SQL Azure
Primeiro, crie a fonte de dados:

    POST https://myservice.search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }


Você pode conseguir a cadeia de conexão no [Portal clássico do Azure](https://portal.azure.com); use a opção `ADO.NET connection string`.

Em seguida, crie um índice de destino da Pesquisa do Azure, caso ainda não tenha criado um. É possível criar um índice usando a [interface do usuário do portal](https://portal.azure.com) ou a [API de Criação de Índices](https://msdn.microsoft.com/library/azure/dn798941.aspx). Verifique se o esquema de seu índice de destino é compatível com o esquema da tabela de origem – consulte [mapeamento entre tipos de dados do Azure Search e SQL](#TypeMapping).

Por fim, crie o indexador dando um nome a ele e fazendo referência à fonte de dados e ao índice de destino:

    POST https://myservice.search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }

Um indexador criado dessa maneira não tem um agenda. Ele é executado automaticamente assim que é criado. Você pode executá-lo novamente a qualquer momento usando uma solicitação **executar indexador** :

    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2016-09-01
    api-key: admin-key

Você pode personalizar vários aspectos do comportamento do indexador, como tamanho do lote e quantos documentos podem ser ignorados antes de uma execução do indexador falhar. Para obter mais informações, consulte [Criar API do indexador](https://msdn.microsoft.com/library/azure/dn946899.aspx).

Talvez seja necessário permitir a conexão dos serviços do Azure ao banco de dados. Confira [Conexão a partir do Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) para obter instruções sobre como fazer isso.

Para monitorar o status do indexador e o histórico de execução (número de itens indexados, falhas etc.), use uma solicitação **status do indexador** :

    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2016-09-01
    api-key: admin-key

A resposta deve parecer com a seguinte:

    {
        "@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2015-02-21T00:23:24.957Z",
            "endTime":"2015-02-21T00:36:47.752Z",
            "errors":[],
            "itemsProcessed":1599501,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        },
        "executionHistory":
        [
            {
                "status":"success",
                "errorMessage":null,
                "startTime":"2015-02-21T00:23:24.957Z",
                "endTime":"2015-02-21T00:36:47.752Z",
                "errors":[],
                "itemsProcessed":1599501,
                "itemsFailed":0,
                "initialTrackingState":null,
                "finalTrackingState":null
            },
            ... earlier history items
        ]
    }

O histórico de execução contém até 50 execuções mais recentes, classificadas em ordem cronológica inversa (de modo que a execução mais recente apareça em primeiro lugar na resposta).
Informações adicionais sobre a resposta podem ser encontradas em [Obter o status do indexador](http://go.microsoft.com/fwlink/p/?LinkId=528198)

## <a name="run-indexers-on-a-schedule"></a>Executar indexadores de acordo com uma agenda
Você também pode organizar o indexador para que execute periodicamente com base em uma agenda. Para fazer isso, adicione a propriedade **schedule** ao criar ou atualizar o indexador. O exemplo a seguir mostra uma solicitação PUT para atualização do indexador:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2016-09-01
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

O parâmetro **interval** é necessário. O intervalo refere-se ao tempo entre o início de duas execuções consecutivas do indexador. O menor intervalo permitido é de cinco minutos, e o maior é de um dia. Ele deve ser formatado como um valor XSD de "dayTimeDuration" (um subconjunto restrito de um [valor de duração ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). O padrão para isso é: `P(nD)(T(nH)(nM))`. Exemplos: `PT15M` para cada 15 minutos, `PT2H` para cada 2 horas.

O **startTime** opcional indica quando as execuções agendadas devem começar. Se for omitido, a hora UTC atual será usada. Esse tempo pode estar no passado; nesse caso, a primeira execução é agendada como se o indexador estivesse em execução contínua desde o startTime.  

Só é possível ocorrer uma execução de um indexador por vez. Se um indexador é executado quando sua execução está agendada, a execução é adiada até a próxima hora agendada.

Vamos considerar um exemplo para tornar isso mais concreto. Vamos supor que o seguinte agendamento por hora está configurado:

    "schedule" : { "interval" : "PT1H", "startTime" : "2015-03-01T00:00:00Z" }

Veja o que acontece:

1. A primeira execução do indexador começa em, ou em torno de, 1º de março de 2015, meia-noite. UTC.
2. Vamos supor que essa execução demore 20 minutos (ou menos de uma hora).
3. A segunda execução começa em, ou em torno de, 1º de março de 2015, 01:00.
4. Agora suponha que essa execução demore mais de uma hora, por exemplo, 70 minutos, então ela será concluída aproximadamente às 2:10.
5. Agora são 2:00, hora da terceira execução começar. No entanto, como a segunda execução de 1h ainda está acontecendo, a terceira execução será ignorada. A terceira execução começa às 3h.

Você pode adicionar, alterar ou excluir uma agenda de um indexador existente usando uma solicitação de **indexador PUT** .

<a name="CaptureChangedRows"></a>

## <a name="capturing-new-changed-and-deleted-rows"></a>Captura de linhas novas, alteradas e excluídas
Se a tabela tiver muitas linhas, use uma política de detecção de alteração de dados. A detecção de alteração permite uma recuperação eficiente apenas das linhas novas ou alteradas sem precisar reindexar a tabela inteira.

### <a name="sql-integrated-change-tracking-policy"></a>Política de controle integrado de alterações do SQL
Se o banco de dados SQL oferece suporte ao [controle de alterações](https://msdn.microsoft.com/library/bb933875.aspx), recomendamos o uso da **Política de controle integrado de alterações do SQL**. Essa é a política mais eficiente. Além disso, ela permite que o Azure Search identifique linhas excluídas sem precisar adicionar uma coluna explícita de "exclusão reversível" à tabela.

O controle integrado de alterações tem suporte a partir das seguintes versões de banco de dados do SQL Server:

* SQL Server 2008 R2 e posterior, se você estiver usando o SQL Server em VMs do Azure.
* Banco de Dados SQL do Azure V12, se você estiver usando o Banco de Dados SQL do Azure.

Ao usar a política de controle integrado de alterações do SQL, não especifique uma política separada de detecção de exclusão de dados, pois essa política tem suporte interno para identificação de linhas excluídas.

Essa política só pode ser usada com tabelas; ela não pode ser usada com exibições. Você precisa habilitar o controle de alterações para a tabela que está usando antes de poder usar essa política. Confira [Habilitar e desabilitar o controle de alterações](https://msdn.microsoft.com/library/bb964713.aspx) para obter instruções.

Para usar essa política, crie ou atualize a fonte de dados da seguinte maneira:

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy"
      }
    }

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>Política de detecção de alteração de marca d’água alta
Enquanto a política de controle de alterações integrado do SQL é recomendada, ela só pode ser usada com tabelas, não com modos de exibição. Se você estiver usando um modo de exibição, considere o uso da política de marca d'água alta. Essa política poderá ser usada se a tabela ou o modo de exibição contiver uma coluna que atenda aos seguintes critérios:

* Todas as inserções especificam um valor para a coluna.
* Todas as atualizações de um item também alteram o valor da coluna.
* O valor dessa coluna aumenta com cada inserção ou atualização.
* Consultas com as cláusulas WHERE e ORDER BY a seguir podem ser executadas com eficiência: `WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`.

> [!IMPORTANT] 
> É altamente recomendável usar uma coluna **rowversion** para acompanhamento de alterações. Se qualquer outro tipo de dados for usado, não será possível assegurar a captura, pelo acompanhamento de alterações, de todas as alterações na presença de transações em execução simultaneamente com uma consulta do indexador.

Para usar uma política de marca d'água alta, crie ou atualize a fonte de dados da seguinte maneira:

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[a rowversion or last_updated column name]"
      }
    }

> [!WARNING]
> Se a tabela de origem não tiver um índice na coluna de marca d'água alta, as consultas usadas pelo indexador SQL poderão atingir o tempo limite. Em particular, a cláusula `ORDER BY [High Water Mark Column]` requer um índice para executar de forma eficiente quando a tabela contém muitas linhas.
>
>

Se você encontrar erros de tempo limite, use a definição da configuração do indexador `queryTimeout` para definir o tempo limite da consulta como um valor maior que o tempo limite padrão de 5 minutos. Por exemplo, para definir o tempo limite de 10 minutos, crie ou atualize o indexador com a seguinte configuração:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

Você também pode desabilitar a cláusula `ORDER BY [High Water Mark Column]`. No entanto, isso não é recomendado porque, se a execução do indexador for interrompida por um erro, o indexador deverá processar novamente todas as linhas se ele for executado mais tarde – mesmo se o indexador já tiver processado quase todas as linhas no momento em que foi interrompido. Para desabilitar a cláusula `ORDER BY`, use a configuração `disableOrderByHighWaterMarkColumn` na definição do indexador:  

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }

### <a name="soft-delete-column-deletion-detection-policy"></a>Política de detecção de exclusão de coluna por exclusão reversível
Quando as linhas são excluídas da tabela de origem, provavelmente você deseja excluí-las também do índice de pesquisa. Se você usar a política de controle integrado de alterações do SQL, isso será resolvido para você. No entanto, a política de controle de alterações de marca d'água alta não ajuda você com relação às linhas excluídas. O que fazer?

Se as linhas forem fisicamente removidas da tabela, o Azure Search não terá como inferir a presença de registros que não existem mais.  No entanto, você pode usar a técnica de "exclusão reversível" para excluir logicamente linhas sem removê-las da tabela. Adicione uma coluna à sua tabela ou exiba e marque as linhas como excluídas usando essa coluna.

Ao usar a técnica de exclusão reversível, você pode especificar a política de exclusão reversível da seguinte maneira ao criar ou atualizar a fonte de dados:

    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }

O **softDeleteMarkerValue** deve ser uma cadeia de caracteres – use a representação de cadeia de caracteres de seu valor real. Por exemplo, se você tiver uma coluna de inteiros na qual as linhas excluídas são marcadas com o valor 1, use `"1"`. Se você tiver uma coluna BIT na qual as linhas excluídas são marcadas com o valor true booliano, use `"True"`.

<a name="TypeMapping"></a>

## <a name="mapping-between-sql-data-types-and-azure-search-data-types"></a>Mapeamento entre tipos de dados SQL e tipos de dados da Pesquisa do Azure
| Tipo de dados SQL | Tipos de campos de índice de destino permitidos | Observações |
| --- | --- | --- |
| bit |Edm.Boolean, Edm.String | |
| int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String | |
| bigint |Edm.Int64, Edm.String | |
| real, float |Edm.Double, Edm.String | |
| smallmoney, numérico decimal dinheiro |Edm.String |O Azure Search não dá suporte à conversão de tipos decimais em Edm.Double porque isso causaria a perda de precisão |
| char, nchar, varchar, nvarchar |Edm.String<br/>Collection(Edm.String) |Uma cadeia de caracteres SQL poderá ser usada para preencher um campo Collection(Edm.String) se a cadeia de caracteres representar uma matriz JSON de cadeias de caracteres: `["red", "white", "blue"]` |
| smalldatetime, datetime, datetime2, date, datetimeoffset |Edm.DateTimeOffset, Edm.String | |
| uniqueidentifer |Edm.String | |
| geografia |Edm.GeographyPoint |Há suporte apenas para instâncias de Geografia do tipo POINT com SRID 4326 (que é o padrão) |
| rowversion |N/D |Colunas de linha-versão não podem ser armazenadas no índice de pesquisa, mas podem ser usadas para o controle de alterações |
| tempo, timespan, binário, varbinário, imagem, xml, geometria, tipos CLR |N/D |Sem suporte |

## <a name="configuration-settings"></a>Definições de configuração
O indexador do SQL expõe várias definições de configuração:

| Configuração | Tipo de dados | Finalidade | Valor padrão |
| --- | --- | --- | --- |
| queryTimeout |string |Define o tempo limite de execução da consulta SQL |5 minutos ("00:05:00") |
| disableOrderByHighWaterMarkColumn |bool |Faz com que a consulta SQL usada pela política de marca d'água alta omita a cláusula ORDER BY. Consulte [Política de marca d'água alta](#HighWaterMarkPolicy) |false |

Essas configurações são usadas no objeto `parameters.configuration` na definição do indexador. Por exemplo, para definir o tempo limite da consulta para 10 minutos, crie ou atualize o indexador com a seguinte configuração:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

## <a name="frequently-asked-questions"></a>Perguntas frequentes
**P:** Posso usar o indexador do SQL Azure com bancos de dados SQL em execução em VMs de IaaS no Azure?

R: Sim. No entanto, você precisa permitir que o serviço de pesquisa se conecte ao banco de dados. Para obter mais informações, consulte [Configurar uma conexão de um indexador do Azure Search ao SQL Server em uma VM do Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md).

**P:** Posso usar o indexador do SQL Azure com bancos de dados SQL em execução no local?

R: Não recomendamos ou damos suporte a isso, pois isso exigiria a abertura de seus bancos de dados ao tráfego da Internet.

**P:** Posso usar o indexador do SQL Azure com outros bancos de dados que não o SQL Server em execução em IaaS no Azure?

R: Não damos suporte a essa situação, pois não testamos o indexador com qualquer outro banco de dados que não o SQL Server.  

**P:** Posso criar vários indexadores em execução com base em uma agenda?

R: Sim. No entanto, somente um indexador pode ser executado por vez em um nó. Se você precisar de vários indexadores em execução simultaneamente, considere o aumento de seu serviço de pesquisa para mais de uma unidade de pesquisa.

**P:** executar um indexador afeta minha carga de trabalho de consulta?

R: Sim. O indexador é executado em um dos nós em seu serviço de pesquisa, e os recursos do nó são compartilhados entre a indexação e o atendimento ao tráfego de consultas e outras solicitações da API. Se você executar cargas de trabalho com indexação e consultas intensas e receber uma taxa alta de erros 503 ou tempos de resposta maiores, considere o aumento de seu serviço de pesquisa.

