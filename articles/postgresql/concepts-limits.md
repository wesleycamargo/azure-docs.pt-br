---
title: Limitações no Banco de Dados do Azure para PostgreSQL
description: Este artigo descreve limitações no Banco de Dados do Azure para PostgreSQL, como número de opções de mecanismo de armazenamento e conexão.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 06/30/2018
ms.openlocfilehash: dc1f8581df5dc7c5728094577298ba078cc2c527
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2018
ms.locfileid: "37343127"
---
# <a name="limitations-in-azure-database-for-postgresql"></a>Limitações no Banco de Dados do Azure para PostgreSQL
As seções a seguir descrevem a capacidade e os limites funcionais no serviço de banco de dados.

## <a name="maximum-connections"></a>Número máximo de conexões
O número máximo de conexões por tipo de preço e vCores é o seguinte: 

|**Tipo de preço**| **vCore(s)**| **Máximo de conexões** |
|---|---|---|
|Basic| 1| 50 |
|Basic| 2| 100 |
|Uso geral| 2| 150|
|Uso geral| 4| 250|
|Uso geral| 8| 480|
|Uso geral| 16| 950|
|Uso geral| 32| 1500|
|Otimizado para memória| 2| 150|
|Otimizado para memória| 4| 250|
|Otimizado para memória| 8| 480|
|Otimizado para memória| 16| 950|

Quando as conexões excederem o limite, você poderá receber o seguinte erro:
> FATAL: já existem muitos clientes

O sistema do Azure exige cinco conexões para monitorar o Banco de Dados do Azure para o servidor PostgreSQL. 

## <a name="functional-limitations"></a>Limitações funcionais
### <a name="scale-operations"></a>Operações de dimensionamento
- O dimensionamento dinâmico de e para as camadas de preços básicas não tem suporte no momento.
- Atualmente, não há suporte para diminuir o tamanho de armazenamento do servidor.

### <a name="server-version-upgrades"></a>Upgrade da versão do servidor
- Não há suporte para a migração automatizada entre versões de mecanismo de banco de dados principal.

### <a name="subscription-management"></a>Gerenciamento de assinaturas
- Atualmente, não há suporte para servidores que deslocam-se dinamicamente em assinaturas e grupos de recursos.

### <a name="vnet-service-endpoints"></a>Pontos de extremidade de serviço de VNet
- O suporte para ponto de extremidade de serviço de VNet é apenas para servidores de Uso Geral e Otimizados para Memória.

### <a name="point-in-time-restore-pitr"></a>PITR (Restauração Pontual)
- Ao usar o recurso PITR, o novo servidor é criado com as mesmas configurações nas quais o servidor está baseado.
- Não há suporte para restaurar um servidor eliminado.

## <a name="next-steps"></a>Próximas etapas
- Entenda [o que está disponível em cada tipo de preço](concepts-pricing-tiers.md)
- Saiba mais sobre [Versões de Banco de Dados PostgreSQL com suporte](concepts-supported-versions.md)
- Veja [Como fazer backup e restaurar um servidor no Banco de Dados do Azure para PostgreSQL usando o Portal do Azure](howto-restore-server-portal.md)
