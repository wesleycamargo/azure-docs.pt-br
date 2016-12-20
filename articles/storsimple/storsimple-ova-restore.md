---
title: Restaurar de um backup de sua Matriz Virtual StorSimple
description: Saiba mais sobre como restaurar um backup de sua StorSimple Virtual Array.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 4d0deb8c-e3c7-4bc4-b89d-9881041960cb
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/07/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2801bc0fc645f9ed23733d1025f1cb42a5174022


---
# <a name="restore-from-a-backup-of-your-storsimple-virtual-array"></a>Restaurar de um backup de sua Matriz Virtual StorSimple
## <a name="overview"></a>Visão geral
Este artigo se aplica ao Microsoft Azure StorSimple Virtual Array (também conhecido como o dispositivo virtual local StorSimple ou dispositivo virtual StorSimple) que executa a versão GA (disponibilidade geral) de março de 2016 ou posterior. Este artigo descreve passo a passo como restaurar um conjunto de backup de seus volumes ou compartilhamentos para seu StorSimple Virtual Array. O artigo também fornece detalhes sobre como a recuperação em nível de item funciona em sua StorSimple Virtual Array que está configurada como um servidor de arquivos.

## <a name="restore-shares-from-a-backup-set"></a>Restaurar compartilhamentos de um conjunto de backup
**Antes de tentar restaurar os compartilhamentos, certifique-se de que você tem espaço suficiente no dispositivo para concluir esta operação.** Para restaurar de um backup, execute as etapas a seguir no [portal clássico do Azure](https://manage.windowsazure.com/).

#### <a name="to-restore-a-share"></a>Para restaurar um compartilhamento
1. Navegue até o **Catálogo de Backup**. Filtrar por dispositivo apropriado e intervalo de tempo para pesquisar seus backups. Clique no ícone de seleção ![](./media/storsimple-ova-restore/image1.png) para executar a consulta.
2. Na lista de conjuntos de backup exibido, clique e selecione um backup específico. Expanda o backup para ver os diversos compartilhamentos sob ele. Clique e selecione um compartilhamento que você deseja restaurar.
3. Na parte inferior da página, clique em **Restaurar como novo**.
4. Isso iniciará o assistente **Restaurar como novo compartilhamento** . Na página **Especifique o nome e a localização** :
   
   1. Verifique o nome do dispositivo de origem. Ele deve ser o dispositivo que contém o compartilhamento que você deseja restaurar. A seleção do dispositivo está esmaecida. Para selecionar um dispositivo de origem diferente, você precisará sair do assistente e selecionar novamente o conjunto de backup mais uma vez.
   2. Forneça um nome do compartilhamento. O nome do compartilhamento deve conter entre 3 e 127 caracteres.
   3. Examine o tamanho, tipo e permissões associados ao compartilhamento que você está tentando restaurar. Você poderá modificar as propriedades de compartilhamento por meio do Windows Explorer após a restauração ser concluída.
   4. Clique no ícone de verificação ![](./media/storsimple-ova-restore/image1.png).
      
      ![](./media/storsimple-ova-restore/image9.png)
5. Depois que o trabalho de restauração for concluído, a restauração será iniciada e você verá outra notificação. Para monitorar o progresso da restauração, clique em **Exibir trabalho**. Isso o levará até a página **Trabalhos** .
6. Você pode acompanhar o andamento do trabalho de restauração. Quando a restauração estiver 100% concluída, navegue de volta à página **Compartilhamentos** em seu dispositivo.
7. Agora você pode exibir o novo compartilhamento restaurado na lista de compartilhamentos em seu dispositivo. Observe que a restauração é feita para o mesmo tipo do compartilhamento. Um compartilhamento em camadas é restaurado como em camadas, e um compartilhamento fixado localmente é restaurado como um compartilhamento fixado localmente.

Agora você concluiu a configuração do dispositivo e sabe como fazer backup ou restaurar um compartilhamento. 

## <a name="restore-volumes-from-a-backup-set"></a>Restaurar volumes de um conjunto de backup
Para restaurar de um backup, execute as etapas a seguir no portal clássico do Azure. A operação de restauração restaura o backup para um novo volume no mesmo dispositivo virtual. Você não pode restaurar para um dispositivo diferente.

#### <a name="to-restore-a-volume"></a>Para restaurar um volume
1. Navegue até o **Catálogo de Backup**. Filtrar por dispositivo apropriado e intervalo de tempo para pesquisar seus backups. Clique no ícone de seleção ![](./media/storsimple-ova-restore/image1.png) para executar a consulta.
2. Na lista de conjuntos de backup exibido, clique e selecione um backup específico. Expanda o backup para ver os diversos volumes sob ele. Selecione o volume que você deseja restaurar. 
3. Na parte inferior da página, clique em **Restaurar como novo**. O assistente **Restaurar como novo volume** será iniciado.
4. Na página **Especifique o nome e a localização** :
   
   1. Verifique o nome do dispositivo de origem. Esse deve ser o dispositivo que contém o volume que você deseja restaurar. A seleção do dispositivo está indisponível. Para selecionar um dispositivo de origem diferente, você precisará sair do assistente e selecionar novamente o conjunto de backup mais uma vez.
   2. Forneça um nome de volume para o volume que está sendo restaurado como novo. O nome do volume deve conter entre 3 e 127 caracteres.
   3. Clique no ícone de seta.
      
      ![](./media/storsimple-ova-restore/image12.png)
5. Na página **Especificar hosts que podem usar este volume** , selecione os ACRs apropriados na lista suspensa.
   
   ![](./media/storsimple-ova-restore/image13.png)
6. Clique no ícone de verificação ![](./media/storsimple-ova-restore/image1.png). Isso iniciará um trabalho de restauração e você verá a notificação a seguir, de que o trabalho está em andamento.
7. Depois que o trabalho de restauração for concluído, a restauração será iniciada e você verá outra notificação. Para monitorar o progresso da restauração, clique em **Exibir trabalho**. Isso o levará até a página **Trabalhos** .
8. Você pode acompanhar o andamento do trabalho de restauração. Navegue de volta à página **Volumes** em seu dispositivo.
9. Agora você pode exibir o novo volume restaurado na lista de volume em seu dispositivo. Observe que a restauração é feita para o mesmo tipo de volume. Um volume em camadas é restaurado como em camadas, e um volume fixado localmente é restaurado como um volume fixado localmente.
10. Quando o volume for exibido online na lista de volumes, o volume estará disponível para uso.  No host do iniciador iSCSI, atualize a lista de destinos na janela de propriedades do iniciador iSCSI.  Um novo destino que contém o nome do volume restaurado deve aparecer como 'inativo' na coluna status.
11. Selecione o destino e clique em **Conectar**.   Após o iniciador ser conectado ao destino, o status deverá mudar para **Conectado**. 
12. Na janela **Gerenciamento de Disco** , os volumes montados serão exibidos conforme exibido na ilustração a seguir. Clique com o botão direito no volume descoberto (clique no nome do disco) e depois clique em **Online**.

> [!IMPORTANT]
> Ao tentar restaurar um volume ou um compartilhamento de um conjunto de backup, se o trabalho de restauração falhar, um volume de destino ou compartilhamento ainda poderá ser criado no portal. É importante excluir este volume de destino ou compartilhá-lo no portal para minimizar quaisquer problemas futuros causado por esse elemento.
> 
> 

## <a name="item-level-recovery-ilr"></a>ILR (recuperação em nível de item)
Esta versão apresenta a recuperação em nível de item (ILR) em um dispositivo virtual StorSimple configurado como um servidor de arquivos. O recurso permite fazer uma recuperação granular de arquivos e pastas de um backup de nuvem de todos os compartilhamentos no dispositivo StorSimple. Os usuários podem recuperar arquivos excluídos de backups recentes usando um modelo de autoatendimento.

Cada compartilhamento tem uma pasta *.backups* que contém os backups mais recentes. O usuário pode navegar até o backup desejado, copiar arquivos e pastas relevantes do backup e restaurá-los. Isso elimina chamadas aos administradores para restaurar arquivos de backups.

1. Ao executar a ILR, você pode exibir os backups por meio do Windows Explorer. Clique no compartilhamento específico em busca do qual você deseja pesquisar o backup. Você verá uma pasta *.backups* criada no compartilhamento que armazena todos os backups. Expanda a pasta *.backups* para exibir os backups. A pasta mostrará então a exibição detalhada de toda a hierarquia de backup. Este modo de exibição é criado sob demanda e sua criação geralmente leva apenas alguns segundos.
   
   Os últimos 5 backups são exibidos dessa forma e podem ser usados para realizar uma recuperação em nível de item. Os 5 backups recentes incluem tanto os backups agendados padrão quanto os manuais.

    -   **Backups agendados** nomeados como &lt;Device name&gt;DailySchedule-YYYYMMDD-HHMMSS-UTC.

    -   **Backups manuais** nomeados como Ad-hoc-YYYYMMDD-HHMMSS-UTC.

        ![](./media/storsimple-ova-restore/image14.png)

1. Identifique o backup que contém a versão mais recente do arquivo excluído. Embora o nome da pasta contenha um carimbo de data/hora UTC em cada um dos casos acima, a hora em que a pasta foi criada é a hora efetiva do dispositivo no momento em que o backup foi iniciado. Use o carimbo de data/hora da pasta para localizar e identificar os backups.
2. Localize a pasta ou o arquivo que você deseja restaurar no backup que você identificou na etapa anterior. Observação que você pode exibir somente os arquivos ou pastas para os quais você tem permissões. Se você não for capaz de acessar determinados arquivos ou pastas, precisará entrar em contato com um administrador de compartilhamento que pode usar o Windows Explorer para editar as permissões de compartilhamento e fornecer a você acesso a determinado arquivo ou pasta. É uma melhor prática recomendada que o administrador do compartilhamento seja um grupo de usuários em vez de um único usuário.
3. Copie o arquivo ou pasta no compartilhamento apropriado no servidor de arquivos StorSimple.

![video_icon](./media/storsimple-ova-restore/video_icon.png) **Vídeo disponível**

Assista ao vídeo para ver como você pode criar compartilhamentos, fazer backup de compartilhamentos e restaurar dados em uma StorSimple Virtual Array.

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Use-the-StorSimple-Virtual-Array/player]
> 
> 

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como [administrar sua StorSimple Virtual Array usando a interface do usuário da Web local](storsimple-ova-web-ui-admin.md).




<!--HONumber=Nov16_HO3-->


