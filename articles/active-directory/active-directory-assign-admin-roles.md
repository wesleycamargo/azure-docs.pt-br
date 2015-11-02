<properties
	pageTitle="Atribuindo funções de administrador no Azure AD"
	description="Explica quais funções administrativas estão disponíveis com o AD do Azure e como atribuí-las."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/19/2015"
	ms.author="curtand"/>

# Atribuindo funções de administrador no Azure AD

Dependendo do tamanho da sua empresa, convém designar vários administradores que sirvam diferentes funções. Esses administradores terão acesso a vários recursos no portal do Azure e, dependendo da sua função, poderão criar ou editar usuários, atribuir funções administrativas a outros usuários, redefinir senhas de usuários, gerenciar licenças de usuários e gerenciar domínios, entre outras coisas.

É importante entender que um usuário ao qual é atribuída uma função administrativa terá as mesmas permissões em todos os serviços de nuvem que sua organização tenha assinado, independentemente de você ter atribuído a função no portal do Office 365 ou no portal do Azure ou usando o módulo do Azure AD para Windows PowerShell.

As seguintes funções de administrador estão disponíveis:

- **Administrador de cobrança**: faz compras, gerencia as assinaturas, gerencia tíquetes de suporte e monitora a integridade do serviço.
- **Administrador global**: tem acesso a todos os recursos administrativos. A pessoa que se inscreve para a conta do Azure torna-se um administrador global. Somente os administradores globais podem atribuir outras funções de administrador. Pode haver mais de um administrador global na sua empresa.
- **Administrador de senha**: redefine as senhas, gerencia as solicitações de serviço e monitora a integridade do serviço. Administradores de senha podem redefinir senhas somente para os usuários e outros administradores de senha.
- **Administrador de serviço**: gerencia as solicitações de serviço e monitora a integridade do serviço.
    > [AZURE.NOTE]
    > Para atribuir a função de administrador de serviços a um usuário, o administrador global deve primeiro atribuir permissões administrativas para o usuário no serviço, como o Exchange Online, e, em seguida, atribuir a função de administrador de serviço para o usuário no Portal de Gerenciamento do Azure. 
- **Administrador de usuários**: redefine as senhas, monitora a integridade do serviço e gerencia contas de usuário, grupos de usuários e solicitações de serviço. Algumas limitações se aplicam às permissões de um administrador de gerenciamento de usuário. Por exemplo, eles não podem excluir um administrador global ou criar outros administradores. Além disso, eles não podem redefinir senhas para cobrança, globais e administradores de serviço.

## Permissões de administrador

### Administrador de cobrança

O que ele pode fazer | O que não pode fazer
------------- | -------------
<p>Exibir informações da empresa e do usuário</p><p>Gerenciar tíquetes de suporte do Office gerenciar</p><p>Executar operações de faturamento e compra para produtos do Office</p> | <p>Redefinir senhas de usuário</p><p>Criar e gerenciar modos de exibição do usuário</p><p>Criar, editar e excluir usuários e grupos e gerenciar licenças de usuário</p><p>Gerenciar domínios</p><p>Gerenciar informações da empresa</p><p>Delegar funções administrativas a outros</p><p>Usar a sincronização de diretório</p>

### Administrador global

O que ele pode fazer | O que não pode fazer
------------- | -------------
<p>Exibir informações da empresa e do usuário</p><p>Gerenciar tíquetes de suporte do Office</p><p>Executar operações de faturamento e compra para produtos do Office</p> <p>Redefinir senhas de usuário</p><p>Criar e gerenciar modos de exibição do usuário</p><p>Criar, editar e excluir usuários e grupos e gerenciar licenças de usuário</p><p>Gerenciar domínios</p><p>Gerenciar informações da empresa</p><p>Delegar funções administrativas a outros</p><p>Usar a sincronização de diretório</p><p>Habilitar ou desabilitar a autenticação multifator</p> | N/D

### Administrador de senha

O que ele pode fazer | O que não pode fazer
------------- | -------------
<p>Exibir informações da empresa e do usuário</p><p>Gerenciar tíquetes de suporte do Office</p><p>Redefinir senhas de usuário</p> | <p>Realizar operações de faturamento e compra para produtos do Office</p><p>Criar e gerenciar modos de exibição do usuário</p><p>Criar, editar e excluir usuários e grupos e gerenciar licenças de usuário</p><p>Gerenciar domínios</p><p>Gerenciar informações da empresa</p><p>Delegar funções administrativas a outros</p><p>Usar a sincronização de diretório</p>

### Administrador de serviço

O que ele pode fazer | O que não pode fazer
------------- | -------------
<p>Exibir informações da empresa e do usuário</p><p>Gerenciar tíquetes de suporte do Office</p> | <p>Redefinir senhas de usuários</p><p>Realizar operações de faturamento e compra para produtos do Office</p><p>Criar e gerenciar modos de exibição do usuário</p><p>Criar, editar e excluir usuários e grupos e gerenciar licenças de usuário</p><p>Gerenciar domínios</p><p>Gerenciar informações da empresa</p><p>Delegar funções administrativas a outros</p><p>Usar a sincronização de diretório</p>

### Administrador de usuários

O que ele pode fazer | O que não pode fazer
------------- | -------------
<p>Exibir informações da empresa e do usuário</p><p>Gerenciar tíquetes de suporte do Office</p><p>Redefinir senhas de usuário, com limitações. Eles não podem redefinir senhas para administradores de cobrança, globais e de serviço.</p><p>Criar e gerenciar modos de exibição do usuário</p><p>Criar, editar e excluir usuários e grupos e gerenciar licenças de usuário, com limitações. Eles não podem excluir um administrador global ou criar outros administradores.</p> | <p>Realizar operações de faturamento e de compra para produtos do Office</p><p>Gerenciar domínios</p><p>Gerenciar informações da empresa</p><p>Delegar funções administrativas a outros</p><p>Usar a sincronização de diretório</p><p>Habilitar ou desabilitar a autenticação multifator</p>

## Detalhes sobre a função de administrador global

O administrador global tem acesso a todos os recursos administrativos. Por padrão, a pessoa que se inscreve para uma assinatura do Azure recebe a função de administrador global para o diretório. Somente os administradores globais podem atribuir outras funções de administrador.

## Atribuir ou remover funções de administrador 


1. No Portal de Gerenciamento, clique em **Active Directory** e no nome do diretório da sua organização.
2. Na página **Usuários**, clique no nome de exibição do usuário que deseja editar.
3. Selecione o menu suspenso **Função Organizacional** e selecione a função de administrador que você deseja atribuir a este usuário ou selecione **Usuário** se você quiser remover uma função de administrador existente. 
4. No campo **Endereço de email alternativo**, digite um endereço de email. Este endereço de email é usado para notificações importantes, incluindo redefinição automática de senha, por isso, o usuário deve ser capaz de acessar a conta de email independentemente de poder acessar o Azure ou não.
5. Selecione **Permitir** ou **Bloquear** para especificar se deseja permitir que o usuário entre e acesse os serviços. 
6. Especifique um local na lista suspensa **Local de Uso**.
7. Ao terminar, clique em **Salvar**.

## O que vem a seguir?

- [Gerenciar usuários](../active-directory-manage-users.md)
- [Gerenciar senhas](active-directory-manage-passwords.md)
- [Gerenciar grupos](active-directory-manage-groups.md)

<!---HONumber=Oct15_HO4-->