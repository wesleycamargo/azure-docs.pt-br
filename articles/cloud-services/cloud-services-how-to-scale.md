---
title: "Dimensionar automaticamente um serviço de nuvem no portal (portal clássico) | Microsoft Docs"
description: "(clássico) Saiba como usar o portal clássico para configurar regras de dimensionamento automático para uma função web ou função de trabalho do serviço de nuvem no Azure."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: eb167d70-4eba-42a4-b157-d8d0688abf4b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2017
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 0b404af5b638ec2d543ce98b562b7df538652f70
ms.openlocfilehash: cf91520197ec292ff213afef06955a3f4df926f1


---
# <a name="how-to-auto-scale-a-cloud-service"></a>Como dimensionar automaticamente um serviço de nuvem
> [!div class="op_single_selector"]
> * [Portal do Azure](cloud-services-how-to-scale-portal.md)
> * [Portal clássico do Azure](cloud-services-how-to-scale.md)
> 
> 

Na página Dimensionar do portal clássico do Azure, você pode dimensionar manualmente sua função Web ou função de trabalho, ou pode habilitar o dimensionamento automático com base na carga de CPU ou em uma fila de mensagens.

> [!NOTE]
> Este artigo se concentra nas funções Web e de trabalho do Serviço de Nuvem. Ao criar uma máquina virtual (modelo clássico) diretamente, ela será hospedada em um serviço de nuvem. Algumas dessas informações se aplica a esses tipos de máquinas virtuais. O dimensionamento de um conjunto de disponibilidade das máquinas virtuais é, na verdade, simplesmente ligá-las e desligá-las com base nas regras de dimensionamento configuradas. Para saber mais sobre as máquinas virtuais e conjuntos de disponibilidade, confira [Gerenciar a disponibilidade de máquinas virtuais](../virtual-machines/virtual-machines-windows-classic-configure-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> 
> 

Você deve considerar as seguintes informações antes de configurar a colocação em escala do seu aplicativo:

* A colocação em escala é afetada pelo uso de núcleo. As instâncias de função maiores usam mais núcleos. Você só pode dimensionar um aplicativo dentro do limite de núcleos para sua assinatura. Por exemplo, se sua assinatura tem um limite de vinte núcleos e você executa um aplicativo com os dois serviços de nuvem de tamanho médio (um total de quatro núcleos), você só pode escalar verticalmente o dimensionamento de outras implantações do serviço de nuvem em sua assinatura em dezesseis núcleos. Para saber mais, confira [Tamanhos do Serviço de Nuvem](cloud-services-sizes-specs.md) .
* Você deve criar uma fila e associá-la a uma função antes de dimensionar um aplicativo com base em um limite de mensagens. Para obter mais informações, consulte [Como usar o serviço de armazenamento de fila](../storage/storage-dotnet-how-to-use-queues.md)(a página pode estar em inglês).
* Você pode dimensionar recursos vinculados ao seu serviço de nuvem. Para obter mais informações sobre a vinculação de recursos, consulte [Como vincular um recurso a um serviço de nuvem](cloud-services-how-to-manage.md#how-to-link-a-resource-to-a-cloud-service).
* Para habilitar a alta disponibilidade do seu aplicativo, você deverá garantir que ele esteja implantado com duas ou mais instâncias de função. Para obter mais informações, consulte [Contratos de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/).

## <a name="schedule-scaling"></a>Agendar o dimensionamento
Por padrão, nenhuma função segue um agendamento específico. Portanto, qualquer configuração alterada será aplicada a todos os horários e a todos os dias durante o ano. Se desejar, você pode configurar o dimensionamento manual ou automático para:

* Dias da semana
* Finais de semana
* Noites da semana
* Manhãs da semana
* Datas específicas
* Intervalos de datas específicos

Isso é configurado no [Portal clássico do Azure](https://manage.windowsazure.com/) na página  
**Serviços de Nuvem** > **\[Seu serviço de nuvem\]** > **Escala** > **\[Produção ou preparo\]**.

Clique no botão **configurar horas agendadas** para cada função que você deseja alterar.

![Dimensionamento automático do serviço de nuvem baseado em uma agenda][scale_schedules]

## <a name="manual-scale"></a>Dimensionamento manual
Na página **Escala** , você pode aumentar ou diminuir manualmente o número de instâncias em execução em um serviço de nuvem. Isso é configurado para cada agenda criada ou para todos os horários, caso você não tenha criado uma agenda.

1. No [portal clássico do Azure](https://manage.windowsazure.com/), clique em **Serviços de Nuvem**e no nome do Serviço de Nuvem para abrir o painel.
   
   > [!TIP]
   > Se você não vir seu serviço de nuvem, talvez seja necessário alterar de **Produção** para **Preparo** ou vice-versa.
   > 
   > 
2. Clique em **Escala**.
3. Escolha a agenda para a qual você deseja alterar as opções de dimensionamento. O padrão será *Nenhum horário agendado* se você não tiver agendas definidas.
4. Encontre a seção **Dimensionar por métrica** e escolha **NENHUMA**. Essa é a configuração padrão para todas as funções.
5. Cada função no Serviço de Nuvem tem um controle deslizante para alterar o número de instâncias a serem usadas.
   
    ![Dimensionar manualmente uma função de serviço de nuvem][manual_scale]
   
    Se você precisar de mais instâncias, talvez seja necessário alterar o [tamanho da máquina virtual do serviço de nuvem](cloud-services-sizes-specs.md).
6. Clique em **Salvar**.  
   As instâncias de função serão adicionadas ou removidas com base nas suas seleções.

> [!TIP]
> Sempre que você vir ![][tip_icon], mova o mouse até ele e obtenha ajuda sobre a função de uma configuração específica.
> 
> 

## <a name="automatic-scale---cpu"></a>Dimensionamento automático - CPU
Isso dimensiona se a porcentagem média do uso da CPU ficar acima ou abaixo de limites especificados; as instâncias de função são criadas ou excluídas.

1. No [portal clássico do Azure](https://manage.windowsazure.com/), clique em **Serviços de Nuvem**e no nome do Serviço de Nuvem para abrir o painel.
   
   > [!TIP]
   > Se você não vir seu serviço de nuvem, talvez seja necessário alterar de **Produção** para **Preparo** ou vice-versa.
   > 
   > 
2. Clique em **Escala**.
3. Escolha a agenda para a qual você deseja alterar as opções de dimensionamento. O padrão será *Nenhum horário agendado* se você não tiver agendas definidas.
4. Encontre a seção **Dimensionar por métrica** e selecione **CPU**.
5. Agora você pode configurar um intervalo mínimo e máximo de instâncias de função, o uso da CPU de destino (para disparar um escalonamento vertical) e quantas instâncias para escalonar vertical ou horizontalmente.

![Dimensionar uma função de serviço de nuvem por carga de cpu][cpu_scale]

> [!TIP]
> Sempre que você vir ![][tip_icon], mova o mouse até ele e obtenha ajuda sobre a função de uma configuração específica.
> 
> 

## <a name="automatic-scale---queue"></a>Dimensionamento automático - fila
Isso dimensionará automaticamente se o número de mensagens em uma fila estiver acima ou abaixo do limite especificado. As instâncias de função são criadas ou excluídas.

1. No [portal clássico do Azure](https://manage.windowsazure.com/), clique em **Serviços de Nuvem**e no nome do Serviço de Nuvem para abrir o painel.
   
   > [!TIP]
   > Se você não vir seu serviço de nuvem, talvez seja necessário alterar de **Produção** para **Preparo** ou vice-versa.
   > 
   > 
2. Clique em **Escala**.
3. Encontre a seção **Dimensionar por métrica** e selecione **CPU**.
4. Agora você pode configurar um intervalo mínimo e máximo de instâncias de função, a fila e a quantidade de mensagens de filas a serem processadas para cada instância e quantas instâncias para escalonar vertical ou horizontalmente.

![Dimensionar uma função de serviço de nuvem por fila de mensagens][queue_scale]

> [!TIP]
> Sempre que você vir ![][tip_icon], mova o mouse até ele e obtenha ajuda sobre a função de uma configuração específica.
> 
> 

## <a name="scale-linked-resources"></a>Dimensionar recursos vinculados
Sempre que você dimensionar uma função, também é benéfico dimensionar o banco de dados que o aplicativo está usando. Se você vincular o banco de dados ao serviço de nuvem, poderá acessar as configurações de dimensionamento para esse recurso clicando no link apropriado.

1. No [portal clássico do Azure](https://manage.windowsazure.com/), clique em **Serviços de Nuvem**e no nome do Serviço de Nuvem para abrir o painel.
   
   > [!TIP]
   > Se você não vir seu serviço de nuvem, talvez seja necessário alterar de **Produção** para **Preparo** ou vice-versa.
   > 
   > 
2. Clique em **Escala**.
3. Encontre a seção **recursos vinculados** e clique em **Gerenciar dimensionamento para este banco de dados**.
   
   > [!NOTE]
   > Caso você não veja uma seção **recursos vinculados** , é provável que você não tenha recursos vinculados.
   > 
   > 

![][linked_resource]

[manual_scale]: ./media/cloud-services-how-to-scale/manual-scale.png
[queue_scale]: ./media/cloud-services-how-to-scale/queue-scale.png
[cpu_scale]: ./media/cloud-services-how-to-scale/cpu-scale.png
[tip_icon]: ./media/cloud-services-how-to-scale/tip.png
[scale_schedules]: ./media/cloud-services-how-to-scale/schedules.png
[scale_popup]: ./media/cloud-services-how-to-scale/schedules-dialog.png
[linked_resource]: ./media/cloud-services-how-to-scale/linked-resources.png



<!--HONumber=Jan17_HO1-->


