---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: cb160a140b5c0cb184a5172da10ade0de37c4fed
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50164266"
---
<!--v-sharos 10/13/2105 virtual device security-->

Tenha as seguintes considerações de segurança em mente ao usar o dispositivo virtual StorSimple:

* O dispositivo virtual é protegido por meio de sua assinatura do Microsoft Azure. Isso significa que, se você estiver usando o dispositivo virtual e se sua assinatura do Azure estiver comprometida, os dados armazenados em seu dispositivo virtual também estarão suscetíveis.
* A chave pública do certificado usado para criptografar os dados armazenados no Azure StorSimple foi disponibilizada com segurança para o portal clássico do Azure e a chave privada é mantida com o dispositivo StorSimple. No dispositivo virtual StorSimple, as chaves públicas e privadas são armazenadas no Azure.
* O dispositivo virtual é hospedado no datacenter do Microsoft Azure.

