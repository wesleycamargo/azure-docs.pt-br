<properties 
   pageTitle="Fontes de dados no Log Analytics | Microsoft Azure"
   description="As fontes de dados definem os dados que o Log Analytics coleta de agentes e outras fontes conectadas. Este artigo descreve o conceito de como o Log Analytics usa fontes de dados, explica os detalhes de como configurá-las e fornece um resumo das diferentes fontes de dados disponíveis."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/02/2016"
   ms.author="bwren" />

# Fontes de dados no Log Analytics

O Log Analytics coleta dados das fontes conectadas em seu espaço de trabalho do OMS e os armazena no repositório do OMS. Os dados que são coletados de cada uma são definidos pelas fontes de dados que você configurar. Os dados no repositório do OMS são armazenados como um conjunto de registros. Cada fonte de dados cria registros de um determinado tipo com cada tipo de tendo seu próprio conjunto de propriedades.

![Coleta de dados do Log Analytics](./media/log-analytics-data-sources/overview.png)

As fontes de dados são diferentes de soluções do OMS, que também coletam dados das fontes conectadas e criam registros no repositório do OMS. As soluções podem ser adicionadas ao espaço de trabalho da Galeria de Soluções e geralmente fornecerão ferramentas de análise adicionais no portal do OMS.

## Resumo das fontes de dados

As fontes de dados que estão disponíveis no momento no Log Analytics são listadas na tabela a seguir. Cada uma tem um link para um artigo à parte que fornece detalhes para aquela fonte de dados.

| Fonte de dados | Tipo de evento | Descrição |
|:--|:--|:--|
| [Logs personalizados](log-analytics-data-sources-custom-logs.md) | <LogName>\_CL | Arquivos de texto em agentes do Windows ou Linux contendo informações de log. | | [Logs de eventos do Windows](log-analytics-data-sources-windows-events.md) | Event | Eventos coletados do log de eventos em computadores com Windows. | | [Contadores de desempenho do Windows](log-analytics-data-sources-performance-counters.md) | Perf | Contadores de desempenho coletados de computadores com Windows. | | [Contadores de desempenho do Linux](log-analytics-data-sources-performance-counters.md) | Perf | Contadores de desempenho coletados de computadores com Linux. | | [Logs do IIS](log-analytics-data-sources-iis-logs.md) | W3CIISLog | Logs dos Serviços de Informações da Internet no formato W3C. | | Syslog | Syslog | Eventos de Syslog em computadores com Windows ou Linux. |

## Configurando fontes de dados

As fontes de dados são configuradas do menu **Dados** nas **Configurações** do Log Analytics. Toda configuração é enviada para todas as fontes conectadas em seu espaço de trabalho do OMS. No momento, você não pode excluir nenhum agente dessa configuração.

![Configurar eventos do Windows](./media/log-analytics-data-sources/configure-events.png)

2. No console do OMS, selecione o bloco **Configurações**.
3. Selecione **Dados**.
4. Clique na fonte de dados para configurar.
5. Siga o link para a documentação para cada fonte de dados na tabela acima para obter detalhes sobre sua configuração.

## Coleta de dados

As configurações de fonte de dados são entregues aos agentes que estão diretamente conectados ao OMS dentro de alguns minutos. Os dados especificados são coletados do agente e entregues diretamente para o Log Analytics em intervalos específicos para cada fonte de dados. Consulte a documentação para cada fonte de dados para encontrar essas especificações.

Para agentes do SCOM (System Center Operations Manager) em um grupo de gerenciamento conectado, as configurações de fonte de dados são convertidas em pacotes de gerenciamento e entregues ao grupo de gerenciamento a cada cinco minutos por padrão. O agente baixa o pacote de gerenciamento como qualquer outro, coleta os dados especificados e os envia para um servidor de gerenciamento que encaminha os dados para o OMS. O agente não requer nenhuma comunicação diretamente com o OMS para qualquer uma das fontes de dados. Você pode ler sobre os detalhes da conexão do SCOM e do OMS e a modificação da frequência com que a configuração é entregue em [Configure Integration with System Center Operations Manager](log-analytics-om-agents.md) (Configurar a integração com o System Center Operations Manager).

## Registros do Log Analytics

Todos os dados coletados pelo Log Analytics são armazenados no repositório do OMS como registros. Registros coletados por diferentes fontes de dados terão seu próprio conjunto de propriedades e serão identificados por sua propriedade **Type**. Consulte a documentação para cada fonte de dados e a solução para obter detalhes sobre cada tipo de registro.


## Próximas etapas

- Saiba mais sobre [soluções](log-analytics-add-solutions.md) que adicionam funcionalidades ao Log Analytics e também coletam dados para o repositório do OMS.
- Saiba mais sobre [pesquisas de log](log-analytics-log-searches.md) para analisar os dados coletados de fontes de dados e soluções.  
- Configure alertas para notificá-lo proativamente sobre dados críticos coletados de fontes de dados e soluções.

<!---HONumber=AcomDC_0504_2016-->