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
ms.sourcegitcommit: f8b63e5831897d3a45298b0415bb2d6d44ab0de1
ms.openlocfilehash: 73727d8d1e5de01794589538bd67d3d698d325a0
ms.lasthandoff: 03/01/2017


---

# <a name="assign-licenses-to-a-group-of-users-in-azure-active-directory"></a>Atribuir de licenças a um grupo de usuários no Azure Active Directory

Neste artigo, vamos examinar um cenário básico de atribuir licenças de produto a um grupo e verificando todos os membros do grupo estão corretamente licenciados.

Em seu exemplo, o locatário contém um grupo de segurança chamado **departamento de RH** que inclui todos os membros do departamento de recursos humanos, que nesse caso é aproximadamente 1.000 usuários. O administrador deseja atribuir licenças do Office 365 Enterprise E3 para todo o departamento; o serviço de Enterprise Yammer incluído no produto precisa ser temporariamente desabilitada até um momento posterior, quando o departamento está pronto para começar a usá-lo. O administrador também deseja implantar as licenças do Enterprise Mobility + Segurança para o mesmo grupo de usuários.

## <a name="step-1-assign-the-required-licenses"></a>Etapa 1: Atribuir as licenças necessárias

1. Entre no [**portal do Azure**](https://portal.azure.com) com uma conta de administrador. Para gerenciar licenças, a conta precisa a função de Administrador Global ou a função de administrador da conta de usuário.

2. Selecione **mais serviços** no painel de navegação do lado esquerdo e selecione **Azure Active Directory**. Você pode "Favoritos" nessa folha clicando no ícone de estrela ou fixá-lo para o painel do portal.

3. Sobre o **Azure Active Directory** folha, selecione **licenças**. Isso abrirá uma folha no qual você pode ver e gerenciar todos os produtos licenciados por locatário.

4. Em **todos os produtos**, selecione o Office 365 Enterprise E3 e mobilidade corporativa + segurança selecionando os nomes de produto. Selecione **atribuir** na parte superior da folha para iniciar a atribuição.

  ![todos os produtos, atribuir licença](media/active-directory-licensing-group-assignment-azure-portal/all-products-assign.png)

5. Sobre o **atribuir licença** folha, clique em **usuários e grupos** para abrir a folha de usuário e grupo. Procure o nome do grupo *departamento de RH*, selecione o grupo e, em seguida, certifique-se de confirmar clicando **selecione** na parte inferior da folha.

  ![Selecione um grupo](media/active-directory-licensing-group-assignment-azure-portal/select-a-group.png)

6. Sobre o **atribuir licença** folha, clique em **opções de atribuição (opcionais)** que exibe todos os planos de serviço que inclui os dois produtos são selecionados anteriormente. Localizar Enterprise Yammer e desative- **Off** para desabilitar esse serviço de licença do produto. Confirme clicando em **OK** na parte inferior das **opções de Atribuição**.

  ![opções de atribuição](media/active-directory-licensing-group-assignment-azure-portal/assignment-options.png)

7. Por fim, no **atribuir licença** folha, clique em **atribuir** na parte inferior da folha para concluir a atribuição.

8. Uma notificação é exibida no canto superior direito do lado mostrando o status e o resultado do processo. Se a atribuição para o grupo não pôde ser concluída (por exemplo, devido a licenças já existentes no grupo), clique na notificação para exibir detalhes da falha.

Agora podemos ter especificado um modelo de licença no grupo de departamento de RH. Um processo em segundo plano no Azure AD foi iniciado para processar todos os membros existentes do grupo. Essa operação inicial pode levar algum tempo, dependendo do tamanho atual do grupo. Na próxima etapa, descreveremos como verificar que o processo foi concluído e se atenção adicional é necessária para resolver problemas.

> [!NOTE]
> A mesma atribuição pode ser iniciada a partir de um local alternativo: **usuários e grupos** no Azure AD. Vá para **Azure Active Directory &gt; usuários e grupos &gt; todos os grupos,** localizar o grupo, selecione-a e vá para o **licenças** guia. O **atribuir** botão na parte superior da lâmina será aberta a folha de atribuição de licença.

## <a name="step-2-verify-that-the-initial-assignment-has-completed"></a>Etapa 2: Verificar se a atribuição inicial foi concluída

1. Vá para **Azure Active Directory &gt; Usuários e grupos &gt; Todos os grupos** e localize o grupo *Departamento de RH* ao qual as licenças foram atribuídas.

2. No *departamento de RH* folha de grupo, selecione **licenças** para confirmar rapidamente se licenças foram totalmente atribuídas a usuários e se houver erros que exigem exame, incluindo:

  - As licenças de produto que foram atribuídas ao grupo. Selecione uma entrada para mostrar a serviços específicos que foram habilitada e fazer alterações.

  - Status das alterações mais recentes feitas na atribuição de licença: se as alterações estão sendo processadas ou se o processamento foi concluído em todos os membros de usuário.

  - Se houver erros, informações sobre os usuários em estado de erro para licenças de quem não pôde ser atribuídas.

  ![opções de atribuição](media/active-directory-licensing-group-assignment-azure-portal/assignment-errors.png)

3. Informações mais detalhadas sobre o processamento de licença estão disponíveis em **Azure Active Directory &gt; Usuários e grupos &gt;
    *nomedogrupo* &gt; Logs de auditoria**:

  - Atividade: **começar a aplicar a licença de grupo com base para usuários**. Ele é registrado quando nosso sistema pega a alteração de atribuição de licença no grupo e inicia a aplicá-la a todos os membros de usuário. Ele contém informações sobre a alteração foi feita.

  - Atividade: **terminar de aplicar a licença de grupo com base para usuários**. Ele é registrado quando nosso sistema conclui o processamento de todos os usuários no grupo. Ele contém um resumo de quantos usuários foram processados com êxito e quantos usuários não foi possível atribuir as licenças do grupo.

## <a name="step-3-checking-for-license-problems-and-resolving-them"></a>Etapa 3: Verificar problemas de licença e resolvê-los

1. Vá para **Azure Active Directory &gt; Usuários e grupos &gt; Todos os grupos** e localize o grupo *Departamento de RH* ao qual as licenças foram atribuídas.

2. Sobre o **departamento de RH** folha de grupo, selecione **licenças**. A notificação na parte superior da lâmina significa que há 10 usuários para que as licenças não pôde ser atribuídas. Ele abre uma lista de todos os usuários em estado de erro para esse grupo de licenciamento.

3. O **falha atribuições** coluna informa que ambas as licenças de produto não pôde ser atribuídas aos usuários. **Principais motivo da falha** contém a causa da falha, neste caso **planos de serviço conflitante**.

  ![atribuições com falha](media/active-directory-licensing-group-assignment-azure-portal/failed-assignments.png)

4. Selecione um usuário para abrir o **licenças** folha mostrando todas as licenças atualmente atribuídas ao usuário. Neste exemplo, podemos ver o usuário tem o Office 365 Enterprise E1 licença herdada da **os usuários do quiosque** grupo. Entra em conflito com a licença E3 que o sistema tenta aplicar a partir de **departamento de RH** grupo; como resultado, nenhuma das licenças desse grupo foi atribuída ao usuário.

  ![Exibir as licenças para um usuário](media/active-directory-licensing-group-assignment-azure-portal/user-license-view.png)

5. Para resolver isso, podemos remover o usuário a **os usuários do quiosque** grupo. Após a alteração do Azure AD processa o **departamento de RH** licenças são atribuídas corretamente.

  ![licença atribuída corretamente](media/active-directory-licensing-group-assignment-azure-portal/license-correctly-assigned.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o conjunto de recursos para gerenciamento de licenças por meio de grupos, leia

* [O que é o licenciamento baseado em grupo no Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [Identificar e resolver problemas de licença para um grupo no Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Como migrar usuários individuais licenciados para licenciamento baseado em grupo no Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)
* [Cenários adicionais de licenciamento baseado em grupo do Azure Active Directory](active-directory-licensing-group-advanced.md)

