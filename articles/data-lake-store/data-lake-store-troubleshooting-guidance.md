---
title: Perguntas frequentes sobre o Azure Data Lake Store | Microsoft Docs
description: "Orientação sobre a solução de problemas ou a redução de problemas com o Azure Data Lake Store"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: bf7fd555-3e30-43ce-b28c-c3ad0a241fdb
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/02/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 84d644e07036f511e685b0fd020cdf8c310fd969


---
# <a name="frequently-asked-questions-for-azure-data-lake-store"></a>Perguntas frequentes sobre o Azure Data Lake Store
Neste artigo, você verá as perguntas frequentes relacionadas ao Azure Data Lake Store.

## <a name="how-do-i-handle-disaster-recovery-and-data-corruption-for-my-azure-data-lake-store-account"></a>Como eu lido com a recuperação de desastre e com a corrupção de dados para a minha conta do Azure Data Lake Store?
Os dados em sua conta do Azure Data Lake Store são resistentes a falhas transitórias de hardware dentro de uma região por meio de réplicas automatizadas. Isso garante a durabilidade e a alta disponibilidade, atendendo ao SLA do Azure Data Lake Store. Aqui estão algumas diretrizes sobre como proteger ainda mais seus dados contra as raras interrupções de toda a região ou contra exclusões acidentais.

### <a name="disaster-recovery-guidance"></a>Guia de recuperação de desastres
É essencial para todos os clientes preparar seu próprio plano de recuperação de desastre. Consulte a documentação do Azure abaixo para compilar seu plano de recuperação de desastre. Aqui estão alguns recursos que podem ajudar você a criar seu próprio plano.

* [Recuperação de desastre e alta disponibilidade para aplicativos do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)
* [Orientações técnicas de resiliência do Azure](../resiliency/resiliency-technical-guidance.md)

#### <a name="best-practices"></a>Práticas recomendadas
Recomendamos que você copie seus dados críticos para outra conta do Data Lake Store em outra região com uma frequência alinhada às necessidades de seu plano de recuperação de desastre. Há uma variedade de métodos para copiar dados, incluindo o [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), o [Azure PowerShell](data-lake-store-get-started-powershell.md) ou o [Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md). O Azure Data Factory é um serviço útil para criar e implantar pipelines de movimentação de dados de forma recorrente.

Se ocorrer uma interrupção regional, você poderá acessar os dados na região em que os dados foram copiados. Você pode monitorar o [Painel de Integridade do Serviço do Azure](https://azure.microsoft.com/status/) para determinar o status do serviço do Azure em todo o mundo.

### <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>Orientação de recuperação contra corrupção de dados ou exclusão acidental
Enquanto o Azure Data Lake Store oferece resiliência de dados por meio de réplicas automáticas, isso não impede que seu aplicativo (ou desenvolvedores/usuários) corrompa dados ou os exclua acidentalmente.

#### <a name="best-practices"></a>Práticas recomendadas
Para evitar a exclusão acidental, é recomendável que você defina primeiro as políticas de acesso correto para sua conta do Data Lake Store usando os [recursos de segurança do Data Lake Store](data-lake-store-security-overview.md) disponíveis. Também é recomendável que você crie cópias periódicas dos seus dados críticos usando o [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), o [Azure PowerShell](data-lake-store-get-started-powershell.md) ou o [Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md) em outra conta ou pasta do Data Lake Store, ou assinatura do Azure.  Isso pode ser usado para a recuperação de um incidente de corrupção ou de exclusão de dados. O Azure Data Factory é um serviço útil para criar e implantar pipelines de movimentação de dados de forma recorrente.

As organizações também podem habilitar o [log de diagnóstico](data-lake-store-diagnostic-logs.md) para sua conta de Azure Data Lake Store para coletar trilhas de auditoria de acesso de dados que fornecem informações sobre quem pode ter excluído ou atualizado um arquivo.

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Azure Data Lake Store](data-lake-store-get-started-portal.md)
* [Proteger dados no Repositório Data Lake](data-lake-store-secure-data.md)




<!--HONumber=Nov16_HO2-->


