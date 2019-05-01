---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: active-directory
author: rolyon
ms.service: active-directory
ms.topic: include
ms.date: 04/16/2019
ms.author: rolyon
ms.custom: include file
ms.openlocfilehash: ef8155cda6c31a63204af80ca091a13bb16a687d
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866428"
---
### <a name="policy-for-users-in-your-directory"></a>Política: Para usuários em seu diretório

Siga estas etapas se desejar que sua política para usuários e grupos no diretório que podem solicitar este pacote de acesso.

1. No **os usuários podem solicitar acesso** seção, selecione **para os usuários em seu diretório**.

1. No **selecionar usuários e grupos** seção, clique em **adicionar usuários e grupos**.

1. No painel de grupos e selecionar usuários, selecione os usuários e grupos que você deseja adicionar.

    ![Pacote de acesso - seleção de política de usuários e grupos](./media/active-directory-entitlement-management-policy/policy-select-users-groups.png)

1. Clique em **selecionar** para adicionar os usuários e grupos.

1. Pular para a [política: Solicitar](#policy-request) seção.

### <a name="policy-for-users-not-in-your-directory"></a>Política: Para que os usuários não em seu diretório

Siga estas etapas se desejar que sua política para usuários não em seu diretório que podem solicitar este pacote de acesso. Diretórios devem ser configurados para ser permitido na **restrições de colaboração de relações organizacionais** configurações.

1. No **os usuários podem solicitar acesso** seção, selecione **para que os usuários não em seu diretório**.

1. No **Select externo do Azure AD directory** seção, clique em **adicionar diretórios**.

1. Insira um nome de domínio e pesquise externo diretório do Azure AD.

1. Verifique se que ele é o diretório correto do nome de diretório fornecido e o domínio inicial.

    > [!NOTE]
    > Todos os usuários do diretório será capazes de solicitar esse pacote de acesso. Isso inclui usuários de todos os subdomínios associados com o diretório, não apenas o domínio usado na pesquisa.

    ![Pacote de acesso - seleção de política de diretórios](./media/active-directory-entitlement-management-policy/policy-select-directories.png)

1. Clique em **adicionar** para adicionar o diretório.

1. Repita essa etapa para adicionar quaisquer diretórios mais.

1. Depois de ter adicionado todos os diretórios você gostaria de incluir na política, clique em **selecionar**.

1. Pular para a [política: Solicitar](#policy-request) seção.

### <a name="policy-none-administrator-direct-assignments-only"></a>Política: None (atribuições diretas administrador somente)

Siga estas etapas se desejar que sua política para ignorar as solicitações de acesso e permitem aos administradores atribuir diretamente a usuários específicos para o pacote de acesso. Os usuários não precisam solicitar o acesso de pacote. Você ainda pode definir as configurações de expiração, mas não há nenhuma configuração de solicitação.

1. No **os usuários podem solicitar acesso** seção, selecione **None (atribuições diretas administrador apenas**.

    Depois de criar o pacote de acesso, você pode atribuir usuários internos e externos específicos diretamente para o pacote de acesso. Se você especificar um usuário externo, uma conta de usuário convidado será criada em seu diretório.

1. Pular para a [política: Expiração](#policy-expiration) seção.

### <a name="policy-request"></a>Política: Solicitação

Na seção Request, você especificar as configurações de aprovação quando os usuários solicitam o pacote de acesso.

1. Para solicitar aprovação para solicitações de usuários selecionados, defina as **exigir aprovação** alternar para o **Sim**. Para que as solicitações aprovadas automaticamente, defina a alternância como **não**.

1. Se você precisar de aprovação, na **Selecionar aprovadores** seção, clique em **adicione aprovadores**.

1. No painel Selecionar aprovadores, selecione um ou mais usuários e/ou grupos para ser aprovadores.

    Somente um dos aprovadores selecionados precisa aprovar uma solicitação. Não é necessária a aprovação de todos os aprovadores. A decisão de aprovação baseia-se em qualquer aprovador examina a solicitação primeiro.

    ![Pacote de acesso - seleção de política de aprovadores](./media/active-directory-entitlement-management-policy/policy-select-approvers.png)

1. Clique em **selecionar** para adicionar os aprovadores.

1. Clique em **Mostrar configurações de solicitação avançadas** para mostrar as configurações adicionais.

    ![Pacote de acesso - seleção de política de diretórios](./media/active-directory-entitlement-management-policy/policy-advanced-request.png)

1. Para exigir que os usuários forneçam uma justificativa para solicitar o acesso de pacote, defina **exigir justificativa** à **Sim**.

1. Para exigir que o aprovador fornecer uma justificativa para aprovar uma solicitação para o pacote de acesso, defina **exigir justificativa de aprovador** à **Sim**.

1. No **tempo limite de solicitação de aprovação (dias)** , especifique a quantidade de tempo que os aprovadores tem para uma solicitação de revisão. Se nenhum aprovador revisá-lo neste número de dias, a expiração da solicitação e o usuário terá que enviar outra solicitação para o pacote de acesso.

### <a name="policy-expiration"></a>Política: Expiração

Na seção expiração, você especificar quando a atribuição do usuário para o pacote de acesso expira.

1. No **validade** seção, defina **pacote de acesso expira** para **na data**, **número de dias**, ou **nunca**.

    Para **data**, selecione uma data de expiração no futuro.

    Para **número de dias**, especifique um número entre 0 e 3660 dias.

    Com base em sua seleção, a atribuição do usuário para o pacote de acesso expira em uma determinada data, um determinado número de dias depois que elas são aprovadas, ou nunca.

1. Clique em **Mostrar configurações de expiração avançadas** para mostrar as configurações adicionais.

1. Para permitir que o usuário estender suas atribuições, defina **permitir que os usuários estender o acesso** à **Sim**.

    Se as extensões são permitidas na política, o usuário receberá um email 14 dias e também o 1 dia antes de sua atribuição de acesso do pacote é definida para expirar notificá-los para estender a atribuição.

    ![Pacote de acesso - configurações de política de expiração](./media/active-directory-entitlement-management-policy/policy-expiration.png)

### <a name="policy-enable-policy"></a>Política: Habilitar política

1. Se você quiser que o pacote fique imediatamente disponível para os usuários na política de acesso, clique em **Sim** para habilitar a política.

    Você sempre poderá habilitá-lo no futuro depois de concluir a criação do pacote de acesso.

    ![Pacote de acesso – habilitar política de configuração de política](./media/active-directory-entitlement-management-policy/policy-enable.png)

1. Clique em **próxima** ou **criar**.
