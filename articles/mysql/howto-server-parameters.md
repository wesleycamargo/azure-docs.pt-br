---
title: Como configurar parâmetros do servidor no Banco de Dados do Azure para MySQL
description: Este artigo descreve como configurar os parâmetros do MySQL Server no Banco de Dados do Azure para MySQL usando o portal do Azure.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 32414cb034bf6ee95a284021111759959054c00e
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35267298"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Como configurar parâmetros de servidor no Banco de Dados do Azure para MySQL usando o portal do Azure

O Banco de Dados do Azure para MySQL dá suporte à configuração de alguns parâmetros de servidor. Este artigo descreve como configurar esses parâmetros usando o portal do Azure. Nem todos os parâmetros de servidor podem ser ajustados. 

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Navegue até Parâmetros de servidor no portal do Azure
1. Faça logon no portal do Azure e, em seguida, localize o seu Banco de Dados do Azure para MySQL Server.
2. Na seção **CONFIGURAÇÕES**, clique em **Parâmetros de servidor** para abrir a página Parâmetros de servidor do Banco de Dados do Azure para MySQL.
![Página de parâmetros do servidor do Portal do Azure](./media/howto-server-parameters/auzre-portal-server-parameters.png)
3. Localize as configurações que você precisa ajustar. Examine a coluna **Descrição** para entender a finalidade e os valores permitidos. 
![Enumerar a lista suspensa](./media/howto-server-parameters/3-toggle_parameter.png)
4. Clique em **Salvar** para salvar as alterações.
![Clique em salvar ou descartar mudanças](./media/howto-server-parameters/4-save_parameters.png)
5. Se você tiver salvo os novos valores para os parâmetros, você sempre pode reverter tudo o que fazer com os valores padrão selecionando **Redefinir tudo para o padrão**.
![Redefinir tudo para padrão](./media/howto-server-parameters/5-reset_parameters.png)


## <a name="list-of-configurable-server-parameters"></a>Lista de parâmetros de servidor configuráveis

A lista de parâmetros de servidor com suporte está em constante crescimento. Use a guia de parâmetros de servidor no portal do Azure para obter a definição e configurar parâmetros de servidor com base em seus requisitos de aplicativo. 

## <a name="nonconfigurable-server-parameters"></a>Parâmetros de servidor não configuráveis
Pool de buffers InnoDB e Máximo de Conexões não são configuráveis e são associados ao seu [tipo de preço](concepts-service-tiers.md). 

|**Tipo de preço**| **Geração de computação**|**vCore(s)**|**Pool de Buffers InnoDB (MB)**| **Máximo de conexões**|
|---|---|---|---|--|
|Basic| Gen 4| 1| 1024| 50|
|Basic| Gen 4| 2| 2560| 100|
|Basic| Gen 5| 1| 1024| 50|
|Basic| Gen 5| 2| 2560| 100|
|Uso geral| Gen 4| 2| 3584| 300|
|Uso geral| Gen 4| 4| 7680| 625|
|Uso geral| Gen 4| 8| 15360| 1250|
|Uso geral| Gen 4| 16| 31232| 2500|
|Uso geral| Gen 4| 32| 62976| 5.000|
|Uso geral| Gen 5| 2| 3584| 300|
|Uso geral| Gen 5| 4| 7680| 625|
|Uso geral| Gen 5| 8| 15360| 1250|
|Uso geral| Gen 5| 16| 31232| 2500|
|Uso geral| Gen 5| 32| 62976| 5.000|
|Otimizado para memória| Gen 5| 2| 7168| 600|
|Otimizado para memória| Gen 5| 4| 15360| 1250|
|Otimizado para memória| Gen 5| 8| 30720| 2500|
|Otimizado para memória| Gen 5| 16| 62464| 5.000|

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
