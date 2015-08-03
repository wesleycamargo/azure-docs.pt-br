<properties 
   pageTitle="Gerenciar catálogo de backup do StorSimple | Microsoft Azure"
   description="Explica como usar o serviço StorSimple de gerenciador do StorSimple para gerenciar conjuntos de backup para um volume."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/24/2015"
   ms.author="v-sharos" />

# Gerenciar catálogo de backup do StorSimple

## Visão geral

A página **Catálogo de Backup** exibe todos os conjuntos de backup criados após a realização de backups manuais ou automatizados. Você pode usar esta página para listar todos os backups para uma política de backup ou volume, selecionar ou excluir os backups, ou usar um backup para restaurar ou clonar um volume.

Este tutorial explica como listar, selecionar e excluir um conjunto de backup. Para saber como restaurar seu dispositivo de backup, vá para [Restaurar seu dispositivo por meio de um conjunto de backup](storsimple-restore-from-backup-set.md). Para saber como clonar um volume, vá para [Clonar um volume do StorSimple](storsimple-clone-volume.md).

![Catálogo de backup](./media/storsimple-manage-backup-catalog/HCS_BackupCatalog.png)

**Figura 1: Catálogo de backup**

A página **Catálogo de Backup** oferece uma consulta que ajuda a restringir sua seleção de conjuntos de backup. Você pode filtrar os conjuntos de backup recuperados com base nos seguintes parâmetros:

- **Dispositivo** – O dispositivo no qual o conjunto de backup foi criado.

- **Política de backup ou volume** – A política ou volume de backup associado a este conjunto de backup.

- **De e Para** – O intervalo de data e hora em que o conjunto de backup foi criado.

Os conjuntos de backup filtrados são então tabulados com base nos seguintes atributos:

- **Nome** – O nome da política de backup ou do volume associada a este conjunto de backup.

- **Tamanho** – O tamanho real do conjunto de backup.

- **Criado** em – O intervalo de data e hora em que os backups foram criados.

- **Tipo** – Conjuntos de Backup podem ser instantâneos locais ou instantâneos de nuvem. Um instantâneo local é um backup de todos os dados do volume armazenadas localmente no dispositivo, enquanto um instantâneo de nuvem refere-se ao backup dos dados do volume que residem na nuvem. Instantâneos locais fornecem acesso mais rápido, enquanto os instantâneos de nuvem são escolhidos para resiliência de dados.

- **Iniciada por** – Os backups podem ser iniciados de forma automática, de acordo com uma agenda, ou de forma manual por um usuário. Você pode usar uma política de backup para agendar backups. Como alternativa, você pode usar a opção **Fazer backup** para fazer um backup interativo.

## Listar conjuntos de backup para um volume
 
Conclua as etapas a seguir para listar todos os backups de um volume.

#### Para listar conjuntos de backup

1. Na página do serviço Gerenciador do StorSimple, clique na guia **Catálogo de backup**.

2. Filtre as seleções da seguinte maneira:

    1. Selecione o dispositivo adequado.

    2. Na lista suspensa, escolha um volume para exibir os backups correspondentes.

    3. Especifique o intervalo de tempo.

    4. Clique no ícone de verificação ![Ícone de verificação](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) para executar esta consulta.
 
    Os backups associados ao volume selecionado devem aparecer na lista de conjuntos de backup.

## Selecione um conjunto de backup

Conclua as seguintes etapas para selecionar um conjunto de backup para uma política de volume ou backup.

#### Para selecionar um conjunto de backups

1. Na página do serviço Gerenciador do StorSimple, clique na guia **Catálogo de backup**.

2. Filtre as seleções da seguinte maneira:

    1. Selecione o dispositivo adequado.

    2. Na lista suspensa, escolha o volume ou a política de backup para o backup que você deseja selecionar.

    3. Especifique o intervalo de tempo.

    4. Clique no ícone de verificação ![Ícone de verificação](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) para executar esta consulta.

    Os backups associados ao volume ou à política de backup selecionada devem aparecer na lista de conjuntos de backup.

3. Selecione e expanda um conjunto de backups. As opções **Restaurar e Excluir** são exibidas na parte inferior da página. Você pode executar qualquer uma dessas ações no conjunto de backup selecionado.

## Excluir um conjunto de backups

Exclua um backup quando você não quiser mais manter os dados associados a ele. Execute as etapas a seguir para excluir um conjunto de backups.

#### Para excluir um conjunto de backups

1. Na página do serviço Gerenciador do StorSimple, clique na guia **Catálogo de backup**.

2. Filtre as seleções da seguinte maneira:

    1. Selecione o dispositivo adequado.

    2. Na lista suspensa, escolha o volume ou a política de backup para o backup que você deseja selecionar.

    3. Especifique o intervalo de tempo.

    4. Clique no ícone de verificação ![Ícone de verificação](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) para executar esta consulta.

    Os backups associados ao volume ou à política de backup selecionada devem aparecer na lista de conjuntos de backup.

3. Selecione e expanda um conjunto de backups. As opções **Restaurar e Excluir** são exibidas na parte inferior da página. Clique em **Excluir**. Excluir conjunto de backups

4. Você será notificado quando a exclusão estiver em andamento e quando ela for concluída com êxito. Após a exclusão, atualize a consulta nesta página. O conjunto de backup excluído não aparecerá mais na lista de conjuntos de backup.

## Próximas etapas

Saiba como usar o catálogo de backup para [restaurar seu dispositivo por meio de um conjunto de backup](storsimple-restore-from-backup-set.md).

<!---HONumber=July15_HO4-->