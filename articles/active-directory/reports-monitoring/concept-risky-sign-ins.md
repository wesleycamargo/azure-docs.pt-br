---
title: Relatório de entradas de risco no portal do Azure Active Directory | Microsoft Docs
description: Saiba mais sobre o relatório de entradas de risco no portal do Azure Active Directory
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 7728fcd7-3dd5-4b99-a0e4-949c69788c0f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40e125f8e1e7909c5866a03c0571f49ec42d690a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60287337"
---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal"></a>Relatório de entradas de risco no portal do Azure Active Directory

O Azure AD (Azure Active Directory) detecta ações suspeitas relacionadas às contas de usuário. Para cada ação detectada, um registro chamado **evento de risco** é criado. Para obter mais detalhes, consulte [Eventos de risco do Azure AD](concept-risk-events.md). 

Você pode acessar os relatórios de segurança do [portal do Azure](https://portal.azure.com) selecionando a folha **Azure Active Directory** e, em seguida, navegando até a seção **Segurança**. 

Há dois relatórios de segurança diferentes que são calculados com base nos eventos de risco:

- **Entradas arriscadas** - uma entrada arriscada é um indicador para uma tentativa de logon que pode ter sido realizada por alguém que não é o proprietário legítimo de uma conta de usuário.

- **Usuários sinalizados para riscos** - um usuário arriscado é um indicador de uma conta de usuário que pode ter sido comprometida. 

![Entradas de risco](./media/concept-risky-sign-ins/10.png)

Para saber como configurar as políticas que disparam esses eventos de risco, consulte [Como configurar a política de risco do usuário](../identity-protection/howto-user-risk-policy.md).  

## <a name="who-can-access-the-risky-sign-ins-report"></a>Quem pode acessar o relatório de entradas de risco?

Os relatórios de entradas de risco estão disponíveis para usuários nas funções a seguir:

- Administrador de segurança
- Administrador global
- Leitor de segurança

Para saber como atribuir funções administrativas a um usuário no Azure Active Directory, veja [Exibir e atribuir funções de administrador no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-manage-roles-portal).

## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Qual licença do Azure AD você precisa para acessar a atividade de entrada?  

Todas as edições do Azure AD fornecem relatórios de entradas de risco. No entanto, o nível de granularidade do relatório varia entre as edições: 

- Nas **edições Gratuita e Básica do Azure Active**, você recebe uma lista de entradas de risco. 

- Além disso, a edição do **Azure Active Directory Premium 1** permite que você examine alguns dos eventos de risco subjacentes que foram detectados para cada relatório. 

- A edição do **Azure Active Directory Premium 2** fornece as informações mais detalhadas sobre todos os eventos de risco subjacentes e também permite configurar políticas de segurança que atendem automaticamente aos níveis de risco configurados.

## <a name="risky-sign-ins-report-for-azure-ad-free-and-basic-edition"></a>Relatório de entradas arriscadas da edição gratuita e básica do Azure AD

As edições gratuitas e básicas do Azure AD fornecem uma lista de entradas arriscadas que foram detectadas para os usuários. Cada registro contém os seguintes atributos:

- **Usuário**: o nome de usuário usado durante a operação de entrada.
- **IP**: o endereço IP do dispositivo usado para se conectar ao Azure Active Directory.
- **Local**: o local usado para se conectar ao Azure Active Directory. Trata-se do melhor esforço de aproximação com base em rastreamentos, dados de registro, pesquisas inversas e outras informações.
- **Hora da entrada** - o horário em que a entrada foi realizada
- **Status** - o status da entrada

![Entradas de risco](./media/concept-risky-sign-ins/01.png)

Com base na investigação de entrada arriscada, você poderá fornecer feedback ao Azure AD realizando as seguintes ações:

- Resolver
- Marcar como falso positivo
- Ignorar
- Reativar

![Entradas de risco](./media/concept-risky-sign-ins/21.png)

Este relatório também fornece uma opção para:

- Recursos do Search
- Baixar os dados do relatório

![Entradas de risco](./media/concept-risky-sign-ins/93.png)

## <a name="risky-sign-ins-report-for-azure-ad-premium-editions"></a>Relatório de entradas arriscadas para as edições Premium do Azure AD

O relatório de entradas arriscadas nas edições Premium do Azure AD fornece:

- Informações agregadas sobre os [tipos de eventos de risco](concept-risk-events.md) que foram detectados. Com o **Azure AD Premium P1 Edition**, as detecções que não são cobertas por sua licença aparecem como o evento de risco **Entrada com risco adicional detectado**. Com o **Azure AD Premium P2 Edition**, você obtém as informações mais detalhadas sobre todas as detecções subjacentes.

- Uma opção para baixar o relatório

![Entradas de risco](./media/concept-risky-sign-ins/456.png)

Ao selecionar um evento de risco, você obtém uma exibição detalhada do relatório deste evento de risco que habilita:

- Uma opção para configurar uma [política de correção de risco de usuário](../identity-protection/howto-user-risk-policy.md)  

- O exame do cronograma de detecção do evento de risco  

- O exame de uma lista de usuários para os quais esse evento de risco foi detectado

- Fechar eventos de risco manualmente. 

![Entradas de risco](./media/concept-risky-sign-ins/457.png)

> [!IMPORTANT]
> Às vezes, você pode encontrar um evento de risco sem uma entrada correspondente no [relatório de entradas](concept-sign-ins.md). Isso ocorre porque o Identity Protection avalia o risco para as entradas **interativa** e **não interativa**, enquanto o relatório de entradas mostra apenas as entradas interativas.

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

- [Como configurar a política de risco do usuário](../identity-protection/howto-user-risk-policy.md)
- [Como configurar a política de correção de risco](../identity-protection/howto-user-risk-policy.md)
- [Tipos de evento de risco](concept-risk-events.md)
