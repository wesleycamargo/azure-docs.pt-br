---
title: Bancos de dados do Azure Governamental | Microsoft Docs
description: "Este guia fornece uma comparação dos recursos e orientações sobre como desenvolver aplicativos para o Azure Government"
services: azure-government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
ms.assetid: a1e173a9-996a-4091-a2e3-6b1e36da9ae1
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 11/14/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: c94e643565374f852633254a94c2138713cc290e
ms.openlocfilehash: 58d6295057c71dad488e04be25cc9a85d73bcd20


---
# <a name="azure-government-databases"></a>Bancos de dados do Azure Governamental
## <a name="sql-database"></a>Banco de Dados SQL
Consulte a <a href="https://msdn.microsoft.com/en-us/library/bb510589.aspx"> Central de Segurança da Microsoft para o Mecanismo de Banco de Dados SQL </a> e a [ Documentação pública do Banco de Dados SQL do Azure ](../sql-database/index.md) para obter orientação adicional sobre a configuração de visibilidade de metadados e práticas recomendadas de proteção.

### <a name="variations"></a>Variações
O Banco de Dados SQL V12 está disponível no Azure Governamental.

O endereço de servidores do SQL Azure no Azure Governamental é diferente:

| Tipo de serviço | Público do Azure | Azure Government |
| --- | --- | --- |
| Banco de Dados SQL |*.database.windows.net |*.database.usgovcloudapi.net |

### <a name="considerations"></a>Considerações
As informações a seguir identificam o limite do Azure Governamental para o Azure SQL:

| Dados regulamentados/controlados permitidos | Dados regulamentados/controlados não permitidos |
| --- | --- |
| Todos os dados armazenados e processados no Microsoft Azure SQL podem conter dados regulamentados do Azure Governamental. Você deve usar ferramentas de banco de dados para transferência de dados regulamentados do Azure Governamental. |Metadados do Azure SQL não podem conter dados de exportação controlados. Esses metadados incluem todos os dados de configuração inseridos durante a criação e manutenção de seu produto de armazenamento.  Não insira dados Regulamentados/controlados nos seguintes campos: Nomes de banco de dados, Nome de assinatura, Grupos de recursos, Nome de servidor, Logon de administrador do servidor, Nomes de implantação, Nomes de recursos, Marcas de recurso |

## <a name="azure-redis-cache"></a>Cache Redis do Azure
Para obter detalhes sobre esse serviço e como usá-lo, veja a [documentação pública do Cache Redis do Azure](../redis-cache/index.md).

### <a name="variations"></a>Variações
As URLs para acessar e gerenciar o Cache Redis do Azure no Azure Governamental são diferentes:

| Tipo de serviço | Público do Azure | Azure Government |
| --- | --- | --- |
| Ponto de extremidade do cache |*.redis.cache.windows.net |*.redis.cache.usgovcloudapi.net |
| Portal do Azure |https://portal.azure.com |https://portal.azure.us |

> [!NOTE]
> Todos os seus scripts e códigos precisam de uma conta para os pontos de extremidade e os ambientes apropriados. Para saber mais, veja [Como se conectar à Nuvem do Azure Governamental](../redis-cache/cache-howto-manage-redis-cache-powershell.md#how-to-connect-to-azure-government-cloud-or-azure-china-cloud).
> 
> 

### <a name="considerations"></a>Considerações
As informações a seguir identificam o limite do Azure Governamental para o Cache Redis do Azure:

| Dados regulamentados/controlados permitidos | Dados regulamentados/controlados não permitidos |
| --- | --- |
| Todos os dados armazenados e processados no Cache Redis do Azure podem conter dados regulamentados do Azure Governamental. |Os metadados do Cache Redis do Azure não podem conter dados de exportação controlados. Não insira dados regulamentados/controlados nos seguintes campos: Nome do cache, Nome da rede virtual, Nome da assinatura, Grupos de recursos, Marcas de recurso, Propriedades do Redis. |

## <a name="next-steps"></a>Próximas etapas
Para obter informações complementares e atualizações, assine o <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog do Microsoft Azure Governamental. </a>




<!--HONumber=Nov16_HO3-->


