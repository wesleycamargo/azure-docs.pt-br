---
title: "Criar e restaurar um backup nos Serviços BizTalk | Microsoft Docs"
description: "Os serviços BizTalk incluem backup e restauração. Saiba como criar e restaurar um backup e determinar do que é feito backup. MABS, WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 59f91173-4683-48df-abd5-41262bfce6df
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 45365092f5bcd1a8d309c10404a7437c494a8967
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2017
---
# <a name="biztalk-services-backup-and-restore"></a>Serviços BizTalk: backup e restauração

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Os Serviços BizTalk do Azure incluem recursos de backup e restauração. 

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

> [!NOTE]
> NÃO se faz backup das conexões híbridas, independentemente da Edição. Você deve recriar suas conexões híbridas.


## <a name="before-you-begin"></a>Antes de começar
* É possível que o backup e a restauração não estejam disponíveis para todas as edições. Consulte [Serviços BizTalk: gráfico de edições](biztalk-editions-feature-chart.md).
* O conteúdo de backup pode ser restaurado para o mesmo Serviço do BizTalk ou para um novo Serviço do BizTalk. Para restaurar o Serviço do BizTalk usando o mesmo nome, o Serviço do BizTalk existente precisa ser excluído e o nome precisa estar disponível. Após excluir um Serviço do BizTalk, pode levar mais tempo do que o desejado para que o mesmo nome esteja disponível. Se você não puder esperar para o mesmo nome esteja disponível, faça a restauração para um novo Serviço do BizTalk.
* Os Serviços do BizTalk podem ser restaurados para a mesma edição ou para uma edição superior. Não há suporte para a restauração dos Serviços do BizTalk para uma edição inferior, de quando foi feito um backup.
  
    Por exemplo, um backup usando a Basic Edition pode ser restaurado para a Premium Edition. Um backup usando a Premium Edition não pode ser restaurado para a Standard Edition.
* O backup dos números de controle de EDI são feitos para manter a continuidade dos números de controle. Se as mensagens forem processadas depois do último backup, a restauração desse conteúdo de backup pode provocar a duplicação dos números de controle.
* Se um lote tiver mensagens ativas, processe o lote **antes** de executar um backup. Ao criar um backup (conforme necessário ou agendado), as mensagens em lotes nunca são armazenadas. 
  
    **Se um backup for feito com mensagens ativas em um lote, o backup dessas mensagens não será feito e, portanto, elas serão perdidas.**
* Opcional: no Portal dos Serviços BizTalk, interrompa todas as operações de gerenciamento.

## <a name="create-a-backup"></a>Criar um backup
Um backup pode ser obtido a qualquer momento e é totalmente controlado por você. Para criar um backup, use o [API REST para gerenciar Serviços BizTalk no Azure](https://msdn.microsoft.com/library/azure/dn232347.aspx).

## <a name="restore"></a>Restaurar
Para criar um backup, use o [API REST para gerenciar Serviços BizTalk no Azure](https://msdn.microsoft.com/library/azure/dn232347.aspx).

### <a name="postrestore"></a>Depois de restaurar um backup
O Serviço BizTalk sempre é restaurado em um estado **Suspenso** . Neste estado, você pode fazer qualquer alteração de configuração antes que o novo ambiente esteja funcional, incluindo:

* Se você tiver criado aplicativos do Serviço BizTalk usando o SDK dos Serviços BizTalk do Azure, será necessário atualizar as credenciais de ACS (Controle de Acesso) nesses aplicativos para trabalhar com o ambiente restaurado.
* Você restaura um Serviço do BizTalk para replicar um ambiente de Serviço do BizTalk existente. Nesta situação, se houver contratos configurados no portal original de Serviços do BizTalk que usam uma pasta FTP de origem, é possível que precise atualizar os contratos no ambiente recentemente restaurado para usar uma pasta FTP de origem diferente. De outra forma, pode haver dois contratos diferentes tentando puxar a mesma mensagem.
* Se você fez a restauração para ter vários ambientes do Serviço do BizTalk, certifique-se de ter como destino o ambiente certo nos aplicativos do Visual Studio, cmdlets do PowerShell, APIs REST ou APIs de OM de Gerenciamento de Parceiros Comerciais.
* É uma boa prática configurar backups automáticos no ambiente do Serviço do BizTalk Service recém-restaurado.

## <a name="what-gets-backed-up"></a>Do que é feito backup
Quando um backup é criado, os seguintes itens são copiados:

<table border="1"> 
<tr bgcolor="FAF9F9">
<th> </th>
<TH>Itens do backup</TH> 
</tr> 
<tr>
<td colspan="2">
 <strong>Portal de Serviços BizTalk do Azure</strong></td>
</tr> 
<tr>
<td>Configuração e tempo de execução</td> 
<td>
<ul>
<li>Detalhes e perfil do parceiro</li>
<li>Contratos de parceiro</li>
<li>Assemblies personalizados implantados</li>
<li>Pontes implantadas</li>
<li>Certificados</li>
<li>Transformações implantadas</li>
<li>Pipelines</li>
<li>Modelos criados e salvos no Portal de Serviços BizTalk</li>
<li>Mapeamentos de X12 ST01 e GS01</li>
<li>Números de controle (EDI)</li>
<li>Valores de MIC de mensagens AS2</li>
</ul>
</td>
</tr> 

<tr>
<td colspan="2">
 <strong>Serviço BizTalk do Azure</strong></td>
</tr> 
<tr>
<td>Certificado SSL</td> 
<td>
<ul>
<li>Dados do certificado SSL</li>
<li>Senha do certificado SSL</li>
</ul>
</td>
</tr> 
<tr>
<td>Configurações do Serviço do BizTalk</td> 
<td>
<ul>
<li>Contagem de unidades da escala</li>
<li>Edição</li>
<li>Versão do produto</li>
<li>Região/Datacenter</li>
<li>O namespace e a chave do ACS (Serviço de Controle de Acesso)</li>
<li>Cadeia de conexão do banco de dados de acompanhamento</li>
<li>Cadeia de conexão da conta de armazenamento de arquivamento</li>
<li>Cadeia de conexão da conta de armazenamento de monitoramento</li>
</ul>
</td>
</tr> 
<tr>
<td colspan="2">
 <strong>Itens Adicionais</strong></td>
</tr> 
<tr>
<td>Banco de Dados de Acompanhamento</td> 
<td>Quando o Serviço do BizTalk é criado, os detalhes do Banco de Dados de Acompanhamento são inseridos, incluindo o Servidor do Banco de Dados SQL do Azure e o nome do Banco de Dados de Acompanhamento. O backup do Banco de Dados de Acompanhamento não é feito automaticamente.
<br/><br/>
<strong>Importante</strong><br/>
Se o Banco de Dados de Acompanhamento for excluído e o banco de dados precisar ser recuperado, deverá existir um backup anterior. Se não existir um backup, o Banco de Dados de Acompanhamento e seus dados não poderão ser recuperados. Nessa situação, crie um novo Banco de Dados de Acompanhamento com o mesmo nome do banco de dados. A Replicação Geográfica é recomendada.</td>
</tr> 
</table>

## <a name="next"></a>Avançar
Para criar Serviços BizTalk do Azure, vá para [Serviços BizTalk: Provisionamento](http://go.microsoft.com/fwlink/p/?LinkID=302280). Para começar a criar aplicativos, visite [Serviços BizTalk do Azure (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="see-also"></a>Consulte também
* [Fazer o backup do Serviço BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584)
* [Restaurar o Serviço BizTalk do backup](http://go.microsoft.com/fwlink/p/?LinkID=325582)
* [Serviços BizTalk: gráfico das edições Developer, Basic, Standard e Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)
* [Serviços BizTalk: Provisionamento](http://go.microsoft.com/fwlink/p/?LinkID=302280)
* [Serviços BizTalk: gráfico do status do provisionamento](http://go.microsoft.com/fwlink/p/?LinkID=329870)
* [Serviços BizTalk: guias Painel, Monitor e Escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)
* [Serviços BizTalk: limitação](http://go.microsoft.com/fwlink/p/?LinkID=302282)
* [Serviços BizTalk: nome e chave do emissor](http://go.microsoft.com/fwlink/p/?LinkID=303941)
* [Como começar a usar o SDK dos Serviços BizTalk do Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[BackupStatus]: ./media/biztalk-backup-restore/status-last-backup.png
[Restore]: ./media/biztalk-backup-restore/restore-ui.png
[AutomaticBU]: ./media/biztalk-backup-restore/AutomaticBU.png
[RestoreBizTalkService]: ./media/biztalk-backup-restore/RestoreBizTalkServiceWindow.png

