---
title: Criptografar dados de tabela de armazenamento do Azure | Microsoft Docs
description: Saiba mais sobre a criptografia de dados de tabela no armazenamento do Azure.
services: storage
documentationcenter: na
author: MarkMcGeeAtAquent
manager: kfile
ms.assetid: 8e228b0c-2998-4462-8101-9f16517393ca
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/11/2018
ms.author: sngun
ms.openlocfilehash: 082e8a54cc8625a4bbdea2157f73874dbc86fde2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659011"
---
# <a name="encrypt-table-data"></a>Criptografar dados de tabela
A Biblioteca de Cliente do Armazenamento do Azure para .NET dá suporte à criptografia de propriedades de entidade para as operações de inserção e substituição. As cadeias de caracteres criptografadas são armazenadas no serviço como propriedades binárias, e são convertidas novamente em cadeias de caracteres após a descriptografia.    

Para tabelas, além da política de criptografia, os usuários devem especificar as propriedades que devem ser criptografadas. Isso pode ser feito especificando o atributo [EncryptProperty] \(para entidades POCO que derivam de TableEntity) ou um resolvedor de criptografia nas opções de solicitação. Um resolvedor de criptografia é um delegado que usa uma chave de partição, a chave de linha e o nome da propriedade e retorna um valor booliano que indica se essa propriedade deve ser criptografada. Durante a criptografia, a biblioteca de clientes usará essas informações para decidir se uma propriedade deve ser criptografada durante a gravação para a transmissão. O representante também oferece a possibilidade de lógica em torno de como as propriedades são criptografadas. (Por exemplo, se X, então criptografar a propriedade A; caso contrário, criptografar as propriedades A e B.) Não é necessário fornecer essas informações durante a leitura ou ao consultar entidades.

## <a name="merge-support"></a>Suporte de mesclagem

Atualmente não há suporte para a mesclagem. Como um subconjunto de propriedades pode ter sido criptografado anteriormente usando uma chave diferente, simplesmente mesclar as novas propriedades e atualizar os metadados resultará em perda de dados. Mesclar requer fazer chamadas de serviço extra para ler a entidade já existente no serviço ou usar uma nova chave por propriedade, os quais não são ambos adequados por motivos de desempenho.     

Para obter informações sobre como criptografar dados de tabela, confira [Criptografia do lado do cliente e Cofre da Chave do Azure para Armazenamento do Microsoft Azure](../common/storage-client-side-encryption.md).  

## <a name="next-steps"></a>Próximas etapas

- [Padrões de design de tabela](table-storage-design-patterns.md)
- [Relações de modelagem](table-storage-design-modeling.md)
- [Relações de modelagem](table-storage-design-modeling.md)
- [Design para modificação de dados](table-storage-design-for-modification.md)