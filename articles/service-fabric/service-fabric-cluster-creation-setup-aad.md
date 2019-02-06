---
title: Configurar o Azure Active Directory para autenticação de cliente do Service Fabric | Microsoft Docs
description: Saiba como configurar o Azure AD (Azure Active Directory) para autenticar clientes para clusters do Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/15/2018
ms.author: aljo
ms.openlocfilehash: 691995d0aa426766caed2f5e2458399b32332c9d
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2019
ms.locfileid: "54903495"
---
# <a name="set-up-azure-active-directory-for-client-authentication"></a>Configurar o Azure Active Directory para autenticação de cliente

Para clusters em execução no Azure, o Azure AD (Azure Active Directory) é recomendado para proteger o acesso a pontos de extremidade de gerenciamento.  Este artigo descreve como configurar o Azure AD para autenticar clientes para um cluster do Service Fabric, que deve ser feito antes de [criar o cluster](service-fabric-cluster-creation-via-arm.md).  O Azure AD permite que as organizações (conhecidas como locatários) gerenciem o acesso dos usuários aos aplicativos. Os aplicativos são divididos entre os que têm IU de entrada na Web e os que têm experiência de cliente nativa. Neste artigo, partimos do pressuposto que você já tenha criado um locatário. Se não for o caso, comece lendo [Como obter um locatário do Azure Active Directory][active-directory-howto-tenant].

## <a name="create-azure-ad-applications"></a>Criar aplicativos do Azure AD
Os clusters do Service Fabric oferecem vários pontos de entrada para a funcionalidade de gerenciamento, incluindo o [Service Fabric Explorer][service-fabric-visualizing-your-cluster] online o [Visual Studio][service-fabric-manage-application-in-visual-studio]. Como resultado, você criará dois aplicativos do Azure AD para controlar o acesso ao cluster, um aplicativo Web e um aplicativo nativo.  Depois que os aplicativos forem criados, você atribuirá usuários a funções de administrador e somente leitura.

Para simplificar algumas das etapas envolvidas na configuração do Azure AD com um cluster do Service Fabric, criamos um conjunto de scripts do Windows PowerShell.

> [!NOTE]
> Você deve concluir as etapas a seguir para poder criar o cluster. Como os scripts esperam pontos de extremidade e nomes de cluster, os valores devem ser planejados, não os valores que você já tinha criado.

1. [Faça download dos scripts](https://github.com/robotechredmond/Azure-PowerShell-Snippets/tree/master/MicrosoftAzureServiceFabric-AADHelpers/AADTool) em seu computador.
2. Clique com o botão direito do mouse no arquivo zip, selecione **Propriedades**, marque a caixa de seleção **Desbloquear** e clique em **Aplicar**.
3. Extraia o arquivo zip.
4. Execute `SetupApplications.ps1` e forneça TenantId, ClusterName e WebApplicationReplyUrl como parâmetros. Por exemplo: 

```PowerShell
.\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
```

> [!NOTE]
> Para nuvens nacionais (Azure Governamental, Azure China, Azure Alemanha), você também deve especificar o `-Location` parâmetro.

Você pode encontrar o TenantId executando o comando `Get-AzureSubscription` do PowerShell. A execução desse comando exibe a TenantId para cada assinatura.

O ClusterName é usado para prefixar os aplicativos do Azure AD criados pelo script. Ele não precisa corresponder exatamente ao nome real do cluster. Serve apenas para tornar mais fácil mapear artefatos do Azure AD para o cluster do Service Fabric com que estão sendo usados.

WebApplicationReplyUrl é o ponto de extremidade padrão que o Azure AD retorna para seus usuários depois que eles concluem o processo de entrada. Defina esse ponto de extremidade como o ponto de extremidade do Service Fabric Explorer para o seu cluster, que por padrão é:

https://&lt;cluster_domain&gt;:19080/Explorer

Você precisará entrar em uma conta que tenha privilégios administrativos para o locatário do Azure AD. Depois de entrar, o script criará aplicativos Web e nativos para representar seu cluster do Service Fabric. Se você observar os aplicativos do locatário no [Portal do Azure][azure-portal], você verá duas novas entradas:

   * *ClusterName*\_Cluster
   * *ClusterName*\_Cliente

O script imprimirá o JSON necessário para o modelo do Azure Resource Manager ao criar o cluster na próxima seção. Portanto, convém manter a janela do PowerShell aberta.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

<a name="assign-roles"></a>

## <a name="assign-users-to-roles"></a>Atribuir usuários a funções
Depois de criar os aplicativos para representar seu cluster, atribua os usuários às funções com suporte no Service Fabric: somente leitura e administrador. É possível atribuir as funções usando o [Portal do Azure][azure-portal].

1. No Portal do Azure, selecione seu locatário no canto superior direito.

    ![Selecionar o botão de locatário][select-tenant-button]
2. Selecione **Azure Active Directory** na guia à esquerda e selecione “Aplicativos corporativos”.
3. Selecione "Todos os aplicativos" e, em seguida, localize e selecione o aplicativo Web, que tem um nome como `myTestCluster_Cluster`.
4. Clique na guia **Usuários e grupos**.

    ![Guia Usuários e grupos][users-and-groups-tab]
5. Clique no botão **Adicionar usuário** na nova página, selecione um usuário e a função a ser atribuída e clique no botão **Selecionar** na parte inferior da página.

    ![Página Atribuir usuários a funções][assign-users-to-roles-page]
6. Clique no botão **Atribuir** na parte inferior da página.

    ![Adicionar confirmação de atribuição][assign-users-to-roles-confirm]

> [!NOTE]
> Para saber mais sobre as funções no Service Fabric, veja [Controle de acesso baseado em função para clientes do Service Fabric](service-fabric-cluster-security-roles.md).
>
>

## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Ajuda da solução de problemas na configuração do Azure Active Directory
Configurar e usar o Azure AD - Microsoft Azure Active Directory, pode ser um desafio, portanto, aqui estão algumas dicas sobre o que você pode fazer para depurar o problema.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>O Service Fabric Explorer solicita que você selecione um certificado
#### <a name="problem"></a>Problema
Depois de entrar no Azure AD no Service Fabric Explorer, o navegador retornará para a home page, mas uma mensagem solicitará que você selecione um certificado.

![Caixa de diálogo do certificado SFX][sfx-select-certificate-dialog]

#### <a name="reason"></a>Motivo
O usuário não recebeu uma função no aplicativo de cluster do Azure AD. Assim, a autenticação do Azure AD falha no cluster do Service Fabric. O Service Fabric Explorer reverterá para autenticação de certificado.

#### <a name="solution"></a>Solução
Siga as instruções para configurar o Azure AD e atribuir funções de usuário. Além disso, recomendamos que você habilite a "Atribuição de usuário necessária para acessar o aplicativo" da mesma forma feita por `SetupApplications.ps1`.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>A conexão com o PowerShell falha com um erro: "As credenciais especificadas são inválidas"
#### <a name="problem"></a>Problema
Quando você usa o PowerShell para se conectar ao cluster usando o modo de segurança do "AzureActiveDirectory", depois entrar no AD do Azure, a conexão falha com um erro: "As credenciais especificadas são inválidas."

#### <a name="solution"></a>Solução
Essa solução é igual à anterior.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>O Service Fabric Explorer retorna uma falha quando você entra: "AADSTS50011"
#### <a name="problem"></a>Problema
Quando você tenta entrar no Azure AD, no Service Fabric Explorer, a página retorna uma falha: "AADSTS50011: O endereço de resposta &lt;url&gt; não corresponde aos endereços de resposta configurados para o aplicativo: &lt;guid&gt;."

![O endereço de resposta SFX não corresponde][sfx-reply-address-not-match]

#### <a name="reason"></a>Motivo
O aplicativo do cluster (Web) que representa o Service Fabric Explorer tenta se autenticar no Azure AD como parte da solicitação que ele fornece à URL de retorno de redirecionamento. Mas a URL não está listada na lista **URL DE RESPOSTA** do aplicativo Azure AD.

#### <a name="solution"></a>Solução
Selecione "Registros do aplicativo" na página do AAD, selecione o aplicativo de cluster e, em seguida, selecione o botão **URLs de Resposta**. Na página “URLs de Resposta”, adicione a URL do Service Fabric Explorer à lista ou substitua um dos itens na lista. Quando terminar, salve a alteração.

![URL de resposta do aplicativo Web][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>Conectar o cluster usando a autenticação do Azure AD por meio do PowerShell
Para se conectar ao cluster do Service Fabric, use o seguinte exemplo de comando do PowerShell:

```PowerShell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Para saber mais, consulte o cmdlet [Connect-ServiceFabricCluster](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>Posso reutilizar o mesmo locatário do Azure AD em vários clusters?
Sim. Mas lembre-se de adicionar a URL do Service Fabric Explorer ao aplicativo do cluster (Web). Caso contrário, o Service Fabric Explorer não funcionará.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Por que eu ainda preciso de um certificado de servidor quando o Azure AD está habilitado?
FabricClient e FabricGateway realizam autenticação mútua. Durante a autenticação do Azure AD, a integração dele fornece uma identidade de cliente para o servidor e o certificado do servidor é usado para verificar a identidade do servidor. Para saber mais sobre certificados do Service Fabric, confira [Certificados X.509 e Service Fabric][x509-certificates-and-service-fabric].

## <a name="next-steps"></a>Próximas etapas
Depois de configurar os aplicativos do Azure Active Directory e definir as funções para os usuários, [configure e implante um cluster](service-fabric-cluster-creation-via-arm.md).


<!-- Links -->
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[azure-portal]: https://portal.azure.com/
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[select-tenant-button]: ./media/service-fabric-cluster-creation-setup-aad/select-tenant-button.png
[users-and-groups-tab]: ./media/service-fabric-cluster-creation-setup-aad/users-and-groups-tab.png
[assign-users-to-roles-page]: ./media/service-fabric-cluster-creation-setup-aad/assign-users-to-roles-page.png
[assign-users-to-roles-confirm]: ./media/service-fabric-cluster-creation-setup-aad/assign-users-to-roles-confirm.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-setup-aad/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-setup-aad/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-setup-aad/web-application-reply-url.png
