---
title: Logon em uma VM do Azure clássica | Microsoft Docs
description: Use o portal do Azure para fazer logon na máquina virtual do Windows criada com o modelo clássico de implantação.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 3c1239ed-07dc-48b8-8b3d-dc8c5f0ab20e
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: 87ecc65d2d4802ae826f3260b66b26e0bbe414e6
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012352"
---
# <a name="log-on-to-a-windows-virtual-machine-using-the-azure-portal"></a>Faça logon em uma máquina virtual do Windows usando o Portal do Azure.
No Portal do Azure, você usará o botão **Conectar** para iniciar uma sessão de Área de Trabalho Remota e fazer logon em uma VM do Windows.

Deseja conectar-se a uma VM do Linux? Consulte [Como fazer logon em uma máquina virtual que esteja executando o Linux](../../linux/mac-create-ssh-keys.md).

<!--
Deleting, but not 100% sure
Learn how to [perform these steps using new Azure portal](../connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
-->

> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../../../resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação Clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos. Para obter informações sobre como fazer logon em uma VM usando o modelo do Resource Manager, consulte [aqui](../connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="connect-to-the-virtual-machine"></a>Conectar-se à máquina virtual
1. Entre no Portal do Azure.
2. Clique na máquina virtual que você quer acessar. O nome é listado no painel **Todos os recursos**.

    ![Virtual-machine-locations](./media/connect-logon/azureportaldashboard.png)

1. Clique no botão **Conectar** na página de propriedades da máquina virtual. 
2. Na página **Conectar à máquina virtual**, mantenha selecionadas as opções apropriadas e clique em **Fazer o download do arquivo RDP**.
2. Abra o arquivo RDP baixado e clique em **Conectar** quando solicitado. 
2. Você receberá um aviso de que o arquivo `.rdp` é proveniente de um editor desconhecido. Isso é normal. Na janela de Área de Trabalho Remota, clique em **Conectar** para continuar.
   
    ![Captura de tela de um aviso sobre um editor desconhecido.](./media/connect-logon/rdp-warn.png)
3. Na janela **Segurança do Windows**, selecione **Mais opções** e **Usar uma conta diferente**. Digite as credenciais de uma conta na máquina virtual e clique em **OK**.
   
     **Conta local** - normalmente, são o nome de usuário e a senha da conta local especificados por você no momento da criação da máquina virtual. Nesse caso, o domínio é o nome da máquina virtual e é inserido como *nomedavm*&#92;*nome de usuário*.  
   
    **VM Ingressada no Domínio** – se a VM pertencer a um domínio, digite o nome de usuário no formato *Domínio*&#92;*Nome de usuário*. A conta precisa estar no grupo Administradores ou ter privilégios de acesso remoto concedidos à VM.
   
    **Controlador de domínio** - se a VM for um controlador de domínio, digite o nome de usuário e a senha da conta de administrador para esse domínio.
4. Clique em **Sim** para verificar a identidade da máquina virtual e concluir o logon.
   
   ![Captura de tela mostrando uma mensagem sobre como verificar a identidade da VM.](./media/connect-logon/cert-warning.png)

## <a name="next-steps"></a>Próximas etapas
* Se o botão **Conectar** estiver inativo, ou se você estiver enfrentando outros problemas com a conexão de Área de Trabalho Remota, tente redefinir a configuração. Clique em **Redefinir acesso remoto** no painel da máquina virtual.

    ![Reset-remote-access](./media/connect-logon/virtualmachine_dashboard_reset_remote_access.png)

* Se houver problemas com a senha, tente redefini-la. Clique em **Redefinir senha** na borda esquerda do painel da virtual máquina, em **Suporte + Solução de problemas**.

    ![Reset-password](./media/connect-logon/virtualmachine_dashboard_reset_password.png)

Se essas dicas não funcionarem ou se não forem necessárias, consulte [Solucionar problemas de conexões da Área de Trabalho Remota com uma Máquina Virtual do Azure baseada no Windows](../troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Este artigo orienta você no diagnóstico e na solução de problemas comuns.
