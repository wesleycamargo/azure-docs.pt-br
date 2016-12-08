---
title: "Instalar a Atualização 2 no dispositivo StorSimple | Microsoft Docs"
description: "Explica como instalar a Atualização 2 do StorSimple série 8000 em seu dispositivo StorSimple série 8000."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 8c8981df-75d9-4d19-b137-d6c6ba39dcfb
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/21/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e788439608b7122f2bca6b99b832baa5258e472d


---
# <a name="install-update-2-on-your-storsimple-device"></a>Instalar a Atualização 2 no dispositivo StorSimple
## <a name="overview"></a>Visão geral
Este tutorial explica como instalar a Atualização 2 em um dispositivo StorSimple que está executando uma versão de software anterior por meio do portal clássico do Azure. O tutorial também aborda as etapas necessárias para a atualização quando um gateway é configurado em uma interface de rede que não seja DATA 0 do dispositivo StorSimple e você está tentando atualizar de uma versão de software anterior à Atualização 1.

A Atualização 2 inclui atualizações de software do dispositivo, atualizações do driver LSI e atualizações de firmware de disco. As atualizações de software do dispositivo e do LSI são atualizações sem interrupções e podem ser aplicadas por meio do portal clássico do Azure. As atualizações de firmware de disco são as atualizações de interrupção e só podem ser aplicadas por meio da interface do Windows PowerShell do dispositivo.

> [!IMPORTANT]
> * Talvez você não veja a Atualização 2 imediatamente porque fazemos uma distribuição das atualizações dividida em fases. Procure novamente as atualizações em poucos dias, uma vez que elas serão disponibilizadas em breve.
> * Um conjunto de verificações prévias manuais e automáticas para são realizadas antes da instalação para determinar a integridade do dispositivo em termos de conectividade de rede e estado do hardware. Essas pré-verificações são executadas somente se você aplicar as atualizações no portal clássico do Azure.
> * Recomendamos que você instale as atualizações de software e driver através do Portal clássico do Azure. Você só deve ir para a interface do Windows PowerShell do dispositivo (para instalar atualizações) se a verificação de pré-atualização de gateway falhar no portal. As atualizações podem levar de 4 a 7 horas para instalar (incluindo as Atualizações do Windows). As atualizações do modo de manutenção devem ser instaladas por meio da interface do Windows PowerShell do dispositivo. Como as atualizações do modo de manutenção são atualizações com interrupção, elas resultarão em um tempo de inatividade para seu dispositivo.
> * Se estiver executando o StorSimple Snapshot Manager opcional, certifique-se de ter atualizado sua versão Snapshot Manager para a Atualização 2 antes de atualizar o dispositivo.
> 
> 

[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-2-via-the-azure-classic-portal"></a>Instalar a Atualização 2 por meio do portal clássico do Azure
Realize as etapas a seguir para atualizar seu dispositivo para a [Atualização 2](storsimple-update2-release-notes.md).

> [!NOTE]
> A Atualização 2 permite que a Microsoft efetue pull de informações adicionais de diagnóstico do dispositivo. Como resultado, quando nossa equipe de operações identifica dispositivos com problemas, estamos mais bem equipados para coletar informações do dispositivo e diagnosticar problemas. Ao aceitar a Atualização 2, você nos permite a dar esse suporte proativo.
> 
> 

[!INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

1. Verifique se o dispositivo está executando a **Atualização 2 do StorSimple Série 8000 (6.3.9600.17673)**. A **Data da última atualização** também deve ser modificada. Você também verá que as atualizações do modo de Manutenção estão disponíveis (essa mensagem poderá continuar sendo exibida por até 24 horas após a instalação das atualizações).
   
   As atualizações do modo de manutenção são atualizações interrompidas que resultam em tempo de inatividade do dispositivo e podem ser aplicadas apenas por meio da interface do Windows PowerShell de seu dispositivo. Em alguns casos quando a Atualização 1.2 estiver sendo executada, o firmware de disco poderá já estar atualizado; nesse caso, não é necessário instalar quaisquer atualizações do modo de manutenção.
2. Baixe as atualizações do modo de manutenção usando as etapas listadas em [Para baixar hotfixes](#to-download-hotfixes) a fim pesquisar e baixar a KB3121899, que instala atualizações de firmware de disco (as outras atualizações já devem estar instaladas agora).
3. Siga as etapas listadas em [instalar e verificar hotfixes do modo de manutenção](#to-install-and-verify-maintenance-mode-hotfixes) para instalar as atualizações do modo de manutenção.

## <a name="install-update-2-as-a-hotfix"></a>Instalar a Atualização 2 como um hotfix
Use este procedimento se a verificação de gateway falhar ao tentar instalar as atualizações por meio do portal clássico do Azure. A verificação falha pois você tem um gateway atribuído a uma interface de rede diferente de DATA 0 e o dispositivo está executando uma versão de software anterior à Atualização 1.

As versões de software que podem ser atualizadas usando o método de hotfix são Atualização 0.1, Atualização 0.2, Atualização 0.3, Atualização 1, Atualização 1.1 e Atualização 1.2. O método de hotfix envolve as três etapas a seguir:

* Baixe os hotfixes do Catálogo do Microsoft Update.
* Instale e verifique os hotfixes do modo normal.
* Instale e verifique o hotfix do modo de manutenção.

Para instalar a Atualização 2 como um hotfix, é necessário baixar e instalar os seguintes hotfixes:

| Classificar | KB | Descrição | Tipo de atualização |
| --- | --- | --- | --- |
| 1 |KB3121901 |Atualização de software |Regular |
| 2 |KB3121900 |Driver LSI |Regular |
| 3 |KB3080728 |Correção do Storport  </br>  Windows Server 2012 R2 |Regular |
| 4 |KB3090322 |Correção do Spaceport  </br>  Windows Server 2012 R2 |Regular |
| 5 |KB3121899 |Firmware de disco |Manutenção |

> [!IMPORTANT]
> * Se o dispositivo estiver executando a versão de lançamento (GA), contate o [Suporte da Microsoft](storsimple-contact-microsoft-support.md) para ajudar na instalação dessa atualização.
> * Esse procedimento deve ser executado apenas uma vez para aplicar a Atualização 2. É possível usar o portal clássico do Azure para aplicar atualizações subsequentes.
> * Cada instalação de hotfix pode levar cerca de 20 minutos para concluir. O tempo total de instalação é de quase 2 horas.
> * Antes de usar este procedimento para aplicar a atualização, certifique-se de que ambos os controladores de dispositivo estão online e todos os componentes de hardware estão íntegros.
> 
> 

Realize as etapas a seguir para aplicar essa atualização como um hotfix.

[!INCLUDE [storsimple-install-update2-hotfix](../../includes/storsimple-install-update2-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre a [versão da Atualização 2](storsimple-update2-release-notes.md).




<!--HONumber=Nov16_HO3-->


