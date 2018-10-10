---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: scheduler
ms.service: scheduler
author: derek1ee
ms.topic: include
ms.date: 08/16/2016
ms.author: deli
ms.custom: include file
ms.openlocfilehash: be52bf289898dfcd3ee669f12329669f6fabd356
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47006458"
---
A tabela a seguir descreve cada uma das principais cotas, limites, padrões e limitações no Agendador do Azure.

| Recurso | Descrição de limite |
| -------- | ----------------- |
| **Tamanho do trabalho** | O tamanho máximo do trabalho é 16K. Se uma operação PUT ou uma PATCH resultar em um tamanho de trabalho maior do que esse limite, um código de status 400 Solicitação incorreta será retornado. | 
| **Coleções de trabalho** | O número máximo de coleções de trabalho por assinatura do Azure é de 200.000. | 
| **Trabalhos por coleção** | Por padrão, o número máximo de trabalhos é de cinco em uma coleção de trabalhos gratuita e 50 trabalhos em uma coleção de trabalhos padrão. <p>É possível alterar o número máximo de trabalhos em uma coleção. Todos os trabalhos na coleção de trabalhos estão limitados ao valor definido na coleção de trabalhos. Se você tentar criar mais trabalhos do que a cota máxima de trabalhos, a solicitação falhará com um código de status 409 Conflito. | 
| **Hora de início da hora** | O "tempo para hora de início" é de 18 meses. |
| **Intervalo de recorrência** | O período máximo de recorrência é de 18 meses. | 
| **Frequência** | Por padrão, a cota máxima de frequência é de uma hora em uma coleção de trabalhos gratuita e de um minuto em uma coleção de trabalhos padrão. <p>É possível realizar a frequência máxima em uma coleção de trabalhos menor do que o máximo. Todos os trabalhos na coleção estão limitados ao valor definido na coleção de trabalhos. Se você tentar criar um trabalho com uma frequência maior do que a máxima na coleção de trabalhos, a solicitação falhará com um código de status 409 Conflito. | 
| **Tamanho do corpo** | O tamanho máximo do corpo para uma solicitação é de 8192 caracteres. |
| **Tamanho da solicitação de URL** | O tamanho máximo de uma URL de solicitação é de 2048 caracteres. |
| **Contagem de cabeçalho** | A contagem máxima de cabeçalhos é de 50 cabeçalhos. | 
| **Tamanho do cabeçalho de agregação** | O tamanho máximo do cabeçalho de agregação é de 4096 caracteres. |
| **Tempo limite** | O tempo limite da solicitação é estático (não configurável) e de 60 segundos para ações de HTTP. Para operações mais demoradas, siga os protocolos assíncronos do HTTP. Por exemplo, retornar um 202 imediatamente, mas continuar funcionando em segundo plano. | 
| **Histórico de trabalho** | O corpo de resposta máximo armazenado no histórico de trabalhos é de 2048 bytes. |
| **Retenção de histórico de trabalho** | O histórico de trabalhos é retido por até dois meses ou até as últimas 1000 execuções. | 
| **Retenção de trabalhos concluídos e com falha** | Os trabalhos concluídos e com falha são retidos por 60 dias. |
||| 

