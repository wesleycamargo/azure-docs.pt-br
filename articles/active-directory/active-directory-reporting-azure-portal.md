---

title: "Relatórios do Azure Active Directory | Microsoft Docs"
description: "Lista os diversos relatórios disponíveis no Azure Active Directory"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 6141a333-38db-478a-927e-526f1e7614f4
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/06/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: c7fe995f097c72ab5275249538fe2bb65efac256
ms.contentlocale: pt-br
ms.lasthandoff: 05/09/2017


---
# <a name="azure-active-directory-reporting"></a>Relatórios do Azure Active Directory


*Esta documentação é parte do [Guia de Relatórios do Azure Active Directory](active-directory-reporting-guide.md).*

Com os relatórios no Azure Active Directory (Azure AD) você obtém todas as informações de que precisa para determinar como seu ambiente está se comportando.

Há duas áreas principais de relatórios:

* **Atividades de entrada** – informações sobre o uso de aplicativos gerenciados e de atividades de entrada do usuário
* **Logs de auditoria** - informações de auditoria sobre o gerenciamento de usuários e de grupos, os aplicativos gerenciados e as atividades de diretório

Dependendo do escopo dos dados que você estiver procurando, será possível acessar esses relatórios clicando em **Usuários e grupos** ou em **Aplicativos empresariais** na lista de serviços no [Portal do Azure](https://portal.azure.com).

## <a name="sign-in-activities"></a>Atividades de entrada
### <a name="user-sign-in-activities"></a>Atividades de entrada do usuário
Com as informações fornecidas pelo relatório de entrada de usuário, você encontra respostas para perguntas como:

* O que é o padrão de entrada de um usuário?
* Quantos usuários entraram em uma semana?
* Qual é o status dessas entradas?

O ponto de entrada para esses dados é o gráfico de entrada do usuário na seção **Visão geral** em **Usuários e grupos**.

 ![Relatórios](./media/active-directory-reporting-azure-portal/05.png "Relatórios")

O gráfico de entrada do usuário mostra agregações semanais de entradas para todos os usuários em um determinado período de tempo. O padrão para o período é de 30 dias.

![Relatórios](./media/active-directory-reporting-azure-portal/02.png "Relatórios")

Quando você clica em um dia no gráfico de entradas, obtém uma lista detalhada das atividades de entrada.

![Relatórios](./media/active-directory-reporting-azure-portal/03.png "Relatórios")

Cada linha na lista de atividades de entrada oferece as informações detalhadas sobre a entrada selecionada, como:

* Quem entrou?
* Qual era o UPN relacionado?
* Qual aplicativo era o destino da entrada?
* Qual é o endereço IP da entrada?
* Qual era o status da entrada?

### <a name="usage-of-managed-applications"></a>Uso de aplicativos gerenciados
Com uma exibição centrada no aplicativo de seus dados de entrada, você pode responder a perguntas como:

* Quem está usando meus aplicativos?
* Quais são os três principais aplicativos em sua organização?
* Recentemente, eu implantei um aplicativo. Como ele está se saindo?

Seu ponto de entrada para esses dados é composto pelos três principais aplicativos em sua organização no relatório dos 30 últimos dias, presente na seção **Visão geral**, em **Aplicativos empresariais**.

 ![Relatórios](./media/active-directory-reporting-azure-portal/06.png "Relatórios")

As agregações semanais ao gráfico de uso do aplicativo de entradas para seus três principais aplicativos em um determinado período de tempo. O padrão para o período é de 30 dias.

![Relatórios](./media/active-directory-reporting-azure-portal/78.png "Relatórios")

Se desejar, você pode definir o foco em um aplicativo específico.

![Relatórios](./media/active-directory-reporting-azure-portal/single_spp_usage_graph.png "Relatórios")

Quando você clica em um dia no gráfico de uso do aplicativo, pode obter uma lista detalhada das atividades de entrada.

![Relatórios](./media/active-directory-reporting-azure-portal/top_app_sign_ins.png "Relatórios")

A opção **Entradas** oferece uma visão geral completa de todos os eventos de entrada para seus aplicativos.

![Relatórios](./media/active-directory-reporting-azure-portal/85.png "Relatórios")

Usando o seletor de coluna, você pode selecionar os campos de dados que deseja exibir.

![Relatórios](./media/active-directory-reporting-azure-portal/column_chooser.png "Relatórios")

### <a name="filtering-sign-ins"></a>Filtragem de entradas
Você pode filtrar entradas para limitar a quantidade de dados exibidos, usando os campos a seguir:

* Data e hora 
* Nome UPN do usuário
* Nome do aplicativo
* Nome do cliente
* Status de entrada

![Relatórios](./media/active-directory-reporting-azure-portal/293.png "Relatórios")

Outro método para filtrar as entradas das atividades de entrada é procurar itens específicos.
O método search permite que você defina o escopo de suas entradas em torno de **usuários**, **grupos** ou **aplicativos** específicos.

![Relatórios](./media/active-directory-reporting-azure-portal/84.png "Relatórios")

## <a name="audit-logs"></a>Logs de auditoria
Os logs de auditoria no Azure Active Directory fornecem registros de atividades do sistema para fins de conformidade.

Há três categorias principais de auditoria de atividades relacionadas no portal do Azure:

* Usuários e grupos   
* aplicativos
* Diretório   

Para obter uma lista completa de atividades de relatório de auditoria, consulte o [lista de eventos de relatório de auditoria](active-directory-reporting-audit-events.md#list-of-audit-report-events).

O ponto de entrada para todos os dados de auditoria é **Logs de auditoria**, na seção **Atividade** do **Azure Active Directory**.

![Auditoria](./media/active-directory-reporting-azure-portal/61.png "Auditoria")

Um log de auditoria tem um modo de exibição de lista que mostra os atores (quem), as atividades (o que) e os destinos.

![Auditoria](./media/active-directory-reporting-azure-portal/345.png "Auditoria")

Ao clicar em um item na exibição de lista, você poderá obter mais detalhes sobre ele.

![Auditoria](./media/active-directory-reporting-azure-portal/873.png "Auditoria")

### <a name="users-and-groups-audit-logs"></a>Logs de auditoria de usuários e grupos
Com relatórios de auditoria baseados em grupos e usuários, você pode obter respostas a perguntas como:

* Que tipos de atualizações foram aplicadas os usuários?
* Quantos usuários foram alterados?
* Quantas senhas foram alteradas?
* O que um administrador fez em um diretório?
* Quais são os grupos que foram adicionados?
* Existem grupos com alterações de associação?
* Os proprietários do grupo foram alterados?
* Quais licenças foram atribuídas a um grupo ou a um usuário?

Se você quiser examinar os dados de auditoria relacionados aos usuários e aos grupos, poderá encontrar uma exibição filtrada em **Logs de auditoria** na seção **Atividade** de **Usuários e Grupos**.

![Auditoria](./media/active-directory-reporting-azure-portal/93.png "Auditoria")

### <a name="application-audit-logs"></a>Logs de auditoria de aplicativo
Com relatórios de auditoria baseados em aplicativos, você pode obter respostas a perguntas como:

* Quais aplicativos foram adicionados ou atualizados?
* Quais aplicativos foram removidos?
* Um princípio de serviço para um aplicativo foi alterado?
* Os nomes de aplicativos foram alterados?
* Quem deu permissão a um aplicativo?

Se você quiser examinar os dados de auditoria relacionados aos aplicativos, poderá encontrar uma exibição filtrada em **Logs de auditoria** na seção **Atividade** de **Aplicativos empresariais**.

![Auditoria](./media/active-directory-reporting-azure-portal/134.png "Auditoria")

### <a name="filtering-audit-logs"></a>Filtragem de logs de auditoria
Você pode filtrar entradas para limitar a quantidade de dados exibidos, usando os campos a seguir:

* Data e hora
* Nome UPN do ator
* Tipo de atividade
* Atividade

![Auditoria](./media/active-directory-reporting-azure-portal/356.png "Auditoria")

O conteúdo da lista **tipo de atividade** está vinculado ao seu ponto de entrada para esta folha.  
Se o ponto de entrada for o Azure Active Directory, essa lista conterá todos os tipos de atividade possíveis:

* Aplicativo 
* Agrupar 
* Usuário
* Dispositivo
* Diretório
* Política
* Outros

![Auditoria](./media/active-directory-reporting-azure-portal/825.png "Auditoria")

As atividades listadas são delimitadas por tipo de atividade.
Por exemplo, se você tiver **Grupo** selecionado como **Tipo de Atividade**, a lista **Atividade** conterá apenas o grupo de atividades relacionadas.   

![Auditoria](./media/active-directory-reporting-azure-portal/654.png "Auditoria")

Outro método para filtrar as entradas de um log de auditoria é procurar itens específicos.

![Auditoria](./media/active-directory-reporting-azure-portal/237.png "Auditoria")

## <a name="next-steps"></a>Próximas etapas
Veja o [Guia de relatórios do Azure Active Directory](active-directory-reporting-guide.md).


