Recurso|Grátis|Basic (Visualização) <sup>6</sup>|S1|S2 <sup>7</sup>
---|---|---|---|----
Máximo de serviços de pesquisa|N/D|---|12 por assinatura do Azure |12 por assinatura do Azure
Tamanho máximo de armazenamento <sup>1</sup>|50 MB ou 10.000 documentos|2 GB por serviço|25 GB por partição ou 300 GB de documentos por serviço|100 GB por partição ou 1,2 TB por serviço
Máximo de documentos hospedados|Total de 10.000|1 milhão por serviço|15 milhões por partição (até 180 milhões de documentos por serviço)|60 milhões por partição (até 720 milhões de documentos por serviço)
Máximo de índices|3|5|50|200
Máximo de índices|3|5|50|200
Máximo de fontes de dados do indexador|3|5|50|200
Índice: máximo de campos por índice|1000|100 <sup>5</sup>|1000|1000
Índice: máximo de perfis de pontuação por índice|16|16|16|16
Índice: máximo de funções por perfil|8|8|8|8
Indexadores: carga máxima de indexação por invocação|10.000 documentos|Limitado somente pelo número máximo de documentos|Limitado somente pelo número máximo de documentos|Limitado somente pelo número máximo de documentos
Indexadores: tempo máximo de execução|3 minutos|24 horas|24 horas|24 horas
Consultas por segundo (QPS) <sup>2</sup>|N/D|~3 por réplica|~15 por réplica|~60 por réplica
Escalar horizontalmente: máximo de unidades de pesquisa (SU) <sup>3</sup>|N/D|Até três unidades (3 réplicas e 1 partição)|36 unidades|36 unidades
Preços <sup>4</sup>|N/D|US$ 75 por SU por mês|US$ 250 por SU por mês|US$ 1000 por SU por mês

<sup>1</sup> O tamanho do armazenamento é um valor fixo ou o número de documentos por serviço, o que ocorrer primeiro.

<sup>2</sup> O QPS é uma aproximação baseada em heurística, usando as cargas de trabalho de cliente real e simulada para derivar os valores estimados. A taxa de transferência de QPS exata irá variar dependendo dos dados e da natureza da consulta.

<sup>3</sup> As unidades de pesquisa são a unidade faturável para uma partição ou para uma réplica. Você precisa de ambas para as operações de armazenamento, indexação e consulta. Veja [Planejamento da Capacidade](../articles/search/search-capacity-planning.md) para obter as combinações válidas de réplicas e de partições que mantêm você no limite máximo de 3 ou de 36 unidades, para Basic e Standard, respectivamente.

<sup>4</sup> O preço é válido para o mercado americano, ilustrando os custos relativos entre as camadas alternativas. Mercados diferentes têm preços diferentes. Veja a [página Preços](https://azure.microsoft.com/pricing/details/search/) para obter as taxas em outras moedas. A taxa é por unidade de pesquisa (SU). No nível S1, uma configuração de 3 unidades de pesquisa (digamos, 3 réplicas e 1 partição) custaria, em média, US$ 750 por mês. Se você reduzir verticalmente para menos SU no mês, a conta será rateada de forma que você seja cobrado somente pelo que usar.

<sup>5</sup> Isso não é um erro de digitação. A camada Basic tem um limite de 100 campos por índice. É a única camada que tem esse limite mais baixo.

<sup>6</sup> A [camada Basic](http://aka.ms/azuresearchbasic) está disponível com uma taxa introdutória de 50% sobre o preço total durante o período de visualização.

<sup>7</sup> S2 requer um tíquete de serviço. Ela não pode ser provisionada no portal. Envie um email para azuresearch_contact@microsoft.com para obter ajuda para começar.

<!----HONumber=AcomDC_0316_2016-->