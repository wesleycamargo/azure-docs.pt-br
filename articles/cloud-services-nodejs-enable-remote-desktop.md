<properties 
	pageTitle="Habilitar área de trabalho remota para serviços de nuvem (Node.js)" 
	description="Saiba como habilitar o acesso à área de trabalho remota para as máquinas virtuais que hospedam o aplicativo Azure Node.js." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="wpickett"/>






# Habilitando a Área de Trabalho Remota no Azure

A Área de Trabalho Remota permite que você acesse a área de trabalho da instância de função
em execução no Azure. É possível usar uma conexão de área de trabalho remota para
configurar a máquina virtual ou solucionar problemas do seu
aplicativo.

> [AZURE.NOTE] As etapas deste artigo se aplicam somente a aplicativos de nó hospedados como um Serviço de Nuvem do Azure.

Esta tarefa inclui as seguintes etapas:

-   [Etapa 1: Configurar o serviço para acesso à Área de Trabalho Remota usando o PowerShell do Azure]
-   [Etapa 2: Conectar-se à instância da função]
-   [Etapa 3: Configurar o serviço para desabilitar o acesso à Área de Trabalho Remota
    usando o PowerShell do Azure]

## <a name="step1"> </a>Etapa 1: Configurar o serviço para acesso à Área de Trabalho Remota usando o PowerShell do Azure

Para usar a área de trabalho remota, você precisa configurar sua definição de serviço e
configuração de serviço com um nome de usuário, senha e certificado para
autenticar com instâncias de função na nuvem. [O PowerShell do Azure] inclui o cmdlet **Enable-AzureServiceProjectRemoteDesktop** que faz essa
configuração para você.

Execute as seguintes etapas no computador onde a definição do serviço
foi criada.

1.  No menu **Iniciar**, selecione **PowerShell do Azure**.

	![Azure PowerShell start menu entry][powershell-menu]

2.  Altere o diretório para o diretório de serviço, digite
    **Enable-AzureServiceProjectRemoteDesktop**e, em seguida, digite um nome de usuário e
    senha a ser usada durante a autenticação com instâncias de função na
    nuvem.

	![enable-azureserviceprojectremotedesktop][enable-rdp]

3.  Publique as alterações na configuração do serviço na nuvem. No
    prompt do **PowerShell do Azure**, digite
    **Publish-AzureServiceProject**.

	![publish-azureserviceproject][publish-project]

Quando essas etapas forem concluídas, as instâncias de função do serviço
na nuvem são configuradas para acesso de área de trabalho remota.

## <a name="step2"> </a>Etapa 2: Conectar-se à instância da função

Com a implantação ativa e em execução no Azure, é possível conectar
à instância de função.

1.  No [Portal de Gerenciamento do Azure], selecione **Serviços de Nuvem** e, em seguida, no serviço implantado na Etapa 1 acima

	![azure management portal][cloud-services]

2.  Clique em **Instâncias** e, em seguida, clique em **Produção** ou **Preparo** para ver as instâncias de seu serviço. Selecione uma instância e clique em **Conectar** na parte inferior da página.

    ![The instances page][3]

2.  Quando você clica em **Conectar**, o navegador da web solicita que você salve um
    arquivo .rdp. Se estiver usando o Internet Explorer, clique em **Abrir**.

    ![prompt to open or save the .rdp file][4]

3.  Quando o arquivo é aberto, o seguinte prompt de segurança é exibido:

    ![Windows security prompt][5]

4.  Clique em **Conectar**, e será exibido um aviso de segurança para inserir
    credenciais para acessar a instância. Digite a senha que você criou
    na [Etapa 1][Etapa 1: Configure o serviço para acesso de área de trabalho remota usando o PowerShell do Azure]e, em seguida, clique em **OK**.

    ![username/password prompt][6]

Quando a conexão é feita, a conexão de área de trabalho remota exibe a
área de trabalho da instância no Azure. Você teve um acesso remoto com ganho com êxito
para sua instância e pode executar qualquer tarefa necessária para
gerenciar o aplicativo.

![Remote desktop session][7]

## <a name="step3"> </a>Etapa 3: Configurar o serviço para desabilitar o acesso à Área de Trabalho Remota usando o PowerShell do Azure

Quando você não precisar mais de conexões de área de trabalho remota para as instâncias de função
na nuvem, desabilite o acesso de área de trabalho remoto usando o [PowerShell do Azure]

1.  No menu **Iniciar**, selecione **PowerShell do Azure**.

2.  Altere o diretório para o diretório de serviço e digite
    **Disable-AzureServiceProjectRemoteDesktop**:

3.  Publique as alterações na configuração do serviço na nuvem. No
    prompt do **PowerShell do Azure**, digite
    **Publish-AzureServiceProject**:

## Recursos adicionais

- [Acessando instâncias de função remotamente no Azure] 
- [Usando a Área de Trabalho Remota com as funções do Azure]

  [Etapa 1: Configurar o serviço para acesso à Área de Trabalho Remota usando o PowerShell do Azure]: #step1
  [Etapa 2: Conectar-se à instância da função]: #step2
  [Etapa 3: Configurar o serviço para desabilitar o acesso à Área de Trabalho Remota usando o PowerShell do Azure]: #step3
  [O PowerShell do Azure]: http://go.microsoft.com/?linkid=9790229&clcid=0x409

[Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
[powershell-menu]: ./media/cloud-services-nodejs-enable-remote-desktop/azure-powershell-menu.png
[publish-project]: ./media/cloud-services-nodejs-enable-remote-desktop/publish-rdp.png
[enable-rdp]: ./media/cloud-services-nodejs-enable-remote-desktop/enable-rdp.png
[cloud-services]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-services-remote.png
  [3]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-service-instance.png
  [4]: ./media/cloud-services-nodejs-enable-remote-desktop/rdp-open.png
  [5]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-12.png
  [6]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-13.png
  [7]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-14.png
  
  [Acessando instâncias de função remotamente no Azure]: http://msdn.microsoft.com/library/windowsazure/hh124107.aspx
  [Usando a Área de Trabalho Remota com as funções do Azure]: http://msdn.microsoft.com/library/windowsazure/gg443832.aspx

<!--HONumber=45--> 
