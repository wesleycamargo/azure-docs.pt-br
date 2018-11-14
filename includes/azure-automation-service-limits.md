---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: automation
author: georgewallace
ms.service: automation
ms.topic: include
ms.date: 04/05/2018
ms.author: gwallace
ms.custom: include file
ms.openlocfilehash: abe7668ed16ddd5cc1b247207d3e80f7c1118137
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50964519"
---
| Recurso | Limite máximo |Observações|
| --- | --- |---|
| Número máximo de novos trabalhos que podem ser enviados a cada 30 segundos por conta de automação (trabalhos não agendados) |100 |Quando esse limite é atingido, as solicitações subsequentes para criar um trabalho falham. O cliente recebe uma resposta de erro.|
| Número máximo de trabalhos simultâneos em execução na mesma instância de tempo por conta de automação (trabalhos não agendados) |200 |Quando esse limite é atingido, as solicitações subsequentes para criar um trabalho falham. O cliente recebe uma resposta de erro.|
| Número máximo de módulos que podem ser importados por conta de automação a cada 30 segundos |5 ||
| Tamanho máximo de um módulo |100 MB ||
| Tempo de execução do trabalho - camada gratuita |500 minutos por assinatura por mês ||
| Quantidade máxima de espaço em disco permitido por área restrita**<sup>1</sup>** |1 GB |Aplica-se apenas a áreas de segurança do Azure|
| Quantidade máxima de memória fornecida a uma área restrita **<sup>1</sup>** |400 MB |Aplica-se apenas a áreas de segurança do Azure|
| Número máximo de soquetes de rede permitido por área restrita **<sup>1</sup>** |1000 |Aplica-se apenas a áreas de segurança do Azure|
| Tempo de execução máximo permitido por runbook **<sup>1</sup>** |3 horas |Aplica-se apenas a áreas de segurança do Azure|
| Número máximo de Contas de Automação em uma assinatura |Sem limite ||
|Número máximo de trabalhos simultâneos que serão executados em um único Hybrid Runbook Worker|50 ||
| Tamanho dos parâmetros de trabalo do Max Runbook   | 512 kb||
| Parâmetros de Max Runbook   | 50|Você pode passar uma cadeia de caracteres JSON ou XML para um parâmetro e analisá-lo com o runbook se atingir o limite parâmetro 50|
| Tamanho máximo de carga de webhook |  512 kb|
| Máximo de dias que os dados de trabalho são retidos|30 dias|

**<sup>1</sup>**  Uma área restrita é um ambiente compartilhado que pode ser usado por vários trabalhos, trabalhos usando a mesma área restrita são associados pelas limitações de recursos da área de segurança.
