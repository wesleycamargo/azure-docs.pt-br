---
title: "Como configurar parâmetros de servidor no Banco de Dados do Azure para MySQL | Microsoft Docs"
description: "Este artigo descreve como configurar os parâmetros do MySQL Server no Banco de Dados do Azure para MySQL usando o portal do Azure."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 01/25/2018
ms.openlocfilehash: 59eeed42356a276c259bd8da55890b7ada67d729
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2018
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Como configurar parâmetros de servidor no Banco de Dados do Azure para MySQL usando o portal do Azure

O Banco de Dados do Azure para MySQL dá suporte à configuração de alguns parâmetros de servidor. Este artigo descreve como configurar esses parâmetros usando o portal do Azure. Nem todos os parâmetros de servidor podem ser ajustados. 

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Navegue até Parâmetros de servidor no portal do Azure
1. Faça logon no portal do Azure e, em seguida, localize o seu Banco de Dados do Azure para MySQL Server.
2. Na seção **CONFIGURAÇÕES**, clique em **Parâmetros de servidor** para abrir a página Parâmetros de servidor do Banco de Dados do Azure para MySQL.
3. Localize as configurações que você precisa ajustar. Examine a coluna **Descrição** para entender a finalidade e os valores permitidos. 
4. Clique em **Salvar** para salvar as alterações.

![Página de parâmetros do servidor do portal do Azure](./media/howto-server-parameters/auzre-portal-server-parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Lista de parâmetros de servidor configuráveis

A lista de parâmetros de servidor com suporte está em constante crescimento. Use a guia de parâmetros de servidor no portal do Azure para obter a definição e configurar parâmetros de servidor com base em seus requisitos de aplicativo. 

## <a name="nonconfigurable-server-parameters"></a>Parâmetros de servidor não configuráveis
Pool de buffers InnoDB e Máximo de Conexões não são configuráveis e são associados ao seu [tipo de preço](concepts-service-tiers.md). 

| **Tipo de preços** | **Pool de Buffers InnoDB (MB)** | **Máximo de conexões** |
| :------------------------ | :-------- | :----------- |
| Basic 50 | 1024 | 50 | 
| Basic 100  | 2560 | 100 | 
| Standard 100 | 2560 | 200 | 
| Standard 200 | 5120 | 400 | 
| Standard 400 | 10240 | 800 | 
| Standard 800 | 20480 | 1600 |

Esses parâmetros de servidor adicionais não são configuráveis no sistema:

|**Parâmetro**|**Valor fixo**|
| :------------------------ | :-------- |
|innodb_file_per_table na camada Básica|DESATIVADO|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|512MB|

Outros parâmetros de servidor que não estão listados aqui são configurados com seus valores padrão iniciais MySQL nas versões [5.7](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html) e [5.6](https://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html).

## <a name="next-steps"></a>Próximas etapas
- [Bibliotecas de conexão para o Banco de Dados do Azure para MySQL](concepts-connection-libraries.md).
