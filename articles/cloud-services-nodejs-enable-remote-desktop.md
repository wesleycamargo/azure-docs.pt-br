<properties linkid="dev-node-remotedesktop" urlDisplayName="Habilitar a Área de Trabalho Remota" pageTitle="Habilitar a Área de Trabalho Remota para serviços de nuvem (Node.js)" metaKeywords="acesso remoto do Node.js do Azure, conexão remota do Node.js do Azure, acesso à VM do Node.js do Azure, acesso à máquina virtual do Node.js Azure" description="Saiba como habilitar acesso de Área de Trabalho Remota para as máquinas virtuais que hospedam seu aplicativo Node.js do Azure. " metaCanonical="" services="cloud-services" documentationCenter="Node.js" title="Habilitando a Área de Trabalho Remota no Azure" authors="" solutions="" manager="" editor="" />





# Habilitando a Área de Trabalho Remota no Azure

A Área de Trabalho Remota permite que você acesse a área de trabalho da instância de função em execução no Azure. Você pode usar uma conexão de área de trabalho remota para configurar a máquina virtual ou solucionar problemas de seu aplicativo.

<div class="dev-callout">
	<b>Observação</b>
	<p>As etapas deste artigo se aplicam somente a aplicativos de nó hospedados como um Serviço de Nuvem do Azure.</p>
	</div>

Esta tarefa inclui as seguintes etapas:

-   [Etapa 1: Configurar o serviço para acesso à Área de Trabalho Remota usando o PowerShell do Azure]
-   [Etapa 2: Conectar-se à instância da função]
-   [Etapa 3: Configurar o serviço para desabilitar o acesso à Área de Trabalho Remota usando o PowerShell do Azure]

## <a name="step1"> </a>Etapa 1: Configurar o serviço para acesso à Área de Trabalho Remota usando o PowerShell do Azure

Para usar a Área de Trabalho Remota, você precisa configurar a definição de seu serviço e a configuração do serviço com um nome de usuário, senha e certificado para autenticação com instâncias de função na nuvem. O [PowerShell do Azure] inclui o cmdlet **Enable-AzureServiceProjectRemoteDesktop** que faz essa configuração para você.

Execute as etapas a seguir no computador onde a definição do serviço foi criada.

1.  No menu **Iniciar**, selecione **PowerShell do Azure**.

	![Entrada no menu de início do PowerShell do Azure][powershell-menu]

2.  Altere o diretório para o diretório de serviço, digite
    **Enable-AzureServiceProjectRemoteDesktop** e, em seguida, digite um nome de usuário e uma senha a ser usada durante a autenticação com instâncias de função na nuvem.

	![enable-azureserviceprojectremotedesktop][enable-rdp]

3.  Publique as alterações na configuração do serviço na nuvem. No prompt do
    **PowerShell do Azure**, digite
    **Publish-AzureServiceProject**.

	![publish-azureserviceproject][publish-project]

Quando essas etapas forem concluídas, as instâncias de função do serviço na nuvem serão configuradas para acesso à Área de Trabalho Remota.

## <a name="step2"> </a>Etapa 2: Conectar-se à instância da função

Com sua implantação ativa e em execução no Azure, você pode se conectar à instância da função.

1.  No [Portal de Gerenciamento do Azure], selecione **Serviços de Nuvem** e, em seguida, o serviço implantado na Etapa 1 acima

	![portal de gerenciamento do azure][cloud-services]

2.  Clique em **Instâncias** e, em seguida, clique em **Produção** ou **Preparo** para ver as instâncias de seu serviço. Selecione uma instância e clique em **Conectar** na parte inferior da página.

    ![A página de instâncias][3]

2.  Quando você clica em **Conectar**, o navegador da web solicita que você salve um arquivo .rdp. Se estiver usando o Internet Explorer, clique em **Abrir**.

    ![solicitação para abrir ou salvar o arquivo .rdp][4]

3.  Quando o arquivo é aberto, o seguinte prompt de segurança é exibido:

    ![Prompt de segurança do Windows][5]

4.  Clique em **Conectar** e será exibido um prompt de segurança para a inserção das credenciais para acessar a instância. Digite a senha criada na [Etapa 1][Etapa 1: Configurar o serviço para acesso à Área de Trabalho Remota usando o PowerShell do Azure] e, em seguida, clique em **OK**.

    ![prompt de nome de usuário/senha][6]

Quando a conexão for feita, a Conexão de Área de Trabalho Remota exibe a área de trabalho da instância no Azure. Você obteve acesso remoto à sua instância com êxito e pode executar qualquer tarefa necessária para gerenciar o aplicativo.

![Sessão de área de trabalho remota][7]

## <a name="step3"> </a>Etapa 3: Configurar o serviço para desabilitar o acesso à Área de Trabalho Remota usando o PowerShell do Azure

Quando você não precisar mais de conexões de área de trabalho remota para as instâncias de função na nuvem, desabilite o acesso remoto à área de trabalho remota usando o [PowerShell do Azure]

1.  No menu **Iniciar**, selecione **PowerShell do Azure**.

2.  Altere o diretório para o diretório de serviço e digite
    **Disable-AzureServiceProjectRemoteDesktop**:

3.  Publique as alterações na configuração do serviço na nuvem. No prompt do **PowerShell do Azure**, digite **Publish-AzureServiceProject**.

## Recursos adicionais

- [Acessando instâncias de função remotamente no Azure] 
- [Usando a Área de Trabalho Remota com as funções do Azure]

  [Etapa 1: Configurar o serviço para acesso à Área de Trabalho Remota usando o PowerShell do Azure]: #step1
  [Etapa 2: Conectar-se à instância da função]: #step2
  [Etapa 3: Configurar o serviço para desabilitar o acesso à Área de Trabalho Remota usando o PowerShell do Azure]: #step3
  [PowerShell do Azure]: http://go.microsoft.com/?linkid=9790229&clcid=0x409

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
  
  [Acessando instâncias de função remotamente no Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh124107.aspx
  [Usando a Área de Trabalho Remota com as funções do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg443832.aspx

