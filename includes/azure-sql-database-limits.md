<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Recurso</th>
   <th align="left" valign="middle">Limite padrão</th>
</tr>
<tr>
   <td valign="middle"><p>Tamanho do banco de dados</p></td>
   <td valign="middle"><p>Depende do nível de desempenho <sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Logons</p></td>
   <td valign="middle"><p>Depende do nível de desempenho <sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Uso de memória</p></td>
   <td valign="middle"><p>Concessão de memória de 16 MB por mais de 20 segundos</p></td>
</tr>
<tr>
   <td valign="middle"><p>Sessões</p></td>
   <td valign="middle"><p>Depende do nível de desempenho <sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Tamanho do tempdb</p></td>
   <td valign="middle"><p>5 GB</p></td>
</tr>
<tr>
   <td valign="middle"><p>Duração da transação</p></td>
   <td valign="middle"><p>24 horas<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Bloqueios por transação</p></td>
   <td valign="middle"><p>1 milhão</p></td>
</tr>
<tr>
   <td valign="middle"><p>Tamanho por transação</p></td>
   <td valign="middle"><p>2 GB</p></td>
</tr>
<tr>
   <td valign="middle"><p>Porcentagem do espaço total de log usado por transação</p></td>
   <td valign="middle"><p>20%</p></td>
</tr>
<tr>
   <td valign="middle"><p>Máximo de solicitações simultâneas (threads de trabalho)</p></td>
   <td valign="middle"><p>Depende do nível de desempenho <sup>1</sup></p></td>
</tr>
</table>

<sup>1</sup>O Banco de Dados SQL apresenta camadas de desempenho Basic, Standard e Premium. Standard e Premium também são divididos em níveis de desempenho. Para obter limites detalhados por camada e nível de serviço, consulte[Camadas de serviço e níveis de desempenho do Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/dn741336.aspx).

<sup>2</sup>Se a transação bloquear um recurso exigido por uma tarefa de sistema subjacente, a duração máxima será de 20 segundos.

<!---HONumber=July15_HO3-->