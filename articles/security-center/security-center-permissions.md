---
title: "Permissões na Central de Segurança do Azure | Microsoft Docs"
description: "Este artigo explica como a Central de Segurança do Azure usa o controle de acesso baseado em função para atribuir permissões aos usuários e identifica as ações permitidas para cada função."
services: security-center
cloud: na
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
ms.assetid: 
ms.service: security-center
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 5c4a08d96175d431e0a29dfc5927b64567c40117
ms.openlocfilehash: d1c8a39bbcda7d22fdce08d122098e994ca87451


---

# <a name="permissions-in-azure-security-center"></a>Permissões na Central de Segurança do Azure

A Central de Segurança do Azure usa o [RBAC (Controle de Acesso Baseado em Função)](../active-directory/role-based-access-control-configure.md), que fornece [funções internas](../active-directory/role-based-access-built-in-roles.md) que podem ser atribuídas a usuários, grupos e serviços no Azure.

A Central de Segurança avalia a configuração de seus recursos para identificar problemas de segurança e vulnerabilidades. Na Central de Segurança, você vê apenas as informações relacionadas a um recurso quando for atribuído à função de Proprietário, Colaborador ou Leitor da assinatura ou do grupo de recursos ao qual o recurso pertence.

## <a name="roles-and-allowed-actions"></a>Funções e ações permitidas

A tabela a seguir exibe as funções e as ações permitidas na Central de Segurança. Um X indica que a ação é permitida para essa função.

| Função | Editar política de segurança | Aplicar as recomendações de segurança a um recurso | Corrigir ou Ignorar alertas | Exibir os alertas em uma assinatura | Exibir os alertas de um recurso específico |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Proprietário da assinatura | X | X | X | X | X |
| Colaborador da assinatura | X | X | X | X | X |
| Proprietário do Grupo de Recursos | -- | X | -- | -- | X |
| Colaborador do Grupo de Recursos | -- | X | -- | -- | X |
| Leitor | -- | -- | -- | X | X |

> [!NOTE]
> Recomendamos que você atribua a função menos permissiva necessária para os usuários realizarem suas tarefas. Por exemplo, atribua a função Leitor aos usuários que precisam apenas exibir informações sobre a integridade da segurança de um recurso, mas que não precisam executar nenhuma ação, como aplicar recomendações ou editar políticas.
>
>

## <a name="next-steps"></a>Próximas etapas
Este artigo explicou como a Central de Segurança usa o RBAC para atribuir permissões aos usuários e identificou as ações permitidas para cada função. Agora que você está familiarizado com as atribuições de função necessárias para monitorar o estado de segurança de sua assinatura, editar as políticas de segurança e aplicar recomendações, saiba como:

- [Configurar políticas de segurança na Central de Segurança do Azure](security-center-policies.md)
- [Gerenciando recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md)
- [Monitoramento da integridade da segurança na Central de Segurança do Azure](security-center-monitoring.md)
- [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md)
- [Monitoring partner solutions with Azure Security Center](security-center-partner-solutions.md) (Monitorando soluções de parceiros com a Central de Segurança do Azure)



<!--HONumber=Dec16_HO2-->


