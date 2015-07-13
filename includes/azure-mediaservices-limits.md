<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Recurso</th>
   <th align="left" valign="middle">Limite padrão</th>
   <th align="left" valign="middle">Limite máximo</th>
</tr>
<tr>
   <td valign="middle"><p>AMS (Contas de Serviços de Mídia) do Azure em uma única assinatura</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>25</p></td>
</tr>
<tr>
   <td valign="middle"><p>Ativos por conta AMS</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>1.000.000</p></td>
</tr>
<tr>
   <td valign="middle"><p>Tarefas encadeadas por trabalho</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>30</p></td>
</tr>
<tr>
   <td valign="middle"><p>Ativos por tarefa</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>Ativos por trabalho</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>Trabalhos por conta AMS </p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>50.000<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Localizadores exclusivos associados a um ativo simultaneamente</p></td>
   <td valign="middle"><p></p></td>
   <td valign="middle"><p>5<sup>4</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Canais ao vivo por conta AMS </p></td>
   <td valign="middle"><p>5</p></td>
   <td valign="middle"><p>N/A<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Programas no estado “interrompido” por canal </p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>N/A<sup>1</sup></p></td>
</tr><tr>
   <td valign="middle"><p>Programas no estado “executando” por canal </p></td>
   <td valign="middle"><p>3</p></td>
   <td valign="middle"><p>N/A<sup>1</sup></p></td>
</tr><tr>
   <td valign="middle"><p>Pontos de extremidade de streaming no estado “executando” por conta AMS</p></td>
   <td valign="middle"><p>2</p></td>
   <td valign="middle"><p>N/A<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Unidades de streaming por ponto de extremidade de streaming </p></td>
   <td valign="middle"><p>10 </p></td>
   <td valign="middle"><p>N/A<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Unidades de codificação por conta AMS </p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>N/A<sup>1</sup></p></td>
</tr>
</table>

<sup>1</sup> Você pode solicitar para atualizar os limites para essa cota abrindo um tíquete de suporte. Não crie mais contas AMS para aumentar os limites; em vez disso, envie um tíquete de suporte.

<sup>2</sup> Esse número inclui trabalhos em fila, concluídos, ativos e cancelados. Ele não inclui trabalhos excluídos. Você pode excluir os trabalhos antigos usando solicitação HTTP de **IJob.Delete** ou **DELETE**.

<sup>3</sup> Ao fazer uma solicitação para listar as Entidades de trabalho, será retornado um máximo de 1.000 por solicitação. Se você precisar manter o controle dos de todos os trabalhos enviados, você pode usar superior/pular conforme descrito em [Opções de consulta OData sistema](http://msdn.microsoft.com/library/gg309461.aspx).

<sup>4</sup> Os localizadores não foram desenvolvidos para gerenciar o controle de acesso por usuário. Para conceder direitos de acesso diferentes para usuários individuais, use as soluções de gerenciamento de direitos digitais (DRM).

<!---HONumber=62-->