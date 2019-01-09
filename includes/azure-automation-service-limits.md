---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: automation
author: georgewallace
ms.service: automation
ms.topic: include
ms.date: 12/13/2018
ms.author: gwallace
ms.custom: include file
ms.openlocfilehash: f3ae2289112948dea7d2649c4fad6b1cafb3804b
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2018
ms.locfileid: "53443965"
---
#### <a name="process-automation"></a>Automação de processos

| Recurso | Limite máximo |Observações|
| --- | --- |---|
| Número máximo de novos trabalhos que podem ser enviados a cada 30 segundos por conta de automação (trabalhos não agendados) |100 |Quando esse limite é atingido, as solicitações subsequentes para criar um trabalho falham. O cliente recebe uma resposta de erro.|
| Número máximo de trabalhos simultâneos em execução na mesma instância de tempo por conta de automação (trabalhos não agendados) |200 |Quando esse limite é atingido, as solicitações subsequentes para criar um trabalho falham. O cliente recebe uma resposta de erro.|
| Tamanho máximo de armazenamento de metadados de trabalho para um período de 30 dias sem interrupção. | 10GB (aproximadamente 4 milhões trabalhos)|Quando esse limite é atingido, as solicitações subsequentes para criar um trabalho falham. |
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
| Tamanho do estado do fluxo de trabalho do Max PowerShell |5 MB| Aplica-se aos runbooks de fluxo de trabalho do PowerShell no fluxo de trabalho de ponto de verificação.|

**<sup>1</sup>**  Uma área restrita é um ambiente compartilhado que pode ser usado por vários trabalhos, trabalhos usando a mesma área restrita são associados pelas limitações de recursos da área de segurança.

#### <a name="change-tracking-and-inventory"></a>Controle de Alterações e Inventário

A tabela a seguir mostra os limites de item controlados por máquina para Controle de Alterações.

| **Recurso** | **Limite**| **Observações** |
|---|---|---|
|Arquivo|500||
|Registro|250||
|Software do Windows|250|Não inclui as atualizações de software|
|Pacotes do Linux|1250||
|Serviços|250||
|Daemon|250||
