---
title: "Introdução às políticas de segurança da Central de Segurança do Azure | Microsoft Docs"
description: "Conheça as políticas de segurança da Central de Segurança do Azure e suas principais funcionalidades."
services: security-center
documentationcenter: na
author: YuriDio
manager: MBaldwin
editor: 
ms.assetid: f24b1e4a-cc36-4542-b21e-041453cdfcd8
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: yurid
ms.openlocfilehash: 7a4d056fa71f211dea5e7be649c39034eabc9395
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2017
---
# <a name="security-policies-overview"></a>Visão geral das políticas de segurança
Este artigo apresenta uma visão geral das políticas de segurança na Central de Segurança.

## <a name="what-are-security-policies"></a>Quais são políticas de segurança?
Uma política de segurança define a configuração desejada de suas cargas de trabalho e ajuda a garantir a conformidade com requisitos de regulamentação de segurança ou da empresa. Na Central de Segurança do Azure, você pode definir políticas para suas assinaturas do Azure e adaptá-las ao tipo de carga de trabalho ou à confidencialidade dos seus dados. Por exemplo, os aplicativos que usam dados regulamentados, como as informações de identificação pessoal, podem exigir um nível mais alto de segurança do que outras cargas de trabalho. 

As políticas de Central de Segurança contêm os seguintes componentes:

- **Coleta de dados**: determina configurações de provisionamento de agente e de [coleta de dados](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection).
- **Política de segurança**: determina que controles a Central de Segurança monitora e recomenda. Você pode editar a [política de segurança](https://docs.microsoft.com/azure/security-center/security-center-policies) na Central de Segurança. Você também pode usar o [Azure Policy](security-center-azure-policy.md) (em versão prévia limitada) para criar novas definições, definir políticas adicionais e atribuir políticas entre grupos de gerenciamento.
- **Notificações por email**: determina contatos de segurança e configurações de [notificação por email](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details).
- **Tipo de preço**: define a [seleção de preço](https://docs.microsoft.com/azure/security-center/security-center-pricing) padrão ou gratuita. A camada que você escolhe determina quais recursos da Central de Segurança estão disponíveis para os recursos no escopo. Você pode especificar uma camada para assinaturas, grupos de recursos e espaços de trabalho. 


## <a name="who-can-edit-security-policies"></a>Quem pode editar as políticas de segurança?
A Central de Segurança usa o RBAC (Controle de Acesso Baseado em Função) que fornece funções internas que podem ser atribuídas a usuários, grupos e serviços no Azure. Quando os usuários abrem a Central de Segurança, eles veem somente informações relacionadas aos recursos aos quais eles têm acesso. Isso significa que os usuários recebem a função de *proprietário*, *colaborador* ou *leitor* para a assinatura ou grupo de recursos ao qual o recurso pertence. Além dessas funções, há duas funções específicas da Central de Segurança:

- **Leitor de segurança**: tem direitos de exibição para a Central de Segurança, incluindo recomendações, alertas, política e integridade, mas não podem fazer alterações.
- **Administrador de segurança**: têm os mesmos direitos de exibição que o *leitor de segurança* e também podem atualizar a política de segurança e ignorar recomendações e alertas.


## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu sobre as políticas de segurança na Central de Segurança do Azure. Para saber mais sobre a Central de Segurança do Azure, leia os seguintes artigos:

* [Como configurar políticas de segurança na Central de Segurança do Azure](security-center-policies.md): saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
* [Como gerenciar as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md): saiba como as recomendações da Central de Segurança ajudam a proteger os recursos do Azure.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md): saiba como monitorar a integridade dos recursos do Azure.
* [Gerenciando e respondendo aos alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md): aprenda a gerenciar e responder aos alertas de segurança.
* [Monitorando as soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md): saiba como monitorar o status de integridade de suas soluções de parceiros.
- [Segurança de dados da Central de Segurança do Azure](security-center-data-security.md): saiba como a Central de Segurança gerencia e protege os dados.
* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md): encontre as respostas para as perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/): obtenha as últimas informações e notícias de segurança do Azure.


