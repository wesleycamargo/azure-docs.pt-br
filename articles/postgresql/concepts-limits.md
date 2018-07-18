---
title: Limitações no Banco de Dados do Azure para PostgreSQL
description: Este artigo descreve limitações no Banco de Dados do Azure para PostgreSQL, como número de opções de mecanismo de armazenamento e conexão.
services: postgresql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 03/20/2018
ms.openlocfilehash: 7e06cdba7c9c9f7e5c1d621e7421a18c342c0fdb
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2018
---
# <a name="limitations-in-azure-database-for-postgresql"></a>Limitações no Banco de Dados do Azure para PostgreSQL
As seções a seguir descrevem a capacidade e os limites funcionais no serviço de banco de dados.

## <a name="pricing-tier-maximums"></a>Tipos de preço máximos
O Banco de Dados do Azure para PostgreSQL possui vários tipos de preço que você pode escolher ao criar um servidor. Para obter mais informações, consulte [Tipos de preço no Banco de Dados do Azure para PostgreSQL](concepts-pricing-tiers.md).  

Há um número máximo de conexões, unidades de computação e armazenamento em tipo de preço, conforme a seguir: 

|Camada de preços| Geração de computação| vCore(s)| Máximo de conexões |
|---|---|---|---|
|Basic| Gen 4| 1| 50 |
|Basic| Gen 4| 2| 100 |
|Basic| Gen 5| 1| 50 |
|Basic| Gen 5| 2| 100 |
|Uso geral| Gen 4| 2| 150|
|Uso geral| Gen 4| 4| 250|
|Uso geral| Gen 4| 8| 480|
|Uso geral| Gen 4| 16| 950|
|Uso geral| Gen 4| 32| 1500|
|Uso geral| Gen 5| 2| 150|
|Uso geral| Gen 5| 4| 250|
|Uso geral| Gen 5| 8| 480|
|Uso geral| Gen 5| 16| 950|
|Uso geral| Gen 5| 32| 1500|
|Otimizado para memória| Gen 5| 2| 150|
|Otimizado para memória| Gen 5| 4| 250|
|Otimizado para memória| Gen 5| 8| 480|
|Otimizado para memória| Gen 5| 16| 950|

Quando as conexões excederem o limite, você poderá receber o seguinte erro:
> FATAL: já existem muitos clientes

O sistema do Azure exige cinco conexões para monitorar o Banco de Dados do Azure para o servidor PostgreSQL. 

## <a name="functional-limitations"></a>Limitações funcionais
### <a name="scale-operations"></a>Operações de dimensionamento
1.  Atualmente, não há suporte para o dimensionamento dos servidores em tipos de preço. Ou seja, alternando entre Básico, Uso Geral e Otimizado para Memória.
2.  Atualmente, não há suporte para diminuir o tamanho de armazenamento do servidor.

### <a name="server-version-upgrades"></a>Upgrade da versão do servidor
- Não há suporte para a migração automatizada entre versões de mecanismo de banco de dados principal.

### <a name="subscription-management"></a>Gerenciamento de assinaturas
- Atualmente, não há suporte para servidores que deslocam-se dinamicamente em assinaturas e grupos de recursos.

### <a name="point-in-time-restore-pitr"></a>PITR (Restauração Pontual)
1.  Ao usar o recurso PITR, o novo servidor é criado com as mesmas configurações nas quais o servidor está baseado.
2.  Não há suporte para restaurar um servidor eliminado.

## <a name="next-steps"></a>Próximas etapas
- Entenda [o que está disponível em cada tipo de preço](concepts-pricing-tiers.md)
- Saiba mais sobre [Versões de Banco de Dados PostgreSQL com suporte](concepts-supported-versions.md)
- Veja [Como fazer backup e restaurar um servidor no Banco de Dados do Azure para PostgreSQL usando o Portal do Azure](howto-restore-server-portal.md)
