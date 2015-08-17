
<properties
    pageTitle="Acessando seus aplicativos"
    description="Saiba quais clientes têm suporte para o Azure RemoteApp e como acessar seus aplicativos."
    services="remoteapp"
	documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/23/2015"
    ms.author="elizapo" />



# Acessando seus aplicativos no Azure RemoteApp

Uma das vantagens do Azure RemoteApp é que você pode acessar os aplicativos publicados em qualquer um dos seus dispositivos. Melhor ainda, você pode começar a trabalhar em um dispositivo e transitar sem problemas para um segundo dispositivo e selecioná-lo bem onde parou. Para começar você precisa baixar o cliente apropriado para seu dispositivo e entrar no serviço.

Neste tópico, vamos analisar os clientes com suporte atualmente e como baixá-los antes de mostrar como entrar no RemoteApp por meio de cada um dos clientes.

## Clientes com suporte

Você pode acessar o RemoteApp usando as etapas a seguir se seu dispositivo estiver executando um dos seguintes sistemas operacionais:

 - Visualização do Windows 10
 - Windows 8.1
 - Windows 8
 - Windows 7 Service Pack 1
 - Windows RT
 - Windows Phone 8,1
 - iOS
 - Mac OS X
 - Android


 E clientes finos? Há suporte para os seguintes clientes finos do Windows Embedded:

 - Windows Embedded Standard 7 com Service Pack 1
 - Windows Embedded POSReady7
 - Windows Embedded Thin PC
 - Windows Embedded 8.1 Industry

## Baixar o cliente

Não importa qual plataforma você esteja usando, o cliente que você precisa que acesse o RemoteApp pode ser encontrado na página [Download do cliente de Área de Trabalho Remota](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx).

Clicar nos links diferentes irá iniciar diretamente baixando o cliente ou enviará você à página de download do cliente na loja de aplicativos para essa plataforma. Instale o cliente, seguindo as instruções na tela.

Depois de ter instalado o cliente no dispositivo e o ter iniciado, vá para a seção correspondente abaixo para saber como entrar no RemoteApp a partir desse cliente.

## Android

Depois de instalar o aplicativo de Área de Trabalho Remota da Microsoft a partir do armazenamento do Google Play, você o encontrará em sua lista de aplicativos na **Área de Trabalho Remota**.

1. Iniciar o aplicativo leva você a um Centro de Conexões vazio, a menos que você já tenha usado o aplicativo. Para começar com o Azure RemoteApp, toque no botão Adicionar **"" +""** e em **Azure RemoteApp**.	

	 ![Centro de Conexões Vazio](./media/remoteapp-clients/Android1.png)

2. Você precisa entrar com seu endereço de email para acessar o serviço. Toque em **Introdução**.

	![Prompt de logon](./media/remoteapp-clients/Android2.png)

3. Na página seguinte, digite seu **endereço de email** e toque em **Continuar**. Isso inicia o processo de entrada usando o Active Directory do Azure.

	![Primeira página do Active Directory do Azure](./media/remoteapp-clients/Android3.png)

4. Siga as instruções na tela para entrar com sua conta da Microsoft (antes chamada de “LiveID”) ou a ID da Organização. Depois de conectado, pode ser apresentada uma página listando todos os convites que você recebeu. Se estiver, selecione os convites que você confia e toque em **Concluído**.

	![Página de convites](./media/remoteapp-clients/Android4.png)

5. Após aceitar os convites, a lista de aplicativos que você terá acesso será baixada no dispositivo e disponibilizada no Centro de Conexões. Toque em um dos aplicativos para começar a usá-lo.

	![Centro de Conexões com um feed](./media/remoteapp-clients/Android5.png)

6. Se você ainda não tiver um convite, pode experimentar o serviço. Para fazer isso, toque em **Ir para a avaliação gratuita** quando solicitado.

	![Prompt do feed de demonstração](./media/remoteapp-clients/Android6.png)

7. Isso lhe dará acesso a um conjunto básico de aplicativos para começar com o RemoteApp.

	![Feed de demonstração do Azure RemoteApp](./media/remoteapp-clients/Android7.png)

## iOS

Depois de instalar o aplicativo de Área de Trabalho Remota da Microsoft a partir do armazenamento do Aplicativo, você o encontrará em sua lista de aplicativos na **Cliente da Área de Trabalho Remota**.

1. Iniciar o aplicativo leva você a um Centro de Conexões vazio, a menos que você já tenha usado o aplicativo. Para começar com o Azure RemoteApp, toque no botão Adicionar **"" +""** e em **Adicionar Azure RemoteApp**.

	![Centro de Conexões Vazio](./media/remoteapp-clients/IOS1.png)

2. Você precisa entrar com seu endereço de email para acessar o serviço. Para iniciar esse processo, toque em seu **endereço de email** e em **Continuar**.

	![Prompt de logon](./media/remoteapp-clients/picture1.png)

3. Siga as instruções na tela para entrar com sua conta da Microsoft (LiveID) ou a ID da Organização. Depois de conectado, pode ser apresentada uma página listando todos os convites que você recebeu. Se estiver, selecione os convites que você confia e toque em **Concluído**.

	![Página de convites](./media/remoteapp-clients/IOS3.png)

4. Após aceitar os convites, a lista de aplicativos que você terá acesso será baixada no dispositivo e disponibilizada no Centro de Conexões. Toque em um dos aplicativos para iniciá-lo e começar a usá-lo.

	![Centro de Conexões com um feed](./media/remoteapp-clients/IOS4.png)

5. Se você ainda não tiver um convite, pode experimentar o serviço. Para fazer isso, toque em **Ir para a avaliação gratuita** quando solicitado.

	![Prompt do feed de demonstração](./media/remoteapp-clients/IOS5.png)

6. Isso lhe dará acesso a um conjunto básico de aplicativos para começar com o RemoteApp.

	![Feed de demonstração do Azure RemoteApp](./media/remoteapp-clients/IOS6.png)

## Mac OS X

Depois de instalar o aplicativo de Área de Trabalho Remota da Microsoft a partir do armazenamento do Aplicativo, você o encontrará em sua lista de aplicativos na **Área de Trabalho Remota da Microsoft**.

1. Iniciar o aplicativo leva você a um Centro de Conexões vazio, a menos que você já tenha usado o aplicativo. Para começar com o Azure RemoteApp, clique no botão **Azure RemoteApp**.

	![Centro de Conexões Vazio](./media/remoteapp-clients/Mac1.png)

2. Você precisa entrar com seu endereço de email para acessar o serviço. Para iniciar esse processo, toque em **Introdução**.

	![Prompt de logon](./media/remoteapp-clients/Mac2.png)

3. Na página seguinte, digite seu **endereço de email** e toque em **Continuar**. Isso inicia o processo de entrada usando o Active Directory do Azure.

	![Primeira página do Active Directory do Azure](./media/remoteapp-clients/picture2.png)

4. Siga as instruções na tela para entrar com sua conta da Microsoft (LiveID) ou a ID da Organização. Depois de conectado, pode ser apresentada uma página listando todos os convites que você recebeu. Se estiver, selecione os convites que você confia e feche a caixa de diálogo.

	![Página de convites](./media/remoteapp-clients/Mac4.png)

5. Após aceitar os convites, a lista de aplicativos que você terá acesso será baixada no dispositivo e disponibilizada no Centro de Conexões. Toque duas vezes em um dos aplicativos para iniciá-lo e começar a usá-lo.

	![Centro de Conexões com um feed](./media/remoteapp-clients/Mac5.png)

6. Se você ainda não tiver um convite, pode experimentar o serviço. Para fazer isso, clique em **Ir para a avaliação gratuita** quando solicitado.

	![Prompt do feed de demonstração](./media/remoteapp-clients/Mac6.png)

7. Isso lhe dará acesso a um conjunto básico de aplicativos para começar com o RemoteApp.

	![Feed de demonstração do Azure RemoteApp](./media/remoteapp-clients/Mac7.png)

## Windows (Todas as versões com suporte exceto o Windows Phone)

O cliente será iniciado automaticamente depois de concluir a instalação. No entanto, quando você precisar acessá-lo novamente mais tarde, ele pode ser encontrado na lista de aplicativos sob o nome **Azure RemoteApp**.

1. Após iniciar o cliente, a primeira página que você vê apresenta o Azure RemoteApp. Para continuar, clique em **Introdução**.

	![Página de boas-vindas do cliente Azure RemoteApp](./media/remoteapp-clients/Windows1.png)

2. A próxima página inicia o processo de entrada do Azure RemoteApp usando o Active Directory do Azure. Esse processo deve parecer familiar se você já usou os serviços da Microsoft no passado. Comece digitando seu **endereço de email** e clique em **Continuar**.

	![Primeiro prompt do Active Directory do Azure](./media/remoteapp-clients/Windows2.png)

3. Siga as instruções na tela para entrar com sua conta da Microsoft (LiveID) ou a ID da Organização. Depois de conectado, pode ser apresentada uma página listando todos os convites que você recebeu. Se estiver, selecione os convites que você confia e clique em **Concluído**.

	![Página de convites do cliente Azure RemoteApp](./media/remoteapp-clients/Windows3.png)

4. Após aceitar os convites, a lista de aplicativos que você terá acesso será baixada no dispositivo e disponibilizada no Centro de Conexões. Toque duas vezes em um dos aplicativos para iniciá-lo e começar a usá-lo.

	![Centro de Conexões do cliente do Azure RemoteApp](./media/remoteapp-clients/Windows4.png)

5. Não se preocupe se ninguém enviou um convite ainda, nós ajudamos você! Você ainda terá acesso a uma coleção de demonstração para testar o serviço.

	![Feed de demonstração do Azure RemoteApp](./media/remoteapp-clients/Windows5.png)

## Windows Phone 8,1

Depois de instalar o aplicativo de Área de Trabalho Remota da Microsoft a partir do armazenamento do Windows Phone 8.1, você o encontrará em sua lista de aplicativos na **Área de Trabalho Remota**.

1. Iniciar o aplicativo leva você a um Centro de Conexões vazio, a menos que você já tenha usado o aplicativo. Para começar com o Azure RemoteApp, toque no botão Adicionar **"" +""** na parte inferior da tela.

	![Centro de Conexões Vazio](./media/remoteapp-clients/WinPhone1.png)

2. Em seguida, toque em **Azure RemoteApp**.

	![Adicionar página de itens](./media/remoteapp-clients/WinPhone2.png)

3. Você precisa entrar com seu endereço de email para acessar o serviço. Para iniciar esse processo, toque em **Conectar**.

	![Prompt de logon](./media/remoteapp-clients/WinPhone3.png)

4. Na página seguinte, digite seu **endereço de email** e toque em **Continuar**. Isso inicia o processo de entrada usando o Active Directory do Azure.

	![Primeira página do Active Directory do Azure](./media/remoteapp-clients/WinPhone4.png)

5. Siga as instruções na tela para entrar com sua conta da Microsoft (LiveID) ou a ID da Organização. Depois de conectado, pode ser apresentada uma página listando todos os convites que você recebeu. Se estiver, selecione os convites que você confia e toque em **salvar**.

	![Página de convites](./media/remoteapp-clients/WinPhone5.png)

6. Após aceitar os convites, a lista de aplicativos que você terá acesso será baixada no dispositivo e disponibilizada no Centro de Conexões. Toque em um dos aplicativos para iniciá-lo e começar a usá-lo.

	![Centro de Conexões com um feed](./media/remoteapp-clients/WinPhone6.png)

7. Se você ainda não tiver um convite, pode experimentar o serviço. Para fazer isso, toque **sim** quando solicitado.

	![Prompt do feed de demonstração](./media/remoteapp-clients/WinPhone7.png)

8. Isso lhe dará acesso a um conjunto básico de aplicativos para começar com o RemoteApp.

	![Feed de demonstração do Azure RemoteApp](./media/remoteapp-clients/WinPhone8.png)
 

<!---HONumber=August15_HO6-->