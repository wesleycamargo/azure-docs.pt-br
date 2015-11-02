<properties 
	pageTitle="Caso de uso do Data Factory - recomendações de produtos" 
	description="Saiba mais sobre um caso de uso implementado usando o Azure Data Factory junto com outros serviços." 
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
	ms.date="10/20/2015" 
	ms.author="spelluru"/>

# Caso de uso - recomendações de produtos 

O Azure Data Factory é um dos muitos serviços usados para implementar o Pacote de Análise do Cortana de aceleradores de solução. Consulte a página [Pacote de Análise do Cortana](http://www.microsoft.com/cortanaanalytics) para obter detalhes sobre este pacote. Neste documento, descrevemos um caso de uso comum que usuários do Azure já resolveram e implementaram usando o Azure Data Factory e outros serviços de componente da Análise do Cortana.

## Cenário

Revendedores online normalmente desejam estimular seus clientes a comprar produtos ao apresentar a esses clientes os produtos nos quais é mais provável que eles estejam interessados e, portanto, que têm mais probabilidade de comprar. Para fazer isso, os revendedores online devem personalizar a experiência online do seu usuário usando recomendações de produtos personalizadas para esse usuário específico, que levam em consideração seus dados atuais e históricos de comportamento em compras, informações de produto, marcas introduzidas recentemente e dados de segmentação de produto e cliente para apresentar o produto mais relevante para o cliente. Além disso, eles podem oferecer ao usuário recomendações de produto do usuário com base na análise do comportamento geral de uso de todos os seus usuários combinados.

O objetivo desses revendedores é otimizar o processo para conversões de clique em venda pelo usuário e aumentar sua receita de vendas. Eles alcançam isso fornecendo recomendações de produtos contextualizadas, baseadas em comportamento, nas ações e nos interesses do cliente. Para esse caso, usamos revendedores online como um exemplo de empresas que desejam otimizar as vendas para seus clientes, mas esses princípios se aplicam a qualquer empresa que deseje atrair seus clientes com seus produtos e serviços e aprimorar a experiência de compra dos clientes com recomendações de produtos personalizadas.

## Desafios

Existem muitos desafios enfrentados por revendedores online ao tentar implementar esse tipo de caso de uso. Primeiro, dados de formas e tamanhos diferentes devem ser incluídos de várias fontes de dados, tanto locais quanto na nuvem, para capturar dados de produto, dados de histórico de comportamento do cliente e dados de usuário conforme o usuário pesquisa o site de varejo online.

Segundo, recomendações de produtos personalizadas devem ser calculadas e previstas de modo razoável e preciso. Além de produto, marca e os dados de comportamento e de navegador, os revendedores online também precisam incluir comentários fornecidos por outros clientes em compras passadas do produto para influenciar a determinação das melhores recomendações de produto para um usuário.

Em terceiro lugar, as recomendações devem poder ser entregues imediatamente ao usuário para fornecer uma experiência perfeita de navegação e compra, além de fornecer as recomendações mais recentes e relevantes. Por fim, os revendedores devem avaliar a eficiência de sua abordagem rastreando as medidas de venda bem-sucedidas de conversão de clique, tanto em vendas suplementares quanto em vendas cruzadas; então, devem fazer ajustes em suas recomendações futuras.

## Visão geral da solução

Esse exemplo de caso de uso foi resolvido e implementado por usuários reais do Azure com o Azure Data Factory e outros serviços de componente de Análise do Cortana, inclusive [HDInsight](http://azure.microsoft.com/services/hdinsight/) e [Power BI](https://powerbi.microsoft.com/), para incluir, preparar, transformar, analisar e publicar os dados finais.

O revendedor online usa um Armazenamento de Blob do Azure, um SQL Server local, um Banco de Dados SQL do Azure e um data mart para dados relacionais como suas opções de armazenamento de dados durante o fluxo de trabalho. O armazenamento de blob contém dados de informações de produto, dados de comportamento do cliente e informações do produto. Os dados de informações de produto incluem informações sobre a marca do produto e um catálogo de produtos armazenados localmente em um SQL data warehouse.

Conforme ilustrado na figura a seguir, todos os dados são combinados alimentados em um sistema de recomendação de produtos para fornecer recomendações personalizadas com base em ações e os interesses do cliente, enquanto o usuário procura produtos no catálogo existente no site do revendedor. O cliente também vê produtos similares que podem estar relacionados ao produto está examinando com base nos padrões de uso geral do site, que não estão relacionadas a nenhum usuário específico

![diagrama de casos de uso](./media/data-factory-product-reco-usecase/diagram-1.png)

Gigabytes de arquivos de log da Web brutos são gerados diariamente no site do revendedor online como arquivos semiestruturados. Os arquivos de log da Web brutos e as informações de catálogo do cliente e de produto são incluídos regularmente em uma conta de Armazenamento de Blob do Azure, usando a movimentação de dados implantada globalmente do Data Factory como um serviço. Os arquivos de log brutos do dia são particionados (por ano e mês) no armazenamento de blob para armazenamento a longo prazo. O [Azure HDInsight](http://azure.microsoft.com/services/hdinsight/)(Hadoop como um serviço) é usado para particionar os arquivos de log brutos (para melhor capacidade de gerenciamento, disponibilidade e desempenho) no armazenamento de blob e processar os logs incluídos em escala, usando tanto scripts Hive como Pig. Os dados de logs da Web particionados são processados em seguida, para extrair as entradas necessárias para uma sistema de recomendação de aprendizado de máquina para gerar as recomendações personalizadas de produto.

O sistema de recomendação usado para o aprendizado de máquina neste exemplo é uma plataforma de recomendação de aprendizado de máquina de software livre do [Apache Mahout](http://mahout.apache.org/). Observe que qualquer [aprendizado de máquina do Azure](http://azure.microsoft.com/services/machine-learning/) ou modelo personalizado pode ser aplicado. O modelo Mahout é usado para prever a semelhança entre os itens no site do revendedor com base nos padrões gerais de uso e para gerar as recomendações personalizadas com base no usuário individual.

Por fim, o conjunto de resultados de recomendações de produtos personalizadas é movido para um data mart para dados relacionais para consumo pelo site do revendedor. O conjunto de resultados também pode ser acessado diretamente do armazenamento de blob por outro aplicativo ou movido para repositórios adicionais para outros consumidores e casos de uso.

## Benefícios

Ao otimizar sua estratégia de recomendação de produtos e alinhá-la com as metas de negócios, a solução atendeu aos objetivos de marketing e gerenciamento de mercadorias do revendedor online. Além disso, ele foi capaz de operacionalizar e gerenciar o fluxo de trabalho de recomendação de produtos de maneira eficiente, confiável e econômica, tornando mais fácil para eles atualizar seus modelos e ajustar sua eficácia com base em medidas de vendas bem-sucedidas de conversão de clique. Usando o Azure Data Factory, eles conseguiram abandonar seu gerenciamento de recursos de nuvem manual dispendioso e demorado e passar a usar o gerenciamento de recursos de nuvem sob demanda, poupando tempo, dinheiro e reduzindo o tempo que eles precisam para implantação de soluções. Tornou-se fácil visualizar e solucionar problemas nas exibições de linhagem de dados e integridade do serviço operacional com a interface de usuário intuitiva de monitoramento e gerenciamento do Data Factory, disponível no Portal do Azure. Sua solução agora pode ser agendada e gerenciada de modo que os dados concluídos sejam produzidos e entregues aos usuários de maneira confiável e que os dados em dependências de processamento sejam gerenciados automaticamente, sem intervenção humana.

Ao fornecer essa experiência personalizada de compra, o revendedor online criou uma experiência de cliente mais competitiva, atraente e, consequentemente, haverá aumento da satisfação geral e das vendas.



  

<!---HONumber=Oct15_HO4-->