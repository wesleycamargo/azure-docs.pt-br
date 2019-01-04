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
ms.date: 12/12/2018
ms.openlocfilehash: 108d2ac83c0dc317dee2f8c66f95f01d3569a7c4
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53311653"
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
|Uso geral| 64| 1900|
|Otimizado para memória| 2| 300|
|Otimizado para memória| 4| 500|
|Otimizado para memória| 8| 960|
|Otimizado para memória| 16| 1900|
|Otimizado para memória| 32| 3000|

Quando as conexões excederem o limite, você poderá receber o seguinte erro:
> FATAL: já existem muitos clientes

O sistema do Azure exige cinco conexões para monitorar o Banco de Dados do Azure para o servidor PostgreSQL. 

## <a name="functional-limitations"></a>Limitações funcionais
### <a name="scale-operations"></a>Operações de dimensionamento
- O dimensionamento dinâmico de e para as camadas de preços básicas não tem suporte no momento.
- Atualmente, não há suporte para diminuir o tamanho de armazenamento do servidor.

### <a name="server-version-upgrades"></a>Upgrade da versão do servidor
- Não há suporte para a migração automatizada entre versões de mecanismo de banco de dados principal. Se você quiser atualizar para a próxima versão principal, faça um [despejo e restaure](./howto-migrate-using-dump-and-restore.md) para um servidor que foi criado com a nova versão do mecanismo.

### <a name="vnet-service-endpoints"></a>Ponto de extremidade de serviço VNet
- O suporte para ponto de extremidade de serviço de VNet é apenas para servidores de Uso Geral e Otimizados para Memória.

### <a name="restoring-a-server"></a>Restaurando um servidor
- Ao usar o recurso PITR, o novo servidor é criado com as mesmas configurações de camadas de preços que o servidor em que é baseado.
- O novo servidor criado durante uma restauração não possui as regras de firewall existentes no servidor original. As regras de firewall precisam ser configuradas separadamente para esse novo servidor.
- Não há suporte para restaurar um servidor eliminado.

### <a name="utf-8-characters-on-windows"></a>Caracteres UTF-8 no Windows
- Em alguns cenários, não há suporte completo para caracteres UTF-8 no PostgreSQL de software livre no Windows, o que afeta o Banco de Dados do Azure para PostgreSQL. Confira o thread [Bug nº 15476 nos arquivos do PostgreSQL](https://www.postgresql-archive.org/BUG-15476-Problem-on-show-trgm-with-4-byte-UTF-8-characters-td6056677.html) para obter mais informações.

## <a name="next-steps"></a>Próximas etapas
- Entenda [o que está disponível em cada tipo de preço](concepts-pricing-tiers.md)
- Saiba mais sobre [Versões de Banco de Dados PostgreSQL com suporte](concepts-supported-versions.md)
- Veja [Como fazer backup e restaurar um servidor no Banco de Dados do Azure para PostgreSQL usando o Portal do Azure](howto-restore-server-portal.md)
