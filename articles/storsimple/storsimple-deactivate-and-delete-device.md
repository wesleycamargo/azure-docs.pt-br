<properties 
   pageTitle="Desativar e excluir um dispositivo StorSimple | Microsoft Azure"
   description="Descreve como remover o dispositivo StorSimple do serviço, desativá-lo e depois excluí-lo."
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/09/2015"
   ms.author="v-sharos" />

# Desativar e excluir um dispositivo StorSimple

Este tutorial explica como remover um dispositivo StorSimple do serviço desativando-o e depois excluindo-o.

>[AZURE.NOTE]É necessário desativar um dispositivo antes de excluí-lo.

## Desativar um dispositivo

Você poderá deixar um dispositivo fora de serviço. Nesse caso, o dispositivo precisará ser desativado. A desativação rompe a conexão entre o dispositivo e o serviço StorSimple Manager correspondente.

>[AZURE.WARNING]A desativação é uma operação PERMANENTE e não pode ser desfeita. Um dispositivo desativado não pode ser registrado com o serviço StorSimple Manager, a menos que ele seja primeiramente redefinido com as configurações de fábrica.

Quando você desativa um dispositivo, todos os dados que foram armazenados localmente no dispositivo não estarão acessíveis. Somente os dados associados ao dispositivo que foi armazenado na nuvem podem ser recuperados. Depois de desativado, um dispositivo precisa ser redefinido para poder ser reutilizado com um serviço de fábrica novo ou existente. O processo de redefinição de fábrica exclui todos os dados que foram armazenados localmente no seu dispositivo. Por isso, é essencial fazer um instantâneo de nuvem de todos os dados antes de desativar um dispositivo. Isso permitirá que você recupere todos os dados em um estágio posterior.

Para um dispositivo virtual StorSimple, a desativação exclui a máquina virtual e os recursos criados quando ela foi provisionada. Depois que o dispositivo virtual for desativado, ele não poderá ser restaurado ao estado anterior. Antes de desativar um dispositivo virtual StorSimple, certifique-se de interromper ou excluir clientes e hosts que dependem desse dispositivo virtual.

### Desativar e excluir dados

Se você estiver interessado em excluir o dispositivo completamente e não quiser reter os dados do dispositivo, faça o seguinte:

1. Antes de desativar um dispositivo, é necessário excluir todos os contêineres de volumes (e os próprios volumes) associados ao dispositivo. Somente é possível excluir contêineres de volume depois de excluir os backups associados.

2. Desative o dispositivo. Vá para [Etapas para desativar](#steps-to-deactivate) para obter instruções.

3. Após a desativação, você pode excluir o dispositivo completamente. Acesse [Excluir um dispositivo](#delete-a-device) para obter instruções.

### Desativar e reter dados

Se você desejar excluir o dispositivo, mas quiser manter os dados do dispositivo, faça o seguinte:

1. Desative o dispositivo. Todos os contêineres de volume e os instantâneos do dispositivo serão mantidos. Vá para [Etapas para desativar](#steps-to-deactivate) para obter instruções.

2. Agora é possível realizar failover dos contêineres de volume e dos instantâneos associados. Para procedimentos, vá para [Failover e recuperação de desastres para seu dispositivo StorSimple](storsimple-device-failover-disaster-recovery.md)

3. Após a desativação e failover, você poderá excluir o dispositivo completamente. Acesse [Excluir um dispositivo](#delete-a-device) para obter instruções.

### Etapas para desativar

Use o procedimento a seguir para desativar um dispositivo para prepará-lo para excluí-lo.

#### Para desativar um dispositivo

1. Na página **Dispositivos** do serviço do StorSimple Manager, selecione o dispositivo que você deseja desativar e clique em **Desativar** na parte inferior da página.

2. Será exibida uma mensagem de confirmação. Clique em **Sim** para continuar. O processo de desativação começará e levará alguns minutos para ser concluído.

    Em um dispositivo virtual StorSimple, a desativação resulta nas seguintes ações:

      - O dispositivo virtual StorSimple é removido.

      - O OSDisk e Discos de Dados criados para o dispositivo virtual do StorSimple são removidos.

      - O serviço hospedado e a rede virtual criados durante o provisionamento são mantidos. Se você não estiver usando as entidades, deverá excluí-las manualmente.

      - Instantâneos de nuvem criados pelo dispositivo virtual StorSimple são mantidos.

<!--After the device is deactivated, you will need to perform a failover before you can delete it completely. For failover instructions, go to [Failover and disaster recovery for your StorSimple device](storsimple-device-failover-disaster-recovery.md).-->
## Excluir um dispositivo

Você pode excluir apenas os dispositivos que foram desativados. Excluir um dispositivo o remove da lista de dispositivos conectados ao serviço. O serviço não poderá mais gerenciar o dispositivo excluído.

#### Para excluir um dispositivo

1. Na página **Dispositivos** do serviço do StorSimple Manager, selecione um dispositivo desativado que você deseja excluir.

2. Na parte inferior da página, clique em **Excluir**.

3. Será solicitada a sua confirmação. Clique em **Sim** para continuar.

Pode levar alguns minutos para o serviço ser excluído.

## Próximas etapas
Para restaurar os dispositivos desativados para os padrões de fábrica, acesse [Redefinir o dispositivo para as configurações padrão de fábrica](https://msdn.microsoft.com/library/dn772373.aspx).

Para obter assistência técnica, [contate o Suporte da Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx).

<!---HONumber=July15_HO4-->