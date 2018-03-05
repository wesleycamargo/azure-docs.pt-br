---
title: "Limitações no Banco de Dados do Azure para MySQL"
description: "Este artigo descreve limitações no Banco de Dados do Azure para MySQL, como número de opções de mecanismo de armazenamento e conexão."
services: mysql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 85e57170c1cbd977d2de6e7e614916333c79e047
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="limitations-in-azure-database-for-mysql"></a>Limitações no Banco de Dados do Azure para MySQL
O Banco de Dados do Azure para o serviço do MySQL está em visualização pública. As seções a seguir descrevem a capacidade, suporte do mecanismo de armazenamento, suporte de privilégio, suporte à instrução de manipulação de dados e limites funcionais no serviço do banco de dados. Consulte também as [limitações gerais](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) aplicáveis ao mecanismo de banco de dados MySQL.

## <a name="service-tier-maximums"></a>Limites máximos da camada de serviço
O Banco de Dados do Azure para MySQL tem vários níveis de serviço que podem ser escolhidos durante a criação de um servidor. Para saber mais, confira [Tipos de preço do Banco de Dados do Azure para MySQL](concepts-pricing-tiers.md).  

Há um número máximo de conexões, unidades de computação e armazenamento em cada camada de serviço durante a versão prévia, conforme a seguir: 

|**Tipo de preço**| **Geração de computação**|**vCore(s)**| **Máximo de conexões**|
|---|---|---|---|
|Basic| Gen 4| 1| 50|
|Basic| Gen 4| 2| 100|
|Basic| Gen 5| 1| 50|
|Basic| Gen 5| 2| 100|
|Uso geral| Gen 4| 2| 200|
|Uso geral| Gen 4| 4| 400|
|Uso geral| Gen 4| 8| 800|
|Uso geral| Gen 4| 16| 1600|
|Uso geral| Gen 4| 32| 3200|
|Uso geral| Gen 5| 2| 200|
|Uso geral| Gen 5| 4| 400|
|Uso geral| Gen 5| 8| 800|
|Uso geral| Gen 5| 16| 1600|
|Uso geral| Gen 5| 32| 3200|
|Otimizado para memória| Gen 5| 2| 600|
|Otimizado para memória| Gen 5| 4| 1250|
|Otimizado para memória| Gen 5| 8| 2500|
|Otimizado para memória| Gen 5| 16| 5.000|
|Otimizado para memória| Gen 5| 32| 10000| 

Quando um número excessivo de conexões for atingido, você receberá o seguinte erro:
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

## <a name="preview-functional-limitations"></a>Limitações funcionais da versão prévia

### <a name="scale-operations"></a>Operações de dimensionamento
- Atualmente, não há suporte para o dimensionamento dos servidores em tipos de preço. Ou seja, alternando entre os tipos de preço Básico, Uso Geral e Otimizado para Memória.
- Não há suporte para diminuir o tamanho de armazenamento do servidor.

### <a name="server-version-upgrades"></a>Upgrade da versão do servidor
- Não há suporte para a migração automatizada entre versões de mecanismo de banco de dados principal.

### <a name="point-in-time-restore"></a>Restauração pontual
- Não é permitido restaurar para a camada de serviço diferente e/ou Unidades de computação e Tamanho do armazenamento.
- Não há suporte para restaurar um servidor eliminado.

## <a name="functional-limitations"></a>Limitações funcionais

### <a name="subscription-management"></a>Gerenciamento de assinaturas
- Não há suporte para mover dinamicamente servidores criados previamente entre a assinatura e o grupo de recursos.

## <a name="current-known-issues"></a>Problemas frequentes conhecidos
- A Instância do MySQL Server exibe a versão de servidor incorreta após a conexão ser estabelecida. Para obter a versão correta da instância de servidor, use o comando select version(); no prompt do MySQL.

## <a name="next-steps"></a>Próximas etapas
- [O que está disponível em cada camada de serviço](concepts-pricing-tiers.md)
- [Versões de banco de dados MySQL com suporte](concepts-supported-versions.md)
