---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 36a014fbff30c32a7149a83907c9586bc3b2f01a
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50166271"
---
<!--author=SharS last changed: 11/18/16-->

#### <a name="to-install-regular-updates-via-windows-powershell-for-storsimple"></a>Para instalar atualizações regulares através do Windows PowerShell para StorSimple
1. Abra o console serial do dispositivo e selecione a opção 1, **Efetuar login com acesso total**. Digite a senha. A senha padrão é *Senha1*. 
2. No prompt de comando, digite:
   
     `Get-HcsUpdateAvailability`
   
    Você será notificado se houver atualizações disponíveis e se as atualizações são interruptivas ou não interruptivas.
3. No prompt de comando, digite:
   
     `Start-HcsUpdate`
   
    O processo de atualização será iniciado.

> [!IMPORTANT]
> * Este comando se aplica somente a hotfixes regulares. Executar esse comando em apenas um controlador, mas ambos os controladores serão atualizados. 
> * Você pode perceber um failover de controlador durante o processo de atualização; no entanto, o failover não afetará a disponibilidade ou operação do sistema.
> 
> 

