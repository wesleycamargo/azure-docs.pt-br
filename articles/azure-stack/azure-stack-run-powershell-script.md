---
title: Implantar o Kit de desenvolvimento de pilha do Azure | Microsoft Docs
description: "Saiba como preparar o Kit de desenvolvimento de pilha do Azure e executar o script do PowerShell para implantá-lo."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 0ad02941-ed14-4888-8695-b82ad6e43c66
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/11/2017
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 7c320c6ba51ae0800407aab7aee92c42b2b441a7
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2017
---
# <a name="deploy-the-azure-stack-development-kit"></a>Implantar o Kit de desenvolvimento de pilha do Azure

*Aplica-se a: Kit de desenvolvimento de pilha do Azure*

Para implantar o [Kit de desenvolvimento de pilha do Azure](azure-stack-poc.md), você deve concluir as etapas a seguir:

1. [Baixe o pacote de implantação](https://azure.microsoft.com/overview/azure-stack/try/?v=try) para obter o Cloudbuilder.vhdx.
2. Prepare o cloudbuilder.vhdx para configurar o computador (o host do kit de desenvolvimento) no qual você deseja instalar o kit de desenvolvimento. Após essa etapa, o host do kit de desenvolvimento será inicializado a Cloudbuilder.vhdx.
3. Implante o kit de desenvolvimento no host do kit de desenvolvimento.

> [!NOTE]
> Para obter melhores resultados, mesmo se você quiser usar um ambiente desconectado da pilha do Azure, é aconselhável implantar enquanto estiver conectado à internet. Dessa forma, a versão de avaliação do Windows Server 2016 incluída com a instalação do kit de desenvolvimento pode ser ativada no momento da implantação.

## <a name="download-and-extract-the-development-kit"></a>Baixe e extraia o kit de desenvolvimento
1. Antes de iniciar o download, certifique-se de que o computador atende aos seguintes pré-requisitos:

  - O computador deve ter pelo menos 60 GB de espaço livre em disco disponível em quatro separados, idênticos discos rígidos lógicos além para o disco do sistema operacional.
  - [.NET framework 4.6 (ou uma versão posterior)](https://aka.ms/r6mkiy) deve ser instalado.

2. [Vá para a página de Introdução](https://azure.microsoft.com/overview/azure-stack/try/?v=try) onde você pode baixar o Kit de desenvolvimento de pilha do Azure, forneça os detalhes e, em seguida, clique em **enviar**.
3. Baixe e execute o [Verificador de implantação do Kit de desenvolvimento de pilha do Azure](https://go.microsoft.com/fwlink/?LinkId=828735&clcid=0x409) script Verificador de pré-requisitos. Esse script autônomo mostra as verificações de pré-requisitos feitas pela instalação para o Kit de desenvolvimento de pilha do Azure. Ele fornece uma maneira para confirmar que você cumpra os requisitos de hardware e software, antes de baixar o pacote maior para o Kit de desenvolvimento de pilha do Azure.
4. Em **baixar o software**, clique em **Kit de desenvolvimento de pilha do Azure**.

  > [!NOTE]
  > O download ASDK (AzureStackDevelopmentKit.exe) é approximiately 10GB por si só. Se você optar por também baixar o arquivo ISO de versão de avaliação de Windows Server 2016, o tamanho do download aumenta para cerca de 17GB. Você pode usar o arquivo ISO para criar e adicionar uma imagem de máquina virtual do Windows Server 2016 a pilha do Azure Marketplace após a conclusão da instalação ASDK. Observe que esta imagem de avaliação do Windows Server 2016 só pode ser usada com o ASDK e está sujeito aos termos de licença ASDK.

5. Após a conclusão do download, clique em **executar** para iniciar o Self-extractor ASDK (AzureStackDevelopmentKit.exe).
6. Leia e aceite o contrato de licença exibidos a **contrato de licença** página do Assistente de Self-Extractor e depois clique em **próximo**.
7. Revise as informações de declaração de privacidade exibidas no **Aviso importante** página do Assistente de Self-Extractor e depois clique em **próximo**.
8. Selecione o local dos arquivos de instalação de pilha do Azure a ser extraído no **Selecionar local de destino** página do Assistente de Self-Extractor e depois clique em **próximo**. O local padrão é *pasta atual*\Azure Kit de desenvolvimento de pilha. 
9. Revise o resumo de local de destino a **pronto para extrair** página do Assistente de Self-Extractor e depois clique em **extrair** para extrair o CloudBuilder.vhdx (aproximadamente 25 GB) e Arquivos de ThirdPartyLicenses.rtf. Este processo levará algum tempo para concluir.
10. Copiar ou mover o arquivo CloudBuilder.vhdx para a raiz da unidade C:\ (C:\CloudBuilder.vhdx) no computador host ASDK.

> [!NOTE]
> Depois de extrair os arquivos, você pode excluir o. EXE e. Arquivos BINÁRIOS para recuperar espaço no disco rígido. Ou, você pode fazer backup desses arquivos para que você não precisa baixar os arquivos novamente, se você precisar reimplantar o ASDK.

## <a name="deploy-the-asdk-using-a-guided-experience"></a>Implantar o ASDK usando uma experiência guiada
O ASDK pode ser implantado usando uma interface gráfica do usuário (GUI) fornecida baixando e executando o script do PowerShell asdk installer.ps1.

> [!NOTE]
> A interface do usuário do instalador para o Kit de desenvolvimento de pilha do Azure é um script de originários aberto baseado no WCF e do PowerShell.

### <a name="prepare-the-development-kit-host-using-a-guided-user-experience"></a>Preparar o host do kit de desenvolvimento usando uma experiência de usuário interativa
Antes de instalar o ASDK no computador host, o ambiente de ASDK deve ser preparado.
1. Entrar como Administrador Local para o computador de host ASDK.
2. Certifique-se de que o arquivo CloudBuilder.vhdx foi movido para a raiz da unidade C:\ (C:\CloudBuilder.vhdx).
3. Execute o script a seguir para baixar o arquivo de instalador do kit de desenvolvimento (asdk installer.ps1) da [repositório de ferramentas do Azure pilha GitHub](https://github.com/Azure/AzureStack-Tools) para o **C:\AzureStack_Installer** pasta no seu computador de host do kit de desenvolvimento:

  ```powershell
  # Variables
  $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
  $LocalPath = 'C:\AzureStack_Installer'
  # Create folder
  New-Item $LocalPath -Type directory
  # Download file
  Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
  ```

4. Em um console do PowerShell com privilégios elevados, inicie o **C:\AzureStack_Installer\asdk-installer.ps1** script e, em seguida, clique em **preparar o ambiente**.
5. Sobre o **selecione Cloudbuilder vhdx** página do instalador, navegue até e selecione o **cloudbuilder.vhdx** arquivo que você baixou e extraiu nas etapas anteriores. Nessa página também, opcionalmente, você pode habilitar o **adicionar drivers** caixa de seleção se você precisar adicionar drivers adicionais no computador de host do kit de desenvolvimento.  
6. Sobre o **configurações opcionais** , forneça a conta de administrador local para o computador de host do kit de desenvolvimento. 

  > [!IMPORTANT]
  > Se você não fornecer essas credenciais, você precisará direta ou acesso KVM ao host após reiniciar o computador como parte da configuração do kit de desenvolvimento.

7. Você também pode fornecer essas configurações opcionais sobre o **configurações opcionais** página:
    - **ComputerName**: essa opção define o nome do host do kit de desenvolvimento. O nome deve estar em conformidade com os requisitos de FQDN e deve ser de 15 caracteres ou menos. O padrão é um nome aleatório gerado pelo Windows.
    - **Fuso horário**: define o fuso horário para o host do kit de desenvolvimento. O padrão é (UTC-8:00) hora do Pacífico (EUA e Canadá).
    - **Configuração de IP estático**: define sua implantação usar um endereço IP estático. Caso contrário, quando o instalador é reinicializado no cloudbuilder.vhx, as interfaces de rede são configuradas com DHCP.
11. Clique em **Avançar**.
12. Se você escolher uma configuração de IP estático na etapa anterior, agora você deve:
    - Selecione um adaptador de rede. Verifique se você pode se conectar ao adaptador antes de clicar em **próximo**.
    - Verifique se o **endereço IP**, **Gateway**, e **DNS** valores estão corretos e, em seguida, clique em **próximo**.
13. Clique em **próximo** para iniciar o processo de preparação.
14. Quando a preparação indica **concluído**, clique em **próximo**.
15. Clique em **reinicializar agora** Inicialize o cloudbuilder.vhdx e continuar o processo de implantação.


### <a name="deploy-the-development-kit-using-a-guided-experience"></a>Implantar o kit de desenvolvimento usando uma experiência guiada
Depois de preparar o computador de host ASDK, o ASDK pode ser implantado na imagem CloudBuilder.vhdx usando as etapas a seguir. 
1. Depois que o computador host é inicializado com êxito na imagem de CloudBuilder.vhdx, faça logon usando as credenciais de administrador especificadas nas etapas anteriores. 
2. Abra um console do PowerShell com privilégios elevados e execute o **\AzureStack_Installer\asdk-installer.ps1** script (que agora pode ser em uma unidade diferente da imagem CloudBuilder.vhdx). Clique em **Instalar**.
3. No **tipo** caixa de lista suspensa, selecione **nuvem do Azure** ou **do AD FS**.
    - **Nuvem do Azure**: configura o Azure Active Directory (AD do Azure) como o provedor de identidade. Para usar essa opção, você precisará de uma conexão de internet, o nome completo do AD do Azure locatário de diretório na forma de *domainname*. c o m e credenciais de administrador global para o diretório especificado. 
    - **O AD FS**: O carimbo de padrão, o serviço de diretório será usado como o provedor de identidade. É a conta padrão para entrar com azurestackadmin@azurestack.local, e a senha a ser usada é fornecida como parte da instalação.
4. Em **senha de Administrador Local**, além de **senha** caixa, digite a senha de administrador local (que deve corresponder à senha de administrador local configurado atual) e, em seguida, clique em **Próxima**.
5. Selecione um adaptador de rede a ser usado para o kit de desenvolvimento e, em seguida, clique em **próximo**.
6. Selecione o DHCP ou configuração de rede estático para a máquina virtual BGPNAT01.
    - **DHCP** (padrão): A máquina virtual obtém a configuração de rede IP do servidor DHCP.
    - **Estático**: use essa opção somente se o DHCP não é possível atribuir um endereço IP válido para a pilha do Azure acessar a Internet. Um endereço IP estático deve ser especificado com o comprimento da máscara de sub-rede no formato CIDR (por exemplo, 10.0.0.5/24).
7. Opcionalmente, defina os seguintes valores:
    - **ID de VLAN**: define a ID de VLAN. Só use essa opção se o host e AzS BGPNAT01 devem configurar o ID de VLAN para acessar a rede física (e internet). 
    - **O encaminhador de DNS**: um servidor DNS é criado como parte da implantação da pilha do Azure. Para permitir que os computadores dentro da solução para resolver nomes fora o carimbo, forneça o servidor DNS de infraestrutura existente. O servidor DNS no carimbo encaminha solicitações de resolução de nome desconhecido para o servidor.
    - **Servidor de horário**: isso necessário campo define o servidor de horário a ser usado pelo kit de desenvolvimento. Esse parâmetro deve ser fornecido como um endereço IP do servidor de tempo válido. Não há suporte para nomes de servidor.
  
  > [!TIP]
  > Para localizar um servidor de horário de endereço IP, visite [pool.ntp.org](http:\\pool.ntp.org) ou executar ping time.windows.com. 
  
8. Clique em **Avançar**. 
9. Sobre o **verificando as propriedades de placa de interface de rede** página, você verá uma barra de progresso. 
    - Se ele for **não é possível baixar uma atualização**, siga as instruções na página.
    - Quando diz **concluído**, clique em **próximo**.
10. Em **resumo** , clique em **implantar**. Aqui você também pode copiar os comandos de instalação do PowerShell que serão usados para instalar o kit de desenvolvimento.
11. Se você estiver usando uma implantação do AD do Azure, você será solicitado a inserir suas credenciais de conta de administrador global do AD do Azure alguns minutos após o início da instalação.
12. O processo de implantação pode levar algumas horas, durante o qual o sistema é reinicializado automaticamente uma vez. Quando a implantação for bem-sucedido, exibe o console do PowerShell: **concluir: ação 'Implantação'**. Se a implantação falhar, você pode [reimplantar](azure-stack-redeploy.md) de zero ou use os seguinte comandos do PowerShell, na mesma janela do PowerShell com privilégios elevados, reiniciar a implantação da última etapa bem-sucedida:

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

   > [!IMPORTANT]
   > Se você quiser monitorar o progresso da implantação, entre como azurestack\AzureStackAdmin quando o host do kit de desenvolvimento é reiniciado durante a instalação. Se você entrar como um administrador local depois que o computador está ingressado no domínio, você não verá o progresso da implantação. Não execute novamente a implantação, em vez disso, entre como azurestack\AzureStackAdmin validar que está sendo executado.
   

## <a name="deploy-the-asdk-using-powershell"></a>Implantar o ASDK usando o PowerShell
As etapas anteriores analisou a implantar o ASDK usando uma experiência de usuário interativa. Como alternativa, você pode usar o PowerShell para implantar o ASDK no host do kit de desenvolvimento, seguindo as etapas nesta seção.

### <a name="configure-the-asdk-host-computer-to-boot-from-cloudbuildervhdx"></a>Configurar o computador de host ASDK para inicializar a partir CloudBuilder.vhdx
Esses comandos configurará o computador de host ASDK para inicializar o baixados e extraído pilha Azure virtual disco rígido (CloudBuilder.vhdx). Depois de concluir essas etapas, reinicie o computador de host ASDK.

1. Inicie um prompt de comando como administrador.
2. Execute o `bcdedit /copy {current} /d "Azure Stack"`
3. Copiar (CTRL + C) o valor CLSID retornado, incluindo o {} necessária ' s. Esse valor é conhecido como {CLSID} e precisará ser colado (CTRL + V ou o botão direito do mouse) nas etapas restantes.
4. Execute o `bcdedit /set {CLSID} device vhd=[C:]\CloudBuilder.vhdx` 
5. Execute o `bcdedit /set {CLSID} osdevice vhd=[C:]\CloudBuilder.vhdx` 
6. Execute o `bcdedit /set {CLSID} detecthal on` 
7. Execute o `bcdedit /default {CLSID}`
8. Para verificar as configurações de inicialização, execute `bcdedit`. 
9. Certifique-se de que o CloudBuilder.vhdx arquivo foi movido para a raiz da unidade C:\ (C:\CloudBuilder.vhdx) e reinicie o computador de host do kit de desenvolvimento. Quando o host ASDK for reiniciado, ele agora deve padrão para inicialização a partir da VM CloudBuilder.vhdx. 

### <a name="prepare-the-development-kit-host-using-powershell"></a>Preparar o host do kit de desenvolvimento usando o PowerShell 
Depois que o computador de host do kit de desenvolvimento é inicializado com êxito à imagem de CloudBuilder.vhdx, você pode abrir um console do PowerShell com privilégios elevados e execute os comandos nesta seção para implantar o ASDK no host do kit de desenvolvimento.

> [!IMPORTANT] 
> Instalação ASDK dá suporte a exatamente uma placa de interface de rede (NIC) de rede. Se você tiver várias placas de rede, certifique-se de que somente um está habilitado (e todos os outros são desativados) antes de executar o script de implantação.

Você pode implantar a pilha do Azure com o Azure AD ou AD FS como o provedor de identidade. A pilha do Azure, provedores de recursos e outros aplicativos funcionam da mesma maneira com ambos. Para saber mais sobre o que é compatível com o AD FS na pilha do Azure, consulte o [principais recursos e conceitos](.\azure-stack-key-features.md) artigo.

> [!TIP]
> Se você não fornecer quaisquer parâmetros de configuração (consulte parâmetros opcionais InstallAzureStackPOC.ps1 e os exemplos abaixo), você será solicitado a fornecer os parâmetros necessários.

### <a name="deploy-azure-stack-using-azure-ad"></a>Implantar a pilha do Azure usando o Azure AD 
Para implantar o Azure pilha **usando o AD do Azure como o provedor de identidade**, você deve ter conectividade com a internet diretamente ou através de um proxy transparente. Execute os seguintes comandos do PowerShell para implantar o kit de desenvolvimento usando o Azure AD:

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator     
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password
  ```

  Alguns minutos para instalação ASDK que você será solicitado para credenciais do Azure AD. Você deve fornecer credenciais de administrador global para seu locatário do AD do Azure. 

### <a name="deploy-azure-stack-using-ad-fs"></a>Implantar a pilha do Azure usando o AD FS 
Para implantar o kit de desenvolvimento **usando o AD FS como o provedor de identidade**, execute os seguintes comandos do PowerShell (basta adicionar o parâmetro - UseADFS): 

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator 
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -UseADFS
  ```

Em implantações do AD FS, o serviço de diretório do carimbo de padrão é usado como o provedor de identidade. É a conta padrão para entrar com azurestackadmin@azurestack.local, e a senha será definida como o que é fornecido como parte dos comandos de instalação do PowerShell.

O processo de implantação pode levar algumas horas, durante o qual o sistema é reinicializado automaticamente uma vez. Quando a implantação for bem-sucedido, exibe o console do PowerShell: **concluir: ação 'Implantação'**. Se a implantação falhar, você poderá tentar executar o script novamente usando o parâmetro-execute. Ou, você pode [reimplantar ASDK](.\azure-stack-redeploy.md) do zero.
> [!IMPORTANT]
> Se você quiser monitorar o progresso da implantação após a reinicialização do host ASDK, você deve entrar como AzureStack\AzureStackAdmin. Se você entrar como um administrador local depois que o computador host é reiniciado (e ingressar no domínio azurestack.local), você não verá o progresso da implantação. Não execute novamente a implantação, em vez disso, entre como azurestack validar que está sendo executado.
>

#### <a name="azure-ad-deployment-script-examples"></a>Exemplos de script de implantação de AD do Azure
Você pode gerar o script de toda a implantação do AD do Azure. Aqui estão alguns exemplos de comentários que incluem alguns parâmetros opcionais.

Se sua identidade do AD do Azure só é associada **um** diretório AD do Azure:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -TimeServer 52.168.138.145 #Example time server IP address.
```

Se sua identidade do AD do Azure está associada com **maior do que um** diretório AD do Azure:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" #Example: user@AADDirName.onmicrosoft.com 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -InfraAzureDirectoryTenantName "<specific Azure AD directory in the form of domainname.onmicrosoft.com>" -TimeServer 52.168.138.145 #Example time server IP address.
```

Se seu ambiente **não** ter o DHCP habilitado, você deve incluir os seguintes parâmetros adicionais para uma das opções acima (uso de exemplo fornecidos): 

```powershell
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -NatIPv4Subnet 10.10.10.0/24 -NatIPv4Address 10.10.10.3 -NatIPv4DefaultGateway 10.10.10.1 -TimeServer 10.222.112.26
```

### <a name="asdk-installazurestackpocps1-optional-parameters"></a>Parâmetros opcionais ASDK InstallAzureStackPOC.ps1
|Parâmetro|Obrigatório/Opcional|Descrição|
|-----|-----|-----|
|AdminPassword|Obrigatório|Define a conta de administrador local e todas as outras contas de usuário em todas as máquinas virtuais criadas como parte da implantação do kit de desenvolvimento. Esta senha deve corresponder a senha de administrador local atual no host.|
|InfraAzureDirectoryTenantName|Obrigatório|Define o diretório do locatário. Use esse parâmetro para especificar um diretório específico em que a conta do AAD tem permissões para gerenciar vários diretórios. Nome de um locatário de diretório do AAD no formato completo. c o m.|
|TimeServer|Obrigatório|Use esse parâmetro para especificar um servidor de tempo específico. Esse parâmetro deve ser fornecido como um endereço IP do servidor de tempo válido. Não há suporte para nomes de servidor.|
|InfraAzureDirectoryTenantAdminCredential|Opcional|Define o nome de usuário do Active Directory do Azure e a senha. Essas credenciais do Azure devem ser um ID. Org|
|InfraAzureEnvironment|Opcional|Selecione o ambiente do Azure com o qual você deseja registrar essa implantação de pilha do Azure. As opções incluem pública do Azure, Azure - China, Azure - governo dos EUA.|
|DNSForwarder|Opcional|Um servidor DNS é criado como parte da implantação da pilha do Azure. Para permitir que os computadores dentro da solução para resolver nomes fora o carimbo, forneça o servidor DNS de infraestrutura existente. O servidor DNS no carimbo encaminha solicitações de resolução de nome desconhecido para o servidor.|
|NatIPv4Address|Necessário para suporte de NAT DHCP|Define um endereço IP estático para MAS BGPNAT01. Use esse parâmetro apenas se o DHCP não puder atribuir um endereço IP válido para acessar a Internet.|
|NatIPv4Subnet|Necessário para suporte de NAT DHCP|Prefixo de sub-rede IP usado para DHCP sobre suporte NAT. Use esse parâmetro apenas se o DHCP não puder atribuir um endereço IP válido para acessar a Internet.|
|PublicVlanId|Opcional|Define a ID de VLAN. Somente use esse parâmetro se o host e BGPNAT01 MAS deverá configurar o ID de VLAN para acessar a rede física (e Internet). Por exemplo,.\InstallAzureStackPOC.ps1-Verbose - PublicVLan 305|
|Executar novamente|Opcional|Use este sinalizador para executar novamente a implantação. Todas as entradas anteriores é usada. Inserir novamente dados previamente fornecidos não são suportados porque vários valores exclusivos são gerados e usados para implantação.|

## <a name="activate-the-administrator-and-tenant-portals"></a>Ativar os portais de administrador e locatário
Depois de implantações que usam o AD do Azure, você deve ativar ambos os portais de administrador e locatário pilha do Azure. Essa ativação consente fornecendo o portal de pilha do Azure e o Azure Resource Manager as permissões corretas (listadas na página de consentimento) para todos os usuários do diretório.

- Para o portal do administrador, navegue até https://adminportal.local.azurestack.external/guest/signup, leia as informações e, em seguida, clique em **aceitar**. Depois de aceitar, você pode adicionar administradores de serviço que não são administradores de locatário de diretório também.

- Para o portal de locatário, navegue até https://portal.local.azurestack.external/guest/signup, leia as informações e, em seguida, clique em **aceitar**. Após aceitar, os usuários no diretório podem entrar portal de locatário. 

> [!NOTE] 
> Se os portais não estão ativados, somente o administrador de diretório pode entrar e usar os portais. Se nenhum outro usuário fizer logon, eles verão um erro informando que o administrador não concedeu permissões a outros usuários. Quando o administrador não pertence ao diretório para que Azure pilha está registrada nativamente, o diretório de pilha do Azure deve ser anexado à URL de ativação. Por exemplo, se for registrada Azure pilha fabrikam.onmicrosoft.com e o usuário administrador é admin@contoso.com, navegue até https://portal.local.azurestack.external/guest/signup/fabrikam.onmicrosoft.com para ativar o portal. 

## <a name="reset-the-password-expiration-policy"></a>Redefina a política de expiração de senha 
Para certificar-se de que a senha para o host do kit de desenvolvimento não expira antes de seu período de avaliação terminar, siga estas etapas depois de implantar o ASDK.

### <a name="to-change-the-password-expiration-policy-from-powershell"></a>Para alterar a política de expiração de senha do Powershell:
Em um console do Powershell com privilégios elevados, execute o comando:

```powershell
Set-ADDefaultDomainPasswordPolicy -MaxPasswordAge 180.00:00:00 -Identity azurestack.local
```

### <a name="to-change-the-password-expiration-policy-manually"></a>Para alterar manualmente a política de expiração de senha:
1. No host do kit de desenvolvimento, abra **Group Policy Management** e navegue até **Group Policy Management** – **floresta: azurestack.local** – **domínios** – **azurestack.local**.
2. Clique com botão direito **Default Domain Policy** e clique em **editar**.
3. No Editor de gerenciamento de diretiva de grupo, navegue até **configuração do computador** – **políticas** – **configurações do Windows** – **as configurações de segurança**– **Políticas de conta** – **política de senha**.
4. No painel direito, clique duas vezes em **duração máxima da senha**.
5. No **duração máxima da senha propriedades** caixa de diálogo, altere o **senha expirará em** valor 180, em seguida, clique em **Okey**.


## <a name="next-steps"></a>Próximas etapas

[Conectar-se ao Azure Stack](azure-stack-connect-azure-stack.md)

[Configurar o PowerShell para ambientes de pilha do Azure](azure-stack-powershell-configure-quickstart.md)

[Registrar a pilha do Azure com sua assinatura do Azure](azure-stack-register.md)



