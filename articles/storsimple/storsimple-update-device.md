<properties
   pageTitle="Atualizar o dispositivo StorSimple | Microsoft Azure"
   description="Explica como usar o recurso de atualização do StorSimple para instalar hotfixes e atualizações regulares e no modo de manutenção."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/28/2016"
   ms.author="v-sharos" />

# Atualizar seu dispositivo do StorSimple série 8000

## Visão geral

Os recursos de atualização do StorSimple permitem que você mantenha seu dispositivo StorSimple atualizado com facilidade. Dependendo do tipo de atualização, você poderá aplicar as atualizações ao dispositivo por meio do Portal Clássico do Azure ou por meio da interface do Windows PowerShell. Este tutorial descreve os tipos de atualização e como instalar cada um deles.

Você pode aplicar dois tipos de atualização de dispositivo:

- Atualizações regulares (ou modo Normal)
- Atualizações no modo de Manutenção

Você pode instalar atualizações regulares por meio do Portal Clássico do Azure ou do Windows PowerShell; no entanto, você deve usar o Windows PowerShell para instalar atualizações do modo de Manutenção.

Cada tipo de atualização será descrito separadamente a seguir.

### Atualizações regulares

As atualizações regulares são atualizações sem interrupções que podem ser instaladas quando o dispositivo estiver em modo Normal. Essas atualizações são aplicadas por meio do site Microsoft Update para cada controlador de dispositivo.

> [AZURE.IMPORTANT] Um failover de controlador pode ocorrer durante o processo de atualização. No entanto, isso não afetará a disponibilidade do sistema ou a operação.

- Para obter detalhes sobre como instalar atualizações regulares através do portal clássico do Azure, consulte [instalar atualizações regulares através do portal(#install-regular-updates-via-the-azure-classic-portal) clássico do Azure.

- Você também pode instalar atualizações regulares por meio do Windows PowerShell para StorSimple. Para obter detalhes, consulte [Instalar atualizações regulares por meio do Windows PowerShell para StorSimple](#install-regular-updates-via-windows-powershell-for-storsimple).

### Atualizações no modo de Manutenção

As atualizações do Modo de Manutenção são atualizações com interrupção, como atualizações de firmware de disco. Essas atualizações exigem que o dispositivo seja colocado em modo de Manutenção. Para obter detalhes, consulte [Etapa 2: Entrar no modo de Manutenção](#step2). Você não pode usar o Portal clássico do Azure para instalar atualizações do modo de Manutenção. Em vez disso, você deverá usar o Windows PowerShell para StorSimple.

Para obter detalhes sobre como instalar atualizações do modo de Manutenção, consulte [Instalar atualizações do modo de Manutenção instalar por meio do Windows PowerShell para StorSimple](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple).

> [AZURE.IMPORTANT] As atualizações do modo de Manutenção devem ser aplicadas separadamente para cada controlador.

## Instalar atualizações regulares por meio do Portal clássico do Azure

Você pode usar o Portal clássico do Azure para aplicar atualizações ao dispositivo StorSimple.

[AZURE.INCLUDE [storsimple-instalar-atualizações-manualmente](../../includes/storsimple-install-updates-manually.md)]

## Instalar atualizações regulares por meio do Windows PowerShell para StorSimple

Como alternativa, você pode usar o Windows PowerShell para StorSimple para aplicar atualizações regulares (modo Normal).

> [AZURE.IMPORTANT] Embora seja possível instalar atualizações regulares usando o Windows PowerShell para StorSimple, é altamente recomendável que você instale atualizações regulares pelo Portal clássico do Azure. Começando da Atualização 1, serão executadas verificações prévias antes da instalação de atualizações pelo portal. Essas verificações evitarão falhas e garantirão uma experiência mais uniforme.

[AZURE.INCLUDE [storsimple-instalar-atualizações-regulares-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## Instalar atualizações do modo de Manutenção por meio do Windows PowerShell para StorSimple

Você pode usar o Windows PowerShell para StorSimple para aplicar atualizações do modo de Manutenção ao dispositivo StorSimple. Todas as solicitações de E/S são pausadas neste modo. Serviços como NVRAM (memória de acesso aleatório não volátil) ou o serviço de cluster também são interrompidos. Ambos os controladores são reiniciados quando você entra ou sai desse modo. Quando você sair desse modo, todos os serviços serão retomados e deverão estar íntegros. (Isso pode levar alguns minutos).

Se você precisar aplicar atualizações do modo de Manutenção, receberá um alerta por meio do Portal clássico do Azure indicando que há atualizações que devem ser instaladas. Esse alerta incluirá instruções para o uso do Windows PowerShell para StorSimple para instalar as atualizações. Depois de atualizar o dispositivo, use o mesmo procedimento para alterar o dispositivo para o modo Normal. Para obter instruções passo a passo, consulte [Etapa 4: Sair do modo de Manutenção](#step4).

> [AZURE.IMPORTANT] 
> 
> - Antes de entrar no modo de Manutenção, verifique se ambos os controladores de dispositivo estão íntegros, verificando o **Status de Hardware** na página **Manutenção** no Portal clássico do Azure. Se o controlador não estiver íntegro, contate o Suporte da Microsoft para as próximas etapas. Para saber mais, acesse Contatar Suporte da Microsoft.
> - Quando você estiver no modo de Manutenção, precisará aplicar a atualização primeiro em um controlador e então no outro controlador.

### Etapa 1: Conectar-se ao console serial <a name="step1">

Primeiro, use um aplicativo como o PuTTY para acessar o console serial. O procedimento a seguir explica como usar o PuTTY para se conectar ao console serial.

[AZURE.INCLUDE [storsimple-usar-putty](../../includes/storsimple-use-putty.md)]

### Etapa 2: Entrar no modo de Manutenção <a name="step2">

Depois de se conectar ao console, determine se há atualizações a serem instaladas e entre no modo de manutenção para instalá-las.

[AZURE.INCLUDE [storsimple-entrar-modo de manutenção](../../includes/storsimple-enter-maintenance-mode.md)]

### Etapa 3: Instalar as atualizações <a name="step3">

Em seguida, instale as atualizações.

[AZURE.INCLUDE [storsimple-instalar-modo-de-manutenção-atualizações](../../includes/storsimple-install-maintenance-mode-updates.md)]
 
### Etapa 4: Sair do modo de Manutenção <a name="step4">

Por fim, saia do modo de Manutenção.

[AZURE.INCLUDE [storsimple-sair-modo-de-manutenção](../../includes/storsimple-exit-maintenance-mode.md)]

## Instale correções por meio do Windows PowerShell para StorSimple

Ao contrário de atualizações para o Microsoft Azure StorSimple, os hotfixes são instalados de uma pasta compartilhada. Assim como acontece com as atualizações, há dois tipos de hotfixes:

- Hotfixes regulares
- Hotfixes no modo de Manutenção

Os procedimentos a seguir explicam como usar o Windows PowerShell para StorSimple para instalar hotfixes regulares e no modo de Manutenção.

[AZURE.INCLUDE [storsimple-instalar-hotfixes-regulares](../../includes/storsimple-install-regular-hotfixes.md)]

[AZURE.INCLUDE [storsimple-instalar-hotfixes-modo-de-manutenção](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## O que acontece com as atualizações se você executar uma redefinição de fábrica do dispositivo?

Se um dispositivo for redefinido para as configurações de fábrica, então todas as atualizações serão perdidas. Depois que o dispositivo com a redefinição de fábrica for registrado e configurado, você precisará instalar manualmente as atualizações por meio do Portal clássico do Azure e/ou do Windows PowerShell para StorSimple. Para saber mais sobre as redefinições de fábrica, consulte [Redefinir o dispositivo para as configurações padrão de fábrica](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings).

## Próximas etapas

- Saiba mais sobre [como usar o Windows PowerShell para StorSimple a fim de administrar seu dispositivo StorSimple](storsimple-windows-powershell-administration.md).
- Saiba mais sobre o [uso do serviço StorSimple Manager para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=AcomDC_0629_2016-->