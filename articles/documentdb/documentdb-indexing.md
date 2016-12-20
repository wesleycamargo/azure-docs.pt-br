---
title: "Indexação automática no DocumentDB | Microsoft Docs"
description: "Saiba mais sobre o funcionamento da indexação no Banco de Dados de Documentos do Azure."
services: documentdb
author: arramac
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 126bfd36-9332-4127-8747-1a1c806760f7
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2016
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 9b88c62a7ea76d61ff593217392c3225624bb886


---
# <a name="automatic-indexing-in-azure-documentdb"></a>Indexação automática no Banco de Dados de Documentos do Azure
Este artigo foi retirado do documento [“Indexação agnóstica do esquema com o Azure DocumentDB](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)”, que será apresentado na [41ª Conferência Interna sobre Bancos de dados muito grandes](http://www.vldb.org/2015/) entre 31 de agosto e 4 de setembro de 2015, e é uma introdução do funcionamento da indexação no Azure DocumentDB. 

Depois de ler este artigo, você poderá responder às seguintes perguntas:

* Como o Banco de Dados de Documentos infere o esquema de um documento JSON?
* Como o Banco de Dados de Documentos cria um índice em documentos diferentes?
* Como o Banco de Dados de Documentos executa a indexação automática em escala?

## <a name="a-idhowdocumentdbindexingworksa-how-documentdb-indexing-works"></a><a id="HowDocumentDBIndexingWorks"></a> Funcionamento da indexação do Banco de Dados de Documentos
[Banco de Dados de Documentos do Microsoft Azure](https://azure.microsoft.com/services/documentdb/) é um verdadeiro banco de dados independente de esquema desenvolvido especificamente para JSON. Ele não espera ou exige qualquer esquema ou definições de índice secundárias para indexar dados em escala. Isso permite que você defina rapidamente e itere em modelos de dados de aplicativo usando o Banco de Dados de Documentos. Ao incluir documentos em uma coleção, o Banco de Dados de Documentos indexa todas as propriedades de documento automaticamente e elas ficam disponíveis para consulta. A indexação automática permite que você armazene documentos pertencentes a esquemas completamente arbitrários sem se preocupar com esquemas ou índices secundários.

Com o objetivo de eliminar a incompatibilidade de impedância entre o banco de dados e os modelos de programação de aplicativo, o Banco de Dados de Documentos explora a simplicidade do JSON e sua falta de uma especificação de esquema. Ele não faz nenhuma suposição sobre os documentos e permite que os documentos de uma coleção do Banco de Dados de Documentos variem no esquema, além de valores específicos da instância. Em comparação com outros bancos de dados de documentos, o mecanismo de banco de dados do Banco de Dados de Documentos opera diretamente no nível de gramática do JSON, permanecendo agnóstico em relação ao conceito de um esquema de documento e obscurecendo o limite entre os valores de instância e de estrutura de documentos. Isso, por sua vez, permite que ele indexe automaticamente documentos sem exigir um esquema ou índices secundários.

A indexação do Banco de Dados de Documentos aproveita-se que a gramática JSON permite que os documentos sejam **representados como árvores**. Para um documento JSON ser representado como uma árvore, precisa ser criado um nó raiz fictício que seja pai do restante de nós reais no documento abaixo. Cada rótulo, inclusive os índices da matriz em um documento JSON torna-se um nó da árvore. A figura a seguir ilustra um documento JSON de exemplo e sua representação de árvore correspondente.

> [!NOTE]
> Como o JSON é autodescritivo, ou seja, cada documento inclui o esquema (metadados) e os dados; por exemplo, o `{"locationId": 5, "city": "Moscow"}` revela que há duas propriedades `locationId` e `city`, e que elas têm valores de propriedade numéricos e de cadeia de caracteres. O Banco de Dados de Documentos pode inferir o esquema de documentos e indexá-los quando inseridos ou substituídos, sem que seja necessário definir esquemas ou índices secundários.
> 
> 

**Documentos JSON como árvores:**

![Documentos como árvores](media/documentdb-indexing/DocumentsAsTrees.png)

No exemplo mostrado acima:

* A propriedade JSON `{"headquarters": "Belgium"}` no exemplo acima corresponde ao caminho /headquarters/Belgium.
* A matriz JSON `{"exports": [{"city": “Moscow"}`, `{"city": Athens"}]}` corresponde aos caminhos `/exports/[]/city/Moscow` e `/exports/[]/city/Athens`.

Com a indexação automática, (1) todos os caminhos em uma árvore de documentos são indexados (a menos que o desenvolvedor tenha configurado explicitamente a política de indexação para excluir determinados padrões de caminho). (2) Cada atualização de um documento para uma coleção do Banco de Dados de Documentos leva a uma atualização da estrutura do índice (isto é, causa a adição ou a remoção de nós). Um dos principais requisitos da indexação automática de documentos é garantir que o custo de indexar e de consultar um documento com estrutura profundamente aninhada, digamos, 10 níveis, seja igual ao de um documento JSON simples que consista em pares de chave-valor com apenas um nível de profundidade. Portanto, uma representação de caminho normalizado é a fundação sobre a qual ambos os subsistemas de indexação e de consulta automáticos são criados.

Uma implicação importante do tratamento uniforme de ambos os valores de instância e de esquema em termos de caminhos é que, logicamente, apenas os documentos individuais, um índice dos dois documentos mostrados que mantém um mapa entre os caminhos e as ids do documento com esse caminho também podem ser representados como uma árvore. O Banco de Dados de Documentos usa esse fato para criar uma árvore de índice construída da união de todas as árvores que representam documentos individuais na coleção. A árvore de índice nas coleções do Banco de Dados de Documentos cresce ao longo do tempo conforme novos documentos são adicionados à coleção ou atualizados.

**Índice do Banco de Dados de Documentos como uma árvore:**

![Índice como uma árvore](media/documentdb-indexing/IndexAsTree.png)

Apesar de serem independentes de esquema, as linguagens de consulta SQL e JavaScript do Banco de Dados de Documentos oferecem projeções e filtros relacionais, navegação hierárquica entre documentos, operações espaciais e invocação de UDFs totalmente escritas em JavaScript. O tempo de execução da consulta do Banco de Dados de Documentos é capaz de dar suporte a essas consultas já que pode operar diretamente nesta representação de árvore de índice dos dados.

A política de indexação padrão indexa automaticamente todas as propriedades de todos os documentos e oferece consultas consistentes (o que significa que o índice é atualizado de forma síncrona com a gravação de documento). Como o Banco de Dados de Documentos dá suporte a atualizações consistentes à árvore de índice em escala? O Banco de Dados de Documentos usa técnicas otimizadas de manutenção de índice estruturado de log, livre de bloqueios. Isso significa que o Banco de Dados de Documentos pode oferecer suporte a um volume permanente de gravações rápidas e ainda assim oferecer consultas consistentes. 

A indexação do Banco de Dados de Documentos foi projetada para eficiência de armazenamento e para lidar com a multilocação. Para manter um bom custo-benefício, a sobrecarga do armazenamento em disco do índice é baixa e previsível. As atualizações de índice também são realizadas dentro do orçamento de recursos do sistema alocados por coleção do Banco de Dados de Documentos.

## <a name="a-namenextstepsa-next-steps"></a><a name="NextSteps"></a> Próximas etapas
* Baixe ["Indexação independente de esquema com o Banco de Dados de Documentos do Azure"](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), a ser apresentado na 41ª Conferência Interna sobre Bancos de dados muito grandes, de 31 de agosto a 4 de setembro de 2015.
* [Consulta com SQL do Banco de Dados de Documentos](documentdb-sql-query.md)
* Saiba mais sobre como personalizar o índice do Banco de Dados de Documentos [aqui](documentdb-indexing-policies.md)




<!--HONumber=Nov16_HO3-->


