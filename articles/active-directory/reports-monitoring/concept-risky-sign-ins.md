---
title: Relatório de entradas de risco no portal do Azure Active Directory | Microsoft Docs
description: Saiba mais sobre o relatório de entradas de risco no portal do Azure Active Directory
services: active-directory
author: priyamohanram
manager: mtillman
ms.assetid: 7728fcd7-3dd5-4b99-a0e4-949c69788c0f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/14/2017
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 38ae18dca08b50a90102149d7e44169c956a1c0e
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48869628"
---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal"></a>Relatório de entradas de risco no portal do Azure Active Directory

Com os relatórios de segurança no Azure Active Directory (Azure AD) você pode obter informações sobre a probabilidade de contas de usuário comprometidas em seu ambiente. 

O Azure AD detecta ações suspeitas relacionadas às suas contas de usuário. Para cada ação detectada, um registro chamado *evento de risco* é criado. Para obter mais detalhes, veja [Eventos de risco do Azure Active Directory](concept-risk-events.md). 

Os eventos de risco detectados são usados para calcular:

- **Entradas arriscadas** - uma entrada arriscada é um indicador para uma tentativa de logon que pode ter sido realizada por alguém que não é o proprietário legítimo de uma conta de usuário. Para obter mais detalhes, confira [Como configurar a política de risco de entrada](../identity-protection/howto-sign-in-risk-policy.md). 

- **Usuários sinalizados para riscos** - um usuário arriscado é um indicador de uma conta de usuário que pode ter sido comprometida. Para obter mais detalhes, confira [Como configurar a política de risco](../identity-protection/howto-user-risk-policy.md).  

Você pode encontrar os relatórios de segurança no [Portal do Azure](https://portal.azure.com) na folha **Azure Active Directory** na seção **Segurança**. 

![Entradas de risco](./media/concept-risky-sign-ins/10.png)

## <a name="who-can-access-the-risky-sign-ins-report"></a>Quem pode acessar o relatório de entradas de risco?

Os relatórios de entradas de risco estão disponíveis para usuários nas funções a seguir:

- Administrador de segurança
- Administrador global
- Leitor de segurança

Para saber como atribuir funções administrativas a um usuário no Azure Active Directory, veja [Exibir e atribuir funções de administrador no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-manage-roles-portal).

## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Qual licença do Azure AD você precisa para acessar a atividade de entrada?  

Todas as edições do Azure Active Directory fornecem relatórios de entradas de risco.  
No entanto, o nível de granularidade do relatório varia entre as edições: 

- Nas **edições do Azure Active Directory Gratuita e Basic**, você obtém uma lista de entradas de risco. 

- A edição do **Azure Active Directory Premium 1** estende esse modelo, também permitindo que você examine alguns dos eventos de risco subjacentes que foram detectados para cada relatório. 

- A edição do **Azure Active Directory Premium 2** fornece as informações mais detalhadas sobre todos os eventos de risco subjacentes e também permite configurar políticas de segurança que atendem automaticamente aos níveis de risco configurados.

## <a name="azure-active-directory-free-and-basic-edition"></a>Edições gratuita e básica do Azure Active Directory

As edições gratuita e básica do Azure Active Directory fornecem uma lista de entradas de risco que foram detectadas dos seus usuários. O relatório lista:

- **Usuário** - o nome do usuário usado durante a operação de entrada
- **IP** - o endereço IP do dispositivo usado para se conectar ao Azure Active Directory
- **Local** - o local usado para se conectar ao Azure Active Directory
- **Hora da entrada** - o horário em que a entrada foi realizada
- **Status** - o status da entrada


![Entradas de risco](./media/concept-risky-sign-ins/01.png)

Com base em sua investigação da entrada arriscada, você poderá fazer comentários ao Azure Active Directory na forma das seguintes ações:

- Resolver
- Marcar como falso positivo
- Ignorar
- Reativar

![Entradas de risco](./media/concept-risky-sign-ins/21.png)



Esse relatório fornece uma opção para:

- Recursos do Search
- Baixar os dados do relatório


![Entradas de risco](./media/concept-risky-sign-ins/93.png)


## <a name="azure-active-directory-premium-editions"></a>Edições premium do Azure Active Directory

O relatório de entradas de risco nas edições premium do Azure Active Directory oferece:

- Informações agregadas sobre os [tipos de eventos de risco](concept-risk-events.md) que foram detectados

- Uma opção para baixar o relatório


![Entradas de risco](./media/concept-risky-sign-ins/456.png)


Ao selecionar um evento de risco, você obtém uma exibição detalhada do relatório deste evento de risco que habilita:

- Uma opção para configurar uma [política de correção de risco de usuário](../identity-protection/howto-user-risk-policy.md)  

- O exame do cronograma de detecção do evento de risco  

- O exame de uma lista de usuários para os quais esse evento de risco foi detectado

- Fechar eventos de risco manualmente. 


![Entradas de risco](./media/concept-risky-sign-ins/457.png)

Ao selecionar um usuário, você obtém uma exibição detalhada do relatório deste usuário, que lhe habilita a:

- Abrir a exibição Todas as entradas

- Redefinir a senha do usuário

- Descartar todos os eventos

- Investigar os eventos de risco relatados para o usuário. 


![Entradas de risco](./media/concept-risky-sign-ins/324.png)


Para investigar um evento de risco, selecione um na lista.  
Isso abre a folha de **Detalhes** para este evento de risco. Na folha **Detalhes**, existe a opção de fechar manualmente um evento de risco ou reativar um evento de risco fechado manualmente. 


![Entradas de risco](./media/concept-risky-sign-ins/325.png)





## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre o Azure Active Directory Identity Protection, veja [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

