<properties
	pageTitle="Adicionar usuários ou alterar as informações de usuário no Azure Active Directory | Microsoft Azure"
	description="Explica como Adicionar usuários ou alterar as informações de usuário no Azure Active Directory, incluindo usuários externos e convidados."
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
	ms.topic="get-started-article"
	ms.date="03/31/2016"
	ms.author="curtand;viviali"/>

# Adicionar ou alterar usuários no Azure Active Directory

Adicione uma conta ao diretório do locatário para todos os usuários que acessem um serviço de nuvem da Microsoft. Os usuários adicionados não têm permissões de administrador, mas você pode atribuir funções a eles a qualquer momento.

## Adicionar um usuário

1. Entre no [portal clássico do Azure](https://manage.windowsazure.com) com uma conta que seja um administrador global para o diretório.
2. Selecione **Active Directory** e selecione o nome do diretório da sua organização.
3. Selecione a guia **Usuários** e, na barra de comandos, selecione **Adicionar Usuário**.
4. Na página **Conte-nos sobre este usuário**, em **Tipo de usuário**, selecione:

	- **Novo usuário em sua organização** – adiciona uma nova conta de usuário a seu diretório
	- **Usuário com uma conta da Microsoft existente** – adiciona uma conta de consumidor da Microsoft existente a seu diretório (por exemplo, uma conta do Outlook)
	- **Usuário em outro diretório do AD do Azure** – adiciona uma conta de usuário a seu diretório originado de outro diretório do Azure AD. Você só poderá selecionar um usuário em outro diretório se também for membro desse diretório.
	- **Usuários em empresas parceiras** - para convidar e autorizar os usuários da empresa parceira para o diretório ([veja Colaboração B2B do Azure Active Directory](active-directory-b2b-what-is-azure-ad-b2b.md))


5. Dependendo do **Tipo de usuário**, insira um nome de usuário, um endereço de email ou carregue um arquivo CSV que especifique endereços de email.
6. Na página **Perfil** do usuário, forneça um nome e um sobrenome, um nome amigável e uma função de usuário da lista **Funções**. Para obter mais informações sobre funções de usuário e administrador, consulte [Atribuindo funções de administrador no Azure AD](active-directory-assign-admin-roles.md). Especifique se deseja **Habilitar a autenticação multifator**.
7. Na página **Obter senha temporária**, selecione **Criar**.

> [AZURE.IMPORTANT] Se sua organização usa mais de um domínio, você deve saber sobre os seguintes problemas ao adicionar uma conta de usuário:
>
> - PARA adicionar contas de usuário com o mesmo nome UPN entre domínios **primeiro** adicione, por exemplo, geoffgrisso@contoso.onmicrosoft.com, **seguido de** geoffgrisso@contoso.com.
> - **Não** adicione geoffgrisso@contoso.com antes de adicionar geoffgrisso@contoso.onmicrosoft.com. Essa ordem é extremamente importante e pode ser inconveniente para desfazer.

## Alterar as informações do usuário

Você pode alterar qualquer atributo de usuário, exceto a ID de objeto.

1. Abra seu diretório.
2. Selecione a guia **Usuários** e selecione o nome de exibição do usuário que você deseja alterar.
3. Conclua suas alterações e, em seguida, clique em **Salvar**.

Se o usuário que você está alterando estiver sincronizado com seu serviço do Active Directory local, não poderá alterar as informações do usuário usando este procedimento. Para alterar o usuário, use suas ferramentas de gerenciamento do Active Directory local.

## Redefinir a senha de um usuário

1. Abra seu diretório.
2. Selecione a guia **Usuários** e selecione o nome de exibição do usuário que você deseja alterar.
3. Na barra de comandos, selecione **Redefinir Senha**.
4. Na caixa de diálogo de redefinição de senha, clique em **Redefinir**.
5. Selecione a marca de seleção para concluir a redefinição da senha.

## Adicionar usuários externos

Você também pode adicionar usuários de outro diretório do AD do Azure ao qual você pertence, ou de empresas parceiras, ao carregar um arquivo CSV. Para adicionar um usuário externo, em **Tipo de Usuário**, especifique **Usuário em outro diretório do Microsoft Azure AD** ou **Usuários em empresas parceiras**.

Os usuários de ambos os tipos são originados de outro diretório e adicionados como **usuários externos**. Os usuários externos podem colaborar com outros usuários em um diretório sem qualquer requisito de adicionar novas contas e credenciais. Os usuários externos autenticados em seu diretório base quando entrarem e a autenticação funcionará para quaisquer outros diretórios aos quais eles forem adicionados.

## Limitações e gerenciamento de usuários externos

Quando você adiciona um usuário de outro diretório ao seu diretório, esse usuário será um usuário externo no diretório. O nome de exibição e o nome de usuário são copiados do diretório base do usuário e usados para o usuário externo no seu diretório. Daí em seguida diante, as propriedades da conta de usuário externo serão completamente independentes. Se forem feitas alterações de propriedade para o usuário no seu diretório base, essas alterações não serão propagadas para a conta de usuário externo no diretório.

O único vínculo entre as duas contas é que o usuário sempre se autentica no diretório base ou com a conta da Microsoft dele. É por isso que você não vê uma opção para redefinir a senha ou habilitar a autenticação multifator para um usuário externo. Atualmente, a política de autenticação do diretório base ou da conta da Microsoft é a única que é avaliada quando o usuário entra.

> [AZURE.NOTE]
Você ainda pode desabilitar o usuário externo no diretório, o que bloqueará o acesso ao seu diretório.

Se um usuário for excluído em seu diretório base ou cancelar a sua conta da Microsoft, o usuário externo ainda existirá no seu diretório. No entanto, o usuário em seu diretório não pode acessar recursos porque ele não pode ser autenticado com um diretório base ou uma conta da Microsoft.

### Serviços que oferecem suporte ao acesso por usuários externos do AD do Azure:

- **Portal clássico do Azure**: permite que um usuário que é administrador de vários diretórios gerencie cada um desses diretórios.
- **SharePoint Online**: se o compartilhamento externo estiver habilitado, permite que um usuário externo acesse recursos autorizados do SharePoint Online.
- **Dynamics CRM**: se o usuário for licenciado por meio do PowerShell, permite que um usuário externo acesse recursos autorizados no Dynamics CRM.
- **Dynamics AX**: se o usuário for licenciado por meio do PowerShell, permite que um usuário externo acesse recursos autorizados no Dynamics AX. As limitações de [usuários externos do Azure AD](#known-limitations-of-azure-ad-external-users) e de [Usuários convidados](#guest-user-management-and-limitations) também se aplicam a usuários externos no Dynamics AX.

### Limitações conhecidas dos usuários externos do AD do Azure:

- Os usuários externos que são administradores não podem adicionar usuários de empresas parceiras a diretórios (colaboração B2B) fora de seu diretório base
- Os usuários externos não podem consentir aplicativos multilocatários em diretórios fora de seu diretório base
- No momento, o PowerBI não dá suporte ao acesso por usuários externos
- O Portal do Office não dá suporte ao licenciamento de usuários externos

## Limitações e gerenciamento de usuário convidado

Contas de convidados são usuários de outros diretórios que foram convidados a seu diretório para acessar documentos do SharePoint, aplicativos ou outros recursos do Azure. Uma conta de convidado no diretório que tem o atributo UserType subjacente definido como "Convidado". Usuários normais (especificamente, membros de seu diretório) têm o atributo UserType "Membro".

Os convidados têm um conjunto limitado de direitos no diretório. Esses direitos limitam a capacidade dos Convidados para descobrir informações sobre outros usuários no diretório. No entanto, os usuários convidados ainda podem interagir com os usuários e os grupos associados aos recursos em que estão trabalhando. Os usuários convidados podem:

- Ver outros usuários e grupos associados a uma assinatura do Azure à qual estão atribuídos
- Ver os membros de grupos aos quais eles pertencem
- Pesquisar outros usuários no diretório, desde que saibam o endereço de email completo do usuário
- ver apenas um conjunto limitado de atributos dos usuários pesquisados - limitados ao nome de exibição, o endereço de email, o nome UPN e a foto em miniatura
- Obter uma lista dos domínios verificados no diretório do locatário
- consentir o acesso a aplicativos, concedendo a eles o mesmo acesso que os Membros têm em seu diretório

## Definir políticas de acesso de usuário

A guia **Configurar** do diretório inclui opções para controlar o acesso para usuários externos. Essas opções só podem ser alteradas no portal clássico do Azure por um administrador global. Atualmente, não existe um método de API ou do PowerShell.

Para abrir a guia **Configurar** no portal clássico do Azure, selecione **Active Directory** e selecione o nome do diretório.

![Configurar a guia no Azure Active Directory][1]

Então você pode editar as opções para controlar o acesso para os usuários externos.

![][2]


## O que vem a seguir

- [Administrando o Azure AD](active-directory-administer.md)
- [Gerenciar senhas no Azure AD](active-directory-manage-passwords.md)
- [Gerenciar grupos no Azure AD](active-directory-manage-groups.md)

<!--Image references-->
[1]: ./media/active-directory-create-users/RBACDirConfigTab.png
[2]: ./media/active-directory-create-users/RBACGuestAccessControls.png

<!---HONumber=AcomDC_0413_2016-->