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
ms.date: 10/28/2018
ms.author: terrylan
ms.openlocfilehash: ea014aae49ec322e9a1f1222c881885b84e87584
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2018
ms.locfileid: "52311765"
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

## <a name="data-and-storage-recommendations"></a>Recomendações de disco e de armazenamento

|Tipo de recurso|Classificação de segurança|Recomendações|DESCRIÇÃO|
|----|----|----|----|
|Conta de armazenamento|20|Exigir transferência segura para a conta de armazenamento|A transferência segura é uma opção que força a sua conta de armazenamento a aceitar somente solicitações de conexões seguras (HTTPS). O uso de HTTPS garante a autenticação entre o servidor e o serviço e protege dados em trânsito de ataques de camada de rede, como ataques intermediários, interceptação e sequestro de sessão.|
|Redis|20|Habilitar apenas conexões seguras para seu Cache Redis|Habilite somente conexões via SSL para o Cache Redis. O uso de conexões seguras garante a autenticação entre o servidor e o serviço e protege dados em trânsito de ataques de camada de rede, como ataques intermediários, interceptação e sequestro de sessão.|
|SQL|15|Habilitar Transparent Data Encryption em bancos de dados SQL|Habilite a criptografia transparente de dados para proteger dados em repouso e atender aos requisitos de conformidade.|
|SQL|15|Habilitar auditoria em servidores SQL|Habilite auditoria para servidores SQL do Azure. (Apenas o serviço do SQL Azure. Não inclui o SQL em execução nas suas máquinas virtuais.)|
|SQL|15|Habilitar auditoria em bancos de dados SQL|Habilite auditoria para bancos de dados SQL do Azure. (Apenas o serviço do SQL Azure. Não inclui o SQL em execução nas suas máquinas virtuais.)|
|Data Lake Analytics|15|Habilitar a criptografia em repouso do Data Lake Analytics|Habilite Transparent Data Encryption para proteger os dados em repouso no seu Data Lake Analytics. A criptografia em repouso é transparente, o que significa que o Data Lake Analytics criptografa automaticamente os dados antes de persistir e descriptografa os dados antes de recuperá-los. Nenhuma alteração é necessária em aplicativos e serviços que interagem com o Data Lake Analytics devido à criptografia. A criptografia em repouso minimiza o risco de perda de dados por roubo físico e também ajuda a atender aos requisitos de conformidade a normas.|
|Data Lake Store|15|Habilitar a criptografia em repouso do Data Lake Store|Habilite Transparent Data Encryption para proteger os dados em repouso no seu Data Lake Store. A criptografia em repouso é transparente, o que significa que o Data Lake Store criptografa automaticamente os dados antes de persistir e descriptografa os dados antes de recuperá-los. Você não precisa fazer nenhuma alteração nos aplicativos e serviços que interagem com o Data Lake Store para comportar a criptografia. A criptografia em repouso minimiza o risco de perda de dados por roubo físico e também ajuda a atender aos requisitos de conformidade a normas.|
|Conta de armazenamento|15|Habilitar a criptografia para a Conta de Armazenamento do Microsoft Azure|Habilite a Criptografia do Serviço de Armazenamento do Microsoft Azure para dados em repouso. A SSE (Criptografia do Serviço de Armazenamento) funciona criptografando os dados quando eles são gravados no armazenamento do Azure e descriptografando-os antes da recuperação. Atualmente, a SSE está disponível somente para o serviço Blob do Azure e pode ser usada para blobs de blocos, blobs de páginas e blobs de acréscimo.|
|Data Lake Analytics|5|Habilitar logs de diagnóstico no Data Lake Analytics|Habilite os logs e retenha-os por até um ano. Isso permitirá que você recrie trilhas de atividade para fins de investigação quando ocorrer um incidente de segurança ou sua rede estiver comprometida. |
|Data Lake Store|5|Habilitar logs de diagnóstico no Azure Data Lake Store|Habilite os logs e retenha-os por até um ano. Isso permite recriar trilhas de atividades para fins de investigação quando ocorre um incidente de segurança ou quando sua rede é comprometida. |
|SQL|30|Corrigir vulnerabilidades em seus bancos de dados SQL|A avaliação de vulnerabilidade do SQL verifica a presença de eventuais vulnerabilidades de segurança em seu banco de dados e expõe quaisquer desvios das práticas recomendadas como configurações incorretas, permissões excessivas e dados confidenciais desprotegidos. Resolver as vulnerabilidades encontradas pode melhorar muito o desenvolvimento da segurança de seu banco de dados.|
|SQL|20|Provisionar um administrador do Microsoft Azure Active Directory para o servidor SQL|Provisione um administrador do Microsoft Azure Active Directory para o servidor SQL para habilitar a autenticação do Microsoft Azure Active Directory. A autenticação do Microsoft Azure Active Directory permite o gerenciamento simplificado de permissões e o gerenciamento centralizado de identidades dos usuários de banco de dados e de outros serviços da Microsoft.|
|Conta de armazenamento|15|Desabilitar o acesso de rede irrestrito à conta de armazenamento|Audite o acesso irrestrito à rede nas configurações de firewall da conta de armazenamento. Em vez disso, configure as regras de rede de forma que somente aplicativos das redes permitidas podem acessar a conta de armazenamento. Para permitir conexões de clientes específicos locais ou da internet, o acesso pode ser concedido para o tráfego de redes virtuais do Azure específicas ou para intervalos de endereços IP públicos da internet.|
|Conta de armazenamento|1||Migrar contas de armazenamento aos novos recursos do AzureRM|Use o novo Azure Resource Manager v2 para que suas contas de armazenamento forneçam aprimoramentos de segurança, como: maior controle de acesso (RBCA), melhor auditoria, governança e implantação com base no Resource Manager, acesso a identidades gerenciadas, acesso ao cofre de chaves por segredos, autenticação baseada no Microsoft Azure Active Directory e suporte para marcas e grupos de recursos para facilitar o gerenciamento de segurança.|



## <a name="see-also"></a>Consulte também
Para saber mais sobre as recomendações que se aplicam aos outros tipos de recursos do Azure, consulte o seguinte:

* [Protegendo suas máquinas virtuais na Central de Segurança do Azure](security-center-virtual-machine-recommendations.md)
* [Protegendo seus aplicativos na Central de segurança do Azure](security-center-application-recommendations.md)
* [Protegendo sua rede na Central de Segurança do Azure](security-center-network-recommendations.md)

Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Configurando políticas de segurança na Central de Segurança do Azure](security-center-azure-policy.md) : saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
* [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) : aprenda a gerenciar e a responder a alertas de segurança.
* [Perguntas frequentes da Central de Segurança do Azure](security-center-faq.md) : encontre as perguntas frequentes sobre como usar o serviço.
