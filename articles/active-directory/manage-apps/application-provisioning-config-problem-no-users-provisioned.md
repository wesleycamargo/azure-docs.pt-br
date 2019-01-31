---
title: Nenhum usuário está sendo provisionado para um aplicativo de galeria do Azure AD | Microsoft Docs
description: Como solucionar problemas comuns enfrentados ao não ver os usuários que aparecem em uma Galeria de Aplicativos do Azure AD que você configurou para o provisionamento do usuário com o Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 181f8488255bca67131ed32474bb05a9d6d082aa
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55154976"
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Nenhum usuário está sendo provisionado para um aplicativo de galeria do Azure AD
Após o provisionamento automático ser configurado para um aplicativo (incluindo verificar se as credenciais de aplicativo fornecidas ao Azure AD para se conectar ao aplicativo são válidas), os usuários e/ou grupos são provisionados no aplicativo. O provisionamento é determinado pelos seguintes fatores:

-   Quais usuários e grupos têm sido **atribuídos** para o aplicativo. Para obter mais informações sobre atribuição, consulte [Atribuir um usuário ou grupo a um aplicativo corporativo no Azure Active Directory](assign-user-or-group-access-portal.md).
-   Estejam os **mapeamentos de atributos** habilitados ou não, e configurados para sincronizar atributos válidos do Azure AD para o aplicativo. Para obter mais informações sobre mapeamentos de atributo, consulte [Personalizando mapeamentos de atributo do provisionamento de usuário para aplicativos SaaS no Azure Active Directory](customize-application-attributes.md).
-   Se há ou não um **filtro de escopo** presente que está filtrando usuários com base nos valores de atributo específico. Para obter mais informações sobre filtros de escopo, consulte [provisionamento de aplicativos com base em atributo com filtros de escopo](define-conditional-rules-for-provisioning-user-accounts.md).
  
Se você observar que os usuários não estão sendo provisionados, consulte os logs de auditoria no Azure AD. Pesquise por entradas de log referentes a um usuário específico.

Os logs de auditoria de provisionamento podem ser acessados no portal do Azure, na guia **Azure Active Directory &gt; Aplicativos Empresariais &gt; \[Nome do Aplicativo\] &gt; Logs de auditoria**. Filtre os logs na categoria de **provisionamento de conta**, para ver apenas os eventos de provisionamento para aquele aplicativo. Você pode procurar por usuários com base na "ID correspondente" que foi configurado para eles nos mapeamentos de atributo. Por exemplo, se você configurou o "nome UPN" ou "endereço de email" como o atributo correspondente no lado do Azure AD e o usuário não sendo provisionado tem um valor de "audrey@contoso.com", em seguida, pesquise os logs de auditoria para "audrey@contoso.com" e reveja as entradas retornadas.

Os logs de auditoria do provisionamento registram todas as operações executadas pelo serviço de provisionamento, incluindo consultas ao Azure AD para usuários que estão no escopo para provisionamento, consultando o aplicativo de destino para a existência desses usuários, comparando os objetos de usuário entre o sistema. Em seguida, adicione, atualize ou desabilite a conta de usuário no sistema de destino com base na comparação.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Áreas de problemas gerais com o provisionamento a considerar
Abaixo está uma lista das áreas de problema geral que você poderá analisar se você tiver uma ideia de onde começar.

- [Serviço de provisionamento não aparece ao iniciar](#provisioning-service-does-not-appear-to-start)
- [Logs de auditoria informa quais usuários são ignorados e não provisionados, mesmo que eles sejam atribuídos](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Serviço de provisionamento não aparece ao iniciar
Se você definir o **Status de provisionamento** como **Ligado** na seção **Azure Active Directory &gt; Aplicativos Empresariais &gt; \[Nome do Aplicativo\] &gt;Provisionamento** do portal do Azure. No entanto, nenhum outro detalhe de status é mostrados nessa página depois de recarregamentos subsequentes, é provável que o serviço esteja em execução, mas não concluiu uma sincronização inicial ainda. Verifique os **logs de auditoria** descritos acima para determinar quais operações o serviço está executando, e se existem erros.

>[!NOTE]
>Uma sincronização inicial entre o Azure AD e um aplicativo pode levar de 20 minutos até várias horas, dependendo do tamanho do diretório do Azure AD e o número de usuários no escopo para provisionamento. Sincronizações subsequentes após a sincronização inicial são mais rápidas, como o serviço de provisionamento armazena as marcas d'água que representam o estado dos dois sistemas após a sincronização inicial. Essa sincronização inicial melhora o desempenho de sincronizações subsequentes.
>


## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Logs de auditoria informa quais usuários são ignorados e não provisionados, mesmo que eles sejam atribuídos

Quando um usuário aparece como "ignorado" nos logs de auditoria, é importante ler os detalhes estendidos na mensagem de log para determinar o motivo. Veja a seguir as resoluções e motivos comuns:

- **Um filtro de escopo foi configurado** **que está filtrando o usuário com base em um valor de atributo**. Para obter mais informações sobre filtros de escopo, consulte [filtros de escopo](define-conditional-rules-for-provisioning-user-accounts.md).
- **O usuário é definido como "não qualificado efetivamente".** Caso você veja essa mensagem de erro específica, é porque há um problema com o registro de atribuição de usuário armazenado no Azure AD. Para corrigir esse problema, cancele a atribuição do usuário (ou grupo) do aplicativo e reatribua-a novamente. Para obter mais informações sobre a atribuição, consulte [Atribuir acesso de usuário ou grupo](assign-user-or-group-access-portal.md).
- **Um atributo obrigatório está ausente ou não está preenchido para um usuário.** Uma coisa importante a considerar ao configurar o provisionamento é examinar e configurar os mapeamentos de atributos e fluxos de trabalho que definem quais propriedades de usuário (ou grupo) fluem do Azure AD para o aplicativo. Essa configuração inclui a definição da "propriedade correspondente" usada para identificar exclusivamente e corresponder usuários/grupos entre os dois sistemas. Para obter mais informações sobre este importante processo, consulte [Personalizando mapeamentos de atributo do provisionamento de usuário para aplicativos SaaS no Azure Active Directory](customize-application-attributes.md).
- **Mapeamentos de atributo para grupos:** Provisionamento do nome do grupo e dos detalhes do grupo, além dos membros, se houver suporte para alguns aplicativos. Você pode habilitar ou desabilitar essa funcionalidade, habilitando ou desabilitando o **Mapeamento** para objetos de grupo mostrados na guia **Provisionamento**. Se o provisionamento de grupos é habilitado, certifique-se de rever os mapeamentos de atributos para garantir que um campo apropriado está sendo usado para a "ID correspondente". Essa ID correspondente pode ser o nome de exibição ou o alias do email. O grupo e seus membros não serão provisionados se a propriedade correspondente estiver vazia ou não for populada para um grupo no Microsoft Azure AD.

## <a name="next-steps"></a>Próximas etapas

[Sincronização do Azure AD Connect: Noções básicas sobre o provisionamento declarativo](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)
