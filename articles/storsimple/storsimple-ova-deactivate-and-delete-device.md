<properties 
   pageTitle="Desativar e excluir uma Matriz Virtual StorSimple | Microsoft Azure"
   description="Descreve como remover o dispositivo StorSimple do serviço primeiro desativando-o e então excluindo-o."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/08/2016"
   ms.author="alkohli" />

# Desativar e excluir uma Matriz Virtual StorSimple

## Visão geral

Quando você desativar uma Matriz Virtual StorSimple, você dividirá a conexão entre o dispositivo e o serviço StorSimple Manager correspondente. A desativação é uma operação PERMANENTE e não pode ser desfeita. Um dispositivo desativado não pode ser registrado com o serviço StorSimple Manager novamente.

Talvez seja necessário desativar e excluir um dispositivo virtual StorSimple nos seguintes cenários:


- Seu dispositivo está online e você planeja efetuar failover neste dispositivo. Talvez seja necessário fazer isso se você estiver planejando atualizar para um dispositivo maior. Depois que os dados do dispositivo forem transferidos e o failover estiver concluído, você poderá excluir o dispositivo.

- Seu dispositivo está offline e você planeja efetuar failover neste dispositivo. Isso pode acontecer em caso de desastre em que, devido a uma interrupção no datacenter, o dispositivo primário está inativo. Você planeja fazer failover de dispositivo para um dispositivo secundário. Depois que os dados do dispositivo forem transferidos e o failover estiver concluído, você poderá excluir o dispositivo.

- Deseja encerrar o dispositivo e, em seguida, excluí-lo.
 

Quando você desativa um dispositivo, todos os dados que foram armazenados localmente não estarão mais acessíveis. Somente os dados armazenados na nuvem podem ser recuperados. Se você planeja manter os dados do dispositivo após a desativação, você deve tirar um instantâneo de nuvem de todos os dados antes de desativar um dispositivo. Isso permitirá que você recupere todos os dados em um estágio posterior.


Este tutorial explica como:

- Desativar um dispositivo 
- Excluir um dispositivo desativado


## Desativar um dispositivo

Execute as seguintes etapas para atualizar o dispositivo.

#### Para desativar o dispositivo   

1. Vá para a página **Dispositivos**. Selecione o dispositivo que você deseja desativar.

	![Selecione o dispositivo para desativar](./media/storsimple-ova-deactivate-and-delete-device/deactivate1m.png)

3. Na parte inferior da página, clique em **Desativar**.

	![Clique em desativar](./media/storsimple-ova-deactivate-and-delete-device/deactivate2m.png)

4. Será exibida uma mensagem de confirmação. Clique em **Sim** para continuar.

	![Confirme desativar](./media/storsimple-ova-deactivate-and-delete-device/deactivate3m.png)

	O processo de desativação começará e levará alguns minutos para ser concluído.

	![Desativação em andamento](./media/storsimple-ova-deactivate-and-delete-device/deactivate4m.png)

3. Após a desativação, a lista de dispositivos será atualizada.

	![Desativação concluída](./media/storsimple-ova-deactivate-and-delete-device/deactivate5m.png)

	Agora você pode excluir este dispositivo.

## Excluir o dispositivo

Um dispositivo deve primeiro ser desativado para excluí-lo. Excluir um dispositivo o remove da lista de dispositivos conectados ao serviço. O serviço não poderá mais gerenciar o dispositivo excluído. No entanto, os dados associados ao dispositivo permanecerão na nuvem. Lembre-se de que esses dados acumularão cobranças.

Conclua as etapas a seguir para excluir o dispositivo:

#### Para excluir o dispositivo 

 1. Na página **Dispositivos** do serviço do StorSimple Manager, selecione um dispositivo desativado que você deseja excluir.

	![Selecione o dispositivo para excluir](./media/storsimple-ova-deactivate-and-delete-device/deactivate5m.png)

 2. Na parte inferior da página, clique em **Excluir**.
 
	![Clique em Excluir](./media/storsimple-ova-deactivate-and-delete-device/deactivate6m.png)

 3. Será solicitada a sua confirmação. Digite o nome do dispositivo para confirmar a exclusão do dispositivo. Observe que a exclusão do dispositivo não excluirá os dados de nuvem associados ao dispositivo. Clique no ícone de seleção para continuar.
 
	![Confirmar exclusão](./media/storsimple-ova-deactivate-and-delete-device/deactivate7m.png)

 5. Pode levar alguns minutos para o serviço ser excluído.

	![Exclusão em andamento](./media/storsimple-ova-deactivate-and-delete-device/deactivate8m.png)

 	Depois que o dispositivo for excluído, a lista de dispositivos será atualizada.

	![Exclusão concluída](./media/storsimple-ova-deactivate-and-delete-device/deactivate9m.png)


## Próximas etapas

- Para saber como usar o serviço StorSimple Manager, acesse [Usar o serviço StorSimple Manager para administrar a sua Matriz Virtual StorSimple](storsimple-ova-manager-service-administration.md). 

<!---HONumber=AcomDC_0218_2016-->