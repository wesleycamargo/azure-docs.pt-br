---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/11/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ee9b70827c19236287f81f66f4b9c6e1004a54fd
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/12/2019
ms.locfileid: "59532580"
---
A tabela a seguir descreve os limites padrão para o Azure uso geral v1, v2 e contas de armazenamento de Blob. O *ingresso* limite refere-se a todos os dados de solicitações que são enviadas para uma conta de armazenamento. O *egresso* limite refere-se a todos os dados de respostas são recebidas de uma conta de armazenamento.

| Recurso | Limite padrão |
| --- | --- |
| Número de contas de armazenamento por região por assinatura, incluindo contas Standard e Premium | 250 |
| Capacidade da conta de armazenamento máximo | 2 PB para os EUA e Europa, 500 TB para todas as outras regiões, que inclui o Reino Unido |
| Número máximo de contêineres de blob, blobs, compartilhamentos de arquivos, tabelas, filas, entidades ou mensagens por conta de armazenamento | Sem limite |
| Taxa<sup>1</sup> máxima de solicitação por conta de armazenamento | 20 mil solicitações por segundo |
| Máximo de entrada<sup>1</sup> por conta de armazenamento (regiões dos EUA) | 10 Gbps se RA-GRS/GRS estiver habilitado, 20 Gbps para LRS/ZRS<sup>2</sup> |
| Máximo de entrada<sup>1</sup> por conta de armazenamento (regiões fora dos EUA) | 5 Gbps se RA-GRS/GRS estiver habilitado, 10 Gbps para LRS/ZRS<sup>2</sup> |
| Saída máxima para uso geral v2 e contas de armazenamento de BLOBs (todas as regiões) | 50 Gbps |
| Saída máxima para contas de armazenamento de uso geral v1 (regiões dos EUA) | 20 Gbps se RA-GRS/GRS estiver habilitado, 30 Gbps para LRS/ZRS<sup>2</sup> |
| Saída máxima para contas de armazenamento de uso geral v1 (regiões fora dos EUA) | 10 Gbps se RA-GRS/GRS estiver habilitado, 15 Gbps para LRS/ZRS<sup>2</sup> |

<sup>1</sup>contas de armazenamento Standard do azure dar suporte a limites mais altos para a entrada por solicitação. Para solicitar um aumento nos limites de conta para a entrada, entre em contato com [suporte do Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup> As opções de [replicação do Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy) incluem:
* **RA-GRS**: armazenamento com redundância geográfica com acesso de leitura. Se o RA-GRS estiver habilitado, os destinos de saída para o local secundário serão idênticos àqueles para o local principal.
* **GRS**: armazenamento com redundância geográfica. 
* **ZRS**: armazenamento com redundância de zona.
* **LRS**: armazenamento com redundância local. 

> [!NOTE]
> É recomendável que você use uma conta de armazenamento de uso geral v2 para a maioria dos cenários. Você pode atualizar facilmente um v1 de uso geral ou uma conta de armazenamento de BLOBs do Azure para uma conta sem tempo de inatividade e sem a necessidade de copiar dados de uso geral v2.
>
> Para obter mais informações sobre contas de armazenamento do Azure, consulte [visão geral da conta de armazenamento](../articles/storage/common/storage-account-overview.md). 

Se as necessidades do seu aplicativo excederem as metas de escalabilidade de uma conta única de armazenamento, você pode preparar seu aplicativo para usar várias contas de armazenamento. Em seguida, particione seus objetos de dados nessas contas de armazenamento. Para obter informações sobre preço por volume, consulte [preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).

Todas as contas de armazenamento são executadas em uma topologia de rede simples e suportam as metas de escalabilidade e desempenho descritas neste artigo, independentemente de quando foram criadas. Para obter mais informações sobre a arquitetura de rede simples do armazenamento do Azure e sobre escalabilidade, confira [Armazenamento do Microsoft Azure: um serviço de armazenamento em nuvem altamente disponível com coerência forte](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).

