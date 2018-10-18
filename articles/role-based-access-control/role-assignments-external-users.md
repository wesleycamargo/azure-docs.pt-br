---
title: Gerenciar acesso para usuários externos usando RBAC no Azure| Microsoft Docs
description: Saiba como gerenciar o acesso de usuários externos para uma organização usando RBAC (controle de acesso baseado em função) no Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 03/20/2018
ms.author: rolyon
ms.reviewer: skwan
ms.custom: it-pro
ms.openlocfilehash: bd75ecde75d0f22dc66f047cd063dd85807f6f33
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44304434"
---
# <a name="manage-access-for-external-users-using-rbac"></a>Gerenciar acesso para usuários externos usando RBAC

O controle de acesso baseado em função (RBAC) permite um melhor gerenciamento de segurança para grandes empresas e SMBs que trabalham com colaboradores externos, fornecedores ou freelancers que precisam de acesso a recursos específicos em seu ambiente, mas não necessariamente a toda a infraestrutura nem nenhum escopo relacionado à cobrança. O RBAC proporciona a flexibilidade de ter uma assinatura do Azure gerenciada pela conta de administrador (função de administrador de serviços no nível da assinatura) e ter vários usuários convidados para trabalhar na mesma assinatura, mas sem os direitos administrativos para ela.

> [!NOTE]
> As assinaturas do Office 365 ou licenças do Azure Active Directory (por exemplo: Acesso ao Azure Active Directory) provisionadas no centro do Office 365 Admin não se qualificam para uso do RBAC.

## <a name="assign-rbac-roles-at-the-subscription-scope"></a>Atribuir funções de RBAC no escopo de assinatura

Há dois exemplos comuns de momentos em que o RBAC é usado (entre outros):

* Quando há usuários externos às organizações (que não fazem parte do locatário do Azure Active Directory do usuário administrador) convidados para gerenciar determinados recursos ou a assinatura inteira
* Ao trabalhar com usuários de dentro a organização (que fazem parte do locatário do Azure Active Directory do usuário), mas que fazem parte de equipes ou grupos diferentes que precisam de acesso granular a toda a assinatura ou a determinados grupos de recursos ou escopos de recursos no ambiente

## <a name="grant-access-at-a-subscription-level-for-a-user-outside-of-azure-active-directory"></a>Conceder acesso no nível da assinatura para um usuário fora do Azure Active Directory

Funções RBAC podem ser concedidas somente pelos **Proprietários** da assinatura. Portanto, o administrador deve estar conectado como um usuário que tenha essa função previamente atribuída ou tenha criado a assinatura do Azure.

No Portal do Azure, depois de entrar como administrador, selecione "Assinaturas" e escolha a desejada.
![folha de assinatura no portal do Azure](./media/role-assignments-external-users/0.png) Por padrão, se o usuário administrador tiver comprado a assinatura do Azure, ele aparecerá como **Administrador da conta**, sendo essa a função da assinatura. Para obter mais informações sobre as funções de assinatura do Azure, confira [Adicionar ou alterar administradores de assinatura do Azure](../billing/billing-add-change-azure-subscription-administrator.md).

Neste exemplo, o usuário "alflanigan@outlook.com" é o **Proprietário** da assinatura de "Avaliação Gratuita" no locatário AAD definido como "Locatário padrão do Azure". Uma vez que esse usuário é o criador da assinatura do Azure com a Conta da Microsoft inicial "Outlook" (Conta da Microsoft = Outlook, Live etc.), o nome de domínio padrão para todos os outros usuários adicionados neste locatário será **"\@alflaniganuoutlook.onmicrosoft.com"**. Por design, a sintaxe do novo domínio é formada reunindo o nome de usuário e o nome de domínio do usuário que criou o locatário e adicionando a extensão **".onmicrosoft.com"**.
Além disso, os usuários podem entrar com um nome de domínio personalizado no locatário depois de o adicionarem e verificarem para o novo locatário. Para obter mais informações sobre como verificar um nome de domínio personalizado em um locatário do Azure Active Directory, consulte [Adicionar um nome de domínio personalizado ao seu diretório](/active-directory/active-directory-add-domain).

Neste exemplo, o diretório "Locatário padrão do Azure" contém somente usuários com o nome de domínio "\@alflanigan.onmicrosoft.com".

Depois de selecionar a assinatura, o usuário administrador deve clicar em **Controle de acesso (IAM)** e, em seguida, em **Adicionar uma nova função**.

![recurso IAM de controle de acesso no portal do Azure](./media/role-assignments-external-users/1.png)

![adicionar novo usuário no recurso IAM de controle de acesso no portal do Azure](./media/role-assignments-external-users/2.png)

A próxima etapa é selecionar a função a ser atribuída e o usuário ao qual a função de RBAC será atribuída. No menu suspenso **Função**, o usuário administrador vê apenas as funções RBAC internas que estão disponíveis no Azure. Para obter explicações mais detalhadas sobre cada função e seus escopos atribuíveis, consulte [Funções internas](built-in-roles.md).

Em seguida, o usuário administrador precisa adicionar o endereço de email do usuário externo. O comportamento esperado para o usuário externo é não aparecer no locatário existente. Depois de ser convidado, o usuário externo fica visível em **Assinaturas > Controle de Acesso (IAM)** com todos os usuários atuais que são atribuídos a uma função RBAC no escopo de Assinatura no momento.

![adicionar permissões à nova função RBAC](./media/role-assignments-external-users/3.png)

![lista de funções de RBAC no nível de assinatura](./media/role-assignments-external-users/4.png)

O usuário "chessercarlton@gmail.com" foi convidado a ser um **Proprietário** para a assinatura de "Avaliação Gratuita". Depois de enviar o convite, o usuário externo receberá um email de confirmação com um link de ativação.
![convite por email para a função RBAC](./media/role-assignments-external-users/5.png)

Sendo externo à organização, o novo usuário não tem nenhum atributo existente no diretório "Locatário padrão do Azure". Ele será criado depois que o usuário externo tiver autorizado a ser gravado no diretório associado à assinatura para a qual ele recebeu uma função.

![mensagem de convite de email para a função de RBAC](./media/role-assignments-external-users/6.png)

O usuário externo aparece no locatário do Azure Active Directory de agora em diante como usuário externo e isso pode ser exibido no portal do Azure.

![folha dos usuários no azure active-directory, portal do Azure](./media/role-assignments-external-users/7.png)

Na exibição **Usuários**, os usuários externos podem ser reconhecidos pelo tipo de ícone diferentes no portal do Azure.

No entanto, conceder acesso de **Proprietário** ou **Colaborador** a um usuário externo no escopo da **Assinatura** não permite acesso ao diretório do usuário administrador, a menos que o **Administrador Global** o permita. Nas propriedades do usuário, o **Tipo de Usuário** que tem dois parâmetros comuns, **Membro** e **Convidado** pode ser identificado. Um membro é um usuário registrado no diretório, enquanto um convidado é um usuário convidado para o diretório de uma fonte externa. Para obter mais informações, consulte [Como os administradores do Azure Active Directory adicionam usuários de colaboração B2B](../active-directory/active-directory-b2b-admin-add-users.md).

> [!NOTE]
> Certifique-se de que, depois de inserir as credenciais no portal, o usuário externo selecione o diretório correto para entrar. O mesmo usuário pode ter acesso a vários diretórios e pode selecionar qualquer um deles clicando no nome de usuário na parte superior direita no portal do Azure e escolhendo o diretório apropriado na lista suspensa.

Enquanto for um convidado no diretório, o usuário externo poderá gerenciar todos os recursos da assinatura do Azure, mas não poderá acessar o diretório.

![acesso restrito ao azure active-directory, portal do Azure](./media/role-assignments-external-users/9.png)

O Azure Active Directory e uma assinatura do Azure não têm uma relação de pai-filho como outros recursos do Azure (por exemplo: máquinas virtuais, redes virtuais, aplicativos Web, armazenamento etc.) têm com uma assinatura do Azure. Todos estes segundos são criados, gerenciados e cobrados em uma assinatura do Azure, enquanto uma assinatura do Azure é usada para gerenciar o acesso a um diretório do Azure. Para mais informações, consulte [Como uma assinatura do Azure está relacionada ao Microsoft Azure Active Directory](/active-directory/active-directory-how-subscriptions-associated-directory).

De todas as funções RBAC internas, **Proprietário** e **Colaborador** oferecem acesso de gerenciamento total a todos os recursos no ambiente, sendo a diferença que um Colaborador não pode criar nem excluir novas funções de RBAC. Outras funções internas, como **Colaborador de Máquina Virtual** oferecem acesso de gerenciamento completo apenas aos recursos indicados pelo nome, não importa o **Grupo de Recursos** em que eles estão sendo criados.

Atribuir a função de RBAC interna de **Colaborador de Máquina Virtual** em um nível de assinatura significa que o usuário atribuído à função:

* Pode exibir todas as máquinas virtuais, não importa a data de implantação nem os grupos de recursos dos quais fazem parte
* Tem acesso de gerenciamento completo para as máquinas virtuais na assinatura
* Não é possível exibir outros tipos de recursos na assinatura
* Não é possível operar nenhuma alteração de uma perspectiva de cobrança

## <a name="assign-a-built-in-rbac-role-to-an-external-user"></a>Atribuir uma função de RBAC interna a um usuário externo

Para um cenário diferente nesse teste, o usuário externo "alflanigan@gmail.com" é adicionado como um **Colaborador de Máquina Virtual**.

![função interna de colaborador de máquina virtual](./media/role-assignments-external-users/11.png)

O comportamento normal para esse usuário externo com essa função interna é ver e gerenciar somente máquinas virtuais e seus recursos do Resource Manager adjacentes necessários ao implantar. Por design, essas funções limitadas oferecem acesso apenas aos recursos correspondentes criado no portal do Azure.

![visão geral da função de colaborador de máquina virtual no portal do Azure](./media/role-assignments-external-users/12.png)

## <a name="grant-access-at-a-subscription-level-for-a-user-in-the-same-directory"></a>Conceder acesso no nível da assinatura para um usuário no mesmo diretório

O fluxo do processo é idêntico a adicionar um usuário externo, tanto da perspectiva do administrador que está concedendo a função RBAC quanto do usuário que está recebendo acesso à função. A diferença aqui é que o usuário convidado não receberá nenhum convite por email, pois todos os escopos de recursos dentro da assinatura estarão disponíveis no painel depois do logon.

## <a name="assign-rbac-roles-at-the-resource-group-scope"></a>Atribuir funções de RBAC no escopo do grupo de recursos

Atribuir uma função de RBAC em um escopo de **Grupo de Recursos** tem um processo idêntico ao de atribuir a função no nível de assinatura para os dois tipos de usuários, externos e internos (parte do mesmo diretório). Os usuários que recebem a função de RBAC devem ver em seu ambiente somente o grupo de recursos para o qual eles receberam acesso usando o ícone **Grupos de Recursos** no portal do Azure.

## <a name="assign-rbac-roles-at-the-resource-scope"></a>Atribuir funções de RBAC no escopo do recurso

Atribuir uma função de RBAC no escopo de um recurso no Azure tem um processo idêntico ao de atribuir uma função no nível de assinatura ou no nível do grupo de recursos, seguindo o mesmo fluxo de trabalho em ambos os cenários. Novamente, os usuários que recebem a função de RBAC podem ver apenas os itens aos quais eles receberam acesso, seja na guia **Todos os Recursos** ou diretamente no painel.

Um aspecto importante para RBAC tanto no escopo do grupo de recursos quanto no escopo do recurso é os usuários entrarem no diretório correto.

![logon do diretório no portal do Azure](./media/role-assignments-external-users/13.png)

## <a name="assign-rbac-roles-for-an-azure-active-directory-group"></a>Atribuir funções de RBAC a um grupo do Azure Active Directory

Todos os cenários que usam RBAC nos três escopos diferentes no Azure oferecem o privilégio de gerenciar, implantar e administrar vários recursos como um usuário atribuído sem a necessidade de gerenciar uma assinatura pessoal. Independentemente da função de RBAC atribuída a uma assinatura, grupo de recursos ou escopo de recurso, todos os recursos criados mais adiante pelos usuários atribuídos são cobrados sob a única assinatura do Azure à qual os usuários têm acesso. Dessa forma, os usuários que têm permissões de administrador de cobrança para toda essa assinatura do Azure têm uma visão geral completa do consumo, não importa quem está gerenciando os recursos.

Para organizações maiores, as funções de RBAC podem ser aplicadas da mesma maneira para grupos do Azure Active Directory considerando a perspectiva de que o usuário administrador deseja conceder acesso granular a equipes ou departamentos inteiros, e não individualmente a cada usuário, considerando, portanto, essa opção muito eficiente em termos de tempo e gerenciamento. Para ilustrar esse exemplo, a função de **Colaborador** foi adicionada a um dos grupos no locatário no nível da assinatura.

![adicionar função de RBAC a grupos do AAD](./media/role-assignments-external-users/14.png)

Esses grupos são grupos de segurança provisionados e gerenciados somente dentro do Azure Active Directory.

