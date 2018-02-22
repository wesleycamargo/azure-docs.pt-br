---
title: "Provisionamento de usuário automatizado em aplicativo SaaS no Azure AD | Microsoft Docs"
description: "Uma introdução a como você pode usar o Azure AD para provisionar, desprovisionar e atualizar contínua e automaticamente as contas de usuário em vários aplicativos SaaS de terceiros."
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: mtillman
editor: 
ms.assetid: 58c5fa2d-bb33-4fba-8742-4441adf2cb62
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2017
ms.author: asmalser
ms.openlocfilehash: e14ba62ce2d6c48e47a6b75387bcede68bb1a5b0
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/11/2018
---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Automatize o provisionamento e o desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory
## <a name="what-is-automated-user-provisioning-for-saas-apps"></a>O que é o provisionamento automatizado de usuários para aplicativos SaaS?
O Azure AD (Azure Active Directory) permite que você automatize a criação, a manutenção e a remoção de identidades de usuário em aplicativos em nuvem ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) como Dropbox, Salesforce, ServiceNow e muito mais.

**Abaixo estão alguns exemplos do que esse recurso permite que você faça:**

* Crie automaticamente novas contas nos sistemas certos quando novas pessoas entrarem em sua equipe ou organização.
* Desative automaticamente as contas nos sistemas certos quando as pessoas deixarem a equipe ou organização.
* Mantenha as identidades em seus aplicativos e sistemas atualizadas com base nas alterações no diretório ou em seu sistema de recursos humanos.
* Provisione objetos que não são de usuário, como grupos, para aplicativos que dão suporte a eles.

**O provisionamento automatizado de usuário também inclui a seguinte funcionalidade:**

* A capacidade de corresponder identidades existentes entre os aplicativos de origem e destino.
* Os mapeamentos de atributos personalizável que definem quais dados do usuário devem fluir do sistema de origem para o sistema de destino.
* Alertas de email opcionais para erros de provisionamento
* Logs de relatórios e de registros para ajudá-lo a monitorar e a solucionar problemas.

## <a name="why-use-automated-provisioning"></a>Por que usar o provisionamento automatizado?
Alguns motivos comuns para usar esse recurso incluem:

* Evitando custos, ineficiências e erro humano associados a processos manuais de provisionamento.
* Evitando os custos associados à hospedagem e manutenção de scripts e soluções de provisionamento personalizadas
* Proteger sua organização removendo instantaneamente as identidades dos usuários dos principais aplicativos SaaS quando eles saírem da organização.
* Importar facilmente um número grande de usuários para determinado aplicativo SaaS ou sistema.
* Para aproveitar ter um único conjunto de políticas para determinar quem é provisionado e quem pode entrar um aplicativo.


## <a name="how-does-automatic-provisioning-work"></a>Como funciona o provisionamento automático?
    
O **Serviço de provisionamento do Azure AD** provisiona usuários para aplicativos SaaS e outros sistemas conectando-se aos pontos de extremidade da API de gerenciamento fornecidos por cada fornecedor de aplicativo. Esses pontos de extremidade de API de gerenciamento permitem que o Azure AD crie, atualize e remova usuários por meio de programação. Para os aplicativos selecionados, o serviço de provisionamento também pode criar, atualizar e remover objetos adicionais relacionados à identidade, como grupos e funções. 

![Provisionamento](./media/active-directory-saas-app-provisioning/provisioning0.PNG)
*Figura 1: o Serviço de provisionamento do Azure AD*

![Provisionamento externo](./media/active-directory-saas-app-provisioning/provisioning1.PNG)
*Figura 2: fluxo de trabalho de provisionamento do usuário "Externo" do Azure AD para aplicativos SaaS populares*

![Provisionamento interno](./media/active-directory-saas-app-provisioning/provisioning2.PNG)
*Figura 3: fluxo de trabalho de provisionamento do usuário "Interno" de aplicativos de HCM (gerenciamento de capital humano) para o Azure Active Directory e o Windows Server Active Directory*


## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Quais aplicativos e sistemas posso usar com o provisionamento de usuário automático do Azure AD?

O Azure AD apresenta suporte pré-integrado para vários sistemas de recursos humanos e aplicativos SaaS populares, bem como suporte genérico para aplicativos que implementam partes específicas do padrão SCIM 2.0.

Para obter uma lista de todos os aplicativos para os quais o Azure Active Directory dá suporte em um conector de provisionamento pré-integrado, consulte a [lista de tutoriais de aplicativo para provisionamento do usuário](active-directory-saas-tutorial-list.md).

Para obter informações sobre como adicionar suporte para o provisionamento do usuário do Azure AD a um aplicativo, consulte [Como usar SCIM para provisionar automaticamente usuários e grupos do Azure Active Directory para aplicativos](active-directory-scim-provisioning.md).

Para entrar em contato com a equipe de engenharia do Azure AD e solicitar o suporte de provisionamento para mais aplicativos, envie uma mensagem no [Fórum de comentários do Active Directory do Azure](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/filters/new?category_id=172035).    

> [!NOTE]
> Para que um aplicativo dê suporte a provisionamento automatizado de usuários, primeiro ele deve fornecer as APIs de gerenciamento necessárias que permitam que programas externos automatizem a criação, a manutenção e a remoção de usuários. Portanto, nem todos os aplicativos SaaS são compatíveis com esse recurso. Para aplicativos que dão suporte às APIs de gerenciamento de usuário, a equipe de engenharia do Azure AD será capaz de criar um conector de provisionamento para eles e esse trabalho é priorizado de acordo com as necessidades de clientes atuais e potenciais. 
    
    
## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Como configurar o provisionamento automático para um aplicativo?

A configuração do serviço de provisionamento do Azure AD em um aplicativo selecionado começa no **[Portal do Azure](https://portal.azure.com)**. Na seção **Azure Active Directory > Aplicativos Corporativos**, selecione **Adicionar**, **Tudo** e, depois, adicione o seguinte, dependendo do cenário:

* Todos os aplicativos na seção **Aplicativos em destaque** oferecem suporte ao provisionamento automático. Consulte a [lista de tutoriais de aplicativo para provisionamento do usuário] active-directory-saas-tutorial-list.md) para obter adicionais.

* Use a opção "aplicativo que não é da galeria" para integrações de SCIM personalizados

![Galeria](./media/active-directory-saas-app-provisioning/gallery.png)

Na tela de gerenciamento de aplicativo, o provisionamento é configurado na guia **Provisionamento**.

![Configurações](./media/active-directory-saas-app-provisioning/provisioning_settings0.PNG)


* É necessário fornecer **credenciais de administrador** para o serviço de provisionamento do Azure AD, assim ele poderá se conectar à API de gerenciamento de usuário fornecida pelo aplicativo. Esta seção também permitirá habilitar notificações por email, se as credenciais falharem, ou o trabalho de provisionamento entrará em [quarentena](#quarantine).

* **Mapeamentos de atributo** podem ser configurados para especificar quais campos no sistema de origem (exemplo: Azure AD) passarão por sincronização de seus conteúdos, e com quais campos no sistema de destino (exemplo: ServiceNow). Se o aplicativo de destino oferecer suporte a isso, esta seção permitirá que você configure opcionalmente o provisionamento de grupos, além de contas de usuário. "Propriedades de correspondência" permitem que você selecione quais campos são usados para corresponder as contas entre os sistemas. "[Expressões](active-directory-saas-writing-expressions-for-attribute-mappings.md)" permitem que você modifique e transforme os valores recuperados do sistema de origem, antes de serem gravados no sistema de destino. Para saber mais, confira [Personalizar mapeamentos de atributo](active-directory-saas-customizing-attribute-mappings.md).

![Configurações](./media/active-directory-saas-app-provisioning/provisioning_settings1.PNG)

* **Filtros de escopo** informam ao serviço de provisionamento quais usuários e grupos no sistema de origem devem ser provisionados e/ou desprovisionados no sistema de destino. Há dois aspectos para a definição de escopo dos filtros que são avaliados em conjunto e determinam quem está no escopo para provisionamento:

    * **Filtro em valores de atributo** - O menu "Escopo do Objeto de Origem" nos mapeamentos de atributo permitem a filtragem de valores de atributo específicos. Por exemplo, você pode especificar que apenas os usuários com um atributo "Departamento" de "Vendas" devem estar no escopo para provisionamento. Para obter mais informações, consulte [Como usar filtros de escopo](active-directory-saas-scoping-filters.md).

    * **Filtro nas atribuições de** - O menu "Escopo" na seção Provisionamento > Configurações do portal permite que você especifique se apenas usuários e grupos "atribuídos" devem estar no escopo para provisionamento, ou se todos os usuários no diretório do Azure AD devem ser provisionados. Para saber mais sobre "como atribuir" usuários e grupos, consulte [Atribuir um usuário ou grupo a um aplicativo corporativo no Azure Active Directory](active-directory-coreapps-assign-user-azure-portal.md).
    
* **Configurações** controlam a operação do serviço de provisionamento de um aplicativo, incluindo se ele está em execução no momento ou não.

* **Logs de auditoria** fornecem registros de todas as operações executadas pelo serviço de provisionamento do Azure AD. Para obter mais detalhes, consulte o [guia de relatório de provisionamento](active-directory-saas-provisioning-reporting.md).

![Configurações](./media/active-directory-saas-app-provisioning/audit_logs.PNG)

> [!NOTE]
> O serviço de provisionamento de usuário do Azure AD também pode ser configurado e gerenciado usando-se a [API do Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview).


## <a name="what-happens-during-provisioning"></a>O que acontece durante o provisionamento?

Quando o Azure AD é o sistema de origem, o serviço de provisionamento usa o [recurso Consulta Diferencial da API do Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query) para monitorar usuários e grupos. O serviço de provisionamento executa uma sincronização inicial com o sistema de origem e o sistema de destino, seguida de sincronizações incrementais periódicas. 

### <a name="initial-sync"></a>Sincronização inicial
Quando o serviço de provisionamento for iniciado, a primeira sincronização a ser executada será:

1. Consultar todos os usuários e grupos do sistema de origem, recuperando todos os atributos definidos nos [mapeamentos de atributo](active-directory-saas-customizing-attribute-mappings.md).
2. Filtre os usuários e os grupos retornados usando qualquer [atribuição](active-directory-coreapps-assign-user-azure-portal.md) configurada ou [filtros de escopo com base em atributo](active-directory-saas-scoping-filters.md).
3. Quando um usuário se encontra atribuído ou em escopo para provisionamento, o serviço consulta o sistema de destino em busca de um usuário correspondente usando os [atributos correspondentes](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-properties) designados. Exemplo: se o nome userPrincipal no sistema de origem for o atributo correspondente e for mapeado para userName no sistema de destino, o serviço de provisionamento consultará o sistema de destino em busca de userNames que correspondam aos valores de nome de Principal no sistema de origem.
4. Se um usuário correspondente não for encontrado no sistema de destino, ele será criado usando-se os atributos retornados do sistema de origem.
5. Se um usuário correspondente for encontrado, ele será atualizado usando-se os atributos fornecidos pelo sistema de origem.
6. Se os mapeamentos de atributo contiverem atributos "reference", o serviço realizará atualizações adicionais no sistema de destino para criar e vincular os objetos referenciados. Por exemplo, um usuário pode ter um atributo "Manager" no sistema de destino, vinculado a outro usuário criado no sistema de destino.
7. Mantenha uma marca d'água ao final da sincronização inicial, que fornece o ponto de partida para as sincronizações incrementais subsequentes.

Alguns aplicativos, como ServiceNow, Google Apps e Box, dão suporte não apenas ao provisionamento de usuários, mas também ao provisionamento de grupos e seus membros. Nesses casos, se o provisionamento de grupos estiver habilitado nos [mapeamentos](active-directory-saas-customizing-attribute-mappings.md), o serviço de provisionamento sincronizará os usuários e os grupos e, em seguida, as associações de grupos. 

### <a name="incremental-syncs"></a>Sincronizações incrementais
Após a sincronização inicial, todas as sincronizações subsequentes vão:

1. Consultar o sistema de origem em busca de eventuais usuários e grupos que tenham sido atualizados desde a última marca d'água armazenada.
2. Filtre os usuários e os grupos retornados usando qualquer [atribuição](active-directory-coreapps-assign-user-azure-portal.md) configurada ou [filtros de escopo com base em atributo](active-directory-saas-scoping-filters.md).
3. Quando um usuário se encontra atribuído ou em escopo para provisionamento, o serviço consulta o sistema de destino em busca de um usuário correspondente usando os [atributos correspondentes](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-properties) designados.
4. Se um usuário correspondente não for encontrado no sistema de destino, ele será criado usando-se os atributos retornados do sistema de origem.
5. Se um usuário correspondente for encontrado, ele será atualizado usando-se os atributos fornecidos pelo sistema de origem.
6. Se os mapeamentos de atributo contiverem atributos "reference", o serviço realizará atualizações adicionais no sistema de destino para criar e vincular os objetos referenciados. Por exemplo, um usuário pode ter um atributo "Manager" no sistema de destino, vinculado a outro usuário criado no sistema de destino.
7. Se um usuário que estava anteriormente no escopo para provisionamento for removido do escopo (inclusive sendo desatribuído), o serviço desabilitará o usuário no sistema de destino por meio de uma atualização.
8. Se um usuário que estava anteriormente no escopo para provisionamento for desabilitado ou excluído no sistema de origem, o serviço desabilitará o usuário no sistema de destino por meio de uma atualização.
9. Se um usuário que estava anteriormente no escopo para provisionamento for excluído no sistema de origem, o serviço excluirá o usuário no sistema de destino. No Azure AD, os usuários são excluídos 30 dias depois que forem excluídos pelo software.
10. Mantenha uma nova marca d'água ao final da sincronização incremental, que fornece o ponto de partida para as sincronizações incrementais subsequentes.

>[!NOTE]
> Você também pode desabilitar as operações de criação, atualização ou exclusão operações usando as caixas de seleção **Ações do Objeto de Destino** na seção [Mapeamentos de Atributos](active-directory-saas-customizing-attribute-mappings.md). A lógica para desabilitar um usuário durante uma atualização também é controlada por meio de um mapeamento de atributos em um campo como "accountEnabled".

O serviço de provisionamento continuará executando sincronizações incrementais back-to-back indefinidamente, em intervalos definidos no [tutorial específico de cada aplicativo](active-directory-saas-tutorial-list.md), até um dos seguintes eventos ocorrer:

* O serviço é interrompido manualmente usando-se o portal do Azure ou o comando apropriado da API do Graph 
* Uma nova sincronização inicial é disparada usando-se a opção **Limpar estado e reiniciar** no portal do Azure ou o comando apropriado da API do Graph. Isso apaga qualquer marca d'água armazenada e faz todos os objetos de origem serem reavaliados.
* Uma nova sincronização inicial será disparada por causa de uma alteração em mapeamentos de atributos ou filtros de escopo. Isso também apaga qualquer marca d'água armazenada e faz todos os objetos de origem serem reavaliados.
* O processo de provisionamento vai para quarentena (veja abaixo) por causa de uma taxa de erros alta e permanece em quarentena por mais de quatro semanas. Neste caso, o serviço será desabilitado automaticamente.

### <a name="errors-and-retries"></a>Erros e novas tentativas 
Se um usuário individual não puder ser adicionado, atualizado ou excluído no sistema de destino por causa de um erro no sistema de destino, a operação será repetida no próximo ciclo de sincronização. Se o usuário continuar falhando, as novas tentativas começarão a ocorrer em uma frequência reduzida, voltando gradualmente para apenas uma tentativa por dia. Para resolver a falha, os administradores precisarão verificar os [logs de auditoria](active-directory-saas-provisioning-reporting.md) em busca de eventos "caução de processo" para determinar a causa raiz e realizar a ação apropriada. Entre as falhas comuns podem estar:

* Usuários que não tenham um atributo preenchido no sistema de origem necessário no sistema de destino
* Usuários que tenham um valor de atributo no sistema de origem para o qual há uma restrição exclusiva no sistema de destino e o mesmo valor está presente em outro registro de usuário

Essas falhas podem ser resolvidas ajustando-se os valores de atributo do usuário afetado no sistema de origem ou os mapeamentos de atributos para não causar conflitos.   

### <a name="quarantine"></a>Quarentena
Se a maioria ou todas as chamadas feitas no sistema de destino falharem consistentemente por causa de um erro (como no caso de credenciais de administrador inválidas), o trabalho de provisionamento entrará em um estado de "quarentena". Isso é indicado no [relatório de resumo do provisionamento](active-directory-saas-provisioning-reporting.md) e por email, se notificações por email estiverem configuradas no portal do Azure. 

Quando em quarentena, a frequência de sincronizações incrementais é reduzida gradualmente a uma por dia. 

O trabalho de provisionamento será removido da quarentena depois de todos os erros ofensivos terem sido corrigidos, e o próximo ciclo de sincronização começa. Se permanecer em quarentena por mais de quatro semanas, o trabalho de provisionamento será desabilitado.


## <a name="frequently-asked-questions"></a>Perguntas frequentes

**Quanto tempo levará para provisionar meus usuários?**

O desempenho será diferente dependendo do trabalho de provisionamento estar realizando uma sincronização inicial ou uma sincronização incremental.

Para sincronizações inicias, o tempo necessário para conclusão dependerá diretamente de quantos usuários, grupos e membros do grupo estão presentes no sistema de origem. Sistemas de origem muito pequenos, com centenas de objetos, podem concluir sincronizações inicias em questão de minutos. No entanto, os sistemas de origem com centenas de milhares ou milhões de objetos combinados podem demorar muito.

Para sincronizações incrementais, o tempo necessário depende do número de alterações detectadas nesse ciclo de sincronização. Se houver menos de 5.000 alterações de usuário ou associação ao grupo detectadas, elas normalmente poderão ser sincronizadas dentro de um ciclo de 40 minutos. 

O desempenho geral depende dos sistemas de origem e de destino. Alguns sistemas de destino implementam limites de taxa de solicitação e otimização que podem afetar o desempenho durante grandes operações de sincronização, e os conectores de provisionamento do Azure AD pré-criados para esses sistemas levam isso em conta.

O desempenho também é mais lento se houver muitos erros (registrados nos [logs de auditoria](active-directory-saas-provisioning-reporting.md)) e o serviço de provisionamento tiver entrado em um estado de "quarentena".

**Como posso melhorar o desempenho da sincronização?**

A maioria dos problemas de desempenho ocorre durante sincronizações inicias de sistemas que tenham um grande número de grupos e membros do grupo.

Se a sincronização de grupos ou associações ao grupo não for necessária, o desempenho da sincronização poderá melhorar muito:

1. Definindo-se o menu **Provisionamento > Configurações > Escopo** como **Sincronizar tudo**, em vez de sincronizar usuários e grupos atribuídos.
2. Use [filtros de escopo](active-directory-saas-scoping-filters.md) em vez de atribuições para filtrar a lista de usuários provisionados.

> [!NOTE]
> Para aplicativos que deem suporte ao provisionamento de nomes e propriedades do grupo (como ServiceNow e Google Apps), desabilitar isso também reduz o tempo necessário à conclusão da sincronização inicial. Se você não quiser provisionar nomes de grupo e associações de grupo para o seu aplicativo, você pode desabilitar essa opção nos [mapeamentos de atributo](active-directory-saas-customizing-attribute-mappings.md) de sua configuração de provisionamento.

**Como posso acompanhar o progresso do trabalho de provisionamento atual?**

Confira o [guia de relatório de provisionamento](active-directory-saas-provisioning-reporting.md).

**Como saber se os usuários não estão sendo provisionados corretamente?**

Todas as falhas são registradas nos logs de auditoria do Azure AD. Para saber mais, confira o [guia de relatório de provisionamento](active-directory-saas-provisioning-reporting.md).

**Como posso criar um aplicativo que funcione com o serviço de provisionamento?**

Consulte [Como usar o SCIM para provisionar automaticamente usuários e grupos do Azure Active Directory para aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning).

**Como posso enviar comentários à equipe de engenharia?**

Entre em contato conosco através do [Fórum de comentários do Active Directory do Azure](https://feedback.azure.com/forums/169401-azure-active-directory/).


## <a name="related-articles"></a>Artigos relacionados
* [Lista de tutoriais sobre como integrar aplicativos SaaS](active-directory-saas-tutorial-list.md)
* [Personalizando os mapeamentos de atributos para provisionamento de usuários](active-directory-saas-customizing-attribute-mappings.md)
* [Escrevendo expressões para mapeamentos de atributo](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Filtros de escopo para provisionamento de usuários](active-directory-saas-scoping-filters.md)
* [Usando o SCIM para habilitar o provisionamento automático de usuários e grupos do Active Directory do Azure para aplicativos](active-directory-scim-provisioning.md)
* [Visão geral da API de sincronização do Azure AD](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)

