<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Recurso</th>
   <th align="left" valign="middle">Limite padrão</th>
   <th align="left" valign="middle">Limite máximo</th>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/documentation/services/virtual-machines/">Máquinas virtuais</a> por serviço de nuvem<sup>1</sup></p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>Pontos de extremidade por serviço de nuvem<sup>2</sup></p></td>
   <td valign="middle"><p>150</p></td>
   <td valign="middle"><p>150</p></td>
</tr>
</table>

<sup>1</sup>Quando você cria uma máquina virtual fora de um Grupo de Recursos do Azure, um serviço de nuvem é criado automaticamente para conter a máquina. Então, é possível adicionar várias máquinas virtuais no mesmo serviço de nuvem.

<sup>2</sup>Os pontos de extremidade são utilizados para permitir a comunicação com as máquinas virtuais externas que contêm o serviço de nuvem. As máquinas virtuais dentro do mesmo serviço de nuvem permitem automaticamente a comunicação entre todas as portas TCP e UDP para comunicação interna.

<!---HONumber=62-->