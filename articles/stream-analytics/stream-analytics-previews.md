---
title: Recursos de visualização do Azure Stream Analytics
description: Este artigo lista os recursos do Azure Stream Analytics que estão atualmente na visualização.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/05/2018
ms.openlocfilehash: 77c761ce72449f91cf91c66219ebc815d1620e94
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49343667"
---
# <a name="azure-stream-analytics-preview-features"></a>Recursos de visualização do Azure Stream Analytics

Este artigo resume todos os recursos atualmente em visualização do Azure Stream Analytics. O uso de recursos de visualização em um ambiente de produção não é recomendado.

## <a name="public-previews"></a>Visualizações públicas

Os seguintes recursos estão em pré-visualização pública. Você pode aproveitar esses recursos hoje, mas não os use em seu ambiente de produção.

### <a name="azure-stream-analytics-on-iot-edge"></a>O Stream Analytics do Azure no IoT Edge

O Azure Stream Analytics no IoT Edge permite que os desenvolvedores implantem análises quase em tempo real em dispositivos IoT Edge. Para obter mais informações, consulte a documentação do [Azure Stream Analytics na IoT Edge](stream-analytics-edge.md).

### <a name="integration-with-azure-machine-learning"></a>Integração com o Azure Machine Learning

Você pode dimensionar os trabalhos do Stream Analytics com as funções de Aprendizado de Máquina (ML). Para saber mais sobre como usar as funções do ML em seu trabalho do Stream Analytics, visite [Dimensionar seu trabalho do Stream Analytics com as funções do Azure Machine Learning](stream-analytics-scale-with-machine-learning-functions.md). Confira um cenário do mundo real com [Realizando análise de sentimentos usando o Azure Stream Analytics e o Aprendizado de Máquina do Azure](stream-analytics-machine-learning-integration-tutorial.md).

### <a name="session-windows"></a>Janela de sessão

O Stream Analytics tem suporte nativo para funções de janela, permitindo que os desenvolvedores criem trabalhos de processamento de streaming complexos com o mínimo de esforço. As [janelas de sessão](https://msdn.microsoft.com/azure/stream-analytics/reference/session-window-azure-stream-analytics) agrupam eventos que chegam em momentos semelhantes, filtrando períodos de tempo em que não há dados. Para saber mais sobre as funções de janelas, visite [Introdução às funções de janelas do Stream Analytics](stream-analytics-window-functions.md).

### <a name="blob-output-partitioning-by-custom-time"></a>Saída de blob, o particionamento por meio de tempo personalizado

O Azure Stream Analytics pode gerar saída para o armazenamento do Blob com base em atributos de horário personalizados. Para obter mais informações, visite [Padrões de caminho personalizados de DateTime para a saída de armazenamento de blobs do Azure Stream Analytics](stream-analytics-custom-path-patterns-blob-storage-output.md).

### <a name="javascript-user-defined-aggregate"></a>Agregado definido pelo usuário de JavaScript

O Azure Stream Analytics oferece suporte a agregados definidos pelo usuário (UDA) escritos em JavaScript, que permitem implementar lógica de negócios com informações de estado complexas. Aprenda a usar UDAs da documentação [agregados definidos pelo usuário do JavaScript do Azure Stream Analytics](stream-analytics-javascript-user-defined-aggregates.md). 

### <a name="live-data-testing-in-visual-studio"></a>Em tempo real de dados de teste no Visual Studio

As ferramentas do Visual Studio para o Azure Stream Analytics aprimoram o recurso de teste local que permite testar suas consultas em fluxos de eventos ao vivo de fontes de nuvem, como Hub de Eventos ou Hub de IoT. Saiba como [Testar dados ao vivo localmente usando as ferramentas do Azure Stream Analytics para o Visual Studio](stream-analytics-live-data-local-testing.md).

### <a name="net-user-defined-functions-on-iot-edge"></a>Funções definidas pelo usuário .NET no IoT Edge

Com as funções definidas pelo usuário padrão do .NET, você pode executar o código do .NET Standard como parte de seu fluxo de pipeline. Você pode criar classes C# simples ou importar projetos e bibliotecas completos. Experiência completa de autoria e depuração é suportada no Visual Studio. Para obter mais informações, visite [Desenvolver funções definidas pelo usuário do .NET Standard para tarefas do Edge Analytics Broadcast do Azure](stream-analytics-edge-csharp-udf-methods.md).

## <a name="private-previews"></a>Versões prévias privadas

Os seguintes recursos estão em versão prévia privada. Para acessar essas visualizações, visite a página de [inscrição particular](https://aka.ms/ASApreview1) da visualização do Azure Stream Analytics

### <a name="anomaly-detection"></a>Detecção de anomalias

O Azure Stream Analytics apresenta novos modelos de aprendizado de máquina com suporte para a detecção de *spike* e *dips*, além da detecção de tendências negativas bidirecionais, lentas positivas e lentas.

### <a name="c-custom-deserializer-for-azure-stream-analytics-on-iot-edge"></a>Deserializador personalizado do C# para o Azure Stream Analytics no IoT Edge

Os desenvolvedores agora podem implementar deserializadores personalizados em C# para desserializar os eventos recebidos pelo Azure Stream Analytics. Exemplos de formatos que podem ser desserializados incluem Parquet, Protobuf, XML ou qualquer formato binário.

### <a name="blob-output-partitioning-by-custom-attribute"></a>O particionamento por meio do atributo personalizado de saída de blob

Agora é possível particionar sua saída do Azure Stream Analytics para armazenamento de Blob com base em qualquer coluna em sua consulta.

### <a name="managed-identities-for-azure-resources-authentication-to-azure-data-lake-storage"></a>Identidades gerenciadas para autenticação de recursos do Azure no Armazenamento do Data Lake do Azure

Agora você pode operacionalizar seus pipelines em tempo real com identidades gerenciadas para autenticação baseada em recursos do Azure enquanto grava no Azure Data Lake Storage Gen1, permitindo que você crie tarefas programaticamente. Para obter mais informações, visite [Use as identidades gerenciadas dos recursos do Azure para autenticar as tarefas do Azure Stream Analytics para a saída Gen1 do armazenamento do Lake Data do Azure](stream-analytics-managed-identities-adls.md).

## <a name="next-steps"></a>Próximas etapas

* [Oito novos recursos no Azure Stream Analytics](https://azure.microsoft.com/blog/eight-new-features-in-azure-stream-analytics/)

* [4 novos recursos agora disponíveis no Azure Stream Analytics](https://azure.microsoft.com/blog/4-new-features-now-available-in-azure-stream-analytics/)
