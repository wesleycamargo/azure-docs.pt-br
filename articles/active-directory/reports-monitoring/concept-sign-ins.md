---
title: Relatórios de atividade de entrada no portal do Azure Active Directory | Microsoft Docs
description: Introdução aos relatórios de atividades de entrada no portal do Azure Active Directory
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 993a8ca48df40b400c21852d3d28941d9d62affb
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57549010"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Relatórios de atividades de entrada no portal do Azure Active Directory

A arquitetura de relatórios no Azure AD (Azure Active Directory) consiste nos seguintes componentes:

- **Atividade** 
    - **Entradas** – Informações sobre o uso de aplicativos gerenciados e atividades de entrada do usuário.
    - **Logs de Auditoria** - [Logs de Auditoria](concept-audit-logs.md) fornecer informações de atividades do sistema sobre usuários e gerenciamento de grupos, aplicativos gerenciados e atividades de diretório.
- **Segurança** 
    - **Entradas arriscadas** - Uma [entrada arriscada](concept-risky-sign-ins.md) é um indicador para uma tentativa de entrada que pode ter sido realizada por alguém que não é o proprietário legítimo de uma conta de usuário.
    - **Usuários sinalizados para risco** - Um [usuário arriscado](concept-user-at-risk.md) é um indicador de uma conta de usuário que pode ter sido comprometida.

Este tópico fornece uma visão geral das atividades de entradas.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="who-can-access-the-data"></a>Quem pode acessar os dados?
* Usuários nas funções Administrador de Segurança, Leitor de Segurança e Leitor de Relatório
* Administradores globais
* Além disso, qualquer usuário (não administrador) pode acessar suas próprias entradas 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Qual licença do Azure AD você precisa para acessar a atividade de entrada?
* O locatário deve ter uma licença do Azure AD Premium associada a ele para ver todo o relatório de atividade de entrada. Consulte [Introdução ao Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) para fazer upgrade da edição do Azure Active Directory. Observe que, se você não tiver dados das atividades antes da atualização, eles demorarão alguns dias para ser exibidos nos relatórios depois de atualizar para uma licença premium.

## <a name="sign-ins-report"></a>Relatório de entradas

O relatório de entradas de usuário fornece respostas para as seguintes perguntas:

* O que é o padrão de entrada de um usuário?
* Quantos usuários entraram em uma semana?
* Qual é o status dessas entradas?

É possível acessar o relatório de entradas, selecionando **Entradas** na seção **Atividade** da folha **Azure Active Directory** no [portal do Azure](https://portal.azure.com). Observe que alguns registros de entrada podem levar até duas horas para ser exibidos no portal.

![Atividade de entrada](./media/concept-sign-ins/61.png "Atividade de entrada")

> [!IMPORTANT]
> O relatório de entradas exibe apenas os **logins** interativos, isto é, os logins nos quais um usuário faz login manualmente usando seu nome de usuário e senha. Os logins não interativos, como a autenticação de serviço a serviço, não são exibidos no relatório de logins. 

Um log de entradas tem um modo de exibição de lista padrão que mostra:

- A hora de entrada
- O usuário relacionado
- O aplicativo no qual o usuário entrou
- O status de entrada
- O status da detecção de riscos
- O status do requisito de MFA (autenticação multifator)

![Atividade de entrada](./media/concept-sign-ins/01.png "Atividade de entrada")

Você pode personalizar o modo de exibição de lista clicando em **Colunas** na barra de ferramentas.

![Atividade de entrada](./media/concept-sign-ins/19.png "Atividade de entrada")

Isso permite a você exibir campos adicionais ou remover campos que já estão exibidos.

![Atividade de entrada](./media/concept-sign-ins/02.png "Atividade de entrada")

Selecione um item na exibição de lista para obter informações mais detalhadas.

![Atividade de entrada](./media/concept-sign-ins/03.png "Atividade de entrada")

> [!NOTE]
> Os clientes agora podem solucionar problemas de políticas de acesso condicional por meio de todos os relatórios de login. Ao clicar na guia **Acesso condicional** para um registro de entrada, os clientes podem rever o status de acesso condicional e analisar os detalhes das políticas aplicadas ao login e o resultado de cada política.
> Para obter mais informações, consulte as [Perguntas frequentes sobre as informações de CA em todos os logins](reports-faq.md#conditional-access).

![Atividade de entrada](./media/concept-sign-ins/ConditionalAccess.png "Atividade de entrada")


## <a name="filter-sign-in-activities"></a>Filtrar atividades de entrada

Para restringir os dados relatados a um nível que funciona para você, filtre os dados de entradas usando os seguintes campos padrão:

- Usuário
- Aplicativo
- Status de entrada
- Acesso Condicional
- Data

![Atividade de entrada](./media/concept-sign-ins/04.png "Atividade de entrada")

O filtro **Usuário** permite que você especifique o nome ou o UPN (nome UPN) do usuário desejado.

O filtro **Aplicativo** permite que você especifique o nome do aplicativo desejado.

O filtro **status de entrada** permite que você selecione:

- Todos
- Sucesso
- Failure

O filtro **Acesso Condicional** permite que você selecione o status da política de Autoridade de Certificação para a entrada:

- Todos
- Não aplicado
- Sucesso
- Failure

O filtro **Data** permite definir um período de tempo para os dados retornados.  
Os valores possíveis são:

- 1 mês
- 7 dias
- 24 horas
- Intervalo de tempo personalizado

Quando você seleciona um período de tempo personalizado, pode configurar uma hora de início e uma hora de término.

Se você adicionar outros campos ao modo de exibição de entradas, esses campos serão adicionados automaticamente à lista de filtros. Por exemplo, ao adicionar o campo **Aplicativo Cliente** à sua lista, você também obtém outra opção de filtro que permite definir os seguintes filtros:

- Navegador      
- Exchange ActiveSync (com suporte)               
- Exchange ActiveSync (sem suporte)
- Outros clientes               
    - IMAP
    - MAPI
    - Clientes mais antigos do Office
    - POP
    - SMTP


![Atividade de entrada](./media/concept-sign-ins/12.png "Atividade de entrada")


## <a name="download-sign-in-activities"></a>Baixar atividades de entrada

Você poderá [baixar os dados de entradas](quickstart-download-sign-in-report.md), caso queira trabalhar com esses dados fora do portal do Azure. Clicando em **baixar** lhe dá a opção de criar um arquivo CSV ou JSCON dos registros de 250.000 mais recentes.  

![Baixar](./media/concept-sign-ins/71.png "Baixar")

> [!IMPORTANT]
> O número de registros que podem ser baixados é restringido pelas [políticas de retenção de relatórios do Azure Active Directory](reference-reports-data-retention.md).  


## <a name="sign-ins-data-shortcuts"></a>Atalhos dos dados de entradas

Além do Azure AD, o portal do Azure fornece pontos de entrada adicionais aos dados de entradas:

- Visão geral da proteção de segurança de identidade
- Usuários
- Grupos
- Aplicativos empresariais

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Dados de entradas de usuário na proteção de segurança de identidade

O grafo de entrada de usuário na página de visão geral da **proteção de segurança de identidade** mostra agregações semanais de entradas de todos os usuários em um determinado período de tempo. O padrão para o período é de 30 dias.

![Atividade de entrada](./media/concept-sign-ins/06.png "Atividade de entrada")

Quando você clica em um dia no gráfico de entradas, obtém uma lista detalhada das atividades de entrada do dia.

Cada linha na lista de atividades de entrada mostra:

* Quem entrou?
* Qual aplicativo era o destino da entrada?
* Qual é o status da entrada?
* Qual é o status de MFA da entrada?

Ao clicar em um item, você verá mais detalhes sobre a operação de entrada:

- Id de Usuário
- Usuário
- Nome de Usuário
- ID do aplicativo
- Aplicativo
- Cliente
- Local padrão
- Endereço IP
- Data
- MFA obrigatório
- Status de entrada

> [!NOTE]
> Os endereços IP são emitidos de uma forma que não há nenhuma conexão definitiva entre um endereço IP e em que o computador com esse endereço está localizado fisicamente. O mapeamento de endereços IP é dificultado pelo fato de que os provedores móveis e VPNs emitem endereços IP de pools centrais que geralmente estão muito longe de onde o dispositivo cliente realmente é usado. Atualmente, nos relatórios do Azure AD, a conversão do endereço IP em um local físico é um melhor esforço com base em rastreamentos, dados de registro, pesquisas inversas e outras informações.

Na página **Usuários**, você obtém uma visão geral completa de todas as entradas do usuário clicando em **Entradas** na seção **Atividade**.

![Atividade de entrada](./media/concept-sign-ins/08.png "Atividade de entrada")

## <a name="usage-of-managed-applications"></a>Uso de aplicativos gerenciados

Com uma exibição centrada no aplicativo de seus dados de entrada, você pode responder a perguntas como:

* Quem está usando meus aplicativos?
* Quais são os três principais aplicativos em sua organização?
* Recentemente, eu implantei um aplicativo. Como ele está se saindo?

Seu ponto de entrada para esses dados é composto pelos três principais aplicativos em sua organização no relatório dos 30 últimos dias, presente na seção **Visão geral**, em **Aplicativos empresariais**.

![Atividade de entrada](./media/concept-sign-ins/10.png "Atividade de entrada")

As agregações semanais ao grafo de uso do aplicativo de entradas para seus três principais aplicativos em um determinado período de tempo. O padrão para o período é de 30 dias.

![Atividade de entrada](./media/concept-sign-ins/47.png "Atividade de entrada")

Se desejar, você pode definir o foco em um aplicativo específico.

![Relatórios](./media/concept-sign-ins/single_spp_usage_graph.png "Relatórios")

Quando você clica em um dia no grafo de uso do aplicativo, pode obter uma lista detalhada das atividades de entrada.

A opção **Entradas** oferece uma visão geral completa de todos os eventos de entrada para seus aplicativos.

![Atividade de entrada](./media/concept-sign-ins/11.png "Atividade de entrada")

## <a name="office-365-activity-logs"></a>Logs de atividade do Office 365

É possível exibir logs de atividades do Office 365 no [Centro de Administração do Office 365](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Embora os logs de atividades do Office 365 e do Azure AD compartilhem muitos dos recursos de diretório, somente o Centro de Administração do Office 365 oferece uma exibição completa dos logs de atividades do Office 365. 

Você também pode acessar os logs de atividade do Office 365 de modo programático usando as [APIs de gerenciamento do Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Próximas etapas

* [Códigos de erro do relatório de atividade de entrada](reference-sign-ins-error-codes.md)
* [Políticas de retenção de dados do Azure AD](reference-reports-data-retention.md)
* [Latências do relatório do Azure AD](reference-reports-latencies.md)

