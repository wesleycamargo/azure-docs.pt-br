<properties linkid="manage-services-biztalk-state-chart" urlDisplayName="BizTalk Services: Service state chart" pageTitle="BizTalk Services: Service state chart | Azure" metaKeywords="" description="" metaCanonical="" services="biztalk-services" documentationCenter="" title="BizTalk Services: Service state chart" authors="mandia" solutions="integration" manager="dwrede" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/10/2014" ms.author="mandia" />

# Serviços do BizTalk: Gráfico de estado de serviço

Dependendo do estado atual do Serviço BizTalk, há operações que podem ou não serem executadas no Serviço BizTalk.

Por exemplo, você pode provisionar um novo Serviço BizTalk no Portal de Gerenciamento do Azure. Quando for concluído com êxito, o serviço BizTalk estará no estado Ativo. No estado Ativo, você pode parar o serviço BizTalk. Se a parada for bem-sucedida, o Serviço BizTalk irá para o estado de Parado. Se houver falha na parada, o Serviço BizTalk irá para o estado StopFailed. No estado StopFailed, você pode reiniciar o Serviço BizTalk. Se você tentar uma operação que não é permitida, como reiniciar o Serviço BizTalk, ocorrerá o seguinte erro:

**Operação não permitida**

Para provisionar o Serviço BizTalk, consulte [BizTalk Services: provisionando usando o Portal de Gerenciamento do Azure][BizTalk Services: provisionando usando o Portal de Gerenciamento do Azure].

As tabelas a seguir listam as operações que podem ser executadas quando o Serviço BizTalk está em um estado específico. Uma marca de seleção significa que a operação pode ser executada enquanto estiver nesse estado. Uma entrada em branco significa que a operação não pode ser executada enquanto estiver nesse estado.

#### Operações Iniciar, Parar, Reiniciar, Suspender, Continuar e Excluir

<table border="1">
<tr>
<th colspan="15">
Operação

</th>
</tr>
<tr>
<th rowspan="18">
Estado do Serviço BizTalk

</th>
</tr>
<tr bgcolor="FAF9F9">
<th>
</th>
<th>
Iniciar

</th>
<th>
Parar

</th>
<th>
Reiniciar

</th>
<th>
Suspender

</th>
<th>
Continuar

</th>
<th>
Excluir

</th>
</tr>
<tr>
<td bgcolor="FAF9F9">
**Ativo**

</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**Desabilitado**

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**Suspenso**

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**Parada**

</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**Falha na atualização do serviço**

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**DisableFailed**

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**EnableFailed**

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**StartFailed
 StopFailed
 RestartFailed**

</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**SuspendedFailed
 ResumeFailed**

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**CreatedFailed
 RestoreFailed
**

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**ConfigUpdateFailed**

</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**ScaleFailed**

</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
</table>

#### Operações de Escala, Atualização, Configuração e Backup

<table border="1">
<tr>
<th colspan="15">
Operação

</th>
</tr>
<tr>
<th rowspan="18">
Estado do Serviço BizTalk

</th>
</tr>
<tr bgcolor="FAF9F9">
<th>
</th>
<th>
Escala

</th>
<th>
Atualizar a configuração

</th>
<th>
Backup

</th>
</tr>
<tr>
<td bgcolor="FAF9F9">
**Ativo**

</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**Desabilitado**

</td>
<td>
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**Suspenso**

</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**Parada**

</td>
<td>
</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**Falha na atualização do serviço**

</td>
<td>
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**DisableFailed**

</td>
<td>
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**EnableFailed**

</td>
<td>
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**StartFailed
 StopFailed
 RestartFailed**

</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**SuspendedFailed
 ResumeFailed**

</td>
<td>
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**CreatedFailed
 RestoreFailed
**

</td>
<td>
</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**ConfigUpdateFailed**

</td>
<td>
</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
</tr>
<tr>
<td bgcolor="FAF9F9">
**ScaleFailed**

</td>
<td>
<center>
x

</center>
</td>
<td>
</td>
<td>
</td>
</tr>
</table>
## Consulte também

-   [Serviços do BizTalk: provisionamento usando o Portal de Gerenciamento do Azure][BizTalk Services: provisionando usando o Portal de Gerenciamento do Azure]
-   [Serviços do BizTalk: guias Painel, Monitoramento e Escala][Serviços do BizTalk: guias Painel, Monitoramento e Escala]
-   [Serviços do BizTalk: tabela de edições Developer, Basic, Standard e Premium][Serviços do BizTalk: tabela de edições Developer, Basic, Standard e Premium]
-   [Serviços do BizTalk: Backup e restauração][Serviços do BizTalk: Backup e restauração]
-   [Serviços do BizTalk: limitação][Serviços do BizTalk: limitação]
-   [Serviços do BizTalk: nome e chave do emissor][Serviços do BizTalk: nome e chave do emissor]
-   [Como começar a usar o SDK dos Serviços BizTalk do Azure (a página pode estar em inglês)][Como começar a usar o SDK dos Serviços BizTalk do Azure (a página pode estar em inglês)]

  [BizTalk Services: provisionando usando o Portal de Gerenciamento do Azure]: http://go.microsoft.com/fwlink/p/?LinkID=302280
  [Serviços do BizTalk: guias Painel, Monitoramento e Escala]: http://go.microsoft.com/fwlink/p/?LinkID=302281
  [Serviços do BizTalk: tabela de edições Developer, Basic, Standard e Premium]: http://go.microsoft.com/fwlink/p/?LinkID=302279
  [Serviços do BizTalk: Backup e restauração]: http://go.microsoft.com/fwlink/p/?LinkID=329873
  [Serviços do BizTalk: limitação]: http://go.microsoft.com/fwlink/p/?LinkID=302282
  [Serviços do BizTalk: nome e chave do emissor]: http://go.microsoft.com/fwlink/p/?LinkID=303941
  [Como começar a usar o SDK dos Serviços BizTalk do Azure (a página pode estar em inglês)]: http://go.microsoft.com/fwlink/p/?LinkID=302335
