<properties 
   pageTitle="Opção 2: usar o Portal de Gerenciamento do Azure para aplicar a Atualização 1.2"
   description="Explica como usar o Portal de Gerenciamento para instalar a Atualização 1.2 do StorSimple Série 8000."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="09/08/2015"
   ms.author="v-sharos" />

#### Para instalar a Atualização 1.2 por meio do Portal de Gerenciamento do Azure

1. No Portal de Gerenciamento, vá até a página **Dispositivos** e selecione seu dispositivo.
 
2. Navegue até **Dispositivos** > **Configurar**.

3. Em **Interfaces de Rede**, verifique primeiro se você tem pelo menos uma interface de rede que esteja habilitada para iSCSI. Depois localize a interface de rede (diferente de DATA 0) que tem um gateway atribuído.

4. Desabilite a interface de rede que contém um gateway atribuído e salve a configuração modificada. Lembre-se de que as configurações de interface de rede são mantidas e, portanto, quando você reabilitar essa interface de rede mais tarde, o portal será revertido para as configurações originais.

7. Agora você pode [usar o Portal de Gerenciamento para instalar a Atualização 1.2](#use-the-management-portal-to-install-update-1). Siga as instruções a partir da etapa 3 deste procedimento. Depois de instalar todas as atualizações, você pode reabilitar a interface de rede que você desabilitou.

<!---HONumber=Sept15_HO2-->