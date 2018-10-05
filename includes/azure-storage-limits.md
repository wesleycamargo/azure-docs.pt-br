---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/25/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a0a777151216cb70b696da088b8a0d34779ebc39
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47396113"
---
A tabela a seguir descreve os limites padrão para o Armazenamento do Azure. O limite de *entrada* refere-se a todos os dados (solicitações) que estão sendo enviados a uma conta de armazenamento. O limite de *saída* refere-se a todos os dados (respostas) recebidos de uma conta de armazenamento.

| Recurso | Limite padrão |
| --- | --- |
| Número de contas de armazenamento por região por assinatura, incluindo contas Standard e Premium | 200 |
| Capacidade<sup>1</sup> máxima da conta de armazenamento | 500 TiB |
| Número máximo de contêineres de blob, blobs, compartilhamentos de arquivo, tabelas, filas, entidades ou mensagens por conta de armazenamento | Sem limite |
| Taxa<sup>1</sup> máxima de solicitação por conta de armazenamento | 20 mil solicitações por segundo |
| Entrada máxima<sup>1</sup> por conta de armazenamento (regiões dos EUA) | 10 Gbps se RA-GRS/GRS estiver habilitado, 20 Gbps para LRS/ZRS<sup>2</sup> |
| Saída máxima<sup>1</sup> por conta de armazenamento (regiões dos EUA) | 20 Gbps se RA-GRS/GRS estiver habilitado, 30 Gbps para LRS/ZRS |
| Entrada<sup>1</sup> máxima por conta de armazenamento (regiões fora dos EUA) | 5 Gbps se RA-GRS/GRS estiver habilitado, 10 Gbps para LRS/ZRS<sup>2</sup> |
| Saída<sup>1</sup> máxima por conta de armazenamento (regiões fora dos EUA) | 10 Gbps se RA-GRS/GRS estiver habilitado, 15 Gbps para LRS/ZRS |

<sup>1</sup> As contas de armazenamento do Azure dão suporte a limites mais altos de capacidade, taxa de solicitação, entrada e saída por solicitação. Para obter mais informações sobre o aumento dos limites, confira [Announcing larger, higher scale storage accounts](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/) (Anunciando contas de armazenamento maiores e de maior escala). Para solicitar um aumento nos limites de conta, contate o [Suporte do Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup>As opções de [Replicação do Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy) incluem:
* **RA-GRS**: armazenamento com redundância geográfica com acesso de leitura. Se o RA-GRS estiver habilitado, os destinos de saída para o local secundário serão idênticos àqueles para o local principal.
* **GRS**: armazenamento com redundância geográfica. 
* **ZRS**: armazenamento com redundância de zona.
* **LRS**: armazenamento com redundância local. 

