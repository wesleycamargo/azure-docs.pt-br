<properties
	pageTitle="Solucionar problemas de conexão de Área de Trabalho Remota em uma VM do Windows | Microsoft Azure"
	description="Solucionar problemas de conexões de RDP ou Área de Trabalho Remota para uma máquina virtual do Azure executando o Windows."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2015"
	ms.author="dkshir"/>

# Solucionar problemas de conexões de Área de Trabalho Remota para uma máquina virtual do Azure executando o Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Este artigo abrange solução de problemas em uma máquina virtual criada com o modelo de implantação clássica ou o modelo de implantação do Gerenciador de Recursos.

Pode haver vários motivos para RDP (Área de Trabalho Remota) falhar ao se conectar à sua máquina virtual do Azure executando o Windows. Este artigo o ajudará a descobrir as causas e corrigi-las.

> [AZURE.NOTE]Este artigo aplica-se somente a máquinas virtuais do Azure executando o Windows. Para solucionar problemas em conexões a máquinas virtuais do Azure executando o Linux, consulte [este artigo](virtual-machines-troubleshoot-ssh-connections.md).

## Contate o Suporte ao Cliente do Azure

Se você precisar de mais ajuda em qualquer momento neste artigo, você pode contatar os especialistas do Azure nos [fóruns do Azure MSDN e Excedente de Pilha](http://azure.microsoft.com/support/forums/).

Como alternativa, você também pode registrar um incidente de suporte do Azure. Acesse o [site de Suporte do Azure](http://azure.microsoft.com/support/options/) e clique em **Obter Suporte**. Para obter informações sobre como usar o Suporte do Azure, leia as [Perguntas Frequentes de Suporte do Microsoft Azure](http://azure.microsoft.com/support/faq/).


## Etapas básicas

Estas etapas básicas podem ajudar a resolver a maioria das falhas de conexão de Área de Trabalho Remota:

- Redefinir o serviço de Área de Trabalho Remota no [portal do Azure](https://portal.azure.com). Clique em **Procurar tudo** > **Máquinas virtuais (clássicas)** > sua máquina virtual do Windows > **Redefinir Acesso Remoto**.

![Redefinir Acesso Remoto](./media/virtual-machines-troubleshoot-remote-desktop-connections/Portal-RDP-Reset-Windows.png)

- [Reiniciar a máquina virtual](https://msdn.microsoft.com/library/azure/dn763934.aspx).

- [Redimensionar a máquina virtual](https://msdn.microsoft.com/library/dn168976.aspx).


## Executar o pacote de Diagnóstico do Azure IaaS no Windows

Se você está solucionando problemas de um computador executando o Windows 8, Windows 8.1, Windows Server 2012 ou Windows Server 2012 R2, pode tentar executar o [pacote de diagnóstico do Azure IaaS (Windows)](http://support.microsoft.com/kb/2976864). Esse pacote pode resolver muitos dos problemas comuns com a Área de Trabalho Remota.

1.	Clique em **Pacote de diagnóstico do Microsoft Azure IaaS (Windows)** na [Página de diagnóstico de suporte](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864). Clique em **Criar** para uma nova sessão de diagnóstico. Você pode **Compartilhar** esta sessão com um computador de destino diferente ou **Baixar** a sessão em seu computador local.
2.	**Execute** a sessão, **Aceite** o contrato de licença da Microsoft e **Inicie** a ferramenta de diagnóstico.
3.	Autentique a sua assinatura do Azure na janela pop-up e siga os prompts.
4.	Na página **Quais dos seguintes problemas você está enfrentando com sua VM do Azure?**, selecione o problema **Conectividade RDP a uma VM do Azure (necessário reinicializar)**.

Se o pacote de diagnóstico do Azure IaaS não pôde executar ou não foi útil, continue até a próxima seção para corrigir o problema com base no erro obtido do cliente de Área de Trabalho Remota.


## Erros comuns de RDP

Estes são os erros mais comuns que você pode encontrar durante a tentativa de conexão por Área de Trabalho Remota à máquina virtual do Azure:

1. [Erro de conexão de Área de Trabalho Remota: a sessão remota foi desconectada porque não há Servidores de Licença da Área de Trabalho Remota disponíveis para fornecer uma licença](#rdplicense).

2. [Erro de conexão de Área de Trabalho Remota: a Área de Trabalho Remota não consegue localizar o computador "nome"](#rdpname).

3. [Erro de conexão de Área de Trabalho Remota: ocorreu um erro de autenticação. A Autoridade de Segurança Local não pode ser contatada](#rdpauth).

4. [Erro de Segurança do Windows: suas credenciais não funcionaram](#wincred).

5. [Erro de conexão de Área de Trabalho Remota: este computador não pode se conectar ao computador remoto](#rdpconnect).

<a id="rdplicense"></a>
### Erro de conexão de Área de Trabalho Remota: a sessão remota foi desconectada porque não há Servidores de Licença da Área de Trabalho Remota disponíveis para fornecer uma licença.

Causa: o período de cortesia de licenciamento de 120 dias para a função de Servidor de Área de Trabalho Remota expirou e você precisa instalar licenças.

Como solução, salve uma cópia local do arquivo RDP do portal do Azure e execute este comando no prompt de comando do Windows PowerShell para se conectar.

		mstsc <File name>.RDP /admin

Isso desabilitará o licenciamento apenas para essa conexão.

Se não precisar realmente de mais de duas conexões simultâneas de Área de Trabalho Remota à máquina virtual, você poderá usar o Gerenciador de Servidores para remover a função de Servidor de Área de Trabalho Remota.

Consulte também a postagem de blog [A M do Azure falha com "Sem Servidores de Licença da Área de Trabalho Remota disponíveis"](http://blogs.msdn.com/b/wats/archive/2014/01/21/rdp-to-azure-vm-fails-with-quot-no-remote-desktop-license-servers-available-quot.aspx).

<a id="rdpname"></a>
### Erro de conexão de Área de Trabalho Remota: a Área de Trabalho Remota não consegue localizar o computador "nome".

Causa: o cliente da Área de Trabalho Remota em seu computador não pôde resolver o nome do computador nas configurações do arquivo RDP.

Soluções possíveis:

- Se você estiver na intranet de uma organização, certifique-se que o computador tem acesso ao servidor proxy e pode enviar o tráfego HTTPS para ele.
- Se você estiver usando um arquivo RDP armazenado localmente, tente usar aquele gerado pelo portal do Azure. Isso garantirá que o você tenha o nome DNS correto para a máquina virtual ou o serviço de nuvem e a porta de ponto de extremidade da máquina virtual. Aqui está um arquivo RDP de exemplo gerado pelo portal do Azure:

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

- Para contas locais, use a sintaxe *ComputerName*\*UserName* (exemplo: SQL1\\Admin4798).
- Para contas de domínio, use a sintaxe *DomainName*\*UserName* (exemplo: CONTOSO\\johndoe).

Se você promoveu sua máquina virtual a um controlador de domínio em uma nova floresta do Active Directory, a conta de administrador local à qual você está conectado também é convertida em uma conta equivalente com a mesma senha na nova floresta e domínio. A conta de administrador local é então excluída. Por exemplo, se você estiver conectado à conta de administrador local DC1\\DCAdmin e tiver promovido a máquina virtual como um controlador de domínio em uma nova floresta para o domínio corp.contoso.com, a conta local DC1\\DCAdmin será excluída, e uma nova conta de domínio (CORP\\DCAdmin) será criada com a mesma senha.

Verifique o nome da conta para garantir que ele é um nome que a máquina virtual pode verificar como uma conta válida e que a senha está correta.

Se você precisar alterar a senha da conta de administrador local, consulte [Como redefinir uma senha ou o serviço de Área de Trabalho Remota para máquinas virtuais do Windows](virtual-machines-windows-reset-password.md).

<a id="rdpconnect"></a>
### Erro de conexão de Área de Trabalho Remota: este computador não pode se conectar ao computador remoto.

Causa: a conta que você usou para se conectar não tem direitos de logon na Área de Trabalho Remota.

Todo computador Windows tem um grupo local Usuários da Área de Trabalho Remota que contém as contas e os grupos que podem fazer logon remotamente. Os membros do grupo local Administradores também têm acesso, mesmo que essas contas não sejam listadas no grupo local Usuários da Área de Trabalho Remota. Para computadores que ingressaram no domínio, o grupo local Administradores também contém os administradores de domínio para o domínio.

Certifique-se que a conta que você está usando para conectar-se tem direitos de logon de Área de Trabalho Remota. Como uma solução alternativa, use uma conta de administrador local ou de administrador de domínio pela Área de Trabalho Remota e, então, use o snap-in Gerenciamento de Computador (**Ferramentas do Sistema > Usuários e Grupos Locais > Grupos > Usuários da Área de Trabalho Remota**) para adicionar a conta desejada ao grupo local de Usuários da Área de Trabalho Remota.


## Solução de problemas detalhada

Se nenhum desses erros ocorreu e ainda não foi possível para você conectar-se à VM por meio da Área de Trabalho Remota, leia [este artigo](virtual-machines-rdp-detailed-troubleshoot.md) para descobrir outras causas.


## Recursos adicionais

[Pacote de diagnóstico do Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Como redefinir uma senha ou o serviço de Área de Trabalho Remota para máquinas virtuais Windows](virtual-machines-windows-reset-password.md)

[Como instalar e configurar o Azure PowerShell](../install-configure-powershell.md)

[Solucionar problemas de conexões SSH (Secure Shell) para uma máquina virtual do Azure baseada em Linux](virtual-machines-troubleshoot-ssh-connections.md)

[Solucionar problemas de acesso a um aplicativo executado em uma máquina virtual do Azure](virtual-machines-troubleshoot-access-application.md)

<!---HONumber=Sept15_HO4-->