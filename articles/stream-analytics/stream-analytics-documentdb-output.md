---
title: "Saída JSON para o Stream Analytics | Microsoft Docs"
description: "Saiba como o Stream Analytics pode direcionar o Banco de Dados de Documentos do Azure para saída em JSON, para arquivamento de dados e consultas de baixa latência em dados JSON não estruturados."
keywords: "Saída em JSON"
documentationcenter: 
services: stream-analytics,documentdb
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 5d2a61a6-0dbf-4f1b-80af-60a80eb25dd1
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/24/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 20880eccbf28cabfb594bb8129cb0a5a3beeb224
ms.openlocfilehash: e62e4f6c208f5506108b2ef5f6c1aabe43f086a2


---
# <a name="target-azure-documentdb-for-json-output-from-stream-analytics"></a>Direcionamento do Banco de Dados de Documentos do Azure para saída em JSON a partir do Stream Analytics
O Stream Analytics pode direcionar o [Banco de Dados de Documentos do Azure](https://azure.microsoft.com/services/documentdb/) para saída em JSON, possibilitando o arquivamento de dados e consultas de baixa latência em dados JSON não estruturados. Este documento aborda algumas práticas recomendadas para implementar essa configuração.

Para aqueles que não estão familiarizados com o Banco de Dados de Documentos, veja [Roteiro de aprendizagem do Banco de Dados de Documentos](https://azure.microsoft.com/documentation/learning-paths/documentdb/) para obter uma introdução.

## <a name="basics-of-documentdb-as-an-output-target"></a>Noções básicas do Banco de Dados de Documentos como um destino de saída
A saída do Banco de Dados de Documentos do Azure no Stream Analytics permite a gravação dos resultados de seu processamento de fluxo como uma saída em JSON em suas coleções do Banco de Dados de Documentos. O Stream Analytics não cria coleções em seu banco de dados, em vez disso, ele exige a criação delas antecipadamente. Isso serve para que os custos de cobrança de coleções de Banco de Dados de Documentos sejam transparentes, e para que você possa ajustar o desempenho, a consistência e a capacidade de suas coleções diretamente com as [APIs do Banco de Dados de Documentos](https://msdn.microsoft.com/library/azure/dn781481.aspx). Recomendamos o uso de um banco de dados do Banco de Dados de Documentos por trabalho de transmissão a fim de separar logicamente suas coleções de acordo com o trabalho de transmissão.

Algumas das opções de coleção de Banco de Dados de Documentos estão detalhadas abaixo.

## <a name="tune-consistency-availability-and-latency"></a>Ajustar a consistência, a disponibilidade e a latência
Para atender aos seus requisitos de aplicativo, o Banco de Dados de Documentos permite o ajuste do Banco de dados e das Coleções, e compensações entre consistência, disponibilidade e latência. Dependendo dos níveis de consistência de leitura exigidos pelo seu cenário em relação à latência de leitura e de gravação, você poderá escolher um nível de consistência em sua conta de banco de dados. Também por padrão, o Banco de Dados de Documentos permite a indexação síncrona em cada operação CRUD para sua coleção. Essa é outra opção útil para controlar o desempenho de leitura/gravação no Banco de Dados de Documentos. Para saber mais sobre esse tópico, confira o artigo [Alterar os níveis de consistência do banco de dados e de consulta](../documentdb/documentdb-consistency-levels.md) .

## <a name="choose-a-performance-level"></a>Escolher um nível de desempenho
As coleções do Banco de Dados de Documentos podem ser criadas em três níveis de desempenho diferentes (S1, S2 ou S3), e isso determinará a taxa de transferência disponível para CRUDs dessa coleção. Além disso, o desempenho é afetado pelos níveis de indexação/consistência em sua coleção. Confira [este artigo](../documentdb/documentdb-performance-levels.md) para entender esses níveis de desempenho com mais detalhes.

## <a name="upserts-from-stream-analytics"></a>Inserções e atualizações a partir do Stream Analytics
A integração do Stream Analytics com o DocumentDB permite inserir ou atualizar registros em sua coleção do DocumentDB com base em uma determinada coluna de ID do Documento. Isso também é chamado de *Upsert*.

O Stream Analytics utiliza uma abordagem Upsert otimista, na qual as atualizações são feitas somente quando a inserção falha devido a um conflito de ID do Documento. Essa atualização é executada pelo Stream Analytics como um PATCH. Assim, é possível realizar atualizações parciais no documento, ou seja, a adição de novas propriedades ou a substituição de uma propriedade existente é executada de forma incremental. Observe que as alterações nos valores de propriedades de matriz em seu documento JSON resultam na substituição de toda a matriz, ou seja, a matriz não é mesclada.

## <a name="data-partitioning-in-documentdb"></a>Particionamento de dados no Banco de Dados de Documentos
Agora há suporte para Coleções Particionadas do DocumentDB e essa é a abordagem recomendada para particionar seus dados. 

Para coleções únicas do DocumentDB, o Stream Analytics ainda permite particionar seus dados com base nos padrões de consulta e nas necessidades de desempenho do seu aplicativo. Cada coleção pode conter até 10 GB de dados (máximo) e, atualmente, não há uma maneira de escalar verticalmente (ou estourar) uma coleção. Para dimensionamento, o Stream Analytics permite que você grave várias coleções com um determinado prefixo (veja os detalhes de uso abaixo). O Stream Analytics usa a estratégia de [Resolvedor de Partição Hash](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) consistente com base na coluna PartitionKey fornecida pelo usuário para particionar seus registros de saída. O número de coleções com o prefixo especificado na hora de início do trabalho de streaming é usado como a contagem de partições de saída, nas quais o trabalho grava em paralelo (coleções do Banco de Dados de Documentos = Partições de saída). Para uma única coleção S3 com a indexação lenta realizando apenas inserções, é possível esperar uma produtividade de gravação de cerca de 0,4 MB/s. O uso de várias coleções permite alcançar maior produtividade e maior capacidade.

Se você pretender aumentar a contagem de partições no futuro, talvez seja necessário interromper seu trabalho, reparticionar os dados de suas coleções existentes em novas coleções e reiniciar o trabalho do Stream Analytics. Incluiremos mais detalhes sobre como usar PartitionResolver e o novo particionamento, junto com um exemplo de código, em uma próxima postagem. O artigo [Particionamento e dimensionamento no Banco de Dados de Documentos do Azure](../documentdb/documentdb-partition-data.md) também apresenta detalhes sobre isso.

## <a name="documentdb-settings-for-json-output"></a>Configurações do Banco de Dados de Documentos para saída em JSON
A criação de Banco de Dados de Documentos como uma saída no Stream Analytics gera uma solicitação de informações, conforme mostra abaixo. Esta seção fornece uma explicação da definição de propriedades.

Coleção particionada | Várias coleções de “partição única”
---|---
![tela de saída do stream analytics do banco de dados de documentos](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png) |  ![tela de saída do stream analytics do banco de dados de documentos](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)


  
> [!NOTE]
> O cenário **Várias coleções de “partição única”** requer uma chave de partição e é uma configuração com suporte. 

* **Alias de Saída** : um alias para se referir a essa saída em sua consulta ASA  
* **Nome da Conta** : o nome ou URI do ponto de extremidade da conta do Banco de Dados de Documentos.  
* **Chave da Conta** : a chave de acesso compartilhado para a conta do Banco de Dados de Documentos.  
* **Banco de dados** : o nome do banco de dados do Banco de Dados de Documentos.  
* **Padrão do Nome de Coleção** – O nome da coleção ou seu padrão que será usado para as coleções. O formato de nome da coleção pode ser construído com o token {partição} opcional, em que as partições começam em 0. A seguir estão as entradas válidas de exemplo:  
  1\) MyCollection – uma coleção denominada “MyCollection” deve existir.  
  2\) MyCollection{partition} – estas coleções devem existir – "MyCollection0”, “MyCollection1”, “MyCollection2” e assim por diante.  
* **Chave de Partição** — opcional. Isso só será necessário se você estiver usando um token {partition} no seu padrão de nome de coleção. O nome do campo nos eventos de saída usado para especificar a chave para o particionamento de saída em várias coleções. Para uma saída de coleção única, nenhuma coluna de saída arbitrária pode ser usada, por exemplo, PartitionId.  
* **ID do Documento** : opcional. O nome do campo em eventos de saída usado para especificar a chave primária que serve de base para as operações de inserção ou atualização.  



<!--HONumber=Jan17_HO1-->


