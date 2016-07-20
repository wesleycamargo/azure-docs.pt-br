<properties
	pageTitle="Visualização dos Serviços de Domínio do Azure Active Directory: administrar um domínio gerenciado | Microsoft Azure"
	description="Administrar domínios gerenciados dos Serviços de Domínio do Azure Active Directory"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2016"
	ms.author="maheshu"/>

# Administrar um domínio gerenciado dos Serviços de Domínio do Azure Active Directory
Este artigo mostra como administrar um domínio gerenciado dos Serviços de Domínio do Azure Active Directory (AD).


## Antes de começar
Para executar as tarefas listadas neste artigo, você precisará de:

1. Uma **assinatura do Azure** válida.

2. Um **diretório do AD do Azure** - seja sincronizado com um diretório local ou com um diretório somente na nuvem.

3. Os **Serviços de Domínio do Azure AD** devem ser habilitados para o diretório do Azure AD. Se você ainda não tiver feito isso, execute todas as tarefas descritas no [guia de Introdução](./active-directory-ds-getting-started.md).

4. Uma **máquina virtual ingressada no domínio** por meio da qual você administrará o domínio gerenciado dos Serviços de Domínio do Azure AD. Caso não tenha uma máquina virtual desse tipo, realize todas as tarefas descritas no artigo [Ingressar uma máquina virtual do Windows em um domínio gerenciado](./active-directory-ds-admin-guide-join-windows-vm.md).

5. Você precisará das credenciais de uma **conta de usuário que pertença ao grupo 'Administradores do controlador de domínio do AAD'** em seu diretório para administrar o seu domínio gerenciado.

<br>


## Tarefas administrativas que você pode executar em um domínio gerenciado
Para começar, vamos dar uma olhada em tarefas administrativas que você pode executar em um domínio gerenciado. Membros do grupo 'Administradores do AAD DC' recebem privilégios no domínio gerenciado que permitem executar tarefas como:

- Ingressar máquinas no domínio gerenciado.

- Configure o GPO interno para os contêineres 'Computadores AADDC' e 'Usuários AADDC' no domínio gerenciado.

- Administre o DNS no domínio gerenciado.

- Crie e administre personalizado UOs (Unidades Organizacionais) personalizadas no domínio gerenciado.

- Obter acesso administrativo a computadores ingressados no domínio gerenciado.


## Privilégios administrativos que você não tem em um domínio gerenciado
O domínio é gerenciado pela Microsoft, incluindo atividades como aplicação de patches, monitoramento, realização de backups, etc. Portanto, o domínio está bloqueado e você não tem privilégios para executar determinadas tarefas administrativas no domínio. Abaixo, alguns exemplos de tarefas que você não pode executar.

- Você não recebe privilégios de Administrador Corporativo ou de Administrador de Domínio para o domínio gerenciado.

- Você não pode estender o esquema do domínio gerenciado.

- Você não pode se conectar a controladores de domínio do domínio gerenciado usando a Área de Trabalho Remota.

- É possível adicionar controladores de domínio ao domínio gerenciado.


## Tarefa 1: provisionar uma máquina virtual do Windows Server ingressada no domínio para administrar remotamente o domínio gerenciado
Domínios gerenciados dos Serviços de Domínio do Azure AD podem ser gerenciados usando as ferramentas administrativas familiares do Active Directory, como o ADAC (Centro Administrativo do Active Directory) ou o AD PowerShell. Os administradores de locatários não têm privilégios para conectar-se a controladores de domínio no domínio gerenciado por meio da Área de Trabalho Remota. Portanto, os membros do grupo 'Administradores do AAD DC' podem administrar domínios gerenciados remotamente usando as ferramentas administrativas do AD de um computador cliente do Windows/Windows Server que tenha ingressado no domínio gerenciado. As ferramentas administrativas do AD podem ser instaladas como parte do recurso opcional RSAT (Ferramentas de Administração de Servidor Remoto) no Windows Server e computadores clientes que ingressaram no domínio gerenciado.

A primeira etapa é configurar uma máquina virtual do Windows Server que tenha ingressado no domínio gerenciado. Para obter instruções sobre como fazer isso, confira o artigo intitulado [Ingressar uma máquina virtual do Windows Server em um domínio gerenciado dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-join-windows-vm.md).

### Administrar remotamente o domínio gerenciado de um computador cliente (por exemplo, Windows 10)
Observe que as instruções neste artigo usam uma máquina virtual do Windows Server para administrar o domínio gerenciado do AAD-DS. No entanto, você também pode optar por usar uma máquina virtual cliente do Windows (por exemplo, Windows 10) para fazer isso.

Você pode [instalar as Ferramentas de Administração de Servidor Remoto (RSAT)](http://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx) em uma máquina virtual cliente do Windows seguindo as instruções no TechNet.


## Tarefa 2: instalar ferramentas de administração do Active Directory na máquina virtual
Execute as etapas a seguir para instalar as Ferramentas de administração do Active Directory na máquina virtual ingressada no domínio. Para obter mais detalhes sobre [como instalar e usar as Ferramentas de Administração de Servidor Remoto](https://technet.microsoft.com/library/hh831501.aspx), confira o TechNet.

1. Navegue até o nó **Máquinas Virtuais** no portal clássico do Azure. Selecione a máquina virtual que você acabou de criar e clique em **Conectar** na barra de comandos na parte inferior da janela.

    ![Conectar-se à máquina virtual do Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. O portal clássico será solicitado a abrir ou salvar um arquivo .rdp, que é usado para conectar-se à máquina virtual. Clique no arquivo. rdp quando o download for concluído.

3. No prompt de logon, use as credenciais de um usuário pertencente ao grupo “Administradores do AAD DC”. Por exemplo, 'bob@domainservicespreview.onmicrosoft.com' em nosso caso.

4. Na tela inicial, abra o **Gerenciador do Servidor**. Clique em **Adicionar Funções e Recursos** no painel central da janela do Gerenciador do Servidor.

    ![Iniciar o Gerenciador do Servidor na máquina virtual](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)

5. Na página **Antes de Começar** do **Assistente Adicionar Funções e Recursos**, clique em **Avançar**.

    ![Página Antes de Começar](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)

6. Na página **Tipo de Instalação**, deixe a opção **Instalação baseada em função ou recurso** marcada e clique em **Avançar**.

	![Página Tipo de Instalação](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)

7. Na página **Seleção de Servidor**, selecione a máquina virtual atual no pool de servidores e clique em **Avançar**.

	![Página Seleção de Servidor](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)

8. Na página **Funções do Servidor**, clique em **Avançar**. Vamos ignorar esta página, já que não estamos instalando nenhuma função no servidor.

9. Na página **Recursos**, clique para expandir o nó **Ferramentas de Administração de Servidor Remoto** e clique para expandir o nó **Ferramentas de Administração de Funções**. Selecione o recurso **Ferramentas do AD LDS e AD DS** da lista de ferramentas de administração de funções, conforme mostrado abaixo.

	![Página Recursos](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-ad-tools.png)

10. Na página **Confirmações**, clique em **Instalar** para instalar o AD e o recurso de ferramentas do AD LDS na máquina virtual. Quando a instalação do recurso for concluída com êxito, clique em **Fechar** para sair do assistente **Adicionar Funções e Recursos**.

	![Página de confirmação](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-confirmation.png)


## Tarefa 3: conectar e explorar o domínio gerenciado
Agora que as Ferramentas Administrativas do AD estão instaladas na máquina virtual ingressada no domínio, podemos usar essas ferramentas para explorar e administrar o domínio gerenciado.

> [AZURE.NOTE] Você precisará ser um membro do grupo "Administradores do controlador de domínio do AAD" para administrar o DNS no domínio gerenciado.

1. Na tela inicial, clique em **Ferramentas Administrativas**. Você deve ver as ferramentas administrativas do AD instaladas na máquina virtual.

	![Ferramentas Administrativas instaladas no servidor](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)

2. Clique no **Centro Administrativo do Active Directory**.

	![Centro Administrativo do Active Directory](./media/active-directory-domain-services-admin-guide/adac-overview.png)

3. Clique no nome do domínio no painel à esquerda (por exemplo, 'contoso100.com') para explorar o domínio. Observe os dois contêineres chamados 'Computadores do AADDC' e 'Usuários do AADDC', respectivamente.

    ![ADAC - exibir domínio](./media/active-directory-domain-services-admin-guide/adac-domain-view.png)

4. Clique no contêiner chamado **Usuários do AADDC** para ver todos os usuários e grupos que pertencem ao domínio gerenciado. Você deve ver as contas de usuário e grupos do seu locatário do Azure AD exibidas neste contêiner. Observe que neste exemplo, uma conta de usuário para o usuário 'bob' e um grupo chamado 'Administradores do AAD DC' estão disponíveis neste contêiner.

    ![ADAC - usuários de domínio](./media/active-directory-domain-services-admin-guide/adac-aaddc-users.png)

5. Clique no contêiner chamado **Computadores do AADDC** para ver os computadores associados a esse domínio gerenciado. Você deve ver uma entrada para a máquina virtual atual, que é ingressada no domínio. Contas de computador para todos os computadores que ingressaram em um domínio gerenciado aparecerá dos Serviços de Domínio do Azure AD aparecerão nesse contêiner 'Computadores do AADDC'.

    ![ADAC - computadores ingressados no domínio](./media/active-directory-domain-services-admin-guide/adac-aaddc-computers.png)

<br>

## Conteúdo relacionado

- [Serviços de Domínio do Azure AD - guia de Introdução](./active-directory-ds-getting-started.md)

- [Ingressar uma máquina virtual do Windows Server em um domínio gerenciado dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)

- [Implantar as Ferramentas de Administração de Servidor Remoto](https://technet.microsoft.com/library/hh831501.aspx)

<!---HONumber=AcomDC_0706_2016-->