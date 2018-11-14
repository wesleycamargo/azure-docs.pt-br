---
title: Implantar o serviço de aplicativo em um ambiente offline no Azure Stack | Microsoft Docs
description: Orientações detalhadas sobre como implantar o serviço de aplicativo em um ambiente desconectado do Azure Stack protegido pelo AD FS.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: anwestg
ms.openlocfilehash: 54ceadc8173526e8638e70c8c859109eea157fa7
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51614031"
---
# <a name="add-an-app-service-resource-provider-to-a-disconnected-azure-stack-environment-secured-by-ad-fs"></a>Adicionar um provedor de recursos do serviço de aplicativo para um ambiente desconectado do Azure Stack protegido pelo AD FS

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

> [!IMPORTANT]
> Aplicar a atualização 1809 seu sistema integrado do Azure Stack ou implantar o kit de desenvolvimento mais recente do Azure Stack antes de implantar 1.4 de serviço de aplicativo do Azure.
>
>

Seguindo as instruções neste artigo, você pode instalar o [provedor de recursos do serviço de aplicativo](azure-stack-app-service-overview.md) em um ambiente do Azure Stack que é:

- não conectado à Internet
- protegido pelos serviços de Federação do Active Directory (AD FS).

Para adicionar o provedor de recursos do serviço de aplicativo para sua implantação offline do Azure Stack, você deve concluir essas tarefas de nível superior:

1. Conclua o [etapas de pré-requisito](azure-stack-app-service-before-you-get-started.md) (como comprar certificados, que pode levar alguns dias para receber).
2. [Baixe e extraia os arquivos de instalação e auxiliar](azure-stack-app-service-before-you-get-started.md) para um computador conectado à Internet.
3. Crie um pacote de instalação offline.
4. Execute o arquivo de instalador appservice.exe.

## <a name="create-an-offline-installation-package"></a>Criar um pacote de instalação offline

Para implantar o serviço de aplicativo em um ambiente desconectado, você deve primeiro criar um pacote de instalação offline em um computador que está conectado à Internet.

1. Execute o instalador de AppService.exe em um computador que está conectado à Internet.

2. Clique em **Advanced** > **criar pacote de instalação offline**.

    ![Instalador do serviço de aplicativo][1]

3. O instalador do serviço de aplicativo cria um pacote de instalação offline e exibe o caminho para ele. Você pode clicar em **Abrir pasta** para abrir a pasta no Gerenciador de arquivos.

    ![Instalador do serviço de aplicativo](media/azure-stack-app-service-deploy-offline/image02.png)

4. Copie o instalador (AppService.exe) e o pacote de instalação offline para sua máquina de host do Azure Stack.

## <a name="complete-the-offline-installation-of-app-service-on-azure-stack"></a>Concluir a instalação offline do serviço de aplicativo no Azure Stack

1. Execute appservice.exe como um administrador de um computador que pode alcançar o ponto de extremidade de gerenciamento de recursos do Azure Stack Admin do Azure.

2. Clique em **Advanced** > **concluir a instalação offline**.

    ![Instalador do serviço de aplicativo][2]

3. Navegue até o local do pacote de instalação offline que você criou anteriormente e, em seguida, clique em **próxima**.

    ![Instalador do serviço de aplicativo](media/azure-stack-app-service-deploy-offline/image04.png)

4. Examine e aceite os termos de licença de Software da Microsoft e, em seguida, clique em **próxima**.

5. Examine e aceite os termos de licença de terceiros e, em seguida, clique em **próxima**.

6. Certifique-se de que as informações de configuração de nuvem do serviço de aplicativo estão corretas. Se você usou as configurações padrão durante a implantação do Kit de desenvolvimento do Azure Stack, você pode aceitar os valores padrão aqui. No entanto, se você personalizou as opções quando você implantou o Azure Stack ou estiver implantando em um sistema integrado, você deve editar os valores nesta janela para refletir isso. Por exemplo, se você usar o mycloud.com de sufixo de domínio, o ponto de extremidade do Gerenciador de recursos do Azure Stack locatário do Azure deve alterar para gerenciamento. <region>. mycloud.com. Depois de confirmar suas informações, clique em **próxima**.

    ![Instalador do serviço de aplicativo][3]

7. Na próxima página:
    1. Clique o **Connect** lado a **assinaturas do Azure Stack** caixa.
        - Fornece sua conta de administrador. Por exemplo, cloudadmin@azurestack.local. Insira sua senha e clique em **Sign In**.
    2. No **assinaturas do Azure Stack** caixa, selecione a **assinatura do provedor padrão**.
    
    > [!NOTE]
    > O serviço de aplicativo só pode ser implantado na **assinatura do provedor padrão**.
    >
    
    3. No **locais da pilha do Azure** , selecione o local que corresponde à região em que você está implantando. Por exemplo, selecione **local** se sua implantação para o Kit de desenvolvimento do Azure Stack.
    4. Clique em **Próximo**.

    ![Instalador do serviço de aplicativo][4]

8. Agora você tem a opção de implantar em uma rede Virtual existente, conforme configurado pelas etapas [aqui](azure-stack-app-service-before-you-get-started.md#virtual-network), ou permitir que o instalador do serviço de aplicativo criar uma rede Virtual e sub-redes associadas.
    1. Selecione **criar VNet com as configurações padrão**, aceite os padrões e, em seguida, clique em **próxima**, ou;
    2. Selecione **usar a rede virtual existente e sub-redes**.
        1. Selecione o **grupo de recursos** que contém sua rede Virtual.
        2. Escolher o correto **rede Virtual** nome você deseja implantar em;
        3. Selecione a correta **sub-rede** valores para cada uma das sub-redes função necessários;
        4. Clique em **Avançar**

    ![Instalador do serviço de aplicativo][5]

9. Insira as informações para o compartilhamento de arquivos e, em seguida, clique em **próxima**. O endereço do compartilhamento de arquivos deve usar o nome de domínio totalmente qualificado ou endereço IP do servidor de arquivos. Por exemplo, \\\appservicefileserver.local.cloudapp.azurestack.external\websites, ou \\\10.0.0.1\websites

    > [!NOTE]
    > O instalador tenta testar a conectividade com o compartilhamento de arquivos antes de continuar.  No entanto, se você optar por implantar em uma rede Virtual existente, o instalador pode não conseguir se conectar ao compartilhamento de arquivos e exibe um aviso, perguntando se deseja continuar.  Verifique as informações de compartilhamento de arquivos e continuar se elas estão corretas.
    >
    >

   ![Instalador do serviço de aplicativo][8]

10. Na próxima página:
    1. No **ID do aplicativo de identidade** , digite o GUID para o aplicativo que você está usando para a identidade (do Azure AD).
    2. No **arquivo de certificado de identidade de aplicativo** caixa, digite (ou procure) o local do arquivo de certificado.
    3. No **senha do certificado de identidade de aplicativo** , digite a senha do certificado. Essa senha é aquela que você anotou quando você usou o script para criar os certificados.
    4. No **arquivo de certificado raiz do Azure Resource Manager** caixa, digite (ou procure) o local do arquivo de certificado.
    5. Clique em **Próximo**.

    ![Instalador do serviço de aplicativo][10]

11. Para cada um dos três caixas de arquivo de certificado, clique em **procurar** e, em seguida, navegue até o arquivo de certificado apropriado. Você deve fornecer a senha para cada certificado. Esses certificados são aqueles que você criou na [etapa de certificados necessários crie](azure-stack-app-service-before-you-get-started.md#get-certificates). Clique em **próxima** depois de inserir todas as informações.

    | Box | Exemplo de nome de arquivo de certificado |
    | --- | --- |
    | **Arquivo de certificado padrão SSL do serviço de aplicativo** | \_.appservice.local.AzureStack.external.pfx |
    | **Arquivo de certificado SSL de API do serviço de aplicativo** | api.appservice.local.AzureStack.external.pfx |
    | **Arquivo de certificado SSL do fornecedor de serviço de aplicativo** | ftp.appservice.local.AzureStack.external.pfx |

    Se você usou um sufixo de domínio diferente quando você criou os certificados, seus nomes de arquivo de certificado não usam *local. AzureStack.external*. Em vez disso, use suas informações de domínio personalizado.

    ![Instalador do serviço de aplicativo][11]

12. Insira os detalhes do SQL Server para a instância do servidor usada para hospedar os bancos de dados do provedor de recursos do serviço de aplicativo e, em seguida, clique em **próxima**. O instalador valida as propriedades de conexão SQL. Você **deve** insira o endereço ip interno ou o nome de domínio totalmente qualificado para o nome do SQL Server.

    > [!NOTE]
    > O instalador tenta testar a conectividade com o SQl Server antes de continuar.  No entanto, se você optar por implantar em uma rede Virtual existente, o instalador não pode ser capaz de se conectar ao SQL Server e exibe um aviso perguntando se você deseja continuar.  Verifique as informações do SQL Server e continuar se elas estão corretas.
    >
    > Do serviço de aplicativo do Azure no Azure Stack 1.3 em diante, o instalador verificará se o SQL Server tem a contenção de banco de dados habilitada no nível do SQL Server.  Se não for, você será solicitado com a seguinte exceção:
    > ```sql
    >    Enable contained database authentication for SQL server by running below command on SQL server (Ctrl+C to copy)
    >    ***********************************************************
    >    sp_configure 'contained database authentication', 1;  
    >    GO  
    >    RECONFIGURE;  
    >    GO
    >    ***********************************************************
    > ```
    > Consulte a [notas de versão do serviço de aplicativo do Azure no Azure Stack 1.3](azure-stack-app-service-release-notes-update-three.md) para obter mais detalhes.
   
   ![Instalador do serviço de aplicativo][12]

13. Examine as opções de SKU e a instância de função. Os padrões são preenchidos com o número mínimo de instâncias e o SKU mínimo para cada função em uma implantação ASDK. Um resumo dos requisitos de memória e vCPU é fornecido para ajudar a planejar sua implantação. Depois de fazer suas seleções, clique em **próxima**.

     > [!NOTE]
     > Para implantações de produção, siga as orientações [planejamento de capacidade de funções de servidor do serviço de aplicativo do Azure no Azure Stack](azure-stack-app-service-capacity-planning.md).
     >
     >

    | Função | Instâncias mínimas | SKU mínimo | Observações |
    | --- | --- | --- | --- |
    | Controller | 1 | Standard_A2 - (2 Vcpus, 3584 MB) | Gerencia e mantém a integridade da nuvem do serviço de aplicativo. |
    | Gerenciamento | 1 | Standard_A2 - (2 vCPUs, 3584 MB) | Gerencia os pontos de extremidade de serviço de aplicativo do Azure Resource Manager e a API, extensões portal (administrador, locatário, o portal de funções) e o serviço de dados. Para dar suporte a failover, o aumento das instâncias recomendadas para 2. |
    | Publicador | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Publica o conteúdo por meio de implantação da web e FTP. |
    | FrontEnd | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Encaminha solicitações para aplicativos de serviço de aplicativo. |
    | Trabalho compartilhada | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Hosts ou aplicativos de API do aplicativos web e funções do Azure. Talvez você queira adicionar mais instâncias. Como um operador, você pode definir sua oferta e escolha qualquer camada SKU. As camadas devem ter um mínimo de uma vCPU. |

    ![Instalador do serviço de aplicativo][14]

    > [!NOTE]
    > **Windows Server 2016 Core não é uma imagem de plataforma com suporte para uso com o serviço de aplicativo do Azure no Azure Stack.  Não use imagens de avaliação para implantações de produção.  Serviço de aplicativo do Azure no Azure Stack requer que o Microsoft.NET 3.5.1 SP1 é ativado na imagem usada para a implantação.   Marketplace de imagens não têm esse recurso habilitado do Windows Server 2016 seja distribuído, portanto você deve criar e usar uma imagem do Windows Server 2016 com esse recurso habilitado previamente.**

14. No **selecione a imagem de plataforma** , escolha sua imagem de máquina virtual de implantação do Windows Server 2016 a partir daqueles disponíveis no provedor de recursos de computação para a nuvem de serviço de aplicativo. Clique em **Próximo**.

15. Na próxima página:
     1. Insira o nome de usuário do administrador de máquina virtual de função de trabalho e a senha.
     2. Insira o nome de usuário do administrador de máquina virtual de outras funções e a senha.
     3. Clique em **Próximo**.

    ![Instalador do serviço de aplicativo][16]

16. Na página de resumo:
    1. Verifique se as seleções feitas por você. Para fazer alterações, use o **anterior** botões para visitar a páginas anteriores.
    2. Se as configurações estão corretas, selecione a caixa de seleção.
    3. Para iniciar a implantação, clique em **próxima**.

    ![Instalador do serviço de aplicativo][17]

17. Na próxima página:
    1. Acompanhe o progresso da instalação. Serviço de aplicativo no Azure Stack leva cerca de 60 minutos para implantar com base nas seleções padrão.
    2. Depois que o instalador foi concluída com êxito, clique em **Exit**.

    ![Instalador do serviço de aplicativo][18]

## <a name="validate-the-app-service-on-azure-stack-installation"></a>Validar o serviço de aplicativo na instalação do Azure Stack

1. No portal de administração do Azure Stack, acesse **administração - serviço de aplicativo**.

2. Em geral, em status, verificar se o **Status** exibe **todas as funções estão prontas**.

    ![Gerenciamento de serviço de aplicativo](media/azure-stack-app-service-deploy/image12.png)
    
> [!NOTE]
> Se você optar por implantar em uma rede virtual existente e um endereço IP interno para se conectar ao seu servidor de arquivos, você deve adicionar uma regra de segurança de saída, permitindo o tráfego entre a sub-rede de trabalho e o servidor de arquivos SMB.  Para fazer isso, vá para o WorkersNsg no Portal de administração e adicionar uma regra de segurança de saída com as seguintes propriedades:
> * Fonte: qualquer
> * Intervalo de porta de origem: *
> * Destino: Endereços IP
> * Intervalo de endereços IP de destino: intervalo de IPs para seu servidor de arquivos
> * Intervalo de porta de destino: 445
> * Protocolo: TCP
> * Ação: permitir
> * Prioridade: 700
> * Nome: Outbound_Allow_SMB445
>

## <a name="test-drive-app-service-on-azure-stack"></a>Test drive do serviço de aplicativo no Azure Stack

Depois de implantar e registrar o provedor de recursos do serviço de aplicativo, testá-lo para certificar-se de que os usuários podem implantar aplicativos de API e da web.

> [!NOTE]
> Você precisará criar uma oferta que tenha o namespace Microsoft. Web dentro do plano. Em seguida, você precisa ter uma assinatura de locatário assina essa oferta. Para obter mais informações, consulte [criar oferta](azure-stack-create-offer.md) e [criar plano](azure-stack-create-plan.md).
>
Você *deve* possui uma assinatura de locatário para criar aplicativos que usam o serviço de aplicativo no Azure Stack. Os recursos de somente um administrador de serviço pode ser concluída dentro do portal de administração são relacionados para a administração de provedor de recursos do serviço de aplicativo. Esses recursos incluem a adição de capacidade, configurando as fontes de implantação e adicionar SKUs e camadas de trabalhador.
>
A partir do technical preview do terceiro criar a web, API e o Azure Functions aplicativos, você deve usar o portal de locatário e ter uma assinatura de locatário.

1. No portal de locatário do Azure Stack, clique em **+ criar um recurso** > **Web + móvel** > **Web App**.

2. Sobre o **aplicativo Web** folha, digite um nome na **aplicativo Web** caixa.

3. Sob **grupo de recursos**, clique em **New**. Digite um nome na **grupo de recursos** caixa.

4. Clique em **Plano do Serviço de Aplicativo/Local** > **Criar Novo**.

5. Sobre o **plano do serviço de aplicativo** folha, digite um nome na **plano do serviço de aplicativo** caixa.

6. Clique em **tipo de preço** > **compartilhado gratuito** ou **compartilhado compartilhado** > **selecione**  >   **Okey** > **criar**.

7. Em menos de um minuto, um bloco para o novo aplicativo web é exibida no painel. Clique no bloco.

8. Sobre o **aplicativo Web** folha, clique em **procurar** para exibir o site padrão para este aplicativo.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Implantar um site WordPress, DNN ou Django (opcional)

1. No portal de locatário do Azure Stack, clique em **+**, vá para o Azure Marketplace, implante um site Django e aguardar a conclusão bem-sucedida. A plataforma de web do Django usa um banco de dados com base no sistema do arquivo. Ele não requer quaisquer provedores de recursos adicionais, como SQL ou MySQL.

2. Se você também implantou um provedor de recursos do MySQL, você pode implantar um site do WordPress no Marketplace. Quando você for solicitado para parâmetros de banco de dados, insira o nome de usuário *User1@Server1*com o nome de usuário e o nome do servidor de sua escolha.

3. Se você também implantou um provedor de recursos do SQL Server, você pode implantar um site do DNN do Marketplace. Quando você for solicitado para parâmetros de banco de dados, escolha um banco de dados no computador executando o SQL Server que está conectado ao seu provedor de recursos.

## <a name="next-steps"></a>Próximas etapas

Você também pode experimentar outras [plataforma como um serviço (PaaS) serviços](azure-stack-tools-paas-services.md).

- [Provedor de recursos do SQL Server](azure-stack-sql-resource-provider-deploy.md)
- [Provedor de recursos MySQL](azure-stack-mysql-resource-provider-deploy.md)

<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[App_Service_Deployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525

<!--Image references-->
[1]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-create-package.png
[2]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-complete-offline.png
[3]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-arm-endpoints.png
[4]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-subscription-information.png
[5]: ./media/azure-stack-app-service-deploy-offline/app-service-default-VNET-config.png
[6]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config.png
[7]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config-with-values.png
[8]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration.png
[9]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration-error.png
[10]: ./media/azure-stack-app-service-deploy-offline/app-service-identity-app.png
[11]: ./media/azure-stack-app-service-deploy-offline/app-service-certificates.png
[12]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration.png
[13]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration-error.png
[14]: ./media/azure-stack-app-service-deploy-offline/app-service-cloud-quantities.png
[15]: ./media/azure-stack-app-service-deploy-offline/app-service-windows-image-selection.png
[16]: ./media/azure-stack-app-service-deploy-offline/app-service-role-credentials.png
[17]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-deployment-summary.png
[18]: ./media/azure-stack-app-service-deploy-offline/app-service-deployment-progress.png
