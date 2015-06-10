<properties 
	pageTitle="Criar ou editar usuários no Azure AD" 
	description="Um tópico que explica como criar ou editar contas de usuário no Azure AD." 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/05/2015" 
	ms.author="Justinha"/>

# Criar ou editar usuários no Azure AD

Você precisa criar uma conta para cada usuário que acessará um serviço de nuvem da Microsoft. Você também pode alterar as contas de usuário ou excluí-las quando elas não forem mais necessárias. Por padrão, os usuários não têm permissões de administrador, mas, opcionalmente, você pode atribuí-las.

## Criar um usuário

1. Clique em **Active Directory** e no nome do diretório da sua organização.
2. Na página **Usuários**, clique em **Adicionar usuário**.
3. Na página **Conte-nos sobre este usuário**, para **Tipo de usuário**, selecione: 
	1. **Novo usuário na sua organização** – indica que você deseja que uma nova conta de usuário seja criada e gerenciada no diretório.
	2. **Usuário com uma conta Microsoft existente** – indica que você deseja adicionar uma conta existente da Microsoft no seu diretório para colaborar com os recursos do Azure com um co-administrador que acessa o Azure com uma conta da Microsoft.	
	3. **Usuário em outro diretório do Azure AD** – indica que você deseja adicionar uma conta de usuário ao seu diretório que foi originado de outro diretório do Azure AD. Você precisa ser um membro do outro diretório para selecionar um usuário nele. 
4. Dependendo da opção selecionada, digite um nome de usuário ou nome de conta da Microsoft com que esse usuário irá se conectar.
5. O na página **Perfil** do usuário, forneça o nome e sobrenome do usuário, um nome amigável e uma função de usuário no menu suspenso Funções. Para obter mais informações sobre funções de usuário e administrador, consulte [Atribuindo funções de administrador no Azure AD](active-directory-assign-admin-roles.md). Especifique se deseja **Habilitar a autenticação multifator**.
6. Na página **Obter senha temporária**, clique em **Criar**.

Se sua organização usa mais de um domínio, você deve saber sobre os seguintes problemas ao criar uma conta de usuário:

- Você pode criar contas de usuário com o mesmo UPN (nome de entidade de usuário) entre domínios se criar, primeiramente, por exemplo, geoffgrisso@contoso.onmicrosoft.com seguido por geoffgrisso@contoso.com.
- Não é possível criar geoffgrisso@contoso.com seguido por geoffgrisso@contoso.onmicrosoft.com.

## Editar um usuário

Se o usuário que você está tentando editar estiver sincronizado com o serviço do Active Directory local, será exibida uma mensagem de erro e não será possível editar o usuário usando esse procedimento. Para editar o usuário, use suas ferramentas de gerenciamento do Active Directory locais.
 
Para editar um usuário no Portal de Gerenciamento do Azure:

1. Clique em **Active Directory** e no nome do diretório da sua organização.
2. Na página **Usuários**, clique no nome de exibição do usuário que deseja editar.
3. Conclua suas alterações e, em seguida, clique em **Salvar**.

## Redefinir a senha de um usuário

1. Clique em **Active Directory** e no nome do diretório da sua organização.
2. Na página **Usuários**, clique no nome de exibição do usuário que deseja editar.
3. Na parte inferior do portal, clique em **Redefinir senha**.
4. Na caixa de diálogo de redefinição de senha, clique em **Redefinir**.
5. Clique na marca de seleção para confirmar que a senha foi redefinida.

## Criar um usuário externo

No Azure AD, você também pode adicionar usuários a um diretório do Azure AD a partir de outro diretório do Azure AD ou de um usuário com uma conta da Microsoft. Um usuário pode ser um membro de até 20 diretórios diferentes.

Os usuários que são adicionados de outro diretório são usuários externos. Os usuários externos podem colaborar com usuários que já existem em um diretório, como em um ambiente de teste, sem exigir que eles façam login com novas contas e credenciais. Usuários externos são autenticados por seu diretório base ao fazerem login, e essa autenticação funciona para todos os outros diretórios de que eles fazem parte.

Para criar um usuário externo, crie um usuário no portal e para **Tipo de usuário**, selecione **Usuário em outro diretório do Azure AD**.

## Limitações e gerenciamento de usuários externos

Quando você adiciona um usuário de um diretório em um novo diretório, esse usuário é um usuário externo no novo diretório. Inicialmente, o nome de exibição e o nome de usuário são copiados do "diretório base" do usuário e marcados no usuário externo no outro diretório. Daí em diante, essas e outras propriedades do objeto do usuário externo são completamente independentes: se você fizer uma alteração no usuário no diretório base, como alterar o nome do usuário, adicionar um cargo, etc., essas alterações não são propagadas para a conta do usuário externo no outro diretório.

A única ligação entre os dois objetos é que o usuário sempre se autentica no diretório base ou com sua conta da Microsoft. É por isso que você não vê uma opção para redefinir a senha ou habilitar a autenticação multifator para uma conta de usuário externo: atualmente, a política de autenticação do diretório base ou da conta da Microsoft é a única que é avaliada quando o usuário faz logon.

> [AZURE.NOTE]Você ainda pode desabilitar o usuário externo no diretório e isso bloqueará o acesso ao seu diretório.

Se um usuário for excluído em seu diretório base ou cancelar a sua conta da Microsoft, o usuário externo ainda existirá no diretório. No entanto, o usuário não pode acessar recursos no diretório uma vez que ele não poderá mais se autenticar no seu diretório base ou conta da Microsoft.

Um usuário que seja um administrador de vários diretórios pode gerenciar cada um desses diretórios no portal de gerenciamento do Azure. No entanto, outros aplicativos, como o Office 365 não fornecem atualmente experiências para atribuir e acessar os serviços como um usuário externo em outro diretório. No futuro, forneceremos orientações para desenvolvedores sobre como seus aplicativos podem funcionar com usuários que sejam membros de vários diretórios.

Atualmente, há limitações no sentido de que um administrador pode conceder permissão apenas a um aplicativo multilocatário no seu diretório base e só pode ser provisionado para aplicativos SaaS e SSO por meio do painel de acesso no seu diretório base. Usuários de contas da Microsoft têm as mesmas limitações no sentido de que atualmente eles não podem conceder permissão para um aplicativo multilocatário ou usar o painel de acesso.

## Convidados

Um **convidado** é um usuário em seu diretório que tem um tipo de usuário definido como "Convidado". Usuários regulares têm um tipo de usuário de "Membros" para indicar que eles são membros do diretório. Convidados são criados quando você compartilha um recurso com alguém externo ao seu diretório, por exemplo, quando você adiciona uma conta da Microsoft em sua assinatura do Azure ou compartilha um documento no SharePoint com um usuário externo.

Os convidados têm um conjunto limitado de direitos no diretório. Esses direitos limitam a capacidade dos Convidados de descobrir informações sobre outros usuários no diretório, ao mesmo tempo em que podem interagir com os usuários e grupos associados com os recursos em que estão trabalhando. Por exemplo, um Convidado atribuído a uma assinatura do Azure poderá ver outros usuários e grupos associados à assinatura do Azure. Eles também podem localizar outros usuários no diretório que devem receber acesso à assinatura, contanto que saibam o endereço de email completo do usuário. Um convidado só pode ver um conjunto limitado de propriedades de outros usuários. Essas propriedades são limitadas ao nome de exibição, endereço de email, UPN (nome de entidade de usuário) e miniatura da foto.

## Configurar políticas de acesso de usuário

A guia **Configurar** do diretório inclui opções para controlar o acesso para usuários externos. Essas opções podem ser alteradas apenas na interface do usuário (não há nenhum método de API do Windows PowerShell) no portal do Azure completo por um administrador global do diretório. Para abrir a guia **Configurar** no portal do Azure, clique em **Active Directory** e no nome do diretório.

![][1]

Então você pode editar as opções para controlar o acesso para os usuários externos.

![][2]

Por padrão, os convidados não podem enumerar o conteúdo do diretório, assim eles não podem ver nenhum usuário ou grupo na **Lista de membros**. Eles podem pesquisar por um usuário digitando o endereço de email completo do usuário e depois conceder acesso. O conjunto de restrições padrão para os convidados são:

- Eles não podem enumerar os usuários e grupos no diretório.
- Eles podem ver detalhes limitados de um usuário se sabem o endereço de email do usuário.
- Eles podem ver detalhes limitados de um grupo quando eles sabem o nome do grupo.

A capacidade dos convidados verem detalhes limitados de um usuário ou grupo permite que eles convidem outras pessoas e vejam alguns detalhes das pessoas com quem eles estão colaborando.

## O que vem a seguir?

- [Administrando o Azure AD](active-directory-administer.md)
- [Gerenciar senhas no Azure AD](active-directory-manage-passwords.md)
- [Gerenciar grupos no Azure AD](active-directory-manage-groups.md)

<!--Image references-->
[1]: ./media/active-directory-create-users/RBACDirConfigTab.png
[2]: ./media/active-directory-create-users/RBACGuestAccessControls.png

<!---HONumber=58-->