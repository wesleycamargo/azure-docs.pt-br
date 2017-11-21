---
title: Exibir e gerenciar trabalhos do StorSimple | Microsoft Docs
description: "Descreve a página de Trabalhos do serviço StorSimple Manager e como usá-la para controlar os trabalhos de backup recentes, atuais e agendados."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 55922cd0-d490-48eb-938a-012a67c1c09e
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 426fd3ec76157670c9d192f007adacc4284abb42
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2017
---
# <a name="use-the-storsimple-manager-service-to-view-and-manage-storsimple-jobs"></a>Use o serviço StorSimple Manager para exibir e gerenciar trabalhos do StorSimple
> [!NOTE]
> O portal clássico para StorSimple foi preterido. Os Gerenciadores de Dispositivos do StorSimple migrarão automaticamente para o novo Portal do Azure, seguindo o agendamento definido para preteri-los. Você receberá um email e uma notificação de portal para essa mudança. Este documento também será desativado em breve. Para dúvidas sobre a migração, consulte [Perguntas Frequentes: migração para o Portal do Azure](storsimple-8000-move-azure-portal-faq.md).

[!INCLUDE [storsimple-version-selector-manage-jobs](../../includes/storsimple-version-selector-manage-jobs.md)]

## <a name="overview"></a>Visão geral
A página **Trabalhos** oferece um portal central único para visualização e gerenciamento de trabalhos que foram iniciados em dispositivos conectados ao serviço StorSimple Manager. É possível exibir os trabalhos agendados, em execução, concluídos e com falha para vários dispositivos. Os resultados são apresentados em um formato tabular.

![Página Trabalhos](./media/storsimple-manage-jobs/HCS_JobsPage.png)

Você pode localizar rapidamente os trabalhos nos quais está interessado filtrando os campos, como:

* **Status** – os trabalhos podem estar em execução, agendados, com falha, concluídos, em cancelamento ou cancelados.
* **Tipo** – Os trabalhos podem ser criados como resultado de um backup sob demanda ou agendado (**Fazer Backup**), clonagem, uma restauração de dispositivo ou uma operação de atualização.
* **Dispositivos** – os trabalhos são iniciados em um determinado dispositivo conectado ao seu serviço.
* **De e Para** – os trabalhos podem ser filtrados com base no intervalo de data e hora.

Os trabalhos filtrados são então tabulados com base nos seguintes atributos:

* **Tipo** – backup, clone, restauração, failover ou atualização.
* **Status** – em execução, agendado, com falha, concluído, em cancelamento ou cancelado.
* **Entidade** – os trabalhos podem ser associados a um volume, uma política de backup ou um dispositivo. Um trabalho de clone é associado a um volume, enquanto um trabalho de backup agendado é associado a uma política de backup. Um trabalho de dispositivo é criado como resultado de uma recuperação de desastres (DR) ou uma operação de restauração.
* **Dispositivo** – o nome do dispositivo no qual o trabalho foi iniciado.
* **Iniciado em** – a hora em que o trabalho foi iniciado.
* **Andamento** – o percentual de conclusão de um trabalho em execução. Para um trabalho concluído, sempre deve ser 100%.

A lista de trabalhos é atualizada a cada 30 segundos.

É possível executar as seguintes ações relacionadas a trabalho nesta página:

* Exibir detalhes do trabalho
* Cancelar um trabalho

## <a name="view-job-details"></a>Exibir detalhes do trabalho
Execute as etapas a seguir para exibir os detalhes de qualquer trabalho.

#### <a name="to-view-job-details"></a>Para exibir detalhes do trabalho
1. Na página **Trabalhos** , exiba o(s) trabalho(s) no(s) qual(is) está interessado executando uma consulta com os filtros apropriados. É possível pesquisar trabalhos concluídos, em execução ou cancelados.
2. Selecione um trabalho.
3. Na parte inferior da página, clique em **Detalhes**.
4. Na caixa de diálogo **Detalhes do Trabalho de Backup** , é possível exibir o status, os detalhes, as estatísticas de tempo e as estatísticas de dados.

## <a name="cancel-a-job"></a>Cancelar um trabalho
Realize as etapas a seguir para cancelar um trabalho em execução.

### <a name="to-cancel-a-job"></a>Para cancelar um trabalho
1. Na página **Trabalhos** , exiba os trabalhos em execução que você deseja cancelar executando uma consulta com os filtros apropriados.
2. Selecione o trabalho.
3. Na parte inferior da página, clique em **Cancelar**.
4. Quando solicitado a confirmar, clique em **Sim**.

Este trabalho agora está cancelado.

## <a name="next-steps"></a>Próximas etapas
* Saiba como [gerenciar as políticas de backup do StorSimple](storsimple-manage-backup-policies.md).
* Saiba como [usar o serviço StorSimple Manager para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).

