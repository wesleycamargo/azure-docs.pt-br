<properties
	pageTitle="Criar ou editar usuários no Active Directory do Azure | Microsoft Azure"
	description="Um tópico que explica como criar ou editar contas de usuário no Active Directory do Azure."
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
	ms.date="03/03/2016"
	ms.author="curtand;viviali"/>

# Criar ou editar usuários no Azure AD

Você deve adicionar uma conta ao diretório de locatário para cada usuário que acessará um serviço de nuvem da Microsoft. Você também pode alterar as contas de usuário ou excluí-las, quando elas não forem mais necessárias. Por padrão, os usuários não têm permissões de administrador, mas você pode atribuí-las.

## Criar um usuário

1. Clique em **Active Directory** e selecione o nome do diretório da sua organização.
2. Na página **Usuários**, clique em **Adicionar usuário**.
3. Na página **Conte-nos sobre este usuário**, para **Tipo de usuário**, selecione:

	- **Novo usuário na sua organização** – para criar uma nova conta de usuário em seu diretório
	- **Usuário com uma conta da Microsoft existente** – para adicionar uma conta de consumidor da Microsoft existente ao seu diretório (isto é, uma conta do outlook)
	- **Usuário em outro diretório do AD do Azure** – para adicionar uma conta de usuário ao seu diretório originado de outro diretório do AD do Azure (observação: você precisará ser membro do outro diretório para selecionar um usuário nele).
	- **Usuários em empresas parceiras** - para convidar e autorizar os usuários da empresa parceira para o diretório ([veja Colaboração B2B do Active Directory do Azure](active-directory-b2b-what-is-azure-ad-b2b.md))


4. Dependendo da opção selecionada, insira um nome de usuário, um endereço de email ou carregue um arquivo CSV especificando os endereços de email com os quais os usuários entrarão.
5. O na página **Perfil** do usuário, forneça o nome e sobrenome do usuário, um nome amigável e uma função de usuário no menu suspenso Funções. Para obter mais informações sobre funções de usuário e administrador, consulte [Atribuindo funções de administrador no Azure AD](active-directory-assign-admin-roles.md). Especifique se deseja **Habilitar a autenticação multifator**.
6. Na página **Obter senha temporária**, clique em **Criar**.

Se sua organização usa mais de um domínio, você deve saber sobre os seguintes problemas ao criar uma conta de usuário:

- Você pode criar contas de usuário com o mesmo UPN (nome de entidade de usuário) entre domínios se criar, primeiramente, por exemplo, geoffgrisso@contoso.onmicrosoft.com seguido por geoffgrisso@contoso.com.
- Não é possível criar geoffgrisso@contoso.com seguido por geoffgrisso@contoso.onmicrosoft.com.

## Editar um usuário

Para editar um usuário no portal clássico do Azure:

1. Clique em **Active Directory** e no nome do diretório da sua organização.
2. Na página **Usuários**, clique no nome de exibição do usuário que deseja editar.
3. Conclua suas alterações e, em seguida, clique em **Salvar**.

Se o usuário que você está tentando editar estiver sincronizado com o serviço do Active Directory local, será exibida uma mensagem de erro e não será possível editar o usuário usando esse procedimento. Para editar o usuário, use suas ferramentas de gerenciamento do Active Directory locais.

## Redefinir a senha de um usuário

1. Clique em **Active Directory** e no nome do diretório da sua organização.
2. Na página **Usuários**, clique no nome de exibição do usuário que deseja editar.
3. Na parte inferior do portal, clique em **Redefinir senha**.
4. Na caixa de diálogo de redefinição de senha, clique em **Redefinir**.
5. Clique na marca de seleção para confirmar que a senha foi redefinida.

## Criar usuários externos

No Azure AD, você também pode adicionar usuários a um diretório do Azure AD com uma conta da Microsoft de outro diretório do Azure AD à qual você pertença ou de empresas parceiras, carregando um arquivo CSV. Para criar um usuário externo, adicione um usuário no portal e, como **Tipo de usuário**, selecione **Usuário em outro diretório do Azure AD** ou **Usuários em empresas parceiras**.

Os usuários ou os tipos de usuários são originados de outro diretório e criados como **usuários externos**. Os usuários externos podem colaborar com os usuários que já existem em um diretório usando sua conta única, sem a necessidade de criar novas contas e credenciais. Os usuários externos serão autenticados por seu diretório base quando entrarem e a autenticação funcionará para quaisquer outros diretórios aos quais eles forem adicionados.

## Limitações e gerenciamento de usuários externos

Quando você adiciona um usuário de outro diretório ao seu diretório, esse usuário será um usuário externo no diretório. Inicialmente, o nome de exibição e o nome de usuário são copiados do "diretório base" do usuário e marcados no usuário externo no seu diretório. Daí em diante, essas e outras propriedades da conta do usuário externo são completamente independentes: se houver uma alteração no usuário no diretório base, como alterar o nome do usuário, adicionar um cargo etc., essas alterações não serão propagadas para a conta do usuário externo no seu diretório.

A única ligação entre as duas contas é que o usuário sempre se autentica no diretório base ou com a conta da Microsoft dele. É por isso que você não vê uma opção para redefinir a senha ou habilitar a Multi-Factor Authentication para um usuário externo: atualmente, a política de autenticação do diretório base ou da conta da Microsoft é a única que é avaliada quando o usuário faz logon.

> [AZURE.NOTE]
Você ainda pode desabilitar o usuário externo no diretório e isso bloqueará o acesso ao seu diretório.

Se um usuário for excluído em seu diretório base ou cancelar a sua conta da Microsoft, o usuário externo ainda existirá no seu diretório. No entanto, o usuário não poderá acessar recursos no diretório, pois não poderá mais se autenticar no seu diretório base ou conta da Microsoft.

Estes são os serviços que oferecem suporte ao acesso por usuários externos do AD do Azure:

- **Portal clássico do Azure**: permite que um usuário que seja um administrador de vários diretórios possa gerenciar cada um desses diretórios.
- **SharePoint Online**: permite que um usuário externo acesse recursos autorizados do SharePoint Online se o compartilhamento externo estiver habilitado
- **Dynamics CRM**: permite que um usuário externo acesse recursos autorizados no Dynamics CRM se o usuário for licenciado por meio do PowerShell

Estas são as limitações conhecidas dos usuários externos do AD do Azure:

- Os usuários externos que são administradores não podem adicionar usuários de empresas parceiras a diretórios (colaboração B2B) fora de seu diretório base
- Os usuários externos não podem consentir aplicativos multilocatários em diretórios fora de seu diretório base
- No momento, o Visual Studio Online não oferece suporte ao acesso de usuários externos*
- No momento, o PowerBI não oferece suporte ao acesso de usuários externos
- O Portal do Office não oferece suporte ao licenciamento de usuários externos

* O Visual Studio Online permite acesso por usuários externos que se autenticam usando contas da Microsoft, mas não aos usuários externos que se autenticam usando contas do trabalho ou da escola.

## Limitações e gerenciamento de usuário convidado

Um **Convidado** é uma conta de usuário no diretório que tem o atributo UserType definido como "Convidado". Os usuários regulares têm um atributo UserType de "Membro" para indicar que são membros do diretório. Os Convidados representam usuários de outros diretórios que foram convidados ao seu diretório para acessar um recurso específico, como um documento do SharePoint Online, um aplicativo ou um recurso do Azure.

Os convidados têm um conjunto limitado de direitos no diretório. Esses direitos limitam a capacidade dos Convidados de descobrir informações sobre outros usuários no diretório, ao mesmo tempo em que podem interagir com os usuários e grupos associados com os recursos em que estão trabalhando. Os usuários convidados podem:

- ver outros usuários e grupos associados a uma assinatura do Azure a que estejam atribuídos
- ver os membros dos grupos aos quais pertencem
- pesquisar outros usuários no diretório desde que saibam o endereço de email completo do usuário
- ver apenas um conjunto limitado de atributos dos usuários pesquisados - limitados ao nome de exibição, o endereço de email, o nome UPN e a foto em miniatura
- obter uma lista dos domínios verificados no locatário
- consentir o acesso a aplicativos, concedendo a eles o mesmo acesso que os Membros têm em seu diretório

## Configurar políticas de acesso de usuário

A guia **Configurar** do diretório inclui opções para controlar o acesso para usuários externos. Essas opções só podem ser alteradas por um administrador global do diretório na interface do usuário (não há nenhum método do Windows PowerShell ou de API) do portal clássico do Azure. Para abrir a guia **Configurar** no portal clássico do Azure, clique em **Active Directory** e no nome do diretório.

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

<!---HONumber=AcomDC_0309_2016-->