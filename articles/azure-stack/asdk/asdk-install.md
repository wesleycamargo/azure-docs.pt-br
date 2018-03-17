---
title: Instalar o Kit de desenvolvimento de pilha do Azure (ASDK) | Microsoft Docs
description: Descreve como instalar o Kit de desenvolvimento de pilha do Azure (ASDK).
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: e1f4cfb74d83cb23631e5a16a6e6f2dba98027ef
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/17/2018
---
# <a name="install-the-azure-stack-development-kit-asdk"></a>Instalar o Kit de desenvolvimento de pilha do Azure (ASDK)
Depois de [preparar o computador de host ASDK](asdk-prepare-host.md), o ASDK pode ser implantado em imagem CloudBuilder.vhdx usando as etapas a seguir neste artigo.

## <a name="install-the-asdk"></a>Instalar o ASDK
As etapas neste artigo mostram como implantar o ASDK usando uma interface gráfica do usuário (GUI) fornecida baixando e executando o **asdk installer.ps1** script do PowerShell.

> [!NOTE]
> A interface do usuário do instalador para o Kit de desenvolvimento de pilha do Azure é um script de código aberto baseado no WCF e do PowerShell.


1. Depois que o computador host é inicializado com êxito na imagem de CloudBuilder.vhdx, efetue logon usando as credenciais de administrador especificado quando você [preparado o computador de host do kit de desenvolvimento](asdk-prepare-host.md) para instalação ASDK. Isso deve ser o mesmo que as credenciais de administrador de host de kit de desenvolvimento.
2. Abra um console do PowerShell com privilégios elevados e execute o  **&lt;letra da unidade > \AzureStack_Installer\asdk-installer.ps1** script (que agora pode estar em uma unidade diferente C:\ na imagem CloudBuilder.vhdx). Clique em **Instalar**.

    ![](media/asdk-install/1.PNG) 

3. No provedor de identidade **tipo** caixa de lista suspensa, selecione **nuvem do Azure** ou **do AD FS**. Em **senha de Administrador Local** digite a senha de administrador local (que deve corresponder à senha de administrador local configurado atual) a **senha** caixa e, em seguida, clique em  **Próxima**.
    - **Nuvem do Azure**: configura o Azure Active Directory (AD do Azure) como o provedor de identidade. Para usar essa opção, você precisa de uma conexão de internet, o nome completo do AD do Azure locatário de diretório na forma de *domainname*. c o m ou do AD do Azure verificar nome de domínio personalizado e as credenciais de administrador global especificado diretório. 
    - **O AD FS**: O serviço de diretório do carimbo de padrão é usado como o provedor de identidade. É a conta padrão para entrar com azurestackadmin@azurestack.local, e a senha a ser usada é fornecida como parte da instalação.

    ![](media/asdk-install/2.PNG) 
    
    > [!NOTE]
    > Para obter melhores resultados, mesmo se você quiser usar um ambiente desconectado de pilha do Azure usando o AD FS como o provedor de identidade, é aconselhável instalar ASDK enquanto estiver conectado à internet. Dessa forma, a versão de avaliação do Windows Server 2016 incluída com a instalação do kit de desenvolvimento pode ser ativada no momento da implantação.
4. Selecione um adaptador de rede a ser usado para o kit de desenvolvimento e, em seguida, clique em **próximo**.

    ![](media/asdk-install/3.PNG)

5. Selecione o DHCP ou configuração de rede estático para a máquina virtual BGPNAT01.
    > [!TIP]
    > A VM BGPNAT01 é o roteador de borda que fornece recursos NAT e VPN para a pilha do Azure.

    - **DHCP** (padrão): A máquina virtual obtém a configuração de rede IP do servidor DHCP.
    - **Estático**: use essa opção somente se o DHCP não é possível atribuir um endereço IP válido para a pilha do Azure acessar a Internet. **Um endereço IP estático deve ser especificado com o comprimento da máscara de sub-rede no formato CIDR (por exemplo, 10.0.0.5/24)**.
    - Tipo em uma opção válida **IP do servidor de tempo** endereço. Isso necessário campo define o servidor de horário a ser usado pelo kit de desenvolvimento. Esse parâmetro deve ser fornecido como um endereço IP do servidor de tempo válido. Não há suporte para nomes de servidor.
      > [!TIP]
      > Para localizar um servidor de horário de endereço IP, visite [pool.ntp.org](http:\\pool.ntp.org) ou executar ping time.windows.com. 
    - **Opcionalmente,**, defina os seguintes valores:
        - **ID de VLAN**: define a ID de VLAN. Só use essa opção se o host e AzS BGPNAT01 devem configurar o ID de VLAN para acessar a rede física (e internet). 
        - **O encaminhador de DNS**: um servidor DNS é criado como parte da implantação da pilha do Azure. Para permitir que os computadores dentro da solução para resolver nomes fora o carimbo, forneça o servidor DNS de infraestrutura existente. O servidor DNS no carimbo encaminha solicitações de resolução de nome desconhecido para o servidor.

    ![](media/asdk-install/4.PNG)

6. Sobre o **verificando as propriedades de placa de interface de rede** página, você verá uma barra de progresso. Quando a verificação for concluída, clique em **próximo**.

    ![](media/asdk-install/5.PNG)

9. Em **resumo** , clique em **implantar** para iniciar a instalação de ASDK no computador de host do kit de desenvolvimento.

    ![](media/asdk-install/6.PNG)

    > [!TIP]
    > Aqui você também pode copiar os comandos de instalação do PowerShell que serão usados para instalar o kit de desenvolvimento. Isso é útil se você precisar [reimplantar ASDK no computador host usando o PowerShell](asdk-deploy-powershell.md).

10. Se você estiver executando uma implantação do AD do Azure, você será solicitado a inserir suas credenciais de conta de administrador global do AD do Azure alguns minutos após o início da instalação.

    ![](media/asdk-install/7.PNG)

11. O processo de implantação levará algumas horas, durante o qual o computador host será reiniciado automaticamente uma vez. Se você quiser monitorar o progresso da implantação, entre como azurestack\AzureStackAdmin depois que o host do kit de desenvolvimento é reiniciado. Quando a implantação for bem-sucedido, exibe o console do PowerShell: **concluir: ação 'Implantação'**. 
    > [!IMPORTANT]
    > Se você entrar como um administrador local depois que o computador está ingressado no domínio, você não verá o progresso da implantação. Não execute novamente a implantação, em vez disso, entre como azurestack\AzureStackAdmin validar que está sendo executado.

    ![](media/asdk-install/8.PNG)

Parabéns, você instalou com êxito o ASDK!

Se a implantação falhar por algum motivo, você pode [reimplantar](asdk-redeploy.md) de zero ou use os seguinte comandos do PowerShell, na mesma janela do PowerShell com privilégios elevados, reiniciar a implantação da última etapa bem-sucedida:

    ```powershell
    cd C:\CloudDeployment\Setup
    .\InstallAzureStackPOC.ps1 -Rerun
    ```

## <a name="next-steps"></a>Próximas etapas
[Configuração de implantação de postagem](asdk-post-deploy.md)