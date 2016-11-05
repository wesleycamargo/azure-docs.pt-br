---
title: Caso de uso - Criação de perfil de cliente
description: Saiba como o Azure Data Factory é usado para criar um fluxo de trabalho orientado a dados (pipeline) para criar o perfil de clientes de jogos.
services: data-factory
documentationcenter: ''
author: spelluru
manager: jhubbard
editor: monicar

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: spelluru

---
# Caso de uso - Criação de perfil de cliente
O Azure Data Factory é um dos muitos serviços usados para implementar o Cortana Intelligence Suite de aceleradores de solução. Para obter mais informações sobre o Cortana Intelligence, visite [Cortana Intelligence Suite](http://www.microsoft.com/cortanaanalytics). Neste documento, descrevemos um caso de uso simples para ajudá-lo a começar a entender como o Azure Data Factory pode resolver problemas comuns de análise.

Tudo o que você precisa para acessar e testar este caso de uso simples é uma [assinatura do Azure](https://azure.microsoft.com/pricing/free-trial/). Você pode implantar um exemplo que implementa esse caso de uso, seguindo as etapas descritas no artigo [Exemplos](data-factory-samples.md).

## Cenário
A Contoso é uma empresa de jogos que cria os jogos para várias plataformas: PCs (computadores pessoais), dispositivos de mão e consoles de jogos. À medida que os usuários jogam, grandes volumes de dados de log são produzidos, controlando os padrões de uso, estilo de jogos e preferências do usuário. Quando combinados com dados demográficos, regionais e dados de produto, a Contoso pode executar análises para orientá-los sobre como aprimorar a experiência dos jogadores e direcioná-los para atualizações e compras de jogos.

O objetivo da Contoso é identificar oportunidades de venda/venda cruzada com base no histórico de jogos de seus jogadores e adicionar novos recursos atraentes para gerar o crescimento dos negócios e proporcionar uma melhor experiência para os clientes. Para esse caso, usamos uma empresa de jogos como um exemplo de uma empresa. A empresa deseja otimizar seus jogos com base no comportamento dos jogadores. Esses princípios se aplicam a qualquer empresa que deseje atrair seus clientes para seus produtos e serviços e aprimorar a experiência dos clientes.

## Desafios
## Visão geral da solução
Este caso de uso simples pode ser usado como um exemplo de como você pode usar o Azure Data Factory para ingestão, preparação, transformação, análise e publicação dos dados.

![Fluxos de trabalho completos](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

Esta figura mostra como os pipelines de dados são exibidos no Portal do Azure após terem sido implantados.

1. O **PartitionGameLogsPipeline** lê os eventos brutos de jogos de um armazenamento de blob e cria partições com base no ano, mês e dia.
2. O **EnrichGameLogsPipeline** junta eventos de jogos particionados com dados de referência de código geográfico e enriquece os dados pelo mapeamento de endereços IP para os locais geográficos correspondentes.
3. O pipeline **AnalyzeMarketingCampaignPipeline** usa dados enriquecidos e processa-os com os dados de anúncio para criar a saída final, que contém a eficácia de uma campanha de marketing.

Neste exemplo, o Data Factory é usado para coordenar as atividades que copiam dados de entrada, transformam e processam os dados e fazem a saída de dados final para um Banco de Dados SQL do Azure. Você também pode visualizar a rede de pipelines de dados, gerenciá-los e monitorar o status da interface do usuário.

## Benefícios
Ao otimizar suas análises de perfil do usuário e o alinhamento com as metas de negócios, a empresa de jogos é capaz coletar rapidamente os padrões de uso e analisar a eficácia de suas campanhas de marketing.

<!---HONumber=AcomDC_0907_2016-->