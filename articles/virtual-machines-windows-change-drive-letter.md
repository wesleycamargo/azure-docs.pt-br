<properties title="How To Change the Drive Letter of the Windows Temporary Disk" pageTitle="How To Change the Drive Letter of the Windows Temporary Disk" description="Describes how to remap the temporary disk on a Windows VM in Azure" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav" />

# Como alterar a letra da unidade do disco temporário do Windows

Você poderá alterar a letra da unidade do disco temporário, se usar a unidade D com essa finalidade. Você costuma fazer isso para permitir um aplicativo ou um serviço que usa a unidade D como um local de armazenamento permanente.

Antes de começar, verifique se você tem o seguinte:

-   Um disco de dados anexado que é possível usar para armazenar o arquivo de paginação do Windows (pagefile.sys) durante esse procedimento. Para obter instruções, consulte [Como anexar um disco de dados a uma Máquina Virtual do Windows][Como anexar um disco de dados a uma Máquina Virtual do Windows].
-   Um VHD carregado na conta de armazenamento, caso você queira usar um VHD de um disco de dados existente na unidade D. Para obter instruções, consulte as etapas 3 e 4 em [Criar e carregar um VHD do Windows Server no Azure][Criar e carregar um VHD do Windows Server no Azure].

## Alterar a letra da unidade

1.  Faça logon na máquina virtual.

2.  Mova pagefile.sys da unidade D para outra unidade.

3.  Reinicie a máquina virtual.

4.  Faça logon novamente e altere a letra da unidade de D para E.

5.  No [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], anexe um disco de dados existente ou esvazie o disco de dados.

6.  Faça logon na máquina virtual novamente, inicialize o disco e atribua D como a letra da unidade do disco que você acabou de anexar.

7.  Verifique se E está mapeada para o disco de armazenamento temporário.

8.  Mova pagefile.sys da outra unidade para a unidade E.

## Recursos adicionais

[Como fazer logon em uma máquina virtual executando o Windows Server][Como fazer logon em uma máquina virtual executando o Windows Server]

[Como desanexar um disco de dados de uma máquina virtual][Como desanexar um disco de dados de uma máquina virtual]

[O que é uma conta de armazenamento?][O que é uma conta de armazenamento?]

<!--Link references-->

  [Como anexar um disco de dados a uma Máquina Virtual do Windows]: ../storage-windows-attach-disk
  [Criar e carregar um VHD do Windows Server no Azure]: ../virtual-machines-create-upload-vhd-windows-server/
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [Como fazer logon em uma máquina virtual executando o Windows Server]: ../virtual-machines-log-on-windows-server/
  [Como desanexar um disco de dados de uma máquina virtual]: ../storage-windows-detach-disk/
