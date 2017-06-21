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
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c19304ec2605faa3d69e82ac41d26ec54fee6ac7
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017

---
# <a name="limitations-in-azure-database-for-mysql-preview"></a>Limitações no Banco de Dados do Azure para MySQL (Visualização)
O Banco de Dados do Azure para o serviço do MySQL está em visualização pública. As seções a seguir descrevem a capacidade e os limites funcionais no serviço de banco de dados.

## <a name="service-tier-maximums"></a>Limites máximos da camada de serviço
O Banco de Dados do Azure para MySQL tem vários níveis de serviço que você pode escolher ao criar um servidor. Para saber mais, confira [Entenda o que está disponível em cada camada de serviço](concepts-service-tiers.md).  

Há um número máximo de conexões, unidades de computação e armazenamento em cada camada de serviço durante a visualização do serviço, da seguinte maneira: 

|                            |                   |
| :------------------------- | :---------------- |
| **Conexões máximas**        |                   |
| 50 unidades de computação básica     | 50 conexões    |
| 100 unidades de computação básica    | 100 conexões   |
| 100 unidades de computação standard | 200 conexões   |
| 200 unidades de computação standard | 300 conexões   |
| 400 unidades de computação standard | 400 conexões   |
| 800 unidades de computação standard | 500 conexões   |
| **Unidades de computação máxima**      |                   |
| Camada de serviço Básica         | 100 Unidades de computação |
| Camada de serviço Standard      | 800 Unidades de computação |
| **Armazenamento Máximo**            |                   |
| Camada de serviço Básica         | 1 TB              |
| Camada de serviço Standard      | 1 TB              |

Quando um número excessivo de conexões for atingido, você receberá o seguinte erro:
> ERRO 1040 (08004): número excessivo de conexões

## <a name="preview-functional-limitations"></a>Limitações funcionais de visualização:
### <a name="scale-operations"></a>Operações de dimensionamento:
1.  Não há suporte para o dimensionamento dinâmico de servidores por meio de camadas de serviço. Ou seja, alternando entre as camadas de serviço Básico e Standard.
2.  Não há suporte para o aumento sob demanda dinâmico de armazenamento no servidor criado previamente.
3.  Não há suporte para diminuir o tamanho de armazenamento do servidor.

### <a name="server-version-upgrades"></a>Atualizações da versão do servidor:
- Não há suporte para a migração automatizada entre versões de mecanismo de banco de dados principal.

### <a name="subscription-management"></a>Gerenciamento de assinaturas:
- Não há suporte para mover dinamicamente servidores criados previamente entre a assinatura e o grupo de recursos.

### <a name="point-in-time-restore"></a>Recuperação pontual:
1.  Não é permitido restaurar para a camada de serviço diferente e/ou Unidades de computação e Tamanho do armazenamento.
2.  Não há suporte para restaurar um servidor eliminado.

## <a name="next-steps"></a>Próximas etapas:
[O que está disponível em cada camada de serviço](concepts-service-tiers.md)
[Versões de Banco de Dados MySQL com suporte](concepts-supported-versions.md)

