---
title: Limitações no Banco de Dados do Azure para o MariaDB
description: Este artigo descreve as limitações no Banco de Dados do Azure para o MariaDB, como o número de opções de mecanismo de conexão e armazenamento.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: ac05a2dcee3adaa93d31e28e5597a788c0159ddd
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46955428"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Limitações no Banco de Dados do Azure para o MariaDB
O banco de dados do Azure para o serviço MariaDB está em pré-visualização pública. As seções a seguir descrevem a capacidade, suporte do mecanismo de armazenamento, suporte de privilégio, suporte à instrução de manipulação de dados e limites funcionais no serviço do banco de dados.

## <a name="maximum-connections"></a>Número máximo de conexões
O número máximo de conexões por tipo de preço e vCores durante a visualização é a seguinte:

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
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [MEMORY](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>Sem suporte
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [BLACKHOLE](https://mariadb.com/kb/en/library/blackhole/l)
- [ARCHIVE](https://mariadb.com/kb/en/library/archive/)

## <a name="privilege-support"></a>Suporte de privilégio

### <a name="unsupported"></a>Sem suporte
- Função DBA: muitas configurações e parâmetros do servidor podem inadvertidamente prejudicar o desempenho do servidor ou negar as propriedades ACID do DBMS. Desa forma, para manter a SLA e integridade do serviço em um nível de produto, esse serviço não expõe a função DBA. A conta de usuário padrão, que é construída quando uma nova instância do banco de dados é criada, permite que o usuário execute a maioria das instruções DDL e DML na instância do banco de dados gerenciado.
- Privilégio SUPER: semelhante a privilégio [SUPER](https://mariadb.com/kb/en/library/grant/#global-privileges) também é restrito.

## <a name="data-manipulation-statement-support"></a>Suporte à instrução de manipulação de dados

### <a name="supported"></a>Com suporte
- `LOAD DATA INFILE` tem suporte, mas o parâmetro `[LOCAL]` deve ser especificado e direcionado para um caminho UNC (armazenamento do Azure montado por meio do SMB).

### <a name="unsupported"></a>Sem suporte
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>Limitações funcionais

### <a name="scale-operations"></a>Operações de dimensionamento
- O dimensionamento dinâmico de e para as camadas de preços básicas não tem suporte no momento.
- Não há suporte para diminuir o tamanho de armazenamento do servidor.

### <a name="server-version-upgrades"></a>Upgrade da versão do servidor
- Não há suporte para a migração automatizada entre versões de mecanismo de banco de dados principal.

### <a name="point-in-time-restore"></a>Restauração pontual
- Ao usar o recurso PITR, o novo servidor é criado com as mesmas configurações nas quais o servidor está baseado.
- Não há suporte para restaurar um servidor eliminado.

### <a name="subscription-management"></a>Gerenciamento de assinaturas
- Não há suporte para mover dinamicamente servidores criados previamente entre a assinatura e o grupo de recursos.

## <a name="current-known-issues"></a>Problemas frequentes conhecidos
- Instância de servidor MariaDB exibe a versão de servidor incorreto após o estabelecimento de conexão. Para obter a versão correta do mecanismo de instância de servidor, use o comando `select version();`.

## <a name="next-steps"></a>Próximas etapas
- [O que está disponível em cada camada de serviço](concepts-pricing-tiers.md)
- [Versões com suporte de banco de dados MariaDB](concepts-supported-versions.md)
