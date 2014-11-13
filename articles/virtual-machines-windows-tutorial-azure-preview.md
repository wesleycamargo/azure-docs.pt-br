<properties urlDisplayName="Create a virtual machine in the Preview Portal" pageTitle="Criar uma m&aacute;quina virtual executando o Windows no Portal de Visualiza&ccedil;&atilde;o do Azure" metaKeywords="Azure image gallery vm" description="Saiba como criar uma m&aacute;quina virtual (VM) do Azure executando o Windows, usando a Galeria de VMs do Portal de Visualiza&ccedil;&atilde;o do Azure" metaCanonical="" services="virtual-machines" documentationCenter="" title="Criar uma m&aacute;quina virtual executando o Windows no Portal de Visualiza&ccedil;&atilde;o do Azure" authors="danlep,kathydav,rasquill" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="09/16/2014" ms.author="danlep,kathydav,rasquill" />

# Criar uma máquina virtual executando o Windows no Portal de Visualização do Azure

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/virtual-machines-windows-tutorial/" title="Portal do Azure">Portal do Azure</a><a href="/pt-br/documentation/articles/virtual-machines-windows-tutorial-azure-preview/" title="Portal de visualiza&ccedil;&atilde;o do Azure" class="current">Portal de visualiza&ccedil;&atilde;o do Azure</a></div>

Este tutorial mostra como é fácil criar uma máquina virtual (VM) no Azure executando o Windows, usando como exemplo uma imagem do Windows Server da Galeria de Imagens no Portal de Visualização do Azure. A Galeria oferece várias imagens, inclusive sistema operacionais Windows, sistemas operacionais baseados em Linux e imagens de aplicativos.

> [WACOM.NOTE] Não é necessária nenhuma experiência com VMs do Azure para concluir este tutorial. Porém, você precisa de uma conta do Azure. Você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Criar uma conta do Azure][Criar uma conta do Azure].

Este tutorial mostra:

-   [Como criar a máquina virtual][Como criar a máquina virtual]
-   [Como fazer o logon na máquina virtual após você criá-la][Como fazer o logon na máquina virtual após você criá-la]

Para saber mais, consulte [Máquinas Virtuais][Máquinas Virtuais].

## <span id="createvirtualmachine"></span> </a>Como criar a máquina virtual

Esta seção mostra como usar o Portal de Visualização para criar uma VM, usando o Windows Server como exemplo. É possível usar as configurações padrão do Azure na maior parte da configuração e criar a VM em questão de minutos.

> [WACOM.NOTE] O número e o tipo de imagens que são disponibilizadas na galeria dependem do tipo de assinatura que você tem. Este tutorial usa uma imagem do Windows Server, mas uma assinatura do MSDN pode ter imagens adicionais disponíveis, incluindo imagens da área de trabalho.

1.  Entre no [Portal de Visualização do Azure][Portal de Visualização do Azure]. Confira a oferta [Avaliação Gratuita][Avaliação Gratuita] caso você ainda não tenha uma assinatura.

2.  No menu Ajuda, clique em **Novo**.

    ![Selecionar Novo na barra de comandos][Selecionar Novo na barra de comandos]

3.  Em **Novo**, clique em **Tudo** e, em **Galeria**, clique em **Máquinas virtuais**. Clique em **Windows Server 2012 R2 Datacenter**. Depois clique em **Criar**.

    ![Selecionar uma imagem de VM na Galeria][Selecionar uma imagem de VM na Galeria]

4.  Na faixa **Criar VM**, preencha o **Nome de Host** desejado para a VM, o **Nome de Usuário** administrativo e uma **Senha** forte.

    ![Configurar nome de host e credenciais de logon][Configurar nome de host e credenciais de logon]

    > [WACOM.NOTE] **Nome do Usuário** se refere à conta administrativa que você usa para gerenciar o servidor. Crie uma senha exclusiva para essa conta e não se esqueça dela. **Você precisará do nome do usuário e da senha para fazer logon na máquina virtual**.

5.  Para usar as configurações padrão para as demais opções de VM e começar a criar a VM, clique em **Criar**. Mas, se quiser, antes de clicar em **Criar**, você poderá explorar as configurações de **Configuração Opcional**. Por exemplo, é possível configurar um diagnóstico opcional na VM para acompanhar várias métricas na VM depois. Clique em **Configuração Opcional**, em **Diagnóstico** e alterne **Status** para **Ativado**.

    ![Ativar diagnóstico da VM][Ativar diagnóstico da VM]

    > [WACOM.NOTE] Se você quiser ativar o Diagnóstico do Azure, o Azure armazenará os dados de diagnóstico em uma conta de armazenamento do Azure, resultando em um custo de armazenamento adicional.

6.  Enquanto o Azure cria a VM, é possível acompanhar o progresso em **Notificações**, no menu Hub. Depois que o Azure criar a VM, você a verá no quadro inicial.

    ![A VM é exibida no quadro inicial][A VM é exibida no quadro inicial]

## <span id="logon"></span> </a>Como fazer o logon na máquina virtual após você criá-la

Esta seção mostra como fazer logon na VM de forma que você possa gerenciar as configurações e os aplicativos que serão executados nela.

> [WACOM.NOTE] Para obter requisitos e dicas para solução de problemas, consulte [Conectar-se a uma máquina virtual do Azure com protocolo RDP ou SSH][Conectar-se a uma máquina virtual do Azure com protocolo RDP ou SSH].

1.  Se você ainda não fez isso, entre no [Portal de Visualização do Azure][Portal de Visualização do Azure].

2.  Clique na VM no quadro inicial. Se você precisar encontrá-lo, clique em **Procurar** e em **Máquinas virtuais**.

    ![Procurar a VM][Procurar a VM]

3.  Na faixa da VM, clique em **Conectar** na parte superior.

    ![Faça logon na máquina virtual][Faça logon na máquina virtual]

4.  Clique em **Abrir** para usar o arquivo do protocolo RDP criado automaticamente para a máquina virtual.

5.  Clique em **Conectar** para prosseguir com o processo de conexão.

    ![Continuar com a conexão][Continuar com a conexão]

6.  Digite o nome do usuário e a senha da conta administrativa na máquina virtual e, em seguida, clique em **OK**

7.  Clique em **Sim** para verificar a identidade da máquina virtual.

    ![Verificar a identidade do computador][Verificar a identidade do computador]

    Você agora pode trabalhar com a máquina virtual, como faria com qualquer outro servidor.

## Próximas etapas

Para saber mais sobre a configuração de máquinas virtuais do Windows no Azure, consulte os seguintes artigos:

[Como conectar máquinas virtuais em um serviço de nuvem][Como conectar máquinas virtuais em um serviço de nuvem]

[Como criar e carregar um Disco Rígido Virtual que contém o sistema operacional Windows Server][Como criar e carregar um Disco Rígido Virtual que contém o sistema operacional Windows Server]

[Anexar um disco de dados à máquina virtual][Anexar um disco de dados à máquina virtual]

[Gerenciar a disponibilidade de máquinas virtuais][Gerenciar a disponibilidade de máquinas virtuais]

[Sobre as definições de configuração da VM do Azure][Sobre as definições de configuração da VM do Azure]

  [Criar uma conta do Azure]: http://www.windowsazure.com/pt-br/develop/php/tutorials/create-a-windows-azure-account/
  [Como criar a máquina virtual]: #createvirtualmachine
  [Como fazer o logon na máquina virtual após você criá-la]: #logon
  [Máquinas Virtuais]: http://go.microsoft.com/fwlink/p/?LinkID=271224
  [Portal de Visualização do Azure]: https://portal.azure.com
  [Avaliação Gratuita]: http://www.windowsazure.com/pt-br/pricing/free-trial/
  [Selecionar Novo na barra de comandos]: ./media/virtual-machines-windows-tutorial-azure-preview/new_button_preview_portal.png
  [Selecionar uma imagem de VM na Galeria]: ./media/virtual-machines-windows-tutorial-azure-preview/image_gallery_preview_portal.png
  [Configurar nome de host e credenciais de logon]: ./media/virtual-machines-windows-tutorial-azure-preview/create_vm_preview_portal.png
  [Ativar diagnóstico da VM]: ./media/virtual-machines-windows-tutorial-azure-preview/vm_diagnostics_status_preview_portal.png
  [A VM é exibida no quadro inicial]: ./media/virtual-machines-windows-tutorial-azure-preview/vm_startboard_preview_portal.png
  [Conectar-se a uma máquina virtual do Azure com protocolo RDP ou SSH]: http://go.microsoft.com/fwlink/p/?LinkId=398294
  [Procurar a VM]: ./media/virtual-machines-windows-tutorial-azure-preview/browse_vm_preview_portal.png
  [Faça logon na máquina virtual]: ./media/virtual-machines-windows-tutorial-azure-preview/connect_vm_preview_portal.png
  [Continuar com a conexão]: ./media/virtual-machines-log-on-windows-server/connectpublisher.png
  [Verificar a identidade do computador]: ./media/virtual-machines-log-on-windows-server/connectverify.png
  [Como conectar máquinas virtuais em um serviço de nuvem]: http://www.windowsazure.com/pt-br/documentation/articles/cloud-services-connect-virtual-machine/
  [Como criar e carregar um Disco Rígido Virtual que contém o sistema operacional Windows Server]: http://www.windowsazure.com/pt-br/documentation/articles/virtual-machines-create-upload-vhd-windows-server/
  [Anexar um disco de dados à máquina virtual]: http://www.windowsazure.com/pt-br/documentation/articles/storage-windows-attach-disk/
  [Gerenciar a disponibilidade de máquinas virtuais]: http://www.windowsazure.com/pt-br/documentation/articles/manage-availability-virtual-machines/
  [Sobre as definições de configuração da VM do Azure]: http://msdn.microsoft.com/library/azure/dn763935.aspx
