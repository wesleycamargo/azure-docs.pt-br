---
title: 'Azure AD Connect: próximas etapas e como gerenciar o Azure AD Connect | Microsoft Docs'
description: Aprenda a estender configuração padrão e tarefas operacionais para o Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: femila
editor: curtand

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2016
ms.author: billmath

---
# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>Próximas etapas e como gerenciar o Azure AD Connect
A seguir estão tópicos operacionais avançados que permitem que você personalize o Azure Active Directory Connect para atender às necessidades e requisitos de sua organização.  

## <a name="add-additional-sync-administrators"></a>Adicionar administradores de sincronização adicionais
Por padrão, somente o usuário que fez a instalação e os administradores locais poderão gerenciar o mecanismo de sincronização instalado. Para que outras pessoas possam acessar e gerenciar o mecanismo de sincronização, localize o grupo chamado ADSyncAdmins no servidor local e adicione-os a esse grupo.

## <a name="assigning-licenses-to-azure-ad-premium-and-enterprise-mobility-users"></a>Atribuir licenças aos usuários do Azure AD Premium e do Enterprise Mobility
Agora que os usuários foram sincronizados para a nuvem, você precisará atribua uma licença para que eles possam começar a usar aplicativos de nuvem, como o Office 365.

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>Como atribuir uma licença do Enterprise Mobility Suite ou do Azure AD Premium
- - -
1. Entre no portal do Azure como administrador.
2. Selecione **Active Directory**à esquerda.
3. Na página do Active Directory, clique duas vezes no diretório que tenha os usuários que você deseja habilitar.
4. Na parte superior da página do diretório, selecione **Licenças**.
5. Na página de licenças, selecione Active Directory Premium ou Enterprise Mobility Suite e, em seguida, clique em **Atribuir**.
6. Na caixa de diálogo, selecione os usuários para os quais você deseja atribuir licenças e, em seguida, clique no ícone de marca de seleção para salvar as alterações.

## <a name="verifying-the-scheduled-synchronization-task"></a>Verificando a tarefa de sincronização agendada
Se você quiser verificar o status de uma sincronização, você pode fazer isso verificando no portal do Azure.

### <a name="to-verify-the-scheduled-synchronization-task"></a>Como verificar a tarefa de sincronização agendada
- - -
1. Entre no portal do Azure como administrador.
2. Selecione **Active Directory**à esquerda.
3. Na página do Active Directory, clique duas vezes no diretório que tenha os usuários que você deseja habilitar.
4. Na parte superior da página do diretório, selecione **Integração de diretório**.
5. Na integração com o Active Directory local, observe o horário da última sincronização.

<center>![Nuvem](./media/active-directory-aadconnect-whats-next/verify.png)</center>

## <a name="starting-a-scheduled-synchronization-task"></a>Iniciando uma tarefa de sincronização agendada
Se você precisar executar uma tarefa de sincronização, você pode fazer isso executando novamente por meio do assistente do Azure AD Connect.  Você precisará fornecer suas credenciais do Azure AD.  No assistente, selecione a tarefa **Personalizar opções de sincronização** e clique em Avançar no assistente. No final, certifique-se de que a caixa **Iniciar o processo de sincronização assim que a configuração for concluída** esteja marcada.

<center>![Nuvem](./media/active-directory-aadconnect-whats-next/startsynch.png)</center>

Para obter mais informações sobre a sincronização do Azure AD Connect: Agendador, veja [Agendador do Azure AD Connect](active-directory-aadconnectsync-feature-scheduler.md)

## <a name="additional-tasks-available-in-azure-ad-connect"></a>Tarefas adicionais disponíveis no Azure AD Connect
Após a instalação inicial do Azure AD Connect, você pode sempre iniciar o assistente novamente de um atalho da área de trabalho ou da página inicial do Azure AD Connect.  Você observará que executar novamente o assistente fornece algumas novas opções na forma de tarefas adicionais.  

A tabela a seguir fornece um resumo dessas tarefas e uma breve descrição de cada uma delas.

![Regra de junção](./media/active-directory-aadconnect-whats-next/addtasks.png)

| Tarefas adicionais | Descrição |
| --- | --- |
| Exibir o cenário selecionado |Permite que você exiba sua solução atual para o Azure AD Connect.  Isso inclui configurações gerais, diretórios sincronizados, configurações de sincronização, etc. |
| Personalizar opções de sincronização |Permite que você altere a configuração atual, incluindo a adição de florestas do Active Directory para a configuração ou habilitando opções de sincronização como usuário, grupo, dispositivo ou write-back de senha. |
| Habilitar o modo de preparação |Isso permite que você prepare as informações que posteriormente serão sincronizadas, mas nada será exportado para o Azure AD ou Active Directory.  Isso permite que você visualize as sincronizações antes que elas ocorram. |

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).

<!--HONumber=Oct16_HO2-->


