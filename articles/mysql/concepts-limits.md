---
title: "Limitações no Banco de Dados do Azure para MySQL | Microsoft Docs"
description: "Descreve as limitações de visualização no Banco de Dados para MySQL."
services: mysql
author: jasonh
ms.author: kamathsun
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 01/11/2018
ms.openlocfilehash: f0f9a10f987f19d8ae77a07038cffe23446856fd
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2018
---
# <a name="limitations-in-azure-database-for-mysql"></a>Limitações no Banco de Dados do Azure para MySQL
O Banco de Dados do Azure para o serviço do MySQL está em visualização pública. As seções a seguir descrevem a capacidade, suporte do mecanismo de armazenamento, suporte de privilégio, suporte à instrução de manipulação de dados e limites funcionais no serviço do banco de dados. Consulte também as [limitações gerais](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) aplicáveis ao mecanismo de banco de dados MySQL.

## <a name="service-tier-maximums"></a>Limites máximos da camada de serviço
O Banco de Dados do Azure para MySQL tem vários níveis de serviço que podem ser escolhidos durante a criação de um servidor. Para saber mais, confira [Entenda o que está disponível em cada camada de serviço](concepts-service-tiers.md).  

Há um número máximo de conexões, unidades de computação e armazenamento em cada camada de serviço durante a versão prévia, conforme a seguir: 

|                            |                   |
| :------------------------- | :---------------- |
| **Conexões máximas**        |                   |
| 50 unidades de computação básica     | 50 conexões    |
| 100 unidades de computação básica    | 100 conexões   |
| 100 unidades de computação standard | 200 conexões   |
| 200 unidades de computação standard | 400 conexões   |
| 400 unidades de computação standard | 800 conexões   |
| 800 unidades de computação standard | 1600 conexões  |
| **Unidades de computação máxima**      |                   |
| Camada de serviço Básica         | 100 Unidades de computação |
| Camada de serviço Standard      | 800 Unidades de computação |
| **Armazenamento Máximo**            |                   |
| Camada de serviço Básica         | 1 TB              |
| Camada de serviço Standard      | 1 TB              |

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
- Função DBA Muitos parâmetros e configurações do servidor e configurações podem prejudicar inadvertidamente o desempenho do servidor ou negar as propriedades ACID do DBMS. Como tal, para manter a integridade dos nossos serviços e SLA em um nível de produto, nós não expomos a função do DBA para os clientes. A conta de usuário padrão, que é construída quando uma nova instância de banco de dados é criada, permite que os clientes executem a maioria das instruções DDL e DML na instância do banco de dados gerenciado. 
- SUPER privilégio semelhante a [SUPER privilégio](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) também é restrito.

## <a name="data-manipulation-statement-support"></a>Suporte à instrução de manipulação de dados

### <a name="supported"></a>Com suporte
- CARREGAR DADOS INFILE - com suporte, mas deve especificar o parâmetro [LOCAL] que é direcionado para um caminho UNC (montado por meio de XSMB do armazenamento do Azure).

### <a name="unsupported"></a>Sem suporte
- SELECT ... INTO OUTFILE

## <a name="preview-functional-limitations"></a>Limitações funcionais da versão prévia

### <a name="scale-operations"></a>Operações de dimensionamento
- Não há suporte para o dimensionamento dinâmico de servidores por meio de camadas de serviço. Ou seja, alternando entre as camadas de serviço Básico e Standard.
- Não há suporte para o aumento sob demanda dinâmico de armazenamento no servidor criado previamente.
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
- [O que está disponível em cada camada de serviço](concepts-service-tiers.md)
- [Versões de banco de dados MySQL com suporte](concepts-supported-versions.md)
