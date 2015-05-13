<properties
   pageTitle="Instalação de atualizações no modo de Manutenção"
   description="Explica como usar o Windows PowerShell para StorSimple para instalar instalações no modo de Manutenção."
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
   ms.date="04/21/2015"
   ms.author="v-sharos" />

### Para instalar atualizações do modo de Manutenção por meio do Windows PowerShell para StorSimple

1. Se você ainda não fez isso, acesse o console serial do dispositivo e selecione a opção 1, **Efetuar login com acesso total**. 

2. Digite a senha. A senha padrão é **Senha1**.

3. No prompt de comando, digite:

   **Get-HcsUpdateAvailability**
    
    You will be notified if updates are available and whether the updates are disruptive or non-disruptive.

4. Para aplicar atualizações sem interrupção, você precisa colocar o dispositivo no modo de Manutenção. Consulte [Para sair do Modo de Manutenção](#to-enter-maintenance-mode) para obter instruções.

5. Quando o dispositivo estiver em modo de Manutenção, no prompt de comando, digite:

    **Start-HcsUpdate**

6. Será solicitada a sua confirmação. Depois de confirmar as atualizações, elas serão instalados no controlador que está sendo atualmente acessado por você. Depois que as atualizações forem instaladas, o controlador será reiniciado.

7. Quando a reinicialização do primeiro controlador for concluída, conecte-se ao outro controlador e execute as etapas 1 a 6.

8. Depois que ambos os controladores estiverem atualizados, saia do modo de Manutenção. Consulte [Para sair do modo de manutenção]\] \(\#to-exit-maintenance-mode\) para obter instruções.

<!--HONumber=52-->
