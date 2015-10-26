<properties 
	pageTitle="Caso de uso - Criação de perfil de cliente" 
	description="Saiba como o Azure Data Factory é usado para criar um fluxo de trabalho orientado a dados (pipeline) para criar o perfil de clientes de jogos." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/26/2015" 
	ms.author="spelluru"/>

# Caso de uso - Criação de perfil de cliente

O Azure Data Factory é um dos muitos serviços usados para implementar o Pacote de Análise do Cortana de aceleradores de solução. Para obter mais informações sobre Análise do Cortana, visite [Pacote de Análise do Cortana](http://www.microsoft.com/cortanaanalytics). Neste documento, descrevemos um caso de uso simples para ajudá-lo a começar a entender como o Azure Data Factory pode resolver problemas comuns de análise.

Tudo o que você precisa para acessar e testar este caso de uso simples é uma [assinatura do Azure](https://azure.microsoft.com/pricing/free-trial/). Você pode implantar um exemplo que implementa esse caso de uso, seguindo as etapas descritas no artigo [Exemplos](data-factory-samples.md).

## Cenário

A Contoso é uma empresa de jogos que cria os jogos para várias plataformas: PCs (computadores pessoais), dispositivos de mão e consoles de jogos. À medida que os usuários jogam, grandes volumes de dados de log são produzidos, controlando os padrões de uso, estilo de jogos e preferências do usuário. Quando combinados com dados demográficos, regionais e dados de produto, a Contoso pode executar análises para orientá-los sobre como aprimorar a experiência de cada usuário e direcioná-los para atualizações e compras de jogos.

Objetivo da Contoso é identificar a venda e oportunidades de venda cruzada com base no perfil de histórico de jogos de seus usuários e desenvolver novos recursos atraentes para gerar o crescimento dos negócios e proporcionar uma melhor experiência para os clientes. Para esse caso, usamos uma empresa de jogos como um exemplo de empresa que deseja otimizar as vendas para seus clientes, mas esses princípios se aplicam a qualquer empresa que deseje atrair seus clientes com seus produtos e serviços e aprimorar a experiência de compra dos clientes.

## Desafios

Existem muitos desafios enfrentados por empresas de jogos ao tentar implementar esse tipo de caso de uso. Primeiro, dados de formas e tamanhos diferentes devem ser incluídos de várias fontes de dados, tanto locais quanto na nuvem, para capturar dados de produto, dados de histórico de comportamento do cliente e dados de usuário conforme o usuário joga nas múltiplas plataformas. Em segundo lugar, padrões de uso de jogo devem ser razoavelmente e precisamente calculados. Por fim, as empresas de jogos devem avaliar a eficiência de sua abordagem rastreando as medidas de venda bem-sucedidas de jogos, tanto em vendas suplementares quanto em vendas cruzadas; então, devem fazer ajustes em suas recomendações futuras.

## Visão geral da solução

Este caso de uso simples pode ser usado como um exemplo de como você pode usar o Azure Data Factory para ingestão, preparação, transformação, análise e publicação dos dados.

![Fluxos de trabalho completos](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png) A figura acima mostra como os pipelines de dados são exibidos na interface do usuário do portal do Azure após terem sido implantados.

1.	O **PartitionGameLogsPipeline** lê os eventos brutos de jogos de um armazenamento de blob e cria partições com base no ano, mês e dia.
2.	O **EnrichGameLogsPipeline** junta eventos de jogos particionados com dados de referência de código geográfico e enriquece os dados pelo mapeamento de endereços IP para os locais geográficos correspondentes.
3.	O pipeline **AnalyzeMarketingCampaignPipeline** aproveita os dados enriquecidos e processa-os com os dados de anúncio para criar a saída final, que contém a eficácia de uma campanha de marketing.

Neste caso de uso de exemplo, o Azure Data Factory é usado para coordenar as atividades que copiam dados de entrada, transformam e processam os dados com atividades de HDInsight (transformações de Hive e Pig) e de saída de dados final para um Banco de Dados SQL do Azure. Você também pode visualizar a rede de pipelines de dados, gerenciá-los e monitorar o status da interface do usuário.

## Benefícios

Ao otimizar suas análises de perfil do usuário e o alinhamento com as metas de negócios, a empresa de jogos é capaz coletar rapidamente os padrões de uso e analisar a eficácia de suas campanhas de marketing para todos os seus produtos de jogos diferentes.

<!---HONumber=Oct15_HO3-->