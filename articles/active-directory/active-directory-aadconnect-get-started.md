<properties 
	pageTitle="Introdução ao Azure AD Connect"
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
	ms.date="08/24/2015"
	ms.author="billmath"/>

# Introdução ao Azure AD Connect



A documentação a seguir o ajudará a começar com o Azure Active Directory Connect. Essa documentação lida com o uso da instalação expressa para o Azure AD Connect. Para obter informações sobre uma instalação personalizada, consulte [Instalação personalizada para o Azure AD Connect](active-directory-aadconnect-get-started-custom.md). Para obter informações sobre como atualizar do DirSync para o Azure AD Connect, consulte [Atualizando o DirSync para o Azure Active Directory Connect.](active-directory-aadconnect-dirsync-upgrade-get-started.md)


## Baixar o Azure AD Connect



Para começar a usar o Azure AD Connect, você pode baixar a versão mais recente usando o seguinte: [Baixar o Azure AD Connect](http://go.microsoft.com/fwlink/?LinkID=615771)

## Antes de instalar o Azure AD Connect
Antes de instalar o Azure AD Connect com configurações expressas, há algumas coisas de que você precisará.


 
- Uma assinatura do Azure ou uma [assinatura de avaliação do Azure](http://azure.microsoft.com/pricing/free-trial/) - isso só é necessário para acessar o portal do Azure, não para usar o Azure AD Connect. Se estiver usando o PowerShell ou o Office 365, você não precisará de uma assinatura do Azure para usar o Azure AD Connect.
- Uma conta de Administrador Global de AD do Azure para o locatário do AD do Azure com o qual você deseja se integrar
- Um controlador de domínio ou servidor membro do AD com o Windows Server 2008 ou posterior
- Uma conta de administrador corporativo para o Active Directory local
- Opcional: uma conta de usuário de teste para verificar a sincronização. 

### Requisitos de hardware para o Azure AD Connect
A tabela a seguir mostra os requisitos mínimos para o computador do Azure AD Connect.

| Número de objetos no Active Directory | CPU | Memória | Tamanho do disco rígido |
| ------------------------------------- | --- | ------ | --------------- |
| Menos de 10.000 | 1,6 GHz | 4 GB | 70 GB |
| 10\.000–50.000 | 1,6 GHz | 4 GB | 70 GB |
| 50\.000–100.000 | 1,6 GHz | 16 GB | 100 GB |
| Para 100.000 ou mais objetos, é necessária a versão completa do SQL Server| | | |
| 100\.000–300.000 | 1,6 GHz | 32 GB | 300 GB |
| 300\.000–600.000 | 1,6 GHz | 32 GB | 450 GB |
| Mais de 600.000 | 1,6 GHz | 32 GB | 500 GB |




Para opções personalizadas, como várias florestas ou logon federado, saiba mais sobre os requisitos adicionais [aqui.](active-directory-aadconnect-get-started-custom.md)


## Instalação expressa do Azure AD Connect
Selecionar as configurações expressas é a opção padrão e é um dos cenários mais comuns. Ao fazer isso, o Azure AD Connect implanta a sincronização com a opção de sincronização de hash de senha. Isso é apenas para uma única floresta e permite que os usuários usem suas senhas locais para entrar na nuvem. O uso das configurações expressas iniciará automaticamente uma sincronização depois que a instalação for concluída (embora você possa optar por não fazer isso). Com essa opção, bastam alguns cliques breves para estender seu diretório local para a nuvem.

<center>![Bem-vindo ao Azure AD Connect](./media/active-directory-aadconnect-get-started/welcome.png)</center>

### Para instalar o Azure AD Connect usando configurações expressas
--------------------------------------------------------------------------------------------

1. Faça logon no servidor no qual deseja instalar o Azure AD Connect como um administrador corporativo. Esse deve ser o servidor que você deseja que seja o servidor de sincronização.
2. Navegue até AzureADConnect.msi e clique duas vezes
3. Na tela de boas-vindas, marque a caixa de concordar com os termos da licença e clique em **Continuar**.
4. Na tela de configurações expressas, clique em **Usar configurações expressas**.
<center>![Bem-vindo ao Azure AD Connect](./media/active-directory-aadconnect-get-started/express.png)</center>
6. Na tela Conectar ao AD do Azure, insira o nome de usuário e a senha de um administrador global do Azure para seu AD do Azure. Clique em **Próximo**.
8. Na tela Conectar-se ao AD DS, digite o nome de usuário e a senha para uma conta de administrador corporativa. Clique em **Próximo**.
<center>![Bem-vindo ao Azure AD Connect](./media/active-directory-aadconnect-get-started/install4.png)</center>
9. Na tela Pronto para configurar, clique em **Instalar**.
	- Na página “Pronto para configurar”, você pode desmarcar a caixa de seleção “**Iniciar o processo de sincronização assim que a configuração for concluída**”. Se você fizer isso, o assistente vai configurar a sincronização, mas deixará a tarefa desabilitada para que ela não seja executada até você habilitá-la manualmente no Agendador de Tarefas. Quando a tarefa estiver habilitada, a sincronização será executada a cada três horas.
	- Também é possível configurar os serviços de sincronização para **implantação híbrida do Exchange** marcando a caixa de seleção correspondente. Se você não planeja ter caixas de correio do Exchange na nuvem e no local, isso não é necessário.

<center>![Bem-vindo ao Azure AD Connect](./media/active-directory-aadconnect-get-started/readyinstall.png)</center>
8. Quando a instalação for concluída, clique em **Sair**.


<br> <br>

Para ver um vídeo sobre como usar a instalação expressa, verifique o seguinte:

<center>[AZURE.VIDEO azure-active-directory-connect-express-settings]</center>



## Verificação da instalação

Depois de instalar com êxito o Azure Connect AD Connect, você pode verificar se a sincronização está ocorrendo ao entrar no portal do Azure e verificar o horário da última sincronização.

1.  Entre no Portal do Azure.
2.  Selecione Active Directory à esquerda.
3.  Clique duas vezes no diretório usado para configurar o Azure AD Connect.
4.  Na parte superior, selecione Integração de diretórios. Observe o horário da última sincronização.

<center>![Instalação expressa](./media/active-directory-aadconnect-get-started/verify.png)</center>

## O que fazer em seguida?
Agora que você tem o Azure AD Connect instalado, você pode usar o link [aqui](active-directory-aadconnect-whats-next.md) para continuar com as tarefas de pós-instalação, como atribuir aos usuários licenças do Azure AD Premium ou Enterprise Mobility ou configurar opções adicionais.

**Recursos adicionais**

[Comparação de ferramentas de integração de diretório](active-directory-aadconnect-get-started-tools-comparison.md)

 

<!---HONumber=August15_HO9-->