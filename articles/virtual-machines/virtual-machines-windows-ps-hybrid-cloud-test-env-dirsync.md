<properties 
	pageTitle="Ambiente de teste do DirSync do Office 365 | Microsoft Azure" 
	description="Aprenda a configurar um servidor de Sincronização de Diretórios (DirSync) do Office 365 em uma nuvem híbrida para testes profissionais de TI ou testes de desenvolvimento." 
	services="virtual-machines-windows" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines-windows" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/01/2016" 
	ms.author="josephd"/>

# Configurar a Sincronização de Diretórios (DirSync) do Office 365 em uma nuvem híbrida para teste

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico.
 
Este tópico orienta a criação de um ambiente de nuvem híbrida para testar a Sincronização de Diretórios (DirSync) do Office 365 com sincronização de senha hospedada no Microsoft Azure. Veja abaixo a configuração resultante.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync-ph3.png)
 
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

Se você ainda não tiver uma assinatura do Azure, poderá se inscrever em uma conta gratuita no [Experimente o Azure](https://azure.microsoft.com/pricing/free-trial/). Se você tiver uma assinatura do MSDN ou do Visual Studio, confira [Crédito mensal do Azure para assinantes do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

## Fase 1: Configurar o ambiente de nuvem híbrida

Use as instruções no tópico [Configurar um ambiente de nuvem híbrida para testes](virtual-machines-windows-ps-hybrid-cloud-test-env-base.md). Como esse ambiente de teste não exige a presença do servidor APP1 na sub-rede Corpnet, fique à vontade para desligá-lo por enquanto.

Esta é a configuração atual.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync-ph1.png)

> [AZURE.NOTE] Para a Fase 1, você também pode configurar o [ambiente de teste de nuvem híbrida simulada](virtual-machines-windows-ps-hybrid-cloud-test-env-sim.md).

## Fase 2: Configurar a versão de avaliação do Office 365 FastTrack

Para iniciar sua avaliação do Office 365 FastTrack, é necessário um nome de empresa fictício e uma conta da Microsoft. Recomendamos que você use, como nome da empresa, uma variante do nome da empresa Contoso, que é uma empresa fictícia usada no conteúdo de exemplo da Microsoft, embora isso não seja necessário.

Em seguida, inscreva-se para uma nova conta da Microsoft. Acesse **http://outlook.com** e crie uma conta com um endereço de email como user123@outlook.com. Você se inscreverá para uma avaliação do Office 365 FastTrack usando essa conta.

Em seguida, inscreva-se para obter uma nova versão de avaliação do Office 365 Enterprise E3.

1.	Faça logon em CLIENT1 com as credenciais da conta CORP\\User1.
2.	Abra o Internet Explorer e acesse **https://go.microsoft.com/fwlink/p/?LinkID=403802**.
3.	Passe pelo processo de inscrição para obter a versão de avaliação do Office 365 Enterprise E3.

Quando for solicitado o **endereço de email comercial**, especifique a nova conta da Microsoft.

Quando o sistema solicitar a criação de uma ID, digite o nome de uma conta inicial do Office 365, o nome da empresa fictícia e uma senha. Guarde o endereço de email resultante (como user123@contoso123.onmicrosoft.com) e a senha em um local seguro. Você vai precisar dessas informações para concluir a configuração do Azure AD Connect na fase 3.

Quando concluir, a página principal do Portal do Office 365 deve ser exibida. Na Faixa de Opções superior, clique em **Administrador** e em **Office 365**. A página do Centro de Administração do Office 365 aparece. Mantenha essa página aberta no CLIENT1.

Esta é a configuração atual.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync-ph2.png)

## Fase 3: Configurar o servidor DirSync (DS1)

No portal do Azure, inicie o computador DC2, se necessário.

Primeiro, crie uma máquina virtual do Azure para o DS1 com estes comandos, no prompt de comando do Azure PowerShell no seu computador local. Antes de executar estes comandos, preencha os valores variáveis e remova os caracteres < and >.

	$rgName="<your resource group name>"
	$locName="<your Azure location, such as West US>"
	$saName="<your storage account name>"
	
	$vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
	$subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
	$pip=New-AzureRMPublicIpAddress -Name DS1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureRMNetworkInterface -Name DS1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
	$vm=New-AzureRMVMConfig -VMName DS1 -VMSize Standard_A1
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for DS1."
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName DS1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DS1-TestLab-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name DS1-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Em seguida, use o portal do Azure para se conectar à máquina virtual DS1 com as credenciais da conta de administrador local.

Em seguida, configure uma regra do Firewall do Windows para permitir o tráfego e testar a conectividade básica. Em um prompt de comando com nível de administrador do Windows PowerShell no DS1, execute estes comandos.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc2.corp.contoso.com

O comando ping deve resultar em quatro respostas bem-sucedidas do endereço IP 192.168.0.4.

Em seguida, integre a DS1 ao domínio CORP do Active Directory com estes comandos no prompt do Windows PowerShell.

	Add-Computer -DomainName corp.contoso.com
	Restart-Computer

Use a conta CORP\\User1 quando for solicitado a fornecer credenciais de conta de domínio para o comando **Add-Computer**.

Após a reinicialização, use o portal do Azure para conectar-se ao DS1 com a conta e senha de CORP\\User1.

Em seguida, instale o .NET 3.5 no DS1 com este comando em um prompt de comando do Windows PowerShell de nível de administrador.

	Add-WindowsFeature NET-Framework-Core

Em seguida, habilite a Sincronização de Diretórios para sua versão de avaliação do Office 365 FastTrack.

1.	Em CLIENT1, na página do **Centro de administração do Office 365**, no painel esquerdo, clique em **Usuários** e em **Usuários Ativos**.
2.	Para **Sincronização do Active Directory**, clique em **Configurar**.
3.	Na página Configurar e gerenciar a sincronização do Active Directory, da etapa 3, clique em **Ativar**.
4.	Quando a pergunta **Deseja ativar a sincronização do Active Directory?** for exibida, clique em **Ativar**. Depois disso, a mensagem **A sincronização do Active Directory está ativada** é exibida na etapa 3.
5.	Deixe a página **Configurar e gerenciar a sincronização do Active Directory** aberta no CLIENT1.

Em seguida, do prompt do Windows PowerShell em DC1, execute estes comandos **um de cada vez** para criar uma nova unidade organizacional chamada contoso\_users e adicionar duas novas contas de usuário, para Marci Kaufman e Lynda Meyer.

	New-ADOrganizationalUnit -Name contoso_users -Path "DC=corp,DC=contoso,DC=com"
	New-ADUser -SamAccountName marcik -AccountPassword (Read-Host "Set user password" -AsSecureString) -name "Marci Kaufman" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Path "OU=contoso_users,DC=corp,DC=contoso,DC=com"
	New-ADUser -SamAccountName lyndam -AccountPassword (Read-Host "Set user password" -AsSecureString) -name "Lynda Meyer" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Path "OU=contoso_users,DC=corp,DC=contoso,DC=com"

Quando você executo cada comando **New-ADUser** do Windows PowerShell, você é solicitado a fornecer a senha do novo usuário. Registre essas senhas e armazene-as em um local seguro. Você precisará delas mais tarde.

Em seguida, instale e configure a ferramenta Azure AD Connect no DS1.

1.	Abra o Internet Explorer e digite **https://www.microsoft.com/download/details.aspx?id=47594** na barra de **Endereços** e pressione Enter.
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

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync-example.png)

A coluna **Status** indica que a conta foi obtida através da sincronização com um domínio do Active Directory.

Em seguida, demonstre a sincronização de senha do Office 365 com a conta Laura Cunha do Microsoft Active Directory.

1.	Em CLIENT1, na página **Usuários Ativos**, selecione a conta **Laura Cunha**.
2.	Nas propriedades da conta Lynda Meyer, em **Licença Atribuída**, clique em **Editar**.
3.	Na guia **Atribuir Licença**, escolha um local em **Definir local do usuário** (como Brasil).
4.	Selecione **Microsoft Office 365 Plano E3** e clique em **Salvar**.
5.	Feche o Internet Explorer.
6.	Abra o Internet Explorer e acesse **http://portal.microsoftonline.com**.
7.	Faça logon com as credenciais do Office 365 de Laura Cunha. O nome de usuário dela será laurac@<*Seu nome fictício*>.onmicrosoft.com. A senha é a senha da conta de usuário Laura Cunha do Active Directory.
8.	Após o logon bem-sucedido, você verá a página do portal principal do Office 365 com **Vamos fazer a diferença hoje**.

Esta é a configuração atual.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync-ph3.png)
 
Este ambiente agora está pronto para você executar testes de aplicativos do Office 365 que se baseiam na funcionalidade DirSync do Office 365 ou testar a funcionalidade e o desempenho de DirSync do DS1.

## Próxima etapa

- Implante essa carga de trabalho [em produção](http://technet.microsoft.com/library/dn635310.aspx).

<!---HONumber=AcomDC_0413_2016-->