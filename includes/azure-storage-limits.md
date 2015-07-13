<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Recurso<sup>1</sup></th>
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
   <td valign="middle"><p>IOPS máx. de 8 KB por disco persistente (Camada Básica)</p></td>
   <td valign="middle"><p>300<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>IOPS máx. de 8 KB por disco persistente (Camada Padrão)</p></td>
   <td valign="middle"><p>500<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Taxa de solicitação total (presumindo um tamanho de objeto de 1KB) por conta de armazenamento</p></td>
   <td valign="middle"><p>Até 20.000 entidades ou mensagens por segundo</p></td>
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
   <td valign="middle"><p>Ingresso máximo por conta de armazenamento (regiões dos EUA)</p></td>
   <td valign="middle"><p>10 Gbps se o GRS<sup>3</sup> estiver habilitado, 20 Gbps para LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Saída máxima por conta de armazenamento (regiões dos EUA)</p></td>
   <td valign="middle"><p>20 Gbps se o GRS<sup>3</sup> estiver habilitado, 30 Gbps para LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Ingresso máximo por conta de armazenamento (regiões da Ásia e Europa)</p></td>
   <td valign="middle"><p>5 Gbps se o GRS<sup>3</sup> estiver habilitado, 10 Gbps para LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Saída máxima por conta de armazenamento (regiões da Ásia e Europa)</p></td>
   <td valign="middle"><p>10 Gbps se o GRS<sup>3</sup> estiver habilitado, 15 Gbps para LRS</p></td>
</tr>
</table>

<sup>1</sup>Para obter mais detalhes sobre esses limites, consulte [Metas de Desempenho e de Escalabilidade de Armazenamento do Azure](../articles/storage/storage-scalability-targets.md).

<sup>2</sup>Para máquinas virtuais na Camada Básica, não coloque mais de 66 VHDs altamente utilizados em uma conta de armazenamento para evitar o limite de taxa de solicitação total de 20.000 (20.000/300). Para máquinas virtuais na Camada Padrão, não coloque mais de 40 VHDs altamente utilizados em uma conta de armazenamento (20.000/500). Para obter mais informações, veja [Tamanhos de máquina virtual e serviço de nuvem para o Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx).

<sup>3</sup>GRS significa [Armazenamento com Redundância Geográfica](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx). LRS significa [Armazenamento com redundância local](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/08/introducing-locally-redundant-storage-for-windows-azure-storage.aspx). Observe que o GRS também tem redundância local.

<!---HONumber=62-->