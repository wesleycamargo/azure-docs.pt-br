| Recurso | Limite padrão |
| --- | --- |
| Número máximo de contas de armazenamento por assinatura | 200<sup>1</sup> |
| Capacidade máxima da conta de armazenamento | 500 TiB<sup>2</sup> |
| Número máximo de contêineres de blob, blobs, compartilhamentos de arquivo, tabelas, filas, entidades ou mensagens por conta de armazenamento | Sem limite |
| Entrada máxima<sup>3</sup> por conta de armazenamento (Regiões dos EUA) | 10 Gbps se o GRS/ZRS<sup>4</sup> estiver habilitado, 20 Gbps para o LRS<sup>2</sup> |
| Saída máxima<sup>3</sup> por conta de armazenamento (Regiões dos EUA) | 20 Gbps se o RA-GRS/GRS/ZRS<sup>4</sup> estiver habilitado, 30 Gbps para o LRS<sup>2</sup> |
| Entrada máxima<sup>3</sup> por conta de armazenamento (Regiões fora dos EUA) | 5 Gbps se o GRS/ZRS<sup>4</sup> estiver habilitado, 10 Gbps para o LRS<sup>2</sup> |
| Saída máxima<sup>3</sup> por conta de armazenamento (Regiões fora dos EUA) | 10 Gbps se o RA-GRS/GRS/ZRS<sup>4</sup> estiver habilitado, 15 Gbps para o LRS<sup>2</sup> |

<sup>1</sup>Inclui contas de armazenamento Standard e Premium. Se você precisar de mais de 200 contas de armazenamento, faça uma solicitação por meio do [Suporte do Azure](https://azure.microsoft.com/support/faq/). A equipe de Armazenamento do Azure examinará seu caso de negócios e poderá aprovar até 250 contas de armazenamento. 

<sup>2</sup> Para que suas contas de armazenamento padrão cresçam além dos limites anunciados na taxa de capacidade, entrada/saída e solicitação, faça uma solicitação através do [Suporte do Azure](https://azure.microsoft.com/support/faq/). A equipe de Armazenamento do Azure examinará a solicitação e poderá aprovar limites maiores caso a caso.

<sup>3</sup> Limitado somente pelos limites de entrada/saída da conta. A *entrada* refere-se a todos os dados (solicitações) que estão sendo enviados a uma conta de armazenamento. *Saída* refere-se a todos os dados (respostas) recebidos de uma conta de armazenamento.  

<sup>4</sup>As opções de redundância de Armazenamento do Azure incluem:
* **RA-GRS**: armazenamento com redundância geográfica com acesso de leitura. Se o RA-GRS estiver habilitado, os destinos de saída para o local secundário serão idênticos àqueles para o local principal.
* **GRS**: armazenamento com redundância geográfica. 
* **ZRS**: armazenamento com redundância de zona. Disponível apenas para blobs de blocos. 
* **LRS**: armazenamento com redundância local. 