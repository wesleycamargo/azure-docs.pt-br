---
title: Implantar o Kit de desenvolvimento de pilha do Azure | Microsoft Docs
description: "Saiba como preparar o Kit de desenvolvimento de pilha do Azure e executar o script do PowerShell para implantá-lo."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 0ad02941-ed14-4888-8695-b82ad6e43c66
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/17/2017
ms.author: erikje
ms.openlocfilehash: b67cabf0ecdb48f137bfcfbce95eee568a1c298d
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2017
---
# <a name="deploy-the-azure-stack-development-kit"></a>Implantar o Kit de desenvolvimento de pilha do Azure

*Aplica-se a: Kit de desenvolvimento de pilha do Azure*

Para implantar o [Kit de desenvolvimento de pilha do Azure](azure-stack-poc.md), você deve concluir as etapas a seguir:

1. [Baixe o pacote de implantação](https://azure.microsoft.com/overview/azure-stack/try/?v=try) para obter o Cloudbuilder.vhdx.
2. [Preparar o cloudbuilder.vhdx](#prepare-the-development-kit-host) executando o script asdk installer.ps1 para configurar o computador (o host do kit de desenvolvimento) no qual você deseja instalar o kit de desenvolvimento. Após essa etapa, o host do kit de desenvolvimento será inicializado a Cloudbuilder.vhdx.
3. [Implantar o kit de desenvolvimento](#deploy-the-development-kit) no host do kit de desenvolvimento.

> [!NOTE]
> Para obter melhores resultados, mesmo se você quiser usar um ambiente desconectado da pilha do Azure, é aconselhável implantar enquanto estiver conectado à internet. Dessa forma, a versão de avaliação do Windows Server 2016 pode ser ativada no momento da implantação.
> 
> 

## <a name="download-and-extract-the-development-kit"></a>Baixe e extraia o kit de desenvolvimento
1. Antes de iniciar o download, certifique-se de que o computador atende aos seguintes pré-requisitos:

   * O computador deve ter pelo menos 60 GB de espaço livre em disco.
   * [.NET framework 4.6 (ou uma versão posterior)](https://aka.ms/r6mkiy) deve ser instalado.

2. [Vá para a página de Introdução](https://azure.microsoft.com/overview/azure-stack/try/?v=try), forneça os detalhes e clique em **enviar**.
3. Em **baixar o software**, clique em **Kit de desenvolvimento de pilha do Azure**.
4. Execute o arquivo AzureStackDownloader.exe baixado.
5. No **Downloader de Kit de desenvolvimento do Azure pilha** janela, siga as etapas 1 a 5.
6. Após a conclusão do download, clique em **executar** para iniciar o MicrosoftAzureStackPOC.exe.
7. Examine a tela do contrato de licença e informações do assistente Self-Extractor e, em seguida, clique em **próximo**.
8. Examine a tela de declaração de privacidade e informações do assistente Self-Extractor e, em seguida, clique em **próximo**.
9. Selecione o destino para os arquivos extraídos, clique em **próximo**.
   * O padrão é: <drive letter>:\<pasta atual > \Microsoft Azure pilha
10. Examine as informações do assistente Self-Extractor e tela de local de destino e, em seguida, clique em **extrair** para extrair os arquivos de ThirdPartyLicenses.rtf e CloudBuilder.vhdx (~ 25 GB). Este processo levará algum tempo para concluir.

> [!NOTE]
> Depois de extrair os arquivos, você pode excluir os arquivos exe e bin para recuperar espaço no computador. Ou, você pode mover esses arquivos para outro local para que, se for necessário reimplantar você não precisam baixar os arquivos novamente.
> 
> 

## <a name="prepare-the-development-kit-host"></a>Preparar o host do kit de desenvolvimento
1. Certifique-se de que você pode fisicamente conectar ao host do kit de desenvolvimento, ou ter acesso de console físico (como KVM). Você deve ter esse acesso depois que você reinicialize o host do kit de desenvolvimento na etapa 13 abaixo.
2. Verifique se o host do kit de desenvolvimento atende a [requisitos mínimos](azure-stack-deploy.md). Você pode usar o [Verificador de implantação do Azure pilha](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) para confirmar suas necessidades.
3. Entrar como Administrador Local para o host do kit de desenvolvimento.
4. Copiar ou mover o arquivo CloudBuilder.vhdx para a raiz da unidade C:\ (C:\CloudBuilder.vhdx).
5. Execute o script a seguir para baixar o arquivo de instalador do kit de desenvolvimento (asdk installer.ps1) para a pasta c:\AzureStack_Installer em seu host do kit de desenvolvimento.
    ```powershell
    # Variables
    $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
    $LocalPath = 'c:\AzureStack_Installer'

    # Create folder
    New-Item $LocalPath -Type directory

    # Download file
    Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
    ```
6. Abra um console do PowerShell com privilégios elevados > execute o script C:\AzureStack_Installer\asdk-installer.ps1 > clique **preparar o ambiente**.
7. Sobre o **Cloudbuilder selecione vhdx** página do instalador, navegue até e selecione o arquivo cloudbuilder.vhdx que você baixou nas etapas anteriores.
8. Opcional: Verifique o **adicionar drivers** caixa para especificar uma pasta que contém os drivers adicionais que você deseja no host.
9. Sobre o **configurações opcionais** , forneça a conta de administrador local para o host do kit de desenvolvimento. Se você não fornecer essas credenciais, você precisará ter acesso KVM ao host durante o processo de instalação abaixo.
10. Também no **configurações opcionais** página, você tem a opção de definir o seguinte:
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

## <a name="deploy-the-development-kit"></a>Implantar o kit de desenvolvimento
1. Entrar como Administrador Local para o host do kit de desenvolvimento. Use as credenciais especificadas nas etapas anteriores.

    > [!IMPORTANT]
    > Para implantações do Active Directory do Azure, a pilha do Azure requer acesso à Internet, diretamente ou através de um proxy transparente. A implantação dá suporte a exatamente uma NIC de rede. Se você tiver várias placas de rede, certifique-se de que somente um está habilitado (e todos os outros são desativados) antes de executar o script de implantação na próxima seção.
    
2. Abra um console do PowerShell com privilégios elevados > execute o script \AzureStack_Installer\asdk-installer.ps1 (que pode estar em uma unidade diferente de Cloudbuilder.vhdx) > clique **instalar**.
3. No **tipo** selecione **nuvem do Azure** ou **ADFS**.
    - **Nuvem do Azure**: Active Directory do Azure é o provedor de identidade. Use esse parâmetro para especificar um diretório específico em que a conta do AAD tem permissões de administrador global. Nome completo de um locatário de diretório AAD. Por exemplo,. c o m. 
    - **ADFS**: O serviço de diretório do carimbo de padrão é o provedor de identidade, é a conta padrão para entrar com azurestackadmin@azurestack.local, e a senha a ser usada é fornecida como parte da instalação.
4. Em **senha de Administrador Local**, além de **senha** caixa, digite a senha de administrador local (que deve corresponder à senha de administrador local configurado atual) e, em seguida, clique em **Próxima**.
5. Selecione um adaptador de rede a ser usado para o kit de desenvolvimento e, em seguida, clique em **próximo**.
6. Selecione o DHCP ou configuração de rede estático para a máquina virtual BGPNAT01.
    - **DHCP** (padrão): A máquina virtual obtém a configuração de rede IP do servidor DHCP.
    - **Estático**: use essa opção somente se o DHCP não é possível atribuir um endereço IP válido para a pilha do Azure acessar a Internet. Um endereço IP estático deve ser especificado com o comprimento da máscara de sub-rede (por exemplo, 10.0.0.5/24).
7. Opcionalmente, defina os seguintes valores:
    - **ID de VLAN**: define a ID de VLAN. Só use essa opção se o host e AzS BGPNAT01 devem configurar o ID de VLAN para acessar a rede física (e Internet). 
    - **O encaminhador de DNS**: um servidor DNS é criado como parte da implantação da pilha do Azure. Para permitir que os computadores dentro da solução para resolver nomes fora o carimbo, forneça o servidor DNS de infraestrutura existente. O servidor DNS no carimbo encaminha solicitações de resolução de nome desconhecido para o servidor.
    - **Servidor de horário**: isso necessário campo define a hora do servidor e deve ser um endereço IP. Para localizar um servidor de horário de endereço IP, visite [pool.ntp.org](http:\\pool.ntp.org) ou executar ping time.windows.com. 
8. Clique em **Avançar**. 
9. Sobre o **verificando as propriedades de placa de interface de rede** página, você verá uma barra de progresso. 
    - Se ele for **não é possível baixar uma atualização**, siga as instruções na página.
    - Quando diz **concluído**, clique em **próximo**.
10. Em **resumo** , clique em **implantar**.
11. Se você estiver usando uma implantação do Active Directory do Azure, você será solicitado a inserir suas credenciais de conta de administrador global do Active Directory do Azure.
12. O processo de implantação pode levar algumas horas, durante o qual o sistema é reinicializado automaticamente uma vez.
   
   > [!IMPORTANT]
   > Se você quiser monitorar o progresso da implantação, entre como azurestack\AzureStackAdmin. Se você entrar como um administrador local depois que o computador está ingressado no domínio, você não verá o progresso da implantação. Não execute novamente a implantação, em vez disso, entre como azurestack\AzureStackAdmin validar que está sendo executado.
   > 
   > 
   
    Quando a implantação for bem-sucedido, exibe o console do PowerShell: **concluir: ação 'Implantação'**.
   
Se a implantação falhar, você pode usar o seguinte script PowerShell para executar novamente na mesma janela do PowerShell com privilégios elevados:

```powershell
cd c:\CloudDeployment\Setup
.\InstallAzureStackPOC.ps1 -Rerun
```

Esse script reiniciará a implantação da última etapa com êxito.

Ou, você pode [reimplantar](azure-stack-redeploy.md) do zero.


## <a name="reset-the-password-expiration-to-180-days"></a>Redefinir a expiração de senha para 180 dias

Para certificar-se de que a senha para o host do kit de desenvolvimento não expira muito em breve, siga estas etapas após a implantação:

Para alterar a política de expiração de senha do Powershell:
1. Na janela do Powershell, execute o comando; Conjunto ADDefaultDomainPasswordPolicy - MaxPasswordAge 180.00:00:00-azurestack.local de identidade

Para alterar manualmente a política de expiração de senha:
1. No host do kit de desenvolvimento, abra **Group Policy Management** e navegue até **Group Policy Management** – **floresta: azurestack.local** – **domínios** – **azurestack.local**.
2. Clique com botão direito **Default Domain Policy** e clique em **editar**.
3. No Editor de gerenciamento de diretiva de grupo, navegue até **configuração do computador** – **políticas** – **configurações do Windows** – **as configurações de segurança**– **Políticas de conta** – **política de senha**.
4. No painel direito, clique duas vezes em **duração máxima da senha**.
5. No **duração máxima da senha propriedades** caixa de diálogo, altere o **senha expirará em** valor 180, em seguida, clique em **Okey**.


## <a name="next-steps"></a>Próximas etapas

[Instalar o PowerShell](azure-stack-powershell-configure-quickstart.md)

[Registrar a pilha do Azure com sua assinatura do Azure](azure-stack-register.md)

[Conectar-se ao Azure Stack](azure-stack-connect-azure-stack.md)

