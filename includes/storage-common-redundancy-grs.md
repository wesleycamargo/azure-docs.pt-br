---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/26/2018
ms.author: jeking
ms.custom: include file
ms.openlocfilehash: 922f4d5dd8c71bc9365523b1d539d1b0754fff15
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/14/2018
ms.locfileid: "45580932"
---
O armazenamento com redundância geográfica (GRS) foi desenvolvido para fornecer pelo menos 99.99999999999999% (16 9’s) durabilidade dos objetos em um determinado ano, replicando dados para uma região secundária situada a centenas de milhas de distância da região primária. Se sua conta de armazenamento tem GRS habilitado, seus dados serão duráveis mesmo no caso de uma interrupção regional completa ou um desastre no qual a região principal não possa ser recuperada.

Se você optar pelo GRS, você tem duas opções relacionadas para escolher:

* O GRS replica seus dados para outro datacenter em uma região secundária, mas esses dados estão disponíveis para serem lidos somente se a Microsoft iniciar um failover da região primária para a secundária. 
* O armazenamento com redundância geográfica de acesso de leitura (RA-GRS) é baseado no GRS. O RA-GRS replica seus dados para outro datacenter em uma região secundária e também fornece a opção de ler a partir da região secundária. Com o RA-GRS, você pode ler a partir do secundário, independentemente de a Microsoft iniciar um failover do primário para o secundário. 

Para uma conta de armazenamento com GRS ou RA-GRS habilitada, todos os dados são primeiro replicados com armazenamento redundante localmente (LRS). Uma atualização primeiro é confirmada para o local primário e replicados usando o LRS. A atualização, em seguida, é replicada assincronamente para a região secundária usando GRS. Quando dados são gravados para o local secundário, ela também é replicada dentro desse local usando o LRS. 

Ambas as regiões primárias e secundárias gerenciam réplicas entre domínios de falha separados e atualizar domínios dentro de uma unidade de escala de armazenamento. A unidade de escala de armazenamento é a unidade de replicação básica dentro do datacenter. A replicação com esse nível é fornecida por LRS; Para obter mais informações, consulte [armazenamento localmente redundante (LRS): redundância de dados de baixo custo para armazenamento do Azure](../articles/storage/common/storage-redundancy-lrs.md).

Lembre-se esses pontos ao decidir qual opção de replicação para usar:

* Armazenamento com redundância de zona (ZRS) fornece alta disponibilidade com replicação síncrona e pode ser uma escolha melhor em alguns cenários que GRS ou RA-GRS. Para obter mais informações sobre o ZRS, consulte [ZRS](../articles/storage/common/storage-redundancy-zrs.md).
* Como a replicação assíncrona envolve um atraso, no caso de um desastre regional, é possível que as alterações que ainda não foram replicadas para a região secundária sejam perdidas se os dados não puderem ser recuperados da região primária.
* Com o GRS, a réplica não está disponível, a menos que a Microsoft inicie o failover na região secundária. Se a Microsoft iniciar um failover para a região secundária, você terá acesso de leitura e gravação aos dados após o failover ter sido concluído. Para obter mais informações, confira [Guia de Recuperação de Desastres](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Se seu aplicativo precisar ler a partir da região secundária, ative o RA-GRS.