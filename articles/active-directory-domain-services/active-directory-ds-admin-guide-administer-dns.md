<properties
	pageTitle="Preview dos Serviços de Domínio do Azure Active Directory: Administrar o DNS em domínios gerenciados | Microsoft Azure"
	description="Administrar DNS em domínios gerenciados usando os Serviços de Domínio do Azure Active Directory"
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

# Administrar o DNS em um domínio gerenciado dos Serviços de Domínio do Azure AD
Os Serviços de Domínio do Azure Active Directory incluem um servidor DNS (resolução de nomes de domínio) que fornece a resolução DNS para o domínio gerenciado. Ocasionalmente, pode ser necessário configurar o DNS no domínio gerenciado para criar registros DNS para computadores que não ingressaram no domínio, endereços IP virtuais para balanceadores de carga ou encaminhadores DNS externos. Por esse motivo, os usuários que pertencem ao grupo 'Administradores do AAD DC' recebem privilégios para administração de DNS no domínio gerenciado.


## Antes de começar
Para executar as tarefas listadas neste artigo, você precisará de:

1. Uma **assinatura do Azure** válida.

2. Um **diretório do AD do Azure** - seja sincronizado com um diretório local ou com um diretório somente na nuvem.

3. Os **Serviços de Domínio do Azure AD** devem ser habilitados para o diretório do Azure AD. Se você ainda não tiver feito isso, execute todas as tarefas descritas no [guia de Introdução](./active-directory-ds-getting-started.md).

4. Uma **máquina virtual ingressada no domínio** por meio da qual você administrará o domínio gerenciado dos Serviços de Domínio do Azure AD. Caso não tenha uma máquina virtual desse tipo, realize todas as tarefas descritas no artigo [Ingressar uma máquina virtual do Windows em um domínio gerenciado](./active-directory-ds-admin-guide-join-windows-vm.md).

5. Você precisará das credenciais de um **conta de usuário que pertencem ao grupo “Administradores do controlador de domínio do AAD”** em seu diretório, para administrar o DNS para seu domínio gerenciado.

<br>

## Tarefa 1 - Provisionar uma máquina virtual ingressada no domínio para administrar o DNS remotamente para o domínio gerenciado
Domínios gerenciados dos Serviços de Domínio do Azure AD podem ser gerenciados remotamente usando as ferramentas administrativas familiares do Active Directory, como o ADAC (Centro Administrativo do Active Directory) ou o AD PowerShell. Do mesmo modo, o DNS para o domínio gerenciado pode ser administrado remotamente usando as Ferramentas de administração do servidor DNS.

Os administradores em seu diretório do de Azure AD não têm privilégios para se conectar a controladores de domínio no domínio gerenciado por meio da Área de Trabalho Remota. Portanto, os membros do grupo 'Administradores do AAD DC' podem administrar DNS remotamente para domínios gerenciados usando as ferramentas do servidor DNS de um computador cliente do Windows/Windows Server que tenha ingressado no domínio gerenciado. Ferramentas do servidor DNS podem ser instaladas como parte do recurso opcional RSAT (Ferramentas de Administração de Servidor Remoto) no Windows Server e computadores clientes que ingressaram no domínio gerenciado.

A primeira etapa é provisionar uma máquina virtual do Windows Server que tenha ingressado no domínio gerenciado. Para obter instruções sobre como fazer isso, consulte o artigo intitulado [Ingressar uma máquina virtual do Windows Server em um domínio gerenciado dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-join-windows-vm.md).


## Tarefa 2 - Instalar as ferramentas de Servidor DNS na máquina virtual
Execute as etapas a seguir para instalar as Ferramentas de administração de DNS na máquina virtual ingressada no domínio. Para obter mais detalhes sobre [como instalar e usar as Ferramentas de Administração de Servidor Remoto](https://technet.microsoft.com/library/hh831501.aspx), consulte o TechNet.

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

9. Na página **Recursos**, clique para expandir o nó **Ferramentas de Administração de Servidor Remoto** e clique para expandir o nó **Ferramentas de Administração de Funções**. Selecione o recurso **Ferramentas do Servidor DNS** na lista de ferramentas de administração de funções, conforme mostrado abaixo.

	![Página Recursos](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)

10. Na página **Confirmação**, clique em **Instalar** para instalar o recurso de ferramentas do Servidor DNS na máquina virtual. Quando a instalação do recurso for concluída com êxito, clique em **Fechar** para sair do assistente **Adicionar Funções e Recursos**.

	![Página de confirmação](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-confirmation.png)


## Tarefa 3 - Iniciar o console de gerenciamento do DNS para administrar o DNS
Agora que o recurso Ferramentas do Servidor DNS está instalado na máquina virtual ingressada no domínio, podemos usar as ferramentas DNS para administrar DNS no domínio gerenciado.

> [AZURE.NOTE] Você precisará ser um membro do grupo “Administradores do controlador de domínio do AAD”, para administrar o DNS no domínio gerenciado.

1. Na tela inicial, clique em **Ferramentas Administrativas**. Você deverá ver o console **DNS** instalado na máquina virtual.

	![Ferramentas administrativas - Console DNS](./media/active-directory-domain-services-admin-guide/install-rsat-dns-tools-installed.png)

2. Clique em **DNS** para iniciar o console de Gerenciamento de DNS.

3. No diálogo **Conectar-se ao Servidor DNS**, clique na opção intitulada **O computador seguinte** e insira o nome de domínio DNS do domínio gerenciado (por exemplo, 'contoso100.com').

    ![Console DNS - conectar-se ao domínio](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)

4. Conecta o Console DNS ao domínio gerenciado. Você deverá ver uma exibição semelhante à seguinte.

    ![Console DNS - administrar domínio](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)

5. Agora você pode usar o console DNS para adicionar entradas DNS a computadores na rede virtual na qual você habilitou os Serviços de Domínio do AAD.

> [AZURE.WARNING] Seja extremamente cauteloso ao administrar DNS para o domínio gerenciado usando as ferramentas de administração de DNS. Certifique-se de **não excluir nem modificar os registros DNS internos usados pelos Serviços de Domínio no domínio em questão**. Isso inclui registros DNS do domínio, os registros do servidor de nome e outros registros usados para a localização do controlador de domínio. Se você modificar esses registros, os serviços de domínio serão interrompidos na rede virtual.


Para obter mais informações sobre como gerenciar o DNS, veja o [artigo sobre ferramentas de DNS no TechNet](https://technet.microsoft.com/library/cc753579.aspx).


## Conteúdo relacionado

- [Serviços de Domínio do Azure AD - guia de Introdução](./active-directory-ds-getting-started.md)

- [Administrar um domínio gerenciado dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-administer-domain.md)

- [Ingressar uma máquina virtual do Windows Server em um domínio gerenciado dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)

- [Ferramentas de administração do DNS](https://technet.microsoft.com/library/cc753579.aspx)

<!---HONumber=AcomDC_0706_2016-->