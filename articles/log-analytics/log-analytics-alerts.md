<properties 
   pageTitle="Alertas no Log Analytics | Microsoft Azure"
   description="Alertas no Log Analytics identificam informações importantes em seu repositório do OMS e podem notificar proativamente problemas ou invocar ações para tentar corrigi-los.  Este artigo descreve como criar uma regra de alerta e detalha as diferentes ações que elas podem executar."
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
   ms.date="08/22/2016"
   ms.author="bwren" />


# <a name="alerts-in-log-analytics"></a>Alertas no Log Analytics

Alertas no Log Analytics identificam informações importante no seu repositório do OMS.  Regras de Alerta executarão pesquisas de log automaticamente de acordo com um agendamento e criarão um registro de alerta se os resultados corresponderem a critérios específicos.  A regra pode então executar automaticamente uma ou mais ações para notificar você proativamente do alerta ou invocar outro processo.   

![Alertas do Log Analytics](media/log-analytics-alerts/overview.png)

## <a name="creating-an-alert-rule"></a>Criar uma regra de alerta
Para criar uma regra de alerta, você começa criando uma pesquisa de log para os registros que devem invocar o alerta.  O botão **alerta** estará disponível para que você possa criar e configurar a regra de alerta.

1.  Na página de Visão Geral do OMS, clique em **Pesquisa de Log**.
2.  Crie uma nova consulta de pesquisa de log ou selecione uma pesquisa de log salva. 
3.  Clique em **Alerta** na parte superior da página para abrir a tela **Adicionar Regra de Alerta**.
4. Consulte as tabelas abaixo para obter detalhes sobre as opções para configurar o alerta.
5. Quando você fornece a janela de tempo para a regra de alerta, o número de registros existentes que correspondem aos critérios de pesquisa para a janela de tempo será exibido.  Isso pode ajudar a determinar a frequência com que o número de resultados que você espera será fornecido.
4.  Clique em **Salvar** para concluir a regra de alerta.  Ela começa a ser executada imediatamente.


![Adicionar Regra de Alerta](media/log-analytics-alerts/add-alert-rule.png)

| Propriedade | Descrição |
|:--|:--|
| **Informações de alerta** | |
| Nome |  Nome exclusivo para identificar a regra de alerta. |
| Severidade | Severidade do alerta que é criado por essa regra. |
| Consulta de pesquisa | Selecione **Usar a consulta de pesquisa atual** para usar a consulta atual ou escolher uma pesquisa salva existente da lista.  A sintaxe da consulta é fornecida na caixa de texto, na qual você pode modificá-la, se necessário.  |
| Janela de tempo | Especifica o intervalo de tempo para a consulta.  A consulta retorna somente os registros que foram criados dentro desse intervalo de tempo atual.  Este pode ser qualquer valor entre 5 minutos e 24 horas.  Ele deve ser maior que ou igual à frequência de alerta.  <br><br>  Por exemplo, se a janela de tempo está definida para 60 minutos e a consulta é executada às 13:15h, somente os registros criados entre 12:15h e 13:15h serão retornados. |
| **Agenda** |     
| Limite | Critérios para quando criar um alerta.  Um alerta será criado se o número de registros retornados pela consulta corresponderem a esses critérios. |
| Frequência de alerta | Especifica a frequência com que a consulta deve ser executada.  Pode ser qualquer valor entre 5 minutos e 24 horas.  Deve ser igual a ou menor que a janela de tempo. |
| Suprimir alertas | Quando você ativa a supressão da regra de alerta, as ações da regra são desabilitadas por um período definido depois de criar um novo alerta.  A regra ainda estará em execução e criará registros de alerta se os critérios forem atendidos.  Isso serve para permitir que você tenha tempo de corrigir o problema sem executar ações duplicadas. |
| **Ações** | |
| Notificação por email | Especifique **Sim** se desejar que um email seja enviado quando o alerta for disparado. |
| Assunto    | Assunto do email.  Não é possível modificar o corpo do email. |
| Destinatários | Endereços de todos os destinatários de email.  Se você especificar mais de um endereço, separe os endereços com ponto e vírgula (;). |
| webhook | Especifique **Sim** se desejar chamar um webhook quando o alerta for disparado. |
| URL de Webhook | A URL do webhook. |
| Incluir a carga JSON personalizada | Selecione esta opção se você deseja substituir a carga padrão por uma carga personalizada. |
| Insira sua carga JSON personalizada | A carga personalizada para o webhook.  Consulte a seção anterior para ver os detalhes. |
| Runbook | Especifique **Sim** se desejar iniciar um runbook da Automação do Azure quando o alerta for disparado. |
| Selecionar um runbook | Selecione um runbook para ser iniciado dentre os runbooks na conta de automação configurados na sua solução de Automação. |
| Executar em | Selecione **Azure** para executar o runbook na nuvem do Azure.  Selecione **Hybrid Worker** para executar o runbook um [Hybrid Runbook Worker](..\automation\automation-hybrid-runbook-worker.md) no seu ambiente local. |


## <a name="manage-alert-rules"></a>Gerenciar regras de alerta
Você pode obter uma lista de todas as regras de alerta no menu **Alertas** nas Configurações do **Log Analytics**.  

![Gerenciar alertas](./media/log-analytics-alerts/configure.png)

1. No console do OMS, selecione o bloco **Configurações** .
2. Selecione **Alertas**.

Você pode executar várias ações nessa exibição.

- Desabilite uma regra selecionando **Desativar** ao lado dela.
- Edite uma regra de alerta clicando no ícone de lápis ao lado dela.
- Remova uma regra de alerta clicando no ícone de **X** ao lado dela. 


## <a name="setting-time-windows"></a>Configurar janelas de tempo 

### <a name="event-alerts"></a>Alertas de eventos

Eventos incluem fontes de dados como logs de eventos do Windows, Syslog e logs personalizados.  Pode ser útil criar um alerta quando um evento de erro específico é criado ou quando vários eventos de erros são criados dentro de uma janela de tempo específica.

Para alertar quanto a um único evento, defina o número de resultados para maior que 0 e tanto a frequência quanto a janela de tempo para 5 minutos.  Isso vai executar a consulta a cada 5 minutos e verificar a ocorrência de um único evento que foi criado desde a última vez em que a consulta foi executada.  Uma frequência maior pode atrasar o tempo entre o evento que está sendo coletado e o alerta criado.

Alguns aplicativos podem registrar um erro ocasional que não necessariamente gerará um alerta.  Por exemplo, o aplicativo pode repetir o processo que criou o evento de erro e depois ter êxito na próxima vez.  Nesse caso, não convém criar o alerta, a menos que vários eventos sejam criados dentro de uma janela de tempo específica.  

Em alguns casos, pode ser útil criar um alerta na ausência de um evento.  Por exemplo, um processo pode registrar eventos regulares para indicar que ele está funcionando corretamente.  Se ele não registrar um desses eventos dentro de uma janela de tempo específica, um alerta deverá ser criado.  Nesse caso você definiria o limite para *Menos de 1*.

### <a name="performance-alerts"></a>Alertas de desempenho

[Os dados de desempenho](log-analytics-data-sources-performance-counters.md) são armazenado como registros no repositório do OMS de forma semelhante aos eventos.  O valor de cada registro é a média medida nos 30 minutos anteriores.  Se você deseja alertar quando um contador de desempenho exceder um limite específico, esse limite deve ser incluído na consulta.

Por exemplo, digamos que você queira alertar quando o processador fica em execução de mais de 90% por 30 minutos, para isso você usaria uma consulta como *Type=Perf ObjectName=Processor CounterName="% Processor Time" CounterValue>90* e o limite para a regra de alerta *maior que 0*.  

 Como [registros de desempenho](log-analytics-data-sources-performance-counters.md) são agregados a cada 30 minutos independentemente da frequência de coleta de cada contador, uma janela de tempo menor do que 30 minutos pode não retornar nenhum registro.  Definir a janela de tempo para 30 minutos garantirá que você obtenha um único registro para cada fonte conectada que representa a média desse período.

## <a name="alert-actions"></a>Ações de alerta

Além de criar um registro de alerta, você pode configurar a regra de alerta para executar automaticamente uma ou mais ações.  Ações podem notificar você proativamente do alerta ou invocar algum processo que tenta corrigir o problema que foi detectado.  As seções a seguir descrevem as ações que estão disponíveis no momento.

### <a name="email-actions"></a>Ações de email
Ações de email enviam um email com os detalhes do alerta para um ou mais destinatários.  Você pode especificar o assunto do email, mas seu conteúdo é um formato padrão criado pelo Log Analytics.  Ele inclui informações de resumo, como o nome do alerta, além dos detalhes de até dez registros retornados pela pesquisa de log.  Ele também inclui um link para uma pesquisa de log no Log Analytics que retornará todo o conjunto de registros da consulta.   O remetente do email é *Microsoft Operations Management Suite Team (Equipe do Microsoft Operations Management Suite) &lt;noreply@oms.microsoft.com&gt;*. 


### <a name="webhook-actions"></a>Ações de Webhook

Ações de Webhook permitem invocar um processo externo por meio de uma única solicitação HTTP POST.  O serviço que está sendo chamado deve dar suporte a webhooks e determinar como ele usará as cargas recebidas.  Você também pode chamar uma API REST que não dá suporte a webhooks especificamente desde que a solicitação esteja em um formato que a API compreende.  Exemplos de como usar um webhook em resposta a um alerta são usar um serviço como o [Slack](http://slack.com) para enviar uma mensagem com os detalhes do alerta ou criar um incidente em um serviço como o [PagerDuty](http://pagerduty.com/).  

Um passo a passo completo de como criar uma regra de alerta com um webhook para chamar um serviço de exemplo está disponível em [Webhooks em alertas do Log Analytics](log-analytics-alerts-webhooks.md).

Webhooks incluem uma URL e uma carga formatada em JSON, que são os dados enviados para um serviço externo.  Por padrão, a carga incluirá os valores na tabela a seguir.  Você pode optar por substituir essa carga por uma personalizado de sua preferência.  Nesse caso, você pode usar as variáveis da tabela para cada um dos parâmetros para incluir seus valores na carga personalizada.


| Parâmetro | Variável | Descrição |
|:--|:--|:--|
| AlertRuleName | #AlertRuleName | Nome da regra de alerta. |
| AlertThresholdOperator | #thresholdoperator | O operador de limite da regra de alerta.  *Greater than (Maior que)* ou *Less than (Menor que)*. |
| AlertThresholdValue | #thresholdvalue | O valor de limite para a regra de alerta. |
| LinkToSearchResults | #LinkToSearchResults | Vincular a pesquisa de log do Log Analytics que retorna os registros da consulta que criou o alerta. |
| ResultCount  | #searchresultcount | Número de registros nos resultados da pesquisa. |
| SearchIntervalEndtimeUtc  | #SearchIntervalEndtimeUtc | Hora de término da consulta no formato UTC. |
| SearchIntervalInSeconds | #searchinterval | A janela de tempo para a regra de alerta. |
| SearchIntervalStartTimeUtc  | #SearchIntervalStartTimeUtc | Hora de início da consulta no formato UTC. |
| SearchQuery | #SearchQuery | A consulta da pesquisa de log usada pela regra de alerta. |
| SearchResults | Veja abaixo | Registros retornados pela consulta em formato JSON.  Limitado aos primeiros 5.000 registros. |
| WorkspaceID | #WorkspaceID | ID do seu espaço de trabalho do OMS. |


Por exemplo, você pode especificar a seguinte carga personalizada que inclui um único parâmetro chamado *text*.  O serviço chamado por esse webhook seria esperaria receber esse parâmetro.

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }

Essa carga de exemplo seria resolvida como algo semelhante ao mostrado a seguir quando enviado para o webhook.

    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }

Para incluir os resultados da pesquisa em uma carga personalizada, adicione a seguinte linha como uma propriedade de nível superior na carga json.  

    "IncludeSearchResults":true

Por exemplo, para criar uma carga personalizada que inclui apenas o nome do alerta e os resultados da pesquisa, você poderia usar o seguinte. 

    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }


Veja o passo a passo de um exemplo completo de como criar uma regra de alerta com um webhook para iniciar um serviço externo em [Exemplo de webhook de alerta do Log Analytics](log-analytics-alerts-webhooks.md).

### <a name="runbook-actions"></a>Ações de runbook

Ações de runbook iniciam um runbook na Automação do Azure.  Para usar esse tipo de ação, você deve ter a [solução de Automação](log-analytics-add-solutions.md) instalada e configurada em seu espaço de trabalho do OMS.  Se você não a tiver instalado, ao criar uma nova regra de alerta, será exibido um link para sua instalação.  Você pode selecionar os runbooks na conta de automação configurados na solução de Automação.

Ações de runbook iniciam o runbook usando um [webhook](../automation/automation-webhooks.md).  Ao criar a regra de alerta, ela criará automaticamente um novo webhook para o runbook com o nome **OMS Alert Remediation (Remediação de Alerta do OMS)** seguido por um GUID.  

Você não pode preencher diretamente quaisquer parâmetros de runbook, mas o [parâmetro $WebhookData](../automation/automation-webhooks.md) incluirá os detalhes do alerta, incluindo os resultados da pesquisa de log que o criou.  Será necessário definir o runbook **$WebhookData** como um parâmetro para que possa acessar as propriedades do alerta.  Os dados de alerta estão disponíveis no formato json em uma única propriedade chamada **SearchResults** na propriedade **RequestBody** do **$WebhookData**.  Isso está relacionado com as propriedades na tabela abaixo.


| Nó | Descrição |
|:--|:--|
| ID         | Caminho e GUID da pesquisa. |
| __metadata | Informações sobre o alerta, incluindo o número de registros e o status dos resultados da pesquisa. |
|  value     |  Entrada separada para cada registro nos resultados da pesquisa.  Os detalhes da entrada serão compatíveis com as propriedades e os valores do registro.   |

Por exemplo, o seguinte runbook deve extrair os registros retornados pela pesquisa de log e atribuir propriedades diferentes com base no tipo de cada registro.  Observe que o runbook é iniciado com a conversão de **RequestBody** do json para que possa ser editado como um objeto no PowerShell.

    param ( 
        [object]$WebhookData
    )

    $RequestBody = ConvertFrom-JSON -InputObject $WebhookData.RequestBody
    $Records     = $RequestBody.SearchResults.value
    
    foreach ($Record in $Records)
    {
        $Computer = $Record.Computer
        
        if ($Record.Type -eq 'Event')
        {
            $EventNo    = $Record.EventID
            $EventLevel = $Record.EventLevelName
            $EventData  = $Record.EventData
        }
        
        if ($Record.Type -eq 'Perf')
        {
            $Object    = $Record.ObjectName
            $Counter   = $Record.CounterName
            $Instance  = $Record.InstanceName
            $Value     = $Record.CounterValue
        }
    }


## <a name="alert-records"></a>Registros de alerta

Registros de alerta criados por regras de alerta no Log Analytics têm um **Type** que indica **Alert** e um **SourceSystem** que indica **OMS**.  Eles têm as propriedades indicadas na tabela a seguir.

| Propriedade | Descrição |
|:--|:--|
| Tipo          | *Alerta* |
| SourceSystem  | *OMS* |
| AlertSeverity | Nível de severidade do alerta. |
| AlertName     | Nome do alerta. |
| Consultar         | Texto da consulta que foi executada.  |
| QueryExecutionEndTime   | Término do intervalo de tempo para a consulta. |
| QueryExecutionStartTime | Início do intervalo de tempo para a consulta.  |
| TimeGenerated | Data e hora em que o alerta foi criado. |

Há outros tipos de registros de alerta criados pela [solução de Gerenciamento de Alertas](log-analytics-solution-alert-management.md) e por [exportações do Power BI](log-analytics-powerbi.md).  Eles têm um **Type** que indica **Alert**, mas são diferenciados por seus **SourceSystem**.




## <a name="next-steps"></a>Próximas etapas

- Instale a [solução de Gerenciamento de Alertas](log-analytics-solution-alert-management.md) para analisar os alertas criados no Log Analytics junto com aqueles coletados do SCOM (System Center Operations Manager).
- Leia mais sobre [pesquisas de log](log-analytics-log-searches.md) que podem gerar alertas.
- Conclua um passo a passo para [configurar um webhook](log-analytics-alerts-webhooks.md) com uma regra de alerta.  
- Aprenda a criar [runbooks na Automação do Azure](https://azure.microsoft.com/documentation/services/automation) para corrigir os problemas identificados por alertas.


<!--HONumber=Oct16_HO2-->


