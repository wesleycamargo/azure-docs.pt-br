---
title: Gerenciar o custo dos dados no Azure Log Analytics | Microsoft Docs
description: Saiba como alterar o plano de preços e gerenciar o volume de dados e a política de retenção para o espaço de trabalho do Log Analytics no Azure.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: magoedte
ms.openlocfilehash: 8fb20fc9e6249a2d19d62df1ce331ce873d5fd3d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2018
---
# <a name="manage-cost-by-controlling-data-volume-and-retention-in-log-analytics"></a>Gerenciar o custo controlando a retenção e o volume de dados no Log Analytics
O Log Analytics foi projetado para dimensionar e fornecer suporte à coleta, indexação e armazenamento de grandes quantidades de dados por dia, a partir de qualquer fonte em sua empresa ou implantado no Azure.  Embora isso possa ser um driver primário para a organização, a eficiência de custo é, em última instância, o driver subjacente. Para isso, é importante reconhecer que o custo de um espaço de trabalho do Log Analytisc não baseia-se apenas no volume de dados coletados, mas também depende do plano selecionado e de quanto tempo você escolheu armazenar os dados gerados a partir de suas fontes conectadas.  

Neste artigo, analisamos como você pode monitorar proativamente o volume de dados e o aumento do armazenamento e definir limites para controlar esses custos associados. 

O custo dos dados pode ser considerável dependendo dos fatores a seguir: 

- Número de sistemas, componentes de infraestrutura, recursos de nuvem, etc. dos quais você coleta 
- Tipo de dados criados pela origem, como filas de mensagens, logs, eventos, dados relacionados à segurança ou métricas de desempenho 
- Volume dos dados gerados por essas fontes e ingeridos no espaço de trabalho 
- Os dados do período são retidos no espaço de trabalho  
- Número de soluções de gerenciamento habilitadas, fonte de dados e frequência de coleta 

> [!NOTE]
> Consulte a documentação de cada solução, pois ela fornece uma estimativa da quantidade de dados coletados.   

Se você estiver no plano *Gratuito*, os dados estarão limitados a 7 dias de retenção. Para a camada *Autônoma* ou *Paga*, os dados coletados estarão disponíveis para os últimos 31 dias. O plano *Gratuito* tem um limite diário de ingestão de 500 MB e, se você perceber que excede consistentemente o volume permitido, poderá alterar o espaço de trabalho para um plano pago para coletar dados além desse limite. 

> [!NOTE]
> Encargos se aplicam se você optar por selecionar um período de retenção mais longo para a camada paga. Você pode alterar seu tipo de plano a qualquer momento e, para obter mais informações sobre preços, consulte [detalhes de preços](https://azure.microsoft.com/pricing/details/log-analytics/). 

Há duas maneiras pelas quais o volume de dados pode ser limitado e ajudar a controlar o custo, ou seja, limite diário e retenção de dados.  

## <a name="review-estimated-cost"></a>Analisar custo estimado
O Log Analytics facilita reconhecer quais são os custos com base nos padrões de uso recentes.  Para fazer isso, execute as seguintes etapas.  

1. Faça logon no [Portal do Azure](http://portal.azure.com). 
2. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Log Analytics**.<br><br> ![Portal do Azure](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
3. No painel de assinaturas do Log Analytics, selecione seu espaço de trabalho e clique em  **Uso e custos estimados** do painel esquerdo.<br><br> ![Página Uso e custos estimados](media/log-analytics-manage-cost-storage/usage-estimated-cost-dashboard-01.png)<br>

A partir daqui, é possível analisar o volume de dados para o mês. Isso inclui todos os dados recebidos e retidos no espaço de trabalho do Log Analytics.  Clique em **Detalhes de uso** na parte superior da página para exibir o painel de uso com informações sobre tendências de volume de dados por fonte, computadores e oferta. Para exibir e definir um limite diário ou modificar o período de retenção, clique em **Gerenciamento de volume de dados**.
 
Os encargos do Log Analytics são adicionadas à sua fatura do Azure. É possível ver os detalhes da fatura do Azure na seção Cobrança do Portal do Azure ou no [Portal de Cobrança do Azure](https://account.windowsazure.com/Subscriptions).  

## <a name="daily-cap"></a>Limite diário
Ao criar um espaço de trabalho do Log Analytics no Portal do Azure e escolher o plano *Gratuito*, ele é definido como um limite de 500 MB por dia. Não há limite para os outros planos de preços. É possível configurar um limite diário e limitar a ingestão diária para o espaço de trabalho, mas seja cuidadoso, pois sua meta não deve ser atingir o limite diário.  Caso contrário, nesse ponto, você perderá dados pelo restante do dia e a capacidade de observar as condições de integridade dos recursos que fornecem suporte aos serviços de TI será afetada.  O limite diário destina-se a ser usado como uma maneira de gerenciar o aumento inesperado no volume de dados dos recursos gerenciados e permanecer dentro do limite, ou quando você quiser simplesmente limitar encargos não planejados para o espaço de trabalho.  

Quando o limite diário é alcançado, a coleta de tipos de dados faturáveis é interrompida pelo restante do dia.  Uma faixa de aviso aparece na parte superior da página do espaço de trabalho do Log Analytics selecionado e um evento de operação é enviado para a tabela *Operação* na categoria **LogManagement**. A coleta de dados é retomada após o tempo de redefinição definido em *O limite diário será definido em*. É recomendável definir uma regra de alerta com base nesse evento de operação, configurada para notificar quando o limite de dados diários for alcançado. 

### <a name="identify-what-daily-data-limit-to-define"></a>Identificar o limite diário de dados a definir 
Analise [Uso do Log Analytics e custos estimados](log-analytics-usage.md) para reconhecer a tendência de ingestão de dados e qual é o limite diário de volume a ser definido. Considere-o com cuidado, pois não será possível monitorar os recursos depois que o limite for alcançado. 

### <a name="manage-the-maximum-daily-data-volume"></a>Gerenciar o volume de dados diário máximo 
As etapas a seguir descrevem como configurar um limite para gerenciar o volume de dados que o Log Analytics ingerirá por dia.  

1. No espaço de trabalho, selecione **Uso e custos estimados** no painel esquerdo.
2. Na página **Uso e custos estimados** para o espaço de trabalho selecionado, clique em **Gerenciamento de volume de dados** na parte superior da página. 
5. O limite diário é **OFF** por padrão – clique em **ON** para habilitá-lo e defina o limite de volume de dados em GB dia.<br><br> ![Configuração do limite de dados do Log Analytics](media/log-analytics-manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-limit-reached"></a>Alerta quando o limite é alcançado
Embora uma indicação visual seja apresentada no Portal do Azure quando o limite de dados é alcançado, esse comportamento não alinha-se necessariamente à maneira como você gerencia problemas operacionais que exigem atenção imediata.  Para receber uma notificação de alerta, é possível criar uma nova regra de alerta no Azure Monitor.  Para saber mais, consulte [como criar, exibir e gerenciar alertas](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).      

Para começar, aqui estão as configurações recomendadas para o alerta:

* Destino: selecione o recurso do Log Analytics
* Critérios: 
   * Nome do sinal: pesquisa de registro personalizada
   * Consulta de pesquisa: operação | onde o Detalhe tem 'OverQuota'
   * Baseado em: número de resultados
   * Condição: maior do que
   * Limite: 0
   * Período: 5 (minutos)
   * Frequência: 5 (minutos)
* Nome da regra de alerta: limite de dados diários alcançado
* Gravidade: aviso (Grav 1)

Quando o alerta é definido e o limite é alcançado, um alerta é disparado e executa a resposta definida no Grupo de Ações. Ele pode notificar a equipe por email e mensagens de texto, ou automatizar ações usando webhooks, runbooks de Automação ou [integrando com uma solução ITSM externa](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts). 

## <a name="change-the-data-retention-period"></a>Alterar o período de retenção de dados 
As etapas a seguir descrevem como configurar por quanto tempo os dados de log são mantidos no espaço de trabalho.
 
1. No espaço de trabalho, selecione **Uso e custos estimados** no painel esquerdo.
2. Na página **Uso e estimativa de custos**, clique em **Gerenciamento de volume de dados** na parte superior da página.
5. No painel, mova o controle deslizante para aumentar ou diminuir o número de dias e, em seguida, clique em **Salvar**.  Se você usar a camada *Gratuita*, não será possível modificar o período de retenção de dados, sendo necessário atualizar para a camada paga para controlar essa configuração.<br><br> ![Alterar a configuração de retenção de dados do espaço de trabalho](media/log-analytics-manage-cost-storage/manage-cost-change-retention-01.png)

## <a name="troubleshooting"></a>solução de problemas
**Pergunta**: como fazer para solucionar problemas se o Log Analytics não estiver mais coletando dados? 
**Resposta**:  se você estiver no tipo de preço gratuito e tiver enviado mais de 500 MB de dados em um dia, a coleta de dados será interrompida pelo restante do dia. Alcançar o limite diário é um motivo comum para o Log Analytics parar de coletar dados ou para dados parecerem estar ausentes.  
O Log Analytics cria um evento de tipo Operação quando a coleta de dados inicia e para.  
Execute a consulta a seguir na pesquisa para verificar se está alcançando o limite diário e os dados ausentes: Operação | onde OperationCategory == 'Data Collection Status'   
Quando a coleta de dados é interrompida, o OperationStatus torna-se Aviso. Quando a coleta de dados inicia, o OperationStatus é Com êxito.  
A tabela a seguir descreve os motivos pelos quais a coleta de dados é interrompida e uma ação é sugerida para retomar a coleta de dados:  

|Motivo para a interrupção da coleta| Solução| 
|-----------------------|---------|
|Limite diário de dados gratuito atingido<sup>1</sup>|Aguarde até o dia seguinte para que a coleta seja reiniciada automaticamente ou altere para um tipo de preço pago.|
|Limite diário definido no gerenciamento de volume de dados alcançado|Aguarde até o dia seguinte para a coleta ser reiniciada automaticamente ou aumente o limite diário de volume de dados descrito em [gerenciar o volume de dados diário máximo](#manage-the-maximum-daily-volume)|
|Assinatura do Azure está em um estado suspenso devido a:<br> A avaliação gratuita terminou<br> O Azure Pass expirou<br> Limite de gastos mensal atingido (por exemplo, em uma assinatura do MSDN ou do Visual Studio)|Converter para uma assinatura paga<br> Remova o limite ou espere o limite ser redefinido|

<sup>1</sup> Se o seu espaço de trabalho estiver no tipo de preço gratuito, você estará limitado a enviar 500 MB de dados por dia para o serviço. Ao alcançar o limite diário, a coleta de dados será interrompida até o próximo dia. Dados enviados enquanto a coleta de dados está interrompida não são indexados e não ficam disponíveis para pesquisa. Quando a coleta de dados é retomada, o processamento ocorre apenas para novos dados enviados. 

O Log Analytics usa o horário UTC. O tempo de redefinição varia entre os espaços de trabalho para evitar que todos os espaços de trabalho limitados iniciem a ingestão de dados ao mesmo tempo. Se o espaço de trabalho alcançar o limite diário, o processamento será retomado após o tempo de redefinição definido em **O limite diário será definido em**.<br><br> ![Fuso horário UTC de limite do Log Analytics](media/log-analytics-manage-cost-storage/data-volume-mgmt-limit-utc.png)

**Pergunta**: como posso ser notificado quando a coleta de dados é interrompida? 
**Resposta**: use as etapas descritas no alerta *Criar um limite de dados diário* para ser notificado quando a coleta de dados parar e siga as etapas em Usar as etapas descritas em adicionar ações para regras de alerta configurar uma ação de runbook, webhook ou email para a regra de alerta. 

## <a name="next-steps"></a>Próximas etapas  

Para determinar a quantidade de dados coletada, quais fontes estão enviando os dados e os diferentes tipos de dados enviados para ajudar a gerenciar o consumo e os custos, consulte [Analisar o uso de dados no Log Analytics](log-analytics-usage.md).