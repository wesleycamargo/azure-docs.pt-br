---
title: Armazenamento do Azure Governamental | Microsoft Docs
description: "Este guia fornece uma comparação dos recursos e orientações sobre como desenvolver aplicativos para o Azure Government"
services: Azure-Government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
editor: 
ms.assetid: 83df022b-d791-4efb-9fdf-8afe47a885d5
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/13/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: 004860b319b7b32955415ac2d6c7308c32037848
ms.openlocfilehash: 36312ce195e931d174e00d54cab4cb071a85b618


---
# <a name="azure-government-storage"></a>Armazenamento do Azure Governamental
## <a name="azure-storage"></a>Armazenamento do Azure
Para obter detalhes sobre esse serviço e sobre como usá-lo, veja a [documentação pública do Cofre de Chaves do Azure](../storage/index.md).

### <a name="variations"></a>Variações
As URLs para contas de armazenamento no Azure Governamental são diferentes:

| Tipo de serviço | Público do Azure | Azure Government |
| --- | --- | --- |
| Armazenamento de Blob |*.blob.core.windows.net |*.blob.core.usgovcloudapi.net |
| Armazenamento de Fila |*.queue.core.windows.net |*.queue.core.usgovcloudapi.net |
| Armazenamento de tabela |*.table.core.windows.net |*.table.core.usgovcloudapi.net |

> [!NOTE]
> Todos os seus scripts e códigos precisam levar em conta os pontos de extremidade apropriados.  Veja [Configurar cadeias de conexão do Armazenamento do Azure](../storage/storage-configure-connection-string.md). 
>
>

Para saber mais sobre APIs, veja o <a href="https://msdn.microsoft.com/en-us/library/azure/mt616540.aspx"> Construtor de Conta de Armazenamento em Nuvem</a>.

O sufixo de ponto de extremidade a ser usado nessas sobrecargas é core.usgovcloudapi.net

### <a name="considerations"></a>Considerações
As informações a seguir identificam o limite do Azure Governamental para o Armazenamento do Azure:

| Dados regulamentados/controlados permitidos | Dados regulamentados/controlados não permitidos |
| --- | --- |
| Os dados inseridos, armazenados e processados em um produto de Armazenamento do Azure podem conter dados de exportação controlados. Autenticadores estáticos, como senhas e PINs de cartão inteligente para acesso aos componentes da plataforma Azure. Chaves privadas de certificados usados para gerenciar os componentes da plataforma Azure. Outros informações/segredos de segurança, como certificados, chaves de criptografia, chaves mestras e chaves de armazenamento armazenadas nos serviços do Azure. |Metadados do Armazenamento do Azure não podem conter dados de exportação controlados. Esses metadados incluem todos os dados de configuração inseridos durante a criação e manutenção de seu produto de armazenamento.  Não insira dados Regulamentados/controlados nos seguintes campos: Grupos de recursos, Nomes de implantação, Nomes de recursos, Marcas de recurso |

## <a name="premium-storage"></a>Armazenamento Premium
Para obter detalhes sobre esse serviço e sobre como usá-lo, veja [Armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho de máquina Virtual do Azure](../storage/storage-premium-storage.md).

### <a name="variations"></a>Variações
O Armazenamento Premium está disponível no Governo dos EUA - Virgínia. Isso inclui Máquinas Virtuais da série DS.

### <a name="considerations"></a>Considerações
As mesmas considerações de dados de armazenamento acima se aplicam a contas de armazenamento premium.

## <a name="next-steps"></a>Próximas etapas
Para obter informações complementares e atualizações, assine o <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog do Microsoft Azure Governamental. </a>



<!--HONumber=Nov16_HO3-->


