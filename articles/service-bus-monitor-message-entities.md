<properties linkid="service-bus-monitor-messaging-entitites" urlDisplayName="Gerenciador de Tráfego" pageTitle="Monitorar as entidades de mensagens do Barramento de Serviço - Azure" metaKeywords="" description="Saiba como monitorar suas entidades do Barramento de Serviço usando o Portal de Gerenciamento do Azure." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="service-bus" documentationCenter="" title="Como monitorar as entidades de mensagens do Barramento de Serviço" authors="" solutions="" />


# Como monitorar as entidades de mensagens do Barramento de Serviço

Este tópico descreve como gerenciar e monitorar suas entidades do Barramento de Serviço usando o [Portal de Gerenciamento do Azure](http://manage.windowsazure.com). Com o portal, você obtém uma visão abrangente do status de suas filas e tópicos. Você também pode monitorar seu uso.

## Como monitorar a atividade em filas do Barramento de Serviço

Para monitorar uma fila do Barramento de Serviço, faça o seguinte:

1. Faça logon no [Portal de Gerenciamento (Visualização) do Azure](http://manage.windowsazure.com).
2. Clique no ícone de **Barramento de Serviço** na barra de navegação à esquerda para obter a lista de namespaces de serviço. 
3. Clique no namespace que contém a fila que você deseja monitorar. 
4. Na barra dinâmica na parte superior da página, clique em **Filas**.
5. Clique no nome da fila que você deseja monitorar. O painel da fila será exibido.
6. Você pode ver as atividades nas filas que você criou. Você pode exibir essas informações em vários períodos de tempo. O padrão é 1 hora, mas você pode clicar na lista suspensa ao lado do tempo para escolher outro período de tempo: nas últimas 24 horas, sete dias ou nos últimos 30 dias. Você pode ver os dados com uma precisão de pontos de medida de, no mínimo, cinco minutos para o período de uma hora, uma hora para o período de 24 horas, e um dia para o período de sete e 30 dias. 

Para qualquer fila, você pode ver gráficos de:

- **Mensagens de Entrada**: o número de mensagens na fila durante esse intervalo de tempo.
- **Mensagens de Saída**: o número de mensagens que saíram da fila durante esse intervalo de tempo.
- **Comprimento**: o número de mensagens na entidade no final desse intervalo de tempo.
- **Tamanho**: o espaço de armazenamento (em MB) que está sendo usado por essa entidade no final desse intervalo de tempo.

###Visão Rápida###

**Visão Rápida** no painel, reflete o tamanho atual da fila como o **Comprimento da Fila**. Também exibe outras propriedades da fila ou do tópico. Essas informações são atualizadas a cada 10 segundos.

![][1]

## Como monitorar a atividade em tópicos do Barramento de Serviço

Para monitorar um tópico do Barramento de Serviço, faça o seguinte:

1. Faça logon no [Portal de Gerenciamento (Visualização) do Azure](http://manage.windowsazure.com).
2. Clique no ícone de **Barramento de Serviço** na barra de navegação à esquerda para obter a lista de namespaces de serviço. 
3. Clique no namespace que contém o tópico que você deseja monitorar. 
4. Na barra dinâmica na parte superior da página, clique em **Tópicos**.
5. Clique no nome do tópico que você deseja monitorar. O painel do tópico será exibido.

Um painel de tópico é semelhante a um painel de fila, com exceção das métricas de uso. As mensagens de saída e o comprimento não estão presentes no painel de tópico, uma vez que essas informações seriam diferentes para cada uma das assinaturas de um tópico. A guia **Monitoramento** permite que você adicione métricas de uso (número de mensagens de saída e comprimento), por assinatura de tópico. Para adicionar essas métricas, clique na guia **Monitoramento**. Em seguida, clique em **Adicionar Métricas** na parte inferior da página e escolha as assinaturas sob o tópico.


![][2]

[1]: ./media/service-bus-monitor-message-entities/QueueDashboard.png
[2]: ./media/service-bus-monitor-message-entities/AddMetrics.png

