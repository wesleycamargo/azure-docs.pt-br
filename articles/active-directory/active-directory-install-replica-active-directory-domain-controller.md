---
title: "Instale os controladores de domínio de réplica do domínio Active Directory local nas máquinas virtuais do Azure | Microsoft Docs"
description: "Como instalar os controladores de domínio de réplica para um domínio Active Directory local em máquinas virtuais (VMs) do Azure numa rede virtual do Azure."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 8c9ebf1b-289a-4dd6-9567-a946450005c0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 60839f93954bfe38f0346b235259f68e479b8a00
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2017
---
# <a name="install-a-replica-active-directory-domain-controller-in-an-azure-virtual-network"></a>Instalar uma Réplica do Controlador de Domínio do Active Directory em uma rede virtual do Azure
Este artigo discute como instalar os controladores de domínio adicionais (DCs) a serem usados como controladores de domínio de réplica para um domínio Active Directory local em máquinas virtuais (VMs) do Azure numa rede virtual do Azure. Você também pode [instalar uma floresta do Active Directory do Servidor do Windows em uma rede virtual do Azure](active-directory-new-forest-virtual-machine.md). Para saber como instalar os Serviços de Domínio do Active Directory (AD DS) em uma rede virtual do Azure, consulte [Diretrizes para implantar o Active Directory do Windows Server em máquinas virtuais do Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx).

## <a name="scenario-diagram"></a>Diagrama do cenário
Nesse cenário, os usuários externos precisam acessar os aplicativos que são executados ingressados no domínio. As VMs que executam os servidores de aplicativos e os controladores de domínio de réplica são instaladas em uma rede virtual do Azure. A rede virtual pode ser conectada à rede local pelo [ExpressRoute](../expressroute/expressroute-locations-providers.md), ou você pode usar uma conexão [VPN site a site](../vpn-gateway/vpn-gateway-site-to-site-create.md), conforme apresentado: 

![Diagrama de uma réplica de controlador de domínio do Active Directory em uma rede virtual no Azure][1]

Os servidores de aplicativos e os controladores de domínio são implantados em serviços de nuvem separados para distribuir o processamento de computação e em conjuntos de disponibilidade para melhorar a tolerância a falhas.
Os DCs se replicam entre si e com os DCs locais por meio da replicação do Active Directory. Nenhuma ferramenta de sincronização é necessária.

## <a name="create-an-on-premises-active-directory-site-for-the-azure-virtual-network"></a>Crie um site do Active Directory local para a rede virtual do Azure
Você pode criar um site no Active Directory que represente a região de rede correspondente à rede virtual. Esse site pode ajudar a otimizar a autenticação, replicação e outras operações de localização do controlador de domínio. As etapas a seguir explicam como criar um site e, para obter mais informações, consulte [Adicionando um novo Site](https://technet.microsoft.com/library/cc781496.aspx).

1. Abra Serviços e Sites do Active Directory: **Gerenciador do Servidor** > **Ferramentas** > **Serviços e Sites do Active Directory**.
2. Crie um site para representar a região onde você criou uma rede virtual do Azure: clique em **Sites** > **Ação** > **Novo site** > digite o nome do novo site, como Oeste dos EUA do Azure> selecione um link de site > **OK**.
3. Crie uma sub-rede e associe-a ao novo site: clique duas vezes em **Sites** > clique com o botão direito do mouse em **Sub-redes** > **Nova sub-rede** > digite o intervalo de endereços IP da rede virtual (como 10.1.0.0/16 no diagrama do cenário) > selecione o novo site do Azure > **OK**.

## <a name="create-an-azure-virtual-network"></a>Crie uma rede virtual do Azure
Para criar uma rede virtual do Azure e configurar o VPN site a site, siga as etapas inclusas no artigo [Criar uma conexão Site a Site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). 

Você também pode configurar o gateway de rede virtual para criar uma conexão VPN site a site segura. Crie a conexão VPN site a site entre a nova rede virtual e um dispositivo VPN local. Para obter instruções, consulte [Configurar um Gateway de Rede Virtual](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md).

## <a name="create-azure-vms-for-the-dc-roles"></a>Crie máquinas virtuais do Azure para as funções de controlador de domínio
Para criar máquinas virtuais para hospedar a função de controlador de domínio, repita as etapas em [Criar uma máquina virtual do Windows com o portal do Azure](../virtual-machines/windows/quick-create-portal.md) conforme necessário. Implante pelo menos dois controladores de domínio virtuais para fornecer redundância e tolerância à falhas. Se a rede virtual do Azure tiver pelo menos dois controladores de domínio configurados da mesma forma, você pode colocar as VMs que executam esses controladores de domínio em uma conjunto de disponibilidade para melhorar a tolerância a falhas.

Para criar as máquinas virtuais usando o Windows PowerShell em vez do portal do Azure, consulte [Usar o Azure PowerShell para criar e pré-configurar máquinas virtuais baseadas em Windows](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Reserve um endereço IP estático para VMs que executarão a função de controlador de domínio. Para reservar um endereço IP estático, baixe o Microsoft Web Platform Installer, [instale o PowerShell do Azure](/powershell/azure/overview) e execute o cmdlet Set-AzureStaticVNetIP. Por exemplo:

````
Get-AzureVM -ServiceName AzureDC1 -Name AzureDC1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4 | Update-AzureVM
````
Para obter mais informações sobre como definir um endereço IP estático, consulte [Configurar um endereço IP interno estático para uma VM](../virtual-network/virtual-networks-reserved-private-ip.md).

## <a name="install-ad-ds-on-azure-vms"></a>Instale o AD DS em máquinas virtuais do Azure
Conecte-se a uma VM e verifique se tem conectividade através da conexão VPN site a site ou ExpressRoute para recursos em sua rede local. Em seguida, instale o AD DS nas VMs do Azure. Você pode usar o mesmo processo que você usa para instalar um controlador de domínio adicional na sua rede local (interface do usuário, Windows PowerShell ou um arquivo de resposta). Enquanto instala o AD DS, certifique-se de especificar o novo volume para a localização do banco de dados, logs e SYSVOL do AD. Se você precisar de um atualizador na instalação do AD DS, consulte [Instalar os Active Directory Domain Services (nível 100)](https://technet.microsoft.com/library/hh472162.aspx) ou [Instalar um Controlador de Domínio de Réplica do Windows Server 2012 em um Domínio Existente (nível 200)](https://technet.microsoft.com/library/jj574134.aspx).

## <a name="reconfigure-dns-server-for-the-virtual-network"></a>Reconfigure o servidor DNS para a rede virtual
1. Para obter uma lista de nomes de rede virtual, no [Portal do Azure](https://portal.azure.com), procure por *Redes virtuais* e, em seguida, selecione **Redes virtuais** para exibir a lista. 
2. Abra a rede virtual que você desejar gerenciar e, em seguida, [reconfigure os endereços IP do servidor DNS para a sua rede virtual](../virtual-network/virtual-network-manage-network.md#dns-servers) para usar os endereços IP estáticos atribuídos aos controladores de domínio de réplica em vez de endereços IP de servidores DNS locais.
3. Para garantir que todas as máquinas virtuais do DC da réplica na rede virtual estejam configurados para usar os servidores DNS na rede virtual:
  1. Selecione **Máquinas Virtuais do Microsoft Azure**.
  2. Selecione as máquinas virtuais e selecione **Reiniciar**. 
  3. Aguarde até que a máquina virtual esteja em **Execução** novamente e faça logon nela.

## <a name="create-vms-for-application-servers"></a>Crie VMs para servidores de aplicativos

Para criar máquinas virtuais para hospedar a função do servidor do aplicativo, repita as etapas em [Criar uma máquina virtual do Windows com o portal do Azure](../virtual-machines/windows/quick-create-portal.md) conforme necessário. Para criar as máquinas virtuais usando o Microsoft PowerShell em vez do portal do Azure, consulte [Usar o Azure PowerShell para criar e configurar máquinas virtuais baseadas em Windows](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). A tabela a seguir contém as configurações sugeridas.

| Configuração | Valores |
| --- | --- |
|  **Escolha uma imagem** | Windows Server 2012 R2 Datacenter |
|  **Configuração de máquina virtual** |<p>Nome da Máquina Virtual: digite um nome de rótulo único (como AppServer1).</p><p>Novo nome de usuário: digite o nome de um usuário. Esse usuário será um membro do grupo local de Administradores na VM. Você precisará desse nome para entrar na Máquina Virtual pela primeira vez. A conta interna chamada Administrador não funcionará.</p><p>Nova Senha/Confirmar: digite uma senha</p> |
|  **Configuração de máquina virtual** |<p>Serviço de Nuvem: escolha **Criar um novo serviço de nuvem** para a primeira VM e selecione esse mesmo nome de serviço de nuvem ao criar mais VMs que hospedarão o aplicativo.</p><p>Nome DNS do Serviço de Nuvem: especifique um nome global exclusivo</p><p>Região/Grupo de Afinidade/Rede Virtual: especifique o nome da rede virtual (como WestUSVNet).</p><p>Conta de Armazenamento: escolha **Usar uma conta de armazenamento gerada automaticamente** para a primeira VM e selecione esse nome de conta de armazenamento ao criar mais VMs que hospedarão o aplicativo.</p><p>Conjunto de Disponibilidade: escolha **Criar um conjunto de disponibilidade**.</p><p>Nome do conjunto de disponibilidade: digite um nome para o conjunto disponibilidade ao criar a primeira VM e, em seguida, selecione esse mesmo nome quando você criar mais VMs.</p> |
|  **Configuração de máquina virtual** |<p>Selecione <b>Instalar o Agente de VM</b> e quaisquer outras extensões que você precisa.</p> |
  
Após cada VM ter sido provisionada, conecte-se e a associe ao domínio. 
1. Em **Gerenciador do Servidor** &gt; **Servidor Local** &gt; **GRUPO DE TRABALHO** &gt; **Altere…**, selecione **Domínio**.
2. Insira o nome do seu domínio local. 
3. Forneça as credenciais de um usuário de domínio.
4. Reinicie a VM.

## <a name="additional-resources"></a>Recursos adicionais

* Para obter mais informações sobre como usar o Windows PowerShell, consulte [Introdução aos Cmdlets do Azure](/powershell/azure/overview) e [Referência de Cmdlets do Azure](/powershell/azure/get-started-azureps).
* [Diretrizes para implantar o Active Directory do Windows Server em máquinas virtuais do Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx)
* [Como carregar controladores de domínio locais de Hyper-V existentes no Azure usando o Azure PowerShell](http://support.microsoft.com/kb/2904015)
* [Instalar uma nova floresta do Active Directory em uma rede virtual do Azure](active-directory-new-forest-virtual-machine.md)
* [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md)
* [Microsoft Azure IaaS para profissionais de TI: (01) conceitos básicos de máquina virtual](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
* [Microsoft Azure IaaS para profissionais de TI: (05) Criando redes virtuais e conectividade entre instalações](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
* [Azure PowerShell](/powershell/azure/overview)
* [Cmdlets de gerenciamento do Azure](/powershell/module/azurerm.compute/#virtual_machines)

<!--Image references-->
[1]: ./media/active-directory-install-replica-active-directory-domain-controller/ReplicaDCsOnAzureVNet.png
