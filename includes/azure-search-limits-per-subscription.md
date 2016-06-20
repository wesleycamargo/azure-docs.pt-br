Você pode criar vários serviços dentro de uma assinatura, cada um provisionado em uma camada específica, limitada somente pelo número de serviços permitidos em cada camada em uma única assinatura do Azure. A quantidade máxima de serviços por camada está relacionada abaixo. Como indicado, você pode criar até 12 serviços na camada Básico e outros 12 serviços na camada S1 dentro da mesma assinatura.

Outras camadas são uma por assinatura. Você poderá entrar em contato com o suporte do Azure se precisar de mais de um S2, S3 ou S3 HD por assinatura.

Recurso|Grátis|Básico|S1|S2|S3 <sup>1</sup> <br/>(Preview) |S3 HD <sup>1</sup> <br/>(Preview) 
---|---|---|---|----|---|----
Quantidade máxima de serviços |1 |12 |12 |1 |1 |1 
SU de redução horizontal máxima <sup>2</sup>|N/D <sup>3</sup>|3 SU <sup>4</sup> |36 SU|36 SU|36 SU|12 SU <sup>5</sup>

<sup>1</sup> As camadas de **preview** são cobradas a uma taxa introdutória de 50% sobre o preço total. Antes da disponibilidade geral (GA), as camadas são apresentadas como um recurso da Preview. Durante a Preview, não há nenhum SLA (Contrato de Nível de Serviço). Confira [Choose a SKU or tier for Azure Search](../articles/search/search-sku-tier.md) (Escolher um SKU ou camada para a Pesquisa do Azure) para obter mais informações sobre as camadas.

<sup>2</sup> **As SU (unidades de pesquisa)** são unidades faturáveis por serviço, alocadas como uma **réplica** ou como uma **partição**. Você precisa de ambos os tipos de recursos para as operações de armazenamento, indexação e consulta. Confira [Scale resource levels for query and index workloads](../articles/search/search-capacity-planning.md) (Dimensionar os níveis de recursos para cargas de trabalho de consulta e índice) para as combinações válidas que permanecem sob os limites máximos.

<sup>3</sup> **Gratuito** é baseado nos recursos compartilhados usados por vários assinantes. Neste nível, não há nenhum recurso dedicado para um assinante individual. Por esse motivo, não há suporte para escalabilidade.

<sup>4</sup> **Básico** tem uma partição corrigida. As SUs são usadas para alocar réplicas para cargas de trabalho de consulta de colocação em escala.

<sup>5</sup> **S3 HD** é baseado no mesmo hardware de S3, mas em uma configuração que é otimizada para um grande número de índices menores. Ele tem uma partição muito grande, em vez de 12 partições menores, e tem um máximo de 12 réplicas, semelhante ao S3.

<!---HONumber=AcomDC_0608_2016-->