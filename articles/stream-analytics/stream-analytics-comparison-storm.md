---
title: "Plataformas de análise: comparação do Apache Storm com o Stream Analytics | Microsoft Docs"
description: "Obtenha diretrizes ao escolher uma plataforma de análise de nuvem, usando a comparação entre o Apache Storm e o Stream Analytics. Compreender os recursos e diferenças."
keywords: "plataforma de análise, plataformas de análise, plataforma de análise de nuvem, comparação com o storm"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: b9aac017-9866-4d0a-b98f-6f03881e9339
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/24/2017
ms.author: jeffstok
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 5b5aa75756d2a7061d531d50a59a2c52dfb75cbe
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---
# <a name="help-choosing-a-streaming-analytics-platform-apache-storm-comparison-to-azure-stream-analytics"></a>Ajudar a escolher uma plataforma de análise de streaming: comparação do Apache Storm com o Stream Analytics do Azure
Obtenha diretrizes ao escolher uma plataforma de análise de nuvem, usando a comparação entre o Apache Storm e o Stream Analytics do Azure. Compreenda as propostas de valor do Stream Analytics versus o Apache Storm como um serviço gerenciado no Azure HDInsight, para que você possa escolher a solução certa para os casos de uso da sua empresa.

As duas plataformas de análise fornecem benefícios de uma solução PaaS, mas há alguns recursos importantes que as diferenciam. Os recursos, bem como as limitações desses serviços, estão listados abaixo para ajudar você a chegar à solução de que precisa para alcançar seus objetivos.

## <a name="storm-comparison-to-stream-analytics-general-features"></a>Comparação com o Stream Analytics: recursos gerais

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Stream Analytics do Azure</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm no HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Software livre</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Não, o Azure Stream Analytics é uma oferta de propriedade da Microsoft.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Sim, Apache Storm é uma tecnologia licenciada pela Apache.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Com suporte da Microsoft</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Sim </p>
            </td>
            <td width="246" valign="top">
                <p>
Sim </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Requisitos de hardware</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Não há requisitos de hardware. Azure Stream Analytics é um serviço do Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Não há requisitos de hardware. Apache tempestade é um serviço do Azure.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Unidade de nível superior</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Com o Azure Stream Analytics, os clientes implantam e monitoram trabalhos de streaming.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Com o Apache Storm no HDInsight, os clientes implantam e monitoram um cluster inteiro, que pode hospedar vários trabalhos do Storm, bem como outras cargas de trabalho (lote inclusive).
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Preço</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
O preço do Stream Analytics é calculado pelo volume de dados processados e o número de unidades de streaming (por hora de execução do trabalho) necessário.
                </p>
                <p>
                    <a href="http://azure.microsoft.com/pricing/details/stream-analytics/">Mais informações sobre preços podem ser encontradas aqui.</a>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Para Apache Storm no HDInsight, a unidade de compra é baseada em cluster e é cobrada com base no tempo de execução do cluster, independentemente dos trabalhos implantados.
                </p>
                <p>
                    <a href="http://azure.microsoft.com/pricing/details/hdinsight/">Mais informações sobre preços podem ser encontradas aqui.</a>
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="authoring-on-each-analytics-platform"></a>Criação em cada plataforma de análise ##

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Stream Analytics do Azure</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm no HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Funcionalidades: SQL DSL</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Sim, um suporte fácil de usar de liguagem SQL está disponível.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Não, os usuários devem escrever código em Java C# ou usar APIs do Trident.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Recursos: operadores temporais</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Agregações em janelas e junções temporais têm suporte imediato.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Operadores temporais devem ser implementados pelo usuário.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Experiência de desenvolvimento</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Experiência de criação e depuração interativa no Portal do Azure em dados de exemplo.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
A experiência de desenvolvimento, depuração e monitoramento é fornecida por meio da experiência do Visual Studio a usuários do .NET, ao passo que para Java e outras linguagens, os desenvolvedores devem usar o IDE de sua preferência.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Suporte para depuração</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
O Stream Analytics oferece status de trabalho básico e logs de operações como uma forma de depuração, mas atualmente não oferece flexibilidade sobre o quê ou o quanto é incluído no log, ou seja, o modo detalhado.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Logs detalhados estão disponíveis para fins de depuração. Há duas maneiras de exibir logs ao usuário, por meio do Visual Studio ou o usuário pode aplicar RDP ao cluster para acessar os logs.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Suporte para UDF (Funções Definidas pelo Usuário)</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Atualmente não há nenhum suporte para UDFs.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
UDFs podem ser escritos em linguagem C#, Java ou outra de sua escolha.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Extensível - código personalizado </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Não há nenhum suporte para código extensível no Stream Analytics.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Sim, há disponibilidade para escrever código personalizado em C#, Java ou outras linguagens com suporte no Storm.
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="data-sources-and-outputs"></a>Saídas e fontes de dados ##

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Stream Analytics do Azure</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm no HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                 <strong>Fontes de dados de entrada</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>As fontes de entrada com suporte são hubs de eventos do Azure e blobs do Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Há conectores para hubs de eventos, barramento de serviço, Kafka, etc. Conectores sem suporte podem ser implementados via código personalizado.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Formatos de dados de entrada</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Os formatos de entrada com suporte são Avro, JSON, CSV.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Qualquer formato pode ser implementado por meio de código personalizado.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Saídas</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Um trabalho de streaming pode ter várias saídas. Saídas com suporte: hubs de eventos do Azure, armazenamento de blobs do Azure, tabelas do Azure, banco de dados SQL Azure e PowerBI.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Suporte para várias saídas em uma topologia, cada saída pode ter uma lógica personalizada para processamento downstream. Pronto para uso, o Storm inclui conectores para o Power BI, Hubs de Eventos do Azure, Armazenamento de Blobs do Azure, Azure Cosmos DB, SQL e HBase. Conectores sem suporte podem ser implementados via código personalizado.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Formatos de codificação de dados</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
O Stream Analytics requer que o formato dos dados UTF-8 seja utilizado.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Qualquer formato de codificação de dados pode ser implementado por meio de código personalizado.
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="management-and-operations"></a>Gerenciamento e operações ##

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Stream Analytics do Azure</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm no HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Modelo de implantação de trabalho</strong>
                </p>
                <p>
                    - <strong>Portal do Azure</strong>
                </p>
                <p>
                    - <strong>Visual Studio</strong>
                </p>
                <p>
                    - <strong>PowerShell</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
A implantação é feita pelo Portal do Azure, PowerShell e APIs REST.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
A implantação é feita pelo Portal do Azure, PowerShell, Visual Studio e APIs REST.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Monitoramento (estatísticas, contadores, etc.)</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
O monitoramento é feito pelo Portal do Azure e APIs REST.
                </p>
                <p>
O usuário também pode configurar alertas do Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
O monitoramento é feito por IUs do Storm e APIs REST.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Escalabilidade</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Número de unidades de streaming para cada trabalho. Cada unidade de streaming processa até 1 MB/s. Máximo de 50 unidades por padrão. Chamada para aumentar o limite.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Número de nós no cluster do HDI Storm. Nenhum limite no número de nós (limite superior definido por sua cota do Azure). Chamada para aumentar o limite.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Limites de processamento de dados</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Os usuários podem escalar ou reduzir verticalmente o número de unidades de streaming para aumentar o processamento de dados ou otimizar custos.
                </p>
                <p>
Escalar verticalmente até 1 GB/s </p>
            </td>
            <td width="246" valign="top">
                <p>
O usuário pode escalar ou reduzir verticalmente o tamanho do cluster para atender às necessidades.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Parar/Retomar</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Interromper e retomar no último lugar interrompido.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Interromper e retomar no último local parado com base na marca-d'água.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Atualização de serviço e estrutura</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Aplicação de patch automáticas sem tempo de inatividade.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Aplicação de patch automáticas sem tempo de inatividade.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Continuidade de negócios por meio de um serviço altamente disponível com garantia de SLA</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
SLA de 99,9% de tempo de atividade </p>
                <p>
Recuperação automática de falhas </p>
                <p>
A recuperação de operadores temporais com monitoração de estado é interna.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
SLA de 99,9% de tempo de atividade de cluster do Storm. Apache Storm é uma plataforma de streaming tolerante a falhas, mas é responsabilidade do cliente garantir que seus trabalhos de streaming sejam executados ininterruptamente.
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="advanced-features"></a>Recursos avançados ##

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Stream Analytics do Azure</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm no HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Chegada tardia e manipulação de eventos fora de ordem</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Políticas internas configuráveis para reordenar, descartar eventos ou ajustar a hora do evento.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
O usuário deve implementar a lógica para manipular esse cenário.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Dados de referência</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Dados de referência disponíveis de blobs do Azure com o tamanho máximo de 100 MB de cache de pesquisa na memória. A atualização dos dados de referência é gerenciada pelo serviço.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Sem limites de tamanho dos dados. Conectores disponíveis para HBase, Azure Cosmos DB, SQL Server e Azure. Conectores sem suporte podem ser implementados via código personalizado.
                </p>
                <p>
A atualização dos dados de referência deve ser tratada pelo código personalizado.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Integração ao Machine Learning</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Com a configuração de modelos de Machine Learning do Azure publicados como funções durante a criação do trabalho do ASA <a href="http://blogs.msdn.com/b/streamanalytics/archive/2015/05/24/real-time-scoring-of-streaming-data-using-machine-learning-models.aspx">(visualização privada)</a>.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Disponível por meio de bolts do Storm.
                </p>
            </td>
        </tr>
    </tbody>
</table>

