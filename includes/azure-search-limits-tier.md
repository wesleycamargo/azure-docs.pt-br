| Recurso | Grátis | Basic (Preview) | S1 | S2 |
| --- | --- | --- | --- | --- |
| Máximo de serviços em cada tipo por assinatura <sup>1</sup> |1 |12 |12 |1 |
| Máximo de escala por tipo <sup>2</sup> |N/D |3 SU (até 3 réplicas e 1 partição) |36 SU |36 SU |

<sup>1</sup> Cada serviço é provisionado em um determinado tipo de preço, com limites no número de serviços que você pode provisionar em cada tipo em uma única assinatura do Azure. Durante o período de Visualização, os tipos ficam disponíveis por uma taxa introdutória de 50% do preço total.

<sup>2</sup> Os limites de expansão são definidos em termos de SU (Unidades de Pesquisa) por tipo. As unidades de pesquisa são a unidade faturável para uma **réplica** ou uma **partição**. Você precisa de ambas para as operações para armazenamento, indexação e consulta. Visite [Escalar níveis de recurso para cargas de trabalho de consulta e de índice](../articles/search/search-capacity-planning.md) para obter as combinações válidas de réplicas e de partições permanecem no limite máximo de 3 ou de 36 unidades, para **Básico** e **Standard**, respectivamente. Como o **Gratuito** tem base nos recursos compartilhados usados por vários assinantes, a expansão não é fornecida nesse nível.

<!---HONumber=AcomDC_0601_2016-->