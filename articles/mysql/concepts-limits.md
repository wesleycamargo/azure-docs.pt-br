---
title: Limitações no Banco de Dados do Azure para MySQL
description: Este artigo descreve limitações no Banco de Dados do Azure para MySQL, como número de opções de mecanismo de armazenamento e conexão.
services: mysql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 06/04/2018
ms.openlocfilehash: 3ec78b9aad45500a92a8f46f4bb2e654f97da8cb
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264877"
---
# <a name="limitations-in-azure-database-for-mysql"></a>Limitações no Banco de Dados do Azure para MySQL
As seções a seguir descrevem a capacidade, suporte do mecanismo de armazenamento, suporte de privilégio, suporte à instrução de manipulação de dados e limites funcionais no serviço do banco de dados. Consulte também as [limitações gerais](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) aplicáveis ao mecanismo de banco de dados MySQL.

## <a name="maximum-connections"></a>Número máximo de conexões
O número máximo de conexões por tipo de preço e vCores é o seguinte: 

|**Tipo de preço**|**vCore(s)**| **Máximo de conexões**|
|---|---|---|
|Basic| 1| 50|
|Basic| 2| 100|
|Uso geral| 2| 300|
|Uso geral| 4| 625|
|Uso geral| 8| 1250|
|Uso geral| 16| 2500|
|Uso geral| 32| 5.000|
|Otimizado para memória| 2| 600|
|Otimizado para memória| 4| 1250|
|Otimizado para memória| 8| 2500|
|Otimizado para memória| 16| 5.000|

Quando as conexões excederem o limite, você poderá receber o seguinte erro:
> ERRO 1040 (08004): número excessivo de conexões

## <a name="storage-engine-support"></a>Suporte do mecanismo de armazenamento

### <a name="supported"></a>Com suporte
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MEMORY](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Sem suporte
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARCHIVE](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDERATED](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privilege-support"></a>Suporte de privilégio

### <a name="unsupported"></a>Sem suporte
- Função DBA: muitas configurações e parâmetros do servidor podem inadvertidamente prejudicar o desempenho do servidor ou negar as propriedades ACID do DBMS. Desa forma, para manter a SLA e integridade do serviço em um nível de produto, esse serviço não expõe a função DBA. A conta de usuário padrão, que é construída quando uma nova instância do banco de dados é criada, permite que o usuário execute a maioria das instruções DDL e DML na instância do banco de dados gerenciado. 
- Privilégio SUPER: semelhante a privilégio [SUPER](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) também é restrito.

## <a name="data-manipulation-statement-support"></a>Suporte à instrução de manipulação de dados

### <a name="supported"></a>Com suporte
- CARREGAR DADOS INFILE - com suporte, mas deve especificar o parâmetro [LOCAL] que é direcionado para um caminho UNC (montado por meio de XSMB do armazenamento do Azure).

### <a name="unsupported"></a>Sem suporte
- SELECT ... INTO OUTFILE

## <a name="functional-limitations"></a>Limitações funcionais

### <a name="scale-operations"></a>Operações de dimensionamento
- Atualmente, não há suporte para o dimensionamento dos servidores em tipos de preço. Ou seja, alternando entre os tipos de preço Básico, Uso Geral e Otimizado para Memória.
- Não há suporte para diminuir o tamanho de armazenamento do servidor.

### <a name="server-version-upgrades"></a>Upgrade da versão do servidor
- Não há suporte para a migração automatizada entre versões de mecanismo de banco de dados principal.

### <a name="point-in-time-restore"></a>Restauração pontual
- Não é permitido restaurar para a camada de serviço diferente e/ou Unidades de computação e Tamanho do armazenamento.
- Não há suporte para restaurar um servidor eliminado.

### <a name="subscription-management"></a>Gerenciamento de assinaturas
- Não há suporte para mover dinamicamente servidores criados previamente entre a assinatura e o grupo de recursos.

## <a name="current-known-issues"></a>Problemas frequentes conhecidos
- A Instância do MySQL Server exibe a versão de servidor incorreta após a conexão ser estabelecida. Para obter a versão correta da instância de servidor, use o comando select version(); no prompt do MySQL.

## <a name="next-steps"></a>Próximas etapas
- [O que está disponível em cada camada de serviço](concepts-pricing-tiers.md)
- [Versões de banco de dados MySQL com suporte](concepts-supported-versions.md)
