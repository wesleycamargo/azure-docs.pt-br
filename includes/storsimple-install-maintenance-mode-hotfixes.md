<properties
   pageTitle="Instalação de hotfixes no modo de Manutenção"
   description="Explica como usar o Windows PowerShell para StorSimple para instalar hotfixes do modo de Manutenção."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="tysonn" /> <tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/23/2015"
   ms.author="v-sharos" />

### Para instalar os hotfixes do modo de manutenção por meio do Windows PowerShell para StorSimple

> [AZURE.IMPORTANT]Quando você estiver no modo de Manutenção, precisará aplicar a atualização primeiro em um controlador e, em seguida, no outro controlador.

1. Coloque o dispositivo no modo de Manutenção. Consulte os modos do dispositivo de Alteração para obter instruções sobre como entrar no modo de Manutenção.

2. Para aplicar o hotfix, digite:

     **Start-HcsHotfix**

3. Quando solicitado, forneça o caminho para a pasta compartilhada que contém os arquivos de hotfix.

4. Será solicitada a sua confirmação. Digite **Y** para prosseguir com a instalação do hotfix.

5. Após aplicar o hotfix em um controlador, faça logon no outro controlador. Aplique o hotfix da mesma forma que você fez para o controlador anterior.

6. Após a aplicação dos hotfixes, saia do modo de Manutenção. Consulte [Para sair do modo de Manutenção](#to-exit-maintenance-mode) para obter instruções.

<!--HONumber=52-->
