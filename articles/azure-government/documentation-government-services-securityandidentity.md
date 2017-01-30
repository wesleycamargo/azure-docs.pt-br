---
title: "Segurança do Azure Governamental + identidade | Microsoft Docs"
description: "Este guia fornece uma comparação dos recursos e orientações sobre como desenvolver aplicativos para o Azure Government"
services: azure-government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
ms.assetid: e2fe7983-5870-43e9-ae01-2d45d3102c8a
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 11/14/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: cd01170c3c0a3f62024de3357d342af1f4f90c6c
ms.openlocfilehash: 27d447e8e3c336bbce2e1ca81d2c7c413b0360fc


---
# <a name="azure-government-security--identity"></a>Segurança do Azure Governamental + identidade
## <a name="key-vault"></a>Cofre da Chave
Para obter detalhes sobre esse serviço e como usá-lo, consulte a [documentação pública do Cofre de Chaves do Azure](../key-vault/index.md).

### <a name="data-considerations"></a>Considerações de dados
As informações a seguir identificam o limite do Azure Governamental para o Cofre de Chaves do Azure:

| Dados regulamentados/controlados permitidos | Dados regulamentados/controlados não permitidos |
| --- | --- |
| Todos os dados criptografados com uma chave do Cofre de Chaves do Azure podem conter dados regulamentados/controlados. |Metadados do Cofre de Chaves do Azure não são permitidos para conter dados de exportação controlados. Esses metadados incluem todos os dados de configuração inseridos durante a criação e manutenção de seu Cofre de Chaves.  Não insira dados Regulamentados/controlados nos seguintes campos: Nomes de grupo de recursos, Nomes de Cofre de Chaves, Nome de assinatura |

O Cofre de Chaves está disponível no Azure Governamental. Assim como no público, não há qualquer extensão, portanto o Cofre de Chaves só está disponível por meio do PowerShell e da CLI.

## <a name="next-steps"></a>Próximas etapas
Para obter informações complementares e atualizações, assine o <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog do Microsoft Azure Governamental. </a>




<!--HONumber=Nov16_HO3-->


