---
title: "Implantar serviços de aplicativos: Pilha do Azure | Microsoft Docs"
description: "Instruções detalhadas para implantar o serviço de aplicativo na pilha do Azure"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: anwestg
ms.openlocfilehash: 2dd5fe36105f4013c36dd4dc952424d5672ba91f
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2017
---
# <a name="add-an-app-service-resource-provider-to-azure-stack"></a>Adicionar um provedor de recursos do serviço de aplicativo a pilha do Azure

Como um operador de nuvem de pilha do Azure, você pode oferecer aos usuários a capacidade de criar aplicativos web e API. Para fazer isso, você deve primeiro adicionar o [provedor de recursos do serviço de aplicativo](azure-stack-app-service-overview.md) para sua implantação de pilha do Azure conforme descrito neste artigo. Depois de instalar o provedor de recursos do serviço de aplicativo, você pode incluí-lo em suas ofertas e planos. Os usuários podem assinar, em seguida, para obter o serviço e iniciar a criação de aplicativos.

> [!IMPORTANT]
> Antes de executar o instalador, certifique-se de seguir as orientações em [antes de começar](azure-stack-app-service-before-you-get-started.md).
> 
>



## <a name="run-the-app-service-resource-provider-installer"></a>Execute o instalador do provedor de recursos do serviço de aplicativo

Instalar o provedor de recursos do serviço de aplicativo em seu ambiente de pilha do Azure pode demorar até uma hora. Durante esse processo, o instalador irá:

* Crie um contêiner de blob na conta de armazenamento do Azure pilha especificada.
* Crie uma zona DNS e entradas para o serviço de aplicativo.
* Registre o provedor de recursos do serviço de aplicativo.
* Registre os itens da Galeria do serviço de aplicativo.

Para implantar o provedor de recursos do serviço de aplicativo, siga estas etapas:

1. Execute appservice.exe como um administrador (azurestack\CloudAdmin).

2. Clique em **implantar o serviço de aplicativo na nuvem do Azure pilha**.

    ![Instalador de serviço de aplicativo](media/azure-stack-app-service-deploy/image01.png)

3. Revise e aceite os termos de licença de Software da Microsoft e, em seguida, clique em **próximo**.

4. Revise e aceite os termos de licença de terceiros e, em seguida, clique em **próximo**.

5. Certifique-se de que as informações de configuração de nuvem do serviço de aplicativo estão corretas. Se você usou as configurações padrão durante a implantação do Kit de desenvolvimento de pilha do Azure, você pode aceitar os valores padrão aqui. No entanto, se você personalizou as opções quando você implantou a pilha do Azure, você deve editar os valores nesta janela de refletir isso. Por exemplo, se você usar o mycloud.com de sufixo de domínio, o ponto de extremidade deve alterar a management.mycloud.com. Depois de confirmar suas informações, clique em **próximo**.

    ![Instalador de serviço de aplicativo](media/azure-stack-app-service-deploy/image02.png)

6. Na próxima página:
    1. Clique no **conectar** lado a **assinaturas de pilha do Azure** caixa.
        - Se você estiver usando o Azure Active Directory (AD do Azure), insira a conta de administrador do AD do Azure e a senha que você forneceu quando você implantou a pilha do Azure. Clique em **Entrar**.
        - Se você estiver usando os serviços de Federação do Active Directory (AD FS), fornece sua conta de administrador. Por exemplo: cloudadmin@azurestack.local. Digite sua senha e clique em **entrar**.
    2. No **assinaturas do Azure pilha** , selecione sua assinatura.
    3. No **Azure pilha locais** , selecione o local que corresponde à região que você está implantando. Por exemplo, selecione **local** se sua implantação para o Kit de desenvolvimento de pilha do Azure.
    4. Insira um **nome do grupo de recursos** para sua implantação do serviço de aplicativo. Por padrão, ele é definido como **APPSERVICE\<região\>**.
    5. Insira o **nome da conta de armazenamento** que você deseja que o serviço de aplicativo a ser criado como parte da instalação. Por padrão, ele é definido como **appsvclocalstor**.
    6. Clique em **Avançar**.

    ![Instalador de serviço de aplicativo](media/azure-stack-app-service-deploy/image03.png)

7. Insira as informações para o compartilhamento de arquivo e, em seguida, clique em **próximo**. O endereço do compartilhamento de arquivos deve usar o nome totalmente qualificado domínio do servidor de arquivos, por exemplo \\\appservicefileserver.local.cloudapp.azurestack.external\websites ou o endereço IP, por exemplo \\\10.0.0.1\websites.

    ![Instalador de serviço de aplicativo](media/azure-stack-app-service-deploy/image04.png)

8. Na próxima página:
    1. No **ID do aplicativo de identidade** , digite o GUID para o aplicativo que você está usando para a identidade.
    2. No **arquivo de certificado de identidade de aplicativo** caixa, digite (ou procure) o local do arquivo do certificado.
    3. No **senha do certificado de identidade de aplicativo** caixa, digite a senha do certificado. Essa senha é aquele que você anotou quando você usou o script para criar os certificados.
    4. No **arquivo do certificado raiz do Azure Resource Manager** caixa, digite (ou procure) o local do arquivo do certificado.
    5. Clique em **Avançar**.

    ![Instalador de serviço de aplicativo](media/azure-stack-app-service-deploy/image05.png)

9. Para cada um dos três caixas de arquivo de certificado, clique em **procurar** e navegue até o arquivo de certificado apropriado. Você deve fornecer a senha para cada certificado. Esses certificados são aqueles que você criou no [etapa de certificados necessários crie](azure-stack-app-service-deploy.md#create-the-required-certificates). Clique em **próximo** depois de inserir todas as informações.

    | Box | Exemplo de nome de arquivo de certificado |
    | --- | --- |
    | **Arquivo de certificado SSL do serviço de aplicativo padrão** | \_. appservice.local.AzureStack.external.pfx |
    | **Arquivo de certificado SSL de API do serviço de aplicativo** | api.appservice.local.AzureStack.external.pfx |
    | **Arquivo de certificado de SSL de publicador do serviço de aplicativo** | ftp.appservice.local.AzureStack.external.pfx |

    Se você usou um sufixo de domínio diferente quando você criou os certificados, os nomes de arquivo de certificado não usam *local. AzureStack.external*. Em vez disso, use as informações de domínio personalizado.

    ![Instalador de serviço de aplicativo](media/azure-stack-app-service-deploy/image06.png)    

10. Insira os detalhes do SQL Server para a instância do servidor usada para hospedar os bancos de dados do provedor de recursos do serviço de aplicativo e, em seguida, clique em **próximo**. O instalador valida as propriedades de conexão SQL.

    ![Instalador de serviço de aplicativo](media/azure-stack-app-service-deploy/image07.png)    

11. Examine as opções de SKU e a instância de função. Os padrões são preenchidos com o número mínimo de instância e o SKU mínimo para cada função em uma implantação de ASDK. Um resumo dos requisitos de memória e núcleo é fornecido para ajudar a planejar a implantação. Depois de fazer suas seleções, clique em **próximo**.

    > [!NOTE]
    > Para implantações de produção, seguindo as orientações em [planejamento de capacidade de funções de servidor do serviço de aplicativo do Azure na pilha do Azure](azure-stack-app-service-capacity-planning.md).
    > 
    >

    | Função | Instâncias mínimas | SKU mínimo | Observações |
    | --- | --- | --- | --- |
    | Controller | 1 | Standard_A1 - (1 núcleo, 1792 MB) | Gerencia e mantém a integridade da nuvem do serviço de aplicativo. |
    | Gerenciamento | 1 | Standard_A2 - (2 núcleos, 3584 MB) | Gerencia os pontos de extremidade do serviço de aplicativo do Azure Resource Manager e a API, extensões portais (administrador, locatário, portal de funções) e o serviço de dados. Para dar suporte a failover, aumentado instâncias recomendadas para 2. |
    | Editor | 1 | Standard_A1 - (1 núcleo, 1792 MB) | Publica o conteúdo por meio de implantação da web e FTP. |
    | FrontEnd | 1 | Standard_A1 - (1 núcleo, 1792 MB) | Encaminha solicitações para aplicativos de serviço de aplicativo. |
    | Trabalho compartilhados | 1 | Standard_A1 - (1 núcleo, 1792 MB) | Hosts ou aplicativos de API aplicativos web e funções do Azure. Você talvez queira adicionar mais instâncias. Como um operador, você pode definir sua oferta e escolha qualquer camada SKU. As camadas devem ter um mínimo de um núcleo. |

    ![Instalador de serviço de aplicativo](media/azure-stack-app-service-deploy/image08.png)    

    > [!NOTE]
    > **Windows Server 2016 Core não é uma imagem de plataforma com suporte para uso com o serviço de aplicativo do Azure na pilha do Azure**.

12. No **selecione a imagem de plataforma** caixa, escolha sua imagem de máquina virtual de implantação do Windows Server 2016 disponíveis no provedor de recursos de computação para a nuvem de serviço de aplicativo. Clique em **Avançar**.

13. Na próxima página:
     1. Insira o nome de usuário do administrador de máquina virtual de função de trabalho e a senha.
     2. Insira o nome de usuário do administrador de máquina virtual de outras funções e a senha.
     3. Clique em **Avançar**.

    ![Instalador de serviço de aplicativo](media/azure-stack-app-service-deploy/image09.png)    

14. Na página de resumo:
    1. Verifique se as seleções feitas por você. Para fazer alterações, use o **anterior** botões para visitar a páginas anteriores.
    2. Se as configurações estão corretas, selecione a caixa de seleção.
    3. Para iniciar a implantação, clique em **próximo**.

    ![Instalador de serviço de aplicativo](media/azure-stack-app-service-deploy/image10.png)    

15. Na próxima página:
    1. Acompanhar o progresso da instalação. Serviço de aplicativo no Azure pilha leva cerca de 60 minutos para implantar com base nas seleções padrão.
    2. Depois que a instalação for concluída com êxito, clique em **saída**.

    ![Instalador de serviço de aplicativo](media/azure-stack-app-service-deploy/image11.png)    


## <a name="validate-the-app-service-on-azure-stack-installation"></a>Validar o serviço de aplicativo na instalação de pilha do Azure

1. No portal de administração de pilha do Azure, vá para **administração - serviço de aplicativo**.

2. Na visão geral em status, verifique que o **Status** mostra **todas as funções estão prontas**.

    ![Gerenciamento de serviço de aplicativo](media/azure-stack-app-service-deploy/image12.png)    

## <a name="test-drive-app-service-on-azure-stack"></a>Faça o test drive do serviço de aplicativo na pilha do Azure

Depois de implantar e registrar o provedor de recursos do serviço de aplicativo, testá-lo para certificar-se de que os usuários podem implantar aplicativos web e API.

> [!NOTE]
> Você precisa criar uma oferta que tenha o namespace Microsoft Web dentro do plano. Em seguida, você precisa ter uma assinatura de locatário se inscreve para esta oferta. Para obter mais informações, consulte [criar oferta](azure-stack-create-offer.md) e [criar plano](azure-stack-create-plan.md).
>
Você *deve* tem uma assinatura de locatário para criar aplicativos que usam o serviço de aplicativo na pilha do Azure. Os recursos de apenas um administrador de serviço pode ser concluída dentro do portal de administração relacionados para a administração de provedor de recursos de serviço de aplicativo. Esses recursos incluem a capacidade de adicionar, configurando fontes de implantação e adicionar camadas de trabalhador e SKUs.
>
A partir do terceiro technical preview, funções de aplicativos criar a web, API e o Azure, você deve usar o portal de locatário e ter uma assinatura de locatário.

1. No portal de locatário de pilha do Azure, clique em **novo** > **Web + móvel** > **aplicativo Web**.

2. Sobre o **aplicativo Web** folha, digite um nome no **aplicativo Web** caixa.

3. Em **grupo de recursos**, clique em **novo**. Digite um nome no **grupo de recursos** caixa.

4. Clique em **Plano do Serviço de Aplicativo/Local** > **Criar Novo**.

5. No **plano de serviço de aplicativo** folha, digite um nome no **plano de serviço de aplicativo** caixa.

6. Clique em **preço** > **compartilhado gratuito** ou **compartilhado compartilhado** > **selecione**  >   **Okey** > **criar**.

7. Em menos de um minuto, um bloco para o novo aplicativo web é exibida no painel. Clique no bloco.

8. Sobre o **aplicativo Web** folha, clique em **procurar** para exibir o site padrão para este aplicativo.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Implantar um site de WordPress, DNN ou Django (opcional)

1. No portal de locatário de pilha do Azure, clique em  **+** , vá para o Azure Marketplace, implantar um site Django e aguardar a conclusão bem-sucedida. O web platform Django usa um banco de dados do arquivo baseado no sistema. Ele não requer que os provedores de recursos adicionais, como SQL ou MySQL.

2. Se você também implantou um provedor de recursos do MySQL, você pode implantar um site de WordPress do Marketplace. Quando você for solicitado para parâmetros de banco de dados, digite o nome de usuário como  *User1@Server1* com o nome de usuário e o nome do servidor de sua escolha.

3. Se você também implantou um provedor de recursos do SQL Server, você pode implantar um site DNN do Marketplace. Quando você for solicitado para parâmetros de banco de dados, escolha um banco de dados no computador executando o SQL Server que está conectado ao seu provedor de recursos.

## <a name="next-steps"></a>Próximas etapas

Você também pode experimentar outros [plataforma como um serviço (PaaS) serviços](azure-stack-tools-paas-services.md).

- [Provedor de recursos do SQL Server](azure-stack-sql-resource-provider-deploy.md)
- [Provedor de recursos MySQL](azure-stack-mysql-resource-provider-deploy.md)

<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[App_Service_Deployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525
