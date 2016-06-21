<properties
	pageTitle="Escolher uma camada ou SKU para a Pesquisa do Azure | Microsoft Azure"
	description="A Pesquisa do Azure pode ser provisionada nestes SKUs: Gratuito, Básico e Standard, sendo que o Standard está disponível em várias configurações de recursos e níveis de capacidade."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="paulettm"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="06/05/2016"
	ms.author="heidist"/>

# Escolher uma camada ou SKU para a Pesquisa do Azure

Antes de poder [criar um serviço de pesquisa](search-create-service-portal.md) na Pesquisa do Azure, você precisará saber qual camada ou SKU no qual provisionar o serviço. Os SKUs incluem: Gratuito, Básico ou Standard, sendo que Standard está disponível em várias configurações de recursos e capacidades.

A capacidade e os custos de execução do serviço andam de lado a lado. As informações neste artigo podem ajudar você a decidir qual SKU fornece o equilíbrio correto, mas para que sejam úteis você precisará pelo menos de uma estimativa aproximada do número e tamanho dos índices que planeja criar, e alguma ideia do volume de consultas. Com as estimativas em mãos, as etapas a seguir devem simplificar o processo:

- **Etapa 1** Analise as descrições do SKU abaixo para saber quais são suas opções.
- **Etapa 2** Responda a uma série de perguntas para refinar sua escolha.
- **Etapa 3** Valide sua decisão revisando os limites físicos de armazenamento e os preços. 

## Descrições do SKU

A tabela a seguir é uma descrição e uma comparação simples de cada SKU em termos de partições e réplicas, bem como QPS (Consultas por segundo), quando aplicável. Para obter uma introdução às réplicas e partições, confira [limites de serviço NA pesquisa do Azure](search-limits-quotas-capacity.md).

Camada|Capacidade|Projetado para
----|-----------|-----------
Grátis|50 MB ou três índices e 10.000 documentos|Um serviço compartilhado, sem custo adicional, usado para avaliação, investigação ou cargas de trabalho pequenas. Por ser compartilhado com outros assinantes, a taxa de transferência e a indexação da consulta poderão variar dependendo de quem mais está usando o serviço.
Básico|Três réplicas e uma partição|Cargas de trabalho de produção pequenas em hardware dedicado. Altamente disponível.
Standard 1 (S1)|Partições de 25 GB cada. <br/><br/>A QPS em réplicas é de aproximadamente 15 consultas por segundo.|Oferece suporte a combinações flexíveis de partições (12) e réplicas (12), usadas para cargas de trabalho de produção médias em hardware dedicado. Você pode alocar partições e réplicas em combinações com suporte de um número máximo de 36 unidades de pesquisa faturáveis.
Standard 2 (S2)|Partições de 100 GB cada. <br/><br/>A QPS em réplicas é de aproximadamente 60 consultas por segundo.|Executa cargas de trabalho de produção maiores, usando as mesmas configurações que o S1, mas com réplicas e partições maiores.
Standard 3 (S3) Preview|Partições de 200 GB cada. <br/><br/>A QPS em réplicas é superior a 60 consultas por segundo.|Executa cargas de trabalho de produção proporcionalmente maiores em sistemas de alta capacidade, em configurações de até 12 partições ou 12 réplicas. 
Standard 3 de Alta Densidade (S3 HD) Preview|Uma única partição em 200 GB. <br/><br/>A QPS em réplicas é superior a 60 consultas por segundo.|Destinado a clientes que possuem um quantidade maior de índices menores.

## Caminho de decisão para escolher um SKU

O gráfico a seguir é um subconjunto dos limites do [Limites de serviço na Pesquisa do Azure](search-limits-quotas-capacity.md). Ele lista os fatores com maior probabilidade de afetar uma decisão de SKU, incluindo limites de capacidade do SKU da tabela anterior, com limites adicionais relacionados a tipos de conteúdo (ou seja, índices e documentos). Você pode consultar esse gráfico

Recurso|Grátis|Básico|S1|S2|S3 <br/>(Preview) |S3 HD <br/>(Preview) 
---|---|---|---|----|---|----
Contrato de nível de serviço (SLA)|Não <sup>1</sup> |Sim |Sim |Sim |Não <sup>1</sup> |Não <sup>1</sup> 
Máximo de partições|N/D |1 |12 |12 |12|1
Índices permitidos por SKU|3|5|50|200|200|1000
Limites de documentos|10\.000 no total|1 milhão por serviço|15 milhões por partição |60 milhões por partição|120 milhões por partição |1 milhões por índice
Tamanho da partição|Total de 50 MB|2 GB por serviço|25 GB por partição |100 GB por partição (até um máximo de 1,2 TB por serviço)|200 GB por partição (até um máximo de 2,4 TB por serviço)|200 GB (para a única partição)
Máximo de réplicas|N/D |3 |12 |12 |12|12
Consultas por segundo|N/D|~3 por réplica|~15 por réplica|~60 por réplica|>60 por réplica|>60 por réplica

<sup>1</sup> SKUs de visualização e gratuitos não vêm com SLAs. Os SLAs são aplicados depois que um SKU fica disponível para o público geral.

> [AZURE.NOTE] Os limites máximos de partição e de réplica estão sujeitos a uma configuração de cobrança máxima combinada de 36 unidades, o que impõe um limite eficaz inferior ao que o máximo implica no valor nominal. Por exemplo, para usar o máximo de 12 réplicas, você pode ter no máximo três partições (12 * 3 = 36 unidades). Da mesma forma, para usar o máximo de partições, reduza as réplicas para três. Confira [Dimensionar os níveis de recursos para cargas de trabalho de consulta e indexação na Pesquisa do Azure](search-capacity-planning.md) para obter um gráfico de combinações permitidas.

### Perguntas comuns ao escolher um SKU

As perguntas a seguir podem ajudar você a chegar a uma decisão certa sobre o SKU para sua carga de trabalho.

1. Você tem requisitos de **SLA (Contrato de Nível de Serviço)**? Restrinja a decisão sobre o SKU ao Básico ou ao Standard que não seja preview.
2. **De quantos índices** você precisa? Uma das maiores variáveis que influenciará uma decisão de SKU é o número de índices com suporte de cada SKU. O suporte aos índices está em níveis muito diferentes nos tipos de preços inferiores. Os requisitos de número de índices pode ser um fator determinante na escolha do SKU.
3. **Quantos documentos** serão carregados em cada índice? O número e o tamanho dos documentos determinará o tamanho final do índice. Supondo que você possa fazer uma estimativa do tamanho projetado do índice, será possível comparar esse número com o tamanho da partição por SKU, além do número de partições necessárias para armazenar um índice desse tamanho. 
4. **Qual é a carga de consulta esperada**? Após definir os requisitos de armazenamento, considere as cargas de trabalho de consulta. O S2 e os dois SKUs S3 oferecem um rendimento quase equivalente, mas os requisitos de SLA descartarão quaisquer SKUs em fase preview. 

A maioria dos clientes pode incluir ou descartar um SKU específico com base em suas respostas a essas quatro perguntas. Se você ainda não tem certeza de qual SKU escolher, entre em contato com o Suporte do Azure para obter mais orientações.

## Validação de decisão: o SKU oferece armazenamento e QPS suficientes?

Como uma última etapa, reveja a [página de preços](https://azure.microsoft.com/pricing/details/search/) e as [seções por serviço e por índice em Limites de serviço](search-limits-quotas-capacity.md) para verificar suas estimativas em relação aos limites de serviço e assinatura.

Se os requisitos de armazenamento ou o preço estiverem fora dos limites, convém refatorar as cargas de trabalho entre vários serviços menores (por exemplo). Em um nível mais granular, você pode recriar os índices com um tamanho menor ou usar filtros para fazer consultas mais eficientes.

> [AZURE.NOTE] Os requisitos de armazenamento podem ser excessivos se os documentos contiverem dados estranhos. O ideal é que os documentos tenham somente metadados ou dados pesquisáveis. Dados binários que possam sobrecarregar um documento devem ser armazenados separadamente (talvez em um armazenamento de blob ou tabela do Azure) com um campo no índice para manter uma URL de referência aos dados externos. O tamanho máximo de um documento individual é de 16 MB (ou menos, se você estiver carregando em massa vários documentos em uma solicitação). Confira [Limites de serviço na Pesquisa do Azure](search-limits-quotas-capacity.md) para saber mais.

## Próxima etapa

Quando você souber qual SKU é adequado, prossiga com estas etapas:
- [Criar um serviço de pesquisa no portal](search-create-service-portal.md)
- [Alterar a alocação de partições e réplicas para dimensionar seu serviço](search-capacity-planning.md)

<!---HONumber=AcomDC_0608_2016-->