Recurso|Grátis|Basic (Visualização) <sup>6</sup>|S1|S2 <sup>7</sup>
---|---|---|---|----
Máximo de serviços de pesquisa<sup>8</sup>|1 por assinatura|12 por assinatura|12 por assinatura|1 por assinatura
Tamanho máximo de armazenamento <sup>1</sup>|50 MB ou 10.000 documentos|2 GB por serviço|25 GB por partição ou 300 GB de documentos por serviço|100 GB por partição ou 1,2 TB por serviço
Máximo de documentos hospedados|10\.000 no total|1 milhão por serviço|15 milhões por partição (até 180 milhões de documentos por serviço)|60 milhões por partição (até 720 milhões de documentos por serviço)
Índices máximos|3|5|50|200
Indexadores máximos|3|5|50|200
Indexador máximo de fontes de dados|3|5|50|200
Índice: campos máximos por índice|1000|100 <sup>5</sup>|1000|1000
Índice: máximo de perfis de pontuação por índice|16|16|16|16
Índice: funções máximas por perfil|8|8|8|8
Indexadores: carga de indexação máxima por invocação|10\.000 documentos|Limitado apenas pelo máximo de documentos|Limitado apenas pelo máximo de documentos|Limitado apenas pelo máximo de documentos
Indexadores: tempo de execução máximo|3 minutos|24 horas|24 horas|24 horas
Indexador de blob: tamanho máximo do blob, MB|16|16|128|256
Indexador de blob: número máximo de caracteres de conteúdo extraído de um blob|32\.000|64\.000|4 milhões|4 milhões
QPS (Consultas por segundo) <sup>2</sup>|N/D|~3 por réplica|~15 por réplica|~60 por réplica
Escala horizontal: máximo de unidades de pesquisa (SU) <sup>3</sup>|N/D|Até 3 unidades (3 réplicas e 1 partição)|36 unidades|36 unidades
Preço <sup>4</sup>|N/D|U$75 por SU por mês|U$250 por SU por mês|U$1000 por SU por mês

<sup>1</sup> O tamanho do armazenamento é um valor fixo ou o número de documentos por serviço, o que ocorrer primeiro.

<sup>2</sup> O QPS é uma aproximação baseada em heurística, usando as cargas de trabalho de cliente real e simulada para derivar os valores estimados. A taxa de transferência de QPS exata irá variar dependendo dos dados e da natureza da consulta.

<sup>3</sup> As unidades de pesquisa são a unidade faturável para uma partição ou para uma réplica. Você precisa de ambas para as operações para armazenamento, indexação e consulta. Consulte [Planejamento da Capacidade](../articles/search/search-capacity-planning.md) para obter as combinações válidas de réplicas e de partições que mantêm você no limite máximo de 3 ou de 36 unidades, para Básico e Standard, respectivamente.

<sup>4</sup> O preço é válido para o mercado americano, ilustrando os custos relativos entre as tipos alternativos. Mercados diferentes têm preços diferentes. Veja a [página Preços](https://azure.microsoft.com/pricing/details/search/) para obter as taxas em outras moedas. A taxa é por unidade de pesquisa (SU). No nível S1, uma configuração de 3 unidades de pesquisa (digamos, 3 réplicas e 1 partição) custaria, em média, US$ 750 por mês. Se você reduzir verticalmente para menos SU no mês, a conta será rateada de forma que você seja cobrado somente pelo que usar.

<sup>5</sup> O tipo Básico é o único que tem limite inferior de 100 campos por índice.

<sup>6</sup> O [tipo Básico](http://aka.ms/azuresearchbasic) está disponível com uma taxa introdutória de 50% sobre o preço total durante o período de preview.

<sup>7</sup> S2 requer um tíquete de serviço. Ela não pode ser provisionada no portal. Contate o Suporte ou envie um email para azuresearch_contact@microsoft.com para obter assistência.

<sup>8</sup> Você pode aumentar o número máximo de serviços por assinatura para todos os tipos exceto Gratuito. Contate o Suporte ou envie um email para azuresearch_contact@microsoft.com para obter assistência.

<!---HONumber=AcomDC_0525_2016-->