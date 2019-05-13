---
title: Exemplos do Azure PowerShell para o Azure Cosmos DB
description: Exemplos do Azure PowerShell – scripts para ajudá-lo a criar contas do BD Cosmos do Azure.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/08/2019
ms.author: mjbrown
ms.openlocfilehash: 68e845a05f4ebe2d1f25b55c00042c8925c8109e
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65069298"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Exemplos do Azure PowerShell para o BD Cosmos do Azure

A tabela a seguir inclui links para scripts de exemplo do Azure PowerShell para o Azure Cosmos DB para API do Core (SQL).

| |  |
|---|---|
|**Contas do Azure Cosmos**||
|[Criar uma conta](scripts/powershell/sql/ps-account-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma conta da API de SQL do Azure Cosmos. |
|[Obter uma conta](scripts/powershell/sql/ps-account-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obter as propriedades de uma conta do Azure Cosmos. |
|[Adicionar uma região](scripts/powershell/sql/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtenha uma conta do Azure Cosmos e adicione uma região para a lista de locais. |
|[Alterar a prioridade de failover](scripts/powershell/sql/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Altere a prioridade de failover de uma conta do Azure Cosmos com um gatilho de failover manual. |
|[Marcas de atualização](scripts/powershell/sql/ps-account-tags-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Atualize as marcas para uma conta do Azure Cosmos. |
|[Obter chaves da conta](scripts/powershell/sql/ps-account-key-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtenha as chaves primárias e secundárias de uma conta do Azure Cosmos. |
|[Regenerar chaves da conta](scripts/powershell/sql/ps-account-key-regenerate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Regenere as chaves primárias e secundárias de uma conta do Azure Cosmos. |
|[Cadeias de caracteres de conexão de lista](scripts/powershell/sql/ps-account-connection-string-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtenha as cadeias de caracteres de conexão primária e secundária de uma conta do Azure Cosmos. |
|[Criar um firewall de IP](scripts/powershell/sql/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crie um firewall de IP para uma conta do Azure Cosmos. |
|[Excluir uma conta do Azure Cosmos](scripts/powershell/sql/ps-account-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Excluir uma conta do Azure Cosmos. |
|**Bancos de dados do Azure Cosmos**||
| [Criar um banco de dados](scripts/powershell/sql/ps-database-create.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crie um banco de dados dentro de uma conta do Azure Cosmos.|
| [Criar um banco de dados com taxa de transferência compartilhada/nível de banco de dados](scripts/powershell/sql/ps-database-create-shared.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crie um banco de dados do Azure Cosmos com taxa de transferência de nível de banco de dados que é compartilhado com seus contêineres.|
| [Listar todos os bancos de dados](scripts/powershell/sql/ps-database-list.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Liste todos os bancos de dados em uma conta do Azure Cosmos.|
| [Obter um banco de dados](scripts/powershell/sql/ps-database-get.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Obtenha as propriedades de um banco de dados do Azure Cosmos.|
|**Contêineres do Azure Cosmos**||
| [Criar um contêiner](scripts/powershell/sql/ps-container-create.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crie um contêiner do Azure Cosmos com taxa de transferência dedicada.|
| [Criar um contêiner com taxa de transferência compartilhada](scripts/powershell/sql/ps-container-create-shared.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crie um contêiner do Azure Cosmos com taxa de transferência compartilhada com outros contêineres no banco de dados.|
| [Criar um contêiner com a política de índice](scripts/powershell/sql/ps-container-create-index-custom.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crie um contêiner do Azure Cosmos com uma política de índice personalizada.|
| [Criar um contêiner sem a política de índice](scripts/powershell/sql/ps-container-create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crie um contêiner do Azure Cosmos com uma política de índice desativada.|
| [Criar um contêiner com chaves & TTL exclusivas](scripts/powershell/sql/ps-container-create-unique-key-ttl.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crie um contêiner do Azure Cosmos com uma restrição de chave exclusiva e o tempo de vida configurado.|
| [Criar um contêiner com a resolução de conflitos](scripts/powershell/sql/ps-container-create-conflict-policy.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Criar um contêiner do Azure Cosmos com a política de resolução de conflito último-gravador-ganha.|
| [Listar todos os contêineres](scripts/powershell/sql/ps-container-list.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Liste todos os contêineres em um banco de dados do Azure Cosmos.|
| [Obter um contêiner](scripts/powershell/sql/ps-container-get.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Obtenha as propriedades para um contêiner em um banco de dados do Azure Cosmos.|
| [Excluir um contêiner](scripts/powershell/sql/ps-container-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Exclua um contêiner em um banco de dados do Azure Cosmos.|
|||