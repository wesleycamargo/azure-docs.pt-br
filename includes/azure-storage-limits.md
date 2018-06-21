---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/03/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 6572adb0d8d629910492603a17988b89acce2f17
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/07/2018
ms.locfileid: "34852051"
---
| Recurso | Limite padrão |
| --- | --- |
| Número de contas de armazenamento por regiõ por assinatura | 200<sup>1</sup> |
| Capacidade máxima da conta de armazenamento | 500 TiB<sup>2</sup> |
| Número máximo de contêineres de blob, blobs, compartilhamentos de arquivo, tabelas, filas, entidades ou mensagens por conta de armazenamento | Sem limite |
| Taxa máxima de solicitação por conta de armazenamento | 20.000 solicitações por segundo<sup>2</sup> |
| Entrada máxima<sup>3</sup> por conta de armazenamento (Regiões dos EUA) | 10 Gbps se RA-GRS/GRS estiver habilitado, 20 Gbps para LRS/ZRS<sup>4</sup> |
| Saída máxima<sup>3</sup> por conta de armazenamento (Regiões dos EUA) | 20 Gbps se RA-GRS/GRS estiver habilitado, 30 Gbps para LRS/ZRS<sup>4</sup> |
| Entrada máxima<sup>3</sup> por conta de armazenamento (Regiões fora dos EUA) | 5 Gbps se RA-GRS/GRS estiver habilitado, 10 Gbps para LRS/ZRS<sup>4</sup> |
| Saída máxima<sup>3</sup> por conta de armazenamento (Regiões fora dos EUA) | 10 Gbps se RA-GRS/GRS estiver habilitado, 15 Gbps para LRS/ZRS<sup>4</sup> |

<sup>1</sup>Inclui contas de armazenamento Standard e Premium. Se você precisar de mais de 200 contas de armazenamento em uma única região, faça uma solicitação por meio do [Suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/). A equipe de Armazenamento do Microsoft Azure examinará seu caso de negócios e poderá aprovar até 250 contas de armazenamento. 

<sup>2</sup> Se você precisar de limites expandidos para a conta de armazenamento, contate o [Suporte do Azure](https://azure.microsoft.com/support/faq/). A equipe de Armazenamento do Azure examinará a solicitação e poderá aprovar limites maiores caso a caso. Ambas as contas de armazenamento de Blobs e de uso geral dão suporte para o aumento de capacidade, ingresso/saída e taxa de solicitações por solicitação. Para os novos máximos de contas de armazenamento de Blobs, consulte [Anunciando contas de armazenamento maiores e de maior escala](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

<sup>3</sup> Limitado somente pelos limites de entrada/saída da conta. A *entrada* refere-se a todos os dados (solicitações) que estão sendo enviados a uma conta de armazenamento. *Saída* refere-se a todos os dados (respostas) recebidos de uma conta de armazenamento.  

<sup>4</sup>As opções de redundância de Armazenamento do Azure incluem:
* **RA-GRS**: armazenamento com redundância geográfica com acesso de leitura. Se o RA-GRS estiver habilitado, os destinos de saída para o local secundário serão idênticos àqueles para o local principal.
* **GRS**: armazenamento com redundância geográfica. 
* **ZRS**: armazenamento com redundância de zona.
* **LRS**: armazenamento com redundância local. 
