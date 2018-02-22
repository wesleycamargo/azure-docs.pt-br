O armazenamento é restrito pelo espaço em disco ou por um limite rígido do *número máximo* de índices ou documentos, o que ocorrer primeiro.

| Recurso | Grátis | Basic | S1 | S2 | S3 | S3 HD |
| --- | --- | --- | --- | --- | --- | --- |
| Contrato de nível de serviço (SLA) |Não <sup>1</sup> |sim |sim |sim |sim |sim |
| Armazenamento por partição |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |
| Partições por serviço |N/D |1 |12 |12 |12 |3 <sup>2</sup> |
| Tamanho da partição |N/D |2 GB |25 GB |100 GB |200 GB |200 GB |
| Réplicas |N/D |3 |12 |12 |12 |12 |
| Índices máximos |3 |5 <sup>3</sup>|50 |200 |200 |1000 por partição ou 3000 por serviço |
| Indexadores máximos |3 |5 <sup>3</sup>|50 |200 |200 |Não há suporte do indexador |
| Máximo de fontes de dados |3 |5 <sup>3</sup>|50 |200 |200 |Não há suporte do indexador |
| Máximo de documentos <sup>3</sup> |10.000 |1 milhão |15 milhões por partição ou 180 milhões por serviço |60 milhões por partição ou 720 milhões por serviço |120 milhões por partição ou 1,4 bilhão por serviço |1 milhão por serviço, 200 milhões por partição |

<sup>1</sup> Os recursos de camada gratuita e de versão prévia não vêm com SLAs (contratos de nível de serviço). Para todas as camadas faturáveis, os SLAs entram em vigor quando você provisiona redundância suficiente para o serviço. Duas ou mais réplicas são necessárias para o SLA de consulta (leitura). Três ou mais réplicas são necessárias para consulta e indexação do SLA (leitura-gravação). O número de partições não é uma consideração de SLA. 

<sup>2</sup> S3 HD tem um limite rígido de três partições, que é menor do que o limite de partição do S3. O limite de partição inferior é aplicado porque a contagem de índice de S3 HD é consideravelmente maior. Uma vez que há limites de serviço para os recursos de computação (processamento e armazenamento) e de conteúdo (índices e documentos), o limite de conteúdo é atingido primeiro.

>[!Important]
> **<sup>3</sup>** A partir do final de 2017, os serviços do Azure Search criados recentemente foram provisionados usando configurações de hardware subjacentes mais avançadas que permitem que alguns limites sejam alterados em certas regiões (Sul do Brasil, Central do Canadá, Índia Central, Leste dos EUA, Centro-Norte dos EUA, Europa Setentrional, Centro-Sul dos EUA, Sudeste Asiático, Sul do Reino Unido, Europa Ocidental e Oeste dos EUA):
>
>* Os Serviços de Pesquisa de camada Padrão e Básico criados no final de 2017 não têm limites na contagem de documentos. Somente os limites de armazenamento são aplicados nesses serviços. 
>* Para serviços de Alta Densidade S3 criados no final de 2017, o documento de 200 milhões por partição foi removido, mas o limite de 1 milhão de documentos por índice permanece.
>* Os serviços básicos criados no final de 2017 têm um limite aumentado de 15 índices, fontes de dados e indexadores.
>
>Para saber mais sobre quais limites aplicáveis a um serviço existente, use o Portal do Azure para visualizar as informações de limite na página de visão geral do seu serviço.