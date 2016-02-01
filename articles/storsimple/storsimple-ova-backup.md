<properties 
   pageTitle="Tutorial de backup do StorSimple Virtual Array | Microsoft Azure"
   description="Descreve como fazer backup de volumes e compartilhamentos do StorSimple Virtual Array."
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

# Fazer backup de seu StorSimple Virtual Array (visualização)

## Visão geral 

Os tutoriais se aplicam ao Microsoft Azure StorSimple Virtual Array (também conhecido como o dispositivo virtual local StorSimple ou o dispositivo virtual StorSimple) executando somente a v.1.1.1.0 (visualização).

O StorSimple Virtual Array é um dispositivo virtual local de armazenamento em nuvem híbrida que pode ser configurado como servidor de arquivos ou um servidor iSCSI. Ele pode criar backups, restaurar de backups e executar failover de dispositivo, se houver necessidade de recuperação de desastre. Quando configurado como um servidor de arquivos, ele também permite a recuperação em nível de item. Este tutorial descreve como usar o portal clássico do Azure ou a interface do usuário da Web do StorSimple para criar backups agendados e manuais do seu StorSimple Virtual Array.

>[AZURE.IMPORTANT]
>
>- A StorSimple Virtual Array está em visualização e é destinada para fins de planejamento de implantação e avaliação. Não há suporte para a instalação dessa visualização em um ambiente de produção. 
>- Se você tiver quaisquer problemas com a StorSimple Virtual Array, publique-os no [fórum do MSDN do StorSimple](https://social.msdn.microsoft.com/Forums/home?forum=StorSimple).

## Fazer backup de compartilhamentos e volumes

Backups oferecem proteção pontual, melhoram a capacidade de recuperação e minimizam os tempos de compartilhamentos e volumes. Você pode fazer backup de um compartilhamento ou volume no seu dispositivo StorSimple de duas maneiras: **Agendada** ou **Manual**. Cada um desses métodos é discutido nas seções a seguir.

> [AZURE.NOTE]Nesta versão, os backups agendados são criados por uma política padrão que é executada diariamente em um período especificado e faz backup de todos os volumes ou compartilhamentos no dispositivo. Não é possível criar políticas personalizadas para backups agendados no momento.

## Alterar o agendamento de backup

O dispositivo virtual StorSimple tem uma política de backup padrão que é iniciada em uma hora específica do dia (22:30) e faz o backup de todos os volumes ou compartilhamentos no dispositivo uma vez por dia. Você pode alterar a hora de início do backup, mas a frequência e a retenção (que especifica o número de backups a serem retidos) não podem ser alteradas. Durante esses backups, todo o dispositivo virtual é submetido a backup; portanto, é recomendável agendar esses backups para fora do horário de pico.

Execute as etapas a seguir no [portal clássico do Azure](https://manage.windowsazure.com/) para alterar o horário de início do backup padrão.

#### Para alterar o horário de início para a política de backup padrão

1. Navegue até a guia **Configuração** do dispositivo.

2. Na seção **Backup**, especifique o horário de início para o backup diário.

3. Clique em **Salvar**.

### Fazer um backup manual

Além de backups agendados, você pode fazer um backup manual (sob demanda) a qualquer momento.

#### Para criar um backup manual (sob demanda)

1. Navegue até a guia **Compartilhamentos** ou até a guia **Volumes**.

2. Na parte inferior da página, clique em **Fazer backup de tudo**. Você precisará verificar se gostaria de fazer o backup agora. Clique no ícone de verificação ![ícone de verificação](./media/storsimple-ova-backup/image3.png) para prosseguir com o backup.

    ![confirmação de backup](./media/storsimple-ova-backup/image4.png)

    Você será notificado de que um trabalho de backup está iniciando.

    ![backup iniciando](./media/storsimple-ova-backup/image5.png)

    Você será notificado de que o trabalho foi criado com êxito.

    ![trabalho de backup criado](./media/storsimple-ova-backup/image7.png)

3. Para controlar o andamento do trabalho, clique no ícone **Exibir trabalho**.

4. Depois que o trabalho de backup for concluído, vá para a guia **Catálogo de backup**. Você deve ver o backup completo.

    ![Backup concluído](./media/storsimple-ova-backup/image8.png)

5. Defina as seleções de filtro para o dispositivo apropriado, a política de backup e o intervalo de tempo; depois, clique no ícone de verificação ![ícone de verificação](./media/storsimple-ova-backup/image3.png).

    O backup deve aparecer na lista de conjuntos de backup que é exibida no catálogo.

## Exibir os backups existentes

Execute as etapas a seguir no portal clássico do Azure para exibir os backups existentes.

#### Para exibir os backups existentes

1. Na página do serviço Gerenciador do StorSimple, clique na guia **Catálogo de backup**.

2. Selecione um conjunto de backup desta maneira:

    1. Selecione o dispositivo.

    2. Na lista suspensa, escolha o compartilhamento ou volume para o backup que você deseja selecionar.

    3. Especifique o intervalo de tempo.

    4. Clique no ícone de verificação ![](./media/storsimple-ova-backup/image3.png) para executar esta consulta.

    Os backups associados ao compartilhamento ou volume selecionado devem aparecer na lista de conjuntos de backup.

## Próximas etapas

Saiba mais sobre a [administração de sua StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0121_2016-->