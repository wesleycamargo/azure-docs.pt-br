<properties
	pageTitle="Introdução ao Azure AD Connect usando configurações expressas | Microsoft Azure"
	description="Saiba como baixar, instalar e executar o assistente de instalação do Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/13/2015"
	ms.author="billmath;andkjell"/>

# Introdução ao Azure AD Connect usando configurações expressas
A documentação a seguir o ajudará a começar com o Azure Active Directory Connect. Essa documentação lida com o uso da instalação expressa para o Azure AD Connect.

## Documentação relacionada
Se você não leu a documentação em [Integrando suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md), a tabela a seguir fornece links para tópicos relacionados. Os dois primeiros tópicos em negrito são necessários antes de iniciar a instalação.

| Tópico | |
| --------- | --------- |
| **Baixar o Azure AD Connect** | [Baixar o Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771) |
| **Hardware e pré-requisitos** | [Azure AD Connect: hardware e pré-requisitos](active-directory-aadconnect-prerequisites.md) |
| Instalar usando configurações personalizadas | [Instalação personalizada do Azure AD Connect](active-directory-aadconnect-get-started-custom.md) |
| Atualizar do DirSync | [Atualização da ferramenta de sincronização do Azure AD (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md) |
| Após a instalação | [Verifique a instalação e atribua licenças](active-directory-aadconnect-whats-next.md) |
| Contas usadas para instalação | [Mais informações sobre permissões e contas do Azure AD Connect](active-directory-aadconnect-accounts-permissions.md) |


## Instalação expressa do Azure AD Connect
Selecionar as configurações expressas é a opção padrão e é um dos cenários mais comuns. Ao fazer isso, o Azure AD Connect implanta a sincronização com a opção de sincronização de senha. Isso é apenas para uma única floresta e permite que os usuários usem suas senhas locais para entrar na nuvem. O uso das configurações expressas iniciará automaticamente uma sincronização depois que a instalação for concluída (embora você possa optar por não fazer isso). Com essa opção, bastam alguns cliques breves para estender seu diretório local para a nuvem.

![Bem-vindo ao Azure AD Connect](./media/active-directory-aadconnect-get-started/welcome.png)

### Para instalar o Azure AD Connect usando configurações expressas

1. Faça logon no servidor no qual deseja instalar o Azure AD Connect como um administrador local. Esse deve ser o servidor que você deseja que seja o servidor de sincronização.
2. Navegue até AzureADConnect.msi e clique duas vezes
3. Na tela de boas-vindas, marque a caixa de concordar com os termos da licença e clique em **Continuar**.
4. Na tela de configurações expressas, clique em **Usar configurações expressas**.![Bem-vindo ao Azure AD Connect](./media/active-directory-aadconnect-get-started/express.png)
5. Na tela Conectar ao AD do Azure, insira o nome de usuário e a senha de um administrador global do Azure para seu AD do Azure. Clique em **Próximo**.
6. Na tela Conectar-se ao AD DS, digite o nome de usuário e a senha para uma conta de administrador corporativa. Clique em **Próximo**. ![Bem-vindo ao Azure AD Connect](./media/active-directory-aadconnect-get-started/install4.png)
7. Na tela Pronto para configurar, clique em **Instalar**.
	- Na página “Pronto para configurar”, você pode desmarcar a caixa de seleção “**Iniciar o processo de sincronização assim que a configuração for concluída**”. Se você fizer isso, o assistente vai configurar a sincronização, mas deixará a tarefa desabilitada para que ela não seja executada até você habilitá-la manualmente no Agendador de Tarefas. Quando a tarefa estiver habilitada, a sincronização será executada a cada três horas.
	- Também é possível configurar os serviços de sincronização para **implantação híbrida do Exchange** marcando a caixa de seleção correspondente. Se você não planeja ter caixas de correio do Exchange na nuvem e no local, isso não é necessário. ![Bem-vindo ao Azure AD Connect](./media/active-directory-aadconnect-get-started/readyinstall.png)<br>
8. Quando a instalação for concluída, clique em **Sair**.


<br> <br>

Para ver um vídeo sobre como usar a instalação expressa, verifique o seguinte:

<center>[AZURE.VIDEO azure-active-directory-connect-express-settings]</center>


## Próximas etapas
Agora que tem o Azure Connect AD instalado, você pode [verificar a instalação e atribuir licenças](active-directory-aadconnect-whats-next.md).

Saiba mais sobre a [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md).

<!---HONumber=Oct15_HO3-->