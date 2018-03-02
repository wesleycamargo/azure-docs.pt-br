---
title: "Restaurar um banco de dados SQL do Azure em um aplicativo SaaS multilocatário | Microsoft Docs"
description: "Saiba como restaurar um banco de dados do SQL de locatário único após a exclusão acidental de dados"
keywords: tutorial do banco de dados SQL
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: billgib;sstein
ms.openlocfilehash: 46471073f88247510f45d6c4152afa43be6e1aaa
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/22/2018
---
# <a name="restore-a-single-tenant-with-a-database-per-tenant-saas-application"></a>Restaurar um único locatário com um aplicativo de banco de dados por locatário SaaS

O modelo de banco de dados por locatário facilita a restauração de um único locatário a um ponto anterior sem afetar outros locatários.

Neste tutorial, você aprenderá dois padrões de recuperação de dados:

> [!div class="checklist"]

> * Restaurar um banco de dados em um banco de dados paralelo (lado a lado)
> * Restaurar um banco de dados no local substituindo o banco de dados existente


|||
|:--|:--|
| **Restaurar em um banco de dados paralelo** | Esse padrão pode ser usado para revisão, auditoria, conformidade e afins para permitir que um locatário inspecione os dados de um ponto anterior.  O banco de dados atual do locatário permanece inalterado e online. |
| **Restaurar no local** | Esse padrão geralmente é usado para recuperar um locatário para um ponto anterior, depois que ele exclui ou corrompe dados acidentalmente. O banco de dados original é colocado off-line e substituído pelo banco de dados restaurado. |
|||

Para concluir este tutorial, verifique se todos os pré-requisitos a seguir são atendidos:

* O aplicativo SaaS Wingtip é implantado. Para implantar em menos de cinco minutos, confira [Implantar e explorar o aplicativo de SaaS do Wingtip](saas-dbpertenant-get-started-deploy.md)
* O Azure PowerShell está instalado. Para obter detalhes, consulte [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-saas-tenant-restore-patterns"></a>Introdução aos padrões de restauração de locatário de SaaS

Há dois padrões simples para restaurar dados de um locatário individual. Como os bancos de dados de locatário são isolados uns dos outros, restaurar um locatário não afeta os dados de outro locatário.  O recurso PITR (restauração pontual) do Banco de Dados SQL é usado em ambos os padrões.  A PITR sempre cria um novo banco de dados.   

No primeiro padrão, **restaurar em paralelo**, um novo banco de dados paralelo é criado junto com o banco de dados atual do locatário. O locatário recebe acesso somente leitura para o banco de dados restaurado. Os dados restaurados podem ser revisados e possivelmente usados para substituir os valores de dados atuais. Cabe ao designer do aplicativo determinar como o locatário acessa o banco de dados restaurado e quais opções de recuperação são fornecidas. Simplesmente permitir que o locatário revise seus dados em um ponto anterior pode ser suficiente em alguns cenários. 

O segundo padrão, **restaurar no local**, é útil se os dados são perdidos ou corrompidos e locatário deseja reverter para um ponto anterior.  O locatário fica offline enquanto o banco de dados é restaurado. O banco de dados original é excluído e o banco de dados restaurado é renomeado. A cadeia de backup do banco de dados original permanece acessível após a exclusão, permitindo que você restaure o banco de dados para um ponto anterior, se necessário.

Se o banco de dados usa a [Replicação geográfica](sql-database-geo-replication-overview.md) e a restauração em paralelo, recomendamos copiar os dados necessários da cópia restaurada para o banco de dados original. Se substituir o banco de dados original pelo banco de dados restaurado, reconfigure e ressincronize a replicação geográfica.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obter os scripts do aplicativo Wingtip Tickets SaaS Database Per Tenant

Os scripts e o código-fonte do aplicativo SaaS de Banco de Dados Multilocatário Wingtip Tickets estão disponíveis no repositório [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) do GitHub. Confira as [diretrizes gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter as etapas para baixar e desbloquear os scripts SaaS do Wingtip Tickets.

## <a name="before-you-start"></a>Antes de começar

Quando um banco de dados é criado, ele pode levar 10 a 15 minutos para disponibilizar o primeiro backup completo para restauração.  Se você acabou de instalar o aplicativo, talvez seja necessário aguardar alguns minutos antes de tentar esse cenário.

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>Simular um locatário excluindo acidentalmente os dados

Para demonstrar esses cenários de recuperação, precisamos excluir *‘acidentalmente’* alguns dados em um dos bancos de dados de locatário. 

### <a name="open-the-events-app-to-review-the-current-events"></a>Abra o aplicativo de eventos para analisar os eventos atuais

1. Abra o *Hub de eventos* (http://events.wtp.&lt;user&gt;.trafficmanager.net) e clique em **Sala de concerto Contoso**:

   ![hub de eventos](media/saas-dbpertenant-restore-single-tenant/events-hub.png)

1. Role a lista de eventos e anote o último evento na lista:

   ![último evento](media/saas-dbpertenant-restore-single-tenant/last-event.png)


### <a name="accidentally-delete-the-last-event"></a>Excluir 'acidentalmente' o último evento

1. Abra ... \\Módulos de aprendizado\\Continuidade de negócios e recuperação de desastres\\RestoreTenant\\*Demo-RestoreTenant.ps1* no *PowerShell ISE*e defina o seguinte valor:
   * **$DemoScenario** = **1**, Excluir o último evento (sem venda de ingressos).
1. Pressione **F5** para executar o script e excluir o último evento. Você verá a seguinte mensagem de confirmação:

   ```Console
   Deleting last unsold event from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

1. A página de eventos da Contoso abre. Role para baixo e verifique se o evento não existe mais. Se o evento ainda na lista, clique em atualizar e verifique se ele não existe mais.

   ![último evento](media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)


## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>Restaurar um banco de dados do locatário em paralelo com o banco de dados de produção

Este exercício restaura o banco de dados de sala de concertos Contoso para um ponto antes da exclusão do evento. Este cenário presume que você só deseja revisar os dados excluídos em um banco de dados paralelo.

 O script *Restore-TenantInParallel.ps1* cria um banco de dados de locatário paralelo e um banco de dados de locatário paralelo chamado *ContosoConcertHall\_old* com uma entrada no catálogo paralelo. Esse padrão de restauração é mais adequado para a recuperação de perda de dados secundários ou caso precise rever os dados para fins de auditoria ou de conformidade. Também é a abordagem recomendada quando você estiver usando [replicação geográfica](sql-database-geo-replication-overview.md).

1. Conclua a seção [simular um locatário excluindo dados acidentalmente](#simulate-a-tenant-accidentally-deleting-data).
1. No *ISE do PowerShell*, abra ... \\Módulos de aprendizado\\Continuidade de negócios e recuperação de desastre\\RestoreTenant\\_Demo-RestoreTenant.ps1_.
1. Defina **$DemoScenario** = **2**, *Restaurar locatário em paralelo*.
1. Pressione **F5** para executar o script.

O script restaura o banco de dados de locatário para um ponto anterior antes de excluir o evento. O banco de dados é restaurado para o novo banco de dados denominado _ContosoConcertHall\_old_. Os metadados de catálogo existentes nesse banco de dados restaurado é excluído e o banco de dados é adicionado ao catálogo usando uma chave construída a partir do nome *ContosoConcertHall\_old*.

O script de demonstração abre a página de eventos para esse novo banco de dados de locatário em seu navegador. Nota da URL: ```http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old``` que essa página está mostrando os dados do banco de dados restaurado onde *_old* é adicionado ao nome.

Role os eventos listados no navegador para confirmar se o evento excluído na seção anterior foi restaurado.

Observe que expor o locatário restaurado como um locatário adicional, com seu próprio aplicativo de eventos, é improvável ser como você forneceria um acesso de locatário a dados restaurados, mas serve para ilustrar o padrão de restauração. Na realidade, você provavelmente concederia acesso somente leitura aos dados antigos e reteria esse banco de dados restaurado por um período específico. No exemplo, você pode excluir a entrada de locatário restaurado quando terminar, executando o cenário _Remover locatário restaurado_.

1. Defina **$DemoScenario** = **4**, *Remover locatário restaurado*
1. **Execute** **usando** **F5**
1. A entrada *ContosoConcertHall\_old* agora foi excluída do catálogo. Continue e feche a página de eventos para este locatário no seu navegador.


## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>Restaurar um locatário no local, substituindo o banco de dados existente do locatário

Este exercício restaura o locatário de sala de concertos Contoso para um ponto antes da exclusão do evento. O script *Restore-TenantInPlace* restaura um banco de dados de locatário para um novo banco de dados de script e exclui o original.  Esse padrão de restauração é mais adequado para a recuperação de dados corrompidos graves, pois pode haver perda significativa de dados que o locatário precisa acomodar.

1. Abra o arquivo **Demo-RestoreTenant.ps1** no ISE do PowerShell
1. Defina **$DemoScenario** = **5**, *Restaurar locatário no local*.
1. Execute usando **F5**.

O script restaura o banco de dados do locatário para um ponto antes da exclusão do evento. Primeiro ele coloca o locatário Contoso Concert Hall offline para impedir novas atualizações. Em seguida, um banco de dados paralelo é criado pela restauração do ponto de restauração.  O banco de dados restaurado é nomeado com um carimbo de data/hora para fazer com que o nome do banco de dados não entre em conflito com o nome do banco de dados do locatário existente. Em seguida, o banco de dados do locatário antigo é excluído, e o banco de dados restaurado é renomeado para o nome do banco de dados original. Por fim, a sala de concertos Contoso é colocada online para permitir o acesso do aplicativo ao banco de dados restaurado.

Você restaurou com êxito o banco de dados para um ponto no tempo antes da exclusão do evento. A página de eventos abre, portanto confirme se o último evento foi restaurado.

Observe que, depois de restaurar o banco de dados, levará mais 10 a 15 minutos antes do primeiro back completo ficar disponível para restauração. 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]

> * Restaurar um banco de dados em um banco de dados paralelo (lado a lado)
> * Restaurar um banco de dados no local

[Gerenciar o esquema de banco de dados do locatário](saas-tenancy-schema-management.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que aproveitam o aplicativo de SaaS do Wingtip](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Visão geral da continuidade dos negócios com o Banco de Dados SQL do Azure](sql-database-business-continuity.md)
* [Saiba mais sobre o Banco de Dados SQL](sql-database-automated-backups.md)
