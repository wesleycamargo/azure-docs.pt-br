---
title: "Azure AD Connect: próximas etapas e como gerenciar o Azure AD Connect | Microsoft Docs"
description: "Aprenda a estender configuração padrão e tarefas operacionais para o Azure AD Connect."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: curtand
ms.assetid: c18bee36-aebf-4281-b8fc-3fe14116f1a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: dde6242211ca3d8d7b47c0ec4a01965e1439adeb
ms.openlocfilehash: d3b900b40f683cd446b41696c4f9e617299bf0b1
ms.contentlocale: pt-br
ms.lasthandoff: 02/22/2017

---
<a id="next-steps-and-how-to-manage-azure-ad-connect" class="xliff"></a>

# Próximas etapas e como gerenciar o Azure AD Connect
Use os procedimentos operacionais neste artigo para personalizar o Azure AD (Azure Active Directory) Connect para atender às necessidades e requisitos de sua organização.  

<a id="add-additional-sync-admins" class="xliff"></a>

## Adicionar administradores de sincronização adicionais
Por padrão, somente o usuário que fez a instalação e os administradores locais podem gerenciar o mecanismo de sincronização instalado. Para que outras pessoas possam acessar e gerenciar o mecanismo de sincronização, localize o grupo chamado ADSyncAdmins no servidor local e adicione-os a esse grupo.

<a id="assign-licenses-to-azure-ad-premium-and-enterprise-mobility-suite-users" class="xliff"></a>

## Atribuir licenças aos usuários do Azure AD Premium e do Enterprise Mobility Suite
Agora que os usuários foram sincronizados para a nuvem, você precisará atribuir uma licença para que eles possam começar a usar aplicativos de nuvem como o Office 365.

<a id="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license" class="xliff"></a>

### Como atribuir uma licença do Enterprise Mobility Suite ou do Azure AD Premium

1. Entre no Portal do Azure como um administrador.
2. Selecione **Active Directory**à esquerda.
3. Na página do **Active Directory**, clique duas vezes no diretório que tem os usuários que você deseja configurar.
4. Na parte superior da página do diretório, selecione **Licenças**.
5. Na página **Licenças**, selecione **Active Directory Premium** ou **Enterprise Mobility Suite** e, em seguida, clique em **Atribuir**.
6. Na caixa de diálogo, selecione os usuários para os quais você deseja atribuir licenças e, em seguida, clique no ícone de marca de seleção para salvar as alterações.

<a id="verify-the-scheduled-synchronization-task" class="xliff"></a>

## Verificar a tarefa de sincronização agendada
Use o Portal do Azure para verificar o status de uma sincronização.

<a id="to-verify-the-scheduled-synchronization-task" class="xliff"></a>

### Como verificar a tarefa de sincronização agendada
1. Entre no Portal do Azure como um administrador.
2. Selecione **Active Directory**à esquerda.
3. Na página do **Active Directory**, clique duas vezes no diretório que tem os usuários que você deseja configurar.
4. Na parte superior da página do diretório, selecione **Integração de diretório**.
5. Na **integração com o Active Directory local**, observe o horário da última sincronização.

<center>![Horário de sincronização de diretórios](./media/active-directory-aadconnect-whats-next/verify.png)</center>

<a id="start-a-scheduled-synchronization-task" class="xliff"></a>

## Iniciar uma tarefa de sincronização agendada
Se você precisar executar uma tarefa de sincronização, poderá fazer isso executando-a novamente por meio do assistente do Azure AD Connect.  Você precisa fornecer suas credenciais do Azure AD.  No assistente, selecione a tarefa **Personalizar opções de sincronização** e clique em **Avançar** para seguir pelo assistente. No final, certifique-se de que a caixa **Iniciar o processo de sincronização assim que a configuração for concluída** esteja selecionada.

<center>![Iniciar a sincronização](./media/active-directory-aadconnect-whats-next/startsynch.png)</center>

Para obter mais informações sobre o Agendador de sincronização do Azure AD Connect, consulte [Agendador do Azure AD Connect](active-directory-aadconnectsync-feature-scheduler.md).

<a id="additional-tasks-available-in-azure-ad-connect" class="xliff"></a>

## Tarefas adicionais disponíveis no Azure AD Connect
Após a instalação inicial do Azure AD Connect, você pode sempre iniciar o assistente novamente de um atalho da área de trabalho ou da página inicial do Azure AD Connect.  Você observará que executar novamente o assistente fornece algumas novas opções na forma de tarefas adicionais.  

A tabela a seguir fornece um resumo dessas tarefas e uma breve descrição de cada uma delas.

![Lista de tarefas adicionais](./media/active-directory-aadconnect-whats-next/addtasks.png)

| Tarefa adicional | Descrição |
| --- | --- |
| **Exibir o cenário selecionado** |Exiba sua solução atual do Azure AD Connect.  Isso inclui configurações gerais, diretórios sincronizados e configurações de sincronização. |
| **Personalizar opções de sincronização** |Altere a configuração atual, por exemplo adicionando florestas do Active Directory para a configuração ou habilitando opções de sincronização como usuário, grupo, dispositivo ou write-back de senha. |
| **Habilitar o modo de preparo** |Informações de preparo que não são sincronizadas imediatamente e não são exportadas para o Azure AD ou o Active Directory local.  Com esse recurso, você pode visualizar as sincronizações antes que elas ocorram. |

<a id="next-steps" class="xliff"></a>

## Próximas etapas
Saiba mais sobre [como integrar suas identidades locais ao Azure Active Directory](active-directory-aadconnect.md).

