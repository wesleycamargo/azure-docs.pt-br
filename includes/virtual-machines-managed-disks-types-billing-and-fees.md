---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0d771f03f9f71151ef25140148d4dd4daf3d46ec
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56443326"
---
**Transferências de dados de saída**: [Transferências de dados de saída](https://azure.microsoft.com/pricing/details/bandwidth/) (dados saindo dos data centers do Azure) acarretam a cobrança por uso de largura de banda.

**Transações**: Você será cobrado pelo número de transações executadas em um disco gerenciado padrão. O Azure cobra US$ 0,0036 por 100 mil transações para HDDs padrão. As transações incluem operações de leitura e gravação para o armazenamento.

SSDs padrão usam o tamanho da unidade de e/s de 256KB. Se os dados transferidos forem menores que 256 KB, serão considerados uma unidade de E/S. Tamanhos maiores de e/s são contados como várias entradas e saídas de tamanho de 256 KB. Por exemplo, uma E/S de 1.1000 KB é contada como cinco unidades de E/S.

Não há custo para transações de um disco gerenciado premium.

Para obter informações detalhadas sobre os preços para Managed Disks, confira [Preços do Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-ssd-vm-reservation-fee"></a>Taxa de reserva de VM de SSD Ultra

As VMs do Azure tem um recurso que indica se são compatíveis com o SSD ultra. Uma VM compatível com um disco ultra aloca a capacidade de largura de banda dedicada entre a instância de VM de computação e a unidade de escala de armazenamento de bloco para otimizar o desempenho e reduzir a latência. A inclusão desse recurso na VM resulta em uma cobrança de reserva que será imposta somente se você habilitar o recurso de disco ultra na VM sem anexar um disco ultra a ela. Quando um disco ultra é anexado à VM compatível com discos ultra, essa cobrança não é aplicada. Essa cobrança é por vCPU provisionada na VM.

Consulte a [página de preços do Azure Disks](https://azure.microsoft.com/pricing/details/managed-disks/) para ver detalhes de preços do Disks para discos ultra.