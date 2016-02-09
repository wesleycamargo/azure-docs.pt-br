<properties
	pageTitle="Acesso a aplicativo de autoatendimento e gerenciamento delegado com o Active Directory do Azure| Microsoft Azure"
	description="Este artigo descreve como habilitar acesso a aplicativo de autoatendimento e gerenciamento delegado com o Active Directory do Azure."
	services="active-directory"
	documentationCenter=""
	authors="asmalser-msft"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/14/2015"
	ms.author="asmalser"/>

#Acesso a aplicativos de autoatendimento e gerenciamento delegado com o Active Directory do Azure

A habilitação de recursos de autoatendimento para usuários finais é um cenário comum para TI empresarial. Muitos usuários, muitos aplicativos e a pessoa mais bem informada para tomar decisões de concessão de acesso podem não ser o administrador do diretório. Geralmente, a melhor pessoa a decidir quem pode acessar um aplicativo é um líder de equipe ou outro administrador delegado. Mas, afinal, ele é o usuário que usa o aplicativo e o usuário sabe o que precisa para fazer seu trabalho.

O acesso a aplicativo de autoatendimento é um recurso do [Azure Active Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/) que permite aos administradores do diretório:

* Habilitar usuários para solicitar acesso a aplicativos, usando um bloco "Obter mais aplicativos" no [Painel de acesso do AD do Azure](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)
* Definir para quais aplicativos os usuários podem solicitar acesso
* Definir se uma aprovação é ou não necessária para os usuários atribuírem acesso próprio a um aplicativo
* Definir quem deve aprovar as solicitações e gerenciar o acesso a cada aplicativo

Atualmente, essa funcionalidade tem suporte para todos os aplicativos pré-integrados e personalizados que oferecem suporte a logon único federado ou com base em senha na [Galeria de aplicativos do Active Directory do Azure](https://azure.microsoft.com/marketplace/active-directory/all/), incluindo aplicativos como Salesforce, Dropbox, Google e muito mais. Este artigo descreve como:

* Configurar acesso de aplicativo de autoatendimento para usuários finais, incluindo a configuração de um fluxo de trabalho de aprovação opcional 
* Delegar gerenciamento de acesso a aplicativos específicos às pessoas mais apropriados na organização e habilitá-las a usar o painel de acesso do AD do Azure para aprovar solicitações de acesso, atribuir diretamente o acesso a usuários selecionados ou, (opcionalmente), definir credenciais para acesso ao aplicativo quando o logon único baseado em senha estiver configurado


##Configurando acesso ao aplicativo de autoatendimento

Para habilitar o acesso do aplicativo de autoatendimento e configurar quais aplicativos podem ser adicionados ou solicitados pelos usuários finais, siga as instruções abaixo.

**1:** entre no [portal clássico do Azure](https://manage.windowsazure.com/).

**2:** na seção **Active Directory**, selecione o diretório e selecione a guia **Aplicativos**.

**3:** selecione o botão **Adicionar** e use a opção da galeria para selecionar e adicionar um aplicativo.

**4:** depois que seu aplicativo tiver sido adicionado, você obterá a página Início Rápido do aplicativo. Clique em **Configurar logon único**, selecione o modo de logon desejado e salve a configuração.

**5:** em seguida, selecione a guia **Configurar**. Para permitir que os usuários solicitem acesso a esse aplicativo no painel de acesso do AD do Azure, defina **Permitir acesso ao aplicativo de autoatendimento** como **Sim**.

![][1]

**6:** para configurar opcionalmente um fluxo de trabalho de aprovação para solicitações de acesso, defina **Exigir aprovação antes de conceder acesso** como **Sim**. Em seguida, um ou mais aprovadores podem ser selecionados, usando o botão **Aprovadores**.

O aprovador pode ser qualquer usuário na organização, com uma conta do AD do Azure e ser responsável por provisionamento de conta, licenciamento ou qualquer outro processo de negócios que sua organização exigir antes de conceder acesso a um aplicativo. O aprovador pode até mesmo ser o proprietário do grupo de um ou mais grupos de conta compartilhados e pode atribuir usuários a um desses grupos para fornecer acesso por meio de uma conta compartilhada.

Se nenhuma aprovação for necessária, os usuários receberão instantaneamente o aplicativo adicionado ao seu painel de acesso do AD do Azure. Isso será apropriado se o aplicativo tiver sido definido para [provisionamento automático de usuário](active-directory-saas-app-provisioning.md), ou configurado como [modo SSO de senha "gerenciada pelo usuário"](active-directory-appssoaccess-whatis.md#password-based-single-sign-on) no qual o usuário já tem uma conta e sabe a senha.

**7:** se o aplicativo foi configurado para usar logon único baseado em senha, estará disponível uma opção para permitir que o aprovador defina credenciais SSO em nome de cada usuário. Consulte a seção a seguir em delegar gerenciamento de acesso, para obter mais informações.

**8:** por fim, o **grupo de usuários autoatribuídos** mostra o nome do grupo que será usado para armazenar os usuários que receberam acesso ou foram atribuídos ao aplicativo. O aprovador do acesso torna-se o proprietário deste grupo. Se o nome do grupo mostrado não existir, ele será criado automaticamente. Opcionalmente, o nome do grupo pode ser definido como o nome de um grupo existente.

**9:** clique em **Salvar** na parte inferior da tela para salvar a configuração. Agora os usuários poderão solicitar acesso a este aplicativo do painel de acesso.

**10:** para testar a experiência do usuário final, entrar no painel de acesso do AD do Azure da organização em https://myapps.microsoft.com, preferencialmente usando uma conta diferente que não seja um aprovador de aplicativo.

**11:** na guia **Aplicativos** clique no bloco **Obter mais aplicativos**. Isso exibe uma galeria de todos os aplicativos habilitados para acesso a aplicativos de autoatendimento no diretório, com a capacidade de pesquisar e filtrar por categoria de aplicativo à esquerda.

**12:** clicar em um aplicativo inicia o processo de solicitação. Se nenhum processo de aprovação for necessário, o aplicativo será adicionado imediatamente na guia **Aplicativos**, após uma curta confirmação. Se a aprovação for necessária, você verá uma caixa de diálogo indicando isso e um email será enviado aos aprovadores. (Observação: você precisa entrar no painel de acesso como um não aprovador para ver o processo de solicitação).

**13:** o email direciona o aprovador para entrar no painel de acesso do AD do Azure e aprovar a solicitação. Depois que a solicitação for aprovada (e quaisquer processos especiais que você definir forem executados pelo aprovador), o usuário verá o aplicativo na guia **Aplicativos** em que ele poderá entrar.

##Gerenciamento de acesso delegado ao aplicativo

Um aprovador de acesso ao aplicativo pode ser qualquer usuário em sua organização que seja a pessoa mais apropriada para aprovar ou negar acesso ao aplicativo em questão. Este usuário pode ser responsável pela conta de provisionamento, licenciamento, ou qualquer outro processo de negócios que sua organização exige, antes de conceder acesso a um aplicativo.
 
Ao configurar o acesso do aplicativo de autoatendimento descrito acima, qualquer aplicativo atribuído aprovadores verá um bloco adicional **Gerenciar aplicativos** no painel de acesso do AD do Azure, que mostra quais aplicativos são o administrador de acesso para. Clicar em um aplicativo mostra uma tela com várias opções.

![][2]

###Aprovar solicitações

O bloco **Aprovar Solicitações** permite que os aprovadores vejam aprovações pendentes específicas do aplicativo e redireciona para a guia Aprovações em que as solicitações podem ser confirmadas ou negadas. Observe que o aprovador também recebe emails automáticos sempre que uma solicitação é criada, instruindo-o do que fazer.

###Adicionar Usuários

O bloco **Adicionar Usuários** permite que os aprovadores concedam diretamente aos usuários selecionados acesso ao aplicativo. Ao clicar nesse bloco, o aprovador vê que uma caixa de diálogo permite exibir e pesquisar usuários em seu diretório. Adicionar um usuário faz com que o aplicativo seja mostrado nos painéis de acesso do AD do Azure desse usuário ou no Office 365. Se qualquer processo de provisionamento manual do usuário for necessário no aplicativo antes que o usuário possa entrar, o aprovador deve executar esse processo antes de atribuir acesso.

###Gerenciar Usuários

O bloco **Gerenciar Usuários** permite aos aprovadores atualizar ou remover diretamente os usuários que têm acesso ao aplicativo.

###Configurar credenciais de SSO de senha (se aplicável)

O bloco **Configurar** só será mostrado se o aplicativo foi configurado pelo administrador de TI para usar logon único baseado em senha, e o administrador garantiu ao aprovador a capacidade de definir credenciais SSO de senha, conforme descrito anteriormente. Quando selecionado, são apresentadas várias opções ao aprovador sobre como as credenciais serão propagadas aos usuários atribuídos:

![][3]

* **Os usuários entram com suas próprias senhas** – deste modo, os usuários atribuídos sabem que seus nomes de usuário e senhas são para o aplicativo e serão solicitados a inseri-las no primeiro acesso ao aplicativo. Isso corresponde à senha SSO em que os [usuários gerenciam credenciais](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).

* **Os usuários entram automaticamente usando contas separadas que eu gerencio** – assim, os usuários atribuídos não precisam inserir nem saber suas credenciais específicas do aplicativo ao entrar no aplicativo. Em vez disso, o aprovador define as credenciais para cada usuário depois de atribuir acesso usando o bloco **Adicionar Usuário**. Quando o usuário clica no aplicativo no seu painel de acesso ou no Office 365, ele será conectado automaticamente usando as credenciais definidas pelo aprovador. Isso corresponde à senha SSO em que os [administradores gerenciam credenciais](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).

* **Os usuários entram automaticamente usando uma única conta que eu gerencio** –esse é um caso especial e é apropriado para ser usado quando é necessário conceder acesso a todos os usuários atribuídos usando uma conta compartilhada. O caso de uso mais comum para isso são os aplicativos de mídia social, em que uma organização tem uma única conta "corporativa" e vários usuários precisam fazer atualizações nessa conta. Isso também corresponde à senha SSO, em que os [administradores gerenciam credenciais](active-directory-appssoaccess-whatis.md#password-based-single-sign-on). No entanto, depois de selecionar essa opção, o aprovador deverá inserir o nome de usuário e a senha para a conta compartilhada única. Depois de concluído, todos os usuários atribuídos serão conectados usando essa conta ao clicar no aplicativo nos painéis de acesso do AD do Azure ou no Office 365.

<!--Image references-->
[1]: ./media/active-directory-self-service-application-access/ssaa_admin.PNG
[2]: ./media/active-directory-self-service-application-access/ssaa_ap_manage_app.PNG
[3]: ./media/active-directory-self-service-application-access/ssaa_ap_manage_app_config.PNG

<!---HONumber=AcomDC_0128_2016-->