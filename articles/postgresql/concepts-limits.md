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
ms.openlocfilehash: 0bd894e99fd575fca734a101a1cde0f0a2979682
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2018
ms.locfileid: "45983725"
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
- Não há suporte para a migração automatizada entre versões de mecanismo de banco de dados principal. Se você quiser atualizar para a próxima versão principal, faça um [dump e restaure](./howto-migrate-using-dump-and-restore.md) para um servidor que foi criado com a nova versão do mecanismo.

### <a name="vnet-service-endpoints"></a>Ponto de extremidade de serviço VNet
- O suporte para ponto de extremidade de serviço de VNet é apenas para servidores de Uso Geral e Otimizados para Memória.

### <a name="restoring-a-server"></a>Restaurando um servidor
- Ao usar o recurso PITR, o novo servidor é criado com as mesmas configurações de camadas de preços que o servidor em que é baseado.
- O novo servidor criado durante uma restauração não possui as regras de firewall existentes no servidor original. As regras de firewall precisam ser configuradas separadamente para esse novo servidor.
- Não há suporte para restaurar um servidor eliminado.

## <a name="next-steps"></a>Próximas etapas
- Entenda [o que está disponível em cada tipo de preço](concepts-pricing-tiers.md)
- Saiba mais sobre [Versões de Banco de Dados PostgreSQL com suporte](concepts-supported-versions.md)
- Veja [Como fazer backup e restaurar um servidor no Banco de Dados do Azure para PostgreSQL usando o Portal do Azure](howto-restore-server-portal.md)
