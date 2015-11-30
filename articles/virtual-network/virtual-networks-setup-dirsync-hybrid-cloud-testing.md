<properties 
	pageTitle="Ambiente de teste do DirSync do Office 365 | Microsoft Azure" 
	description="Aprenda a configurar um servidor de Sincronização de Diretórios (DirSync) do Office 365 em uma nuvem híbrida para testes profissionais de TI ou testes de desenvolvimento." 
	services="virtual-network" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-service-management"/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/13/2015" 
	ms.author="josephd"/>

# Configurar a Sincronização de Diretórios (DirSync) do Office 365 em uma nuvem híbrida para teste

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.
 

Este tópico orienta a criação de um ambiente de nuvem híbrida para testar a Sincronização de Diretórios (DirSync) do Office 365 com sincronização de senha hospedada no Microsoft Azure. Veja abaixo a configuração resultante.

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_3.png)
 
Esta configuração simula um servidor DirSync no ambiente de produção do Azure em seu local na Internet. Ele consiste em:

- Uma rede local simplificada (a sub-rede Corpnet).
- Uma rede virtual entre locais hospedada no Azure (TestVNET).
- Uma conexão de VPN site a site.
- Uma assinatura de avaliação do Office 365 FastTrack.
- Um servidor DirSync executando a ferramenta Azure AD Connect e um controlador de domínio secundário na rede virtual TestVNET.

Esta configuração proporciona uma base e um ponto de partida comum com os quais é possível:

- Desenvolva e teste aplicativos para o Office 365, que dependem de sincronização com um domínio local do Active Directory usando sincronização de senha.
- Realizar testes dessa carga de trabalho de TI baseada em nuvem.

Há três fases principais para configurar esse ambiente de teste de nuvem híbrida:

1.	Configurar um ambiente de nuvem híbrida para testes.
2.	Configurar a avaliação do Office 365 FastTrack.
3.	Configurar o servidor DirSync (DS1).

Se você não tiver uma conta do Azure, será possível se inscrever em uma conta gratuita no [Experimente o Azure](http://azure.microsoft.com/pricing/free-trial/). Se tiver uma assinatura do MSDN, consulte [Benefício do Azure para assinantes do MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

## Fase 1: Configurar o ambiente de nuvem híbrida

Use as instruções no tópico [Configurar um ambiente de nuvem híbrida para testes](virtual-networks-setup-hybrid-cloud-environment-testing.md). Como esse ambiente de teste não exige a presença do servidor APP1 na sub-rede Corpnet, fique à vontade para desligá-lo por enquanto.

Esta é a configuração atual.

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_1.png)

> [AZURE.NOTE]Para a Fase 1, você também pode configurar o ambiente de teste de nuvem híbrida simulada. Consulte [Configurar um ambiente de nuvem híbrida simulada para testes](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md) para ver as instruções.

## Fase 2: Configurar a versão de avaliação do Office 365 FastTrack

Para iniciar sua avaliação do Office 365 FastTrack, é necessário um nome de empresa fictício e uma conta da Microsoft. Recomendamos que você use, como nome da empresa, uma variante do nome da empresa Contoso, que é uma empresa fictícia usada no conteúdo de exemplo da Microsoft, embora isso não seja necessário.

Em seguida, inscreva-se para uma nova conta da Microsoft. Acesse ****http://outlook.com** e crie uma conta com um endereço de email como user123@outlook.com. Você se inscreverá para uma avaliação do Office 365 FastTrack usando essa conta.

Em seguida, inscreva-se para obter uma nova versão de avaliação do Office 365 Enterprise E3.

1.	Faça logon em CLIENT1 com as credenciais da conta CORP\\User1.
2.	Abra o Internet Explorer e acesse ****https://go.microsoft.com/fwlink/p/?LinkID=403802**.
3.	Passe pelo processo de inscrição para obter a versão de avaliação do Office 365 Enterprise E3.

Quando for solicitado o **endereço de email comercial**, especifique a nova conta da Microsoft.

Quando o sistema solicitar a criação de uma ID, digite o nome de uma conta inicial do Office 365, o nome da empresa fictícia e uma senha. Guarde o endereço de email resultante (como user123@contoso123.onmicrosoft.com) e a senha em um local seguro. Você vai precisar dessas informações para concluir a configuração do Azure AD Connect na fase 3.

Quando concluir, a página principal do Portal do Office 365 deve ser exibida. Na Faixa de Opções superior, clique em **Administrador** e em **Office 365**. A página do Centro de Administração do Office 365 aparece. Mantenha essa página aberta no CLIENT1.

Esta é a configuração atual.

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_2.png)

## Fase 3: Configurar o servidor DirSync (DS1)

Primeiro, crie uma máquina virtual do Azure para o DS1 com estes comandos, no prompt de comando do Azure PowerShell no computador local. Antes de executar estes comandos, preencha os valores variáveis e remova os caracteres < and >.

	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$cred1=Get-Credential -Message "Type the name and password of the local administrator account for DS1."
	$cred2=Get-Credential -UserName "CORP\User1" -Message "Now type the password for the CORP\User1 account."
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name DS1 -InstanceSize Medium -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

Em seguida, conecte-se à máquina virtual DS1.

1.	Na página de Máquinas Virtuais do Portal de Gerenciamento do Azure, clique em **Executando** na coluna STATUS da máquina virtual DS1.
2.	Na barra de tarefas, clique em **Conectar**. 
3.	Quando receber uma solicitação para abrir DS1.rdp, clique em **Abrir**.
4.	Quando receber uma caixa de mensagem de Conexão de Área de Trabalho Remota, clique em **Conectar**.
5.	Quando solicitado a fornecer credenciais, use estas:
	- Nome: **CORP\\User1**
	- Senha: [Senha da conta User1]
6.	Quando receber uma caixa de mensagem de Conexão de Área de Trabalho Remota referindo-se aos certificados, clique em **Sim**.

Em seguida, configure uma regra do Firewall do Windows para permitir o tráfego e testar a conectividade básica. Em um prompt de comando com nível de administrador do Windows PowerShell no DS1, execute estes comandos.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

O comando ping deve resultar em quatro respostas bem-sucedidas do endereço IP 10.0.0.1.

Em seguida, instale o .NET 3.5 no DS1 com este comando no prompt de comando do Windows PowerShell.

	Add-WindowsFeature NET-Framework-Core

Em seguida, habilite a Sincronização de Diretórios para sua versão de avaliação do Office 365 FastTrack.

1.	Em CLIENT1, na página do **Centro de administração do Office 365**, no painel esquerdo, clique em **Usuários** e em **Usuários Ativos**.
2.	Para **Sincronização do Active Directory**, clique em **Configurar**.
3.	Na página Configurar e gerenciar a sincronização do Active Directory, da etapa 3, clique em **Ativar**.
4.	Quando a pergunta **Deseja ativar a sincronização do Active Directory?** for exibida, clique em **Ativar**. Depois disso, a mensagem **A sincronização do Active Directory está ativada** é exibida na etapa 3.
5.	Deixe a página **Configurar e gerenciar a sincronização do Active Directory** aberta em CLIENT1.

Em seguida, faça logon no DC1 com a conta de CORP\\User1 e abra um prompt de comando do Windows PowerShell com nível de administrador. Execute estes comandos para criar uma nova unidade organizacional chamada contoso\_users e adicionar duas novas contas de usuário, para Marci Kaufman e Laura Cunha.

	New-ADOrganizationalUnit -Name contoso_users -Path "DC=corp,DC=contoso,DC=com"
	New-ADUser -SamAccountName marcik -AccountPassword (Read-Host "Set user password" -AsSecureString) -name "Marci Kaufman" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Path "OU=contoso_users,DC=corp,DC=contoso,DC=com"
	New-ADUser -SamAccountName lyndam -AccountPassword (Read-Host "Set user password" -AsSecureString) -name "Lynda Meyer" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Path "OU=contoso_users,DC=corp,DC=contoso,DC=com"

Sempre que executa os comandos do Azure PowerShell, você é solicitado a fornecer a nova senha de usuário. Registre essas senhas e armazene-as em um local seguro. Você precisará delas mais tarde.

Em seguida, instale e configure a ferramenta Azure AD Connect no DS1.

1.	Abra o Internet Explorer e digite ****https://www.microsoft.com/download/details.aspx?id=47594** na barra de **Endereços** e pressione Enter.
2.	Execute o programa de instalação do Microsoft Azure AD Connect.
3.	Na área de trabalho, clique duas vezes em **Azure AD Connect**.
4.	Na **Página Inicial**, selecione **Concordo com os termos de licença e aviso de privacidade** e clique em **Continuar**.
5.	Na página **Configurações expressas**, clique em **Usar Configurações Expressas**.
6.	Na página **Conectar ao Azure AD**, digite o endereço de email e a senha da conta inicial que você criou quando configurou a versão de avaliação do FastTrack para Office 365 na fase 2. Clique em **Próximo**.
7.	Na página **Conectar ao AD DS**, digite **CORP\\User1** em **Nome de usuário** e a senha da conta User1 em **Senha**. Clique em Próximo.
8.	Na página **Pronto para configurar**, revise as configurações e clique em **Instalar**.
9.	Na página **Configuração concluída**, clique em **Sair**.

Em seguida, verifique se as contas de usuário no domínio CORP estão sincronizadas com o Office 365. Observe que pode levar alguns minutos para ocorrer a sincronização.

Em CLIENT1, na página **Configurar e gerenciar a sincronização do Active Directory**, clique no link **Usuários** na etapa 6 desta página. Se a sincronização de diretório tiver ocorrido com êxito, você verá algo semelhante a isto.

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_4.png)

A coluna **Status** indica que a conta foi obtida através da sincronização com um domínio do Active Directory.

Em seguida, demonstre a sincronização de senha do Office 365 com a conta Laura Cunha do Microsoft Active Directory.

1.	Em CLIENT1, na página **Usuários Ativos**, selecione a conta **Laura Cunha**.
2.	Nas propriedades da conta Laura Cunha, em **Licença atribuída**, clique em **Editar**.
3.	Na guia **Atribuir Licença**, escolha um local em **Definir local do usuário** (como Brasil).
4.	Selecione **Microsoft Office 365 Plano E3** e clique em **Salvar**.
5.	Feche o Internet Explorer.
6.	Abra o Internet Explorer e acesse ****http://portal.microsoftonline.com**.
7.	Faça logon com as credenciais do Office 365 de Laura Cunha. O nome de usuário dela será laurac@<*Seu nome fictício*>.onmicrosoft.com. A senha é a senha da conta de usuário Laura Cunha do Active Directory.
8.	Após o logon bem-sucedido, você verá a página principal do Portal do Office 365 com a mensagem **Vamos fazer a diferença hoje**.

Esta é a configuração atual.

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_3.png)
 
Este ambiente agora está pronto para você executar testes de aplicativos do Office 365 que se baseiam na funcionalidade DirSync do Office 365 ou testar a funcionalidade e o desempenho de DirSync do DS1.

## Recursos adicionais

[Implantar DirSync (Sincronização de Diretório) do Office 365 no Microsoft Azure](http://technet.microsoft.com/library/dn635310.aspx)

[Soluções usando os servidores do Office e a nuvem](http://technet.microsoft.com/library/dn262744.aspx)

[Configurar um ambiente de nuvem híbrida para teste](virtual-networks-setup-hybrid-cloud-environment-testing.md)

[Configurar um farm de intranet do SharePoint em uma nuvem híbrida para teste](virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

[Configurar um aplicativo LOB baseado na Web em uma nuvem híbrida para teste](virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Configurar um ambiente de nuvem híbrida simulado para testes](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)

[Ambientes de teste de nuvem híbrida do Azure](../virtual-machines/virtual-machines-hybrid-cloud-test-environments.md)

[Diretrizes de implementação dos Serviços de Infraestrutura do Azure](../virtual-machines/virtual-machines-infrastructure-services-implementation-guidelines.md)

<!---HONumber=Nov15_HO4-->