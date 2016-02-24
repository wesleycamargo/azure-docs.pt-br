<properties 
   pageTitle="Aplicar atualizações de StorSimple Virtual Array | Microsoft Azure"
   description="Descreve como usar a interface do usuário da Web de StorSimple Virtual Array para aplicar atualizações e hotfixes"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="01/13/2016"
   ms.author="alkohli" />

# Aplicar atualizações e hotfixes para a StorSimple Virtual Array (visualização)

## Visão geral

Talvez seja necessário aplicar atualizações de software ou hotfixes para manter sua StorSimple Virtual Array atualizada. Em geral, é recomendável que você instale atualizações por meio do portal clássico do Azure. No entanto, em casos em que o portal não está disponível, você pode usar a interface do usuário da Web local para aplicar hotfixes ou atualizações. Este tutorial descreve como usar a interface do usuário da Web local para aplicar uma atualização ou hotfix. (Para obter procedimentos durante a instalação de atualizações por meio do portal clássico, vá para [Instalar atualizações regulares por meio do portal clássico do Azure](storsimple-update-device.md#install-regular-updates-via-the-azure-classic-portal).)

Tenha em mente que uma instalação de atualização ou hotfix pode reiniciar seu dispositivo. Visto que a StorSimple Virtual Array é um dispositivo de nó único, quaisquer E/Ss até então em andamento serão interrompidas e o dispositivo passará por algum tempo de inatividade.

## Usar a interface do usuário da Web local para aplicar uma atualização ou hotfix

Antes da instalação da atualização ou hotfix, certifique-se de que você tem a atualização ou hotfix baixado localmente em seu host ou acessível por meio de um compartilhamento de rede.

#### Para instalar a atualização ou hotfix

1. Na interface do usuário da Web local, vá para **Manutenção** > **Atualização de Software**.

2. Em **Caminho de arquivo de atualização**, digite o nome de arquivo para a atualização ou hotfix. Você também pode navegar até o arquivo de instalação de hotfix ou atualização se colocado em um compartilhamento de rede. A atualização será iniciada. Depois de concluído, você será notificado.

    ![atualizar dispositivo](./media/storsimple-ova-update/image43.png)

## Próximas etapas

Saiba mais sobre a [administração de sua StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0121_2016-->