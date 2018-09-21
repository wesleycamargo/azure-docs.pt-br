---
title: Protegendo os dados e o serviço SQL do Azure na Central de Segurança do Azure | Microsoft Docs
description: Este documento aborda as recomendações na Central de Segurança do Azure que ajudam a proteger seus dados e o serviço SQL do Azure, bem como a cumprir as políticas de segurança.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2017
ms.author: terrylan
ms.openlocfilehash: 45f5dc840f015793912e314ab3d47e54a409708e
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46126659"
---
# <a name="protecting-azure-sql-service-and-data-in-azure-security-center"></a>Proteção dos dados e do serviço SQL do Azure na Central de Segurança do Azure
A Central de Segurança do Azure analisa o estado de segurança de seus recursos do Azure. Quando a Central de Segurança identifica possíveis vulnerabilidades de segurança, ela cria recomendações que orientam você durante o processo de configuração dos controles necessários.  As recomendações se aplicam aos tipos de recurso do Azure: VMs (máquinas virtuais), rede, SQL e dados, e aplicativos.

Este artigo aborda as recomendações que se aplicam aos dados e serviço SQL do Azure. As recomendações giram em torno de como habilitar a auditoria para os bancos de dados e servidores SQL do Azure, como habilitar a criptografia para bancos de dados SQL e como habilitar a criptografia da conta de armazenamento do Azure.  Use a tabela abaixo como referência para entender as recomendações de dados e serviço SQL disponíveis e a ação de cada uma delas se forem aplicadas.
### <a name="monitor-data-security"></a>Monitorar segurança de dados

Quando você clica em **Segurança de dados** na seção **Prevenção**, a opção **Recursos de Dados** abre com recomendações para SQL e Armazenamento. Ela também contém [recomendações](security-center-sql-service-recommendations.md) para o estado de integridade geral do banco de dados. Para saber mais sobre criptografia de armazenamento, leia [Habilitar a criptografia para a conta de armazenamento do Azure na Central de Segurança do Azure](security-center-enable-encryption-for-storage-account.md).

![Recursos de dados](./media/security-center-monitoring/security-center-monitoring-fig13-newUI-2017.png)

Em **Recomendações de SQL**, você pode clicar em qualquer recomendação e obter mais detalhes sobre uma ação adicional para resolver um problema. O exemplo a seguir mostra a expansão da recomendação **Auditoria e Detecção de ameaças do banco de dados em bancos de dados SQL**.

![Detalhes sobre uma recomendação de SQL](./media/security-center-monitoring/security-center-monitoring-fig14-ga-new.png)

A opção **Habilitar Auditoria e Detecção de ameaças nos bancos de dados SQL** contém as seguintes informações:

* Uma lista de bancos de dados SQL
* O servidor no qual eles estão localizados
* Informações indicando se essa configuração foi herdada do servidor ou se é exclusiva do banco de dados
* O estado atual
* A gravidade do problema

Quando você clicar no banco de dados para lidar com essa recomendação, a opção **Auditoria e Detecção de Ameaças** será aberta, conforme mostrado na tela a seguir.

![Auditoria e Detecção de ameaças](./media/security-center-monitoring/security-center-monitoring-fig15-ga.png)

Para habilitar a auditoria, selecione **ATIVADO** na opção **Auditoria**.

## <a name="available-sql-service-and-data-recommendations"></a>Recomendações de dados e serviço SQL disponíveis
| Recomendações | DESCRIÇÃO |
| --- | --- |
| [Habilitar a detecção de ameaças e auditoria em servidores SQL](security-center-enable-auditing-on-sql-servers.md) |Recomenda que você habilite auditoria e detecção de ameaças para servidores Azure SQL (somente serviço Azure SQL; não inclui SQL em execução em máquinas virtuais). |
| [Habilitar a detecção de ameaças e auditoria em bancos de dados SQL](security-center-enable-auditing-on-sql-databases.md) |Recomenda que você habilite auditoria e detecção de ameaças para bancos de dados SQL do Azure (somente serviço Azure SQL; não inclui SQL em execução em máquinas virtuais). |
| [Habilitar Transparent Data Encryption em bancos de dados SQL](security-center-enable-transparent-data-encryption.md) |Recomenda que você habilite a criptografia para bancos de dados SQL (apenas serviço do Azure SQL). |

## <a name="see-also"></a>Consulte também
Para saber mais sobre as recomendações que se aplicam aos outros tipos de recursos do Azure, consulte o seguinte:

* [Protegendo suas máquinas virtuais na Central de Segurança do Azure](security-center-virtual-machine-recommendations.md)
* [Protegendo seus aplicativos na Central de segurança do Azure](security-center-application-recommendations.md)
* [Protegendo sua rede na Central de Segurança do Azure](security-center-network-recommendations.md)

Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Configurando políticas de segurança na Central de Segurança do Azure](security-center-policies.md) : saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
* [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) : aprenda a gerenciar e a responder a alertas de segurança.
* [Perguntas frequentes da Central de Segurança do Azure](security-center-faq.md) : encontre as perguntas frequentes sobre como usar o serviço.
