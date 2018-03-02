---
title: "Azure AD Connect Health e Regulamento Geral sobre a Proteção de Dados | Microsoft Docs"
description: Este documento descreve como obter a conformidade GDPR com o Azure AD Connect.
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/18/2018
ms.author: billmath
ms.openlocfilehash: b9a0b9027bbead00300040186e453933b3a7f46b
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="gdpr-compliance-and-azure-ad-connect-health"></a>Conformidade GDPR e Azure AD Connect Health 

O [GDPR (Regulamento Geral sobre a Proteção de Dados)](http://ec.europa.eu/justice/data-protection/reform/index_en.htm) é uma lei de privacidade e proteção de dados da União Europeia (UE). O GDPR impõe novas regras às empresas, órgãos governamentais, organizações sem fins lucrativos e outras organizações que oferecem bens e serviços para pessoas da UE ou que coletam e analisam dados vinculados a residentes da UE. 

Os produtos e serviços da Microsoft estão disponíveis atualmente para ajudá-lo a atender aos requisitos do GDPR. Leia mais sobre a Política de Privacidade da Microsoft na [Central de Confiabilidade](https://www.microsoft.com/trustcenter).

O Azure AD Connect Health monitora a infraestrutura de identidade local e o serviço de sincronização. Ele também fornece informações e alertas de superfícies. A Microsoft está comprometida com a conformidade GDPR em todos os serviços de nuvem, quando a imposição iniciar-se em maio de 2018 e fornecer garantias relacionadas ao GDPR em seus compromissos contratuais. 

>[!NOTE] 
> Este artigo descreve a conformidade GDPR no Azure AD Connect Health. Para obter informações sobre a conformidade GDPR no Azure AD Connect, consulte [Conformidade GDPR e Azure AD Connect](../../active-directory/connect/active-directory-aadconnect-gdpr.md).

## <a name="gdpr-classification"></a>Classificação do GDPR
O Azure AD Connect Health se encaixa na categoria **processador de dados** da classificação do GDPR. Como um pipeline de processador de dados, o serviço fornece serviços de processamento de dados para parceiros-chave e consumidores finais. O Azure AD Connect Health não gera dados de usuário e não possui controle independente com relação aos dados pessoais coletados e como são utilizados. A recuperação de dados, agregação, análise e relatórios no Azure AD Connect Health são baseados em dados locais existentes. 

## <a name="data-retention-policy"></a>Política de retenção de dados
O Azure AD Connect Health não gera relatórios, realiza análise nem fornece informações com mais de 30 dias. Portanto, o Azure AD Connect Health não armazena, processa ou retém quaisquer dados com mais de 30 dias. Este design está em conformidade com os regulamentos do GDPR, com os regulamentos de conformidade de privacidade da Microsoft e com as políticas de retenção de dados do Azure Active Directory. 
 
## <a name="disable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Desabilitar a coleta e monitoramento de dados no Azure AD Connect Health
O Azure AD Connect Health habilita interromper a coleta de dados para cada servidor monitorado individual ou para uma instância de um serviço monitorado. Por exemplo, é possível parar a coleta de dados de servidores individuais AD FS (Serviços de Federação do Active Directory) que são monitorados usando o Azure AD Connect Health. Você também pode parar a coleta de dados de toda a instância do AD FS que está sendo monitorada usando o Azure AD Connect Health. Ao optar por fazer isso, os servidores correspondentes serão excluídos do Portal do Azure AD Connect Health após parar a coleta de dados. 

>[!IMPORTANT]
> É necessário ter privilégios de Administrador Global do Azure Active Directory ou a função Colaborador no RBAC para excluir servidores monitorados do Azure AD Connect Health.
>
> Remover uma instância de serviço ou servidor do Azure AD Connect Health não é uma ação reversível. 

### <a name="what-to-expect"></a>O que esperar?
Se você parar a coleta e monitoramento de dados de um servidor monitorado individual ou uma instância de um serviço monitorado, observe o seguinte:

- Ao excluir uma instância de um serviço monitorado, a instância será removida da lista de serviços de monitoramento do Azure AD Connect Health no portal. 
- Ao excluir um servidor monitorado ou uma instância de um serviço monitorado, o Agente de Integridade NÃO será desinstalado nem removido dos servidores. O Agente de Integridade é configurado para não enviar dados para o Azure AD Connect Health. É necessário desinstalar manualmente o Agente de Integridade em servidores anteriormente monitorados.
- Se você não tiver desinstalado o agente de integridade antes de executar essa etapa, poderá ver os eventos de erro no(s) servidor(es) relacionado(s) ao agente de integridade.
- Todos os dados pertencentes à instância do serviço monitorado são excluídos de acordo com a Política de Retenção de Dados do Microsoft Azure.

### <a name="disable-data-collection-and-monitoring-for-a-monitored-server"></a>Desabilitar a coleta de dados e o monitoramento de um servidor monitorado
Consulte [Como remover um servidor do Azure AD Connect Health](active-directory-aadconnect-health-operations.md#to-delete-a-server-from-the-azure-ad-connect-health-service).

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>Desabilitar a coleta e o monitoramento de dados para uma instância de um serviço monitorado
Consulte [Como remover uma instância de serviço do Azure AD Connect Health](active-directory-aadconnect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service).


## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>Habilitar novamente a coleta e o monitoramento de dados no Azure AD Connect Health
Para habilitar novamente o monitoramento no Azure AD Connect Health de um serviço monitorado excluído anteriormente, é necessário desinstalar e [reinstalar o agente de integridade](active-directory-aadconnect-health-agent-install.md) em todos os servidores.


## <a name="next-steps"></a>Próximas etapas
* [Revise a Política de Privacidade da Microsoft na Central de Confiabilidade](https://www.microsoft.com/trustcenter)
* [Azure AD Connect e GDPR](../../active-directory/connect/active-directory-aadconnect-gdpr.md)
* [Operações de Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
