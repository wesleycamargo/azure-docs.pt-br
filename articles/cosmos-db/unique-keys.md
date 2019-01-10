---
title: Chaves exclusivas no Azure Cosmos DB
description: Saiba como usar chaves exclusivas em seu Banco de dados do Azure Cosmos DB.
author: aliuy
ms.author: andrl
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.reviewer: sngun
ms.openlocfilehash: 20e5c96110f07d8eaec218ed167c87a48fd65782
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54037371"
---
# <a name="unique-keys-in-azure-cosmos-db"></a>Chaves exclusivas no Azure Cosmos DB

Chaves exclusivas fornecem a capacidade de adicionar uma camada de integridade de dados a um contêiner Cosmos. Você cria uma política de chave única ao criar um contêiner Cosmos. Com chaves exclusivas, você garante a exclusividade de um ou mais valores dentro de uma partição lógica (você pode garantir a exclusividade por [chave de partição](partition-data.md)). Depois de criar um contêiner com uma política de chave exclusiva, ele evita a criação de novos itens duplicados (ou atualizados) em uma partição lógica, conforme especificado pela restrição de chave exclusiva. A chave de partição combinada com a exclusividade de garantias de chave exclusiva de um item dentro do escopo do contêiner.

Por exemplo, considere um contêiner do Cosmos com endereço de e-mail como restrição de chave exclusiva e `CompanyID` como a chave de partição. Configurando uma chave exclusiva de endereço de e-mail do usuário, você garante que cada item tem um endereço de e-mail exclusivo dentro de um determinado `CompanyID`. Não é possível criar dois itens com endereços de e-mail duplicados e com o mesmo valor de chave de partição.  

Se você quiser fornecer aos usuários a capacidade de criar vários itens com o mesmo endereço de e-mail, mas não o mesmo nome, sobrenome e endereço de e-mail, você poderá adicionar outros caminhos à política de chave exclusiva. Em vez de criar uma chave exclusiva com base no endereço de email, você também pode criar uma chave exclusiva com uma combinação do nome, sobrenome e endereço de email (uma chave exclusiva composta). Nesse caso, cada combinação exclusiva dos três valores dentro de um determinado `CompanyID` é permitido. Por exemplo, o contêiner pode conter itens com os seguintes valores em que cada item está honrando a restrição de chave exclusiva.

|CompanyID|Nome|Sobrenome|Endereço de email|
|---|---|---|---|
|Contoso|Gaby|Duperre|gaby@contoso.com |
|Contoso|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Ivan|Duperre|gaby@fabrikam.com|
|Fabrikam|   |Duperre|gaby@fabraikam.com|
|Fabrikam|   |   |gaby@fabraikam.com|

Se você tentar inserir outro item com as combinações listadas na tabela acima, você receberá um erro indicando que a restrição de chave exclusiva não foi atendida. Você receberá "Recurso com ID ou nome especificado já existente" ou "Recurso com ID, nome ou índice exclusivo já existente" como uma mensagem de retorno.  

## <a name="defining-a-unique-key"></a>Definindo uma chave exclusiva

Você pode definir chaves exclusivas somente ao criar um contêiner Cosmos. Uma chave exclusiva é definida para uma partição lógica. No exemplo anterior, se você particionar o contêiner com base no CEP, acabará tendo itens duplicados em cada partição lógica. Considere as seguintes propriedades ao criar chaves exclusivas:

* Você não pode atualizar um contêiner existente para usar uma chave exclusiva diferente. Em outras palavras, quando um contêiner é criado com uma política de chave exclusiva, a política não pode ser alterada.

* Se você quiser definir uma chave exclusiva para um contêiner existente, terá que criar um novo contêiner com a restrição de chave exclusiva e usar a ferramenta de migração de dados apropriada para mover os dados do contêiner existente para o novo contêiner. Para contêineres SQL, use a [Ferramenta de Migração de Dados](import-data.md) para mover dados. Para contêineres MongoDB, use [mongoimport.exe ou mongorestore.exe](mongodb-migrate.md) para mover dados.

* Uma política de chave exclusiva pode ter no máximo 16 valores de caminho (por exemplo: / firstName, / lastName, / address / zipCode). Cada política de chave exclusiva pode ter no máximo 10 combinações ou restrições de chaves exclusivas e os caminhos combinados para todas as propriedades de índice exclusivo não devem exceder 60 caracteres. Assim, o exemplo anterior que usa nome, sobrenome e endereço de email é apenas uma restrição e usa três dos 16 possíveis caminhos disponíveis.

* Quando um contêiner tem uma política de chave exclusiva, os encargos de RU (unidade) de solicitação para criar, atualizar e excluir um item são um pouco maior.

* Não há suporte para chaves exclusivas esparsas. Se estiverem faltando alguns valores de caminho exclusivo, eles são tratados como valores nulos, que fazem parte de restrição de exclusividade. Portanto, pode haver apenas um único item com um valor nulo para atender a essa restrição.

* Nomes exclusivos de chave diferenciam maiúsculas de minúsculas. Por exemplo, considere um contêiner com a restrição de chave exclusiva definida como /address/zipcode. Se os dados tiverem um campo chamado ZipCode, do Cosmos DB insere "nulo" como a chave exclusiva, como "zipcode" não é igual a "ZipCode". Devido a essa diferenciação de maiúsculas e minúsculas, todos os outros registros com ZipCode não podem ser inseridos porque a duplicata "nula" violará a restrição de chave exclusiva.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [partições lógicas](partition-data.md)
