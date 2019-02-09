---
title: Instalar o Kit de desenvolvimento do Azure Stack (ASDK) | Microsoft Docs
description: Descreve como instalar o Azure Stack desenvolvimento ASDK (Kit).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: a58f5a3794d352fa8671321f5a30d74d2598df75
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2019
ms.locfileid: "55977721"
---
# <a name="install-the-azure-stack-development-kit-asdk"></a>Instalar o Kit de desenvolvimento do Azure Stack ASDK)
Após [preparar o computador de host ASDK](asdk-prepare-host.md), o ASDK pode ser implantado na imagem CloudBuilder.vhdx usando as etapas a seguir neste artigo.

## <a name="install-the-asdk"></a>Instalar o ASDK
As etapas neste artigo mostram como implantar o ASDK usando uma interface gráfica do usuário (GUI) fornecida pelo baixando e executando o **asdk installer.ps1** script do PowerShell.

> [!NOTE]
> A interface do usuário do instalador para o Kit de desenvolvimento do Azure Stack é um script de software livre baseado no WCF e do PowerShell.


1. Depois que o computador host é inicializada com êxito para a imagem CloudBuilder.vhdx, efetue logon usando as credenciais de administrador especificado quando você [preparado o computador de host do kit de desenvolvimento](asdk-prepare-host.md) para instalação ASDK. Isso deve ser o mesmo que as credenciais de administrador local de host de kit de desenvolvimento.
2. Abra um console do PowerShell com privilégios elevados e execute o  **&lt;letra da unidade > \AzureStack_Installer\asdk-installer.ps1** script do PowerShell. Observe que o script agora pode estar em uma unidade C:\ na imagem CloudBuilder.vhdx diferente. Clique em **Instalar**.

    ![](media/asdk-install/1.PNG) 

3. No provedor de identidade **tipo** caixa de lista suspensa, selecione **Azure China Cloud**, **Azure US Government Cloud**, **do AD FS**, ou **Nuvem do azure**. Sob **senha de Administrador Local** digite a senha de administrador local (que deve corresponder à senha de administrador local configurada a atual) a **senha** caixa e, em seguida, clique em  **Próxima**.

    ![](media/asdk-install/2.PNG) 
  
   Se você escolher um provedor de identidade de assinatura do Azure, você precisa de uma conexão de internet, o nome completo do AD do Azure na forma de locatário do diretório *domainname*. onmicrosoft.com ou um Azure AD verificada global e nome de domínio personalizado credenciais de administrador para o diretório especificado.<br><br>Após a implantação, a permissão de administrador global do Azure Active Directory não é necessária. No entanto, algumas operações podem exigir a credencial de administrador global. Por exemplo, um script de instalador do provedor de recursos ou um novo recurso que exigem uma permissão para ser concedida. Você temporariamente pode designar novamente as permissões da conta administrador global ou usar uma conta de administrador global separado que é proprietário do *padrão de assinatura do provedor*.<br><br>Ao usar o AD FS como o provedor de identidade, o serviço de diretório do carimbo de data / padrão é usado. A conta padrão para entrar com é azurestackadmin@azurestack.local, e a senha para usar é fornecido como parte da instalação.

  > [!NOTE]
  > Para obter melhores resultados, mesmo se você quiser usar um ambiente desconectado do Azure Stack usando o AD FS como o provedor de identidade, é melhor instalar o ASDK enquanto estiver conectado à internet. Dessa forma, a versão de avaliação do Windows Server 2016 incluída com a instalação do kit de desenvolvimento pode ser ativada no momento da implantação.

4. Selecione um adaptador de rede a ser usado para o kit de desenvolvimento e, em seguida, clique em **próxima**.

    ![](media/asdk-install/3.PNG)

5. Sobre o **configuração de rede** , forneça um válido **IP do servidor de tempo** endereço. Isso exigia que o campo define o servidor de horário a ser usado pelo kit de desenvolvimento. Esse parâmetro deve ser fornecido como um endereço IP do servidor de tempo válido. Não há suporte para nomes de servidor.

      > [!TIP]
      > Para localizar um servidor de horário de endereço IP, visite [ntppool.org](https://www.ntppool.org/) ou executar ping time.windows.com. 

    **Opcionalmente**, você pode fornecer uma **encaminhador DNS** endereço IP. Um servidor DNS é criado como parte da implantação do Azure Stack. Para permitir que computadores dentro da solução para resolver nomes fora o carimbo, forneça seu servidor DNS de infra-estrutura existente. O servidor DNS no carimbo de data / encaminha solicitações de resolução de nome desconhecido para este servidor.

    ![](media/asdk-install/4.PNG)

6. Sobre o **verificando as propriedades de placa de interface de rede** página, você verá uma barra de progresso. Quando a verificação for concluída, clique em **próxima**.

    ![](media/asdk-install/5.PNG)

7. Na **resumo** , clique em **implantar** para iniciar a instalação de ASDK no computador de host do kit de desenvolvimento.

    ![](media/asdk-install/6.PNG)

    > [!TIP]
    > Aqui você também pode copiar os comandos de instalação do PowerShell que serão usados para instalar o kit de desenvolvimento. Isso é útil se você precisar [reimplantar o ASDK no computador host usando o PowerShell](asdk-deploy-powershell.md).

8. Se você estiver executando uma implantação do AD do Azure, você será solicitado a inserir suas credenciais de conta de administrador global do Azure AD alguns minutos após a instalação é iniciado.

9. O processo de implantação levará algumas horas, período durante o qual o computador host reiniciará automaticamente uma vez. Se você quiser monitorar o progresso da implantação, entre como azurestack\AzureStackAdmin depois que o host do kit de desenvolvimento é reiniciado. Quando a implantação bem-sucedida, exibe o console do PowerShell: **CONCLUA: A ação 'Implantação'**. 
    > [!IMPORTANT]
    > Se você entrar como um administrador local depois que o computador está associado ao domínio azurestack, você não verá o progresso da implantação. Não execute novamente a implantação, em vez disso, entre como azurestack\AzureStackAdmin para validar que ele está em execução.

    ![](media/asdk-install/7.PNG)

Parabéns, você instalou com êxito o ASDK!

Se a implantação falhar por algum motivo, você poderá [reimplantar](asdk-redeploy.md) do zero ou use os seguinte comandos do PowerShell, da mesma janela do PowerShell com privilégios elevados, para reiniciar a implantação da última etapa bem-sucedida:

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

## <a name="next-steps"></a>Próximas etapas
[Configuração pós-implantação](asdk-post-deploy.md)
