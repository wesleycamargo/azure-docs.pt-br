<properties 
   pageTitle="Gerenciar as políticas de backup StorSimple | Microsoft Azure"
   description="Explica como você pode usar o serviço StorSimple Manager para criar e gerenciar backups manuais, agendas de backup e retenção de backup."
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
   ms.date="12/14/2015"
   ms.author="v-sharos"/>

# Usar o serviço StorSimple Manager para gerenciar políticas de backup

[AZURE.INCLUDE [storsimple-version-selector-manage-backup-policies](../../includes/storsimple-version-selector-manage-backup-policies.md)]

## Visão geral

Este tutorial explica como usar a página **Políticas de Backup** do serviço Gerenciador do StorSimple para controlar os processos e a retenção de backup dos volumes do StorSimple. Ele também descreve como concluir um backup manual.

A página **Políticas de Backup** permite gerenciar políticas de backup e agendar instantâneos de nuvem e local. (As políticas de backup são usadas para configurar agendamentos e retenção de backup para um conjunto de volumes). Políticas de backup permitem tirar um instantâneo de vários volumes ao mesmo tempo. Isso significa que os backups criados por uma política de backup serão cópias consistentes com falhas. Essa página lista as políticas de backup, seus tipos, os volumes associados, o número de backups retidos e a opção para habilitar essas políticas.

A página **Políticas de Backup** também permite filtrar as políticas de backup existentes por um ou mais dos seguintes campos:

- **Nome da política** – o nome associado à política. Os diferentes tipos de políticas incluem:

   - Políticas agendadas, que são criadas explicitamente pelo usuário.
   - Políticas automáticas, que são criadas quando o backup padrão para essa opção de volume foi habilitado no momento da criação do volume. Essas políticas são nomeadas como VolumeName\_Default, em que Volume se refere ao nome do volume StorSimple configurado pelo usuário no Portal clássico do Azure. As políticas automáticas resultam em instantâneos diários de nuvem, começando na hora do dispositivo 22:30.
   - Políticas importadas, que foram originalmente criadas no Gerenciador de Instantâneos do StorSimple. Elas têm uma marca que descreve o host do Gerenciador de Instantâneos do StorSimple do qual as políticas foram importadas.

- **Volumes** – os volumes associados à política. Todos os volumes associados a uma política de backup são agrupados quando os backups são criados.

- **Último backup bem-sucedido** – a data e hora do último backup bem-sucedido realizado com essa política.

- **Próximo backup** – a data e hora do próximo backup agendado que será iniciado por essa política.

- **Agendas** – o número de agendamentos associados à política de backup.

As operações usadas com frequência que podem ser executadas nessa página são:

- Adicionar uma política de backup 
- Adicionar ou modificar um agendamento 
- Excluir uma política de backup 
- Fazer um backup manual 
- Criar uma política de backup personalizada com vários volumes e agendamentos 

## Adicionar uma política de backup

Adicione uma política de backup para agendar automaticamente seus backups. Execute as etapas a seguir no Portal clássico do Azure para adicionar uma política de backup ao seu dispositivo StorSimple. Depois de adicionar a política, você poderá definir um agendamento (confira [Adicionar ou modificar um agendamento](#add-or-modify-a-schedule)).

[AZURE.INCLUDE [storsimple-add-backup-policy](../../includes/storsimple-add-backup-policy.md)]

![Vídeo disponível](./media/storsimple-manage-backup-policies/Video_icon.png) **Vídeo disponível**

Para assistir a um vídeo que demonstra como criar um local ou a política de backup na nuvem, clique [aqui](http://azure.microsoft.com/documentation/videos/create-storsimple-backup-policies/).


## Adicionar ou modificar um agendamento

É possível adicionar ou modificar um agendamento que esteja anexado a uma política de backup existente no dispositivo StorSimple. Execute as etapas a seguir no Portal clássico do Azure para adicionar ou modificar um agendamento.

[AZURE.INCLUDE [storsimple-add-modify-backup-schedule](../../includes/storsimple-add-modify-backup-schedule.md)]

## Excluir uma política de backup

Execute as etapas a seguir no Portal clássico do Azure para excluir uma política de backup do seu dispositivo StorSimple.

[AZURE.INCLUDE [storsimple-delete-backup-policy](../../includes/storsimple-delete-backup-policy.md)]


## Fazer um backup manual

Execute as etapas a seguir no Portal clássico do Azure para criar um backup sob demanda (manual) para um único volume.

[AZURE.INCLUDE [storsimple-create-manual-backup](../../includes/storsimple-create-manual-backup.md)]

## Criar uma política de backup personalizada com vários volumes e agendamentos

Execute as etapas a seguir no Portal clássico do Azure para criar uma política de backup personalizada que tenha vários volumes e agendamentos.

[AZURE.INCLUDE [storsimple-create-custom-backup-policy](../../includes/storsimple-create-custom-backup-policy.md)]


## Próximas etapas

- Saiba mais sobre como [usar o serviço do StorSimple Manager para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=AcomDC_1217_2015-->