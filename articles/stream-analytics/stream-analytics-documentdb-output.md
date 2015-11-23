<properties 
	pageTitle="Detalhes de saída do Banco de Dados de Documentos | Microsoft Azure" 
	description="Banco de Dados de Documentos como saída do Stream Analytics do Azure"
	keywords="saída do banco de dados de documentos, saída do docdb, stream analytics e banco de dados de documentos"
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="11/06/2015" 
	ms.author="jeffstok"/>

# Banco de Dados de Documentos como saída do Stream Analytics do Azure

Agora o Stream Analytics do Azure oferece suporte ao [Banco de Dados de Documentos do Azure](http://azure.microsoft.com/services/documentdb/) como saída, possibilitando o arquivamento de dados e consultas de baixa latência em dados JSON não estruturados. Este documento descreve como implementar essa integração da melhor maneira. Para aqueles que não estão familiarizados com o Banco de Dados de Documentos, veja [Roteiro de aprendizagem do Banco de Dados de Documentos](https://azure.microsoft.com/documentation/learning-paths/documentdb/) para obter uma introdução.

A saída do Banco de Dados de Documentos do Azure no Stream Analytics permite a gravação dos resultados de seu processamento de fluxo em sua coleção de Banco de Dados de Documentos. O Stream Analytics não cria coleções em seu banco de dados, em vez disso, ele exige a criação delas antecipadamente. Isso serve para que os custos de cobrança de coleções de Banco de Dados de Documentos sejam transparentes, e para que você possa ajustar o desempenho, a consistência e a capacidade de suas coleções diretamente com as [APIs do Banco de Dados de Documentos](https://msdn.microsoft.com/library/azure/dn781481.aspx). Recomendamos o uso de um banco de dados do Banco de Dados de Documentos por trabalho de transmissão a fim de separar logicamente suas coleções de acordo com o trabalho de transmissão.

Algumas das opções de coleção de Banco de Dados de Documentos estão detalhadas abaixo.

## Consistência

Para atender aos seus requisitos de aplicativo, o Banco de Dados de Documentos permite o ajuste do Banco de dados e das Coleções, e compensações entre consistência, disponibilidade e latência. Dependendo dos níveis de consistência de leitura exigidos pelo seu cenário em relação à latência de leitura e de gravação, você poderá escolher um nível de consistência em sua conta de banco de dados. Também por padrão, o Banco de Dados de Documentos permite a indexação síncrona em cada operação CRUD para sua coleção. Essa é outra opção útil para controlar o desempenho de leitura/gravação no Banco de Dados de Documentos. Para saber mais sobre esse tópico, consulte o artigo [Alterar os níveis de consistência do banco de dados e de consulta](../articles/documentdb-consistency-levels.md).

## Desempenho

As coleções do Banco de Dados de Documentos podem ser criadas em três níveis de desempenho diferentes (S1, S2 ou S3), e isso determinará a taxa de transferência disponível para CRUDs dessa coleção. Além disso, o desempenho é afetado pelos níveis de indexação/consistência em sua coleção. Consulte [este artigo](../articles/documentdb-performance-levels.md) para entender esses níveis de desempenho com mais detalhes.

## Upserts

A integração do Stream Analytics com o Banco de Dados de Documentos permite a inserção ou atualização de registros em sua coleção do Banco de Dados de Documentos com base em uma determinada coluna de ID do Documento. Isso também é chamado de *Upsert*.

O Stream Analytics utiliza uma abordagem Upsert otimista, na qual as atualizações são feitas somente quando a inserção falha devido a um conflito de ID do Documento. Essa atualização é executada pelo Stream Analytics como um PATCH. Assim, é possível realizar atualizações parciais no documento, ou seja, a adição de novas propriedades ou a substituição de uma propriedade existente é executada de forma incremental. Observe que as alterações nos valores de propriedades de matriz em seu documento JSON resultam na substituição de toda a matriz, ou seja, a matriz não é mesclada.

## Particionamento

Coleções do Banco de Dados de Documentos permitem particionar seus dados com base nos padrões de consulta e nas necessidades de desempenho do seu aplicativo. Cada coleção pode conter até 10 GB de dados (máximo) e, atualmente, não há uma maneira de escalar verticalmente (ou estourar) uma coleção. Para dimensionamento, o Stream Analytics permite que você grave várias coleções com um determinado prefixo (veja os detalhes de uso abaixo). O Stream Analytics usa a estratégia [Resolvedor de Partição Hash](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) consistente com base na coluna PartitionKey fornecida pelo usuário para particionar seus registros de saída. O número de coleções com o prefixo especificado na hora de início do trabalho de streaming é usado como a contagem de partições de saída, nas quais o trabalho grava em paralelo (coleções do Banco de Dados de Documentos = Partições de saída). Para uma única coleção S3 com a indexação lenta realizando apenas inserções, é possível esperar uma produtividade de gravação de cerca de 0,4 MB/s. O uso de várias coleções permite alcançar maior produtividade e maior capacidade.

Se você pretender aumentar a contagem de partições no futuro, talvez seja necessário interromper seu trabalho, reparticionar os dados de suas coleções existentes em novas coleções e reiniciar o trabalho do Stream Analytics. Incluiremos mais detalhes sobre como usar PartitionResolver e o novo particionamento, junto com um exemplo de código, em uma próxima postagem. O artigo [Particionamento no Banco de Dados de Documentos](../articles/documentdb-partition-data.md#developing-a-partitioned-application) também fornece detalhes sobre isso.

## Configurações de Banco de Dados de Documentos

A criação de Banco de Dados de Documentos como uma saída no Stream Analytics gera uma solicitação de informações, conforme mostra abaixo. Esta seção fornece uma explicação da definição de propriedades.
  
![tela de saída do stream analytics do banco de dados de documentos](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output.png)

-   **Alias de Saída** – Um alias para se referir a essa saída em sua consulta ASA  
-   **Nome da Conta** – O nome ou URI do ponto de extremidade da conta do Banco de Dados de Documentos.  
-   **Chave da Conta** – A chave de acesso compartilhado para a conta do Banco de Dados de Documentos.  
-   **Banco de dados** – O nome do banco de dados do Banco de Dados de Documentos.  
-   **Padrão de Nome de Coleção** – O padrão de nome da coleção que será usado para as coleções. O formato de nome da coleção pode ser construído com o token {partição} opcional, em que as partições começam em 0. Veja a seguir exemplos de entradas válidas: 1) MyCollection – É necessário existir uma coleção chamada “MyCollection”. 2) MyCollection {partição} – Essas coleções devem existir – "MyCollection0", "MyCollection1", "MyCollection2" etc.  
-   **Chave de Partição** – O nome do campo nos eventos de saída usado para especificar a chave para a saída do particionamento em várias coleções. Para uma saída de coleção única, nenhuma coluna de saída arbitrária pode ser usada, por exemplo, PartitionId.  
-   **ID do Documento** – Opcional. O nome do campo em eventos de saída usado para especificar a chave primária que serve de base para as operações de inserção ou atualização.  

<!---HONumber=Nov15_HO3-->