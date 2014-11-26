<properties title="About Puppet and Azure Virtual Machines" pageTitle="About Puppet and Azure Virtual Machines" description="Describes installing and configuring Puppet on a VM in Azure" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav" />

# Sobre o Puppet e as Máquinas Virtuais do Azure

Puppet Enterprise é um software de automação para compilar, implantar e gerenciar a infraestrutura. É possível usá-lo para gerenciar o ciclo de vida da infraestrutura de TI, inclusive: descoberta, provisionamento, sistema operacional e gerenciamento de configurações do aplicativo, orquestração e relatórios.

Puppet é um sistema cliente/servidor. O Puppet Master e o Puppet Enterprise Agent estão disponíveis para instalação por meio do Azure:

-   O Puppet Master está disponível como uma imagem pré-configurada, instalada em um servidor Ubuntu. Também é possível instalar o Puppet Enterprise em um servidor existente, mas usando a imagem da maneira mais simples como introdução. Você precisará de informações sobre o servidor para configurar o agente.
-   O Puppet Enterprise Agent está disponível como uma extensão de máquina virtual que você pode instalar ao criar uma máquina virtual ou instalar em uma máquina virtual existente.

Para obter instruções, baixe o “Guia de introdução" disponível na página [Microsoft Windows e Azure][Microsoft Windows e Azure].

## Recursos adicionais

[Novas integrações com o Microsoft Azure e o Visual Studio][Novas integrações com o Microsoft Azure e o Visual Studio]

[Como fazer logon em uma máquina virtual executando o Windows Server][Como fazer logon em uma máquina virtual executando o Windows Server]

[Como fazer logon em uma máquina virtual executando o Linux][Como fazer logon em uma máquina virtual executando o Linux]

[Gerenciar extensões][Gerenciar extensões]

<!--Link references-->

  [Microsoft Windows e Azure]: http://puppetlabs.com/solutions/microsoft
  [Novas integrações com o Microsoft Azure e o Visual Studio]: http://puppetlabs.com/blog/new-integrations-windows-azure-and-visual-studio
  [Como fazer logon em uma máquina virtual executando o Windows Server]: ../virtual-machines-log-on-windows-server/
  [Como fazer logon em uma máquina virtual executando o Linux]: ../virtual-machines-linux-how-to-log-on
  [Gerenciar extensões]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
