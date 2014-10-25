<properties title="How to install and configure Symantec Endpoint Protection on an Azure VM" pageTitle="How to install and configure Symantec Endpoint Protection on an Azure VM" description="Describes installing and configuring Symantec Endpoint Protection on a VM in Azure" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Como instalar e configurar o Symantec Endpoint Protection em uma VM do Azure

Este artigo mostra como instalar e configurar o cliente do Symantec Endpoint Protection em uma VM (máquina virtual) nova ou existente com o Windows Server em execução. Trata-se do cliente completo, que inclui serviços como proteção contra vírus e spyware, firewall e prevenção de intrusão.

O cliente é instalado como uma extensão de segurança usando-se o Agente de VM. Em uma máquina virtual nova, você instalará o agente com o cliente do ponto de extremidade. Em uma máquina virtual existente sem o agente, você precisará baixar e instalá-lo primeiro. Este artigo aborda ambas as situações.

Se você tiver uma assinatura existente da Symantec <for an on-premises solution, you can use it to protect your azure virtual machines> Se ainda não for cliente, você poderá fazer uma assinatura de avaliação. Para obter mais informações sobre essa solução, consulte [Symantec Endpoint Protection na plataforma Azure da Microsoft][Symantec Endpoint Protection na plataforma Azure da Microsoft]. Essa página também fornece links para informações de licenciamento e instruções alternativas para instalar o cliente se você já for um cliente da Symantec.

## Instalar o Symantec Endpoint Protection em uma máquina virtual nova

O [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure] permite instalar o Agente de VM e a extensão de segurança da Symantec quando você usa a opção **Da Galeria** para criar a máquina virtual. Usar essa abordagem é uma maneira fácil de adicionar proteção da Symantec caso você esteja criando uma única máquina virtual.

Essa opção **Da Galeria** abre um assistente que ajuda você a configurar a máquina virtual. Você usa a última página do assistente para instalar o Agente de VM e a extensão de segurança da Symantec.

Para obter instruções gerais, consulte [Criar uma máquina virtual na qual o Windows Server esteja em execução][Criar uma máquina virtual na qual o Windows Server esteja em execução]. Quando chegar à última página do assistente, faça o seguinte:

1.  Em Agente de VM, marque **Instalar Agente de VM**.

2.  Em Extensões de Segurança, marque **Symantec Endpoint Protection**.

    ![Instalar o Agente de VM e o Endpoint Protection Client][Instalar o Agente de VM e o Endpoint Protection Client]

3.  Clique na marca de seleção na parte inferior da página para criar a máquina virtual.

## Instalar o Symantec Endpoint Protection em uma máquina virtual existente

Para instalar o Deep Security Agent em uma máquina virtual existente, você precisará do seguinte:

-   Módulo PowerShell do Azure, versão 0.8.2 ou mais nova. Para obter instruções e um link para a versão mais recente, consulte [Como instalar e configurar o PowerShell do Azure][Como instalar e configurar o PowerShell do Azure].

-   O Agente de VM. Para obter instruções e um link para o download, consulte a postagem no blog [VM Agent and Extensions - Part 2][VM Agent and Extensions - Part 2].

Abra uma sessão do PowerShell do Azure e execute os comandos a seguir. Não se esqueça de substituir os próprios valores para espaços reservados, como MyServiceName.

1.  Obtenha o nome do serviço de nuvem, o nome da máquina virtual e a VM e armazene todos eles em variáveis de forma que os próximos comandos possam usá-los:

    `$servicename = MyServiceName`

    `$name = MyVmName`

    `$vm = Get-AzureVM -ServiceName $servicename -Name $name`

    > [WACOM.NOTE] Se você não souber o serviço de nuvem e o nome da VM, execute Get-AzureVM para exibir essas informações para todas as VMs na assinatura atual.

2.  Adicione o Symantec Endpoint Protection Agent à máquina virtual. Para instalar a versão mais recente, execute:

    `Set-AzureVMExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection -VM $vm.VM`

    > [WACOM.NOTE] Se você quiser instalar uma versão específica, execute o seguinte comando para obter uma lista das versões disponíveis: `Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection`.
    > Em seguida, inclua o parâmetro Version ao executar Set-AzureVMExtension.

3.  Atualize a VM, que instala o Symantec Endpoint Protection Agent:

    `Update-AzureVM -ServiceName $servicename -Name $name -VM $vm.VM`

## Recursos adicionais

[Como fazer logon em uma máquina virtual executando o Windows Server][Como fazer logon em uma máquina virtual executando o Windows Server]

[Gerenciar extensões][Gerenciar extensões]

<!--Link references-->

  [Symantec Endpoint Protection na plataforma Azure da Microsoft]: http://go.microsoft.com/fwlink/p/?LinkId=403942
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [Criar uma máquina virtual na qual o Windows Server esteja em execução]: http://go.microsoft.com/fwlink/p/?LinkId=403943
  [Instalar o Agente de VM e o Endpoint Protection Client]: ./media/virtual-machines-install-symantec/InstallVMAgentandSymantec.png
  [Como instalar e configurar o PowerShell do Azure]: http://go.microsoft.com/fwlink/p/?LinkId=320552
  [VM Agent and Extensions - Part 2]: http://go.microsoft.com/fwlink/p/?LinkId=403947
  [Como fazer logon em uma máquina virtual executando o Windows Server]: ../virtual-machines-log-on-windows-server/
  [Gerenciar extensões]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
