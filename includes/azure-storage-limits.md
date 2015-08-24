Recurso|Limite padrão
---|---
Número máximo de contas de armazenamento por assinatura|100<sup>1</sup>
TB por conta de armazenamento|500 TB
Tamanho máximo de um único contêiner de blob, tabela ou fila|500 TB
Número máximo de blocos em um blob de blocos|50\.000
Tamanho máximo de um bloco em um blob de blocos|4 MB
Tamanho máximo de um blob de blocos|50\.000 x 4 MB (aproximadamente 195 GB) 
Tamanho máximo de um blob de páginas |1 TB
Tamanho máximo de uma entidade de tabela|1 MB
Número máximo de propriedades em uma entidade de tabela|252
Tamanho máximo de uma mensagem em uma fila|64 KB
Tamanho máximo de um compartilhamento de arquivo|5 TB
Tamanho máximo de um arquivo em um compartilhamento de arquivos|1 TB
Número máximo de arquivos em um compartilhamento de arquivo|O único limite é a capacidade total de 5 TB do compartilhamento de arquivo
Número máximo de contêineres de blob, blobs, compartilhamentos de arquivo, tabelas, filas, entidades ou mensagens por conta de armazenamento|O único limite é a capacidade de conta de armazenamento de 500 TB
IOPS de no máx. 8 KB por disco persistente (máquina virtual da camada Basic)|300<sup>2</sup>
IOPS de, no máximo, 8 KB por disco persistente (máquina virtual da camada Standard)|500<sup>2</sup>
Taxa de solicitação total (presumindo um tamanho de objeto de 1KB) por conta de armazenamento|Até 20.000 IOPS, entidades por segundo ou mensagens por segundo
Rendimento de destino por blob único|Até 60 MB por segundo, ou até 500 solicitações por segundo
Rendimento de destino por fila única (mensagens de 1 KB)|Até 2000 mensagens por segundo
Rendimento de destino por partição de tabela única (entidades de 1 KB)|Até 2000 entidades por segundo
Taxa de transferência de destino por compartilhamento de arquivo único (Visualização)|Até 60 MB por segundo
Entrada máxima<sup>3</sup> por conta de armazenamento (regiões dos EUA)|10 Gbps se o GRS/ZRS <sup>4</sup> estiver habilitado, 20 Gbps para LRS
Egresso máximo<sup>3</sup> por conta de armazenamento (regiões dos EUA)|20 Gbps se o GRS/ZRS<sup>4</sup> estiver ativado, 30 Gbps para LRS
Entrada máxima<sup>3</sup> por conta de armazenamento (regiões da Ásia e Europa)|5 Gbps se <sup>4</sup> GRS/ZRS estiverem ativos, 10 Gbps para LRS
Egresso máximo <sup>3</sup> por conta de armazenamento (Regiões da Europa e da Asia)|10 Gbps se <sup>4</sup> GRS/ZRS estiverem ativos, 15 Gbps para LRS

<sup>1</sup>se você precisar de mais de 100 contas de armazenamento, entre em contato com[suporte do Azure](http://azure.microsoft.com/support/faq/)para obter assistência.

<sup>2</sup>O limite total da taxa de solicitação para uma conta de armazenamento é 20.000 IOPS. Se uma máquina virtual utilizar o IOPS máximo por disco, para evitar possíveis limitações, certifique-se de que o total de IOPS em todos os VHDs das máquinas virtuais não exceda o limite da conta de armazenamento (20.000 IOPS).

Basicamente, você pode calcular o número de discos altamente utilizados com suporte de uma única conta de armazenamento com base no limite de transação. Por exemplo, para uma VM da camada Basic, o número máximo de discos altamente utilizados é de aproximadamente 66 (20.000/300 IOPS por disco) e, para uma VM da camada Standard, é de aproximadamente 40 (20.000/500 IOPS por disco). No entanto, observe que a conta de armazenamento pode oferecer suporte a um grande número de discos, caso eles não sejam altamente utilizados ao mesmo tempo.

<sup>3</sup>*Entrada* refere-se a todos os dados (solicitações) que estão sendo enviados a uma conta de armazenamento. *Saída* refere-se a todos os dados (respostas) que estão sendo recebidos de uma conta de armazenamento.

<sup>4</sup>GRS refere-se ao armazenamento com redundância geográfica ZRS refere-se ao armazenamento com redundância de zona, e está disponível somente para blobs de bloco. LRS refere-se ao armazenamento com redundância local.

<!---HONumber=August15_HO7-->