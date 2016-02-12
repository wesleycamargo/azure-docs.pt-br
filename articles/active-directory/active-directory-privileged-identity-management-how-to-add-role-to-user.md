<properties
   pageTitle="Privileged Identity Management do Azure: Como Começar a Adicionar uma Função a um Usuário"
   description="Aprenda a adicionar funções a identidades com privilégios com a extensão Privileged Identity Management do Azure."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/21/2016"
   ms.author="kgremban"/>

# Privileged Identity Management do Azure: como adicionar ou remover uma função de usuário

## Adicionando ou removendo uma função de usuário
Há várias maneiras de navegar até a folha **Adicionar usuários gerenciados** da interface PIM. A sequência de cliques para cada uma é listada abaixo:

- Painel > Usuários em Funções Administrativas > Adicionar ou Remover
- Painel > Resumo das Funções > Lista de Todos os Usuários > Adicionar ou Remover
- Painel > clique na função de usuário na tabela de funções (por exemplo, Administrador Global) > Adicionar ou Remover

## Adicionar um usuário a uma função
Depois de navegar até a folha **Adicionar usuários gerenciados**:

1. Clique em **Selecionar uma função**. Se você navegou até aqui clicando em uma função de usuário na tabela de funções, a função já estará selecionada.
2. Selecione uma função na lista de funções. Por exemplo, **Administrador de senha**, a folha **Selecionar usuários** será aberta.
3. Insira o nome do usuário no campo de pesquisa. Se o usuário estiver no diretório, suas contas aparecerão como você está digitando, assim como ocorrerá com outros usuários com nomes semelhantes.
4. Selecione o usuário na lista e clique em **Concluído**.
5. Clique em **OK** para salvar sua seleção. O usuário que você selecionou aparecerá na lista e a função será temporária.
6. Se você quiser tornar a função permanente, clique no usuário na lista. As informações do usuário serão exibidas em uma nova folha. Selecione **tornar perm.** no menu de informações do usuário.
7. Clique em **Ativar** para iniciar uma solicitação para ativar essa função para o usuário. Insira o motivo para a ativação no campo de texto **Motivo da solicitação**. Nesse momento, a função será ativada automaticamente para esse usuário e uma notificação será enviada para os administradores globais.

## Remover um usuário de uma função
1. Navegue até o usuário na lista de funções de usuário usando um dos caminhos descritos acima.
2. Clique no usuário na lista de usuários.
3. Clique em **Remover**. Será exibida para você uma mensagem de confirmação.
4. Clique em **Sim** para remover a função do usuário.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0204_2016-->