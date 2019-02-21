---
title: 'Implante serviços de aplicativos: O Azure Stack | Microsoft Docs'
description: Instruções detalhadas para implantar o serviço de aplicativo no Azure Stack
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: jeffgilb
ms.reviewer: anwestg
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 60767c3c61b0d386e4ac9b0a93d16ad161c59949
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56445927"
---
# <a name="add-an-app-service-resource-provider-to-azure-stack"></a>Adicionar um provedor de recursos do serviço de aplicativo para o Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Use as diretrizes neste artigo para implantar o serviço de aplicativo no Azure Stack.

> [!IMPORTANT]  
> Aplicar a atualização 1809 seu sistema integrado do Azure Stack ou implantar o mais recente do Azure Stack desenvolvimento ASDK (Kit) antes de implantar 1.4 de serviço de aplicativo do Azure.

Você pode dar a seus usuários a capacidade de criar aplicativos web e API. Para permitir que os usuários criem esses aplicativos, você precisa:

 - Adicione a [provedor de recursos do serviço de aplicativo](azure-stack-app-service-overview.md) à sua implantação do Azure Stack usando as etapas descritas neste artigo.
 - Depois de instalar o provedor de recursos do serviço de aplicativo, você pode incluí-lo em seus planos e ofertas. Os usuários podem inscrever-se, em seguida, para obter o serviço e iniciar a criação de aplicativos.

> [!IMPORTANT]  
> Antes de executar o instalador do provedor de recursos, certifique-se de que você seguiu as orientações [antes de você começar a usar](azure-stack-app-service-before-you-get-started.md).

## <a name="run-the-app-service-resource-provider-installer"></a>Execute o instalador de provedor de recursos de serviço de aplicativo

Instalar o provedor de recursos do serviço de aplicativo demora pelo menos uma hora. O período de tempo necessário depende da função quantas instâncias você implantar. Durante a implantação, o instalador executa as seguintes tarefas:

 - Crie um contêiner de blob na conta de armazenamento do Azure Stack especificada.
 - Crie uma zona DNS e entradas para o serviço de aplicativo.
 - Registre o provedor de recursos do serviço de aplicativo.
 - Registre os itens de galeria do serviço de aplicativo.

 > [!IMPORTANT]
 > Antes de implantar o provedor de recursos, revise as notas de versão para saber mais sobre a nova funcionalidade, correções e problemas conhecidos que podem afetar sua implantação.

Para implantar o provedor de recursos do serviço de aplicativo, siga estas etapas:

1. Execute appservice.exe como um administrador de um computador que possa acessar o Azure Stack Azure recurso Gerenciamento de ponto de extremidade administrador.

2. Selecione **implantar o serviço de aplicativo ou atualização para a versão mais recente**.

    ![Instalador do serviço de aplicativo][1]

3. Examine e aceite os termos de licença de Software da Microsoft e, em seguida, selecione **próxima**.

4. Examine e aceite os termos de licença de terceiros e, em seguida, selecione **próxima**.

5. Certifique-se de que as informações de configuração de nuvem do serviço de aplicativo estão corretas. Se você usou as configurações padrão durante a implantação do Kit de desenvolvimento na pilha do Azure (ASDK), você pode aceitar os valores padrão. Mas, se você personalizou as opções quando você implantou o ASDK, ou estiver implantando em um sistema integrado do Azure Stack, você deve editar os valores nesta janela para refletir as diferenças.

   Por exemplo, se você usar o mycloud.com de sufixo de domínio, o ponto de extremidade do Gerenciador de recursos do Azure Stack locatário do Azure deve alterar para gerenciamento. &lt;região&gt;. mycloud.com. Examine essas configurações e, em seguida, selecione **próxima** para salvar as configurações.

   ![Instalador do serviço de aplicativo][2]

6. Na próxima página do instalador do serviço de aplicativo, siga estas etapas:

     a. Selecione **Connect** ao lado de **assinaturas do Azure Stack**.

     - Se você estiver usando o Azure Active Directory (Azure AD), insira a conta de administrador do Azure AD e a senha que você forneceu quando você implantou o Azure Stack. Selecione **entrar**.
     - Se você estiver usando os serviços de Federação do Active Directory (AD FS), forneça sua conta de administrador. Por exemplo, cloudadmin@azurestack.local. Insira sua senha e, em seguida, selecione **Sign In**.

   b. Na **assinaturas do Azure Stack**, selecione o **assinatura do provedor padrão**.

     > [!IMPORTANT]  
     > O serviço de aplicativo **devem** ser implantado para o **assinatura do provedor padrão**.

   c. No **locais da pilha do Azure**, selecione o local que corresponde à região em que você está implantando. Por exemplo, selecione **local** se sua implantação para o Kit de desenvolvimento do Azure Stack.

    ![Instalador do serviço de aplicativo][3]

7. Agora você pode implantar em uma rede virtual existente que você configurou [usando as seguintes etapas](azure-stack-app-service-before-you-get-started.md#virtual-network), ou deixar que o instalador do serviço de aplicativo criar uma nova rede virtual e sub-redes. Para criar uma rede virtual, siga estas etapas:

    a. Selecione **criar VNet com as configurações padrão**, aceite os padrões e, em seguida, selecione **próxima**.

   b. Como alternativa, selecione **usar a rede virtual existente e sub-redes**. Conclua as seguintes ações:

     - Selecione o **grupo de recursos** que contém sua rede Virtual.
     - Escolha o **rede Virtual** nome que você deseja implantar.
     - Selecione a correta **sub-rede** valores para cada uma das sub-redes de função necessários.
     - Selecione **Avançar**.

   ![Instalador do serviço de aplicativo][4]

8. Insira as informações para o compartilhamento de arquivos e, em seguida, selecione **próxima**. O endereço do compartilhamento de arquivos deve usar o nome de domínio totalmente qualificado (FQDN) ou o endereço IP do servidor de arquivos. Por exemplo, \\\appservicefileserver.local.cloudapp.azurestack.external\websites, ou \\\10.0.0.1\websites.

   >[!NOTE]
   >O instalador tentará testar a conectividade com o compartilhamento de arquivos antes de continuar. Mas, se você estiver implantando em uma rede virtual existente, esse teste de conectividade pode falhar. Você recebe um aviso e um prompt para continuar. Se as informações de compartilhamento de arquivo estão corretas, continue a implantação.

   ![Instalador do serviço de aplicativo][7]

9. Na próxima página do instalador do serviço de aplicativo, siga estas etapas:

    a. No **ID do aplicativo de identidade** , digite o GUID para o aplicativo que você está usando para a identidade (do Azure AD).

   b. No **arquivo de certificado de identidade de aplicativo** caixa, digite (ou procure) o local do arquivo de certificado.

   c. No **senha do certificado de identidade de aplicativo** , digite a senha do certificado. Essa senha é aquela que você anotou quando você usou o script para criar os certificados.

   d. No **arquivo de certificado raiz do Azure Resource Manager** caixa, digite (ou procure) o local do arquivo de certificado.

   e. Selecione **Avançar**.

   ![Instalador do serviço de aplicativo][9]

10. Para cada uma das caixas de arquivo de certificado de três, selecione **procurar** e navegue até o arquivo de certificado apropriado. Você deve fornecer a senha para cada certificado. Esses certificados são aqueles que você criou na [etapa de certificados necessários crie](azure-stack-app-service-before-you-get-started.md#get-certificates). Selecione **próxima** depois de inserir todas as informações.

    | Box | Exemplo de nome de arquivo de certificado |
    | --- | --- |
    | **Arquivo de certificado padrão SSL do serviço de aplicativo** | \_.appservice.local.AzureStack.external.pfx |
    | **Arquivo de certificado SSL de API do serviço de aplicativo** | api.appservice.local.AzureStack.external.pfx |
    | **Arquivo de certificado SSL do fornecedor de serviço de aplicativo** | ftp.appservice.local.AzureStack.external.pfx |

    Se você usou um sufixo de domínio diferente quando você criou os certificados, seus nomes de arquivo de certificado não usam *local. AzureStack.external*. Em vez disso, use suas informações de domínio personalizado.

    ![Instalador do serviço de aplicativo][10]

11. Insira os detalhes do SQL Server para a instância do servidor usada para hospedar os bancos de dados do provedor de recursos do serviço de aplicativo e, em seguida, selecione **próxima**. O instalador valida as propriedades de conexão SQL.

    > [!NOTE]
    > O instalador tentará testar a conectividade com o SQL Server antes de continuar. Mas, se você estiver implantando em uma rede virtual existente, esse teste de conectividade pode falhar. Você recebe um aviso e um prompt para continuar. Se as informações do SQL Server estão corretas, continue a implantação.
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

    ![Instalador do serviço de aplicativo][11]

12. Examine as opções de SKU e a instância de função. Preencher os padrões com o número mínimo de instâncias e o SKU mínimo para cada função em uma implantação ASDK. Um resumo dos requisitos de memória e vCPU é fornecido para ajudar a planejar sua implantação. Depois de fazer suas seleções, selecione **próxima**.

    >[!NOTE]
    >Para implantações de produção, seguindo as orientações [planejamento de capacidade de funções de servidor do serviço de aplicativo do Azure no Azure Stack](azure-stack-app-service-capacity-planning.md).

    | Função | Instâncias mínimas | SKU mínimo | Observações |
    | --- | --- | --- | --- |
    | Controller | 1 | Standard_A2 - (2 Vcpus, 3584 MB) | Gerencia e mantém a integridade da nuvem do serviço de aplicativo. |
    | Gerenciamento | 1 | Standard_A2 - (2 vCPUs, 3584 MB) | Gerencia os pontos de extremidade de serviço de aplicativo do Azure Resource Manager e a API, extensões portal (administrador, locatário, o portal de funções) e o serviço de dados. Para dar suporte a failover, o aumento das instâncias recomendadas para 2. |
    | Publicador | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Publica o conteúdo por meio de implantação da web e FTP. |
    | FrontEnd | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Encaminha solicitações para aplicativos de serviço de aplicativo. |
    | Trabalho compartilhada | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Hosts ou aplicativos de API do aplicativos web e funções do Azure. Talvez você queira adicionar mais instâncias. Como um operador, você pode definir sua oferta e escolha qualquer camada SKU. As camadas devem ter um mínimo de uma vCPU. |

    ![Instalador do serviço de aplicativo][13]

    >[!NOTE]
    >**Windows Server 2016 Core não é uma imagem de plataforma com suporte para uso com o serviço de aplicativo do Azure no Azure Stack.  Não use imagens de avaliação para implantações de produção.**

13. No **selecione a imagem de plataforma** , escolha a imagem de máquina virtual do Windows Server 2016 de implantação de imagens disponíveis no provedor de recursos de computação para a nuvem de serviço de aplicativo. Selecione **Avançar**.

14. Na próxima página do instalador do serviço de aplicativo, siga estas etapas:

      a. Insira o nome de usuário do administrador de máquina virtual de função de trabalho e a senha.

     b. Insira o nome de usuário do administrador de máquina virtual de outras funções e a senha.

     c. Selecione **Avançar**.

    ![Instalador do serviço de aplicativo][15]

15. Na página de resumo do instalador do serviço de aplicativo, siga estas etapas:

     a. Verifique se as seleções feitas por você. Para fazer alterações, use o **anterior** botões para visitar a páginas anteriores.

    b. Se as configurações estão corretas, selecione a caixa de seleção.

    c. Para iniciar a implantação, selecione **próxima**.

    ![Instalador do serviço de aplicativo][16]

16. Na próxima página do instalador do serviço de aplicativo, siga estas etapas:

     a. Acompanhe o progresso da instalação. Serviço de aplicativo no Azure Stack leva cerca de 60 minutos para implantar com base nas seleções padrão.

    b. Depois que o instalador foi concluída com êxito, selecione **Exit**.

    ![Instalador do serviço de aplicativo][17]

## <a name="validate-the-app-service-on-azure-stack-installation"></a>Validar o serviço de aplicativo na instalação do Azure Stack

1. No portal de administração do Azure Stack, acesse **administração - serviço de aplicativo**.

2. Em geral, em status, verificar se o **Status** exibe **todas as funções estão prontas**.

    ![Gerenciamento de serviço de aplicativo](media/azure-stack-app-service-deploy/image12.png)

    Se você estiver implantando em uma rede virtual existente e usando um endereço IP interno para se conectar ao seu servidor de arquivos, você deve adicionar uma regra de segurança de saída. Essa regra permite o tráfego entre a sub-rede de trabalho e o servidor de arquivos SMB.  Para fazer isso, vá para o WorkersNsg no Portal de administração e adicionar uma regra de segurança de saída com as seguintes propriedades:

    - Origem: Qualquer
    - Intervalo de porta de origem: *
    - Destino: Endereços IP
    - Intervalo de endereços IP de destino: Intervalo de IPs para seu servidor de arquivos
    - Intervalo de porta de destino: 445
    - Protocolo: TCP
    - Ação: PERMITIR
    - Prioridade: 700
    - Nome: Outbound_Allow_SMB445

## <a name="test-drive-app-service-on-azure-stack"></a>Test drive do serviço de aplicativo no Azure Stack

Depois de implantar e registrar o provedor de recursos do serviço de aplicativo, testá-lo para certificar-se de que os usuários podem implantar aplicativos de API e da web.

>[!NOTE]
>Você precisará criar uma oferta que tenha o namespace Microsoft. Web no plano. Você também precisa de uma assinatura de locatário que assina a oferta. Para obter mais informações, consulte [criar oferta](azure-stack-create-offer.md) e [criar plano](azure-stack-create-plan.md).
>
>Você *deve* possui uma assinatura de locatário para criar aplicativos que usam o serviço de aplicativo no Azure Stack. As únicas tarefas que um administrador de serviço pode concluir no portal de administração estão relacionadas para a administração de provedor de recursos do serviço de aplicativo. Isso inclui a adição de capacidade, configurando as fontes de implantação e adicionar SKUs e camadas de trabalhador.
>
>Para criar a web, API e o Azure Functions aplicativos, você deve usar o portal de locatário e ter uma assinatura de locatário.
>

Para criar um aplicativo web de teste, siga estas etapas:

1. No portal do usuário do Azure Stack, selecione **+ criar um recurso** > **Web + móvel** > **Web App**.

2. Sob **aplicativo Web**, insira um nome na **aplicativo Web**.

3. Sob **grupo de recursos**, selecione **New**. Insira um nome para o **grupo de recursos**.

4. Selecione **plano do serviço de aplicativo/localização** > **criar novo**.

5. Sob **plano do serviço de aplicativo**, insira um nome para o **plano de serviço de aplicativo**.

6. Selecione **tipo de preço** > **compartilhado gratuito** ou **compartilhado compartilhado** > **selecione**  >  **Okey** > **criar**.

7. Um bloco para o novo aplicativo web é exibida no painel. Selecione o bloco.

8. Na **aplicativo Web**, selecione **procurar** para exibir o site padrão para este aplicativo.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Implantar um site WordPress, DNN ou Django (opcional)

1. No portal de locatário do Azure Stack, selecione **+**, vá para o Azure Marketplace, implante um site Django e, em seguida, aguarde a conclusão da implantação. A plataforma de web do Django usa um banco de dados com base no sistema do arquivo. Ele não requer quaisquer provedores de recursos adicionais, como SQL ou MySQL.

2. Se você também implantou um provedor de recursos do MySQL, você pode implantar um site do WordPress no Marketplace. Quando você for solicitado para parâmetros de banco de dados, insira o nome de usuário *User1@Server1*com o nome de usuário e o nome do servidor de sua escolha.

3. Se você também implantou um provedor de recursos do SQL Server, você pode implantar um site do DNN do Marketplace. Quando você for solicitado para parâmetros de banco de dados, escolha um banco de dados no computador executando o SQL Server que está conectado ao seu provedor de recursos.

## <a name="next-steps"></a>Próximas etapas

Você também pode experimentar outras [plataforma como um serviço (PaaS) serviços](azure-stack-tools-paas-services.md).

 - [Provedor de recursos do SQL Server](azure-stack-sql-resource-provider-deploy.md)
 - [Provedor de recursos MySQL](azure-stack-mysql-resource-provider-deploy.md)

<!--Links-->
[Azure_Stack_App_Service_preview_installer]: https://go.microsoft.com/fwlink/?LinkID=717531
[App_Service_Deployment]: https://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: https://go.microsoft.com/fwlink/?LinkId=733525

<!--Image references-->
[1]: ./media/azure-stack-app-service-deploy/app-service-installer.png
[2]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-arm-endpoints.png
[3]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-subscription-information.png
[4]: ./media/azure-stack-app-service-deploy/app-service-default-VNET-config.png
[5]: ./media/azure-stack-app-service-deploy/app-service-custom-VNET-config.png
[6]: ./media/azure-stack-app-service-deploy/app-service-custom-VNET-config-with-values.png
[7]: ./media/azure-stack-app-service-deploy/app-service-fileshare-configuration.png
[8]: ./media/azure-stack-app-service-deploy/app-service-fileshare-configuration-error.png
[9]: ./media/azure-stack-app-service-deploy/app-service-identity-app.png
[10]: ./media/azure-stack-app-service-deploy/app-service-certificates.png
[11]: ./media/azure-stack-app-service-deploy/app-service-sql-configuration.png
[12]: ./media/azure-stack-app-service-deploy/app-service-sql-configuration-error.png
[13]: ./media/azure-stack-app-service-deploy/app-service-cloud-quantities.png
[14]: ./media/azure-stack-app-service-deploy/app-service-windows-image-selection.png
[15]: ./media/azure-stack-app-service-deploy/app-service-role-credentials.png
[16]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-deployment-summary.png
[17]: ./media/azure-stack-app-service-deploy/app-service-deployment-progress.png
