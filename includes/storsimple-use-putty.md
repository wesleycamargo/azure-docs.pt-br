<properties 
   pageTitle="Usar o PuTTY para se conectar ao console serial do dispositivo"
   description="Explica como usar o software de emulação de terminal PuTTY para se conectar ao dispositivo StorSimple."
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
   ms.date="04/01/2015"
   ms.author="v-sharos" />

### Para conectar-se por meio do console serial

1. Conecte o cabo serial ao dispositivo (diretamente ou por meio de um adaptador serial USB).

2. Abra o **Painel de Controle** e, em seguida, abra o **Gerenciador de Dispositivos**.

3. Identifique a porta COM como mostrado na ilustração a seguir.

     ![Conectando por meio do console serial](./media/storsimple-use-putty/HCS_ConnectingDeviceS-include.png)

4. Inicie o PuTTY.

5. No painel à direita, altere o **Tipo de conexão** para **Serial**.

6. No painel à direita, digite a porta COM apropriada. Verifique se os parâmetros da configuração serial foram definidos da seguinte maneira:
  - Velocidade: 115.200
  - Bits de dados: 8
  - Bits de parada: 1
  - Paridade: nenhuma
  - Controle de fluxo: nenhum

    Essas configurações são mostradas na ilustração a seguir.

     ![Configurações do PuTTY](./media/storsimple-use-putty/HCS_ConnectingViaPutty-include.png)

    > [AZURE.NOTE]Se a configuração de controle de fluxo padrão não funcionar, tente definir o controle de fluxo como XON/XOFF.

7. Clique em **Abrir** para iniciar uma sessão serial.
 
<!--HONumber=52-->
