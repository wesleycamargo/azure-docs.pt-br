<properties
	pageTitle="Solucionar problemas de conexão de Área de Trabalho Remota em uma VM do Azure | Microsoft Azure"
	description="Se não for possível acessar sua VM do Azure, pesquise as etapas de solução de problemas de RDP rápidas, a ajuda através de mensagens de erro e as etapas de solução de problemas de rede detalhados."
	keywords="Erro de área de trabalho remota, erro de conexão de área de trabalho remota, não é possível conectar-se à VM, solução de problemas da área de trabalho remota"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="support-article"
	ms.date="06/14/2016"
	ms.author="iainfou"/>

# Solucionar problemas de conexões de Área de Trabalho Remota para uma máquina virtual do Azure executando o Windows

A conexão do protocolo RDP à sua VM (máquina virtual) do Azure baseada no Windows pode falhar por vários motivos, impedindo o seu acesso à VM. O problema pode ser com o serviço de Área de Trabalho Remota na VM, a conexão de rede ou o cliente de Área de Trabalho Remota no computador host. Este artigo explica alguns dos métodos mais comuns para resolver problemas de conexão de RDP. Se seu problema não estiver listado aqui ou ainda não for possível se conectar à VM por RDP, você poderá ler [conceitos e etapas sobre solução de problemas de RDP mais detalhados](virtual-machines-windows-detailed-troubleshoot-rdp.md).

Caso precise de mais ajuda a qualquer momento neste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/support/forums/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.

<a id="quickfixrdp"></a>

## Etapas rápidas para solucionar problemas
Após cada etapa da solução de problemas, tente se reconectar à VM:

1. Redefina o acesso remoto usando o Portal do Azure ou o Azure PowerShell
2. Reinicie a VM
3. Reimplante a VM
4. Verifique o Grupo de Segurança de Rede/regras de ponto de extremidade dos Serviços de Nuvem
5. Analise os logs do console da VM no Portal do Azure ou no Azure PowerShell
6. Verifique o Resource Health da VM no Portal do Azure
7. Redefina a senha da VM

Continue lendo se precisar de etapas e explicações mais detalhadas para o Resource Manager e os modelos de implantação Clássica.


<a id="fix-common-remote-desktop-errors"></a>
## Solucionar problemas de VMs criadas com o modelo de implantação do Gerenciador de Recursos

Após cada etapa de solução de problemas, tente se reconectar à VM.

> [AZURE.TIP] Se o botão 'Conectar' no portal estiver esmaecido e você não estiver conectado ao Azure por meio de uma [Rota Expressa](../expressroute/expressroute-introduction.md) ou de uma conexão [VPN Site a Site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), será necessário criar e atribuir à VM um endereço IP público antes de usar o RDP. Leia mais sobre [endereços IP públicos no Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md).

1. Redefina o acesso remoto usando o PowerShell.
	- Se você ainda não fez isso, [instale e configure o último Azure PowerShell](../powershell-install-configure.md).

	- Redefina sua conexão RDP usando qualquer um dos comandos do PowerShell a seguir. Substitua `myRG`, `myVM`, `myVMAccessExtension` e a localização por valores relevantes à sua instalação.

	```
	Set-AzureRmVMExtension -ResourceGroupName "myRG" -VMName "myVM" `
		-Name "myVMAccessExtension" -ExtensionType "VMAccessAgent" `
		-Publisher "Microsoft.Compute" -typeHandlerVersion "2.0" `
		-Location Westus
	```
	OU

  	```
	Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" `
		-VMName "myVM" -Name "myVMAccess" -Location Westus
	```

	> [AZURE.NOTE] Nos exemplos anteriores, `myVMAccessExtension` ou `MyVMAccess` é um nome especificado para a nova extensão a ser instalada como parte do processo. Geralmente, isso é definido como o nome da VM. Caso você tenha trabalhado anteriormente com o VMAccessAgent, será possível obter o nome da extensão existente usando `Get-AzureRmVM -ResourceGroupName "myRG" -Name "myVM"` para verificar as propriedades da VM. Em seguida, examine a seção 'Extensões' da saída. Já que apenas um VMAccessAgent pode existir em uma VM, você também precisa adicionar o parâmetro `-ForceReRun` ao usar `Set-AzureRmVMExtension` para registrar o agente novamente.

2. Reinicie a VM para resolver outros problemas de inicialização. Escolha **Procurar** > **Máquinas virtuais** > *sua VM* > **Reiniciar**.

3. [Reimplante a VM em um novo nó do Azure](virtual-machines-windows-redeploy-to-new-node.md).

	Após a conclusão dessa operação, os dados de disco efêmeros serão perdidos e os endereços IP dinâmicos associados à máquina virtual serão atualizados.
	
4. Verifique se as [regras do Grupo de Segurança de Rede](../virtual-network/virtual-networks-nsg.md) permitem o tráfego RDP (porta TCP 3389).

5. Examine o log do console ou a captura de tela da VM para corrigir problemas de inicialização. Escolha **Procurar** > **Máquinas virtuais** > *sua máquina virtual do Windows* > **Suporte + Solução de Problemas** > **Diagnóstico de inicialização**.

6. [Redefina a senha da VM](virtual-machines-windows-reset-rdp.md).

Caso ainda esteja tendo problemas de RDP, você poderá [abrir uma solicitação de suporte](https://azure.microsoft.com/support/options/) ou ler [conceitos e etapas de solução de problemas de RDP mais detalhados](virtual-machines-windows-detailed-troubleshoot-rdp.md).


## Solucionar problemas de VMs criadas com o modelo de implantação Clássica

Após cada etapa de solução de problemas, tente se reconectar à VM.

1. Redefina o serviço de Área de Trabalho Remota no [Portal do Azure](https://portal.azure.com). Escolha **Procurar** > **Máquinas virtuais (clássicas)** > *sua VM* > **Redefinir Remoto...**.

2. Reinicie a VM para resolver outros problemas de inicialização. Escolha **Procurar** > **Máquinas virtuais (clássicas)** > *sua VM* > **Reiniciar**.

3. [Reimplante a VM em um novo nó do Azure](virtual-machines-windows-redeploy-to-new-node.md).

	Após a conclusão dessa operação, os dados de disco efêmeros serão perdidos e os endereços IP dinâmicos associados à máquina virtual serão atualizados.
	
4. Verifique se o [ponto de extremidade dos Serviços de Nuvem permite o tráfego RDP](../cloud-services/cloud-services-role-enable-remote-desktop.md).

5. Examine o log do console ou a captura de tela da VM para corrigir problemas de inicialização. Escolha **Procurar** > **Máquinas virtuais (clássicas**) > *sua VM* > **Configurações** > **Diagnóstico de inicialização**.

6. Verifique se há problemas de plataforma na Integridade do Recurso da VM. Escolha **Procurar** > **Máquinas virtuais (clássicas**) > *sua VM* > **Configurações** > **Verificar integridade**.

7. [Redefina a senha da VM](virtual-machines-windows-reset-rdp.md).

Caso ainda esteja tendo problemas de RDP, você poderá [abrir uma solicitação de suporte](https://azure.microsoft.com/support/options/) ou ler [conceitos e etapas de solução de problemas de RDP mais detalhados](virtual-machines-windows-detailed-troubleshoot-rdp.md).


## Solucionar problemas de erros específicos de conexão de área de trabalho remota

Você pode receber um erro específico ao tentar se conectar à sua VM por meio do RDP. Veja abaixo as mensagens de erro mais comuns:

- [A sessão remota foi desconectada porque não há Servidores de Licença da Área de Trabalho Remota disponíveis para fornecer uma licença](#rdplicense).

- [A Área de Trabalho Remota não consegue localizar o "nome" do computador](#rdpname).

- [Ocorreu um erro de autenticação. A Autoridade de Segurança Local não pode ser contatada](#rdpauth).

- [Erro de Segurança do Windows: suas credenciais não funcionaram](#wincred).

- [Este computador não pode se conectar ao computador remoto](#rdpconnect).

<a id="rdplicense"></a>
### A sessão remota foi desconectada porque não há Servidores de Licença da Área de Trabalho Remota disponíveis para fornecer uma licença.

Causa: o período de cortesia de licenciamento de 120 dias para a função de Servidor de Área de Trabalho Remota expirou e você precisa instalar licenças.

Como alternativa, salve uma cópia local do arquivo RDP do portal e execute este comando no prompt de comando do PowerShell para conectar-se. Isso desabilita o licenciamento apenas para esta conexão:

		mstsc <File name>.RDP /admin

Se não precisar realmente de mais de duas conexões simultâneas de Área de Trabalho Remota à VM, você poderá usar o Gerenciador do Servidor para remover a função de Servidor de Área de Trabalho Remota.

Para saber mais, confira a postagem no blog [Azure VM fails with "No Remote Desktop License Servers available" (A VM do Azure falha com a mensagem "Nenhum Servidor de Licença da Área de Trabalho Remota disponível")](http://blogs.msdn.com/b/wats/archive/2014/01/21/rdp-to-azure-vm-fails-with-quot-no-remote-desktop-license-servers-available-quot.aspx).

<a id="rdpname"></a>
### A Área de Trabalho Remota não consegue localizar o “nome” do computador.

Causa: o cliente da Área de Trabalho Remota em seu computador não pode resolver o nome do computador nas configurações do arquivo RDP.

Soluções possíveis:

- Se você estiver na intranet de uma organização, certifique-se que o computador tem acesso ao servidor proxy e que pode enviar o tráfego HTTPS para ele.

- Se você estiver usando um arquivo RDP armazenado localmente, tente usar aquele gerado pelo portal. Isso garante que você tenha o nome DNS correto para a máquina virtual ou para o serviço de nuvem e a porta do ponto de extremidade da VM. Aqui está um arquivo RDP de exemplo gerado pelo portal:

		full address:s:tailspin-azdatatier.cloudapp.net:55919
		prompt for credentials:i:1

A parte do endereço desse arquivo RDP tem:
- O nome de domínio totalmente qualificado do serviço de nuvem que contém a VM ("tailspin-azdatatier.cloudapp.net" neste exemplo).

- A porta TCP externa do ponto de extremidade do tráfego de Área de Trabalho Remota (55919).

<a id="rdpauth"></a>
### Ocorreu um erro de autenticação. A Autoridade de Segurança Local não pode ser contatada.

Causa: a VM de destino não pôde localizar a autoridade de segurança na parte do nome de usuário das suas credenciais.

Quando seu nome de usuário estiver no formato *SecurityAuthority*\\*UserName* (exemplo: CORP\\User1), a parte *SecurityAuthority* será o nome do computador da VM (para a autoridade de segurança local) ou um nome de domínio do Active Directory.

Soluções possíveis:

- Se a conta for local para a VM, verifique se o nome da VM está escrito corretamente.

- Se a conta estiver em um domínio do Active Directory, verifique a ortografia do nome do domínio.

- Se ela for uma conta de domínio do Active Directory e o nome de domínio estiver digitado corretamente, verifique se há um controlador de domínio disponível nesse domínio. É um problema comum em redes virtuais do Azure que contêm controladores de domínio que um controlador de domínio não esteja disponível porque ele ainda não foi iniciado. Como alternativa, você pode usar uma conta de administrador local em vez de uma conta de domínio.

<a id="wincred"></a>
### Erro de Segurança do Windows: suas credenciais não funcionaram.

Causa: a VM de destino não pôde validar seu nome de conta e senha.

Um computador baseado em Windows pode validar as credenciais de uma conta local ou de uma conta de domínio.

- Para contas locais, use a sintaxe *NomeComputador* \\ *NomeUsuário* (exemplo: SQL1\\Admin4798).
- Para contas de domínio, use a sintaxe *DomainName* \\ *UserName* (exemplo: CONTOSO\\fabiopena).

Se você promoveu sua VM a um controlador de domínio em uma nova floresta do Active Directory, a conta de administrador local à qual você está conectado também será convertida em uma conta equivalente com a mesma senha na nova floresta e domínio. A conta local é então excluída.

Por exemplo, se você estiver conectado à conta local DC1\\DCAdmin e tiver promovido a máquina virtual como um controlador de domínio em uma nova floresta para o domínio corp.contoso.com, a conta local DC1\\DCAdmin será excluída, e uma nova conta de domínio (CORP\\DCAdmin) será criada com a mesma senha.

Verifique o nome da conta para garantir que ele é um nome que a máquina virtual pode verificar como uma conta válida e que a senha está correta.

Se você precisar alterar a senha da conta de administrador local, consulte [Como redefinir uma senha ou o serviço de Área de Trabalho Remota para máquinas virtuais do Windows](virtual-machines-windows-reset-rdp.md).

<a id="rdpconnect"></a>
### Este computador não pode se conectar ao computador remoto.

Causa: a conta que você usou para se conectar não tem direitos de entrada na Área de Trabalho Remota.

Todo computador Windows tem um grupo local Usuários da Área de Trabalho Remota que contém as contas e os grupos que podem se conectar remotamente. Os membros do grupo local de administradores também têm acesso, mesmo que essas contas não sejam listadas no grupo local de usuários da Área de Trabalho Remota. Para computadores que ingressaram no domínio, o grupo local de administradores também contém os administradores de domínio para o domínio.

Certifique-se que a conta que você está usando para conectar-se tem direitos de entrada da Área de Trabalho Remota. Como alternativa, use uma conta de administrador local ou domínio para se conectar na Área de Trabalho Remota. Para adicionar a conta desejada ao grupo local de usuários da Área de Trabalho Remota, use o snap-in do Console de Gerenciamento Microsoft (**Ferramentas do sistema > Usuários e Grupos Locais > Grupos > Usuários da Área de Trabalho Remota**).

## Solucionar problemas de erros genéricos de Área de Trabalho Remota

Se nenhum desses erros ocorreu e ainda não for possível se conectar à VM por meio da Área de Trabalho Remota, leia o [guia detalhado de solução de problemas para Área de Trabalho Remota](virtual-machines-windows-detailed-troubleshoot-rdp.md).


## Recursos adicionais

[Pacote de diagnóstico do Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Como redefinir uma senha ou o serviço de Área de Trabalho Remota para máquinas virtuais Windows](virtual-machines-windows-reset-rdp.md)

[Como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md)

[Solucionar problemas de conexões Secure Shell para uma máquina virtual do Azure baseada em Linux](virtual-machines-linux-troubleshoot-ssh-connection.md)

[Solucionar problemas de acesso a um aplicativo executado em uma máquina virtual do Azure](virtual-machines-linux-troubleshoot-app-connection.md)

<!---HONumber=AcomDC_0817_2016-->
