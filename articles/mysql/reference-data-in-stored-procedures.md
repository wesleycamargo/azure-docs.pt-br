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
ms.date: 05/18/2018
ms.openlocfilehash: 2d62cd693d7a67faf836c645f8bd33de9afca949
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35266101"
---
# <a name="azure-database-for-mysql-data-in-replication-stored-procedures"></a>Procedimentos Armazenados de Replicação nos Dados do Banco de Dados do Azure para MySQL

A Replicação nos dados permite sincronizar dados de um Servidor MySQL em execução local, em máquinas virtuais ou em serviços de banco de dados hospedados por outros provedores de nuvem com o serviço do Banco de Dados do Azure para MySQL.

Os procedimentos armazenados a seguir são usados para configurar ou remover a Replicação nos dados entre um local primário e uma réplica.

|**Nome do procedimento armazenado**|**Parâmetros de entrada**|**Parâmetros de saída**|**Observação de uso**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_primary*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|N/D|Para transferir dados com o modo SSL, passe o contexto do Certificado de Autoridade de Certificação para o parâmetro master_ssl_ca. </br><br>Para transferir dados sem SSL, passe uma cadeia de caracteres vazia para o parâmetro master_ssl_ca.|
|*mysql.az_replication _start*|N/D|N/D|Inicia a replicação.|
|*mysql.az_replication _stop*|N/D|N/D|Para a replicação.|
|*mysql.az_replication _remove_primary*|N/D|N/D|Remove a relação de replicação entre o local primário e a réplica.|
|*mysql.az_replication_skip_counter*|N/D|N/D|Ignora um erro de replicação.|

Para configurar a replicação nos dados entre um local primário e uma réplica no Banco de Dados do Azure para MySQL, confira [Como configurar a replicação nos dados](howto-data-in-replication.md).