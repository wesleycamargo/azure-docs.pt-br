---
redirect_url: https://azure.microsoft.com/services/documentdb/
ROBOTS: NOINDEX, NOFOLLOW
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c1547d9910ce2bbfda79718ba74cdd8e7ca468f5
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017



---
# <a name="automatic-indexing-in-azure-cosmos-db"></a>Indexação automática no Azure Cosmos DB
Este artigo foi extraído do documento [“Indexação independente de esquema com o Azure Cosmos DB](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)”, que será apresentado na [41ª Conferência Interna sobre Bancos de Dados Muito Grandes](http://www.vldb.org/2015/), realizada de 31 de agosto a 4 de setembro de 2015 e é uma introdução ao funcionamento da indexação no Azure Cosmos DB. 

Depois de ler este artigo, você poderá responder às seguintes perguntas:

* Como o Azure Cosmos DB infere o esquema de um documento JSON?
* Como o Azure Cosmos DB cria um índice em documentos diferentes?
* Como o Azure Cosmos DB executa a indexação automática em escala?

## <a id="HowDocumentDBIndexingWorks"></a> Funcionamento da indexação do Banco de Dados de Documentos
O [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) é um verdadeiro banco de dados sem esquemas desenvolvido especificamente para o JSON. Ele não espera ou exige qualquer esquema ou definições de índice secundárias para indexar dados em escala. Isso permite que você defina rapidamente e itere em modelos de dados de aplicativo usando o Azure Cosmos DB. Ao adicionar documentos a uma coleção, o Azure Cosmos DB indexa todas as propriedades de documento automaticamente, de forma que elas fiquem disponíveis para consulta. A indexação automática permite que você armazene documentos pertencentes a esquemas completamente arbitrários sem se preocupar com esquemas ou índices secundários.

Com a meta de eliminar a incompatibilidade de impedância entre o banco de dados e os modelos de programação de aplicativo, o Azure Cosmos DB explora a simplicidade do JSON e sua ausência de uma especificação de esquema. Ele não faz nenhuma suposição sobre os documentos e permite que os documentos de uma coleção do Azure Cosmos DB variem no esquema, além de em valores específicos à instância. Em comparação com outros bancos de dados de documentos, o mecanismo de banco de dados do Azure Cosmos DB opera diretamente no nível da gramática JSON, permanecendo independente em relação ao conceito de um esquema de documento e obscurecendo o limite entre os valores de instância e de estrutura de documentos. Isso, por sua vez, permite que ele indexe automaticamente documentos sem exigir um esquema ou índices secundários.

A indexação do Azure Cosmos DB aproveita o fato de que a gramática JSON permite que os documentos sejam **representados como árvores**. Para um documento JSON ser representado como uma árvore, precisa ser criado um nó raiz fictício que seja pai do restante de nós reais no documento abaixo. Cada rótulo, inclusive os índices da matriz em um documento JSON torna-se um nó da árvore. A figura a seguir ilustra um documento JSON de exemplo e sua representação de árvore correspondente.

> [!NOTE]
> Como o JSON é autodescritivo, ou seja, cada documento inclui o esquema (metadados) e os dados; por exemplo, o `{"locationId": 5, "city": "Moscow"}` revela que há duas propriedades `locationId` e `city`, e que elas têm valores de propriedade numéricos e de cadeia de caracteres. O Azure Cosmos DB pode inferir o esquema de documentos e indexá-los quando são inseridos ou substituídos, sem a necessidade de definir esquemas ou índices secundários.
> 
> 

**Documentos JSON como árvores:**

![Documentos como árvores](media/documentdb-indexing/DocumentsAsTrees.png)

No exemplo mostrado acima:

* A propriedade JSON `{"headquarters": "Belgium"}` no exemplo acima corresponde ao caminho /headquarters/Belgium.
* A matriz JSON `{"exports": [{"city": “Moscow"}`, `{"city": Athens"}]}` corresponde aos caminhos `/exports/[]/city/Moscow` e `/exports/[]/city/Athens`.

Com a indexação automática, (1) todos os caminhos em uma árvore de documentos são indexados (a menos que o desenvolvedor tenha configurado explicitamente a política de indexação para excluir determinados padrões de caminho). (2) Cada atualização de um documento em uma coleção do Azure Cosmos DB leva a uma atualização da estrutura do índice (ou seja, causa a adição ou a remoção de nós). Um dos principais requisitos da indexação automática de documentos é garantir que o custo de indexar e de consultar um documento com estrutura profundamente aninhada, digamos, 10 níveis, seja igual ao de um documento JSON simples que consista em pares de chave-valor com apenas um nível de profundidade. Portanto, uma representação de caminho normalizado é a fundação sobre a qual ambos os subsistemas de indexação e de consulta automáticos são criados.

Uma implicação importante do tratamento uniforme de ambos os valores de instância e de esquema em termos de caminhos é que, logicamente, apenas os documentos individuais, um índice dos dois documentos mostrados que mantém um mapa entre os caminhos e as ids do documento com esse caminho também podem ser representados como uma árvore. O Azure Cosmos DB usa esse fato para criar uma árvore de índice construída da união de todas as árvores que representam documentos individuais na coleção. A árvore de índice nas coleções do Azure Cosmos DB cresce ao longo do tempo conforme novos documentos são adicionados à coleção ou atualizados.

**Índice do Azure Cosmos DB como uma Árvore:**

![Índice como uma árvore](media/documentdb-indexing/IndexAsTree.png)

Apesar de não terem esquemas, as linguagens de consulta SQL e JavaScript do Azure Cosmos DB fornecem projeções e filtros relacionais, navegação hierárquica entre documentos, operações espaciais e invocação de UDFs totalmente escritas em JavaScript. O tempo de execução da consulta do Azure Cosmos DB pode dar suporte a essas consultas, já que consegue operar diretamente nessa representação de árvore de índice dos dados.

A política de indexação padrão indexa automaticamente todas as propriedades de todos os documentos e oferece consultas consistentes (o que significa que o índice é atualizado de forma síncrona com a gravação de documento). Como o Azure Cosmos DB dá suporte a atualizações consistentes na árvore de índice em escala? O Azure Cosmos DB usa técnicas otimizadas de manutenção de índice livre de bloqueio e estruturada para log. Isso significa que o Azure Cosmos DB pode dar suporte a um volume sustentado de gravações rápidas e ainda oferecer consultas consistentes. 

A indexação do Azure Cosmos DB foi projetada para eficiência de armazenamento e para lidar com a multilocação. Para manter um bom custo-benefício, a sobrecarga do armazenamento em disco do índice é baixa e previsível. As atualizações de índice também são realizadas dentro do orçamento dos recursos do sistema alocados por coleção do Azure Cosmos DB.

## <a name="NextSteps"></a> Próximas etapas
* Baixe [“Indexação independente de esquema com o Azure Cosmos DB”](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), a ser apresentado na 41ª Conferência Interna sobre Bancos de Dados Muito Grandes, realizada de 31 de agosto a 4 de setembro de 2015.
* [Consulta com SQL do Banco de Dados de Documentos](documentdb-sql-query.md)
* Saiba mais sobre como personalizar o índice do Azure Cosmos DB [aqui](documentdb-indexing-policies.md)


