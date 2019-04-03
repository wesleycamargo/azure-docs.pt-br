---
title: Protegendo os dados e o serviço SQL do Azure na Central de Segurança do Azure | Microsoft Docs
description: Este documento aborda as recomendações na Central de Segurança do Azure que ajudam a proteger seus dados e o serviço SQL do Azure, bem como a cumprir as políticas de segurança.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/15/2019
ms.author: rkarlin
ms.openlocfilehash: 479e6d222c9fc7d007252bab12e807ec337ea9e6
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58880773"
---
# <a name="protecting-azure-sql-service-and-data-in-azure-security-center"></a>Proteção dos dados e do serviço SQL do Azure na Central de Segurança do Azure
A Central de Segurança do Azure analisa o estado de segurança de seus recursos do Azure. Quando a Central de Segurança identifica possíveis vulnerabilidades de segurança, ela cria recomendações que orientam você durante o processo de configuração dos controles necessários.  As recomendações se aplicam aos tipos de recurso do Azure: VMs (máquinas virtuais), rede, SQL e dados, e aplicativos.


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

## <a name="data-and-storage-recommendations"></a>Recomendações de disco e de armazenamento

|Tipo de recurso|Classificação de segurança|Recomendações|DESCRIÇÃO|
|----|----|----|----|
|Conta de armazenamento|20|Exigir transferência segura para a conta de armazenamento|A transferência segura é uma opção que força a sua conta de armazenamento a aceitar somente solicitações de conexões seguras (HTTPS). O uso de HTTPS garante a autenticação entre o servidor e o serviço e protege dados em trânsito de ataques de camada de rede, como ataques intermediários, interceptação e sequestro de sessão.|
|Redis|20|Habilitar apenas conexões seguras para seu Cache Redis do Azure|Habilite apenas conexões via SSL para o Cache Redis do Azure. O uso de conexões seguras garante a autenticação entre o servidor e o serviço e protege dados em trânsito de ataques de camada de rede, como ataques intermediários, interceptação e sequestro de sessão.|
|SQL|15|Habilitar Transparent Data Encryption em bancos de dados SQL|Habilite a criptografia transparente de dados para proteger dados em repouso e atender aos requisitos de conformidade.|
|SQL|15|Habilitar auditoria em servidores SQL|Habilite auditoria para servidores SQL do Azure. (Apenas o serviço do SQL Azure. Não inclui o SQL em execução nas suas máquinas virtuais.)|
|SQL|15|Habilitar auditoria em bancos de dados SQL|Habilite auditoria para bancos de dados SQL do Azure. (Apenas o serviço do SQL Azure. Não inclui o SQL em execução nas suas máquinas virtuais.)|
|Data Lake Analytics|15|Habilitar a criptografia em repouso do Data Lake Analytics|Habilite Transparent Data Encryption para proteger os dados em repouso no seu Data Lake Analytics. A criptografia em repouso é transparente, o que significa que o Data Lake Analytics criptografa automaticamente os dados antes de persistir e descriptografa os dados antes de recuperá-los. Nenhuma alteração é necessária em aplicativos e serviços que interagem com o Data Lake Analytics devido à criptografia. A criptografia em repouso minimiza o risco de perda de dados por roubo físico e também ajuda a atender aos requisitos de conformidade a normas.|
|Data Lake Store|15|Habilitar a criptografia em repouso do Data Lake Store|Habilite Transparent Data Encryption para proteger os dados em repouso no seu Data Lake Store. A criptografia em repouso é transparente, o que significa que o Data Lake Store criptografa automaticamente os dados antes de persistir e descriptografa os dados antes de recuperá-los. Você não precisa fazer nenhuma alteração nos aplicativos e serviços que interagem com o Data Lake Store para comportar a criptografia. A criptografia em repouso minimiza o risco de perda de dados por roubo físico e também ajuda a atender aos requisitos de conformidade a normas.|
|Data Lake Analytics|5|Habilitar logs de diagnóstico no Data Lake Analytics|Ativar os registros e mantenha-os por até um ano. Isso permitirá que você recrie trilhas de atividade para fins de investigação quando ocorrer um incidente de segurança ou sua rede estiver comprometida. |
|Data Lake Store|5|Habilitar logs de diagnóstico no Azure Data Lake Store|Ativar os registros e mantenha-os por até um ano. Isso permite recriar trilhas de atividades para fins de investigação quando ocorre um incidente de segurança ou quando sua rede é comprometida. |
|SQL|30|Corrigir vulnerabilidades em seus bancos de dados SQL|A avaliação de vulnerabilidade do SQL verifica a presença de eventuais vulnerabilidades de segurança em seu banco de dados e expõe quaisquer desvios das práticas recomendadas como configurações incorretas, permissões excessivas e dados confidenciais desprotegidos. Resolver as vulnerabilidades encontradas pode melhorar muito o desenvolvimento da segurança de seu banco de dados.|
|SQL|20|Provisionar um administrador do Microsoft Azure Active Directory para o servidor SQL|Provisione um administrador do Microsoft Azure Active Directory para o servidor SQL para habilitar a autenticação do Microsoft Azure Active Directory. A autenticação do Microsoft Azure Active Directory permite o gerenciamento simplificado de permissões e o gerenciamento centralizado de identidades dos usuários de banco de dados e de outros serviços da Microsoft.|
|Conta de armazenamento|15|Desabilitar o acesso de rede irrestrito à conta de armazenamento|Audite o acesso irrestrito à rede nas configurações de firewall da conta de armazenamento. Em vez disso, configure as regras de rede de forma que somente aplicativos das redes permitidas podem acessar a conta de armazenamento. Para permitir conexões da Internet específica ou em clientes locais, você pode conceder acesso ao tráfego de redes virtuais do Azure de específicas ou a intervalos de endereços IP de Internet públicos.|
|Conta de armazenamento|1|Migrar contas de armazenamento para recursos de modelo do Azure Resource Manager|Use o novo v2 do Azure Resource Manager para suas contas de armazenamento para fornecer melhorias de segurança, como: mais forte controle de acesso (RBAC), auditoria melhor, com base no Gerenciador de recursos de implantação e governança, acesso a identidades gerenciadas, o acesso ao Cofre de chaves para segredos e a autenticação baseada no AD do Azure e suporte para marcas e os grupos de recursos para facilitar o gerenciamento de segurança.|

## <a name="see-also"></a>Consulte também
Para saber mais sobre as recomendações que se aplicam aos outros tipos de recursos do Azure, consulte o seguinte:

* [Protegendo suas máquinas virtuais na Central de Segurança do Azure](security-center-virtual-machine-recommendations.md)
* [Protegendo seus aplicativos na Central de segurança do Azure](security-center-application-recommendations.md)
* [Protegendo sua rede na Central de Segurança do Azure](security-center-network-recommendations.md)

Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Configurando políticas de segurança na Central de Segurança do Azure](tutorial-security-policy.md) : saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
* [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) : aprenda a gerenciar e a responder a alertas de segurança.
* [Perguntas frequentes da Central de Segurança do Azure](security-center-faq.md) : encontre as perguntas frequentes sobre como usar o serviço.
