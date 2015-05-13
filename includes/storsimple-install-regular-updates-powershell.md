<properties
   pageTitle="Instalação de atualizações regulares por meio do Windows PowerShell para StorSimple"
   description="Explica como usar o recurso de atualização do StorSimple e o Windows PowerShell para StorSimple para instalar atualizações regulares."
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
   ms.date="04/27/2015"
   ms.author="v-sharos" />

### Para instalar atualizações regulares através do Windows PowerShell para StorSimple

1. Abra o console serial do dispositivo e selecione a opção 1, **Efetuar login com acesso total**. Digite a senha. A senha padrão é *Senha1*. 

2. No prompt de comando, digite:

    **Get-HcsUpdateAvailability**

    Você será notificado se houver atualizações disponíveis e se as atualizações são interruptivas ou não interruptivas.

3. No prompt de comando, digite:

    **Start-HcsUpdate**

    O processo de atualização com início.

> [AZURE.IMPORTANT]
>
> - Este comando se aplica somente a hotfixes regulares. Executar esse comando em apenas um controlador, mas ambos os controladores serão atualizados. 
> - Você pode perceber um failover de controlador durante o processo de atualização; no entanto, o failover não afetará a disponibilidade ou operação do sistema.

<!--HONumber=52-->
