---
title: Instalar o Symantec Endpoint Protection em uma VM | Microsoft Docs
description: Saiba como instalar e configurar a extensão de segurança do Symantec Endpoint Protection em uma VM do Azure nova ou existente criada com o modelo clássico de implantação.
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: timlt
editor: ''
tags: azure-service-management

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: iainfou

---
# Como instalar e configurar o Symantec Endpoint Protection em uma VM do Windows
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Este artigo mostra como instalar e configurar o cliente do Symantec Endpoint Protection em uma VM (máquina virtual) existente com o Windows Server. Trata-se do cliente completo, que inclui serviços como proteção contra vírus e spyware, firewall e prevenção de intrusão. O cliente é instalado como uma extensão de segurança usando-se o Agente de VM.

Se tiver uma assinatura da Symantec para uma solução local, você poderá usá-la para proteger as máquinas virtuais do Azure. Se ainda não for cliente, você poderá se inscrever em uma assinatura de avaliação. Para obter mais informações sobre essa solução, consulte [Symantec Endpoint Protection na plataforma Azure da Microsoft][Symantec]. Essa página também fornece links para informações de licenciamento e instruções para instalar o cliente se você já for um cliente da Symantec.

## Instalar o Symantec Endpoint Protection em uma VM existente
Antes de começar, você precisa do seguinte:

* O módulo Azure PowerShell, versão 0.8.2 ou mais recente, em seu computador de trabalho. Você pode verificar a versão do PowerShell do Azure instalado com o comando **Get-Module azure | format-table version**. Para obter instruções e um link para a versão mais recente, consulte [Como instalar e configurar o PowerShell do Azure][PS]. Faça logon em sua assinatura do Azure usando `Add-AzureAccount`.
* O Agente de VM em execução na Máquina Virtual do Azure.

Primeiro, verifique se o Agente de VM já está instalado na máquina virtual. Preencha o nome do serviço de nuvem e o nome da máquina virtual e, em seguida, execute os seguintes comandos em um prompt de comando do Azure PowerShell com nível de administrador. Substitua tudo entre aspas, incluindo os caracteres < e >.

> [!TIP]
> Se você não souber o nome da máquina virtual e do serviço de nuvem, execute **Get-AzureVM** para listar os nomes de todas as máquinas virtuais em sua assinatura atual.
> 
> 

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

Se o comando **write-host** exibir **True**, o agente de VM está instalado. Se ele exibir **False**, confira as instruções e um link para download na postagem do blog do Azure [Agente de VM e extensões - parte 2][Agent].

Se o Agente de VM Agent estiver instalado, execute estes comandos para instalar o agente Symantec Endpoint Protection.

    $Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

    Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection -VM $vm | Update-AzureVM

Para verificar se a extensão de segurança Symantec foi instalada e está atualizada:

1. Faça logon na máquina virtual. Para obter instruções, consulte [Como fazer logon em uma máquina virtual que executa o Windows Server][Logon].
2. Para Windows Server 2008 R2, clique em **Iniciar > Symantec Endpoint Protection**. Para o Windows Server 2012 ou Windows Server 2012 R2, na tela inicial, digite **Symantec** e, em seguida, clique em **Symantec Endpoint Protection**.
3. Na guia Status da janela de **Status** do **Status-Symantec Endpoint Protection**, aplique atualizações ou reinicie se necessário.

## Recursos adicionais
[Como fazer logon em uma máquina virtual executando o Windows Server][Logon]

[Recursos e extensões de VM do Azure][Ext]

<!--Link references-->
[Symantec]: http://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Portal]: http://manage.windowsazure.com

[Create]: virtual-machines-windows-classic-tutorial.md

[PS]: ../powershell-install-configure.md

[Agent]: http://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]: virtual-machines-windows-classic-connect-logon.md

[Ext]: http://go.microsoft.com/fwlink/p/?linkid=390493

<!---HONumber=AcomDC_0831_2016-->