---
title: Como restaurar um servidor no Banco de Dados do Azure para PostgreSQL | Microsoft Docs
description: Este artigo descreve como fazer backup e restaurar um servidor no Banco de Dados do Azure para PostgreSQL usando a linha de comando da CLI do Azure.
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 06/13/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: fa63ccedb455e4972bfdd0e15069ad5e932b0fa3
ms.contentlocale: pt-br
ms.lasthandoff: 06/20/2017

---

# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-postgresql-using-the-azure-cli"></a>Como fazer backup e restaurar um servidor no Banco de Dados do Azure para PostgreSQL usando a CLI do Azure

## <a name="prerequisites"></a>Pré-requisitos
Para seguir este guia de instruções, você precisa:
- Um [Banco de Dados do Azure para servidor e banco de dados PostgreSQL](quickstart-create-server-database-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tópico exigirá que você esteja executando a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

## <a name="backup-happens-automatically"></a>O backup ocorre automaticamente
Ao usar o Banco de Dados do Azure para PostgreSQL, o serviço de banco de dados faz automaticamente um backup do serviço a cada cinco minutos. 

Os backups ficam disponíveis por sete dias ao usar a camada Basic e 35 dias ao usar a camada Standard. Para saber mais, confira [Tipos de preço do Banco de Dados do Azure para PostgreSQL](concepts-service-tiers.md)

Com esse recurso de backup automático você pode restaurar o servidor e todos os seus bancos de dados em um novo servidor em um ponto anterior no tempo.

## <a name="restore-a-database-to-a-previous-point-in-time-using-the-azure-cli"></a>Restaurar um banco de dados para um ponto anterior no tempo usando a CLI do Azure
O Banco de Dados do Azure para PostgreSQL permite a restauração do servidor em um ponto anterior no tempo. Os dados restaurados são copiados em um novo servidor, e o servidor existente é deixado como está. Por exemplo, se uma tabela tiver sido descartada acidentalmente ao meio-dia de hoje, você poderia restaurar para um horário um pouco antes do meio-dia. Depois, recuperar a tabela e os dados ausentes da cópia restaurada do servidor. 

Use o comando da CLI do Azure [az postgres server restore](/cli/azure/postgres/server#restore) para fazer a restauração.

### <a name="run-the-restore-command"></a>Executar o comando restore
```azurecli-interactive
az postgres server restore --resource-group myResourceGroup --name mypgserver-restored --restore-point-in-time 2017-04-13T13:59:00Z --source-server mypgserver-20170401
```

O comando `az postgres server restore` exige os seguintes parâmetros:
| Configuração | Valor sugerido | Descrição  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  O grupo de recursos no qual o servidor de origem existe.  |
| name | restaurado mypgserver | O nome do novo servidor que é criado pelo comando de restauração. |
| Restauração-point-in-time | 2017-04-13T13:59:00Z | Selecione um point-in-time para restaurar. Essa data e hora devem estar dentro do período de retenção de backup do servidor de origem. Use o formato ISO8601 de data e hora. Por exemplo, você pode usar seu próprio fuso horário local, como `2017-04-13T05:59:00-08:00`, ou usar o formato UTC Zulu `2017-04-13T13:59:00Z`. |
| source-server | mypgserver-20170401 | O nome ou ID para restaurar a partir do servidor de origem. |

Restaurar um servidor para um point-in-time cria um novo servidor, copiando o servidor original a partir do ponto no tempo que você especificar. O local e os valores de tipo de preço para o servidor restaurado são o mesmo que o servidor de origem. O comando é síncrono e retornará após esperar a restauração do servidor. 

Depois que a restauração é concluída, localize o novo servidor que foi criado. Verifique se os dados foram restaurados conforme o esperado.

## <a name="next-steps"></a>Próximas etapas
[Bibliotecas de conexão para o Banco de Dados do Azure para PostgreSQL](concepts-connection-libraries.md)

