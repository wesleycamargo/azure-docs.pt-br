---
title: "Painel do serviço StorSimple Manager – Matriz Virtual | Microsoft Docs"
description: "Descreve o painel de serviço do StorSimple Manager e explica como usá-lo para monitorar a integridade de sua Matriz Virtual StorSimple."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 36ac6eb4-a616-4bb1-8163-6862ac33da63
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 58aa9508e28a00efe69d0a9cbc8adb6837d085c4


---
# <a name="use-the-storsimple-manager-service-dashboard-for-the-storsimple-virtual-array"></a>Usar o painel do serviço StorSimple Manager para a Matriz Virtual do StorSimple
## <a name="overview"></a>Visão geral
A página de painel do serviço StorSimple Manager fornece uma exibição resumida das Matrizes Virtuais StorSimple (também conhecidas como dispositivos virtuais StorSimple locais ou dispositivos virtuais) que estão conectados ao serviço StorSimple Manager, destacando aqueles que precisam de atenção do administrador do sistema. Este tutorial apresenta a página do painel, explica seu conteúdo e função e descreve as tarefas que podem ser executadas nesta página.

![Painel de serviço](./media/storsimple-ova-service-dashboard/dashboard1.png)

O painel de serviço do StorSimple Manager exibe as seguintes informações:

* Na área **gráfico** na parte superior da página, você pode ver as métricas relevantes para seu dispositivo virtual. Você pode exibir o armazenamento primário usado em todos os dispositivos virtuais, bem como o armazenamento em nuvem consumido pelos dispositivos virtuais durante um período. Use os controles no canto superior direito do gráfico para especificar o uso relativo ou absoluto e para ver uma escala de tempo de 1 semana, 1 mês, 3 meses ou 1 ano. Usar o controle de atualização ![controle de atualização](./media/storsimple-ova-service-dashboard/refresh-control.png) para atualizar o gráfico.
* A **visão geral de uso** mostra o armazenamento primário provisionado e consumido por todos os dispositivos virtuais relativos ao armazenamento total disponível em todos os dispositivos virtuais. **Provisionado** refere-se à quantidade de armazenamento que é preparada e alocada para uso, **Usado** refere-se ao uso de compartilhamentos ou volumes conforme exibido pelos iniciadores conectados aos dispositivos virtuais e **Capacidade máx.** mostra a capacidade máxima de todos os dispositivos virtuais.
* A área de **alertas** fornece um instantâneo de todos os alertas ativos em todos os dispositivos virtuais, agrupados por gravidade do alerta. Clicar no nível de gravidade abre a página **Alertas** , projetada para mostrar os alertas. Sobre a página **Alertas** , é possível clicar em um alerta individual para exibir detalhes adicionais sobre esse alerta, incluindo todas as ações recomendadas. Você também pode limpar o alerta se o problema foi resolvido.
* A área **trabalhos** fornece um instantâneo dos trabalhos recentes em todos os dispositivos conectados ao seu serviço. Há links que você pode usar para examinar os trabalhos que estão atualmente em andamento e aqueles que foram bem-sucedidos ou falharam nas últimas 24 horas. 
* A área **rapidamente** à direita da página fornece informações úteis como o status do serviço, número total de dispositivos virtuais conectados ao serviço, local do serviço e detalhes da assinatura que está associada ao serviço. Também há um link para o log de operações. Clique no link para ver uma lista de todas as operações de serviço concluídas do StorSimple Manager. 

Você pode usar a página do painel serviço do StorSimple Manager para iniciar as seguintes tarefas:

* Obtenha a chave de registro.
* Exibir os logs de operação.

## <a name="get-the-service-registration-key"></a>Obtenha a chave de registro do serviço
A chave de registro é usada para registrar um dispositivo virtual StorSimple com o serviço StorSimple Manager, para que o dispositivo seja exibido no portal clássico do Azure para ações de gerenciamento adicionais. A chave é criada no primeiro dispositivo virtual e compartilhada com os dispositivos virtuais restantes. 

Clicar em **Chave do registro** (na parte inferior da página) abre a caixa de diálogo **Chave de registro de serviço** em que você pode copiar a chave de registro de serviço atual para a área de transferência ou regenerar a chave de registro de serviço.

![chave de registro](./media/storsimple-ova-service-dashboard/service-dashboard3.png)

Regenerar a chave não afeta os dispositivos virtuais registrados anteriormente: afeta apenas os dispositivos virtuais registrados com o serviço depois que a chave é regenerada.

Para obter mais informações sobre como obter a chave de registro do serviço, vá para [Obter a chave de registro](storsimple-ova-manage-service.md#get-the-service-registration-key).

## <a name="view-the-operations-logs"></a>Exibir os logs de operações
Você pode exibir os logs de operação clicando no link de logs de operação disponível na área de **visualização rápida** do painel. Você será levado à página de serviços de gerenciamento, onde você poderá filtrar e consultar os logs específicos ao serviço do StorSimple Manager.

![Log de operações](./media/storsimple-ova-service-dashboard/ops-log.png)

## <a name="next-steps"></a>Próximas etapas
Aprenda como [usar a interface do usuário da Web local para administrar sua StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).




<!--HONumber=Nov16_HO3-->


