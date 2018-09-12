---
title: Procedimentos Armazenados de Replicação nos Dados do Banco de Dados do Azure para MySQL
description: Este artigo apresenta todos os procedimentos armazenados usados para Replicação nos dados.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 08/31/2018
ms.openlocfilehash: fb1a1b31d90df0022e5973de3ae2f55fb4c36701
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2018
ms.locfileid: "43665939"
---
# <a name="azure-database-for-mysql-data-in-replication-stored-procedures"></a>Procedimentos Armazenados de Replicação nos Dados do Banco de Dados do Azure para MySQL

A Replicação nos dados permite sincronizar dados de um Servidor MySQL em execução local, em máquinas virtuais ou em serviços de banco de dados hospedados por outros provedores de nuvem com o serviço do Banco de Dados do Azure para MySQL.

Os procedimentos armazenados a seguir são usados para configurar ou remover a Replicação de Dados entre um mestre e uma réplica.

|**Nome do procedimento armazenado**|**Parâmetros de entrada**|**Parâmetros de saída**|**Observação de uso**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|N/D|Para transferir dados com o modo SSL, passe o contexto do Certificado de Autoridade de Certificação para o parâmetro master_ssl_ca. </br><br>Para transferir dados sem SSL, passe uma cadeia de caracteres vazia para o parâmetro master_ssl_ca.|
|*mysql.az_replication _start*|N/D|N/D|Inicia a replicação.|
|*mysql.az_replication _stop*|N/D|N/D|Para a replicação.|
|*mysql.az_replication _remove_master*|N/D|N/D|Remove o relacionamento de replicação entre o mestre e a réplica.|
|*mysql.az_replication_skip_counter*|N/D|N/D|Ignora um erro de replicação.|

Para configurar a replicação de entrada de dados entre um mestre e uma réplica no Banco de Dados do Azure para MySQL, consulte [como configurar a replicação de entrada de dados](howto-data-in-replication.md).
