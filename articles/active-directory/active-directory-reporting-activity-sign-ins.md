---
title: "Relatório de atividades de entrada no portal do Azure Active Directory – versão prévia | Microsoft Docs"
description: "Introdução aos relatórios de atividades de entrada no portal do Azure Active Directory – versão prévia"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 78617dc7e3a4b6eb4fc32d32b6850b8c0832d6d8
ms.openlocfilehash: 9819c5f6a3aea53664d86e3a23b25946c0f2b731
ms.lasthandoff: 02/22/2017


---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal---preview"></a>Relatórios de atividades de entrada no portal do Azure Active Directory – versão prévia

Com o relatório na [versão prévia](active-directory-preview-explainer.md) do Azure Active Directory, você obtém todas as informações de que precisa para determinar como seu ambiente está se comportando.

A arquitetura de relatório no Azure Active Directory consiste nos seguintes componentes:

- **Atividade** 
    - **Atividades de entrada** – informações sobre o uso de aplicativos gerenciados e de atividades de entrada do usuário
    - **Logs de auditoria** – informações de atividade do sistema sobre o gerenciamento de usuários e de grupos, sobre os aplicativos gerenciados e sobre as atividades de diretório.
- **Segurança** 
    - **Entradas arriscadas** - uma entrada arriscada é um indicador para uma tentativa de logon que pode ter sido realizada por alguém que não é o proprietário legítimo de uma conta de usuário. Para obter mais detalhes, veja Entradas de risco.
    - **Usuários sinalizados para riscos** - um usuário arriscado é um indicador de uma conta de usuário que pode ter sido comprometida. Para obter mais detalhes, consulte Usuários sinalizados para risco.

Este tópico fornece uma visão geral das atividades de entrada.

## <a name="signs-in-activities"></a>Atividades de entrada

Com as informações fornecidas pelo relatório de entrada de usuário, você encontra respostas para perguntas como:

* O que é o padrão de entrada de um usuário?
* Quantos usuários entraram em uma semana?
* Qual é o status dessas entradas?

O ponto de entrada para esses dados é o gráfico de entrada do usuário na seção **Visão geral** em **Usuários e grupos**.

 ![Relatórios](./media/active-directory-reporting-activity-sign-ins/05.png "Relatórios")

O gráfico de entrada do usuário mostra agregações semanais de entradas para todos os usuários em um determinado período de tempo. O padrão para o período é de 30 dias.

![Relatórios](./media/active-directory-reporting-activity-sign-ins/02.png "Relatórios")

Quando você clica em um dia no gráfico de entradas, obtém uma lista detalhada das atividades de entrada.

![Relatórios](./media/active-directory-reporting-activity-sign-ins/03.png "Relatórios")

Cada linha na lista de atividades de entrada oferece as informações detalhadas sobre a entrada selecionada, como:

* Quem entrou?
* Qual era o UPN relacionado?
* Qual aplicativo era o destino da entrada?
* Qual é o endereço IP da entrada?
* Qual era o status da entrada?

## <a name="usage-of-managed-applications"></a>Uso de aplicativos gerenciados

Com uma exibição centrada no aplicativo de seus dados de entrada, você pode responder a perguntas como:

* Quem está usando meus aplicativos?
* Quais são os três principais aplicativos em sua organização?
* Recentemente, eu implantei um aplicativo. Como ele está se saindo?

Seu ponto de entrada para esses dados é composto pelos três principais aplicativos em sua organização no relatório dos 30 últimos dias, presente na seção **Visão geral**, em **Aplicativos empresariais**.

 ![Relatórios](./media/active-directory-reporting-activity-sign-ins/06.png "Relatórios")

As agregações semanais ao gráfico de uso do aplicativo de entradas para seus três principais aplicativos em um determinado período de tempo. O padrão para o período é de 30 dias.

![Relatórios](./media/active-directory-reporting-activity-sign-ins/78.png "Relatórios")

Se desejar, você pode definir o foco em um aplicativo específico.

![Relatórios](./media/active-directory-reporting-activity-sign-ins/single_spp_usage_graph.png "Relatórios")

Quando você clica em um dia no gráfico de uso do aplicativo, pode obter uma lista detalhada das atividades de entrada.

![Relatórios](./media/active-directory-reporting-activity-sign-ins/top_app_sign_ins.png "Relatórios")

A opção **Entradas** oferece uma visão geral completa de todos os eventos de entrada para seus aplicativos.

![Relatórios](./media/active-directory-reporting-activity-sign-ins/85.png "Relatórios")

Usando o seletor de coluna, você pode selecionar os campos de dados que deseja exibir.

![Relatórios](./media/active-directory-reporting-activity-sign-ins/column_chooser.png "Relatórios")

## <a name="filtering-sign-ins"></a>Filtragem de entradas
Você pode filtrar entradas para limitar a quantidade de dados exibidos, usando os campos a seguir:

* Data e hora 
* Nome UPN do usuário
* Nome do aplicativo
* Nome do cliente
* Status de entrada

![Relatórios](./media/active-directory-reporting-activity-sign-ins/293.png "Relatórios")

Outro método para filtrar as entradas das atividades de entrada é procurar itens específicos.
O método search permite que você defina o escopo de suas entradas em torno de **usuários**, **grupos** ou **aplicativos** específicos.

![Relatórios](./media/active-directory-reporting-activity-sign-ins/84.png "Relatórios")


## <a name="next-steps"></a>Próximas etapas
Veja o [Guia de relatórios do Azure Active Directory](active-directory-reporting-guide.md).


