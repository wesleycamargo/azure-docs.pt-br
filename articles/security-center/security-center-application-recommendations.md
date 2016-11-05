---
title: Protegendo seus aplicativos na Central de Segurança do Azure | Microsoft Docs
description: Este documento endereça as recomendações na Central de Segurança do Azure que ajudam a proteger os recursos do Azure e cumprir as políticas de segurança.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''

ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2016
ms.author: terrylan

---
# Protegendo seus aplicativos na Central de segurança do Azure
A Central de Segurança do Azure analisa o estado de segurança de seus recursos do Azure. Quando a Central de Segurança identifica possíveis vulnerabilidades de segurança, ela cria recomendações que orientam você durante o processo de configuração dos controles necessários. As recomendações se aplicam aos tipos de recursos do Azure: máquinas virtuais (VMs), rede, aplicativos e SQL.

Este artigo aborda as recomendações que se referem aos aplicativos. As recomendações do aplicativo giram em torno da implantação de um firewall do aplicativo Web. Use a tabela a seguir como referência para ajudá-lo a entender as recomendações disponíveis do aplicativo e o que cada uma delas fará se você as aplicar.

## Recomendações disponíveis do aplicativo
| Recomendações | Descrição |
| --- | --- |
| [Adicione um firewall do aplicativo Web](security-center-add-web-application-firewall.md) |Recomenda que você implante um WAF (firewall do aplicativo Web) para pontos de extremidade da Web. Você pode proteger vários aplicativos Web na Central de segurança adicionando-os às suas implantações do WAF existentes. Dispositivos WAF (criados usando o modelo de implantação do Gerenciador de Recursos) precisam ser implantados em uma rede virtual separada. Dispositivos WAF (criados usando o modelo de implantação clássico) são restritos ao uso de um grupo de segurança de rede. No futuro, esse suporte será estendido para uma implantação totalmente personalizada de um dispositivo WAF (clássica). |
| [Finalizar a proteção do aplicativo](security-center-add-web-application-firewall.md#finalize-application-protection) |Para concluir a configuração de um WAF, o tráfego deve ser roteado para o dispositivo do WAF. Se essa recomendação for seguida, serão concluídas as alterações de configuração necessárias. |

## Confira também
Para saber mais sobre as recomendações que se aplicam aos outros tipos de recursos do Azure, consulte o seguinte:

* [Protegendo suas máquinas virtuais na Central de Segurança do Azure](security-center-virtual-machine-recommendations.md)
* [Protegendo sua rede na Central de Segurança do Azure](security-center-network-recommendations.md)
* [Protegendo o serviço do SQL Azure na Central de Segurança do Azure](security-center-sql-service-recommendations.md)

Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md): saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
* [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md): aprenda a gerenciar e a responder a alertas de segurança.
* [Perguntas frequentes da Central de Segurança do Azure](security-center-faq.md): encontre as perguntas frequentes sobre como usar o serviço.

<!---HONumber=AcomDC_0810_2016-->