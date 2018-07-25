---
title: 'Azure Active Directory Domain Services: Domínios Suspensos| Microsoft Docs'
description: Suspensão do domínio gerenciado e a exclusão
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2018
ms.author: ergreenl
ms.openlocfilehash: 99896b0f618151cd368a21c4a212b7d8e28b7a6f
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39126086"
---
# <a name="suspended-domains"></a>Domínios suspensos
Quando o Azure AD Domain Services é incapaz de atender a um domínio gerenciado por um longo período de tempo, o domínio gerenciado é colocado em um estado suspenso. Este artigo explica porque os domínios gerenciados são suspensos e como corrigir um domínio suspenso.


## <a name="states-your-managed-domain-can-be-in"></a>Estados de seu domínio gerenciado podem estar em

![Linha do tempo do domínio suspenso](media\active-directory-domain-services-suspension\suspension-timeline.PNG)

O gráfico anterior descreve os possíveis estados de um domínio gerenciado do Azure AD Domain Services pode estar em.

### <a name="running-state"></a>Estado de “execução”
Um domínio gerenciado que está configurado corretamente e operacional regularmente é no estado **Executando**.

**O que você pode esperar:**
* A Microsoft regularmente pode monitorar a integridade do seu domínio gerenciado.
* Controladores de domínio para seu domínio gerenciado são corrigidos e atualizados regularmente.
* Alterações do Azure Active Directory são sincronizadas regularmente para seu domínio gerenciado.
* Backups regulares são feitos para seu domínio gerenciado.


### <a name="needs-attention-state"></a>Estado “Precisa de atenção”
Um domínio gerenciado está no estado **Precisa de atenção**, se um ou mais problemas exigem que um administrador execute uma ação. A página de integridade do seu domínio gerenciado listará um ou mais alertas nesse estado. Por exemplo, se você tiver configurado um NSG restritivo para sua rede virtual, a Microsoft poderá não atualizar e monitorar seu domínio gerenciado. Esta configuração inválida resulta em um alerta sendo gerado e seu domínio gerenciado é colocado no estado “Requer atenção”.

Cada alerta tem um conjunto de etapas de resolução. Alguns alertas são temporários e serão resolvidas automaticamente pelo serviço. Você pode resolver alguns outros alertas, seguindo as instruções nas etapas de resolução correspondente para o alerta. Para resolver alguns alertas críticos, você precisa entrar em contato com o suporte da Microsoft.

Para obter mais informações, consulte [como solucionar problemas de alertas em um domínio gerenciado](active-directory-ds-troubleshoot-alerts.md).

**O que você pode esperar:**

Em alguns casos (por exemplo, se você tiver uma configuração de rede inválido), os controladores de domínio para seu domínio gerenciado podem estar inacessíveis. A Microsoft não garante que seu domínio gerenciado é monitorado, corrigido, atualizado ou backup regularmente nesse estado.

* Seu domínio gerenciado está em um estado não íntegro e o monitoramento de integridade em andamento pode parar, até que o alerta seja resolvido.
* Controladores de domínio para seu domínio gerenciado não podem ser corrigidos ou atualizados.
* Alterações do Azure Active Directory não podem ser sincronizadas para o seu domínio gerenciado.
* Backups para seu domínio gerenciado podem ser executados, se possível.
* Se você resolver os alertas que afetam o seu domínio gerenciado, é possível restaurar seu domínio gerenciado para o estado “Em Execução”.
* Alertas críticos são disparados para problemas de configuração em que a Microsoft não consegue alcançar seus controladores de domínio. Se esses alertas não forem resolvidos em 15 dias, seu domínio gerenciado será colocado no estado “Suspenso”.


### <a name="suspended-state"></a>Estado “Suspenso”
Um domínio gerenciado é colocado no estado **Suspenso** pelos seguintes motivos:
* Um ou mais alertas críticos ainda não foram resolvidos em 15 dias. Alertas críticos podem ser causados por uma configuração incorreta que bloqueia o acesso aos recursos necessários para o Azure AD Domain Services.
    * Por exemplo, se o domínio gerenciado tem alerta [AADDS104: Erro de rede](active-directory-ds-troubleshoot-nsg.md) não resolvido por mais de 15 dias.
* Há um problema de cobrança com sua assinatura do Azure ou sua assinatura do Azure expirou.

Domínios gerenciados são suspensos quando a Microsoft é capaz de gerenciar, monitorar, corrigir ou fazer backup de domínio em uma base contínua.

**O que você pode esperar:**
* Controladores de domínio para o domínio gerenciado são desprovisionados e removidos da rede virtual.
* Acesso LDAP seguro para seu domínio gerenciado na Internet.
* Você observa falhas na autenticação no domínio gerenciado, fazendo logon em máquinas virtuais de ingressado no domínio e se conectar por meio de LDAP/LDAPS.
* Não há mais são realizados backups para seu domínio gerenciado.
* A sincronização com o Azure Active Directory para.
* Resolva o alerta, fazendo com que o seu domínio gerenciado esteja no estado “Suspenso” e, em seguida, entre em contato com suporte.
* O suporte pode restaurar seu domínio gerenciado, somente se existir um backup que for menor que 30 dias.

O domínio gerenciado somente ficará em um estado suspenso por 15 dias. Para recuperar seu domínio gerenciado, a Microsoft recomenda que você resolva os alertas críticos imediatamente.


### <a name="deleted-state"></a>Estado “Excluído”
Um domínio gerenciado que permanece no estado “Suspenso” por 15 dias é **Excluído**.

**O que você pode esperar:**
* Todos os recursos e os backups para o domínio gerenciado serão excluídos.
* Você não pode restaurar o domínio gerenciado e precisa criar um novo domínio gerenciado para usar os serviços de domínio do Azure Active Directory Domain Services.
* Depois de excluído, não são cobrados para o domínio gerenciado.


## <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>Como saber se o seu domínio gerenciado está suspenso?
Você verá uma [alerta](active-directory-ds-troubleshoot-alerts.md) na página de integridade dos serviços de domínio do Azure Active Directory Domain Services no portal do Azure que declara o domínio suspenso. O estado do domínio também mostra "Suspenso".


## <a name="restore-a-suspended-domain"></a>Restaurar um domínio suspenso
Para restaurar um domínio no estado “Suspenso”, conclua as seguintes etapas:

1. Navegue até a página [Azure Active Directory Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) no portal do Azure
2. Clique no domínio gerenciado.
3. No painel de navegação à esquerda, clique em **Integridade**.
4. Clique no alerta. A ID do alerta será AADDS503 ou AADDS504, dependendo da causa da suspensão.
5. Clique no link de resolução fornecido no alerta e siga as etapas para resolver o alerta.

Seu domínio gerenciado pode ser restaurado apenas para a data do último backup. A data de seu último backup é exibida na página de integridade do seu domínio gerenciado. Todas as alterações que ocorreram depois do último backup não serão restauradas. Backups para um domínio gerenciado são armazenados por até 30 dias. Backups com mais de 30 dias serão excluídos.


## <a name="next-steps"></a>Próximas etapas
- [Resolver alertas no domínio gerenciado](active-directory-ds-troubleshoot-alerts.md)
- [Ler mais sobre o Azure AD Domain Services](active-directory-ds-overview.md)
- [Entrar em contato com a equipe do produto](active-directory-ds-contact-us.md)

## <a name="contact-us"></a>Entre em contato
Entre em contato com a equipe de produto do Azure Active Directory Domain Services para [compartilhar comentários ou obter suporte](active-directory-ds-contact-us.md).
