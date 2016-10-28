<properties
   pageTitle="Acesso ao painel do portal do Azure | Microsoft Azure"
   description="Este artigo explica como compartilhar o acesso a um painel no portal do Azure."
   services="azure-portal"
   documentationCenter=""
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="multiple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="08/01/2016"
   ms.author="tomfitz"/>

# Compartilhando painéis do Azure

Depois de configurar um painel, você pode publicá-lo e compartilhá-lo com outros usuários na sua organização. Permitir que outras pessoas acessem seu painel usando o [Controle de acesso baseado em função](../active-directory/role-based-access-control-configure.md) do Azure. Atribua um usuário ou grupo de usuários a uma função e essa função define se os usuários podem exibir ou modificar o painel publicado.

Todos os painéis publicados são implementados como recursos do Azure, o que significa que eles existem como itens gerenciáveis dentro de sua assinatura e estão contidos em um grupo de recursos. Do ponto de vista do controle de acesso, os painéis não são diferentes de outros recursos, como uma máquina virtual ou uma conta de armazenamento.

> [AZURE.TIP] Blocos individuais no painel impõem seus próprios requisitos de controle de acesso com base nos recursos que exibem. Portanto, você pode criar um painel que é compartilhado amplamente e ainda proteger os dados em blocos individuais.

## Noções básicas de controle de acesso de painéis

Com o controle de acesso baseado em função, você pode atribuir usuários a funções em três níveis de escopo diferentes:

- assinatura
- grupo de recursos
- recurso

As permissões atribuídas são herdadas da assinatura até o recurso. O painel publicado é um recurso. Portanto, você já pode ter os usuários atribuídos a funções da assinatura que também funcionam para o painel publicado.

Aqui está um exemplo. Digamos que você tenha uma assinatura do Azure e vários membros de sua equipe receberam as funções de **proprietário**, **colaborador** ou **leitor** da assinatura. Os usuários que são proprietários ou colaboradores podem listar, exibir, criar, modificar ou excluir painéis na assinatura. Os usuários que são os leitores podem listar e exibir os painéis, mas não podem modificá-los ou excluí-los. Os usuários com acesso de leitor podem fazer edições locais em um painel publicado (por exemplo, ao solucionar um problema), mas não podem publicar essas alterações no servidor. Eles terão a opção de fazer uma cópia privada do painel para uso pessoal.

No entanto, você também pode atribuir permissões ao grupo de recursos que contém vários painéis ou a um painel individual. Por exemplo, você pode decidir que um grupo de usuários deve ter permissões limitadas na assinatura, mas um maior acesso a um painel específico. Você pode atribuir os usuários a uma função desse painel.

## Publicar painel

Vamos supor que você concluiu a configuração de um painel que deseja compartilhar com um grupo de usuários em sua assinatura. As etapas a seguir descrevem um grupo personalizado chamado Gerentes de Armazenamento, mas você pode dar o nome que quiser ao grupo. Para obter informações sobre como criar um grupo do Active Directory e adicionar usuários a ele, confira [Gerenciar grupos no Azure Active Directory](../active-directory/active-directory-accessmanagement-manage-groups.md).

1. No painel, selecione **Compartilhar**.

     ![selecionar compartilhamento](./media/azure-portal-dashboard-share-access/select-share.png)

2. Antes de atribuir acesso, você deve publicar o painel. Por padrão, o painel será publicado em um grupo de recursos denominado **painéis**. Selecione **Publicar**.

     ![publish](./media/azure-portal-dashboard-share-access/publish.png)

Seu painel agora foi publicado. Se as permissões herdadas da assinatura são adequadas, você não precisa fazer mais nada. Outros usuários na sua organização poderão acessar e modificar o painel com base em suas funções no nível da assinatura. No entanto, para este tutorial, vamos atribuir um grupo de usuários a uma função desse painel.

## Atribuir acesso a um painel

1. Depois de publicar o painel, selecione **Gerenciar usuários**.

     ![gerenciar usuários](./media/azure-portal-dashboard-share-access/manage-users.png)

2. Você verá uma lista de usuários existentes que já estão atribuídos a uma função desse painel. A lista de usuários existentes será diferente da imagem abaixo. É provável que as atribuições sejam herdadas da assinatura. Para adicionar um novo usuário ou grupo, selecione **Adicionar**.

     ![adicionar usuário](./media/azure-portal-dashboard-share-access/existing-users.png)

3. Selecione a função que representa as permissões que você deseja conceder. Neste exemplo, selecione **Colaborador**.

     ![escolher função](./media/azure-portal-dashboard-share-access/select-role.png)

4. Selecione o usuário ou grupo que você deseja atribuir à função. Se você não vir o usuário ou grupo que está procurando na lista, use a caixa de pesquisa. A lista de grupos disponíveis dependerá dos grupos que você criou no Active Directory.

     ![selecionar usuário](./media/azure-portal-dashboard-share-access/select-user.png)

5. Quando você terminar de adicionar usuários ou grupos, selecione **OK**.

6. A nova atribuição é adicionada à lista de usuários. Observe que seu **Acesso** é listado como **Atribuído** em vez de **Herdado**.

     ![funções atribuídas](./media/azure-portal-dashboard-share-access/assigned-roles.png)

## Próximas etapas

- Para obter uma lista de funções, confira [RBAC: funções internas](../active-directory/role-based-access-built-in-roles.md).
- Para saber sobre como gerenciar recursos, consulte [Gerenciar recursos do Azure pelo portal](resource-group-portal.md).

<!---HONumber=AcomDC_0803_2016-->