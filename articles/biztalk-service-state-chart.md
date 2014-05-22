<properties linkid="manage-services-biztalk-state-chart" urlDisplayName="Serviços BizTalk: Gráfico de estado do serviço" pageTitle="Serviços BizTalk: gráfico de estado do serviço | Azure" metaKeywords="" description="" metaCanonical="" services="biztalk-services" documentationCenter="" title="Serviços BizTalk: gráfico de estado do serviço" authors="mandia" solutions="" manager="paulettm" editor="cgronlun" />



# Serviços BizTalk: gráfico de estado do serviço
Dependendo do estado atual do Serviço BizTalk, há operações que podem ou não serem executadas no Serviço BizTalk.

Por exemplo, você pode provisionar um novo Serviço BizTalk no Portal de Gerenciamento do Azure. Quando for concluído com êxito, o serviço BizTalk estará no estado Ativo. No estado Ativo, você pode parar o serviço BizTalk. Se a parada for bem-sucedida, o Serviço BizTalk irá para o estado de Parado. Se houver falha na parada, o Serviço BizTalk irá para o estado StopFailed. No estado StopFailed, você pode reiniciar o Serviço BizTalk. Se você tentar uma operação que não é permitida, como reiniciar o Serviço BizTalk, ocorrerá o seguinte erro:

**Operação não permitida**

Para provisionar o Serviço BizTalk, consulte [BizTalk Services: provisionando usando o Portal de Gerenciamento do Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280).

As tabelas a seguir listam as operações que podem ser executadas quando o Serviço BizTalk está em um estado específico. Uma marca de seleção significa que a operação pode ser executada enquanto estiver nesse estado. Uma entrada em branco significa que a operação não pode ser executada enquanto estiver nesse estado.

#### Operações Iniciar, Parar, Reiniciar, Suspender, Continuar e Excluir
<table border="1">
<tr>
        <th colspan="15">Operação</th>
</tr>

<tr>
        <th rowspan="18">Estado do Serviço BizTalk</th>
</tr>
<tr bgcolor="FAF9F9">
        <th> </th>
        <th>Iniciar</th>
        <th>Parar</th>
        <th>Reiniciar</th>
        <th>Suspender</th>
        <th>Continuar</th>
        <th>Excluir</th>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Ativo</b></td>
<td> </td>
<td><center>x</center></td>
<td><center>x</center></td>
<td><center>x</center></td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Desabilitado</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Suspenso</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Parada</b></td>
<td><center>x</center></td>
<td> </td>
<td><center>x</center></td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Falha na atualização do serviço.</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>DisableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>EnableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>StartFailed<br/>
StopFailed<br/>
RestartFailed</b></td>
<td><center>x</center></td>
<td><center>x</center></td>
<td><center>x</center></td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>SuspendedFailed<br/>
ResumeFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
<td><center>x</center></td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>CreatedFailed<br/>
RestoreFailed<br/></b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ConfigUpdateFailed</b></td>
<td> </td>
<td> </td>
<td><center>x</center></td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ScaleFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
</table>
<br/>

####Operações de Escala, Atualização, Configuração e Backup
<table border="1">
<tr>
        <th colspan="15">Operação</th>
</tr>

<tr>
        <th rowspan="18">Estado do Serviço BizTalk</th>
</tr>
<tr bgcolor="FAF9F9">
        <th> </th>
        <th>Escala</th>
        <th>Atualizar a configuração</th>
        <th>Backup</th>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Ativo</b></td>
<td><center>x</center></td>
<td><center>x</center></td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Desabilitado</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Suspenso</b></td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Parada</b></td>
<td> </td>
<td> </td>
<td><center>x</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Falha na atualização do serviço</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>DisableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>EnableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>StartFailed<br/>
StopFailed<br/>
RestartFailed</b></td>
<td> </td>
<td><center>x</center></td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>SuspendedFailed<br/>
ResumeFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>CreatedFailed<br/>
RestoreFailed<br/></b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ConfigUpdateFailed</b></td>
<td> </td>
<td><center>x</center></td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ScaleFailed</b></td>
<td><center>x</center></td>
<td> </td>
<td> </td>
</tr>
</table>

## Consulte também
- [Serviços BizTalk: provisionamento usando o Portal de Gerenciamento do Azure (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Serviços BizTalk: guias Painel, Monitor e Escala (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [Serviços BizTalk: gráfico das edições Developer, Basic, Standard e Premium (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Serviços BizTalk: backup e restauração (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [Serviços BizTalk: limitação](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
- [Serviços BizTalk: nome e chave do emissor (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
- [Como começar a usar o SDK dos Serviços BizTalk do Azure (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=302335)



