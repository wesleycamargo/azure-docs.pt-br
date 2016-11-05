---
title: Documentação do Azure Governamental | Microsoft Docs
description: Este guia fornece uma comparação dos recursos e orientações sobre como desenvolver aplicativos para o Azure Government
services: Azure-Government
cloud: gov
documentationcenter: ''
author: ryansoc
manager: zakramer
editor: ''

ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 08/25/2016
ms.author: ryansoc

---
# Dados e armazenamento do Azure Governamental
## Armazenamento
As informações a seguir identificam o limite do Azure Governamental para o Armazenamento do Azure:

| Dados regulamentados/controlados permitidos | Dados regulamentados/controlados não permitidos |
| --- | --- |
| Dados inseridos, armazenados e processados em um produto de Armazenamento do Azure podem conter dados de exportação controlados. Autenticadores estáticos, como senhas e PINs de cartão inteligente para acesso aos componentes da plataforma Azure. Chaves privadas de certificados usados para gerenciar os componentes da plataforma Azure. Outros informações/segredos de segurança, como certificados, chaves de criptografia, chaves mestras e chaves de armazenamento armazenadas nos serviços do Azure. |Metadados do Armazenamento do Azure não podem conter dados de exportação controlados. Esses metadados incluem todos os dados de configuração inseridos durante a criação e manutenção de seu produto de armazenamento. Não insira dados Regulamentados/controlados nos seguintes campos: Grupos de recursos, Nomes de implantação, Nomes de recursos, Marcas de recurso |

Para saber mais, consulte a <a href=https://azure.microsoft.com/documentation/services/storage/> Documentação pública do Armazenamento do Azure</a>.

Para obter informações complementares e atualizações, assine o <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog do Microsoft Azure Governamental. </a>

## Banco de Dados SQL
As informações a seguir identificam o limite do Azure Governamental para o Armazenamento do Azure:

| Dados regulamentados/controlados permitidos | Dados regulamentados/controlados não permitidos |
| --- | --- |
| Todos os dados armazenados e processados no Microsoft Azure SQL podem conter dados regulamentados do Azure Governamental. Você deve usar ferramentas de banco de dados para transferência de dados regulamentados do Azure Governamental. |Metadados do Azure SQL não podem conter dados de exportação controlados. Esses metadados incluem todos os dados de configuração inseridos durante a criação e manutenção de seu produto de armazenamento. Não insira dados Regulamentados/controlados nos seguintes campos: Nomes de banco de dados, Nome de assinatura, Grupos de recursos, Nome de servidor, Logon de administrador do servidor, Nomes de implantação, Nomes de recursos, Marcas de recurso |

O Banco de Dados SQL v1.1 está disponível no Azure Governamental.

Consulte o <a href="https://msdn.microsoft.com/pt-BR/library/bb510589.aspx"> Microsoft Security Center para o mecanismo de Banco de Dados SQL </a> e a <a href="https://azure.microsoft.com/documentation/services/sql-database/"> Documentação pública do Banco de Dados SQL do Azure </a> para obter orientação adicional sobre a configuração de visibilidade de metadados e práticas recomendadas de proteção.

Para obter informações complementares e atualizações, assine o <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog do Microsoft Azure Governamental. </a>

<!---HONumber=AcomDC_0831_2016-->