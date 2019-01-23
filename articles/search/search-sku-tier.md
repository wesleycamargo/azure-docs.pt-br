---
title: Escolher um tipo de preço ou SKU para o serviço Azure Search - Azure Search
description: 'O Azure Search pode ser provisionado nestes SKUs: Gratuito, Básico e Padrão, sendo que o Padrão está disponível em várias configurações de recursos e níveis de capacidade.'
services: search
author: HeidiSteen
manager: cgronlun
tags: azure-portal
ms.service: search
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 664e31590f578b65da09f1e0fe8f57d579ed3cfc
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54354545"
---
# <a name="choose-a-pricing-tier-for-azure-search"></a>Escolher um tipo de preço para o Azure Search

No Azure Search, um [recurso é criado](search-create-service-portal.md) em um tipo de preço ou SKU fixo durante o tempo de vida do serviço. Os tipos incluem: **Gratuito**, **Básico** ou **Standard**, considerando que **Standard** está disponível em várias configurações e capacidades. A maioria dos clientes inicia com a camada **Gratuita** para avaliação e, em seguida, mudam para a **Standard** para desenvolvimento e implantações de produção. Você pode concluir todos os guias de início rápido e tutoriais na camada **Gratuita**, incluindo os de pesquisa cognitiva com uso intensivo de recurso. 

Os níveis determinam a capacidade, não os recursos, e são diferenciados por:

+ Número de índices que você pode criar
+ Tamanho e velocidade de partições (armazenamento físico)

Embora todos os níveis, incluindo a camada **Gratuita**, geralmente ofereçam paridade de recursos, cargas de trabalho maiores podem exigir requisitos de níveis mais altos. Por exemplo, a indexação de [pesquisa cognitiva](cognitive-search-concept-intro.md) tem habilidades de longa execução que atingem o tempo limite em um serviço gratuito, a menos que o conjunto de dados seja pequeno.

> [!NOTE] 
> A exceção à paridade de recursos são os [indexadores](search-indexer-overview.md), que não estão disponíveis no S3HD.
>

Dentro de um nível, você pode [ajustar os recursos de partição e de réplica](search-capacity-planning.md) para ajuste de desempenho. Você pode começar com dois ou três de cada e depois aumentar temporariamente seu poder computacional para uma carga de trabalho de indexação pesada. A capacidade de ajustar os níveis de recursos dentro de uma camada adiciona flexibilidade, mas também complica um pouco a análise. Talvez você precise experimentar para verificar se um nível mais baixo com maior de recursos/réplicas oferece melhor desempenho e valor do que um nível superior com menor alocação de recursos. Para saber mais sobre quando e por que você deve ajustar a capacidade, confira [Considerações sobre desempenho e otimização](search-performance-optimization.md).

<!---
The purpose of this article is to help you choose a tier. It supplements the [pricing page](https://azure.microsoft.com/pricing/details/search/) and [Service Limits](search-limits-quotas-capacity.md) page with a digest of billing concepts and consumption patterns associated with various tiers. It also recommends an iterative approach for understanding which tier best meets your needs. 
--->

## <a name="how-billing-works"></a>Como funciona a cobrança

No Azure Search, há quatro maneiras que você pode incorrer em custos quando você cria um recurso de pesquisa no portal:

* Adicionando réplicas e partições usadas para tarefas regulares de indexação e de consulta. Você começa com uma de cada, mas pode aumentar uma ou ambas para adicionar capacidade, escolhendo níveis adicionais de alocação de recursos e pagando por eles. 
* Encargos de saída de dados durante a indexação. Ao efetuar pull de dados de uma fonte de dados do Banco de Dados SQL do Azure ou do Cosmos DB, você verá cobranças pela transação na fatura para esses recursos.
* Apenas para [pesquisa cognitiva](cognitive-search-concept-intro.md), a extração da imagem durante a decodificação de documentos é cobrada apenas com base no número de imagens extraídas dos documentos. A extração de texto atualmente é gratuita.
* Apenas para [pesquisa cognitiva](cognitive-search-concept-intro.md), aprimoramentos com base em [habilidades cognitivas internas](cognitive-search-predefined-skills.md) são cobrados segundo um recurso dos Serviços Cognitivos. Aprimoramentos são cobrados com a mesma taxa usada se você executa a tarefa usando os Serviços Cognitivos diretamente.

Se você não estiver usando [pesquisa cognitiva](cognitive-search-concept-intro.md) nem [indexadores do Azure Search](search-indexer-overview.md), seus únicos custos serão relacionados às réplicas e partições em uso ativo, para cargas de trabalho de indexação e consulta regulares.

### <a name="billing-for-general-purpose-indexing-and-queries"></a>Cobrança para indexação e consultas de uso geral

Em operações do Azure Search, o conceito de cobrança mais importante a ser entendido é o de UA (*unidade de pesquisa*). Já que Azure Search depende de réplicas e partições para indexação e consultas, não faz sentido cobrar de apenas uma maneira ou de outra. Em vez disso, a cobrança baseia-se em uma combinação de ambos. 

UA é o produto da *réplica* e das *partições* usadas por um serviço: **`(R X P = SU)`**

Cada serviço começa com uma UA (uma réplica multiplicada por uma partição) como o mínimo. O máximo para qualquer serviço é 36 SUs, o que pode ser obtido de várias maneiras: 6 partições x 6 ou réplicas ou 3 partições x 12 réplicas, para citar alguns. É comum usar menos do que a capacidade total. Por exemplo, um serviço de 3 réplicas e 3 partições cobrado como 9 UAs. 

A taxa de cobrança é **por hora por UA**, e cada nível tem uma taxa maior progressivamente. Os níveis mais altos são fornecidos com partições maiores e mais rápidas, contribuindo para uma taxa horária geral maior para esse nível. As taxas de cada camada podem ser encontradas em [Detalhes de Preço](https://azure.microsoft.com/pricing/details/search/). 

A maioria dos clientes coloca apenas uma parte da capacidade total online, mantendo o restante em reserva. Em termos de cobrança, esse é o número de partições e réplicas que você colocar online, calculado usando a fórmula de UA, que determina o que você realmente paga por hora.

### <a name="billing-for-image-extraction-in-cognitive-search"></a>Cobrança para extração de imagens na pesquisa cognitiva

Se você está extraindo imagens de arquivos em um pipeline de indexação de pesquisa cognitiva, você é cobrado por essa operação em sua fatura do Azure Search. Em uma [configuração do indexador](https://docs.microsoft.com/erest/api/searchservice/create-indexer#indexer-parameters), o parâmetro que dispara a extração de imagens é **imageAction**. Se **imageAction** é definido como nenhum (padrão), não há encargos para a extração de imagens.

O preço está sujeito a alterações, mas sempre está documentado na página [Detalhes de preços](https://azure.microsoft.com/pricing/details/search/) do Azure Search. 

### <a name="billing-for-built-in-skills-in-cognitive-search"></a>Cobrança de habilidades internas na pesquisa cognitiva

Quando você configura um pipeline de aprimoramento, quaisquer [habilidades internas](cognitive-search-predefined-skills.md) usadas no pipeline baseiam-se em modelos de aprendizado de máquina. Esses modelos são fornecidos pelos Serviços Cognitivos. O uso desses modelos durante a indexação é cobrado com a mesma taxa aplicada se você solicita o recurso diretamente.

Por exemplo, imagine um cenário com um pipeline que consiste em OCR (reconhecimento óptico de caracteres) usado em arquivos JPEG de imagem digitalizada, em que o texto resultante é enviado para um índice de Azure Search para consultas de pesquisa de forma livre. Seu pipeline de indexação incluiria um indexador com a [habilidade de OCR](cognitive-search-skill-ocr.md), a qual seria uma habilidade [anexada a um recurso dos Serviços Cognitivos](cognitive-search-attach-cognitive-services.md). Quando você executasse o indexador, as cobranças pela execução de OCR apareceriam em sua fatura de Recursos Cognitivos.

## <a name="tips-for-reducing-costs"></a>Dicas para reduzir os custos

Não é possível desligar o serviço para reduzir a fatura. Os recursos dedicados operam 24 horas, 7 dias da semana, alocados para seu uso exclusivo durante o tempo de vida do serviço. A única maneira de reduzir uma fatura é, reduzindo as réplicas e partições para um nível baixo que ainda forneça um desempenho aceitável e [conformidade com o SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Uma alavanca para reduzir os custos é escolher um nível com uma taxa por hora mais baixa. As tarifas por hora do S1 são menores do que as do S2 ou do S3. Supondo que você provisiona um serviço que visa a extremidade inferior do suas projeções de carga, se você excede a capacidade do serviço, você pode criar um segundo serviço em camadas maiores, recompilar os índices em um segundo serviço e, em seguida, excluir o primeiro. 

Se você já fez planejamento de capacidade para servidores locais, você sabe que é comum "comprar a mais" para poder lidar com o crescimento projetado. Mas, com um serviço de nuvem, você pode buscar uma economia de custo de forma agressiva porque você não está limitado a uma compra específica. Você sempre pode mudar para um serviço de nível superior quando o atual é insuficiente.

### <a name="capacity-drill-down"></a>Capacidade de fazer drill down

No Azure Search, a capacidade é estruturada como *réplicas* e *partições*. 

+ As réplicas são instâncias do serviço de pesquisa, onde cada réplica hospeda uma cópia com balanceamento de carga de um índice. Por exemplo, um serviço com 6 réplicas tem 6 cópias de cada índice carregado no serviço. 

+ As partições armazenam índices e automaticamente dividem os dados pesquisados: duas partições de divisão para o seu índice na metade, três participações em três e assim por diante. Em termos de capacidade, o *tamanho da partição* é o principal recurso de diferenciação em camadas.

> [!NOTE]
> Todas as camadas **Padrão** suportam [a réplica de combinações flexíveis e partições](search-capacity-planning.md#chart) para que você possa [pesar seu sistema de armazenamento ou velocidade](search-performance-optimization.md) alterando o equilíbrio. **Básico** oferece até três réplicas para alta disponibilidade, mas possui apenas uma partição. As camadas **Gratuitas** não fornecem recursos dedicados: os recursos de computação são compartilhados por vários assinantes.

### <a name="more-about-service-limits"></a>Mais informações sobre limites de serviço

Serviços de recursos de host, como índices, indexadores e assim por diante. Cada camada impõe [limites de serviço](search-limits-quotas-capacity.md) na quantidade de recursos que você pode criar. Como tal, uma tampa no número de índices (e outros objetos) é o segundo recurso de diferenciação através de camadas. Ao revisar cada opção no portal, observe os limites no número de índices. Outros recursos, como indexadores, fontes de dados e conhecimentos, são vinculados aos limites de índice.

## <a name="consumption-patterns"></a>Padrões de consumo

A maioria dos clientes inicia com o serviço **Livre**, que eles mantêm indefinidamente e, em seguida, escolha uma das camadas **Padrão** para desenvolvimento grave ou cargas de trabalho de produção. 

![Camadas de pesquisa do Azure](./media/search-sku-tier/tiers.png "camadas de preços de pesquisa do Azure")

Em ambos os lados, **Básico** e **HD S3** existem para padrões de consumo importante mas atípicos. **Básico** é para cargas de trabalho de produção de pequeno porte: oferece SLA, recursos dedicados, alta disponibilidade, mas armazenamento modesto, chegando ao total de 2 GB. Esta camada foi projetada para clientes que estão consistentemente em capacidade disponível utilizada. Na extremidade oposta, o **S3 HD** para cargas de trabalho típica de ISVs, parceiros, [soluções multilocatárias](search-modeling-multitenant-saas-applications.md), ou qualquer configuração de chamada para um grande número de índices pequenos. Geralmente é evidente quando a camada **Básico** ou **S3 HD** é a mais adequada, mas se você desejar confirmar que você pode postar em [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) ou [entre em contato com o Azure Support](https://azure.microsoft.com/support/options/) para obter mais orientações.

Mudar o foco para as camadas padrão usadas com mais frequência, **S1-S3** são uma progressão de níveis crescentes de capacidade, com pontos de inflexão no tamanho da partição e os limites máximos em números de índices, indexadores e recursos de resultado:

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| tamanho da partição|  25 GB | 100 GB | 250 GB |  |  |  |  |
| limites de índice e indexador| 50 | 200 | 200 |  |  |  |  |

**S1** é uma opção comum quando os recursos dedicados e várias partições se tornam uma necessidade. Com partições de 25 GB para até 12 partições, o limite por serviço em **S1** é 300 GB total que você aumenta partições sobre réplicas (consulte [Alocar partições e réplicas](search-capacity-planning.md#chart) para mais composições balanceadas)

Páginas de portal e preços colocam o foco no tamanho de partição e de armazenamento, mas para cada camada, todos os recursos de computação (capacidade do disco, velocidade, CPUs) crescem de forma linear com preço. Uma réplica **S2** é mais rápida que **S1**, e **S3** é mais rápido que **S2**. As camadas **S3** quebram o padrão de preços de computação geralmente linear com desproporcionalmente mais rápida de E/S. Se você antecipar E/S como o afunilamento, um **S3** fornece muito mais IOPS de níveis mais baixos.

**S3** e **S3 HD** D contam com infraestrutura de alta capacidade idênticas, mas cada um deles atinge seu limite máximo de maneiras diferentes. O **S3** tem como alvo um número menor de índices muito grandes. Como tal, seu limite máximo está vinculado a recursos (2,4 TB para cada serviço). O **S3 HD** destina-se um grande número de índices muito pequenos. Com 1.000 índices, o **S3 HD** atinge os limites na forma de restrições de índice. Se você for um cliente do **S3 HD** que requer mais de 1.000 índices, entre em contato com o Suporte da Microsoft para obter informações sobre como proceder.

> [!NOTE]
> Anteriormente, os limites de documentos eram uma preocupação, mas não são mais aplicáveis para novos serviços. Para obter mais informações sobre as condições sob as quais os limites de documento ainda se aplicam, confira [Limites de serviço: limites de documentos](search-limits-quotas-capacity.md#document-limits).
>

## <a name="evaluate-capacity"></a>Avaliação da capacidade

A capacidade e os custos de execução do serviço andam de lado a lado. As camadas limitam-se em dois níveis (armazenamento e recursos), portanto você deve considerar ambos porque qualquer um que você alcançar primeiro é o limite efetivo. 

Requisitos de negócios determinarem normalmente o número de índices, que será necessário. Por exemplo, um índice global para um repositório grande de documentos, ou talvez vários índices com base no setor de negócios, aplicativo ou região.

Para determinar o tamanho de um índice, você precisa [compilar um](search-create-index-portal.md). A estrutura de dados no Azure Search é principalmente um [índice invertido](https://en.wikipedia.org/wiki/Inverted_index), que tem características diferentes dos dados de origem. Para um índice invertido, tamanho e complexidade são determinados pelo conteúdo, não necessariamente a quantidade de dados que você alimenta. Uma fonte de dados grande com redundância massiva pode resultar em um índice menor do que um conjunto de dados menor que contém conteúdo altamente variável. Como tal, é raramente possível inferir o tamanho de índice com base no tamanho do conjunto de dados original.

> [!NOTE] 
> Embora a estimativa das necessidades futuras de armazenamento e de índices envolva um pouco de adivinhação, realmente vale a pena fazê-la. Se a capacidade de uma camada for muito baixa, você precisará provisionar um novo serviço na camada superior e [recarregar os índices](search-howto-reindex.md). Não há nenhuma atualização in-loco do mesmo serviço de uma SKU para outra.
>

### <a name="step-1-develop-rough-estimates-using-the-free-tier"></a>Etapa 1: Desenvolver estimativas aproximadas, usando a camada gratuita

Uma abordagem para calcular a capacidade é iniciar com a camada **Livre**. Lembre-se de que o serviço **Livre** oferece até 3 índices, 50 MB de armazenamento e de indexação de 2 minutos. Ele pode ser um desafio para estimar um tamanho de índice projetado com essas restrições, mas o exemplo a seguir ilustra uma abordagem:

+ [Cria um serviço grátis](search-create-service-portal.md)
+ Preparar um conjunto de dados pequeno, representativo (supondo documentos de cinco il e tamanho de modelo dez por cento)
+ [Criar um índice inicial](search-create-index-portal.md) e anote seu tamanho no portal (supondo que 30 MB)

Supondo que o exemplo foi representante e dez por cento da fonte de dados inteiro, um índice de 30 MB se tornará aproximadamente 300 MB se todos os documentos forem indexados. Armado com esse número preliminar, você pode dobrar esse valor para o orçamento para dois índices (desenvolvimento e produção), para um total de 600 MB em requisitos de armazenamento. Isso é facilmente atendido pela camada **Básico**, portanto você deve iniciar de lá.

### <a name="step-2-develop-refined-estimates-using-a-billable-tier"></a>Etapa 2: Desenvolver estimativas refinadas usando uma camada faturável

Alguns clientes preferem iniciar com recursos dedicados que podem acomodar amostragem maior e tempos de processamento, e em seguida desenvolve estimativas realistas da quantidade do índice, tamanho e volumes de consulta durante o desenvolvimento. Inicialmente, um serviço é provisionado com base em uma estimativa melhor alternativa e, em seguida, como o projeto de desenvolvimento amadurece, as equipes geralmente sabem se o serviço existente está acima ou abaixo de capacidade para cargas de trabalho de produção projetadas. 

1. [Examine os limites de serviço em cada camada](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits) para determinar se os níveis mais baixos podem dar suporte a quantidade de índices que você precisa. Entre as camadas **Básico**-**S1**- **S2**, os limites de índice são 50-15-200, respectivamente.

1. [Criar um serviço em uma camada faturável](search-create-service-portal.md):

    + Iniciar pouca em **Básico** ou **S1** se você estiver no início de sua curva de aprendizado.
    + Iniciar alto, em **S2** ou até mesmo **S3**, se indexação de larga escala e cargas de consulta são auto-confiantes.

1. [Criar um índice inicial](search-create-index-portal.md) para determinar como a fonte de dados traduz para um índice. Essa é a única maneira de estimar o tamanho do índice.

1. [Monitorar armazenamento, limites de serviço, volume de consulta e latência](search-monitor-usage.md) no portal. O portal mostra consultas por segundo, consultas de restrição e latência de pesquisa; todos dos quais podem ajudar você a decidir se está na camada certa. Além de métricas de portal, você pode configurar monitoramento profundo, como a análise de clickthrough, habilitando [análise de tráfego de pesquisa](search-traffic-analytics.md). 

O número de índice e o tamanho são igualmente relevantes para sua análise porque os limites máximos são alcançados por meio de utilização total de armazenamento (partições) ou limites máximo de recursos (índices, indexadores e assim por diante), o que ocorrer primeiro. O portal ajuda a manter o controle de ambos, mostrando o uso atual e os limites máximos lado a lado na página Visão geral.

> [!NOTE]
> Os requisitos de armazenamento podem ser excessivos se os documentos contiverem dados estranhos. Idealmente, os documentos contêm apenas os dados necessários para a experiência de pesquisa. Dados binários não são pesquisáveis e devem ser armazenados separadamente (talvez em um armazenamento de blobs ou tabela do Azure) com um campo no índice para manter uma URL de referência aos dados externos. O tamanho máximo de um documento individual é de 16 MB (ou menos, se você estiver carregando em massa vários documentos em uma solicitação). [Os limites de serviço no Azure Search](search-limits-quotas-capacity.md) tem mais informações.
>

**Considerações sobre volume de consultas**

A QPS (consultas por segundo) é uma medida que obtém importância durante o ajuste de desempenho, mas geralmente não é uma consideração de camada, a menos que você espera um volume alto de consulta desde o início.

As camadas padrão podem fornecer um equilíbrio entre réplicas para partições, dando suporte a retorno mais rápido de consulta por meio de réplicas adicionais para carregar as partições adicionais e balanceamento para processamento paralelo. Você pode ajustar o desempenho depois que o serviço é fornecido.

O cliente que espera volumes de consulta sustentadas do início deve considerar camadas maiores, apoiado por hardware mais potente. Você pode colocar as partições e réplicas offline ou até mesmo alternar para um serviço de camada inferior, se os volumes de consulta não materializarem. Para obter mais informações sobre como calcular a taxa de transferência de consulta, consulte [desempenho do Azure Search e otimização](search-performance-optimization.md).


**Contratos de nível de serviço**

A camada **Gratuita** não vêm com [SLAs (contratos de nível de serviço)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Para todas as camadas faturáveis, os SLAs entram em vigor quando você provisiona redundância suficiente para o serviço. Duas ou mais réplicas são necessárias para o SLA de consulta (leitura). Três ou mais réplicas são necessárias para consulta e indexação do SLA (leitura-gravação). O número de partições não é uma consideração de SLA. 

## <a name="tips-for-tier-evaluation"></a>Dicas para avaliação de camada

+ Saiba como criar índices eficientes e as metodologias de atualização são o mínimo de impacto. Nós recomendamos [análise de tráfego de pesquisa](search-traffic-analytics.md) para as informações obtidas na atividade de consulta.

+ Permitir que as métricas criem uma solução alternativa para consultas e coletar dados sobre padrões de uso (consultas durante o horário comercial, a indexação de horário de pico) e usar esses dados para informar as decisões de provisionamento de serviço futuro. Embora não seja prático fazê-lo em uma cadência horária ou diária, você pode ajustar dinamicamente partições e recursos para acomodar alterações planejadas em volumes de consulta ou alterações não planejadas, mas prolongadas se os níveis se mantiverem por tempos suficientemente longos para garantir a execução de uma ação.

+ Lembre-se de que a única desvantagem de provisionamento é que talvez você precise subdividir um serviço se os requisitos reais forem maiores que o previsto. Para evitar a interrupção do serviço, crie um novo serviço na mesma assinatura em uma camada superior e execute-o lado a lado até todos os aplicativos e solicitações de novo ponto de extremidade de destino.

## <a name="next-steps"></a>Próximas etapas

Inicie com uma camada **Gratuita** crie um índice inicial com um subconjunto de seus dados para entender suas características. A estrutura de dados no Azure é um índice invertido, onde o tamanho e complexidade de um índice invertido são determinados pelo conteúdo. Lembre-se de que o conteúdo altamente redundante tende a resultar em um índice menor que o conteúdo altamente irregular. Como tal, são as características de conteúdo e não o tamanho do conjunto de dados que determinam os requisitos de armazenamento de índice.

Depois que você tiver uma ideia inicial do tamanho do índice, [provisione um serviço faturável](search-create-service-portal.md) em uma das camadas discutidas neste artigo, seja camada**Básico** ou **Padrão**. Relaxe quaisquer restrições artificiais em subconjuntos de dados e [recrie o índice](search-howto-reindex.md) para incluir todos os dados que você realmente deseja que sejam pesquisados.

[Alocar partições e réplicas](search-capacity-planning.md) conforme necessário para obter o desempenho e escalabilidade que você precisa.

Se o desempenho e a capacidade são permitidos, termine. Caso contrário, crie novamente um serviço de pesquisa em uma outra camada mais estreitamente alinhada a suas necessidades.

> [!NOTE]
> Para obter ajuda com suas perguntas, postar [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) ou [entre em contato com o Azure Support](https://azure.microsoft.com/support/options/).
