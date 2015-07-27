<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Recurso</th>
   <th align="left" valign="middle">Limite padrão</th>
   <th align="left" valign="middle">Limite máximo</th>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">Redes virtuais</a><sup>1</sup> por assinatura</p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>Máquinas totais<sup>2</sup> por rede virtual</p></td>
   <td valign="middle"><p>2.048</p></td>
   <td valign="middle"><p>2.048</p></td>
</tr>
<tr>
   <td valign="middle"><p>Conexões TCP simultâneas para uma máquina virtual ou instância de função</p></td>
   <td valign="middle"><p>500K</p></td>
   <td valign="middle"><p>500K</p></td>
</tr>
<tr>
   <td valign="middle"><p>Listas de Controle de Acesso (ACLs) por ponto de extremidade<sup>3</sup></p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>Sites de rede local por rede virtual</p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>10</p></td>
</tr>
</table>

<sup>1</sup>Cada rede virtual oferece suporte a um único [gateway de rede virtual](http://msdn.microsoft.com/library/azure/jj156210.aspx).

<sup>2</sup>O número total de máquinas inclui máquinas virtuais e instâncias de funções de trabalho/Web.

<sup>3</sup>Há suporte para ACL nos Pontos de Extremidade de Entrada para Máquinas Virtuais. Para funções de trabalho/Web, ACL tem suporte nos pontos de extremidade de entrada de instância e na entrada.

<!---HONumber=July15_HO3-->