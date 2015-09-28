<properties 
   pageTitle="Exibir e gerenciar trabalhos do StorSimple | Microsoft Azure"
   description="Descreve a página de Trabalhos do serviço StorSimple Manager e como usá-la para controlar os trabalhos de backup recentes, atuais e agendados."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carolz"
   editor=""/>
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="09/15/2015"
   ms.author="alkohli" />

# Use o serviço StorSimple Manager para exibir e gerenciar trabalhos do StorSimple

## Visão geral

A página **Trabalhos** oferece um portal central único para visualização e gerenciamento de trabalhos que foram iniciados em dispositivos conectados ao serviço StorSimple Manager. É possível exibir os trabalhos agendados, em execução, concluídos e com falha para vários dispositivos. Os resultados são apresentados em um formato tabular.

![Página Trabalhos](./media/storsimple-manage-jobs/HCS_JobsPage.png)

Você pode localizar rapidamente os trabalhos nos quais está interessado filtrando os campos, como:

- **Status** – Os trabalhos podem estar em execução, agendados, com falha, concluídos, em cancelamento ou cancelados.

- **Tipo** – Os trabalhos podem ser criados como resultado de um backup sob demanda ou agendado (**Fazer Backup**), clonagem, uma restauração de dispositivo ou uma operação de atualização.

- **Dispositivos** – Os trabalhos são iniciados em um determinado dispositivo conectado ao seu serviço.

- **De e Para** – Os trabalhos podem ser filtrados com base no intervalo de data e hora.

Os trabalhos filtrados são então tabulados com base nos seguintes atributos:

- **Tipo** – Backup, clone, restauração, failover ou atualização.

- **Status** – Em execução, agendado, com falha, concluído, em cancelamento ou cancelado.

- **Entidade** – Os trabalhos podem ser associados a um volume, uma política de backup ou um dispositivo. Um trabalho de clone é associado a um volume, enquanto um trabalho de backup agendado é associado a uma política de backup. Um trabalho de dispositivo é criado como resultado de uma recuperação de desastres (DR) ou uma operação de restauração.

- **Dispositivo** – O nome do dispositivo no qual o trabalho foi iniciado.

- **Iniciado em** – A hora em que o trabalho foi iniciado.

- **Progresso** – A porcentagem de conclusão de um trabalho em execução. Para um trabalho concluído, sempre deve ser 100%.

A lista de trabalhos é atualizada a cada 30 segundos.

É possível executar as seguintes ações relacionadas a trabalho nesta página:

- Exibir detalhes do trabalho

- Cancelar um trabalho

## Exibir detalhes do trabalho

Execute as etapas a seguir para exibir os detalhes de qualquer trabalho.

#### Para exibir detalhes do trabalho

1. Na página **Trabalhos**, exiba os trabalhos nos quais está interessado executando uma consulta com os filtros apropriados. É possível pesquisar trabalhos concluídos, em execução ou cancelados.

2. Selecione um trabalho.

3. Na parte inferior da página, clique em **Detalhes**.

4. Na caixa de diálogo **Detalhes do Trabalho de Backup**, é possível exibir o status, os detalhes, as estatísticas de tempo e as estatísticas de dados.

## Cancelar um trabalho

Realize as etapas a seguir para cancelar um trabalho em execução.

### Para cancelar um trabalho

1. Na página **Trabalhos**, exiba os trabalhos em execução que você deseja cancelar executando uma consulta com os filtros apropriados.

1. Selecione o trabalho.

1. Na parte inferior da página, clique em **Cancelar**.

1. Quando solicitado a confirmar, clique em **Sim**.

Este trabalho agora está cancelado.

## Próximas etapas

- Saiba como [gerenciar as políticas de backup do StorSimple](storsimple-manage-backup-policies.md).

- Saiba como [usar o serviço StorSimple Manager para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=Sept15_HO3-->