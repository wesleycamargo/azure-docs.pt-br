<properties
	pageTitle="Preview dos Serviços de Domínio do Azure Active Directory: Guia de Administração | Microsoft Azure"
	description="Ingressar uma máquina virtual do Windows Server nos Serviços de Domínio do Azure AD"
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
	ms.date="08/31/2016"
	ms.author="maheshu"/>

# Ingressar uma máquina virtual do Windows Server em um domínio gerenciado

> [AZURE.SELECTOR]
- [Portal clássico do Azure - Windows](active-directory-ds-admin-guide-join-windows-vm.md)
- [PowerShell - Windows](active-directory-ds-admin-guide-join-windows-vm-classic-powershell.md)

<br>

Este artigo mostra como ingressar uma máquina virtual executando o Windows Server 2012 R2 em um domínio gerenciado dos Serviços de Domínio do Azure AD, usando o portal clássico do Azure.


## Etapa 1: Criar a máquina virtual do Windows Server
Siga as instruções descritas no tutorial [Criar uma máquina virtual executando o Windows no portal clássico do Azure](../virtual-machines/virtual-machines-windows-classic-tutorial.md). É importante garantir que essa máquina virtual recém-criada seja ingressada na mesma rede virtual em que você habilitou os Serviços de Domínio do Azure AD. A opção 'Criação Rápida' não permite que você ingresse a máquina virtual em uma rede virtual. Portanto, você precisa usar a opção 'Da Galeria' para criar a máquina virtual.

Realize as seguintes etapas para criar uma máquina virtual do Windows ingressada na rede virtual na qual você habilitou os Serviços de Domínio do Azure AD.

1. No portal clássico do Azure, na barra de comandos na parte inferior da janela, clique em **Novo**.

2. Em **Computação**, clique em **Máquina Virtual** e em **Da Galeria**.

3. A primeira tela permite **Escolher uma Imagem** para sua máquina virtual na lista de imagens disponíveis. Selecione a imagem apropriada.

    ![Selecionar a imagem](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)

4. A segunda tela permite separar um nome de computador, tamanho e nome e senha do usuário administrativo. Use a camada e o tamanho necessários à execução do aplicativo ou da carga de trabalho. O nome de usuário que você escolhe aqui é um usuário de administrador local no computador. Não digite as credenciais de uma conta usuário de domínio aqui.

    ![Configurar a máquina virtual](./media/active-directory-domain-services-admin-guide/create-windows-vm-config.png)

5. A terceira tela permite configurar recursos para rede, armazenamento e disponibilidade. Certifique-se de selecionar a rede virtual em que você habilitou os Serviços de Domínio do Azure AD na lista suspensa **Região/Grupo de Afinidades/Rede Virtual**. Especifique um **Nome DNS do Serviço de Nuvem** conforme apropriado para a máquina virtual.

    ![Selecionar a rede virtual para a máquina virtual](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)

    > [AZURE.WARNING]
    Certifique-se de ingressar a máquina virtual na mesma rede virtual em que você habilitou os Serviços de Domínio do Azure AD. Como resultado, a máquina virtual pode 'ver' o domínio e executar tarefas como ingressar no domínio. Se você optar por criar a máquina virtual em uma rede virtual diferente, certifique-se de que a rede virtual esteja conectada à rede virtual na qual você habilitou os Serviços de Domínio do Azure AD.

6. A quarta tela permite instalar o Agente de VM e configurar algumas das extensões disponíveis.

    ![Concluído](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)

7. Depois que a máquina virtual é criada, o portal clássico lista a nova máquina virtual no nó **Máquinas Virtuais**. A máquina virtual e o serviço de nuvem são iniciados automaticamente, e seu status é listado como **Em Execução**.

    ![A máquina virtual já está em funcionamento](./media/active-directory-domain-services-admin-guide/create-windows-vm-running.png)


## Etapa 2: Conectar-se à máquina virtual do Windows Server usando a conta de administrador local
Agora, nos conectamos à máquina virtual do Windows Server recém-criada, para ingressar no domínio. Use as credenciais de administrador local especificadas durante a criação da máquina virtual, para se conectar a ela.

Execute as seguintes etapas para se conectar à máquina virtual.

1. Navegue até o nó **Máquinas Virtuais** no portal clássico. Selecione a máquina virtual que você criou na Etapa 1 e clique em **Conectar** na barra de comandos na parte inferior da janela.

    ![Conectar-se à máquina virtual do Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. O portal clássico solicita que você abra ou salve um arquivo com uma extensão '.rdp', que é usada para se conectar à máquina virtual. Clique para abrir o arquivo quando o download for concluído.

3. No prompt de logon, insira as **credenciais de administrador local** que você especificou ao criar a máquina virtual. Por exemplo, usamos 'localhost\\mahesh' neste exemplo.

Neste ponto, você deve estar conectado à máquina virtual do Windows recém-criada usando as credenciais de administrador local. A próxima etapa é ingressar a máquina virtual no domínio.


## Etapa 3: Ingressar a máquina virtual do Windows Server no domínio gerenciado do DS do AAD
Execute as seguintes etapas para ingressar a máquina virtual do Windows Server no domínio gerenciado do DS do AAD.

1. Conecte-se ao Windows Server como mostrado na Etapa 2. Na tela inicial, abra **Gerenciador do Servidor**.

2. Clique em **Servidor Local** no painel esquerdo da janela do Gerenciador do Servidor.

    ![Iniciar o Gerenciador do Servidor na máquina virtual](./media/active-directory-domain-services-admin-guide/join-domain-server-manager.png)

3. Clique em **GRUPO DE TRABALHO** na seção **PROPRIEDADES**. Isso abre a página de propriedades **Propriedades do Sistema**. Para ingressar no domínio, clique em **Alterar**.

    ![Página Propriedades do Sistema](./media/active-directory-domain-services-admin-guide/join-domain-system-properties.png)

4. Especifique o nome de domínio do seu domínio gerenciado dos Serviços de Domínio do Azure AD na caixa de texto **Domínio** e clique em **OK**.

    ![Especificar o domínio no qual ingressar](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-domain.png)

5. Você é solicitado a inserir suas credenciais para ingressar no domínio. Certifique-se de **especificar as credenciais para um usuário que pertença ao grupo Administradores do DC do AAD**. Somente os membros desse grupo têm privilégios para ingressar computadores no domínio gerenciado.

    ![Especificar credenciais para ingresso no domínio](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-credentials.png)

6. Você pode especificar as credenciais das seguintes maneiras:

    - Formato UPN: esse é o sufixo UPN para a conta de usuário, conforme configurado no Azure AD. Neste exemplo, o sufixo UPN do usuário 'bob' é 'bob@domainservicespreview.onmicrosoft.com'.

    - Formato SAMAccountName: você pode especificar o nome da conta no formato SAMAccountName. Neste exemplo, o usuário 'bob' precisaria inserir 'CONTOSO100\\bob'. Observação - se vários usuários tiverem o mesmo prefixo UPN (por exemplo, 'bob') no locatário do Azure AD, encontrarão problemas para fazer logon no domínio usando o formato SAMAccountName. Nesses casos, o formato UPN pode ser usado de forma confiável para fazer logon no domínio.

7. Após o ingresso no domínio ser realizado com êxito, você vê a mensagem de boas-vindas ao domínio a seguir. Reinicie a máquina virtual para que a operação de junção de domínio seja concluída.

    ![Bem-vindo ao domínio](./media/active-directory-domain-services-admin-guide/join-domain-done.png)


## Solucionando problemas de ingresso no domínio
### Problemas de conectividade
Se a máquina virtual for incapaz de localizar o domínio, consulte as seguintes etapas de solução de problemas:

- Certifique-se de que a máquina virtual esteja conectada à mesma rede virtual na qual você habilitou os Serviços de Domínio. Caso contrário, a máquina virtual não pode se conectar ao domínio e, assim, não é possível ingressar no domínio.

- Se a máquina virtual estiver conectada a outra rede virtual, certifique-se de que essa rede virtual esteja conectada à rede virtual na qual você habilitou os Serviços de Domínio.

- Tente executar o ping no domínio usando o nome do domínio gerenciado (por exemplo, 'ping contoso100.com'). Se você não puder fazer isso, tente executar o ping dos endereços IP para o domínio exibido na página em que você habilitou os Serviços de Domínio do Azure AD (por exemplo, 'ping 10.0.0.4'). Se você conseguir executar o ping do endereço IP, mas não o domínio, o DNS poderá estar configurado incorretamente. Você pode não ter configurado os endereços IP do domínio como servidores DNS para a rede virtual.

- Tente liberar o cache do resolvedor DNS na máquina virtual ('ipconfig /flushdns').

Se você chegar à caixa de diálogo que solicita credenciais para ingressar no domínio, não terá problemas de conectividade.


### Problemas de credenciais
Consulte as etapas a seguir se você estiver enfrentando problemas com as credenciais e não conseguir ingressar no domínio.

- Certifique-se de estar usando credenciais de uma conta de usuário que pertença ao grupo Administradores do DC do AAD. Os usuários que não pertencem a esse grupo não podem ingressar computadores no domínio gerenciado.

- Verifique se você [habilitou a sincronização de senhas](active-directory-ds-getting-started-password-sync.md) de acordo com as etapas descritas no Guia de Introdução.

- Certifique-se de usar o UPN do usuário conforme configurado no Azure AD (por exemplo, 'bob@domainservicespreview.onmicrosoft.com') para entrar.

- Certifique-se de que já esperou tempo suficiente para a sincronização de senha ser concluída conforme especificado no Guia de Introdução.


## Conteúdo relacionado

- [Serviços de Domínio do Azure AD - guia de Introdução](./active-directory-ds-getting-started.md)

- [Administrar um domínio gerenciado dos Serviços de Domínio do Azure AD](./active-directory-ds-admin-guide-administer-domain.md)

<!---HONumber=AcomDC_0907_2016-->