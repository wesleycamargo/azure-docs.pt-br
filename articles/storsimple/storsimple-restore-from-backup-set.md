<properties 
	pageTitle="Restaurar um volume StorSimplede um cojunto de backups |do Microsoft Azure" 
	description ="Explica como usar o catálogo de backup para restaurar um volume StorSimple de um conjunto de backups." 
	services="storsimple" 
	documentationCenter="NA" 
	authors="SharS" 
	manager="carolz" 
	editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/25/2015"
   ms.author="v-sharos"/>

# Restaurar um volume do StorSimple de um conjunto de backup

## Visão geral

A página **Catálogo de Backup** exibe todos os conjuntos de backup criados após a realização de backups manuais ou automatizados. Você pode usar esta página para listar todos os backups para uma política de backup ou volume, selecionar ou excluir os backups, ou usar um backup para restaurar ou clonar um volume.

 ![Página Catálogo de Backup](./media/storsimple-restore-from-backup-set/HCS_BackupCatalog.png)

Este tutorial explica como usar a página **Catálogo de Backup** para restaurar seu dispositivo a partir de um conjunto de backup.

## Como usar o catálogo de backup 

A página **Catálogo de Backup** oferece uma consulta que ajuda a restringir sua seleção de conjuntos de backup. Você pode filtrar os conjuntos de backup recuperados com base nos seguintes parâmetros:

- **Dispositivo** – O dispositivo no qual o conjunto de backup foi criado.
- **Política de backup** ou **volume** – A política de backup ou volume associado a este conjunto de backup.
- **De** e **Para** – O intervalo de data e hora quando o conjunto de backup foi criado.

Os conjuntos de backup filtrados são então tabulados com base nos seguintes atributos:

- **Nome** – O nome da política de backup ou do volume associada a este conjunto de backup.
- **Tamanho** – O tamanho real do conjunto de backup.
- **Criado em** – O intervalo de data e hora quando os backups foram criados. 
- **Tipo** – Conjuntos de Backup podem ser instantâneos locais ou instantâneos de nuvem. Um instantâneo local é um backup de todos os dados do volume armazenadas localmente no dispositivo, enquanto um instantâneo de nuvem refere-se ao backup dos dados do volume que residem na nuvem. Instantâneos locais fornecem acesso mais rápido, enquanto os instantâneos de nuvem são escolhidos para resiliência de dados.
- **Iniciada por** – Os backups podem ser iniciados de forma automática, de acordo com uma agenda, ou de forma manual por um usuário. (Você pode usar uma política de backup para agendar backups. Como alternativa, você pode usar a opção **Fazer backup** para fazer um backup interativo).

## Como restaurar o volume StorSimple de um backup

É possível usar a página **Catálogo de Backup** para restaurar o volume StorSimple de um backup específico. No entanto, tenha em mente que a restauração de um volume reverterá o volume ao estado em que ele estava quando o backup foi feito. Todos os dados adicionados após a operação de backup serão perdidos.

> [AZURE.WARNING]A restauração de um backup substituirá os volumes existentes do backup. Isso pode causar a perda de todos os dados gravados depois que o backup tiver sido feito.


### Para restaurar de um conjunto de backup

1. Na página do serviço Gerenciador do StorSimple, clique na guia **Catálogo de backup**.

    ![Catálogo de backup](./media/storsimple-restore-from-backup-set/HCS_Restore.png)

2. Selecione um conjunto de backup desta maneira:
  1. Selecione o dispositivo adequado.
  2. Na lista suspensa, escolha o volume ou a política de backup para o backup que você deseja selecionar.
  3. Especifique o intervalo de tempo.
  4. Clique no ícone de verificação ![ícone de verificação](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png) para executar esta consulta.
 
    Os backups associados ao volume ou à política de backup selecionada devem aparecer na lista de conjuntos de backup.

3. Expanda o conjunto de backup para exibir os volumes associados. Esses volumes devem ficar offline no host e no dispositivo antes que você possa restaurá-los. Acesse os volumes na página **Contêineres de Volume** e, em seguida, siga as etapas em [Colocar um volume offline](storsimple-manage-volumes.md#take-a-volume-offline) para colocá-los offline.

    >  [AZURE.IMPORTANT]Verifique se você colocou os volumes offline primeiro no host antes de colocar os volumes offline no dispositivo. Se você não colocar os volumes offline no host, poderá ocorrer corrupção nos dados.

4. Navegue de volta para a guia **Catálogo de Backup** e selecione um conjunto de backup.

5. Clique em **Restaurar** na parte inferior da página.

6. Será solicitada a sua confirmação.

    ![Página de confirmação](./media/storsimple-restore-from-backup-set/HCS_ConfirmRestore.png)

7. Examine as informações de restauração e clique no ícone de verificação ![ícone de verificação](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png). Isso iniciará um trabalho de restauração que poderá ser exibido por meio do acesso à página **Trabalhos**.

8. Após a conclusão da restauração, você poderá verificar se o conteúdo de seus volumes foi substituído pelos volumes do backup.

## Próximas etapas

Saiba como [Gerenciar volumes do StorSimple](storsimple-manage-volumes.md)

<!---HONumber=August15_HO6-->