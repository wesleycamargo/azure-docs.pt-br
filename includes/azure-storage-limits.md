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
ms.openlocfilehash: 1dd11e22361e25721effe2ed919f175d9cb1b9e4
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2019
ms.locfileid: "54249620"
---
A tabela a seguir descreve os limites padrão para o Armazenamento do Azure. O limite de *entrada* refere-se a todos os dados (solicitações) que estão sendo enviados a uma conta de armazenamento. O limite de *saída* refere-se a todos os dados (respostas) recebidos de uma conta de armazenamento.

| Recurso | Limite padrão |
| --- | --- |
| Número de contas de armazenamento por região por assinatura, incluindo contas Standard e Premium | 250 |
| Capacidade máxima da conta de armazenamento | 2 PB para os EUA e Europa, 500 TB para todas as outras regiões, incluindo do Reino Unido |
| Número máximo de contêineres de blob, blobs, compartilhamentos de arquivo, tabelas, filas, entidades ou mensagens por conta de armazenamento | Sem limite |
| Taxa<sup>1</sup> máxima de solicitação por conta de armazenamento | 20 mil solicitações por segundo |
| Entrada máxima<sup>1</sup> por conta de armazenamento (regiões dos EUA) | 10 Gbps se RA-GRS/GRS estiver habilitado, 20 Gbps para LRS/ZRS<sup>2</sup> |
| Entrada<sup>1</sup> máxima por conta de armazenamento (regiões fora dos EUA) | 5 Gbps se RA-GRS/GRS estiver habilitado, 10 Gbps para LRS/ZRS<sup>2</sup> |
| Saída máxima para uso geral v2 e contas de armazenamento de BLOBs (todas as regiões) | 50 Gbps |
| Saída máxima para contas de armazenamento v1 de uso geral (regiões dos EUA) | 20 Gbps se RA-GRS/GRS estiver habilitado, 30 Gbps para LRS/ZRS <sup>2</sup> |
| Saída máxima para contas de armazenamento v1 de uso geral (regiões fora dos EUA) | 10 Gbps, se o RA-GRS / GRS estiver ativado, 15 Gbps para o LRS / ZRS <sup>2</sup> |

<sup>1</sup> As contas de armazenamento do Azure dão suporte a limites mais altos para a entrada e IOPS por solicitação. Para solicitar um aumento nos limites da conta, contate o [Suporte do Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup> As opções de [replicação do Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy) incluem:
* **RA-GRS**: armazenamento com redundância geográfica com acesso de leitura. Se o RA-GRS estiver habilitado, os destinos de saída para o local secundário serão idênticos àqueles para o local principal.
* **GRS**: armazenamento com redundância geográfica. 
* **ZRS**: armazenamento com redundância de zona.
* **LRS**: armazenamento com redundância local. 

> [!NOTE]
> A Microsoft recomenda o uso de contas de armazenamento para uso geral v2 na maioria dos cenários. É possível atualizar facilmente de uma conta de armazenamento de blobs ou para uso geral v1 para uma v2 sem tempo de inatividade e sem a necessidade de copiar dados.
>
> Para saber mais sobre as contas de Armazenamento do Microsoft Azure, confira [Visão geral da conta de armazenamento do Microsoft Azure](../articles/storage/common/storage-account-overview.md). 

Se as necessidades do seu aplicativo excederem as metas de escalabilidade de uma conta única de armazenamento, você pode preparar seu aplicativo para usar várias contas de armazenamento. Em seguida, particione seus objetos de dados nessas contas de armazenamento. Consulte [Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/) para obter informações sobre preço por volume.

Todas as contas de armazenamento são executadas em uma topologia de rede simples e suportam as metas de escalabilidade e desempenho descritas neste artigo, independentemente de quando foram criadas. Para obter mais informações sobre a arquitetura de rede simples do armazenamento do Azure e sobre escalabilidade, confira [Armazenamento do Microsoft Azure: um serviço de armazenamento em nuvem altamente disponível com coerência forte](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).

