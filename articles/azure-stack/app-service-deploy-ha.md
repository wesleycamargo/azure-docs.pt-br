---
title: Implantar o serviço de aplicativo do Azure Stack em uma configuração altamente disponível | Microsoft Docs
description: Saiba como implantar o serviço de aplicativo no Azure Stack usando uma configuração altamente disponível.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: ''
ms.date: 03/13/2019
ms.author: jeffgilb
ms.reviewer: anwestg
ms.lastreviewed: 03/13/2019
ms.openlocfilehash: 50e5272d92ad333e70f65173cf024d165dc7501c
ms.sourcegitcommit: f596d88d776a3699f8c8cf98415eb874187e2a48
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2019
ms.locfileid: "58102036"
---
# <a name="deploy-app-service-in-a-highly-available-configuration"></a>Implantar o serviço de aplicativo em uma configuração altamente disponível

Este artigo mostra como usar itens do marketplace do Azure Stack para implantar o serviço de aplicativo para o Azure Stack em uma configuração altamente disponível. Além dos itens do marketplace disponíveis, essa solução também usa o [appservice-compartilhamento de arquivos-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) modelo de início rápido do Azure Stack. Este modelo automatiza a criação de uma infra-estrutura altamente disponível para hospedar o provedor de recursos do serviço de aplicativo. O serviço de aplicativo é instalado nessa infraestrutura de VM altamente disponível. 

## <a name="deploy-the-highly-available-app-service-infrastructure-vms"></a>Implantar a infraestrutura de serviço de aplicativo máquinas virtuais altamente disponível
O [appservice-compartilhamento de arquivos-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) modelo de início rápido do Azure Stack simplifica a implantação do serviço de aplicativo em uma configuração altamente disponível. Ele deve ser implantado na assinatura do provedor padrão. 

Quando usado para criar um recurso personalizado no Azure Stack, o modelo cria:
- Uma rede virtual e sub-redes necessárias.
- Grupos de segurança de rede para o servidor de arquivos, SQL Server e as sub-redes de serviços de domínio Active Directory (AD DS).
- Contas de armazenamento para discos de VM e a testemunha de nuvem do cluster.
- Um balanceador de carga interno para VMs do SQL com endereço IP privado associado ao ouvinte AlwaysOn do SQL.
- Três conjuntos de disponibilidade para o AD DS, o cluster de servidor de arquivos e o cluster do SQL.
- Cluster do SQL de dois nós.
- Cluster de servidores de arquivos de dois nós.
- Dois controladores de domínio.

### <a name="required-azure-stack-marketplace-items"></a>Itens necessários de marketplace do Azure Stack
Antes de usar este modelo, certifique-se de que o seguinte [itens do marketplace do Azure Stack](azure-stack-marketplace-azure-items.md) estão disponíveis na sua instância do Azure Stack:

- Imagem do Windows Server 2016 Datacenter Core (para AD DS e o arquivo server VMs)
- SQL Server 2016 SP2 no Windows Server 2016 (Enterprise)
- Extensão de IaaS do SQL mais recente 
- Extensão de Desired State Configuration do PowerShell mais recente 

> [!TIP]
> Revisão [o arquivo Leiame de modelo](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) no GitHub para obter detalhes adicionais sobre os requisitos de modelo e os valores padrão. 

### <a name="deploy-the-app-service-infrastructure"></a>Implantar a infraestrutura de serviço de aplicativo
Use as etapas nesta seção para criar uma implantação personalizada usando o **appservice-compartilhamento de arquivos-sqlserver-ha** modelo de início rápido do Azure Stack.

1. 
   [!INCLUDE [azs-admin-portal](../../includes/azs-admin-portal.md)]

2. Selecione **\+** **criar um recurso** > **personalizado**e então **implantação de modelo**.

   ![Implantação de modelo personalizado](media/app-service-deploy-ha/1.png)


3. Sobre o **implantação personalizada** folha, selecione **Editar modelo** > **modelo de início rápido** e, em seguida, use a lista suspensa de modelos personalizados disponíveis para Selecione o **appservice-compartilhamento de arquivos-sqlserver-ha** modelo, clique em **Okey**e então **salvar**.

   ![Selecione o modelo de início rápido appservice-compartilhamento de arquivos-sqlserver-alta disponibilidade](media/app-service-deploy-ha/2.png)

4. Sobre o **implantação personalizada** folha, selecione **Editar parâmetros** e role para baixo para examinar os valores de modelo padrão. Modifique esses valores conforme o necessário para fornecer todas as informações de parâmetro necessário e, em seguida, clique em **Okey**.<br><br> No mínimo, fornece senhas complexas para os parâmetros ADMINPASSWORD, FILESHAREOWNERPASSWORD, FILESHAREUSERPASSWORD, SQLSERVERSERVICEACCOUNTPASSWORD e SQLLOGINPASSWORD.
    
   ![Editar parâmetros de implantação personalizada](media/app-service-deploy-ha/3.png)

5. Sobre o **implantação personalizada** folha, certifique-se de **assinatura do provedor padrão** é selecionado como a assinatura para usar e, em seguida, crie um novo grupo de recursos ou selecione um grupo de recursos existente, para o personalizado implantação.<br><br> Em seguida, selecione o local do grupo de recursos (**local** para instalações de ASDK) e, em seguida, clique em **criar**. As configurações de implantação personalizado serão validadas antes do início da implantação de modelo.

    ![Criar implantação personalizada](media/app-service-deploy-ha/4.png)

6. No portal de administração, selecione **grupos de recursos** e, em seguida, o nome do grupo de recursos que você criou para a implantação personalizada (**app-service-ha** neste exemplo). Exiba o status da implantação para garantir que todas as implantações foram concluídas com êxito.

   > [!NOTE]
   > A implantação de modelo levará aproximadamente uma hora para ser concluída.

   [![](media/app-service-deploy-ha/5-sm.png "Examine o status de implantação de modelo")](media/app-service-deploy-ha/5-lg.png#lightbox)


### <a name="record-template-outputs"></a>Saídas do modelo de registro
Depois do modelo de implantação for concluída com êxito, gera a implantação de modelo de registro. Você precisará fornecer essas informações ao executar o instalador do serviço de aplicativo. 

Certifique-se de que você registre cada um desses valores de saída:
- FileSharePath
- FileShareOwner
- FileShareUser
- SQLserver
- SQLuser

Siga estas etapas para descobrir os valores de saída do modelo:

1. 
   [!INCLUDE [azs-admin-portal](../../includes/azs-admin-portal.md)]

2. No portal de administração, selecione **grupos de recursos** e, em seguida, o nome do grupo de recursos que você criou para a implantação personalizada (**app-service-ha** neste exemplo). 

3. Clique em **implantações** e selecione **Template**.

    ![Implantação de Template](media/app-service-deploy-ha/6.png)

4. Depois de selecionar o **Template** implantação, selecione **saídas** e registrar a saída do parâmetro de modelo. Essas informações serão necessárias ao implantar o serviço de aplicativo.

    ![Saída do parâmetro](media/app-service-deploy-ha/7.png)


## <a name="deploy-app-service-in-a-highly-available-configuration"></a>Implantar o serviço de aplicativo em uma configuração altamente disponível
Siga as etapas nesta seção para implantar o serviço de aplicativo para o Azure Stack em uma configuração altamente disponível com base nas [appservice-compartilhamento de arquivos-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) modelo de início rápido do Azure Stack. 

Depois de instalar o provedor de recursos do serviço de aplicativo, você pode incluí-lo em seus planos e ofertas. Os usuários podem inscrever-se, em seguida, para obter o serviço e iniciar a criação de aplicativos.

> [!IMPORTANT]
> Antes de executar o instalador do provedor de recursos, certifique-se de que você leu as notas de versão que acompanham cada versão do serviço de aplicativo, para saber mais sobre a nova funcionalidade, correções e problemas conhecidos que podem afetar sua implantação.

### <a name="prerequisites"></a>Pré-requisitos
Antes de executar o instalador do serviço de aplicativo, várias etapas são necessárias, conforme descrito na [antes de começar com o serviço de aplicativo no artigo do Azure Stack](azure-stack-app-service-before-you-get-started.md):

> [!TIP]
> Nem todas as etapas descritas a antes de você começar a usar o artigo são necessárias porque o modelo deploymnet configura a infraestrutura de VMs para você. 

- [Baixe os serviço de aplicativo instalador e scripts auxiliares](azure-stack-app-service-before-you-get-started.md#download-the-installer-and-helper-scripts).
- [Baixe a extensão de script personalizado mais recente para o Azure Stack marketplace](azure-stack-app-service-before-you-get-started.md#syndicate-the-custom-script-extension-from-the-marketplace).
- [Gerar os certificados necessários](azure-stack-app-service-before-you-get-started.md#get-certificates).
- Crie o aplicativo de ID com base no provedor de identidade que você escolheu para o Azure Stack. Uma ID de aplicativo podem ser feito para uma [do Azure AD](azure-stack-app-service-before-you-get-started.md#create-an-azure-active-directory-application) ou [serviços de Federação do Active Directory](azure-stack-app-service-before-you-get-started.md#create-an-active-directory-federation-services-application) e registrar a ID do aplicativo.
- Certifique-se de que você adicionou a imagem do Windows Server 2016 Datacenter para o marketplace do Azure Stack. Isso é necessário para a instalação do serviço de aplicativo.

### <a name="deploy-app-service-in-highly-available-configuration"></a>Implantar o serviço de aplicativo na configuração de alta disponibilidade
Instalar o provedor de recursos do serviço de aplicativo demora pelo menos uma hora. O período de tempo necessário depende da função quantas instâncias você implantar. Durante a implantação, o instalador executa as seguintes tarefas:

- Crie um contêiner de blob na conta de armazenamento do Azure Stack especificada.
- Crie uma zona DNS e entradas para o serviço de aplicativo.
- Registre o provedor de recursos do serviço de aplicativo.
- Registre os itens de galeria do serviço de aplicativo.

Para implantar o provedor de recursos do serviço de aplicativo, siga estas etapas:

1. Execute o instalador do serviço de aplicativo baixado anteriormente (**appservice.exe**) como um administrador de um computador que possa acessar o Azure Stack Azure recurso Gerenciamento de ponto de extremidade administrador.

2. Selecione **implantar o serviço de aplicativo ou atualização para a versão mais recente**.

    ![Implantar ou atualizar](media/app-service-deploy-ha/01.png)

3. Aceite os termos de licenciamento da Microsoft e clique em **próxima**.

    ![Termos de licenciamento da Microsoft](media/app-service-deploy-ha/02.png)

4. Aceite os termos de licenciamento não são da Microsoft e clique em **próxima**.

    ![Termos de licenciamento não são da Microsoft](media/app-service-deploy-ha/03.png)

5. Forneça o serviço de aplicativo de configuração de ponto de extremidade de nuvem para o seu ambiente do Azure Stack.

    ![Configuração de ponto de extremidade de nuvem do serviço de aplicativo](media/app-service-deploy-ha/04.png)

6. **Conectar-se** à assinatura do Azure Stack para ser usado para a instalação e escolha o local. 

    ![Conectar-se à assinatura do Azure Stack](media/app-service-deploy-ha/05.png)

7. Selecione **usar rede virtual existente e subredes** e o **nome do grupo de recursos** para o grupo de recursos usado para implantar o modelo altamente disponível.<br><br>Em seguida, selecione a rede virtual criada como parte da implantação do modelo e, em seguida, selecione as função apropriada subredes entre as opções da lista suspensa. 

    ![Seleção de rede virtual](media/app-service-deploy-ha/06.png)

8. Fornece que o modelo gravado anteriormente gera informações para o caminho do compartilhamento de arquivo e os parâmetros de proprietário do compartilhamento de arquivo. Quando terminar, clique em **próxima**.

    ![Informações de saída do compartilhamento de arquivo](media/app-service-deploy-ha/07.png)

9. Porque a máquina que está sendo usada para instalar o serviço de aplicativo não está localizada na mesma rede virtual como servidor de arquivos que está sendo usado para hospedar o compartilhamento de arquivos do serviço de aplicativo, você não poderá resolver o nome. Este comportamento é esperado.<br><br>Verifique se as informações inseridas para as informações de contas e o caminho UNC do compartilhamento de arquivos estão corretas e pressione **Sim** na caixa de diálogo alerta para continuar a instalação do serviço de aplicativo.

    ![Caixa de diálogo de erro esperado](media/app-service-deploy-ha/08.png)

10. Forneça a ID do aplicativo de identidade e o caminho e as senhas para os certificados de identidade e clique em **próxima**:
    - Certificado de identidade do aplicativo (no formato **sso.appservice.local.azurestack.external.pfx**)
    - Certificado de raiz do Gerenciador de recursos do Azure (**AzureStackCertificationAuthority.cer**)

    ![Certificado de ID do aplicativo e o certificado raiz](media/app-service-deploy-ha/008.png)

10. Em seguida, forneça as informações restantes necessárias para os seguintes certificados e clique em **próxima**:
    - Certificado SSL de pilha do Azure padrão (no formato **_.appservice.local.azurestack.external.pfx**)
    - Certificado SSL de API (no formato **api.appservice.local.azurestack.external.pfx**)
    - Certificado de Editor (na forma de **ftp.appservice.local.azurestack.external.pfx**) 

    ![Certificados de configuração adicional](media/app-service-deploy-ha/09.png)

11. Forneça as informações de conexão do SQL Server usando as informações de conexão do SQL Server das saídas de implantação de modelo de alta disponibilidade:

    ![Informações de conexão do SQL Server](media/app-service-deploy-ha/10.png)

12. Porque a máquina que está sendo usada para instalar o serviço de aplicativo não está localizada na mesma rede virtual como o SQL server que está sendo usado para hospedar os bancos de dados do serviço de aplicativo, você não poderá resolver o nome.  Este comportamento é esperado.<br><br>Verifique se as informações inseridas para as informações de nome e as contas do SQL Server estão corretas e pressione **Sim** para continuar a instalação do serviço de aplicativo. Clique em **Avançar**.

    ![Informações de conexão do SQL Server](media/app-service-deploy-ha/11.png)

13. Aceite os valores de configuração de função padrão ou altere para os valores recomendados e clique em **próxima**.<br><br>É recomendável que os valores padrão para as instâncias de função de infraestrutura do serviço de aplicativo ser alterado da seguinte maneira para configurações de alta disponibilidade:

    |Função|Padrão|Recomendação de alta disponibilidade|
    |-----|-----|-----|
    |Função do controlador|2|2|
    |Função de Gerenciamento|1|3|
    |Função publicador|1|3|
    |Função FrontEnd|1|3|
    |Função de trabalho compartilhada|1|10|
    |     |     |     |

    ![Valores de instância de função de infraestrutura](media/app-service-deploy-ha/12.png)

    > [!NOTE]
    > A alteração dos valores padão àquelas recomendadas nesse tutoral aumenta os requisitos de hardware para instalar o serviço de aplicativo. Um total de núcleos de 26 e 46,592 MB de RAM é necessária para dar suporte as 21 VMs recomendadas em vez de padão 18 núcleos e 32.256 MB de RAM para 15 VMs.

14. Selecione a imagem da plataforma a ser usado para instalar a infraestrutura de serviço de aplicativo, máquinas virtuais e clique em **próxima**:

    ![Seleção de imagem de plataforma](media/app-service-deploy-ha/13.png)

15. Fornecer informações de credenciais de função de infraestrutura a ser usada e clique em serviço de aplicativo **próxima**:

    ![Credenciais de função de infraestrutura](media/app-service-deploy-ha/14.png)

16. Examine as informações a serem usados para implantar o serviço de aplicativo e clique em **próxima** para iniciar a implantação. 

    ![Resumo da instalação de revisão](media/app-service-deploy-ha/15.png)

17. Examine o progresso da implantação de serviço de aplicativo. Isso pode levar mais de uma hora, dependendo de seu hardware e configurações específicas de implantação. Depois que o instalador foi concluída com êxito, selecione **Exit**.

    ![Instalação concluída](media/app-service-deploy-ha/16.png)


## <a name="next-steps"></a>Próximas etapas

[Expansão do serviço de aplicativo](azure-stack-app-service-add-worker-roles.md). Você talvez precise adicionar trabalhadores de função adicionais do serviço de aplicativo infra-estrutura para atender à demanda esperada de aplicativo em seu ambiente. Por padrão, o serviço de aplicativo no Azure Stack dá suporte a camadas de trabalhador gratuitos e compartilhados. Para adicionar outras camadas de trabalho, você precisa adicionar mais funções de trabalho.

[Configurar fontes de implantação](azure-stack-app-service-configure-deployment-sources.md). Configuração adicional é necessária para dar suporte à implantação sob demanda de vários provedores de controle do código-fonte como GitHub, BitBucket, OneDrive e DropBox.
