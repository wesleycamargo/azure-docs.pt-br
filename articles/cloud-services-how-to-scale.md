<properties urlDisplayName="How to scale" pageTitle="Como dimensionar um serviço de nuvem - Azure" metaKeywords="Azure link resource, scaling cloud service" description="Saiba como dimensionar um serviço de nuvem e os recursos vinculados no Azure." metaCanonical="" services="cloud-services" documentationCenter="" title="How to Scale an Application" authors="davidmu" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/21/2014" ms.author="davidmu" />





#Como dimensionar um aplicativo


Na página Escala do Portal de Gerenciamento do Azure, você pode dimensionar manualmente seu aplicativo ou definir parâmetros para dimensioná-lo automaticamente. Você pode dimensionar aplicativos executados em funções Web, funções de trabalho ou máquinas virtuais. Para dimensionar um aplicativo que esteja executando instâncias de funções Web ou funções de trabalho, você pode adicionar ou remover instâncias de função para acomodar a carga de trabalho.

Quando você expande ou diminui um aplicativo que está sendo executado em máquinas virtuais, novas máquinas não serão criadas nem excluídas, mas serão ativadas ou desativadas de um conjunto de disponibilidade de máquinas criadas anteriormente. Você pode especificar o dimensionamento com base em um percentual médio de uso de CPU ou com base no número de mensagens em uma fila.

Você deve considerar as seguintes informações antes de configurar o dimensionamento do seu aplicativo:

- Você deve adicionar as máquinas virtuais criadas em um conjunto de disponibilidade para dimensionar um aplicativo que as use. As máquinas virtuais que você adicionar podem ser ativadas ou desativadas inicialmente, mas elas serão ativadas em uma ação de expansão e desligadas em uma ação de redução de escala. Para obter mais informações sobre máquinas virtuais e conjuntos de disponibilidade, consulte [Gerenciamento da disponibilidade das máquinas virtuais](http://azure.microsoft.com/pt-br/documentation/articles/virtual-machines-manage-availability/).
- O dimensionamento é afetado pelo uso do núcleo. Instâncias de função ou máquinas virtuais maiores usam mais núcleos. Você só pode dimensionar um aplicativo dentro do limite dos núcleos para sua assinatura. Por exemplo, se sua assinatura tiver um limite de vinte núcleos e você executar um aplicativo com duas máquinas virtuais de tamanho médio (um total de quatro núcleos), você só poderá escalar verticalmente outras implantações do serviço de nuvem em sua assinatura em dezesseis núcleos. Todas as máquinas virtuais em um conjunto de disponibilidade que são usadas no dimensionamento de um aplicativo devem ser do mesmo tamanho. Para obter mais informações sobre o uso de núcleos e o tamanho de máquinas, consulte [Tamanhos de máquinas virtuais e serviço de nuvem para o Azure](http://msdn.microsoft.com/pt-br/library/dn197896.aspx).
- Você deve criar uma fila e associá-la a uma função ou conjunto de disponibilidade antes de dimensionar um aplicativo com base em um limite de mensagens. Para obter mais informações, consulte [Como usar o serviço de armazenamento de fila](http://www.windowsazure.com/pt-br/develop/net/how-to-guides/queue-service).
- Você pode dimensionar recursos que estão vinculados ao seu serviço de nuvem. Para obter mais informações sobre a vinculação de recursos, consulte [Como: Vincular um recurso a um serviço de nuvem](http://www.windowsazure.com/pt-br/manage/services/cloud-services/how-to-manage-a-cloud-service/#linkresources).
- Para habilitar uma alta disponibilidade para seu aplicativo, você deverá garantir que ele esteja implantado com duas ou mais instâncias de função ou máquinas virtuais. Para obter mais informações, consulte [Contratos de nível de serviço](https://www.windowsazure.com/pt-br/support/legal/sla/).

Você pode executar as seguintes ações de dimensionamento para um serviço de nuvem:

- [Dimensionar manualmente um aplicativo executando funções Web ou de trabalho](#manualscale)
- [Dimensionar automaticamente um aplicativo executando funções Web, de trabalho ou máquinas virtuais](#autoscale)
- [Dimensionar recursos vinculados](#scalelink)
- [Agendar o dimensionamento de seu aplicativo](#schedule)


<h2><a id="manualscale"></a>Dimensionar manualmente um aplicativo executando funções Web ou de trabalho</h2>

Na página Escala, você pode aumentar ou diminuir manualmente o número de instâncias em execução em um serviço de nuvem.

1. No [Portal de Gerenciamento](https://manage.windowsazure.com/), clique em **Serviços de Nuvem** e no nome do serviço de nuvem para abrir o painel.

2. Clique em **Escala**.O dimensionamento automático é desabilitado por padrão para todas as funções, o que significa que você pode alterar manualmente o número de instâncias usadas pelo aplicativo.

	![Scale page][manual_scale]

3. Cada função no serviço de nuvem tem um controle deslizante para alterar o número de instâncias a serem usadas. Para adicionar uma instância de função, arraste a barra para a direita. Para remover uma instância, arraste a barra para a esquerda.

	![Scale role][slider_role] 


	Você pode aumentar o número de instâncias usadas apenas se o número apropriado de núcleos estiver disponível para dar suporte às instâncias. As cores do controle deslizante representam os núcleos usados e disponíveis em sua assinatura:

	- O azul representa os núcleos usados pela função selecionada
	- O cinza escuro representa os núcleos usados por todas as funções e máquinas virtuais na assinatura
	- O cinza claro representa os núcleos que estão disponíveis para uso com dimensionamento
	- O rosa representa uma alteração feita que não foi salva

4. Clique em **Salvar**.As instâncias de função serão adicionadas ou removidas com base nas suas seleções.

<h2><a id="autoscale"></a>Dimensionar automaticamente um aplicativo executando funções Web, de trabalho ou máquinas virtuais</h2>

Na página Escala, você pode configurar seu serviço de nuvem para aumentar ou diminuir automaticamente o número de instâncias ou de máquinas virtuais usadas por seu aplicativo. Você pode configurar o dimensionamento com base nos seguintes parâmetros:

- [Uso médio da CPU](#averagecpu) - se o percentual médio de uso da CPU estiver acima ou abaixo dos limites especificados, instâncias de função serão criadas ou excluídas, ou máquinas virtuais serão ativadas ou desativadas de um conjunto de disponibilidade.
- [Mensagens da fila](#queuemessages) - se o número de mensagens em uma fila estiver acima ou abaixo de um limite especificado, instâncias de função serão criadas ou excluídas, ou máquinas virtuais serão ativadas ou desativadas de um conjunto de disponibilidade.

<h3><a id="averagecpu"></a>Uso médio da CPU</h3>

1. No [Portal de Gerenciamento](https://manage.windowsazure.com/), clique em **Serviços de Nuvem** e no nome do serviço de nuvem para abrir o painel.
2. Clique em **Escala**.
3. Role até a seção da função ou conjunto de disponibilidade e clique em **CPU**.Isso habilita o dimensionamento automático de seu aplicativo com base no percentual médio dos recursos de CPU que ele usa.

	![Autoscale on][autoscale_on]

4. Cada função ou conjunto de disponibilidade tem um controle deslizante para alterar o número de instâncias que podem ser usadas. Para definir o número máximo de instâncias que podem ser usadas, arraste a barra à direita para a direita. Para definir o número mínimo de instâncias que podem ser usadas, arraste a barra à esquerda para a esquerda.

	**Observação:**  Na página Escala, **Instância** representa uma instância de função ou uma instância de uma máquina virtual.

	![Instance range][instance_range]

	O número máximo de instâncias é limitado pelos núcleos disponíveis na assinatura. As cores do controle deslizante representam os núcleos usados e disponíveis em sua assinatura:

	- O azul representa o número máximo de núcleos que a função pode usar.
	- O cinza escuro representa os núcleos usados por todas as funções e máquinas virtuais na assinatura. Quando esse valor sobrepuser os núcleos usados pela função, a cor mudará para azul-escuro.
	- O cinza claro representa os núcleos que estão disponíveis para uso com dimensionamento.
	- O rosa representa uma alteração que foi feita que não foi salva.

5. Um controle deslizante é usado para especificar o intervalo do percentual médio do uso de CPU. Quando o percentual médio de uso da CPU ultrapassa a configuração máxima, mais instâncias de função são criadas ou máquinas virtuais são ativadas. Quando o percentual médio de uso da CPU é menor que a configuração máxima, mais instâncias de função são excluídas ou máquinas virtuais são desativadas. Para definir o percentual médio máximo da CPU, arraste a barra à direita para a direita. Para definir o percentual médio mínimo de CPU, arraste a barra à esquerda para a esquerda.

	![Target cpu][target_cpu]

6. Você pode especificar o número de instâncias a serem adicionadas ou ativadas sempre que o aplicativo for expandido. Para aumentar o número de instâncias que são criadas ou ativadas quando o seu aplicativo for expandido, arraste a barra para a direita. Para diminuir o número, arraste a barra para a esquerda.

	![Scale cpu up][scale_cpuup]

7. Defina o número de minutos de espera entre a última ação de dimensionamento e a próxima ação de expansão. A última ação de dimensionamento pode ser de expansão ou de redução.

	![Up time][scale_uptime]

	Todas as instâncias são incluídas no cálculo do percentual médio de uso de CPU e a média é baseada no uso ao longo da hora anterior. Dependendo do número de instâncias que seu aplicativo estiver usando, poderá demorar mais do que o tempo de espera especificado para que a ação de dimensionamento ocorra caso o tempo de espera definido seja muito baixo. O tempo mínimo ente ações de dimensionamento é de cinco minutos. As ações de dimensionamento não poderão ocorrer se alguma das instâncias estiver em um estado de transição.

8. Você também pode especificar o número de instâncias a serem excluídas ou desativadas quando seu aplicativo for reduzido.  Para aumentar o número de instâncias que são excluídas ou desativadas quando o seu aplicativo for reduzido, arraste a barra para a direita. Para diminuir o número, arraste a barra para a esquerda.

	![Scale cpu down][scale_cpudown]

	Se seu aplicativo tiver aumentos repentinos no uso da CPU, você deve certificar-se de que tem um número mínimo suficiente de instâncias para gerenciá-los.

9. Defina o número de minutos de espera entre a última ação de dimensionamento e a próxima ação de redução. A última ação de dimensionamento pode ser de expansão ou de redução.

	![Down time][scale_downtime]

10. Clique em **Salvar**.A ação de dimensionamento pode demorar até cinco minutos para ser concluída.

<h3><a id="queuemessages"></a>Mensagens da fila</h3>

1. No [Portal de Gerenciamento](https://manage.windowsazure.com/), clique em **Serviços de Nuvem** e no nome do serviço de nuvem para abrir o painel.
2. Clique em **Escala**.
3. Role até a seção da função ou do conjunto de disponibilidade e clique em **Fila**.Isso habilita o dimensionamento automático de seu aplicativo com base em um número de destino de mensagens da fila.

	![Scale queue][scale_queue]

4. Cada função ou conjunto de disponibilidade no serviço de nuvem tem um controle deslizante para alterar o número de instâncias que podem ser usadas. Para definir o número máximo de instâncias que podem ser usadas, arraste a barra à direita para a direita. Para definir o número mínimo de instâncias que podem ser usadas, arraste a barra à esquerda para a esquerda.

	![Queue range][queue_range]

	**Observação:**  Na página Escala, **Instância** representa uma instância de função ou uma instância de uma máquina virtual.
	
	O número máximo de instâncias é limitado pelos núcleos disponíveis na assinatura. As cores do controle deslizante representam os núcleos usados e disponíveis em sua assinatura:
	- O azul representa o número máximo de núcleos que a função pode usar.
	- O cinza escuro representa os núcleos usados por todas as funções e máquinas virtuais na assinatura. Quando esse valor sobrepuser os núcleos usados pela função, a cor mudará para azul-escuro.
	- O cinza claro representa os núcleos que estão disponíveis para uso com dimensionamento.
	- O rosa representa uma alteração que foi feita que não foi salva.

5. Selecione a conta de armazenamento associada à fila que você deseja usar.

	![Storage name][storage_name]	

6. Selecione a fila.

	![Queue name][queue_name]

7. Especifique o número de mensagens às quais você espera que cada instância dê suporte. As instâncias serão dimensionadas com base no número total de mensagens dividido pelo número de destino de mensagens por computador.

	![Message number][message_number]

8. Você pode especificar o número de instâncias a serem adicionadas ou ativadas sempre que o aplicativo for expandido. Para aumentar o número de instâncias que são adicionadas ou ativadas quando o dimensionamento do seu aplicativo for expandido, arraste a barra para a direita. Para diminuir o número, arraste a barra para a esquerda.

	![Scale cpu up][scale_cpuup]

9. Defina o número de minutos de espera entre a última ação de dimensionamento e a próxima ação de expansão. A última ação de dimensionamento pode ser de expansão ou de redução.

	![Up time][scale_uptime]

	O tempo mínimo ente ações de dimensionamento é de cinco minutos. As ações de dimensionamento não poderão ocorrer se alguma das instâncias estiver em um estado de transição.

10. Você também pode especificar o número de instâncias a serem excluídas ou não usadas quando o dimensionamento do aplicativo for reduzido.  Um controle deslizante é usado para especificar o incremento de dimensionamento. Para aumentar o número de instâncias que são excluídas ou não são usadas quando o dimensionamento do seu aplicativo for diminuído, arraste a barra para a direita. Para diminuir o número, arraste a barra para a esquerda.

	![Scale cpu down][scale_cpudown]

11.	Defina o número de minutos de espera entre a última ação de dimensionamento e a próxima ação de redução. A última ação de dimensionamento pode ser de expansão ou de redução.

	![Down time][scale_downtime]

12. Clique em **Salvar**.A ação de dimensionamento pode demorar até cinco minutos para ser concluída.

<h2><a id="scalelink"></a>Dimensionar recursos vinculados</h2>

Sempre que você dimensionar uma função, também é benéfico dimensionar o banco de dados que o aplicativo está usando. Se você vincular o banco de dados ao serviço de nuvem, poderá alterar a edição do Banco de Dados SQL e redimensionar o banco de dados na página Escala.

1. No [Portal de Gerenciamento](https://manage.windowsazure.com/), clique em **Serviços de Nuvem** e no nome do serviço de nuvem para abrir o painel.
2. Clique em **Escala**.
3. Na seção Recursos Vinculados, secione a edição para usar para o banco de dados.

	![Linked resources][linked_resources]

4. Selecione o tamanho do banco de dados.
5. Clique em **Salvar** para atualizar os recursos vinculados.

<h2><a id="schedule"></a>Agendar o dimensionamento de seu aplicativo</h2>

Você pode agendar o dimensionamento automático de seu aplicativo configurando agendas para diferentes momentos. As seguintes opções estão disponíveis para o dimensionamento automático:

- **Sem agenda** - essa é a opção padrão e permite que o aplicativo sempre seja dimensionado automaticamente da mesma maneira.

- **Dia e noite** - essa opção permite que você especifique o dimensionamento em determinadas horas do dia e da noite.

**Observação:**Agendas não estão disponíveis no momento para aplicativos que usam máquinas virtuais.

1. No [Portal de Gerenciamento](https://manage.windowsazure.com/), clique em **Serviços de Nuvem** e no nome do serviço de nuvem para abrir o painel.
2. Clique em **Escala**.
3. Na página Escala, clique em **configurar horas agendadas**.

	![Schedule scaling][scale_schedule]

4. Selecione o tipo de agenda de dimensionamento que você deseja configurar.

5. Especifique os horários em que o dia começa e termina e defina o fuso horário. Para o agendamento de dia e noite, os horários representam o início e o final do dia com o tempo restante representando a noite.

6. Clique na marca de seleção na parte inferior da página para salvar as agendas.

7. Depois que você salvar as agendas, elas serão exibidas na lista. Você pode selecionar o cronograma que deseja usar e, em seguida, modificar as configurações de escala. As configurações de escala serão aplicadas somente durante a agenda selecionada. Você pode editar as agendas clicando em **Configurar horas agendadas**.

[manual_scale]: ./media/cloud-services-how-to-scale/CloudServices_ManualScaleRoles.png
[slider_role]: ./media/cloud-services-how-to-scale/CloudServices_SliderRole.png
[autoscale_on]: ./media/cloud-services-how-to-scale/CloudServices_AutoscaleOn.png
[instance_range]: ./media/cloud-services-how-to-scale/CloudServices_InstanceRange.png
[target_cpu]: ./media/cloud-services-how-to-scale/CloudServices_TargetCPURange.png
[scale_cpuup]: ./media/cloud-services-how-to-scale/CloudServices_ScaleUpBy.png
[scale_uptime]: ./media/cloud-services-how-to-scale/CloudServices_ScaleUpWaitTime.png
[scale_cpudown]: ./media/cloud-services-how-to-scale/CloudServices_ScaleDownBy.png
[scale_downtime]: ./media/cloud-services-how-to-scale/CloudServices_ScaleDownWaitTime.png
[scale_queue]: ./media/cloud-services-how-to-scale/CloudServices_QueueScale.png
[queue_range]: ./media/cloud-services-how-to-scale/CloudServices_QueueRange.png
[storage_name]: ./media/cloud-services-how-to-scale/CloudServices_StorageAccountName.png
[queue_name]: ./media/cloud-services-how-to-scale/CloudServices_QueueName.png
[message_number]: ./media/cloud-services-how-to-scale/CloudServices_TargetMessageNumber.png
[linked_resources]: ./media/cloud-services-how-to-scale/CloudServices_ScaleLinkedResources.png
[scale_schedule]: ./media/cloud-services-how-to-scale/CloudServices_SetUpSchedule.png

<!--HONumber=35.1-->
