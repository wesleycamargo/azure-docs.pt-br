---
title: O que são os relatórios do Azure Active Directory? | Microsoft Docs
description: Uma visão geral dos relatórios do Azure Active Directory.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 6141a333-38db-478a-927e-526f1e7614f4
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 01/15/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 220f27ccf2d3eaefd8347e1d52824be2d601d9c9
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46364362"
---
# <a name="what-are-azure-active-directory-reports"></a>O que são os relatórios do Azure Active Directory?

Com os relatórios do Azure Active Directory, é possível obter insights sobre como o ambiente está funcionando.  
Os dados fornecidos permitem a você:

- Determinar como os aplicativos e serviços estão sendo utilizados pelos usuários
- Detectar possíveis riscos que afetem a integridade do seu ambiente
- Solucionar problemas que impedem a conclusão dos trabalhos pelos usuários  

A arquitetura de relatório se baseia em dois pilares principais:

- Relatórios de segurança
- Relatórios de atividades

![Relatórios](./media/overview-reports/01.png)


## <a name="security-reports"></a>Relatórios de segurança

Os relatórios de segurança no Azure Active Directory o ajudam a proteger as identidades da organização.  
Há dois tipos de relatórios de segurança no Azure Active Directory:

- **Usuários sinalizados como risco**: no [relatório de usuários sinalizados como risco de segurança](concept-user-at-risk.md), obtenha uma visão geral das contas de usuário que podem ter sido comprometidas.

- **Entradas de risco**: com o [relatório de entradas de risco](concept-risky-sign-ins.md), você tem um indicador de tentativas de logon que pode ter sido realizadas por alguém que não é o proprietário legítimo de uma conta de usuário. 

**Qual licença do Azure AD você precisa para acessar a atividade de entrada?**  

Todas as edições do Azure Active Directory fornecem relatórios de usuários sinalizados como risco e de entradas de risco.  
No entanto, o nível de granularidade do relatório varia entre as edições: 

- Nas **edições do Azure Active Directory Gratuita e Basic**, você obtém uma lista de usuários sinalizados como risco e de entradas de risco. 

- A edição do **Azure Active Directory Premium 1** estende esse modelo, também permitindo que você examine alguns dos eventos de risco subjacentes que foram detectados para cada relatório. 

- A edição do **Azure Active Directory Premium 2** fornece as informações mais detalhadas sobre os eventos de risco subjacentes e também permite configurar políticas de segurança que atendem automaticamente a níveis de risco configurados.


## <a name="activity-reports"></a>Relatórios de atividades

Há dois tipos de relatórios de atividade no Azure Active Directory:

- **Trilhas de auditoria**: o [relatório de atividade das trilhas de auditoria](concept-audit-logs.md) fornece acesso ao histórico de todas as tarefas executadas em seu locatário.

- **Entradas**: com o [relatório de atividades de entradas](concept-sign-ins.md), você pode determinar quem realizou as tarefas indicadas pelo relatório das trilhas de auditoria.


O **relatório das trilhas de auditoria** fornece registros de atividades de sistema para fins de conformidade. Esses dados permitem abordar cenários comuns, como:

- Alguém em meu locatário tem acesso a um grupo de administração. Quem deu o acesso? 

- Quero saber a lista de usuários que estão entrando em um aplicativo específico, já que integrei o aplicativo recentemente e desejo saber se ele está tendo boa recepção

- Quero saber quantas redefinições de senha estão ocorrendo em meu locatário


**Qual é a licença do Azure AD necessária para acessar o relatório das trilhas de auditoria?**  

O relatório das trilhas de auditoria está disponível para os recursos para os quais você possui licenças. Se você tem uma licença para um recurso específico, também tem acesso às informações da trilha de auditoria dele.

Para obter mais detalhes, confira **Comparando recursos geralmente disponíveis das edições Gratuita, Basic e Premium** em [Recursos do Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features).   


O **relatório de atividades de logins** permite encontrar respostas para perguntas como:

- O que é o padrão de entrada de um usuário?
- Quantos usuários entraram em uma semana?
- Qual é o status dessas entradas?


**Qual licença do Azure AD você precisa para acessar o relatório de atividades de entrada?**  

Para acessar o relatório de atividades de entrada, seu locatário deve ter uma licença do Azure AD Premium associada a ele.

## <a name="programmatic-access"></a>Acesso Programático

Além da interface do usuário, os relatórios do Azure Active Directory também fornecem a você [acesso programático](concept-reporting-api.md) aos dados de relatórios. Os dados desses relatórios podem ser muito úteis para seus aplicativos, como sistemas SIEM, auditoria e ferramentas de business intelligence. As APIs de relatório do Azure AD fornecem acesso programático aos dados através de um conjunto de APIs baseadas em REST. Você pode chamar essas APIs de várias ferramentas e linguagens de programação. 


## <a name="next-steps"></a>Próximas etapas

- [Relatório de entradas de risco](concept-risky-sign-ins.md)
- [Relatório de trilhas de auditoria](concept-audit-logs.md)
- [Relatório de logs de entrada](concept-sign-ins.md)