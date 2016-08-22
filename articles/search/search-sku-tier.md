<properties
	pageTitle="Escolher um tipo de preço ou SKU para a Pesquisa do Azure | Microsoft Azure"
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
	ms.date="08/08/2016"
	ms.author="heidist"/>

# Escolher um tipo de preço ou SKU para a Pesquisa do Azure

Durante o [provisionamento do serviço](search-create-service-portal.md), você precisará especificar um SKU ou tipo de preço. As escolhas incluem: **Gratuito**, **Básico** ou **Standard**, sendo que **Standard** está disponível em várias configurações e capacidades.

Recomendamos que você sempre forneça um serviço **Gratuito** (um por assinatura, sem prazo de expiração) a fim de tê-lo prontamente disponível para projetos leves. Use o serviço **Gratuito** para teste e avaliação; crie um segundo serviço faturável para cargas maiores de trabalho de teste ou produção no tipo **Básico** ou **Standard**.

Na Pesquisa do Azure, o SKU determina a capacidade, não a disponibilidade do recurso. Todos os recursos estão disponíveis em todos os tipos de preço, incluindo recursos de visualização.

## Como abordar uma decisão de tipo de preço

A capacidade e os custos de execução do serviço andam de lado a lado. As informações neste artigo podem ajudá-lo a decidir qual SKU fornece o equilíbrio correto, mas para que possa ser útil, você precisa ter estimativas aproximadas de pelo menos os seguintes itens:

- Número e tamanho dos índices que planeja criar
- Número e tamanho dos documentos a carregar
- Alguma ideia de volume de consulta, em termos de QPS (consultas por segundo)

Número e tamanho são importantes porque os limites máximos são atingidos com um limite rígido na contagem de índices ou documentos em um serviço ou nos recursos (armazenamento ou réplicas) usado pelo serviço. O limite real de seu serviço será aquele consumido primeiro: recursos ou objetos.

Com as estimativas em mãos, as etapas a seguir devem simplificar o processo:

- **Etapa 1** Analisar as descrições de SKU abaixo para saber mais sobre as opções disponíveis.
- **Etapa 2** Revisar as perguntas a restringir sua escolha.
- **Etapa 3** Validar sua decisão revisando os limites físicos de armazenamento e preços.

> [AZURE.NOTE] Se você subestimar a capacidade, precisará provisionar um novo serviço na camada superior e recarregar os índices. Não há nenhuma atualização in-loco do mesmo serviço de uma SKU para outra.

## Descrições do SKU

A tabela a seguir fornece descrições de cada camada.

Camada|Principais cenários
----|-----------------
**Gratuito**|Um serviço compartilhado, sem custo adicional, usado para avaliação, investigação ou cargas de trabalho pequenas. Por ser compartilhado com outros assinantes, a taxa de transferência e a indexação da consulta poderão variar dependendo de quem mais está usando o serviço. Capacidade pequena (50 MB ou três índices com até 10 mil documentos cada).
**Básico**|Cargas de trabalho de produção pequenas em hardware dedicado. Altamente disponível. Capacidade de até três réplicas e uma partição (2 GB).
**S1**|Standard 1 oferece suporte a combinações flexíveis de partições (12) e réplicas (12), usadas para as cargas de trabalho de produção média no hardware dedicado. Você pode alocar partições e réplicas em combinações com suporte de um número máximo de 36 unidades de pesquisa faturáveis. Nesse nível, as partições têm 25 GB cada e a taxa de QPS é aproximadamente 15 consultas por segundo.
**S2**|Standard 2 executa maiores cargas de trabalho de produção, usando as mesmas 36 unidades de pesquisa do S1, mas com réplicas e partições maiores. Nesse nível, as partições têm 100 GB e a taxa de QPS é cerca de 60 consultas por segundo.
**S3** (Visualização)|Standard 3 executa proporcionalmente maiores cargas de trabalho de produção em sistemas de alta capacidade, em configurações de até 12 partições ou 12 réplicas em 36 unidades de pesquisa. Nesse nível, as partições têm 200 GB e a taxa de QPS é maior que 60 consultas por segundo. O S3 está em visualização e disponível mediante uma taxa de apresentação.
**S3 HD** (Visualização)|A Alta Densidade Standard 3 destina-se a um grande número de índices menores. Só há uma partição de 200 GB. QPS de mais de 60 consultas por segundo. O S3 está em visualização e disponível mediante uma taxa de apresentação.

> [AZURE.NOTE] Os limites máximos de partição e de réplica são cobrados como unidades de pesquisa (máximo de 36 unidades por serviço), que impõe um limite eficaz inferior ao limite máximo em valor nominal. Por exemplo, para usar o máximo de 12 réplicas, você pode ter no máximo três partições (12 * 3 = 36 unidades). Da mesma forma, para usar o máximo de partições, reduza as réplicas para três. Confira [Dimensionar os níveis de recursos para cargas de trabalho de consulta e indexação na Pesquisa do Azure](search-capacity-planning.md) para obter um gráfico de combinações permitidas.

## Revisar os limites por tipo

O gráfico a seguir é um subconjunto dos limites do [Limites de serviço na Pesquisa do Azure](search-limits-quotas-capacity.md). Ele lista os fatores com maior probabilidade de afetar uma decisão de SKU. Você pode consultar esse gráfico ao revisar as perguntas a seguir.

Recurso|Grátis|Basic|S1|S2|S3 <br/>(Preview) |S3 HD <br/>(Preview) 
---|---|---|---|----|---|----
Contrato de nível de serviço (SLA)|Não <sup>1</sup> |Sim |Sim |Sim |Não <sup>1</sup> |Não <sup>1</sup> 
Índices permitidos por SKU|3|5|50|200|200|1000
Limites de documentos|10\.000 no total|1 milhão por serviço|15 milhões por partição |60 milhões por partição|120 milhões por partição |1 milhões por índice
Máximo de partições|N/D |1 |12 |12 |12|1
Tamanho da partição|Total de 50 MB|2 GB por serviço|25 GB por partição |100 GB por partição (até um máximo de 1,2 TB por serviço)|200 GB por partição (até um máximo de 2,4 TB por serviço)|200 GB (para a única partição)
Máximo de réplicas|N/D |3 |12 |12 |12|12
Consultas por segundo|N/D|~3 por réplica|~15 por réplica|~60 por réplica|>60 por réplica|>60 por réplica

<sup>1</sup> SKUs de visualização e gratuitos não vêm com SLAs. Os SLAs são aplicados depois que um SKU fica disponível para o público geral.


## Eliminar SKUs que não atendam aos requisitos 

As perguntas a seguir podem ajudar você a chegar a uma decisão certa sobre o SKU para sua carga de trabalho.

1. Você tem requisitos de **SLA (Contrato de Nível de Serviço)**? Restrinja a decisão sobre o SKU ao Básico ou ao Standard que não seja preview.
2. **De quantos índices** você precisa? Uma das maiores variáveis que influenciará uma decisão de SKU é o número de índices com suporte de cada SKU. O suporte aos índices está em níveis muito diferentes nos tipos de preços inferiores. Os requisitos do número de índices pode ser um determinante principal de uma decisão do SKU.
3. **Quantos documentos** serão carregados em cada índice? O número e o tamanho dos documentos determinará o tamanho final do índice. Supondo que você possa fazer uma estimativa do tamanho projetado do índice, será possível comparar esse número com o tamanho da partição por SKU, além do número de partições necessárias para armazenar um índice desse tamanho.
4. **Qual é a carga de consulta esperada**? Após definir os requisitos de armazenamento, considere as cargas de trabalho de consulta. O S2 e os dois SKUs S3 oferecem um rendimento quase equivalente, mas os requisitos de SLA descartarão quaisquer SKUs em fase preview.

A maioria dos clientes pode incluir ou descartar um SKU específico com base em suas respostas a essas quatro perguntas. Se você ainda não tem certeza de qual SKU escolher, entre em contato com o Suporte do Azure para obter mais orientações.

## Validação de decisão: o SKU oferece armazenamento e QPS suficientes?

Como uma última etapa, reveja a [página de preços](https://azure.microsoft.com/pricing/details/search/) e as [seções por serviço e por índice em Limites de serviço](search-limits-quotas-capacity.md) para verificar suas estimativas em relação aos limites de serviço e assinatura.

Se os requisitos de armazenamento ou o preço estiverem fora dos limites, convém refatorar as cargas de trabalho entre vários serviços menores (por exemplo). Em um nível mais granular, você pode recriar os índices com um tamanho menor ou usar filtros para fazer consultas mais eficientes.

> [AZURE.NOTE] Os requisitos de armazenamento podem ser excessivos se os documentos contiverem dados estranhos. O ideal é que os documentos contenham somente metadados ou dados pesquisáveis. Dados binários não são pesquisáveis e devem ser armazenados separadamente (talvez em um armazenamento de blobs ou tabela do Azure) com um campo no índice para manter uma URL de referência aos dados externos. O tamanho máximo de um documento individual é de 16 MB (ou menos, se você estiver carregando em massa vários documentos em uma solicitação). Confira [Limites de serviço na Pesquisa do Azure](search-limits-quotas-capacity.md) para saber mais.

## Próxima etapa

Quando você souber qual SKU é adequado, prossiga com estas etapas:

- [Criar um serviço de pesquisa no portal](search-create-service-portal.md)
- [Alterar a alocação de partições e réplicas para dimensionar seu serviço](search-capacity-planning.md)

<!---HONumber=AcomDC_0810_2016-->