---
title: Escolha o tipo de preço para Azure Mapas | Microsoft Docs
description: Saiba mais sobre tipo de preço oferecido pelo Azure Mapas
author: walsehgal
ms.author: v-musehg
ms.date: 01/02/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 028a227eefb319d19c9274082626a007bd9161de
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2019
ms.locfileid: "53993953"
---
# <a name="choosing-the-right-pricing-tier-in-azure-maps"></a>Escolha o certo tipo de preço no Azure Mapas

Os Mapas do Azure oferecem dois tipos de preço. A finalidade deste artigo é ajudá-lo a escolher o melhor preço para suas necessidades. Para ajudar a escolher o melhor tipo de preço, faça duas perguntas:

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Quais recursos geoespaciais planejo usar?
Se você achar que seus requisitos de serviço são atendidos pelas APIs geoespaciais, então o tipo de preço S0 é ideal para você. Se você quiser recursos mais avançados para seu aplicativo, como imagens aéreas+híbridas, obtendo intervalo de rota, lote geocodificação, etc., considere a possibilidade de aceitar para o tipo de preço S1. A tabela a seguir com **recursos do tipo de preços** irá fornecer uma ideia melhor de necessidades do seu aplicativo e também ajudará você a escolher um tipo de preço mais adequado para seu aplicativo.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Quantos usuários simultâneos planejam oferecer suporte? 
Tipo de preço S0 e S1 pode lidar com quantidades diferentes de taxa de transferência de dados. Antes de escolher um tipo de preço do Azure Mapas, considere perguntando a si mesmo perguntas como quantos usuários simultâneos você deseja oferecer suporte? O tipo de preço S0 pode manipular até **50 consultas por segundo** e o S1 pode lidar com a camada de preços **mais de 50 consultas por segundo**.

### <a name="pricing-tier-capabilities"></a>Recursos do tipo de preço

| Recurso                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Search                                  |        ✓           |     ✓    |
| Roteamento                                 |        ✓           |     ✓    |
| Renderizar                                  |        ✓           |     ✓    |
| Tráfego                                 |        ✓           |     ✓    |
| Fusos horários                              |        ✓           |     ✓    |
| *Imagens + imagens híbridas (visualização)     |        ✓           |     ✓    |
| *Intervalo de rota (visualização)                  |        ✓           |     ✓    |
| *Localização IP 2 (versão prévia)                |        ✓           |     ✓    |
| *Polígonos de pesquisa (versão prévia)         |        ✓           |     ✓    |
| *Geocodificação de lote (versão prévia)              |        ✓           |     ✓    |
| *Roteamento de lote (versão prévia)                |        ✓           |     ✓    |
| *Roteamento de lote (versão prévia)               |        ✓           |     ✓    |

> [!Note]
> Acesso esses recursos a partir da camada de preço S0 será preterido após 4 de fevereiro de 2019.

Alguns pontos de dados adicionais que valem a pena considerar são, que tipo de empresa você tem ou quão crítico é o aplicativo que está sendo criado?

Consulte a tabela com **Clientes de destino do tipo de preço** para obter uma melhor noção dos tipos de preço S1 e S0. Para obter mais informações sobre os preços de Azure Mapas, consulte [Preço do Azure Mapas](https://azure.microsoft.com/pricing/details/azure-maps/). 

### <a name="pricing-tier-targeted-customers"></a>Tipo de preço de clientes de destino

| Tipo de preço  |        Clientes de destino                                                                |
|---------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>O tipo de preço S0 é para clientes que são as empresas de pequeno ou médio porte. É um direito de tipo de preço para você se você não espera grandes volumes de usuários simultâneos e seus requisitos de serviço são atendidos pelas APIs geoespaciais conforme indicado pela tabela acima. Essa camada está disponível e é aplicável para aplicativos em todos os estágios de produção desde o desenvolvimento da prova de conceito e teste da etapa para a produção e implantação do aplicativo.<p>|
| S1            |    <p>O tipo de preço S1 é para clientes em necessidade de transporte para empresa de grande porte, aplicativos de missão crítica, volumes altos de usuários concorrentes ou requer serviços geoespaciais avançados.</p>|

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como exibir e alterar o tipo de preço:

> [!div class="nextstepaction"]
> [Gerenciar o Tipo de Preço](how-to-manage-pricing-tier.md)
