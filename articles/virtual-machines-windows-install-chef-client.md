<properties title="About Chef and Azure Virtual Machines" pageTitle="About Chef and Azure Virtual Machines" description="Describes installing and configuring Chef on a VM in Azure" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Sobre o Chef e as Máquinas Virtuais do Azure

Chef é um sistema de automação para compilar, implantar e gerenciar a infraestrutura. Os recursos são gerenciados usando-se receitas, que são definições reutilizáveis que fornecem instruções para tarefas como configurar um servidor Web.

Chef é um sistema cliente/servidor. Para saber as opções sobre como usar um servidor do Chef, consulte [Escolher a instalação][Escolher a instalação]. Você precisará de informações sobre o servidor do Chef para configurar o cliente. Para instalar o cliente do Chef em uma máquina virtual do Azure, você tem as seguintes opções:

-   Use o Portal de Gerenciamento para instalar o cliente do Chef ao criar uma máquina virtual na qual o Windows Server 2012 ou o Windows Server 2012 R2 está em execução. Para obter instruções, consulte [Portal do Microsoft Azure][Portal do Microsoft Azure].
-   Use o Windows PowerShell para instalar o cliente do Chef em uma máquina virtual existente. Um [script][script] de exemplo está disponível no Github.
-   Use um plug-in do Chef, [knife-azure][knife-azure], para criar uma instância de máquina virtual e instalar o cliente do Chef.

## Recursos adicionais

[Chef e Microsoft Azure][Chef e Microsoft Azure]

[Como fazer logon em uma máquina virtual executando o Windows Server][Como fazer logon em uma máquina virtual executando o Windows Server]

[Como fazer logon em uma máquina virtual executando o Linux][Como fazer logon em uma máquina virtual executando o Linux]

[Gerenciar extensões][Gerenciar extensões]

<!--Link references-->

  [Escolher a instalação]: http://www.getchef.com/chef/choose-your-version/
  [Portal do Microsoft Azure]: http://docs.opscode.com/azure_portal.html
  [script]: https://gist.github.com/kaustubh-d/cea1aa75baebd3615609
  [knife-azure]: http://docs.getchef.com/plugin_knife_azure.html
  [Chef e Microsoft Azure]: http://www.getchef.com/solutions/azure/
  [Como fazer logon em uma máquina virtual executando o Windows Server]: ../virtual-machines-log-on-windows-server/
  [Como fazer logon em uma máquina virtual executando o Linux]: ../virtual-machines-linux-how-to-log-on
  [Gerenciar extensões]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
