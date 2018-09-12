---
title: Diretrizes de recuperação de desastre para o Azure Data Lake Store | Microsoft Docs
description: Diretrizes sobre recuperação de desastre para o Azure Data Lake Store
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: b51f0c1e0c6ef713bf8d3ff0a124300f446a9373
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43306801"
---
# <a name="disaster-recovery-guidance-for-data-in-data-lake-store"></a>Diretrizes de recuperação de desastre para dados no Azure Data Lake Store

O Azure Data Lake Store fornece armazenamento com redundância local (LRS). Portanto, os dados na conta do Azure Data Lake Store são resilientes a falhas de hardware transitórias em um datacenter por meio de réplicas automatizadas. Isso garante a durabilidade e a alta disponibilidade, atendendo ao SLA do Azure Data Lake Store. Este artigo fornece diretrizes de como proteger ainda mais seus dados contra as raras interrupções de toda a região ou contra exclusões acidentais.

## <a name="disaster-recovery-guidance"></a>Guia de recuperação de desastres
É essencial para todos os clientes preparar seu próprio plano de recuperação de desastre. Leia as informações neste artigo para criar seu plano de recuperação de desastres. Aqui estão alguns recursos que podem ajudar você a criar seu próprio plano.

* [Recuperação de desastre e alta disponibilidade para aplicativos do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)
* [Orientações técnicas de resiliência do Azure](../resiliency/resiliency-technical-guidance.md)

### <a name="best-practices"></a>Práticas recomendadas
Recomendamos que você copie seus dados críticos para outra conta do Data Lake Store em outra região com uma frequência alinhada às necessidades de seu plano de recuperação de desastre. Há uma variedade de métodos para copiar dados, incluindo o [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), o [Azure PowerShell](data-lake-store-get-started-powershell.md) ou o [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md). O Azure Data Factory é um serviço útil para criar e implantar pipelines de movimentação de dados de forma recorrente.

Se ocorrer uma interrupção regional, você poderá acessar os dados na região em que os dados foram copiados. Você pode monitorar o [Painel de Integridade do Serviço do Azure](https://azure.microsoft.com/status/) para determinar o status do serviço do Azure em todo o mundo.

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>Orientação de recuperação contra corrupção de dados ou exclusão acidental
Enquanto o Azure Data Lake Store oferece resiliência de dados por meio de réplicas automáticas, isso não impede que seu aplicativo (ou desenvolvedores/usuários) corrompa dados ou os exclua acidentalmente.

### <a name="best-practices"></a>Práticas recomendadas
Para evitar a exclusão acidental, é recomendável que você defina primeiro as políticas de acesso correto para sua conta do Data Lake Store.  Isso inclui a aplicação de [bloqueios de recursos do Azure](../azure-resource-manager/resource-group-lock-resources.md) para bloquear recursos importantes, bem como aplicar controle de acesso de nível conta e o arquivo usando os [recursos de segurança do Data Lake Store](data-lake-store-security-overview.md) disponíveis. Também é recomendável que você crie cópias periódicas dos seus dados críticos usando o [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), o [Azure PowerShell](data-lake-store-get-started-powershell.md) ou o [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) em outra conta ou pasta do Data Lake Store, ou assinatura do Azure.  Isso pode ser usado para a recuperação de um incidente de corrupção ou de exclusão de dados. O Azure Data Factory é um serviço útil para criar e implantar pipelines de movimentação de dados de forma recorrente.

As organizações também podem habilitar o [log de diagnóstico](data-lake-store-diagnostic-logs.md) para sua conta de Azure Data Lake Store para coletar trilhas de auditoria de acesso de dados que fornecem informações sobre quem pode ter excluído ou atualizado um arquivo.

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Azure Data Lake Store](data-lake-store-get-started-portal.md)
* [Proteger dados no Data Lake Store](data-lake-store-secure-data.md)

