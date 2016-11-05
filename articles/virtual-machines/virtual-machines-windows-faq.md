---
title: Perguntas frequentes sobre VMs do Windows | Microsoft Docs
description: Fornece respostas para algumas das perguntas mais comuns sobre as máquinas virtuais do Windows criadas com o modelo do Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: timlt
editor: ''
tags: azure-resource-management

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/16/2016
ms.author: cynthn

---
# <a name="frequently-asked-question-about-windows-virtual-machines"></a>Perguntas frequentes sobre as Máquinas Virtuais do Windows
Este artigo trata de algumas das perguntas mais comuns sobre as máquinas virtuais do Windows criadas no Azure com o modelo de implantação do Gerenciador de Recursos. Para a versão deste tópico relativa ao Linux, veja [Perguntas frequentes sobre as Máquinas Virtuais Linux](virtual-machines-linux-faq.md)

## <a name="what-can-i-run-on-an-azure-vm?"></a>O que eu posso executar em uma VM do Azure?
Todos os assinantes podem executar software para servidores em uma máquina virtual do Azure. Para saber mais sobre a política de suporte para software de servidor Microsoft em execução no Azure, veja [Suporte a software de servidor Microsoft para Máquinas Virtuais do Azure](https://support.microsoft.com/kb/2721672)

Determinadas versões do Windows 7 e Windows 8.1 estão disponíveis para assinantes de benefícios do Azure na MSDN e assinantes do modelo "pré-pago" do Desenvolvimento e Teste da MSDN, para tarefas de teste e desenvolvimento. Para obter detalhes, incluindo instruções e limitações, veja [Imagens do Windows Client para assinantes do MSDN](http://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/). 

## <a name="how-much-storage-can-i-use-with-a-virtual-machine?"></a>Quanto armazenamento eu posso usar com uma máquina virtual?
Cada disco de dados pode ter até 1 TB. O número de discos de dados que você pode usar depende do tamanho da máquina virtual. Para obter detalhes, consulte [Tamanhos das máquinas virtuais](virtual-machines-windows-sizes.md).

Uma conta de armazenamento do Azure fornece armazenamento para o disco do sistema operacional e quaisquer discos de dados. Cada disco é um arquivo .vhd armazenado como um blob de páginas. Para obter detalhes sobre preços, veja [Detalhes de preços de armazenamento](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine?"></a>Como posso acessar minha máquina virtual?
Estabeleça uma conexão remota usando o protocolo RDP (Conexão de Área de Trabalho Remota) para uma VM do Windows. Para obter instruções, consulte [Como se conectar e fazer logon em uma máquina virtual do Azure executando o Windows](virtual-machines-windows-connect-logon.md). Há suporte para o máximo de duas conexões simultâneas, a menos que o servidor esteja configurado como um host de sessão dos Serviços de Área de Trabalho Remota.  

Se você estiver tendo problemas com a Área de Trabalho Remota, veja [Solucionar problemas de conexões da Área de Trabalho Remota para uma Máquina Virtual Azure baseada no Windows](virtual-machines-windows-troubleshoot-rdp-connection.md). 

Se estiver familiarizado com o Hyper-V, você pode estar em busca de uma ferramenta semelhante ao VMConnect. O Azure não oferece uma ferramenta semelhante porque não há suporte para o acesso de console a uma máquina virtual.

## <a name="can-i-use-the-temporary-disk-(the-d:-drive-by-default)-to-store-data?"></a>Posso usar o disco temporário (a unidade d:, por padrão) para armazenar dados?
Não use o disco temporário para armazenar dados. Ele é apenas um armazenamento temporário, você se arriscaria a perder dados que não podem ser recuperados. Pode ocorrer perda de dados quando a máquina virtual é movida para um host diferente. O redimensionamento de uma máquina virtual, atualização do host ou uma falha de hardware no host são algumas das razões que podem levar à mudança de uma máquina virtual.

Se tiver um aplicativo que precise usar a letra da unidade D:, você poderá reatribuir as letras da unidade para que o disco temporário use algo diferente de D:. Para obter instruções, veja [Alterar a letra da unidade do disco temporário do Windows](virtual-machines-windows-classic-change-drive-letter.md).

## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk?"></a>Como eu posso alterar a letra da unidade do disco temporário?
Você pode alterar a letra da unidade movendo o arquivo de paginação e reatribuindo as letras da unidade, mas precisa se lembrar de seguir as etapas em uma ordem específica. Para obter instruções, veja [Alterar a letra da unidade do disco temporário do Windows](virtual-machines-windows-classic-change-drive-letter.md).

## <a name="can-i-add-an-existing-vm-to-an-availability-set?"></a>Posso adicionar uma VM existente a um conjunto de disponibilidade?
Não. Se você quiser que sua VM faça parte de um conjunto de disponibilidade, precisará criar a VM no conjunto. Atualmente, não há uma maneira de adicionar uma VM a um conjunto de disponibilidade após ela ter sido criada.

## <a name="can-i-upload-a-virtual-machine-to-azure?"></a>Posso carregar uma máquina virtual no Azure?
Sim. Para obter instruções, consulte [Carregar uma imagem de VM do Windows no Azure ](virtual-machines-windows-upload-image.md)

## <a name="can-i-resize-the-os-disk?"></a>Posso redimensionar o disco do sistema operacional?
Sim. Para obter instruções, veja [Como expandir a unidade do sistema operacional de uma Máquina Virtual em um Grupo de Recursos do Azure](virtual-machines-windows-expand-os-disk.md).

## <a name="can-i-copy-or-clone-an-existing-azure-vm?"></a>Posso copiar ou clonar uma VM do Azure existente?
Sim. Para obter instruções, veja [How to create a copy of a Windows virtual machine in the Resource Manager deployment model](virtual-machines-windows-vhd-copy.md) (Como criar uma cópia de uma máquina virtual do Windows no modelo de implantação do Resource Manager).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager?"></a>Por que não vejo as regiões Central e Leste do Canadá por meio do Azure Resource Manager?
As duas novas regiões, Central do Canadá e Leste do Canadá, não são registradas automaticamente para a criação de máquinas virtuais para as assinaturas existentes do Azure. Esse registro é feito automaticamente quando uma máquina virtual é implantada por meio do Portal do Azure para qualquer outra região usando o Azure Resource Manager. Depois que uma máquina virtual é implantada em qualquer outra região do Azure, as novas regiões devem estar disponíveis para máquinas virtuais subsequentes.

## <a name="does-azure-support-linux-vms?"></a>O Azure oferece suporte a VMs Linux?
Sim. Para criar rapidamente uma VM Linux para experimentá-la, veja [Criar uma VM Linux no Azure usando o Portal](virtual-machines-linux-quick-create-portal.md).

## <a name="can-i-add-a-nic-to-my-vm-after-it's-created?"></a>Posso adicionar um NIC à minha VM depois que ela é criada?
Não. A adição de uma NIC só pode ser feita no momento da criação.

## <a name="are-there-any-computer-name-requirements?"></a>Há algum requisito de nome do computador?
Sim. O nome do computador pode ter, no máximo, 15 caracteres. Consulte [Diretrizes de nomenclatura de infraestrutura](virtual-machines-windows-infrastructure-naming-guidelines.md) para obter mais informações sobre como nomear seus recursos.

## <a name="what-are-the-username-requirements-when-creating-a-vm?"></a>Quais são os requisitos de nome de usuário ao criar uma VM?
Nomes de usuário podem ter, no máximo, 20 caracteres e não podem terminar com um ponto (“.”). 

Os seguintes nomes de usuário não são permitidos:

<table>
    <tr>
        <td style="text-align:center">administrator </td><td style="text-align:center"> administrador </td><td style="text-align:center"> usuário </td><td style="text-align:center"> user1</td>
    </tr>
    <tr>
        <td style="text-align:center">test </td><td style="text-align:center"> user2 </td><td style="text-align:center"> test1 </td><td style="text-align:center"> user3</td>
    </tr>
    <tr>
        <td style="text-align:center">admin1 </td><td style="text-align:center"> 1 </td><td style="text-align:center"> 123 </td><td style="text-align:center"> a</td>
    </tr>
    <tr>
        <td style="text-align:center">actuser  </td><td style="text-align:center"> adm </td><td style="text-align:center"> admin2 </td><td style="text-align:center"> aspnet</td>
    </tr>
    <tr>
        <td style="text-align:center">backup </td><td style="text-align:center"> console </td><td style="text-align:center"> david </td><td style="text-align:center"> guest</td>
    </tr>
    <tr>
        <td style="text-align:center">julio </td><td style="text-align:center"> proprietário </td><td style="text-align:center"> root </td><td style="text-align:center"> server</td>
    </tr>
    <tr>
        <td style="text-align:center">sql </td><td style="text-align:center"> suporte </td><td style="text-align:center"> support_388945a0 </td><td style="text-align:center"> sys</td>
    </tr>
    <tr>
        <td style="text-align:center">test2 </td><td style="text-align:center"> test3 </td><td style="text-align:center"> user4 </td><td style="text-align:center"> user5</td>
    </tr>
</table>

## <a name="what-are-the-password-requirements-when-creating-a-vm?"></a>Quais são os requisitos de senha ao criar uma VM?
As senhas devem ter de 8 a 123 caracteres e atender a três dos quatro requisitos de complexidade a seguir:

* Ter caracteres minúsculos
* Tem caracteres maiúsculos
* Tem um dígito
* Tem um caractere especial (Correspondência de regex [\W_])

As seguintes senhas não são permitidas:

<table>
    <tr>
        <td style="text-align:center">abc@123</td><td style="text-align:center">P@$$w0rd</td><td style="text-align:center">P@ssw0rd</td><td style="text-align:center">P@ssword123</td><td style="text-align:center">Pa$$word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td><td style="text-align:center">Password!</td><td style="text-align:center">Password1</td><td style="text-align:center">Password22</td><td style="text-align:center">iloveyou!</td>
    </tr>
</table>



<!--HONumber=Oct16_HO2-->


