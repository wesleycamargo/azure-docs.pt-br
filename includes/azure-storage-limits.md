<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Recurso</th>
   <th align="left" valign="middle">Limite padrão</th>
</tr>
<tr>
   <td valign="middle"><p>TB por conta de armazenamento</p></td>
   <td valign="middle"><p>500 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>Tamanho máximo de um único contêiner de blob, tabela ou fila</p></td>
   <td valign="middle"><p>500 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>Número máximo de contêineres de blob, blobs, compartilhamentos de arquivo, tabelas, filas, entidades ou mensagens por conta de armazenamento</p></td>
   <td valign="middle"><p>O único limite é a capacidade de conta de armazenamento de 500 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>Tamanho máximo de um compartilhamento de arquivo</p></td>
   <td valign="middle"><p>5 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>Número máximo de arquivos em um compartilhamento de arquivo</p></td>
   <td valign="middle"><p>O único limite é a capacidade total de 5 TB do compartilhamento de arquivo</p></td>
</tr>
<tr>
   <td valign="middle"><p>IOPS de no máx. 8 KB por disco persistente (máquina virtual da camada Basic)</p></td>
   <td valign="middle"><p>300<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>IOPS de, no máximo, 8 KB por disco persistente (máquina virtual da camada Standard)</p></td>
   <td valign="middle"><p>500<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Taxa de solicitação total (presumindo um tamanho de objeto de 1KB) por conta de armazenamento</p></td>
   <td valign="middle"><p>Até 20.000 IOPS, entidades por segundo ou mensagens por segundo</p></td>
</tr>
<tr>
   <td valign="middle"><p>Rendimento de destino por blob único</p></td>
   <td valign="middle"><p>Até 60 MB por segundo, ou até 500 solicitações por segundo</p></td>
</tr>
<tr>
   <td valign="middle"><p>Rendimento de destino por fila única (mensagens de 1 KB)</p></td>
   <td valign="middle"><p>Até 2000 mensagens por segundo</p></td>
</tr>
<tr>
   <td valign="middle"><p>Rendimento de destino por partição de tabela única (entidades de 1 KB)</p></td>
   <td valign="middle"><p>Até 2000 entidades por segundo</p></td>
</tr>
<tr>
   <td valign="middle"><p>Taxa de transferência de destino por compartilhamento de arquivo único (Visualização)</p></td>
   <td valign="middle"><p>Até 60 MB por segundo</p></td>
</tr>
<tr>
   <td valign="middle"><p>Entrada máxima<sup>2</sup> por conta de armazenamento (regiões dos EUA)</p></td>
   <td valign="middle"><p>10 Gbps se o GRS<sup>3</sup> estiver habilitado, 20 Gbps para LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Saída máxima<sup>2</sup> por conta de armazenamento (regiões dos EUA)</p></td>
   <td valign="middle"><p>20 Gbps se o GRS<sup>3</sup> estiver habilitado, 30 Gbps para LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Entrada máxima<sup>2</sup> por conta de armazenamento (regiões da Ásia e Europa)</p></td>
   <td valign="middle"><p>5 Gbps se o GRS<sup>3</sup> estiver habilitado, 10 Gbps para LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Saída máxima<sup>2</sup> por conta de armazenamento (regiões da Ásia e Europa)</p></td>
   <td valign="middle"><p>10 Gbps se o GRS<sup>3</sup> estiver habilitado, 15 Gbps para LRS</p></td>
</tr>
</table>

<sup>1</sup>O limite total da taxa de solicitação para uma conta de armazenamento é 20.000 IOPS. Se uma máquina virtual utilizar o IOPS máximo por disco, para evitar possíveis limitações, certifique-se de que o total de IOPS em todos os VHDs das máquinas virtuais não exceda o limite da conta de armazenamento (20.000 IOPS).

Basicamente, você pode calcular o número de discos altamente utilizados com suporte de uma única conta de armazenamento com base no limite de transação. Por exemplo, para uma VM da camada Basic, o número máximo de discos altamente utilizados é de aproximadamente 66 (20.000/300 IOPS por disco) e, para uma VM da camada Standard, é de aproximadamente 40 (20.000/500 IOPS por disco). No entanto, observe que a conta de armazenamento pode oferecer suporte a um grande número de discos, caso eles não sejam altamente utilizados ao mesmo tempo.

<sup>2</sup>\*Entrada\* refere-se a todos os dados (solicitações) que estão sendo enviados a uma conta de armazenamento. *Saída* refere-se a todos os dados (respostas) que estão sendo recebidos de uma conta de armazenamento.

<sup>3</sup>GRS refere-se a um armazenamento com redundância geográfica, enquanto LRS refere-se ao armazenamento com redundância local.

<!---HONumber=July15_HO5-->