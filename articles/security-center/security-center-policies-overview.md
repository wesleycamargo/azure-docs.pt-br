---
title: "Introdução às políticas da Central de Segurança do Azure | Microsoft Docs"
description: "Saiba mais sobre as políticas de Central de Segurança do Azure e seus recursos principais."
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
ms.openlocfilehash: 95ef2099cb16bcfd550ce2799428f1a16031f535
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2017
---
# <a name="security-policies-overview"></a>Visão geral das políticas de segurança
Este documento fornece uma visão geral das políticas de segurança na Central de Segurança.

## <a name="what-are-security-policies"></a>Quais são políticas de segurança?
Uma política de segurança define a configuração desejada de suas cargas de trabalho e ajuda a garantir a conformidade com requisitos de regulamentação de segurança ou da empresa. Na Central de Segurança, você pode definir políticas para suas assinaturas do Azure, que podem ser adaptadas para o tipo de carga de trabalho ou a confidencialidade dos dados. Por exemplo, os aplicativos que usam dados regulamentados, como as informações de identificação pessoal, podem requerer um nível mais alto de segurança do que outras cargas de trabalho. 

As políticas de Central de Segurança contêm os seguintes componentes:

- Coleta de dados: configurações de provisionamento de agente e de [coleta de dados](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection).
- Política de segurança: que determina quais controles são monitorados e recomendados pela Central de Segurança (edite [política de segurança](https://docs.microsoft.com/en-us/azure/security-center/security-center-policies) na Central de Segurança ou use a [Política do Azure](security-center-azure-policy.md) em versão prévia limitada para criar novas definições, definir políticas adicionais e atribuir políticas entre Grupos de Gerenciamento).
- Notificações por email: configurações de contatos de segurança e de [notificação por email](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details).
- Tipo de preço: [seleção de preços](https://docs.microsoft.com/azure/security-center/security-center-pricing) gratuito ou padrão, que determina quais recursos da Central de Segurança estão disponíveis para os recursos no escopo (pode ser especificado para assinaturas, grupos de recursos e espaços de trabalho). 


## <a name="who-can-edit-security-policies"></a>Quem pode editar as políticas de segurança?
A Central de Segurança usa o RBAC (Controle de Acesso Baseado em Função) que fornece funções internas que podem ser atribuídas a usuários, grupos e serviços no Azure. Quando um usuário abre a Central de Segurança, ele vê apenas as informações relacionadas aos recursos aos quais tem acesso. Isso significa que o usuário recebe a função de Leitor, Colaborador ou Proprietário para a assinatura ou grupo de recursos ao qual o recurso pertence. Além dessas funções, há duas funções específicas da Central de Segurança:

- Leitor de segurança: o usuário que pertence a essa função é capaz de exibir os direitos da Central de segurança, que inclui recomendações, alertas, política e integridade, mas não será capaz de fazer alterações.
- Administrador de segurança: o mesmo que o leitor de segurança, mas ele também pode atualizar a política de segurança, ignorar as recomendações e alertas.


## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu sobre as políticas de segurança na Central de Segurança do Azure. Para saber mais sobre a Central de Segurança do Azure, veja o seguinte:

* [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md) : saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
* [Gerenciando as recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md) : saiba como as recomendações ajudam a proteger os recursos do Azure.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md) : saiba como monitorar a integridade dos recursos do Azure.
* [Gerenciando e respondendo aos alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) : aprenda a gerenciar e responder aos alertas de segurança.
* [Monitorando as soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md) – saiba como monitorar o status de integridade de suas soluções de parceiros.
- [Segurança de dados da Central de Segurança do Azure](security-center-data-security.md) – saiba como os dados são gerenciados e protegidos na Central de Segurança do Azure.
* [Perguntas frequentes da Central de Segurança do Azure](security-center-faq.md) : encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) : obtenha as últimas notícias de segurança e informações do Azure.


