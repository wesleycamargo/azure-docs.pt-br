<properties 
	pageTitle="Diretrizes de desenvolvimento de visualização para contas do Banco de Dados de Documentos com suporte de protocolo para MongoDB | Microsoft Azure" 
	description="Saiba mais sobre as diretrizes de desenvolvimento de visualização para contas do Banco de Dados de Documentos com suporte de protocolo para MongoDB, agora disponível para a versão de visualização." 
	services="documentdb" 
	authors="andrewhoh" 
	manager="jhubbard" 
	editor="" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/15/2016" 
	ms.author="anhoh"/>

# Diretrizes de desenvolvimento para as contas do DocumentDB com o suporte de protocolo para MongoDB

Você pode se comunicar com o Banco de Dados de Documentos do Azure por meio de qualquer um dos [drivers](https://docs.mongodb.org/ecosystem/drivers/) do cliente MongoDB de software livre. O suporte de protocolo para MongoDB supõe que os drivers do cliente MongoDB estão se comunicando com um ponto de extremidade de servidor 2.6 ou posterior do MongoDB. O DocumentDB oferece suporte seguindo o protocolo de transferência do [MongoDB](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol/), versão 2.6.

O Banco de Dados de Documentos é compatível com as principais funções da API do MongoDB para operações CRUD (Criar, Ler, Atualizar e Excluir) dados, bem como para consultar o banco de dados. Os recursos implementados foram priorizados com base nas necessidades de plataformas, estruturas, ferramentas e padrões de aplicativos comuns.

## Coleções

> [AZURE.IMPORTANT] O Banco de Dados de Documentos usa produtividade reservada no nível de coleção para fornecer desempenho garantido e previsível. Desse modo, as coleções do Banco de Dados de Documentos são entidades faturáveis.

As reservas de desempenho são aplicadas no nível de coleção para que os aplicativos possam ajustar o desempenho no nível mais baixo de contêineres de dados no sistema. O preço de uma coleção, portanto, é determinado pela taxa de transferência provisionada da coleção medida em unidades de solicitação por segundo, junto com o armazenamento total consumido em gigabytes. A produtividade provisionada pode ser ajustada durante toda a vida útil de uma coleção para se adaptar às necessidades de processamento em constante mudança e aos padrões de acesso do aplicativo. Para obter mais informações, consulte [Níveis de desempenho do Banco de Dados de Documentos](documentdb-performance-levels.md).

O Banco de Dados de Documentos com suporte de protocolo para coleções do MongoDB é, por padrão, criado no tipo de preço Standard com 1.000 RU/s de produtividade provisionada. Você pode ajustar a produtividade provisionada de cada uma das coleções, conforme documentado em [Alterando os níveis de desempenho usando o Portal do Azure](documentdb-performance-levels.md#changing-performance-levels-using-the-azure-portal).

## Operações CRUD

As operações Inserir, Ler, Atualizar, Substituir e Excluir do MongoDB são totalmente permitidas. Isso inclui suporte para atualizações de isolamento, campo, matriz e bit a bit, conforme especificado por [Update operator specification](https://docs.mongodb.org/manual/reference/operator/update/) (Atualizar especificações do operador) do MongoDB. Para os operadores Atualizar que precisam de várias manipulações de documento, o Banco de Dados de Documentos fornece semântica ACID completa com isolamento de instantâneo.

## Operações de consulta

O DocumentDB é compatível com gramática completa da Consulta MongoDB, com poucas exceções. As consultas que funcionam no conjunto compatível JSON de [tipos BSON](https://docs.mongodb.org/manual/reference/bson-types/) são permitidas além do suporte ao formato de data/hora do MongoDB. Em consultas que não exigem operadores específicos do tipo JSON, o Banco de Dados de Documentos permite os tipos de dados GUID.

## Experiência do Portal
A experiência do portal do Azure para as contas de protocolo habilitadas do MongoDB é fornecida para as contas de protocolo habilitadas do MongoDB. Estamos procurando expandir a experiência, mas precisamos de seus [comentários](mailto:askdocdb@microsoft.com?subject=DocumentDB%20Protocol%20Support%20for%20MongoDB%20Preview%20Portal%20Experience) para saber quais recursos do portal seriam mais úteis para você.

## Matriz de suporte


### Operações CRUD e de consulta

Recurso|Suportado|Terá suporte
---|---|---
Inserir|InsertOne| 
 |InsertMany| 
 |Inserir| 
Atualização|UpdateOne| 
 |UpdateMany| 
 |Atualização| 
Atualização de Campo|$inc, $mul, $rename, $set, $unset, $min, $max|$currentDate| 
Atualização de Matriz| |-todos-
Bit a bit| |-todos-
Isolamento| |-todos-
Substitua|ReplaceOne| 
Exclusão|DeleteOne | 
 |DeleteMany| 
 |Remover| 
BulkWrite| |bulkWrite()
Comparação|-todos-| 
Lógico|-todos-| 
Consulta de Elemento| |-todos-
Avaliação|$mod, $regex |$text, $where
Geoespacial|2dsphere, 2d, polígono|Todo o resto
Matriz|$all, $size, $elemMatch|
Bit a bit| |-todos-
Comentário|-todos-| 
Projeção| |-todos-


### Comandos de banco de dados

Recurso|Suportado|Terá suporte
---|---|---
Agregação|Contagem| 
Geoespacial| |-todos-
Consulta e Gravação|find, insert, update, delete, getLastError, getMore, findAndModify| 
Autenticação|getnonce, logout, authenticate| 
Administração|createIndex, listIndexes, dropIndexes, connectionStatus, reIndex| 
Diagnóstico|listDatabases, collStats, dbStats| 

## Próximas etapas

- Saiba como [usar o MongoChef](documentdb-mongodb-mongochef.md) com uma conta do Banco de Dados de Documentos com suporte de protocolo para MongoDB.
- Explore [amostras](documentdb-mongodb-samples.md) do Banco de Dados de Documentos com suporte de protocolo para MongoDB.

 

<!---HONumber=AcomDC_0921_2016-->