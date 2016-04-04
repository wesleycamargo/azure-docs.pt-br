<properties
	pageTitle="Solucionar problemas de conexão de Área de Trabalho Remota em uma VM do Azure | Microsoft Azure"
	description="Solucionar erros de conexão de Área de Trabalho Remota em uma VM do Windows. Obtenha etapas paliativas rápidas, ajuda por mensagem de erro e solução de problemas de rede detalhada."
	keywords="Erro de área de trabalho remota, erro de conexão de área de trabalho remota, não é possível conectar-se à VM, solução de problemas da área de trabalho remota"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/25/2016"
	ms.author="dkshir"/>

# Solucionar problemas de conexões de Área de Trabalho Remota para uma máquina virtual do Azure executando o Windows

A conexão de RDP (Área de Trabalho Remota) para sua máquina virtual do Azure baseada no Windows pode falhar por vários motivos. O problema pode ser com o serviço de Área de Trabalho Remota na VM, a conexão de rede ou o cliente de Área de Trabalho Remota no computador host. Este artigo o ajudará a descobrir as causas e corrigi-las.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Este artigo aplica-se a máquinas virtuais do Azure executando o Windows. Para Máquinas Virtuais que executam o Linux, conulte [Solução de problemas de conexão SSH a uma VM do Azure](virtual-machines-linux-troubleshoot-ssh-connection.md).

Se você precisar de mais ajuda a qualquer momento neste artigo, poderá contatar os especialistas do Azure nos [fóruns do Azure MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Como alternativa, você também pode registrar um incidente de suporte do Azure. Acesse o [site de Suporte do Azure](https://azure.microsoft.com/support/options/) e clique em **Obter Suporte**.


<a id="quickfixrdp"></a>
## Corrigir erros comuns de Área de Trabalho Remota

Esta seção lista as etapas de correção rápida para problemas comuns de conexão de Área de Trabalho Remota.

### Máquinas virtuais criadas usando o modelo de implantação clássico

Essas etapas podem resolver a maioria das falhas de conexão de Área de Trabalho Remota em máquinas virtuais criadas com o modelo de implantação clássico. Após cada etapa, tente se reconectar à VM.

- Redefina o serviço da Área de Trabalho Remota no [portal do Azure](https://portal.azure.com) para corrigir problemas de inicialização com o servidor RDP.<br> Clique em **Procurar** > **Máquinas virtuais (clássicas)** > sua máquina virtual do Windows > **Redefinir Acesso...**.

- Reinicie a Máquina Virtual para resolver outros problemas de inicialização.<br> Clique em **Procurar** > **Máquinas virtuais (clássicas)** > sua máquina virtual do Windows > **Reiniciar**.

- Redimensione a VM para corrigir quaisquer problemas de host.<br> Clique em **Procurar** > **Máquinas virtuais (clássicas)** > sua máquina virtual do Windows > **Configurações** > **Tamanho**. Para obter as etapas detalhadas, consulte [Redimensionar a máquina virtual](https://msdn.microsoft.com/library/dn168976.aspx).

- Examine o log do console ou a captura de tela da VM para corrigir problemas de inicialização.<br> Clique em **Procurar** > **Máquinas virtuais (clássicas**) > sua máquina virtual do Windows > **Configurações** > **Diagnóstico de inicialização**.

- Verifique se há problemas de plataforma na Integridade do Recurso da VM.<br> Clique em **Procurar** > **Máquinas virtuais (clássicas)** > sua máquina virtual do Windows > **Configurações** > **Verificar Integridade**.

## Máquinas virtuais criadas usando o modelo de implantação do Gerenciador de Recursos

Essas etapas podem resolver a maioria das falhas de conexão de Área de Trabalho Remota em máquinas virtuais criadas com o modelo de implantação do Gerenciador de Recursos. Após cada etapa, tente se reconectar à VM.

- _Redefina o Acesso Remoto_ usando o Powershell<br> a. Se você ainda não fez isso, [instale o Azure PowerShell e conecte-se à sua assinatura do Azure](../powershell-install-configure.md) usando o método do AD do Azure. Observe que você não precisa alternar para o modo do Gerenciador de Recursos em novas versões do Azure PowerShell 1.0. x.

	b. Redefina sua conexão RDP usando qualquer um dos comandos do Azure PowerShell a seguir. Substitua `myRG`, `myVM`, `myVMAccessExtension` e o local por valores relevantes à sua instalação.

	```
	Set-AzureRmVMExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccessExtension" -ExtensionType "VMAccessAgent" -Publisher "Microsoft.Compute" -typeHandlerVersion "2.0" -Location Westus
	```
	OR<br>

  ```
  Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" -Location Westus
  ```

- Reinicie a Máquina Virtual para resolver outros problemas de inicialização.<br> Clique em **Procurar** > **Máquinas virtuais** > sua máquina virtual do Windows > **Reiniciar**.

- Redimensione a VM para corrigir quaisquer problemas de host.<br> Clique em **Procurar** > **Máquinas virtuais** > sua máquina virtual do Windows > **Configurações** > **Tamanho**.

- Examine o log do console ou a captura de tela da VM para corrigir problemas de inicialização.<br> Clique em **Procurar** > **Máquinas virtuais** > sua máquina virtual do Windows > **Configurações** > **Diagnóstico de inicialização**.


Vá para a próxima seção se as etapas acima não resolveram as falhas de conexão de Área de Trabalho Remota.

## Solucionar problemas de erros específicos de conexão de área de trabalho remota

Estes são os erros mais comuns que você pode encontrar durante a tentativa de conexão por Área de Trabalho Remota à máquina virtual do Azure:

1. [Erro de conexão de Área de Trabalho Remota: a sessão remota foi desconectada porque não há Servidores de Licença da Área de Trabalho Remota disponíveis para fornecer uma licença](#rdplicense).

2. [Erro de conexão de Área de Trabalho Remota: a Área de Trabalho Remota não consegue localizar o computador "nome"](#rdpname).

3. [Erro de conexão de Área de Trabalho Remota: ocorreu um erro de autenticação. A Autoridade de Segurança Local não pode ser contatada](#rdpauth).

4. [Erro de Segurança do Windows: suas credenciais não funcionaram](#wincred).

5. [Erro de conexão de Área de Trabalho Remota: este computador não pode se conectar ao computador remoto](#rdpconnect).

<a id="rdplicense"></a>
### Erro de conexão de Área de Trabalho Remota: a sessão remota foi desconectada porque não há Servidores de Licença da Área de Trabalho Remota disponíveis para fornecer uma licença.

Causa: o período de cortesia de licenciamento de 120 dias para a função de Servidor de Área de Trabalho Remota expirou e você precisa instalar licenças.

Como alternativa, salve uma cópia local do arquivo RDP do portal e execute este comando no prompt de comando do Windows PowerShell para conectar-se. Isso desabilitará o licenciamento apenas para essa conexão.

		mstsc <File name>.RDP /admin

Se não precisar realmente de mais de duas conexões simultâneas de Área de Trabalho Remota à VM, você poderá usar o Gerenciador do Servidor para remover a função de Servidor de Área de Trabalho Remota.

Consulte também a postagem de blog [A M do Azure falha com "Sem Servidores de Licença da Área de Trabalho Remota disponíveis"](http://blogs.msdn.com/b/wats/archive/2014/01/21/rdp-to-azure-vm-fails-with-quot-no-remote-desktop-license-servers-available-quot.aspx).

<a id="rdpname"></a>
### Erro de conexão de Área de Trabalho Remota: a Área de Trabalho Remota não consegue localizar o computador "nome".

Causa: o cliente da Área de Trabalho Remota em seu computador não pôde resolver o nome do computador nas configurações do arquivo RDP.

Soluções possíveis:

- Se você estiver na intranet de uma organização, certifique-se que o computador tem acesso ao servidor proxy e pode enviar o tráfego HTTPS para ele.
- Se você estiver usando um arquivo RDP armazenado localmente, tente usar aquele gerado pelo portal. Isso garantirá que o você tenha o nome DNS correto para a máquina virtual ou o serviço de nuvem e a porta de ponto de extremidade da máquina virtual. Aqui está um arquivo RDP de exemplo gerado pelo portal:

		full address:s:tailspin-azdatatier.cloudapp.net:55919
		prompt for credentials:i:1

A parte do endereço neste arquivo RDP tem o nome de domínio totalmente qualificado do serviço de nuvem que contém a VM (tailspin-azdatatier.cloudapp.net neste exemplo) e a porta TCP externa do ponto de extremidade para o tráfego de Área de Trabalho Remota (55919).

<a id="rdpauth"></a>
### Erro de conexão de Área de Trabalho Remota: ocorreu um erro de autenticação. A Autoridade de Segurança Local não pode ser contatada.

Causa: a VM de destino não pôde localizar a autoridade de segurança na parte do nome de usuário das suas credenciais.

Quando seu nome de usuário estiver no formato *SecurityAuthority*\*UserName* (exemplo: CORP\\User1), o trecho *SecurityAuthority* será o nome do computador da máquina virtual (para a autoridade de segurança local) ou um nome de domínio do Active Directory.

Soluções possíveis:

- Se sua conta de usuário local para a máquina virtual, verifique se o nome da VM está escrito corretamente.
- Se a conta estiver em um domínio do Active Directory, verifique a ortografia do nome do domínio.
- Se ela for uma conta de domínio do Active Directory e o nome de domínio estiver digitado corretamente, verifique se há um controlador de domínio disponível nesse domínio. Esse pode ser um problema comum em uma rede virtual do Azure que contém controladores de domínio e nas quais um computador do controlador de domínio não foi iniciado. Como alternativa, você pode usar uma conta de administrador local em vez de uma conta de domínio.

<a id="wincred"></a>
### Erro de Segurança do Windows: suas credenciais não funcionaram.

Causa: o VM de destino não pôde validar seu nome de conta e senha.

Um computador baseado em Windows pode validar as credenciais de uma conta local ou de uma conta de domínio.

- Para contas locais, use a sintaxe *NomeComputador*\*NomeUsuário* (exemplo: SQL1\\Admin4798).
- Para contas de domínio, use a sintaxe *DomainName*\*UserName* (exemplo: CONTOSO\\johndoe).

Se você promoveu sua VM a um controlador de domínio em uma nova floresta do Active Directory, a conta de administrador local à qual você está conectado também é convertida em uma conta equivalente com a mesma senha na nova floresta e domínio. A conta local é então excluída. Por exemplo, se você estiver conectado à conta local DC1\\DCAdmin e tiver promovido a máquina virtual como um controlador de domínio em uma nova floresta para o domínio corp.contoso.com, a conta local DC1\\DCAdmin será excluída, e uma nova conta de domínio (CORP\\DCAdmin) será criada com a mesma senha.

Verifique o nome da conta para garantir que ele é um nome que a máquina virtual pode verificar como uma conta válida e que a senha está correta.

Se você precisar alterar a senha da conta de administrador local, consulte [Como redefinir uma senha ou o serviço de Área de Trabalho Remota para máquinas virtuais do Windows](virtual-machines-windows-reset-rdp.md).

<a id="rdpconnect"></a>
### Erro de conexão de Área de Trabalho Remota: este computador não pode se conectar ao computador remoto.

Causa: a conta que você usou para se conectar não tem direitos de logon na Área de Trabalho Remota.

Todo computador Windows tem um grupo local Usuários da Área de Trabalho Remota que contém as contas e os grupos que podem fazer logon remotamente. Os membros do grupo local Administradores também têm acesso, mesmo que essas contas não sejam listadas no grupo local Usuários da Área de Trabalho Remota. Para computadores que ingressaram no domínio, o grupo local Administradores também contém os administradores de domínio para o domínio.

Certifique-se que a conta que você está usando para conectar-se tem direitos de logon de Área de Trabalho Remota. Como uma solução alternativa, use uma conta de administrador local ou de administrador de domínio pela Área de Trabalho Remota e, então, use o snap-in Gerenciamento de Computador (**Ferramentas do Sistema > Usuários e Grupos Locais > Grupos > Usuários da Área de Trabalho Remota**) para adicionar a conta desejada ao grupo local de Usuários da Área de Trabalho Remota.

## Solução de problemas de erros genéricos de Área de Trabalho Remota

Se nenhum desses erros ocorreu e ainda não foi possível para você conectar-se à VM por meio da Área de Trabalho Remota, leia [o guia de solução de problemas detalhado para Área de Trabalho Remota](virtual-machines-windows-detailed-troubleshoot-rdp.md).


## Recursos adicionais

[Pacote de diagnóstico do Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Como redefinir uma senha ou o serviço de Área de Trabalho Remota para máquinas virtuais Windows](virtual-machines-windows-reset-rdp.md)

[Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md)

[Solucionar problemas de conexões SSH (Secure Shell) para uma máquina virtual do Azure baseada em Linux](virtual-machines-linux-troubleshoot-ssh-connection.md)

[Solucionar problemas de acesso a um aplicativo executado em uma máquina virtual do Azure](virtual-machines-linux-troubleshoot-app-connection.md)

<!---HONumber=AcomDC_0323_2016-->