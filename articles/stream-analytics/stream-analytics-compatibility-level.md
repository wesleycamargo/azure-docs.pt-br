---
title: "Compreender o nível de compatibilidade para trabalhos do Azure Stream Analytics. | Microsoft Docs"
description: "Saiba como definir um nível de compatibilidade para um trabalho do Azure Stream Analytics e as principais alterações no nível de compatibilidade mais recente"
keywords: "Nível de compatibilidade, fluxo de dados"
documentationcenter: 
services: stream-analytics
author: SnehaGunda
manager: kfile
editor: 
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/03/2018
ms.author: sngun
ms.openlocfilehash: f354c39fc3b366795fe4ed8dbeeb961bb11d5420
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Nível de compatibilidade para trabalhos do Azure Stream Analytics
 
Nível de compatibilidade refere-se aos comportamentos específicos à versão de um serviço do Azure Stream Analytics. O Azure Stream Analytics é um serviço gerenciado, com atualizações de recursos e melhorias de desempenho regulares. Geralmente as atualizações são disponibilizadas automaticamente para os usuários finais. No entanto, alguns recursos novos podem apresentar alterações importantes, tal como alteração no comportamento de um trabalho existente, alterar os processos que consomem dados desses trabalhos, entre outros. Um nível de compatibilidade é usado para representar uma alteração importante introduzida no Stream Analytics. As principais alterações sempre são introduzidas com um novo nível de compatibilidade. 

O nível de compatibilidade garante que os trabalhos existentes sejam executados sem falhas. Quando você cria um novo trabalho do Stream Analytics, é uma prática recomendada criá-lo usando o nível de compatibilidade mais recente que está disponível para você. 
 
## <a name="set-a-compatibility-level"></a>Definir um nível de compatibilidade 

O nível de compatibilidade controla o comportamento de tempo de execução de um trabalho do Stream Analytics. Você pode definir o nível de compatibilidade de um trabalho do Stream Analytics usando o portal ou a [criar a chamada à API REST de trabalho](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job). O Azure Stream Analytics é compatível atualmente com dois níveis de compatibilidade “1.0” e “1.1”. Por padrão, o nível de compatibilidade é definido como “1.0”, que foi introduzido durante a disponibilidade geral do Azure Stream Analytics. Para atualizar o valor padrão, navegue até o trabalho de análise do Stream Analytics > selecione a opção **Nível de compatibilidade** na seção **Configurar** e altere o valor. 

Pare o trabalho antes de atualizar o nível de compatibilidade. Não será possível atualizar o nível de compatibilidade se o trabalho estiver em um estado de execução. 

![Nível de compatibilidade no portal](media\stream-analytics-compatibility-level/image1.png)

 
Quando você atualizar o nível de compatibilidade, o compilador do T-SQL valida o trabalho com a sintaxe que corresponde ao nível de compatibilidade selecionado. 

## <a name="major-changes-in-the-latest-compatibility-level-11"></a>Principais alterações no nível de compatibilidade mais recente (1.1)

As alterações principais a seguir são apresentadas no nível de compatibilidade 1.1:

* **Formato XML do Barramento de Serviço**  

  * **versões anteriores:** o Azure Stream Analytics usava o DataContractSerializer, por isso o conteúdo da mensagem incluía marcas XML. Por exemplo: 
    
   @\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ “SensorId”:”1”, “Temperature”:64\}\u0001 

  * **versão atual:** o conteúdo da mensagem contém o fluxo diretamente sem marcas adicionais. Por exemplo: 
  
   { “SensorId”:”1”, “Temperature”:64} 
 
* **Diferenciação de maiúsculas e minúsculas persistente para nomes de campos**  

  * **versões anteriores:** nomes de campos foram alterados para letras minúsculas quando processados pelo mecanismo do Azure Stream Analytics. 

  * **versão atual:** a diferenciação entre maiúsculas e minúsculas são mantidos para nomes de campos quando eles são processados pelo mecanismo do Azure Stream Analytics. 

  > [!NOTE] 
  > Diferenciação de maiúsculas e minúsculas persistente ainda não está disponível para trabalhos de Análise de Fluxo hospedados usando o ambiente do Edge. Como resultado, todos os nomes de campo são convertidos em minúsculas se o trabalho estiver hospedado no Edge. 

* **FloatNaNDeserializationDisabled**  

  * **versões anteriores:** o comando CREATE TABLE não filtra eventos com NaN (não é um número. Por exemplo, Infinity, -Infinity) em um tipo de coluna FLOAT porque eles estão fora do intervalo documentado para esses números.

  * **versão atual:** CREATE TABLE permite especificar um esquema de alta segurança. O mecanismo do Stream Analytics valida que os dados estão em conformidade com este esquema. Com esse modelo, o comando pode filtrar eventos com valores NaN. 

* **Desabilite a elevação automática para cadeias de caracteres datetime em JSON.**  

  * **versões anteriores:** o analisador JSON realiza upcast automaticamente os valores de cadeia de caracteres com informações de data/hora/fuso horário para o tipo DateTime e, em seguida, converte-os em UTC. Isso resulta na perda das informações de fuso horário.

  * **versão atual:** não há mais nenhum upcast automático de valores de cadeia de caracteres com informações de data/hora/fuso horário para o tipo DateTime. Dessa forma, as informações de fuso horário são mantidas. 

## <a name="next-steps"></a>Próximas etapas
* [Guia de solução de problemas do Azure Stream Analytics](stream-analytics-troubleshooting-guide.md)
* [Folha do Resource Health do Stream Analytics](stream-analytics-resource-health.md)