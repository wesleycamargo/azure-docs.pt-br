<properties 
   pageTitle="Exibir e gerenciar trabalhos do StorSimple | Microsoft Azure"
   description="Descreve a página de Trabalhos do serviço StorSimple Manager e como usá-la para controlar os trabalhos de backup recentes, atuais e agendados."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# Use o serviço StorSimple Manager para exibir e gerenciar trabalhos do StorSimple (Atualização 2)

[AZURE.INCLUDE [storsimple-version-selector-manage-jobs](../../includes/storsimple-version-selector-manage-jobs.md)]

## Visão geral

A página **Trabalhos** oferece um portal central único para visualização e gerenciamento de trabalhos que foram iniciados em dispositivos conectados ao serviço StorSimple Manager. É possível exibir os trabalhos agendados, em execução, concluídos, cancelados e com falha para vários dispositivos. Os resultados são apresentados em um formato tabular.

![Página Trabalhos](./media/storsimple-manage-jobs-u2/jobs.png)

Você pode localizar rapidamente os trabalhos nos quais está interessado filtrando os campos, como:

- **Status** – os trabalhos podem estar em execução, concluídos, cancelados, com falha, em cancelamento ou concluídos com erros.
- **De e Para** – os trabalhos podem ser filtrados com base no intervalo de data e hora.
- **Tipo** – o tipo de trabalho pode ser backup, backup manual, restauração, clonagem, failover de dispositivo, criação de volume fixado localmente, modificação de volume, atualização, pacote de suporte ou provisionamento do dispositivo virtual.

- **Dispositivos** – os trabalhos são iniciados em um determinado dispositivo conectado ao seu serviço. Os trabalhos filtrados são então tabulados com base nos seguintes atributos:

    - **Tipo** – backup, backup manual, restauração, clonagem, failover de dispositivo, criação de volume fixado localmente, modificação de volume, atualização, pacote de suporte ou provisionamento do dispositivo virtual.

    - **Status** – em execução, concluídos, cancelados, com falha, em cancelamento ou concluídos com erros.

    - **Entidade** – os trabalhos podem ser associados a um volume, uma política de backup ou um dispositivo. Por exemplo, um trabalho de clone é associado a um volume, enquanto um trabalho de backup agendado é associado a uma política de backup. Um trabalho de dispositivo é criado como resultado de uma recuperação de desastres (DR) ou uma operação de restauração.

    - **Dispositivo** – o nome do dispositivo no qual o trabalho foi iniciado.

    - **Iniciado em** – a hora em que o trabalho foi iniciado.

    - **Andamento** – o percentual de conclusão de um trabalho em execução. Para um trabalho concluído, sempre deve ser 100%.

A lista de trabalhos é atualizada a cada 30 segundos.

É possível executar as seguintes ações relacionadas a trabalho nesta página:

- Exibir detalhes do trabalho

- Cancelar um trabalho

## Exibir detalhes do trabalho

Execute as etapas a seguir para exibir os detalhes de qualquer trabalho.

#### Para exibir detalhes do trabalho

1. Na página **Trabalhos**, exiba o(s) trabalho(s) no(s) qual(is) está interessado executando uma consulta com os filtros apropriados. É possível pesquisar trabalhos concluídos, em execução ou cancelados.

2. Selecione um trabalho.

3. Na parte inferior da página, clique em **Detalhes**.

4. Na caixa de diálogo **Detalhes do Trabalho de Backup**, é possível exibir o status, os detalhes, as estatísticas de tempo e as estatísticas de dados.
 
    ![Página de detalhes do trabalho](./media/storsimple-manage-jobs-u2/JobDetails.png)

## Cancelar um trabalho

Realize as etapas a seguir para cancelar um trabalho em execução.

>[AZURE.NOTE] Alguns trabalhos, como modificar um volume para alterar o tipo de volume ou expandir um volume, não podem ser cancelados.

### Para cancelar um trabalho

1. Na página **Trabalhos**, exiba os trabalhos em execução que você deseja cancelar executando uma consulta com os filtros apropriados.

1. Selecione o trabalho.

1. Na parte inferior da página, clique em **Cancelar**.

1. Quando solicitado a confirmar, clique em **Sim**.

Este trabalho agora está cancelado.

## Próximas etapas

- Saiba como [gerenciar as políticas de backup do StorSimple](storsimple-manage-backup-policies.md).

- Saiba como [usar o serviço StorSimple Manager para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=AcomDC_0817_2016-->