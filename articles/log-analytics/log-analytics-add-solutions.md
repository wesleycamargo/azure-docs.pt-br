<properties
	pageTitle="Adicionar soluções do Log Analytics por meio da Galeria de Soluções | Microsoft Azure"
	description="As soluções do Log Analytics são uma coleção de lógica, visualização e regras de aquisição de dados que fornecem as métricas que giram em torno de uma área de problema específica."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>

# Adicionar soluções do Log Analytics por meio da Galeria de Soluções

As soluções do Log Analytics são uma coleção de **lógica**, **visualização** e **regras de aquisição de dados** que fornecem as métricas que giram em torno de uma área de problema específica. Este artigo lista as soluções com suporte do Log Analytics e explica como adicionar e removê-las usando a Galeria de Soluções.

As soluções permitem análises mais profundas em relação ao seguinte:
- ajudar a investigar e resolver problemas operacionais com mais rapidez
- coletar e correlacionar vários tipos de dados de computador
- ajudá-lo a ser proativo com atividades tais como planejamento de capacidade, relatórios de status de patch e auditoria de segurança.


>[AZURE.NOTE] O OMS inclui a funcionalidade base de Pesquisa de Log e, portanto, não é necessário instalar uma solução para habilitá-la. No entanto, é possível obter mais funcionalidade com a adição de soluções na página Galeria de Soluções.

Depois de adicionar uma solução, os dados são coletados dos servidores em sua infraestrutura e enviados para o serviço do OMS. O processamento pelo serviço do OMS pode levar de alguns minutos a várias horas. Depois que o serviço processar os dados, será possível exibi-los no OMS.

Você pode facilmente remover uma solução quando ela não for mais necessário. Quando você remove uma solução, seus dados não são enviados ao OMS, o que pode reduzir a quantidade de dados usados pela sua cota diária, caso você tenha uma.


## Soluções com suporte no Microsoft Monitoring Agent

Neste momento, os servidores conectados diretamente ao OMS com o uso do Microsoft Monitoring Agent podem usar a maioria das soluções disponíveis, incluindo:

- Atualizações do Sistema
- Antimalware
- Controle de Alterações
- Avaliação do SQL
- Avaliação do Active Directory
- Gerenciamento de alertas (excluindo alertas SCOM)

No entanto, as soluções a seguir *não* têm suporte do Microsoft Monitoring Agent e exigem o agente SCOM (System Center Operations Manager).

- Gerenciamento de Capacidade
- Gerenciamento de alertas (incluindo alertas SCOM)
- Avaliação de Configuração

Consulte [Conectando o Operations Manager ao Log Analytics](log-analytics-om-agents.md) para obter informações sobre como conectar o agente SCOM ao Log Analytics.

### Para adicionar uma solução usando a Galeria de Soluções

1. Na página Visão geral do OMS, clique no bloco **Galeria de Soluções**.![galeria de soluções](./media/log-analytics-add-solutions/sol-gallery.png)
2. Na página Galeria de Soluções do OMS, saiba mais sobre cada solução disponível. Clique no nome da solução que deseja adicionar ao OMS.
3. Na página para a solução que você escolheu, são exibidas informações detalhadas sobre a solução. Clique em **Adicionar**.
4. Um novo bloco para a solução que você adicionou é mostrado na página Visão geral no OMS e será possível começar a usá-lo depois que o serviço de OMS processar seus dados.

## Para configurar as soluções
1. Você precisará configurar algumas soluções. Por exemplo, você precisará configurar a Automação, o Azure Site Recovery e o Backup antes de usá-los.
2. Para ver qualquer uma dessas soluções, clique em seu bloco na página Visão geral.![configurar solução](./media/log-analytics-add-solutions/configure-additional.png)
3. Em seguida, configure a solução com as informações necessárias e clique em **Salvar**.![configurar solução](./media/log-analytics-add-solutions/configure.png)

### Para remover uma solução usando a Galeria de Soluções

1. Na página Visão geral do OMS, clique no bloco **Configurações**.
2. Na página Configurações, sob a guia Soluções, clique em **Remover** para a solução que deseja remover.
3. No diálogo de confirmação, clique em **Sim** para remover a solução.

## Detalhes da coleta de dados para recursos e soluções do OMS

A tabela a seguir mostra os métodos de coleta de dados e outros detalhes sobre como os dados são coletados para as soluções e os recursos do OMS.

|tipo de dados| plataforma | Agente direto | Agente SCOM | Armazenamento do Azure | SCOM necessário? | Dados do agente SCOM enviados por meio do grupo de gerenciamento | frequência de coleta |
|---|---|---|---|---|---|---|---|
|Avaliação do AD|Windows|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|	7 dias|
|Status de replicação do AD|Windows|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 dias|
|Alertas (Nagios)|Linux|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|na chegada|
|Alertas (Zabbix)|Linux|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|1 minuto|
|Alertas (Operations Manager)|Windows|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|3 minutos|
|Antimalware|Windows|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)| por hora|
|Gerenciamento de Capacidade|Windows|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)| por hora|
|Controle de Alterações|Windows|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)| por hora|
|Controle de Alterações|Linux|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|por hora|
|Avaliação de configuração (Supervisor herdado)|Windows|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)| duas vezes por dia|
|ETW|Windows|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 minutos|
|Logs IIS|Windows|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 minutos|
|Grupos de segurança de rede|Windows|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|10 minutos|
|Office 365|Windows|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|após a notificação|
|Contadores de desempenho|Windows|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|conforme agendado, mínimo de 10 segundos|
|Contadores de desempenho|Linux|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|conforme agendado, mínimo de 10 segundos|
|Service Fabric|Windows|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 minutos|
|Avaliação do SQL|Windows|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|	7 dias|
|SurfaceHub|Windows|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|na chegada|
|Syslog|Linux|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|do armazenamento do Azure: 10 minutos; do agente: na chegada|
|Atualizações do Sistema|Windows|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)| pelo menos, 2 vezes por dia e 15 minutos após a instalação de uma atualização|
|Logs de eventos de segurança do Windows|Windows|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)| para o armazenamento do Azure: 10 min; para o agente: na chegada|
|Logs do firewall do Windows|Windows|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)| na chegada|
|Logs de eventos do Windows|Windows|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)| para o armazenamento do Azure: 1 min; para o agente: na chegada|
|Dados durante a transmissão|Windows (2012 R2/8.1 ou posterior)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sim](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Não](./media/log-analytics-add-solutions/oms-bullet-red.png)| a cada minuto|


## Próximas etapas

- [Pesquisar logs](log-analytics-log-searches.md) para exibir informações detalhadas coletadas pelas soluções.

<!---HONumber=AcomDC_0504_2016-->