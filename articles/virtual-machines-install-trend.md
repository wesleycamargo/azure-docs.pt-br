<properties title="How to install and configure Trend on an Azure VM" pageTitle="Como instalar e configurar o Trend Micro Deep Security as a Service em uma VM do Azure" description="Descreve a instalação e a configuração da segurança Trend Micro em uma máquina virtual no Azure" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" manager="timlt" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="na" ms.topic="article" ms.date="09/24/2014" ms.author="kathydav" />

#Como instalar e configurar o Trend Micro Deep Security as a Service em uma VM do Azure

<p> Este artigo mostra como instalar e configurar o Trend Micro Deep Security as a Service em uma VM (máquina virtual) nova ou existente com o Windows Server em execução. A proteção oferecida pelo Deep Security as a Service inclui proteção anti-malware, firewall, sistema de prevenção de intrusões e monitoramento de integridade. 

<p>O cliente é instalado como uma extensão de segurança usando-se o Agente de VM. Em uma máquina virtual nova, você instalará o Agente de VM com o Deep Security Agent. Em uma máquina virtual existente sem o Agente de VM, você precisará baixar e instalá-lo primeiro. Este artigo aborda ambas as situações.

<p> Se tiver uma assinatura existente da Trend para uma solução local, você poderá usá-la para proteger as máquinas virtuais do Azure. Se ainda não for cliente, você poderá fazer uma assinatura de avaliação. Para obter mais informações sobre essa solução, consulte a postagem no blog [Microsoft Azure VM Agent Extension For Deep Security](http://go.microsoft.com/fwlink/p/?LinkId=403945).

## Instalar o Deep Security Agent em uma máquina virtual nova

O [Portal de Gerenciamento do Azure](http://manage.windowsazure.com) permite instalar o Agente de VM e a extensão de segurança da Trend quando você usa a opção **Da Galeria** para criar a máquina virtual.Usar essa abordagem é uma maneira fácil de adicionar proteção da Trend caso você esteja criando uma única máquina virtual.

Essa opção **Da Galeria** abre um assistente que ajuda você a configurar a máquina virtual. Você usa a última página do assistente para instalar o Agente de VM e a extensão de segurança da Trend.Para obter instruções gerais, consulte [Criar uma máquina virtual na qual o Windows Server esteja em execução](http://go.microsoft.com/fwlink/p/?LinkId=403943). Quando chegar à última página do assistente, faça o seguinte:

1.	Em Agente de VM, marque **Instalar Agente de VM**.

2.	Em Extensões de Segurança, marque **Trend Micro Deep Security Agent**.

3.	Clique na marca de seleção para criar a máquina virtual.

	![Install the VM Agent and the Deep Security Agent](./media/virtual-machines-install-trend/InstallVMAgentandTrend.png)

## Instalar o Deep Security Agent em uma máquina virtual existente

Para isso, você precisará do seguinte:

- Módulo PowerShell do Azure, versão 0.8.2 ou mais nova. Para obter instruções e um link para a versão mais recente, consulte [Como instalar e configurar o PowerShell do Azure](http://go.microsoft.com/fwlink/p/?LinkId=320552).  

- O Agente de VM. Para obter instruções e um link para o download, consulte a postagem no blog [VM Agent and Extensions - Part 2](http://go.microsoft.com/fwlink/p/?LinkId=403947).

Abra uma sessão do PowerShell do Azure e execute os comandos a seguir. Não se esqueça de substituir os próprios valores para espaços reservados, como MyServiceName.

1.	Obtenha o nome do serviço de nuvem, o nome da máquina virtual e a VM e armazene todos eles em variáveis de forma que os próximos comandos possam usá-los:
	<p>`$servicename = MyServiceName`
	<p>`$name = MyVmName`
	<p>`$vm = Get-AzureVM -ServiceName $servicename -Name $name`

	> [WACOM.NOTE] Se você não souber o serviço de nuvem e o nome da VM, execute Get-AzureVM para exibir essas informações para todas as VMs na assinatura atual.

2.	Adicione o Deep Security Agent à máquina virtual:
<p> `Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA -VM $vm.VM`

	> [WACOM.NOTE] Se você quiser instalar uma versão específica, execute o seguinte comando para obter uma lista das versões disponíveis: `Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA`. Em seguida, inclua o parâmetro Version ao executar Set-AzureVMExtension.

3.	Atualize a VM, que instala o Deep Security Agent:
<p> `Update-AzureVM -ServiceName $servicename -Name $name -VM $vm.VM`


## Próximas etapas
Depois de ser instalado, o agente levará alguns minutos para começar a ser executado. Depois disso, você precisará ativar o Deep Security na máquina virtual de forma que ele possa ser gerenciado por um Deep Security Manager. Consulte o seguinte:

- Artigo da Trend sobre essa solução, [Instant-On Cloud Security for Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=404101).
- Uma [amostra de script do Windows PowerShell](http://go.microsoft.com/fwlink/?LinkId=404100) para configurar a máquina virtual.
- [Instruções](http://go.microsoft.com/fwlink/?LinkId=404099)  para a amostra.




##Recursos adicionais
[Como fazer logon em uma máquina virtual executando o Windows Server]

[Gerenciar extensões]


<!--Link references-->
[Como fazer logon em uma máquina virtual executando o Windows Server]: ../virtual-machines-log-on-windows-server/
[Gerenciar extensões]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409



<!--HONumber=35_1-->
