---
title: Atribuir acesso a dados do Gerenciamento de Custos do Azure | Microsoft Docs
description: Este artigo orienta você pela atribuição de permissão para dados do Gerenciamento de Custos do Azure para diversos escopos de acesso.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/09/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: e32e281509da32d4816c9e137a462553891c82f1
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51686136"
---
# <a name="assign-access-to-cost-management-data"></a>Atribuir acesso a dados do Gerenciamento de Custos

Para a maioria dos usuários, uma combinação de permissões concedidas no portal do Azure e no portal do EA (Contrato Enterprise) define o nível de acesso de um usuário aos dados do Gerenciamento de Custos do Azure. Este artigo orienta você pela atribuição de acesso aos dados do Gerenciamento de Custos. Depois que a combinação de permissões é atribuída, os dados das exibições do usuário no Gerenciamento de Custos com base no escopo ao qual eles têm acesso e no escopo que eles selecionam no portal do Azure.

O escopo que um usuário seleciona é usado em todo o Gerenciamento de Custos para fornecer a consolidação de dados e para controlar o acesso a informações de custo. Ao usar escopos, os usuários não selecionam vários deles. Em vez disso, eles selecionam um escopo mais amplo que em que os escopos filho se acumulam, para depois filtrarem o que desejam exibir. A consolidação de dados é importante para entender porque algumas pessoas não devem ter acesso a um escopo pai acumulado por escopos filho.

## <a name="cost-management-scopes"></a>Escopos do Gerenciamento de Custos

Para exibir dados de custo, um usuário precisa ter acesso de leitura a pelo menos um ou mais dos escopos a seguir.

| **Escopo** | **Definido em** | **Acesso necessário para exibir dados** | **Configuração de pré-requisito de EA** | **Consolida os dados para** |
| --- | --- | --- | --- | --- |
| Conta de cobrança<sup>1</sup> | [https://ea.azure.com](https://ea.azure.com/) | Administrador Corporativo | Nenhum | Todas as assinaturas do contrato empresarial |
| department | [https://ea.azure.com](https://ea.azure.com/) | Administrador de departamento | **Encargos de exibição do administrador de departamento** habilitados | Todas as assinaturas que pertencem a uma conta de registro que esteja vinculada ao departamento |
| Conta de registro<sup>2</sup> | [https://ea.azure.com](https://ea.azure.com/) | Proprietário da conta | **Encargos de exibição do proprietário da conta** habilitados | Todas as assinaturas da conta de registro |
| Grupo de gerenciamento | [https://portal.azure.com](https://portal.azure.com/) | Leitor do Gerenciamento de Custos (ou Leitor) | **Encargos de exibição do proprietário da conta** habilitados | Todas as assinaturas abaixo do grupo de gerenciamento |
| Assinatura | [https://portal.azure.com](https://portal.azure.com/) | Leitor do Gerenciamento de Custos (ou Leitor) | **Encargos de exibição do proprietário da conta** habilitados | Todos os recursos/grupos de recursos na assinatura |
| Grupo de recursos | [https://portal.azure.com](https://portal.azure.com/) | Leitor do Gerenciamento de Custos (ou Leitor) | **Encargos de exibição do proprietário da conta** habilitados | Todos os recursos no grupo de recursos |

<sup>1</sup> A conta de cobrança é também chamada de Registro ou Contrato Enterprise.

<sup>2</sup> A conta de registro é também chamada de proprietário da conta.

## <a name="enable-access-to-costs-in-the-ea-portal"></a>Habilitar o acesso aos custos no portal do EA

O escopo da conta de cobrança requer a opção **Encargos de exibição do administrador de departamento** **Habilitada** no portal do EA. Todos os outros escopos requerem a opção **Encargos de exibição do proprietário da conta** **Habilitada** no portal do EA.

Para habilitar uma opção:

1. Entrar no portal do EA em [https://ea.azure.com](https://ea.azure.com) com uma conta de administrador corporativo.
2. No painel esquerdo, selecione **Gerenciar**.
3. Para os escopos do Gerenciamento de Custos aos quais você deseja permitir acesso, habilite a opção de cobrança **Encargos de exibição do administrador de departamento** e/ou **Encargos de exibição do proprietário da conta**.  
    ![Guia Registro, mostrando as opções de encargos de exibição do administrador de departamento e do proprietário da conta](./media/assign-access-acm-data/ea-portal-enrollment-tab.png)

Depois que as opções de cobrança de exibição são habilitadas, a maioria dos escopos também exige a configuração de permissão de RBAC (controle de acesso baseado em função) no portal do Azure.

## <a name="enterprise-administrator-role"></a>Função de administrador corporativo

Por padrão, um administrador corporativo tem acesso à conta de cobrança (Contrato Enterprise/registro) e a todos os outros escopos, que são escopos-filho. O administrador corporativo atribui acesso a escopos para outros usuários. Como prática recomendada para continuidade dos negócios, você deve sempre ter dois usuários com acesso de administrador corporativo. As seções a seguir são exemplos passo a passo do administrador corporativo atribuindo acesso a escopos para outros usuários.

## <a name="assign-billing-account-scope-access"></a>Atribuir acesso ao escopo da conta de cobrança

O acesso ao escopo da conta de cobrança requer permissão de administrador corporativo no portal do EA. O administrador corporativo tem acesso para exibir os custos em todo registro de EA ou em vários registros. Nenhuma ação é necessária no portal do Azure para o escopo da conta de cobrança.

1. Entrar no portal do EA em [https://ea.azure.com](https://ea.azure.com) com uma conta de administrador corporativo.
2. No painel esquerdo, selecione **Gerenciar**.
3. Na guia **registro**, selecione o registro que você deseja gerenciar.  
    ![Portal EA](./media/assign-access-acm-data/ea-portal.png)
4. Clique em **+ Adicionar Administrador**.
5. Na caixa Adicionar Administrador, selecione o tipo de autenticação e digite o endereço de email do usuário.
6. Se o usuário deve ter acesso somente leitura aos dados de uso e de custo, em **Somente leitura**, selecione **Sim**.  Caso contrário, selecione **Não**.
7. Clique em **Adicionar** para criar a conta.  
    ![Caixa Adicionar Administrador](./media/assign-access-acm-data/add-admin.png)

Pode levar até 30 minutos antes que o novo usuário possa acessar dados no Gerenciamento de Custos.

### <a name="assign-department-scope-access"></a>Atribuir acesso ao escopo do departamento

O acesso ao escopo de departamento requer acesso de administrador de departamento (encargos de exibição do administrador de departamento) no portal do EA. O administrador de departamento tem acesso para exibir os dados de custos e de uso associados com um departamento ou com vários departamentos.  Os dados para o departamento incluem todas as assinaturas que pertencem a uma conta de registro vinculada ao departamento. Nenhuma ação é necessária no portal do Azure.

1. Entrar no portal do EA em [https://ea.azure.com](https://ea.azure.com) com uma conta de administrador corporativo.
2. No painel esquerdo, selecione **Gerenciar**.
3. Na guia **registro**, selecione o registro que você deseja gerenciar.
4. Clique na guia **departamento** e, em seguida, clique em **Adicionar administrador**.
5. Na caixa Adicionar Administrador de Departamento, selecione o tipo de autenticação e depois digite o endereço de email do usuário.
6. Se o usuário deve ter acesso somente leitura aos dados de uso e de custo, em **Somente leitura**, selecione **Sim**.  Caso contrário, selecione **Não**.
7. Selecione os departamentos aos quais você deseja conceder permissão administrativa.
8. Clique em **Adicionar** para criar a conta.  
    ![Caixa Adicionar administrador de departamento](./media/assign-access-acm-data/add-depart-admin.png)

## <a name="assign-enrollment-account-scope-access"></a>Atribuir acesso ao escopo da conta de registro

O acesso ao escopo da conta de registro requer acesso de proprietário da conta (encargos de exibição de proprietário da conta) no portal do EA. O proprietário da conta pode exibir dados de uso e de custos associados com uma conta de registro. Dados na conta de registro incluem todas as assinaturas do Azure associadas ao registro. Nenhuma ação é necessária no portal do Azure.

1. Entrar no portal do EA em [https://ea.azure.com](https://ea.azure.com) com uma conta de administrador corporativo.
2. No painel esquerdo, selecione **Gerenciar**.
3. Na guia **registro**, selecione o registro que você deseja gerenciar.
4. Clique na guia **Conta** e depois clique em **Adicionar conta**.
5. Na caixa Adicionar conta, selecione o **departamento** ao qual associar a conta ou deixe-o como não atribuído.
6. Selecione o tipo de autenticação e digite o nome da conta.
7. Digite o endereço de email do usuário e depois, opcionalmente, digite o centro de custo.
8. Clique em **Adicionar** para criar a conta.  
    ![Caixa Adicionar conta](./media/assign-access-acm-data/add-account.png)

## <a name="assign-management-group-scope-access"></a>Atribuir acesso ao escopo do grupo de gerenciamento

O acesso ao escopo do grupo de gerenciamento requer pelo menos permissão ao Leitor de Gerenciamento de Custos (ou Leitor). Você configura a permissão ao grupo de gerenciamento no portal do Azure. Você deve ter pelo menos permissão de colaborador ao grupo de gerenciamento para habilitar o acesso para outras pessoas. E você deve habilitar também a configuração **encargos de exibição do sol** no portal do EA.

1. Entre no Portal do Azure em [http://portal.azure.com](http://portal.azure.com).
2. Na barra lateral, selecione **Todos os serviços**, pesquise _Grupos de gerenciamento_ e, em seguida, selecione **Grupos de gerenciamento**.
3. Selecione o grupo de gerenciamento na hierarquia.
4. Ao lado do nome do grupo de gerenciamento, clique em **Detalhes**.
5. Selecione **Controle de Acesso (IAM)** no painel esquerdo.
6. Clique em **Adicionar**.
7. Em **Função**, selecione **Leitor do Gerenciamento de Custos**.
8. Em **Atribuir acesso a**, selecione **Usuário, grupo ou aplicativo do Azure AD**.
9. Para atribuir acesso, pesquise pelo usuário e selecione-o.
10. Clique em **Salvar**.  
    ![Caixa Adicionar permissões](./media/assign-access-acm-data/add-permissions.png)

## <a name="assign-subscription-scope-access"></a>Atribuir acesso ao escopo de assinatura

O acesso a uma assinatura requer pelo menos permissão ao Leitor de Gerenciamento de Custos (ou Leitor). Você configura a permissão a uma assinatura no portal do Azure. Você precisa ter pelo menos permissão de colaborador à assinatura para habilitar o acesso para outras pessoas. E você deve habilitar também a configuração **encargos de exibição do sol** no portal do EA.

1. Entre no Portal do Azure em [http://portal.azure.com](http://portal.azure.com).
2. Na barra lateral, selecione **Todos os serviços**, pesquise _assinaturas_ e, em seguida, selecione **Assinaturas**.
3. Selecione sua assinatura.
4. Selecione **Controle de Acesso (IAM)** no painel esquerdo.
5. Clique em **Adicionar**.
6. Em **Função**, selecione **Leitor do Gerenciamento de Custos**.
7. Em **Atribuir acesso a**, selecione **Usuário, grupo ou aplicativo do Azure AD**.
8. Para atribuir acesso, pesquise pelo usuário e selecione-o.
9. Clique em **Salvar**.

## <a name="assign-resource-group-scope-access"></a>Atribuir acesso ao escopo do grupo de recursos

O acesso a um grupo de recursos requer pelo menos permissão ao Leitor de Gerenciamento de Custos (ou Leitor). Você configura a permissão ao grupo de recursos no portal do Azure. Você deve ter pelo menos permissão de colaborador ao grupo de recursos para habilitar o acesso para outras pessoas. E você deve habilitar também a configuração **encargos de exibição do sol** no portal do EA.

1. Entre no Portal do Azure em [http://portal.azure.com](http://portal.azure.com).
2. Na barra lateral, selecione **Todos os serviços**, pesquise _Grupos de recursos_ e, em seguida, selecione **Grupos de recursos**.
3. Selecione o grupo de recursos.
4. Selecione **Controle de Acesso (IAM)** no painel esquerdo.
5. Clique em **Adicionar**.
6. Em **Função**, selecione **Leitor do Gerenciamento de Custos**.
7. Em **Atribuir acesso a**, selecione **Usuário, grupo ou aplicativo do Azure AD**.
8. Para atribuir acesso, pesquise pelo usuário e selecione-o.
9. Clique em **Salvar**.

## <a name="next-steps"></a>Próximas etapas

- Se você ainda não concluiu o primeiro início rápido de Gerenciamento de Custos, leia-o em [Iniciar a análise dos custos](quick-acm-cost-analysis.md).
