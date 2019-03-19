---
title: Provisionamento de usuário automatizado em aplicativo SaaS no Azure AD | Microsoft Docs
description: Uma introdução a como você pode usar o Azure AD para provisionar, desprovisionar e atualizar contínua e automaticamente as contas de usuário em vários aplicativos SaaS de terceiros.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/30/2018
ms.author: celested
ms.reviewer: asmalser
ms.collection: M365-identity-device-management
ms.openlocfilehash: 198f32b5f3d7572807b6af3e41ccf8085d9cbc0b
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57576932"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Automatize o provisionamento e o desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory

## <a name="what-is-automated-user-provisioning-for-saas-apps"></a>O que é o provisionamento automatizado de usuários para aplicativos SaaS?
O Azure AD (Azure Active Directory) permite que você automatize a criação, a manutenção e a remoção de identidades de usuário em aplicativos em nuvem ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) como Dropbox, Salesforce, ServiceNow e muito mais.

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

**Abaixo estão alguns exemplos do que esse recurso permite que você faça:**

* Crie automaticamente novas contas nos sistemas certos quando novas pessoas entrarem em sua equipe ou organização.
* Desative automaticamente as contas nos sistemas certos quando as pessoas deixarem a equipe ou organização.
* Mantenha as identidades em seus aplicativos e sistemas atualizadas com base nas alterações no diretório ou em seu sistema de recursos humanos.
* Provisione objetos que não são de usuário, como grupos, para aplicativos que dão suporte a eles.

**O provisionamento automatizado de usuário também inclui a seguinte funcionalidade:**

* A capacidade de corresponder identidades existentes entre os aplicativos de origem e destino.
* Os mapeamentos de atributos personalizável que definem quais dados do usuário devem fluir do sistema de origem para o sistema de destino.
* Alertas de email opcionais para erros de provisionamento.
* Logs de relatórios e de registros para ajudá-lo a monitorar e a solucionar problemas.

## <a name="why-use-automated-provisioning"></a>Por que usar o provisionamento automatizado?

Alguns motivos comuns para usar esse recurso incluem:

* Evitando custos, ineficiências e erro humano associados a processos manuais de provisionamento.
* Evitando os custos associados à hospedagem e à manutenção dos scripts e das soluções de provisionamento personalizadas.
* Proteger sua organização removendo instantaneamente as identidades dos usuários dos principais aplicativos SaaS quando eles saírem da organização.
* Importar facilmente uma grande quantidade de usuários em um determinado aplicativo SaaS ou sistema.
* Para aproveitar ter um único conjunto de políticas para determinar quem é provisionado e quem pode entrar um aplicativo.

## <a name="how-does-automatic-provisioning-work"></a>Como funciona o provisionamento automático?
    
O **Serviço de provisionamento do Azure AD** provisiona usuários para aplicativos SaaS e outros sistemas conectando-se aos pontos de extremidade da API de gerenciamento fornecidos por cada fornecedor de aplicativo. Esses pontos de extremidade de API de gerenciamento permitem que o Azure AD crie, atualize e remova usuários por meio de programação. Para os aplicativos selecionados, o serviço de provisionamento também pode criar, atualizar e remover objetos adicionais relacionados à identidade, como grupos e funções. 

![Provisionamento](./media/user-provisioning/provisioning0.PNG)
*Figura 1: Serviço de provisionamento do Azure AD*

![Provisionamento de saída](./media/user-provisioning/provisioning1.PNG)
*Figura 2: fluxo de trabalho de provisionamento do usuário de "saída" do Azure AD para aplicativos SaaS populares*

![Provisionamento de entrada](./media/user-provisioning/provisioning2.PNG)
*Figura 3: fluxo de trabalho de provisionamento do usuário de "entrada" de aplicativos de HCM (gerenciamento de capital humano) para o Azure Active Directory e o Windows Server Active Directory*


## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Quais aplicativos e sistemas posso usar com o provisionamento de usuário automático do Azure AD?

O Azure AD apresenta suporte pré-integrado para vários sistemas de recursos humanos e aplicativos SaaS populares, bem como suporte genérico para aplicativos que implementam partes específicas do padrão SCIM 2.0.

### <a name="pre-integrated-applications"></a>Aplicações pré-integradas

Para obter uma lista de todos os aplicativos para os quais o Azure Active Directory dá suporte em um conector de provisionamento pré-integrado, consulte a [lista de tutoriais de aplicativo para provisionamento do usuário](../saas-apps/tutorial-list.md).

Para entrar em contato com a equipe de engenharia do Azure AD e solicitar o suporte de provisionamento para mais aplicativos, envie uma mensagem no [Fórum de comentários do Active Directory do Azure](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/filters/new?category_id=172035).

> [!NOTE]
> Para que um aplicativo dê suporte a provisionamento automatizado de usuários, primeiro ele deve fornecer as APIs de gerenciamento necessárias que permitam que programas externos automatizem a criação, a manutenção e a remoção de usuários. Portanto, nem todos os aplicativos SaaS são compatíveis com esse recurso. Para aplicativos que dão suporte às APIs de gerenciamento de usuário, a equipe de engenharia do Azure AD será capaz de criar um conector de provisionamento para eles e esse trabalho é priorizado de acordo com as necessidades de clientes atuais e potenciais. 

### <a name="connecting-applications-that-support-scim-20"></a>Conectando aplicativos que oferecem suporte a SCIM 2.0

Para obter informações sobre como conectar aplicativos de forma genérica que implementem APIs de gerenciamento de usuário baseadas em SCIM 2.0, consulte [Como usar SCIM para provisionar automaticamente usuários e grupos do Azure Active Directory para aplicativos](use-scim-to-provision-users-and-groups.md).

    
## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Como configurar o provisionamento automático para um aplicativo?

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

A configuração do serviço de provisionamento do Azure AD em um aplicativo selecionado começa no **[Portal do Azure](https://portal.azure.com)**. Na seção **Azure Active Directory > Aplicativos Corporativos**, selecione **Adicionar**, **Tudo** e, depois, adicione o seguinte, dependendo do cenário:

* Todos os aplicativos na seção **Aplicativos em destaque** oferecem suporte ao provisionamento automático. Veja a lista [de tutoriais de aplicativos para provisionamento de usuários](../saas-apps/tutorial-list.md) para os adicionais.

* Use a opção "aplicativo que não é da galeria" para integrações de SCIM personalizados

![Galeria](./media/user-provisioning/gallery.png)

Na tela de gerenciamento de aplicativo, o provisionamento é configurado na guia **Provisionamento**.

![Configurações](./media/user-provisioning/provisioning_settings0.PNG)


* É necessário fornecer **credenciais de administrador** para o serviço de provisionamento do Azure AD, assim ele poderá se conectar à API de gerenciamento de usuário fornecida pelo aplicativo. Esta seção também permitirá habilitar notificações por email, se as credenciais falharem, ou o trabalho de provisionamento entrará em [quarentena](#quarantine).

* Os **mapeamentos de atributo** podem ser configurados de forma que especifiquem quais campos no sistema de origem (exemplo: Azure AD) terão seu conteúdo sincronizado com quais campos no sistema de destino (exemplo: ServiceNow). Se o aplicativo de destino oferecer suporte a isso, esta seção permitirá que você configure opcionalmente o provisionamento de grupos, além de contas de usuário. "Propriedades de correspondência" permitem que você selecione quais campos são usados para corresponder as contas entre os sistemas. "[Expressões](functions-for-customizing-application-data.md)" permitem que você modifique e transforme os valores recuperados do sistema de origem, antes de serem gravados no sistema de destino. Para saber mais, confira [Personalizar mapeamentos de atributo](customize-application-attributes.md).

![Configurações](./media/user-provisioning/provisioning_settings1.PNG)

* Os **filtros de escopo** informam ao serviço de provisionamento quais usuários e grupos no sistema de origem devem ser provisionados e/ou desprovisionados no sistema de destino. Há dois aspectos para a definição de escopo dos filtros que são avaliados em conjunto e determinam quem está no escopo para provisionamento:

    * **Filtro em valores de atributo** - O menu "Escopo do Objeto de Origem" nos mapeamentos de atributo permitem a filtragem de valores de atributo específicos. Por exemplo, você pode especificar que apenas os usuários com um atributo "Departamento" de "Vendas" devem estar no escopo para provisionamento. Para obter mais informações, consulte [Como usar filtros de escopo](define-conditional-rules-for-provisioning-user-accounts.md).

    * **Filtro nas atribuições de** - O menu "Escopo" na seção Provisionamento > Configurações do portal permite que você especifique se apenas usuários e grupos "atribuídos" devem estar no escopo para provisionamento, ou se todos os usuários no diretório do Azure AD devem ser provisionados. Para saber mais sobre "como atribuir" usuários e grupos, consulte [Atribuir um usuário ou grupo a um aplicativo corporativo no Azure Active Directory](assign-user-or-group-access-portal.md).
    
* **Configurações** controlam a operação do serviço de provisionamento de um aplicativo, incluindo se ele está em execução no momento ou não.

* **Logs de auditoria** fornecem registros de todas as operações executadas pelo serviço de provisionamento do Azure AD. Para obter mais detalhes, consulte o [guia de relatório de provisionamento](check-status-user-account-provisioning.md).

![Configurações](./media/user-provisioning/audit_logs.PNG)

> [!NOTE]
> O serviço de provisionamento de usuário do Azure AD também pode ser configurado e gerenciado usando-se a [API do Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview).


## <a name="what-happens-during-provisioning"></a>O que acontece durante o provisionamento?

Quando o Azure AD é o sistema de origem, o serviço de provisionamento usa o [recurso Consulta Diferencial da API do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query) para monitorar usuários e grupos. O serviço de provisionamento executa uma sincronização inicial com o sistema de origem e o sistema de destino, seguida de sincronizações incrementais periódicas. 

### <a name="initial-sync"></a>Sincronização inicial

Quando o serviço de provisionamento for iniciado, a primeira sincronização a ser executada será:

1. Consultar todos os usuários e grupos do sistema de origem, recuperando todos os atributos definidos nos [mapeamentos de atributo](customize-application-attributes.md).
2. Filtre os usuários e os grupos retornados usando qualquer [atribuição](assign-user-or-group-access-portal.md) configurada ou [filtros de escopo com base em atributo](define-conditional-rules-for-provisioning-user-accounts.md).
3. Quando um usuário se encontra atribuído ou em escopo para provisionamento, o serviço consulta o sistema de destino em busca de um usuário correspondente usando os [atributos correspondentes](customize-application-attributes.md#understanding-attribute-mapping-properties) designados. Exemplo: Se o nome userPrincipal no sistema de origem for o atributo correspondente e for mapeado para userName no sistema de destino, o serviço de provisionamento consultará o sistema de destino em busca de userNames que correspondam aos valores de nome de Principal no sistema de origem.
4. Se um usuário correspondente não for encontrado no sistema de destino, ele será criado usando-se os atributos retornados do sistema de origem. Depois que a conta de usuário é criada, o serviço de provisionamento detecta a ID do sistema de destino para o novo usuário, que é usado para realizar todas as operações futuras nesse usuário.
5. Se um usuário correspondente for encontrado, ele será atualizado usando-se os atributos fornecidos pelo sistema de origem. Depois que a conta de usuário é correspondida, o serviço de provisionamento detecta a ID do sistema de destino para o novo usuário, que é usado para realizar todas as operações futuras nesse usuário.
6. Se os mapeamentos de atributo contiverem atributos "reference", o serviço realizará atualizações adicionais no sistema de destino para criar e vincular os objetos referenciados. Por exemplo, um usuário pode ter um atributo "Manager" no sistema de destino, vinculado a outro usuário criado no sistema de destino.
7. Mantenha uma marca d'água ao final da sincronização inicial, que fornece o ponto de partida para as sincronizações incrementais subsequentes.

Alguns aplicativos, como ServiceNow, Google Apps e Box, dão suporte não apenas ao provisionamento de usuários, mas também ao provisionamento de grupos e seus membros. Nesses casos, se o provisionamento de grupos estiver habilitado nos [mapeamentos](customize-application-attributes.md), o serviço de provisionamento sincronizará os usuários e os grupos e, em seguida, as associações de grupos. 

### <a name="incremental-syncs"></a>Sincronizações incrementais

Após a sincronização inicial, todas as sincronizações subsequentes vão:

1. Consultar o sistema de origem em busca de eventuais usuários e grupos que tenham sido atualizados desde a última marca d'água armazenada.
2. Filtre os usuários e os grupos retornados usando qualquer [atribuição](assign-user-or-group-access-portal.md) configurada ou [filtros de escopo com base em atributo](define-conditional-rules-for-provisioning-user-accounts.md).
3. Quando um usuário se encontra atribuído ou em escopo para provisionamento, o serviço consulta o sistema de destino em busca de um usuário correspondente usando os [atributos correspondentes](customize-application-attributes.md#understanding-attribute-mapping-properties) designados.
4. Se um usuário correspondente não for encontrado no sistema de destino, ele será criado usando-se os atributos retornados do sistema de origem. Depois que a conta de usuário é criada, o serviço de provisionamento detecta a ID do sistema de destino para o novo usuário, que é usado para realizar todas as operações futuras nesse usuário.
5. Se um usuário correspondente for encontrado, ele será atualizado usando-se os atributos fornecidos pelo sistema de origem. Se uma conta que é correspondida for recentemente atribuída, o provisionamento detecta a ID do sistema de destino para o novo usuário, que é usado para realizar todas as operações futuras nesse usuário.
6. Se os mapeamentos de atributo contiverem atributos "reference", o serviço realizará atualizações adicionais no sistema de destino para criar e vincular os objetos referenciados. Por exemplo, um usuário pode ter um atributo "Manager" no sistema de destino, vinculado a outro usuário criado no sistema de destino.
7. Se um usuário que estava anteriormente no escopo para provisionamento for removido do escopo (inclusive sendo desatribuído), o serviço desabilitará o usuário no sistema de destino por meio de uma atualização.
8. Se um usuário que estava anteriormente no escopo para provisionamento for desabilitado ou excluído no sistema de origem, o serviço desabilitará o usuário no sistema de destino por meio de uma atualização.
9. Se um usuário que estava anteriormente no escopo para provisionamento for excluído no sistema de origem, o serviço excluirá o usuário no sistema de destino. No Azure AD, os usuários são excluídos 30 dias depois que forem excluídos pelo software.
10. Mantenha uma nova marca d'água ao final da sincronização incremental, que fornece o ponto de partida para as sincronizações incrementais subsequentes.

>[!NOTE]
> Você também pode desabilitar as operações de criação, atualização ou exclusão operações usando as caixas de seleção **Ações do Objeto de Destino** na seção [Mapeamentos de Atributos](customize-application-attributes.md). A lógica para desabilitar um usuário durante uma atualização também é controlada por meio de um mapeamento de atributos em um campo como "accountEnabled".

O serviço de provisionamento continuará executando sincronizações incrementais back-to-back indefinidamente, em intervalos definidos no [tutorial específico de cada aplicativo](../saas-apps/tutorial-list.md), até um dos seguintes eventos ocorrer:

* O serviço é interrompido manualmente usando-se o portal do Azure ou o comando apropriado da API do Graph 
* Uma nova sincronização inicial é disparada usando-se a opção **Limpar estado e reiniciar** no portal do Azure ou o comando apropriado da API do Graph. Isso apaga qualquer marca d'água armazenada e faz todos os objetos de origem serem reavaliados.
* Uma nova sincronização inicial será disparada por causa de uma alteração em mapeamentos de atributos ou filtros de escopo. Isso também apaga qualquer marca d'água armazenada e faz todos os objetos de origem serem reavaliados.
* O processo de provisionamento vai para quarentena (veja abaixo) por causa de uma taxa de erros alta e permanece em quarentena por mais de quatro semanas. Neste caso, o serviço será desabilitado automaticamente.

### <a name="errors-and-retries"></a>Erros e novas tentativas

Se um usuário individual não puder ser adicionado, atualizado ou excluído no sistema de destino por causa de um erro no sistema de destino, a operação será repetida no próximo ciclo de sincronização. Se o usuário continuar falhando, as novas tentativas começarão a ocorrer em uma frequência reduzida, voltando gradualmente para apenas uma tentativa por dia. Para resolver a falha, os administradores precisarão verificar os [logs de auditoria](check-status-user-account-provisioning.md) em busca de eventos "caução de processo" para determinar a causa raiz e realizar a ação apropriada. Entre as falhas comuns podem estar:

* Usuários que não tenham um atributo preenchido no sistema de origem necessário no sistema de destino
* Usuários que tenham um valor de atributo no sistema de origem para o qual há uma restrição exclusiva no sistema de destino e o mesmo valor está presente em outro registro de usuário

Essas falhas podem ser resolvidas ajustando-se os valores de atributo do usuário afetado no sistema de origem ou os mapeamentos de atributos para não causar conflitos.   

### <a name="quarantine"></a>Quarentena

Se a maioria ou todas as chamadas feitas no sistema de destino falharem consistentemente por causa de um erro (como no caso de credenciais de administrador inválidas), o trabalho de provisionamento entrará em um estado de "quarentena". Isso é indicado no [relatório de resumo do provisionamento](check-status-user-account-provisioning.md) e por email, se notificações por email estiverem configuradas no portal do Azure. 

Quando em quarentena, a frequência de sincronizações incrementais é reduzida gradualmente a uma por dia. 

O trabalho de provisionamento será removido da quarentena depois de todos os erros ofensivos terem sido corrigidos, e o próximo ciclo de sincronização começa. Se permanecer em quarentena por mais de quatro semanas, o trabalho de provisionamento será desabilitado.


## <a name="how-long-will-it-take-to-provision-users"></a>Quanto tempo levará para provisionar os usuários?

O desempenho dependerá se o trabalho de provisionamento está executando uma sincronização inicial ou uma sincronização incremental, conforme descrito na seção anterior.

Para **sincronizações iniciais**, o tempo do trabalho depende de vários fatores, incluindo o número de usuários e grupos em escopo para provisionamento, e o número total de usuários e grupos no sistema de origem. Confira posteriormente nesta seção uma lista abrangente dos fatores que afetam o desempenho da sincronização inicial.

Para **sincronizações incrementais**, o tempo de trabalho depende do número de alterações detectadas nesse ciclo de sincronização. Se houver menos de 5.000 alterações de usuários ou de associação de grupo, o trabalho poderá terminar dentro de um único ciclo de sincronização incremental. 

A tabela a seguir resume os tempos de sincronização para cenários comuns de provisionamento. Nesses cenários, o sistema de origem é o Azure AD e o sistema de destino é um aplicativo SaaS. Os tempos de sincronização são derivados de uma análise estatística dos trabalhos de sincronização dos aplicativos SaaS ServiceNow, Workplace, Salesforce e Google Apps.


| Configuração de escopo | Usuários, grupos e membros no escopo | Tempo de sincronização inicial | Tempo de sincronização incremental |
| -------- | -------- | -------- | -------- |
| Sincronizar somente usuários e grupos atribuídos |  Menos de 1.000 |  Menos de 30 minutos | Menos de 30 minutos |
| Sincronizar somente usuários e grupos atribuídos |  1.000 a 10.000 | 142 a 708 minutos | Menos de 30 minutos |
| Sincronizar somente usuários e grupos atribuídos |   10.000 a 100.000 | 1.170 a 2.340 minutos | Menos de 30 minutos |
| Sincronizar todos os usuários e grupos no Azure AD |  Menos de 1.000 | Menos de 30 minutos  | Menos de 30 minutos |
| Sincronizar todos os usuários e grupos no Azure AD |  1.000 a 10.000 | Menos de 30 minutos a 120 minutos | Menos de 30 minutos |
| Sincronizar todos os usuários e grupos no Azure AD |  10.000 a 100.000  | 713 minutos a 1.425 minutos | Menos de 30 minutos |
| Sincronizar todos os usuários no Azure AD|  Menos de 1.000  | Menos de 30 minutos | Menos de 30 minutos |
| Sincronizar todos os usuários no Azure AD | 1.000 a 10.000  | 43 a 86 minutos | Menos de 30 minutos |


Para a configuração **Sincronizar apenas usuários e grupos atribuídos**, use as fórmulas a seguir para determinar os valores mínimo e máximo aproximados esperados dos tempos de **sincronização inicial**:

    Minimum minutes =  0.01 x [Number of assigned users, groups, and group members]
    Maximum minutes = 0.08 x [Number of assigned users, groups, and group members] 
    
Resumo de fatores que influenciam o tempo necessário para concluir uma **sincronização inicial**:

* O número total de usuários e grupos no escopo para provisionamento.

* O número total de usuários, grupos e membros do grupo presentes no sistema de origem (Azure AD).

* Se os usuários em escopo para provisionamento batem com os usuários existentes no aplicativo de destino ou se é necessário criá-los pela primeira vez. Os trabalhos de sincronização para os quais todos os usuários são criados pela primeira vez demoram aproximadamente *duas vezes mais* do que os trabalhos de sincronização para os quais todos os usuários correspondem a usuários existentes.

* Número de erros nos [logs de auditoria](check-status-user-account-provisioning.md). O desempenho será mais lento se houver muitos erros e o serviço de provisionamento tiver entrado em estado de quarentena.    

* Limites de taxa de solicitação implementados pelo sistema de destino. Alguns sistemas de destino implementam limites de taxa de solicitação que pode afetar o desempenho durante operações de sincronização de grande porte. Sob essas condições, um aplicativo que recebe muitas solicitações de forma muito rápida pode reduzir sua taxa de resposta ou fechar a conexão. Para melhorar o desempenho, o conector precisa fazer ajustes para não enviar as solicitações de aplicativo mais rápido do que o aplicativo pode processá-las. Os conectores de provisionamento criados pela Microsoft fazem esse ajuste. 

* O número e os tamanhos de grupos atribuídos. A sincronização de grupos atribuídos demora mais do que a sincronização de usuários. O número e os tamanhos dos grupos atribuídos afetam o desempenho. Se um aplicativo tiver [mapeamentos habilitados para sincronização de objeto de grupo](customize-application-attributes.md#editing-group-attribute-mappings), propriedades de grupo como nomes de grupo e associações serão sincronizadas, além dos usuários. Essas sincronizações adicionais tomarão mais tempo do que apenas sincronizar os objetos de usuário.


## <a name="how-can-i-tell-if-users-are-being-provisioned-properly"></a>Como eu posso saber se os usuários estão sendo provisionados corretamente?

Todas as operações feitas pelo serviço provisionando o usuário são gravadas nos logs de auditoria do Azure AD. Isso inclui todas as operações feitas para os sistemas de origem e destino, bem como os dados de usuário que foram lidos ou gravados durante cada operação de leitura e gravação.

Para obter informações sobre como a leitura de logs de auditoria no portal do Azure, consulte o [guia de relatório de provisionamento](check-status-user-account-provisioning.md).


## <a name="how-do-i-troubleshoot-issues-with-user-provisioning"></a>Como solucionar problemas com o provisionamento do usuário?

Para obter diretrizes baseada em cenário sobre como solucionar problemas de provisionamento automático de usuário, consulte [Problemas ao configurar e provisionar usuários para um aplicativo](application-provisioning-config-problem.md).


## <a name="what-are-the-best-practices-for-rolling-out-automatic-user-provisioning"></a>Quais são as práticas recomendadas para implementar o provisionamento automático de usuário?

> [!VIDEO https://www.youtube.com/embed/MAy8s5WSe3A]

Para um plano de implantação passo a passo de exemplo para provisionamento de usuário de saída para um aplicativo, veja o [Guia de implantação de identidade para o provisionamento de usuário](https://aka.ms/userprovisioningdeploymentplan).

## <a name="more-frequently-asked-questions"></a>Mais perguntas frequentes

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-b2b-users-in-azure-ad"></a>O provisionamento automático de usuário para o trabalho de aplicativos SaaS com usuários de B2B do Azure Active Directory?

Sim, é possível usar o provisionamento de usuários do serviço para provisionar B2B (ou convidado) no Azure Active Directory para aplicativos SaaS de usuário do Azure Active Directory.

No entanto, para usuários B2B poder entrar aplicativo SaaS usando o Azure Active Directory, o aplicativo de SaaS deve ter sua capacidade de logon único configurada de forma específica de login único baseado em SAML. Para obter mais informações sobre como configurar aplicativos SaaS para dar suporte a entradas de usuários de B2B, consulte [Configurar aplicativos SaaS para colaboração B2B]( https://docs.microsoft.com/azure/active-directory/b2b/configure-saas-apps).

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-dynamic-groups-in-azure-ad"></a>O provisionamento automático de usuário para o trabalho de aplicativos SaaS com grupos dinâmicos no Microsoft Azure Active Directory?

Sim. Quando configurado para "sincronização atribuída apenas para usuários e grupos", o serviço de provisionamento de usuário do Microsoft Azure Active Directory pode provisionar ou usuários de desprovisionar em um aplicativo SaaS com base se são membros ou não de um [grupo dinâmico](../users-groups-roles/groups-create-rule.md). Grupos dinâmicos também funcionam com a opção "sincronizar todos os usuários e grupos".

No entanto, o uso de grupos dinâmicos pode afetar o desempenho geral de provisionamento do usuário de ponta a ponta do Azure Active Directory para os aplicativo SaaS. Ao usar grupos dinâmicos, tenha essas restrições e recomendações em mente:

* O quão rápido um usuário em um grupo dinâmico é provisionado ou desprovisionado em um aplicativo SaaS depende de quanto tempo o grupo dinâmico pode avaliar as alterações de associação. Para obter informações sobre como verificar o status de processamento de um grupo dinâmico, consulte [Verificar o status de processamento para uma regra de associação](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule).

* Ao usar grupos dinâmicos, as regras devem ser cuidadosamente consideradas com provisionamento e desprovisionamento em mente, pois uma perda de associação resultará em um evento de desprovisionamento.

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-nested-groups-in-azure-ad"></a>O provisionamento automático de usuário para o trabalho de aplicativos SaaS com grupos dinâmicos no Azure Active Directory?

 Não. Quando configurado para "sincronização apenas de usuários e grupos”, o serviço de provisionamento do Azure Active Directory não é capaz de ler ou provisionar usuáios que estão em grupos aninhados. Só é capaz de ler e provisionar os usuários que são membros imediatos do grupo atribuído explicitamente.

Essa é uma limitação de "atribuições baseadas em grupo para aplicativos", o que também afeta o logon único e é descrito em [Usar um grupo para gerenciar o acesso a aplicativos SaaS](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-saasapps ).

Como alternativa, você deve explicitamente-atribuir (ou de outra forma [definir o escopo nos](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)) grupos que contêm os usuários que precisam ser provisionados.

## <a name="related-articles"></a>Artigos relacionados

* [Lista de tutoriais sobre como integrar aplicativos SaaS](../saas-apps/tutorial-list.md)
* [Personalizando os mapeamentos de atributos para provisionamento de usuários](customize-application-attributes.md)
* [Escrevendo expressões para mapeamentos de atributo](functions-for-customizing-application-data.md)
* [Filtros de escopo para provisionamento de usuários](define-conditional-rules-for-provisioning-user-accounts.md)
* [Usando o SCIM para habilitar o provisionamento automático de usuários e grupos do Active Directory do Azure para aplicativos](use-scim-to-provision-users-and-groups.md)
* [Visão geral da API de sincronização do Azure AD](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
