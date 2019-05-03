---
title: Limites de Blockchain do Azure
description: Visão geral do serviço e limites funcionais no serviço do Azure Blockchain
services: azure-blockchain
keywords: blockchain
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 169ec7a8ef407af3f754046aa8e3b06793a7e962
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028164"
---
# <a name="limits-in-azure-blockchain-service"></a>Limites de serviço de Blockchain do Azure

Azure Blockchain Service tem o serviço e limites funcionais, como o número de nós de que um membro pode ter restrições consortium e quantidades de armazenamento.

## <a name="pricing-tier"></a>Tipo de preço

Limites máximos de transações e nós de validador dependem se você provisionar o serviço de Blockchain do Azure em camadas de preço básico ou Standard.

| Tipo de preço | Máximo de nós de transação | Máximo de nós de validador |
|:---|:---:|:---:|
| Basic | 10 | 1 |
| Standard | 10 | 2 |

Alterando o tipo de preço entre básico e padrão depois que não há suporte para a criação de membros.

## <a name="storage-capacity"></a>Capacidade de armazenamento

A quantidade máxima de armazenamento que pode ser usado por nó para logs e dados contábeis é de 1 terabyte.

Não há suporte para diminuir o tamanho de armazenamento contábeis e de log.

## <a name="consortium-limits"></a>Limites de Consortium

* **Nomes de membros Consortium devem ser exclusivos** de outros nomes de membros consortium no serviço de Blockchain do Azure.

* **Nomes de membro e consortium não podem ser alterados.**

* **Todos os membros em um consórcio devem estar no mesmo tipo de preço**

* **Todos os membros que participam de um consórcio devem residir na mesma região**

    O primeiro membro criado em um consórcio determina a região. Membros convidados a serem consórcio devem residir na mesma região que o primeiro membro. Limitar todos os membros da mesma região ajuda a garantir um consenso de rede não é afetado negativamente.

* **Um consórcio deve ter pelo menos um administrador**

    Se houver somente um administrador em um consórcio, eles não podem remover próprios do consórcio ou excluir seu membro até que outro administrador é adicionado ou promovido no consórcio.

* **Os membros removidos de consórcio não podem ser adicionados novamente**

    Em vez disso, eles devem ser novamente convidados para ingressar o consortium e criar um novo membro. Seus recursos existentes do membro não serão excluídos para preservar o histórico de transações.

* **Todos os membros em um consórcio devem estar usando a mesma versão do razão**

    Para obter mais informações sobre a aplicação de patches, atualizações e versões de razão disponíveis no serviço de Blockchain do Azure, consulte [aplicação de patch, atualizações e versões](ledger-versions.md).

## <a name="next-steps"></a>Próximas etapas

* [Aplicação de patches, atualizações e versões](ledger-versions.md)
