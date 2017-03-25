---

title: "Atribuir licenças a um grupo no Azure Active Directory | Microsoft Docs"
description: "Como atribuir licenças usando o licenciamento baseado em grupo do Azure Active Directory"
services: active-directory
keywords: Licenciamento do AD do Azure
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: cd2c8b8bd006d3172cb75f12f4a16fef9b698974
ms.lasthandoff: 03/10/2017


---

# <a name="assign-licenses-to-a-group-of-users-in-azure-active-directory"></a>Atribuir de licenças a um grupo de usuários no Azure Active Directory

Neste artigo, vamos examinar um cenário básico de atribuição de licenças de produtos a um grupo de usuários no Azure Active Directory (Azure AD) e, em seguida, verificar se todos os membros do grupo estão licenciados corretamente.

Neste exemplo, o locatário contém um grupo de segurança chamado *Departamento de RH*. Esse grupo inclui todos os membros do departamento de recursos humanos (nesse caso, a cerca de 1.000 usuários). O administrador deseja atribuir licenças do Office 365 Enterprise E3 para todo o departamento. O serviço Yammer Enterprise, que está incluído no produto, precisa ser desabilitado temporariamente até um momento posterior, quando o departamento estiver pronto para começar a usá-lo. O administrador também deseja implantar as licenças do Enterprise Mobility + Segurança para o mesmo grupo de usuários.

## <a name="step-1-assign-the-required-licenses"></a>Etapa 1: Atribuir as licenças necessárias

1. Entre no [**portal do Azure**](https://portal.azure.com) com uma conta de administrador. Para gerenciar as licenças, a conta precisa da função de administrador global ou da função de administrador da conta de usuário.

2. Selecione **Mais serviços** no painel de navegação do lado esquerdo e selecione **Azure Active Directory**. Para adicionar essa folha aos "Favoritos", clique no ícone de estrela ou fixe-o no painel do portal.

3. Sobre o **Azure Active Directory** folha, selecione **licenças**. Isso abrirá uma folha no qual você pode ver e gerenciar todos os produtos licenciados no locatário.

4. Em **todos os produtos**, selecione o Office 365 Enterprise E3 e mobilidade corporativa + segurança selecionando os nomes de produto. Selecione **atribuir** na parte superior da folha para iniciar a atribuição.

  ![Todos os produtos, atribuir licença](media/active-directory-licensing-group-assignment-azure-portal/all-products-assign.png)

5. Na folha **Atribuir licença**, clique em **Usuários e grupos** para abrir a folha de **Usuários e grupos**. Procure o nome do grupo *departamento de RH*, selecione o grupo e, em seguida, certifique-se de confirmar clicando **selecione** na parte inferior da folha.

  ![Selecione um grupo](media/active-directory-licensing-group-assignment-azure-portal/select-a-group.png)

6. Na folha **Atribuir licença**, clique em **Opções de atribuição (opcionais)**, que exibe todos os planos de serviço incluídos nos dois produtos que foram selecionados anteriormente. Localize **Yammer Enterprise** e **desative** para desabilitar esse serviço de licença do produto. Confirme clicando em **OK** na parte inferior das **opções de Atribuição**.

  ![Opções de atribuição](media/active-directory-licensing-group-assignment-azure-portal/assignment-options.png)

7. Por fim, no **atribuir licença** folha, clique em **atribuir** na parte inferior da folha para concluir a atribuição.

8. Uma notificação é exibida no canto superior direito mostrando o status e o resultado do processo. Se a atribuição para o grupo não pode ser concluída (por exemplo, devido a licenças já existentes no grupo), clique na notificação para exibir detalhes da falha.

Agora podemos ter especificado um modelo de licença no grupo de departamento de RH. Um processo em segundo plano no Azure AD foi iniciado para processar todos os membros existentes do grupo. Essa operação inicial pode levar algum tempo, dependendo do tamanho atual do grupo. Na próxima etapa, descreveremos como verificar que o processo foi concluído e se atenção adicional é necessária para resolver problemas.

> [!NOTE]
> A mesma atribuição pode ser iniciada a partir de um local alternativo: **Usuários e grupos** no Azure AD. Vá para **Azure Active Directory** > **Usuários e grupos** > **Todos os grupos**. Localize o grupo, selecione-o e acesse a guia **Licenças**. O **atribuir** botão na parte superior da lâmina será aberta a folha de atribuição de licença.

## <a name="step-2-verify-that-the-initial-assignment-has-completed"></a>Etapa 2: Verificar se a atribuição inicial foi concluída

1. Vá para **Azure Active Directory** > **Usuários e grupos** > **Todos os grupos**. Localize o grupo do *Departamento de RH* cujas licenças foram atribuídas.

2. Na folha de grupo do *departamento de RH*, selecione **Licenças** para confirmar rapidamente se as licenças foram totalmente atribuídas a usuários, e se houver erros que exigem atenção. As informações a seguir estão disponíveis:

  - Lista de licenças de produtos que estão atualmente atribuídas ao grupo. Selecione uma entrada para mostrar a serviços específicos que foram habilitada e fazer alterações.

  - Status das alterações mais recentes de licença que foram feitas para o grupo (se as alterações estão sendo processadas ou o processamento foi concluído para todos os membros de usuário).

  - Informações sobre os usuários que estão em um estado de erro porque não foi possível atribuir licenças a eles.

  ![Opções de atribuição](media/active-directory-licensing-group-assignment-azure-portal/assignment-errors.png)

3. Confira informações mais detalhadas sobre a licença de processamento em **Azure Active Directory** > **Usuários e grupos** > *Nome do grupo* > **Logs de auditoria**. Observe as seguintes atividades:

  - Atividade: **começar a aplicar a licença de grupo com base para usuários**. Isso é registrado quando o sistema identifica a alteração de atribuição de licença no grupo e inicia a aplicá-la a todos os membros de usuário. Ele contém informações sobre a alteração foi feita.

  - Atividade: **terminar de aplicar a licença de grupo com base para usuários**. Isso é registrado quando o sistema conclui o processamento de todos os usuários no grupo. Ele contém um resumo de quantos usuários foram processados com êxito e quantos usuários não puderam ter licenças de grupo atribuídas.

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>Etapa 3: verificar problemas de licença e resolvê-los

1. Vá para **Azure Active Directory** > **Usuários e grupos** > **Todos os grupos** e localize o grupo do *Departamento de RH* ao qual as licenças foram atribuídas.
2. Sobre o **departamento de RH** folha de grupo, selecione **licenças**. A notificação sobre a folha mostra que há 10 usuários cujas licenças não puderam ser atribuídas. Ele abre uma lista de todos os usuários com um estado de erro para esse grupo.
3. A coluna **Atribuições com falha** informa que ambas as licenças de produtos não puderam ser atribuídas aos usuários. **Principais motivo da falha** contém a causa da falha. Nesse caso, **Planos de serviço conflitante**.

  ![Atribuições com falha](media/active-directory-licensing-group-assignment-azure-portal/failed-assignments.png)

4. Selecione um usuário para abrir a folha **Licenças**. Esta folha mostra todas as licenças que estão atualmente atribuídas ao usuário. Neste exemplo, podemos ver que o usuário tem a licença Office 365 Enterprise E1 herdada do grupo **usuários do Kiosk**. Isso está em conflito com a licença E3 que o sistema tentou aplicar a partir do grupo **Departamento de RH**. Como resultado, nenhuma das licenças do grupo foram atribuídas ao usuário.

  ![Exibir as licenças para um usuário](media/active-directory-licensing-group-assignment-azure-portal/user-license-view.png)

5. Para resolver isso, podemos remover o usuário a **os usuários do quiosque** grupo. Após a alteração do Azure AD processa o **departamento de RH** licenças são atribuídas corretamente.

  ![Licença atribuída corretamente](media/active-directory-licensing-group-assignment-azure-portal/license-correctly-assigned.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o conjunto de recursos para gerenciamento de licenças por meio de grupos, confira:

* [O que é o licenciamento baseado em grupo no Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [Identificar e resolver problemas de licença para um grupo no Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Como migrar usuários individuais licenciados para licenciamento baseado em grupo no Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)
* [Cenários adicionais de licenciamento baseado em grupo do Azure Active Directory](active-directory-licensing-group-advanced.md)

