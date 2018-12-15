---
title: Tempo de ingestão de dados no Azure Log Analytics | Microsoft Docs
description: Explica os diferentes fatores que afetam a latência na coleta de dados no Azure Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2018
ms.author: bwren
ms.openlocfilehash: d8d8e344ce9ee317a7f864492514162b1dc085f9
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52884316"
---
# <a name="data-ingestion-time-in-log-analytics"></a>Tempo de ingestão de dados no Log Analytics
O Azure Log Analytics é um serviço de dados de alta escala no Azure Monitor que atende milhares de clientes que enviam terabytes de dados por mês em um ritmo cada vez maior. Geralmente, há dúvidas sobre o tempo necessário para que os dados fiquem disponíveis no Log Analytics depois de serem coletados. Este artigo explica os diferentes fatores que afetam essa latência.

## <a name="typical-latency"></a>Latência típica
Latência refere-se ao tempo durante o qual os dados são criados no sistema monitorado e o tempo em que são disponibilizados para análise no Log Analytics. A latência típica de ingestão de dados no Log Analytics está entre 2 e 5 minutos. A latência específica para dados específicos variará conforme uma variedade de fatores explicados abaixo.


## <a name="factors-affecting-latency"></a>Fatores que afetam a latência
O tempo total de ingestão para determinado conjunto de dados pode ser dividido nas áreas de alto nível a seguir. 

- Tempo do agente – o tempo necessário para detectar um evento, coletá-lo e, em seguida, enviá-lo para o ponto de ingestão do Log Analytics como um registro de log. Na maioria dos casos, esse processo é manipulado por um agente.
- Tempo do pipeline – o tempo necessário para o pipeline de ingestão processar o registro de log. Isso inclui a análise das propriedades do evento e a adição potencial de informações calculadas.
- Tempo de indexação – o tempo gasto para ingestão de um registro de log no armazenamento de Big Data do Log Analytics.

Os detalhes sobre a latência diferente introduzida nesse processo são descritos abaixo.

### <a name="agent-collection-latency"></a>Latência da coleta de agente
Os agentes e as soluções de gerenciamento usam estratégias diferentes para coletar dados de uma máquina virtual, o que pode afetar a latência. Alguns exemplos específicos incluem os seguintes:

- Os eventos do Windows, os eventos de syslog e as métricas de desempenho são coletados imediatamente. Os contadores de desempenho do Linux são sondados em intervalos de 30 segundos.
- Os logs do IIS e os logs personalizados são coletados quando seu carimbo de data/hora é alterado. Para os logs do IIS, isso é influenciado pelo [agendamento de sobreposição configurado no IIS](../../azure-monitor/platform/data-sources-iis-logs.md). 
- A solução Replicação do Active Directory realiza sua avaliação a cada cinco dias, enquanto a solução Avaliação do Active Directory realiza uma avaliação semanal da infraestrutura do Active Directory. O agente coletará esses logs somente quando a avaliação for concluída.

### <a name="agent-upload-frequency"></a>Frequência de upload de agente
Para garantir que o agente do Log Analytics seja leve, o agente armazena em buffer os logs e carrega-os periodicamente no Log Analytics. A frequência de upload varia entre 30 segundos e 2 minutos, dependendo do tipo de dados. A maioria dos dados é carregada em menos de 1 minuto. As condições de rede podem afetar negativamente a latência com a qual esses dados são recebidos no ponto de ingestão do Log Analytics.

### <a name="azure-logs-and-metrics"></a>Logs e métricas do Azure 
Os dados de log de atividades levarão cerca de 5 minutos para ficarem disponíveis no Log Analytics. Os dados de métricas e logs de diagnóstico podem levar de 1 a 5 minutos para serem disponibilizados, dependendo do serviço do Azure. Em seguida, serão necessários mais 30 a 60 segundos para os logs e 3 minutos para as métricas para que os dados sejam enviados ao ponto de ingestão do Log Analytics.

### <a name="management-solutions-collection"></a>Coleção de soluções de gerenciamento
Algumas soluções não coletam seus dados de um agente e podem usar um método de coleta que introduz latência adicional. Algumas soluções coletam dados em intervalos regulares sem tentar a coleta quase em tempo real. Exemplos específicos incluem os seguintes:

- A solução do Office 365 sonda os logs de atividades usando a API de Gerenciamento de Atividade do Office 365, que atualmente não fornece nenhuma garantia de latência quase em tempo real.
- Os dados das soluções de Análise do Windows (Conformidade de Atualizações, por exemplo) são coletados pela solução com uma frequência diária.

Veja a documentação de cada solução para determinar sua frequência de coleta.

### <a name="pipeline-process-time"></a>Tempo de processo de pipeline
Depois que os registros de log são ingeridos no pipeline do Log Analytics, eles são gravados em um armazenamento temporário para garantir o isolamento do locatário e garantir que os dados não sejam perdidos. Normalmente, esse processo adiciona 5 a 15 segundos. Algumas soluções de gerenciamento implementam algoritmos mais pesados para agregar dados e obter insights conforme os dados são recebidos. Por exemplo, o Monitoramento de Desempenho de Rede agrega os dados recebidos em intervalos de 3 minutos, efetivamente, adicionando uma latência de 3 minutos. Outro processo que adiciona latência é o processo que lida com logs personalizados. Em alguns casos, esse processo pode adicionar alguns minutos de latência aos logs que são coletados de arquivos pelo agente.

### <a name="new-custom-data-types-provisioning"></a>Provisionamento de novos tipos de dados personalizados
Quando um novo tipo de dados personalizados é criado com base em um [log personalizado](data-sources-custom-logs.md) ou na [API do Coletor de Dados](data-collector-api.md), o sistema cria um contêiner de armazenamento dedicado. Essa é uma sobrecarga única que ocorre apenas na primeira aparência desse tipo de dados.

### <a name="surge-protection"></a>Proteção contra aumento
A principal prioridade do Log Analytics é garantir que nenhum dado do cliente seja perdido e, portanto, o sistema tem uma proteção interna para aumentos de dados. Isso inclui buffers para garantir que, mesmo sob carga imensa, o sistema continue funcionando. Sob carga normal, esses controles adicionam menos de um minuto, mas em condições extremas e falhas, eles podem adicionar tempo significativo, ao mesmo tempo que garantem que os dados estão seguros.

### <a name="indexing-time"></a>Tempo de indexação
Há um equilíbrio interno para cada plataforma de Big Data entre o fornecimento de funcionalidades de análise e pesquisa avançada, em detrimento do fornecimento de acesso imediato aos dados. O Azure Log Analytics permite que você execute consultas avançadas em bilhões de registros e obtenha resultados em alguns segundos. Isso se torna possível porque a infraestrutura transforma os dados drasticamente durante sua ingestão e armazena-os em estruturas compactas exclusivas. O sistema armazena os dados em buffer até que dados suficientes estejam disponíveis para criar essas estruturas. Isso precisa ser concluído antes que o registro de log seja exibido nos resultados da pesquisa.

No momento, esse processo leva cerca de 5 minutos quando há um baixo volume de dados, mas menos tempo em taxas mais altas de dados. Isso parece confuso, mas esse processo permite a otimização de latência para cargas de trabalho de produção de alto volume.



## <a name="checking-ingestion-time"></a>Verificando o tempo de ingestão
Use a tabela **Pulsação** para obter uma estimativa da latência para os dados de agentes. Como a Pulsação é enviada uma vez a cada minuto, a diferença entre a hora atual e o último registro de pulsação estará, de preferência, o mais próximo possível de um minuto.

Use a consulta a seguir para listar os computadores com a latência mais alta.

    Heartbeat 
    | summarize IngestionTime = now() - max(TimeGenerated) by Computer 
    | top 50 by IngestionTime asc

 
Use a consulta a seguir em ambientes grandes para resumir a latência para diferentes percentuais do total de computadores.

    Heartbeat 
    | summarize IngestionTime = now() - max(TimeGenerated) by Computer 
    | summarize percentiles(IngestionTime, 50,95,99)



## <a name="next-steps"></a>Próximas etapas
* Leia o [Contrato de Nível de Serviço (SLA)](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_1/) do Log Analytics.

