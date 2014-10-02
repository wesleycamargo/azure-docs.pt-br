<properties title="Interact with DocumentDB resources" pageTitle="Interact with DocumentDB resources | Azure" description="DocumentDB manages resources--uniquely identified by logical URIs--that developers can interact with using HTTP verbs, request/response headers, and status codes." metaKeywords="" services="documentdb" solutions="data-management"  authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="bradsev"></tags>

# Interagir com recursos do Banco de Dados de Documentos

O Banco de Dados de Documentos oferece um modelo de programação RESTful simples e aberto em HTTP. Na versão de teste, o Banco de Dados de Documentos oferece ao cliente SDKs para .NET, Python, Node.js e JavaScript – todos são wrappers simples nas APIs REST. Em versões futuras, ele também fornecerá SDKs para C++ e Java. Incentivamos você a gravar suas próprias SDKs para seu ambiente de programação específico e compartilhá-las com a comunidade já que abrimos nossas SDKs.

> [AZURE.NOTE] Além disso, ele também oferece um protocolo TCP altamente eficiente que também possui o modelo de comunicação RESTful e está disponível por meio do SDK do cliente .NET.

## Recursos

As entidades que o Banco de Dados de Documentos gerencia são referidas como **recursos**, que são identificados exclusivamente pelo seu URI lógico. Os desenvolvedores podem interagir com os recursos usando verbos de HTTP padrão, cabeçalhos de solicitação/resposta e códigos de status. Como ilustrado pelo diagrama a seguir, o **modelo de recursos** do Banco de Dados de Documentos é formado por conjuntos de recursos em uma conta do banco de dados, cada um podendo ser acessado por meio de um URI lógico e estável. Um conjunto de recursos é referido como um **feed** neste documento.

![][]

## Modelo de recurso hierárquico em uma conta de banco de dados

Como cliente do Banco de Dados de Documentos, comece provisionando uma **conta de banco de dados** do Banco de Dados de Documentos usando sua assinatura do Azure. Uma conta do banco de dados é formada por um conjunto de **bancos de dados**, cada um contendo diversas **coleções**, cada uma delas, por sua vez, contendo **procedimentos armazenados, gatilhos, UDFs, documentos** e anexos **relacionados**. Um banco de dados também possui **usuários** associados, cada um com um conjunto de **permissões** para acessar várias outras coleções, procedimentos armazenados, gatilhos, UDFs, documentos ou anexos. Enquanto bancos de dados, usuários, permissões e coleções são recursos definidos pelo sistema com esquemas bastante conhecidos, os documentos e anexos possuem conteúdo JSON arbitrário e definido pelo usuário.

| Recurso                 | Descrição                                                                                                                                                                                                                                                                                                    |
|-------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Conta de banco de dados | Uma conta de banco de dados é associada a uma ou mais unidades de capacidade representando o armazenamento e a taxa de transferência do documento provisionado, um conjunto de bancos de dados e armazenamento de blob. Você pode criar uma ou mais contas do banco de dados usando sua assinatura do Azure. |
| Banco de dados          | Um banco de dados é um contêiner lógico de armazenamento de documentos particionado em coleções. Ele também é um contêiner para usuários.                                                                                                                                                                    |
| Usuário                 | O namespace lógico para obter o escopo/particionamento das permissões.                                                                                                                                                                                                                                       |
| Permissão               | Um token de autorização associado a um usuário para acesso autorizado a um recurso específico.                                                                                                                                                                                                               |
| Coleção                 | Uma coleção é um contêiner de documentos JSON e uma lógica de aplicativo JavaScript associada.                                                                                                                                                                                                               |
| Procedimento armazenado | Lógica de aplicativo gravada no JavaScript que é registrada com uma coleção e executada de forma transacional dentro do mecanismo do banco de dados.                                                                                                                                                         |
| Gatilho                 | Lógica de aplicativo gravada no JavaScript modelando efeitos colaterais associados a uma operação de inserção, substituição ou exclusão.                                                                                                                                                                     |
| UDF                     | Uma lógica de aplicativo livre de efeito colateral gravada em JavaScript. As UDFs permitem modelar um operador de consulta personalizada e, portanto, estender a linguagem de consulta principal do Banco de Dados de Documentos.                                                                            |
| Documento               | Conteúdo JSON (arbitrário) definido pelo usuário. Por padrão, nenhum esquema precisa ser definido nem índices secundários precisam ser fornecidos para todos os documentos adicionados à coleção.                                                                                                            |
| Anexo                   | Anexos são documentos especiais contendo referências e metadados associados a um blob/uma mídia externo/a. O desenvolvedor pode optar por ter o blob gerenciado pelo Banco de Dados de Documentos ou armazená-lo com um provedor de serviços de blob externo, como OneDrive, Dropbox, etc.                   |

### Recursos definidos pelo sistema x usuário

Recursos como contas do banco de dados, bancos de dados, coleções, usuários, permissões, procedimentos armazenados, gatilhos e UDFs, todos possuem um esquema fixo e são chamados de *recursos do sistema*. Em contraste, recursos como documentos e anexos não possuem restrições sobre o esquema e são exemplos de *recursos definidos pelo usuário*. No Banco de Dados de Documentos, ambos os recursos definidos pelo sistema e pelo usuário são representados e gerenciados como JSON em conformidade com o padrão. Todos os recursos, definidos pelo usuário ou pelo sistema, possuem as seguintes propriedades em comum.

> [AZURE.NOTE] Observe que todas as propriedades geradas pelo sistema em um recurso têm como prefixo um sublinhado (\_) na sua representação JSON.

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left"><p><strong>Propriedade</strong></p></td>
<td align="left"><p><strong>Configurável pelo usuário ou gerada pelo sistema?</strong></p></td>
<td align="left"><p><strong>Finalidade</strong></p></td>
</tr>
<tr class="even">
<td align="left"><p>_rid</p></td>
<td align="left"><p>Gerada pelo sistema</p></td>
<td align="left"><p>Identificador hierárquico e exclusivo do recurso, gerado pelo sistema</p></td>
</tr>
<tr class="odd">
<td align="left"><p>_etag</p></td>
<td align="left"><p>Gerada pelo sistema</p></td>
<td align="left"><p>etag do recurso necessário para controle de concorrência otimista</p></td>
</tr>
<tr class="even">
<td align="left"><p>_ts</p></td>
<td align="left"><p>Gerada pelo sistema</p></td>
<td align="left"><p>Último carimbo de data/hora atualizado do recurso</p></td>
</tr>
<tr class="odd">
<td align="left"><p>_self</p></td>
<td align="left"><p>Gerada pelo sistema</p></td>
<td align="left"><p>URI endereçável exclusivo do recurso</p></td>
</tr>
<tr class="even">
<td align="left"><p>ID</p></td>
<td align="left"><p>Configurável pelo usuário</p></td>
<td align="left"><p>Nome exclusivo do recurso definido pelo usuário</p></td>
</tr>
</tbody>
</table>

### Representação da conexão dos recursos

O Banco de Dados de Documentos não obriga nenhuma extensão proprietária para o padrão JSON nem codificações especiais; ele trabalha com documentos JSON compatíveis padrão.

### Endereçamento de um recurso

Todos os recursos são endereçáveis pelo URI. O valor da propriedade \*\*\_self\*\* de um recurso representa o URI relativo do recurso. O formato do URI consiste nos segmentos do caminho /<feed>/{\_rid}:

|Valor da \_self |Descrição
|-------------------|-----------
|/dbs |feed de banco de dados em uma conta de banco de dados
|/dbs/{\_rid-db} |Banco de dados com a propriedade de ID exclusiva com o valor {\_rid-db}
|/dbs/{\_rid-db}/colls/ |feed de coleções em um banco de dados
|/dbs/{\_rid-db}/colls/{\_rid-coll} |Coleção com a propriedade de ID exclusiva com o valor {\_rid-coll}
|/dbs/{\_rid-db}/users/ |feed de usuários em um banco de dados
|/dbs/{\_rid-db}/users/{\_rid-user} |Usuário com a propriedade de ID exclusiva com o valor {\_rid-user}
|/dbs/{\_rid-db}/users/{\_rid-user}/permissions |feed de permissões em um banco de dados
|/dbs/{\_rid-db}/users/{\_rid-user}/permissions/{\_rid-permission} |Permissão com a propriedade de ID exclusiva com o valor {\_rid-permission}

Um recurso também possui um nome exclusivo definido pelo usuário exposto pela propriedade de ID do recurso. A ID é uma cadeia de caracteres longa, e definida pelo usuário, com até 256 caracteres, que é exclusiva dentro do contexto de um recurso pai específico. Por exemplo, o valor da propriedade de ID de todos os documentos dentro de uma determinada coleção é exclusivo, mas não é garantido que seja exclusivo nas coleções. Do mesmo modo, o valor da propriedade de ID de todas as permissões para um determinado usuário é exclusivo, mas não é garantido que seja exclusivo em todos os usuários. A propriedade \_rid é usada para construir o link endereçável \_self de um recurso.

Cada recurso também tem um identificador de recurso hierárquico gerado pelo sistema (também chamado de RID) que está disponível pela propriedade \_rid. O RID codifica a hierarquia inteira de um determinado recurso, sendo uma representação interna muito conveniente usada para impor integridade referencial de maneira distribuída. O RID é exclusivo dentro de uma conta de banco de dados, sendo usado internamente pelo Banco de Dados de Documentos para roteamento eficiente sem exigir pesquisas entre partições.

Os valores das propriedades \_self e \_rid são representações alternativas e canônicas de um recurso.

## Modelo de interação básico

Os recursos suportam os seguintes verbos HTTP com sua interpretação padrão:

> [AZURE.NOTE] No futuro, pretendemos adicionar suporte para atualizações seletivas via PATCH

1.  POST significa criar um novo recurso de item.
2.  GET significa ler um item existente ou um recurso de feed
3.  PUT significa substituir um recurso de item existente
4.  DELETE significa excluir um item de recurso existente
5.  HEAD significa GET sem a carga de resposta (ou seja, apenas os cabeçalhos)

Em versões futuras, os recursos de item também serão compatíveis com o verbo PATCH.

Conforme ilustrado na figura abaixo, POST só pode ser executado em um recurso Feed; PUT, DELETE só podem ser executados em um recurso Item; GET e HEAD podem ser executados em recursos Feed ou Item.

![][1]

**Modelo de interação usando os verbos HTTP padrão**

Para entender melhor o modelo de interação, vamos considerar o caso de criar um novo recurso (também conhecido como INSERT). Para criar um novo recurso, você precisa emitir uma solicitação HTTP POST com o corpo da solicitação contendo a representação do recurso em relação ao URI do feed do contêiner ao qual o recurso pertence. A única propriedade exigida para a solicitação é a ID do recurso.

Como exemplo, para criar um novo banco de dados, você usa POST em um recurso do banco de dados (configurando a propriedade de ID com um nome exclusivo) no /dbs. Do mesmo modo, para criar uma nova coleção, você usa POST em um recurso da coleção no /dbs/\_rid/colls/ e assim por diante. A resposta conterá o recurso totalmente confirmado com as propriedades geradas pelo sistema, incluindo o link \_self do recurso usando o que você pode navegar para outros recursos. Como exemplo do modelo de interação baseado no HTTP simples, um cliente pode emitir uma solicitação HTTP para criar um novo banco de dados dentro de uma conta:

    POST http://fabrikam.documents.azure.net/dbs
    {
          "id":"MyDb"
    }

O Banco de Dados de Documentos responde com uma resposta de êxito e um código de status indicando a criação bem-sucedida do banco de dados.

    [201 Created]
    {
          "id": "MyDb",
          "_rid": "UoEi5w==",
          "_self": "dbs/MyDb/",
          "_ts": 1407370063,
          "_etag": "\"00002100-0000-0000-0000-50e71fda0000\"",
          "_colls": "colls/",
          "_users": "users/"
    }

O serviço do Banco de Dados de Documentos responde com uma resposta de êxito e um código de status indicando a criação bem-sucedida do banco de dados.

Como outro exemplo de criação e execução de um recurso, considere o seguinte procedimento armazenado gravado inteiramente em JavaScript.

    function sproc(docToCreate, addedPropertyName, addedPropertyValue) {
        var collectionManager = getContext().getCollection();
        collectionManager.createDocument(collectionManager.getSelfLink(), docToCreate,   
        function(err, docCreated) {
            if(err) throw new Error('Error while creating document: ' + err.message);
            
            docCreated.addedPropertyName = addedPropertyValue;
            getContext().getResponse().setBody(docCreated);
        });
    }

O procedimento armazenado pode ser registrado em uma coleção em MyDb executando um POST no /dbs/\_rid-db/colls/\_rid-coll/sprocs.

    POST /dbs/MyDb/colls/MyColl/sprocs HTTP/1.1

    {
      "id": "sproc1",
      "body": "function (docToCreate, addedPropertyName, addedPropertyValue) {
                    var collectionManager = getContext().getCollection();
                    collectionManager.createDocument(collectionManager.getSelfLink(), 
                                docToCreate, function(err, docCreated) {
                        if(err) throw new Error('Error while creating document: ' + 
                                                         err.message);
                        
                        docCreated.addedPropertyName = addedPropertyValue;
                        getContext().getResponse().setBody(docCreated);
                    });
                }"
    }

O serviço do Banco de Dados de Documentos responde com uma resposta de êxito e um código de status indicando o registro bem-sucedido do procedimento armazenado.

    [201 Created]
    {
          "id": "sproc1",
          "_rid": "EoEi5w==",
          "_self": "dbs/MyDb/colls/MyColl/sprocs/sproc1",
          "_etag": "\"00002100-0000-0000-0000-50f71fda0000\"",
           ...
    }

Por fim, para executar o procedimento armazenado no exemplo acima, é necessário executar um POST no URI do recurso do procedimento armazenado (/dbs/\_rid-db/colls/\_rid-coll/sprocs/sproc1). Isso é ilustrado abaixo:

    POST /dbs/MyDb/colls/MyColl/sprocs/sproc1 HTTP/1.1
     [ { "id": "TestDocument", "book": "Autumn of the Patriarch"}, "Price", 200 ]

O serviço do Banco de Dados de Documentos responderia com o seguinte:

    HTTP/1.1 200 OK
     { 
      "id": "TestDocument",  
      "_rid": "ZTlcANiwqwIBAAAAAAAAAA==",
      "_ts": 1407370063,
      "_self": "dbs/ZTlcAA==/colls/ZTlcANiwqwI=/docs/ZTlcANiwqwIBAAAAAAAAAA==/",
      "_etag": "00000900-0000-0000-0000-53e2c34f0000",
      "_attachments": "attachments/",
      "book": "Autumn of the Patriarch", 
      "price": 200
    }

Observe que o verbo POST é usado para criação de um novo recurso, para executar um procedimento armazenado ou para emitir uma consulta SQL. Para ilustrar a execução da consulta SQL, considere o seguinte:

    POST /dbs/MyDb/colls/MyColl/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/sql

    SELECT * FROM root.children

O serviço responde com os resultados da consulta SQL.

    HTTP/1.1 200 Ok
    x-ms-activity-id: 83f9992c-abae-4eb1-b8f0-9f2420c520d2
    x-ms-item-count: 2
    x-ms-session-token: 81
    x-ms-request-charge: 1.43
    Content-Length: 287

    {"_rid":"sehcAIE2Qy4=","Documents":[[{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}],[{"familyName":"Merriam","givenName":"Jesse","gender":"female","grade":1},{"familyName":"Miller","givenName":"Lisa","gender":"female","grade":8}]],"count":2}

Substituir ou ler uma quantidade de recursos para executar verbos PUT (com um corpo de solicitação válido) e GET no link \_self do recurso, respectivamente. Do mesmo modo, excluir uma quantidade de recursos para executar um verbo DELETE no link \_self do recurso. É válido destacar que a organização hierárquica de recursos no modelo de recursos do Banco de Dados de Documentos necessita de suporte para exclusões em cascata, em que a exclusão do recurso proprietário causa a exclusão dos recursos dependentes. Os recursos dependentes podem ser distribuídos em outros nós diferentes dos recursos proprietários e, desse modo, a exclusão pode ocorrer lentamente. Independentemente da mecânica da coleta de lixo, ao excluir um recurso, a cota é liberada instantaneamente e fica disponível para uso. Observe que a integridade referencial é preservada pelo sistema. Por exemplo, você não pode inserir uma coleção em um banco de dados que é excluído ou substituído nem consultar um documento de uma coleção que não existe mais.

A execução de um GET em um feed (de) recursos ou a consulta de uma coleção pode, potencialmente, resultar em milhões de itens, tornando impraticável para o servidor materializá-los e para os clientes consumi-los como parte de uma única viagem de ida e volta/solicitação e troca de resposta. Para lidar com isso, o Banco de Dados de Documentos permite aos clientes paginar pelo feed grande com uma página por vez. Os clientes podem usar o cabeçalho de resposta [x-ms-continuationToken] como um cursor para navegar até a próxima página.

## Controle de simultaneidade otimista

A maioria dos aplicativos Web confia na marca da entidade baseada no Controle de Concorrência Otimista para evitar os problemas infames de “Atualização Perdida” e “Exclusão Perdida”. A marca da entidade é um carimbo de data/hora lógico e amigável para HTTP, associado a um recurso. O Banco de Dados de Documentos suporta nativamente o controle de concorrência otimista, garantindo que cada resposta HTTP contenha a versão (permanente) associada ao recurso específico. Os conflitos de controle de concorrência são detectados corretamente para os seguintes casos:

1.  Se dois clientes emitirem simultaneamente solicitações mutantes (através dos verbos PUT/DELETE) em um recurso com a versão mais recente do recurso (especificada através do cabeçalho da solicitação [if-match]), o mecanismo do Banco de Dados de Documentos sujeita-os ao controle de concorrência transacional.
2.  Se um cliente apresentar uma versão mais antiga do recurso (especificada pelo cabeçalho da solicitação [if-match]), a solicitação será rejeitada.

## Opções de conectividade

O Banco de Dados de Documentos expõe um modelo de endereçamento lógico, em que cada recurso tem um URI lógico e estável identificado pelo link \_self. Como um sistema de armazenamento distribuído espalha-se pelas regiões, os recursos em várias contas do banco de dados no Banco de Dados de Documentos são particionados em inúmeros computadores e cada partição é replicada para alta disponibilidade. As réplicas que gerenciam os recursos de uma determinada partição registram endereços físicos. Enquanto os endereços físicos mudam com o tempo devido a falhas, seus endereços lógicos permanecem estáveis e constantes. A conversão do endereço lógico em físico é mantida em uma tabela de roteamento que também está disponível internamente como um recurso. O Banco de Dados de Documentos expõe dois modos de conectividade:

1.  **Modo de Gateway:** Os clientes são protegidos da conversão entre endereços lógicos para físicos ou os detalhes do roteamento; eles lidam apenas com URIs lógicos e navegam de maneira RESTful pelo modelo do recurso. Os clientes emitem as solicitações usando o URI lógico e os computadores de borda convertem o URI lógico para o endereço físico da réplica que gerencia o recurso e encaminha a solicitação. Com os computadores de borda fazendo caching (e atualização periódica) da tabela de roteamento, o roteamento é extremamente eficiente.
2.  **Modo de Conectividade direta:** Os clientes gerenciam diretamente a tabela de roteamento no seu espaço de processamento e atualizam-na periodicamente. O cliente pode conectar-se diretamente a réplicas e ignorar os computadores de borda.

<table>
<colgroup>
<col width="25%" />
<col width="25%" />
<col width="25%" />
<col width="25%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left"><p><strong>Modo de conectividade</strong></p></td>
<td align="left"><p><strong>Protocolo</strong></p></td>
<td align="left"><p><strong>Detalhes</strong></p></td>
<td align="left"><p><strong>SDKs do Banco de Dados de Documentos</strong></p></td>
</tr>
<tr class="even">
<td align="left"><p>Gateway</p></td>
<td align="left"><p>HTTPS</p></td>
<td align="left"><p>Os aplicativos se conectam diretamente aos nós de borda usando URIs lógicos. Isso implica um salto de rede extra.</p></td>
<td align="left"><p>APIs REST</p>
<p>.NET, JavaScript, Node.js, Python</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Conectividade direta</p></td>
<td align="left"><p>HTTPS e</p>
<p>TCP</p></td>
<td align="left"><p>Os aplicativos podem acessar diretamente a tabela de roteamento e executar o roteamento do lado do cliente para se conectar diretamente às réplicas.</p></td>
<td align="left"><p>.NET</p></td>
</tr>
</tbody>
</table>

## Referências

-   REST [][]<http://en.wikipedia.org/wiki/Representational_state_transfer></a>
-   Especificação JSON [][2]<http://-www.ietf.org/rfc/rfc4627.txt></a>
-   Especificação HTTP [][3]<http://www.w3.org/Protocols/rfc2616/rfc2616.html></a>
-   Marcas da entidade [][4]<http://en.wikipedia.org/wiki/HTTP_ETag></a>
-   [Banco de Dados de Documentos de consulta][]
-   [Referência SQL do Banco de Dados de Documentos][]
-   [Programação do Banco de Dados de Documentos: Procedimentos armazenados, gatilhos e UDFs][]
-   [Documentação de referência do Banco de Dados de Documentos][]

  []: ./media/documentdb-interactions-with-resources/interactions-with-resources1.png
  [1]: ./media/documentdb-interactions-with-resources/interactions-with-resources2.png
  []: http://en.wikipedia.org/wiki/Representational_state_transfer
  [2]: http://-www.ietf.org/rfc/rfc4627.txt
  [3]: http://www.w3.org/Protocols/rfc2616/rfc2616.html
  [4]: http://en.wikipedia.org/wiki/HTTP_ETag
  [Banco de Dados de Documentos de consulta]: /documentation/articles/documentdb-sql-query/
  [Referência SQL do Banco de Dados de Documentos]: http://go.microsoft.com/fwlink/p/?LinkID=510612
  [Programação do Banco de Dados de Documentos: Procedimentos armazenados, gatilhos e UDFs]: /documentation/articles/documentdb-programming/
  [Documentação de referência do Banco de Dados de Documentos]: http://go.microsoft.com/fwlink/p/?LinkID=402384
