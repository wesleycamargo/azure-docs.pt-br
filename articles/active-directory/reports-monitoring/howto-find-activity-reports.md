---
title: Encontrar relatórios de atividade de usuário do Azure Active Directory no portal do Azure | Microsoft Docs
description: Saiba onde os relatórios de atividade do usuário do Azure Active Directory estão no portal do Azure.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46fc0a49ebae86a715685e1073b4eb7cc10e6032
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57533401"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Localizar relatórios de atividade no Portal do Azure

Neste artigo, você aprenderá a localizar relatórios de atividades de usuários do Azure AD (Azure Active Directory) no portal do Azure.

## <a name="audit-logs-report"></a>Relatório de logs de auditoria

O relatório de logs de auditoria combina vários relatórios sobre atividades de aplicativos em uma única exibição para relatórios baseados em contexto. Para acessar o relatório de logs de auditoria:

1. Navegue até o [Portal do Azure](https://portal.azure.com).
2. Selecione o diretório no canto superior direito e, em seguida, selecione a folha **Azure Active Directory** do painel de navegação esquerdo.
3. Selecione **Logs de auditorias** da seção **Atividade** da folha do Azure Active Directory. 

    ![Logs de auditoria](./media/howto-find-activity-reports/482.png "Logs de auditoria")

O relatório de logs de auditoria consolida os seguintes relatórios:

* Relatório de auditoria
* Atividade de redefinição de senha
* Atividade de registro de redefinição de senha
* Atividade dos grupos de autoatendimento
* Alterações de nome do grupo do Office365
* Atividade de provisionamento de conta
* Status de substituição de senha
* Erros de provisionamento de conta

### <a name="filtering-on-audit-logs"></a>Filtragem em logs de auditoria

Você pode usar a filtragem avançada no relatório de auditoria para acessar uma categoria específica de dados de auditoria, especificando-na **categoria** filtro. Por exemplo, para exibir todas as atividades relacionadas aos usuários, selecione a **UserManagement** categoria. 

As categorias incluem:

- Todos
- AdministrativeUnit
- ApplicationManagement
- Authentication
- Autorização
- Contato
- Dispositivo
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- Outros
- Política
- ResourceManagement
- RoleManagement
- UserManagement

Você também pode filtrar em um serviço específico usando o **serviço** filtro de lista suspensa. Por exemplo, para obter todos os eventos de auditoria relacionados ao gerenciamento de senha de autoatendimento, selecione a **gerenciamento de senha de autoatendimento** filtro.

Os serviços incluem:

- Todos
- Revisões de acesso
- Provisionamento de conta de usuário 
- Aplicativo de SSO
- Métodos de autenticação
- B2C
- Acesso Condicional
- Diretório principal
- Gerenciamento de Direitos
- Identity Protection
- Usuários Convidados
- PIM
- Gerenciamento de grupos de autoatendimento
- Gerenciamento de autoatendimento Passord
- Termos de Uso

## <a name="sign-ins-report"></a>Relatório de entradas 

A exibição **Entradas** inclui todas as entradas do usuário, bem como o relatório **Uso do Aplicativo**. Também é possível exibir as informações de uso do aplicativo na seção **Gerenciar** da visão geral dos **Aplicativos empresariais**.

Para acessar o relatório de entradas:

1. Navegue até o [Portal do Azure](https://portal.azure.com).
2. Selecione o diretório no canto superior direito e, em seguida, selecione a folha **Azure Active Directory** do painel de navegação esquerdo.
3. Selecione **Entradas** da seção **Atividade** da folha Azure Active Directory. 

    ![Exibição de entradas](./media/howto-find-activity-reports/483.png "Exibição de entradas")


### <a name="filtering-on-application-name"></a>Filtragem no nome do aplicativo

É possível usar o relatório de entradas para exibir detalhes sobre o uso do aplicativo, filtrando o nome de usuário ou o nome do aplicativo.

![Página Filtrar Eventos de Entrada](./media/howto-find-activity-reports/07.png "Página Filtrar Eventos de Entrada")

## <a name="security-reports"></a>Relatórios de segurança

### <a name="anomalous-activity-reports"></a>Relatórios de atividades anômalas

Relatórios de atividade anômalas fornecem informações sobre eventos de risco relacionados à segurança que o Azure AD pode detectar e relatar.

A tabela a seguir lista os relatórios de segurança de atividades anômalas do Azure AD e os tipos de evento de risco correspondentes no Portal do Azure. Para saber mais, veja [Eventos de risco do Azure Active Directory](concept-risk-events.md).  


| Relatório de atividades anômalas do Azure AD |  Tipo de evento de risco do Identity Protection|
| :--- | :--- |
| Usuários com credenciais vazadas | Credenciais vazadas |
| Atividades de entrada irregulares | Viagem impossível a locais atípicos |
| Entradas de dispositivos possivelmente infectados | Entradas de dispositivos infectados|
| Entradas de fontes desconhecidas | Entradas de endereços IP anônimos |
| Entradas de endereços IP com atividade suspeita | Entradas de endereços IP com atividade suspeita |
| - | Entradas de locais desconhecidos |

Os seguintes relatórios de segurança de atividade anômala do Azure AD não são incluídos como eventos de risco no Portal do Azure:

* Entradas após várias falhas
* Entradas de várias geografias


### <a name="detected-risk-events"></a>Eventos de risco detectados

Você pode acessar relatórios sobre eventos de risco detectados na seção **Segurança** da folha **Azure Active Directory** no [portal do Azure](https://portal.azure.com). Os eventos de risco detectados são controlados nos seguintes relatórios:   

- [Usuários em risco](concept-user-at-risk.md)
- [Entradas de risco](concept-risky-sign-ins.md)

    ![Relatórios de segurança](./media/howto-find-activity-reports/04.png "Relatórios de segurança")

## <a name="troubleshoot-issues-with-activity-reports"></a>Solucionar problemas com relatórios de atividade

### <a name="missing-data-in-the-downloaded-activity-logs"></a>Dados ausentes nos logs de atividade baixados

#### <a name="symptoms"></a>Sintomas 

Baixei os logs de atividade (auditoria ou entradas) e não vejo todos os registros para o momento que escolhi. Por quê? 

 ![Relatórios](./media/troubleshoot-missing-data-download/01.png)
 
#### <a name="cause"></a>Causa

Ao baixar logs de atividades no portal do Azure, limitamos a escala a 5000 registros, classificados primeiro pelos mais recentes. 

#### <a name="resolution"></a>Resolução

Você pode aproveitar as [APIs de relatórios do Azure AD](concept-reporting-api.md) para buscar até um milhões de registros em qualquer momento determinado. Nossa abordagem recomendada é [executar um script com base em agendamento](tutorial-signin-logs-download-script.md) que chame as APIs de relatórios para buscar registros de maneira incremental durante um período de tempo (por exemplo, diariamente ou semanalmente). 

### <a name="missing-audit-data-for-recent-actions-in-the-azure-portal"></a>Dados de auditoria ausentes para ações recentes no portal do Azure

#### <a name="symptoms"></a>Sintomas

Eu executei algumas ações no portal do Azure e esperava ver os logs de auditoria para essas ações na folha `Activity logs > Audit Logs`, mas não é possível encontrá-los.

 ![Relatórios](./media/troubleshoot-missing-audit-data/01.png)
 
#### <a name="cause"></a>Causa

As ações não são exibidas imediatamente nos logs de atividades. A tabela a seguir enumera nossos números de latência para os logs de atividades. 

| Relatório | &nbsp; | Latência (P95) | Latência (P99) |
|--------|--------|---------------|---------------|
| Auditoria de diretório | &nbsp; | 2 minutos | 5 min |
| Atividade de entrada | &nbsp; | 2 minutos | 5 min | 

#### <a name="resolution"></a>Resolução

Aguarde de 15 minutos a duas horas e verifique se as ações aparecem no log. Se você não vir os logs mesmo após duas horas, [crie um tíquete de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) e verificaremos o problema.

### <a name="missing-logs-for-recent-user-sign-ins-in-the-azure-ad-sign-ins-activity-log"></a>Logs ausentes para recentes entradas de usuário no Azure AD

#### <a name="symptoms"></a>Sintomas

Eu entrei recentemente no portal do Azure e esperava ver os logs de auditoria para essas ações na folha `Activity logs > Sign-ins`, mas não é possível encontrá-los.

 ![Relatórios](./media/troubleshoot-missing-audit-data/02.png)
 
#### <a name="cause"></a>Causa

As ações não são exibidas imediatamente nos logs de atividades. A tabela a seguir enumera nossos números de latência para os logs de atividades. 

| Relatório | &nbsp; | Latência (P95) | Latência (P99) |
|--------|--------|---------------|---------------|
| Auditoria de diretório | &nbsp; | 2 minutos | 5 min |
| Atividade de entrada | &nbsp; | 2 minutos | 5 min | 

#### <a name="resolution"></a>Resolução

Aguarde de 15 minutos a duas horas e verifique se as ações aparecem no log. Se você não vir os logs mesmo após duas horas, [crie um tíquete de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) e verificaremos o problema.

### <a name="i-cant-view-more-than-30-days-of-report-data-in-the-azure-portal"></a>Não consigo exibir mais de 30 dias de dados de relatório no portal do Azure

#### <a name="symptoms"></a>Sintomas

Não consigo exibir mais de 30 dias de dados de entrada e de auditoria no portal do Azure. Por quê? 

 ![Relatórios](./media/troubleshoot-missing-audit-data/03.png)

#### <a name="cause"></a>Causa

Dependendo da sua licença, as Ações do Azure Active Directory armazenam relatórios de atividades para as durações a seguir:

| Relatório           | &nbsp; |  AD do Azure Gratuito | Azure AD Premium P1 | Azure AD Premium P2 |
| ---              | ----   |  ---           | ---                 | ---                 |
| Auditoria de Diretório  | &nbsp; |   7 dias     | 30 dias             | 30 dias             |
| Atividade de Entrada | &nbsp; | Não disponível. Você pode acessar sua própria atividade de entrada por 7 dias na folha de perfil do usuário individual | 30 dias | 30 dias             |

Para saber mais informações, confira [Políticas de retenção de relatório do Azure Active Directory](reference-reports-data-retention.md).  

#### <a name="resolution"></a>Resolução

Você tem duas opções para manter os dados por mais de 30 dias. Você pode usar as [APIs de emissão de relatórios do Azure AD](concept-reporting-api.md) para recuperar os dados por meio de programação e armazená-los em um banco de dados. Como alternativa, você pode integrar os logs de auditoria em um sistema SIEM de terceiros, como o Splunk ou SumoLogic.

## <a name="next-steps"></a>Próximas etapas

* [Visão geral de logs de auditoria](concept-audit-logs.md)
* [Visão geral de entradas](concept-sign-ins.md)
* [Visão geral de eventos de risco](concept-risk-events.md)
