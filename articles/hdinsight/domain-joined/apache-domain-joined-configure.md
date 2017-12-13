---
title: "Configurar clusters do HDInsight ingressados no domínio – Azure | Microsoft Docs"
description: "Saiba como instalar e configurar clusters HDInsight ingressados no domínio"
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: 0cbb49cc-0de1-4a1a-b658-99897caf827c
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/02/2016
ms.author: saurinsh
ms.openlocfilehash: 649d138a85ca47440e43c00637ee92b86f4eb03e
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2017
---
# <a name="configure-domain-joined-hdinsight-clusters"></a>Configurar clusters HDInsight ingressados no domínio

Saiba como configurar um cluster Azure HDInsight com o Azure Active Directory (Azure AD) e [Apache Ranger](http://hortonworks.com/apache/ranger/) para tirar proveito das políticas de autenticação forte e do RBAC (controle de acesso avançado baseado em função).  O HDInsight ingressado em domínio só pode ser configurado em clusters baseados em Linux. Para obter mais informações, consulte [Introduzir clusters HDInsight ingressados no domínio](apache-domain-joined-introduction.md).

> [!IMPORTANT]
> O Oozie não está habilitado no HDInsight ingressado no domínio.

Este artigo é o primeiro tutorial de uma série:

* Crie um cluster HDInsight conectado ao Azure AD (por meio da funcionalidade Active Directory Domain Services) com o Apache Ranger habilitado.
* Crie e aplique políticas de Hive por meio do Apache Ranger e permita que os usuários (por exemplo, os cientistas de dados) se conectem ao Hive usando ferramentas baseadas em ODBC, por exemplo, Excel, Tableau, etc. A Microsoft está trabalhando para adicionar outras cargas de trabalho como Spark, HBase e Storm ao HDInsight ingressado no domínio, em breve.

Nomes de serviço do Azure devem ser globalmente exclusivos. Os nomes a seguir são usados neste tutorial. Contoso é um nome fictício. Você deve substituir *contoso* por um nome diferente ao passar pelo tutorial. 

**Nomes:**

| Propriedade | Valor |
| --- | --- |
| Diretório do AD do Azure |contosoaaddirectory |
| Nome de domínio do Azure AD |contoso (contoso.onmicrosoft.com) |
| VNet HDInsight |contosohdivnet |
| Grupo de recursos de VNet HDInsight |contosohdirg |
| Cluster HDInsight |contosohdicluster |

Este tutorial fornece as etapas para configurar um cluster HDInsight ingressado no domínio. Cada seção tem links para outros artigos com informações complementares.

## <a name="prerequisite"></a>Pré-requisito:
* Familiarize-se com o [Azure AD Domain Services](https://azure.microsoft.com/services/active-directory-ds/) e sua estrutura de [preços](https://azure.microsoft.com/pricing/details/active-directory-ds/).
* Certifique-se de que sua assinatura está na lista de permissões para essa visualização pública. Você pode fazer isso enviando um email para hdipreview@microsoft.com com sua ID da assinatura.
* Obtenha um certificado SSL assinado por uma autoridade ou certificado de autenticação para seu domínio. O certificado é necessário para configurar o LDAP seguro.

## <a name="procedures"></a>Procedimentos
1. Crie uma VNet HDInsight no modo de gerenciamento de recursos do Azure.
2. Crie e configure o Azure AD e o Azure AD DS.
3. Crie um cluster HDInsight.

> [!NOTE]
> Este tutorial presume que você não tenha um Azure AD. Se você tiver um, poderá ignorar essa parte.
> 
> 

## <a name="create-a-resource-manager-vnet-for-hdinsight-cluster"></a>Criar uma VNet do Resource Manager para o cluster HDInsight
Nesta seção, você criará uma VNet do Azure Resource Manager que será usada para o cluster HDInsight. Para obter mais informações sobre como criar VNET do Azure usando outros métodos, veja [Criar uma rede virtual](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md)

Depois de criar a rede virtual, você irá configurar o Azure AD DS para usar esta rede.

**Para criar uma VNet do Resource Manager**

1. Entre no [portal do Azure](https://portal.azure.com).
2. Clique em **Nova**, **Rede**, **Rede virtual**. 
3. Em **Selecionar um modelo de implantação**, selecione **Resource Manager** e clique em **Criar**.
4. Digite ou selecione os valores a seguir:
   
   * **Nome**: contosohdivnet
   * **Espaço de endereço**: 10.0.0.0/16.
   * **Nome da sub-rede**: Subnet1
   * **Intervalo de endereços da sub-rede**: 10.0.0.0/24
   * **Assinatura**: (Selecione sua assinatura do Azure.)
   * **Grupo de recursos**: contosohdirg
   * **Local**: (selecione o mesmo local d Azure AD VNet. Por exemplo, contosoaadvnet.)
5. Clique em **Criar**.

**Para configurar o DNS para a VNet do Resource Manager**

1. Do [Portal do Azure](https://portal.azure.com), clique em **Mais serviços** > **Redes virtuais**. Certifique-se de não clicar em **Redes virtuais (clássicas)**.
2. Clique em **contosohdivnet**.
3. Clique em **Servidores DNS** do lado esquerdo da nova folha.
4. Clique em **Personalizado** e insira os seguintes valores:
   
   * 10.0.0.4
   * 10.0.0.5     
     
5. Clique em **Salvar**.

## <a name="create-and-configure-azure-ad-ds-for-your-azure-ad"></a>Criar e configurar o Azure AD DS para o Azure AD
Nesta seção, você irá:

1. Criar um Azure AD.
2. Criar usuários do Azure AD. Esses usuários são usuários do domínio. Você pode usar o primeiro usuário para configurar o cluster HDInsight com o Azure AD.  Os dois usuários são opcionais para este tutorial. Eles serão usados em [Configurar políticas Hive para clusters HDInsight ingressados no domínio](apache-domain-joined-run-hive.md), quando você configurar políticas do Apache Ranger.
3. Crie o grupo de administradores de AAD DC e adicione o usuário do Azure AD ao grupo. Você pode usar esse usuário para criar a unidade organizacional.
4. Habilite o Azure AD DS (Azure AD Domain Services) para o Azure AD.
5. Configure o LDAPS para o Azure AD. O protocolo LDAP é usado para ler e gravar no Azure AD.

Se preferir usar um Azure AD existente, você poderá ignorar as etapas 1 e 2.

**Para criar um Azure AD**

1. Do [Portal clássico do Azure](https://manage.windowsazure.com), clique em **Novo** > **Serviços de Aplicativos** > **Active Directory** > **Diretório** > **Criação Personalizada**. 
2. Digite ou selecione os valores a seguir:
   
   * **Nome**: contosoaaddirectory
   * **Nome de domínio**: contoso.  Esse nome deve ser globalmente exclusivo.
   * **País ou região**: selecione seu país ou região.
3. Clique em **Concluído**.

**Criar um usuário do Azure AD**

1. Do [portal do Azure](https://portal.azure.com), clique em **Azure Active Directory** > **contosoaaddirectory** > **usuários e grupos**. 
2. Clique em **todos os usuários** no menu.
3. Clique em **Novo Usuário**.
4. Insira o **Nome** e **Nome de usuário**, em seguida, clique em **Próximo**. 
5. Configure o perfil do usuário; em **Função**, selecione **Administrador Global** e, em seguida, clique em **Próximo**.  A função de Administrador Global é necessária para criar unidades organizacionais.
6. Faça uma cópia da senha temporária.
7. Clique em **Criar**. Mais tarde neste tutorial, você usará esse usuário administrador global para criar o cluster HDInsight.

Siga o mesmo procedimento para criar mais dois usuários com a função **User**, hiveuser1 e hiveuser2. Os usuários a seguir serão usados em [Configurar políticas Hive para clusters HDInsight ingressados no domínio](apache-domain-joined-run-hive.md).

**Para criar o grupo de administradores de AAD DC e adicionar um usuário do Azure AD**

1. Do [portal do Azure](https://portal.azure.com), clique em **Azure Active Directory** > **contosoaaddirectory** > **usuários e grupos**. 
2. No menu superior, clique em **Todos os grupos**.
3. Clique em **Novo grupo**.
4. Digite ou selecione os valores a seguir:
   
   * **Nome**: Administradores do AAD DC.  Não altere o nome do grupo.
   * **Tipo de associação**: Atribuído.
5. Clique em **Selecionar**.
6. Clique em **Membros**.
7. Selecione o primeiro usuário que você criou na etapa anterior e clique em **Selecionar**.
8. Repita as mesmas etapas para criar outro grupo chamado **HiveUsers** e adicione os dois usuários do Hive ao grupo.

Para obter mais informações, veja [Azure AD Domain Services (Preview) – criar o grupo “Administradores do AAD DC”](../../active-directory-domain-services/active-directory-ds-getting-started.md).

**Para habilitar o Azure AD DS para o Azure AD**

1. Do [portal do Azure](https://portal.azure.com), clique em **criar um recurso** > **segurança + identidade** > **Azure AD Domain Services** > **Adicionar**. 
2. Digite ou selecione os valores a seguir:
   * **Nome do diretório**: contosoaaddirectory
   * **Nome DNS de domínio**: mostra o nome DNS padrão do diretório do Azure. Por exemplo, contoso.onmicrosoft.com.
   * **Localização**: selecione uma região.
   * **Rede virtual**: selecione a rede virtual e a sub-rede que você criou anteriormente. Por exemplo, **contosohdivnet**.
3. Clique em **OK** na página de resumo. Você verá **implantação em andamento...**  em notificações.
4. Aguarde até que **implantação em andamento...** desapareça e o campo **Endereço IP** seja populado. Dois endereços IP serão populados. Esses são os endereços IP dos controladores de domínio configurados pelo Domain Services. Cada endereço IP ficará visível depois que o controlador de domínio correspondente estiver provisionado e pronto. Anote os dois endereços IP. Você precisará delas mais tarde.

Para obter mais informações, consulte [Enable Azure Active Directory Domain Services using the Azure portal](../../active-directory-domain-services/active-directory-ds-getting-started.md) (Habilitar o Azure Active Directory Domain Services usando o Portal do Azure).

**Para sincronizar a senha**

Se você usar o seu próprio domínio, precisará sincronizar a senha. Veja [Enable password synchronization to Azure AD domain services for a cloud-only Azure AD directory](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) (Habilitar a sincronização de senha nos serviços de domínio do Azure AD para um diretório do Azure AD somente na nuvem).

**Para configurar o LDAPS para o Azure AD**

1. Obtenha um certificado SSL assinado por uma autoridade de autenticação para seu domínio.
2. Do [portal do Azure](https://portal.azure.com), clique em **Azure AD Domain Services** > **contoso.onmicrosoft.com**. 
3. Habilitar o **LDAP Seguro**.
6. Siga as instruções para especificar o arquivo de certificado e a senha.  
7. Aguarde até que **Certificado LDAP Seguro** seja populado. Isso pode levar 10 minutos ou mais.

> [!NOTE]
> Se algumas tarefas em segundo plano estiverem sendo executadas no Azure AD DS, você verá um erro ao carregar o certificado – <i>Há uma operação sendo executada para este locatário. Tente novamente mais tarde</i>.  Caso esse erro ocorra, tente novamente após algum tempo. O IP do segundo controlador de domínio pode levar até 3 horas para ser provisionado.
> 
> 

Para obter mais informações, veja [Configurar o LDAPS (LDAP Seguro) para um domínio gerenciado do Azure AD Domain Services](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="create-hdinsight-cluster"></a>Criar cluster HDInsight
Nesta seção, você criará um cluster Hadoop baseado em Linux no HDInsight usando o Portal do Azure ou então o [modelo do Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md). Para outros métodos de criação de cluster e noções básicas sobre as configurações, confira [Criar clusters do HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Para obter mais informações sobre como usar um modelo do Resource Manager para criar clusters Hadoop no HDInsight, confira [Criar clusters Hadoop no HDInsight usando modelos do Resource Manager](../hdinsight-hadoop-create-windows-clusters-arm-templates.md)

**Para criar um cluster HDInsight ingressado no Domínio usando o Portal do Azure**

1. Entre no [portal do Azure](https://portal.azure.com).
2. Clique em **Novo**, **Inteligência + análise** e então em **HDInsight**.
3. Da folha **Novo cluster HDInsight**, insira ou selecione os valores a seguir:
   
   * **Nome do cluster**: insira um novo nome do cluster para o cluster HDInsight ingressado no domínio.
   * **Assinatura**: selecione uma assinatura do Azure usada para criar esse cluster.
   * **Configuração do cluster**:
     
     * **Tipo de cluster**: Hadoop. Domínio HDInsight é suportado atualmente nos clusters Hadoop, Spark e Interactive Query.
     * **Sistema Operacional**: Linux.  Apenas clusters HDInsight baseados em Linux dão suporte ao HDInsight ingressado no domínio.
     * **Versão**: HDI 3.6. Somente a versão 3.6 do cluster HDInsight dá suporte ao HDInsight ingressado no domínio.
     * **Tipo de cluster**: PREMIUM
       
       Clique em **Selecionar** para salvar as alterações.
   * **Credenciais**: configure as credenciais para o usuário do cluster e o usuário SSH.
   * **Fonte de Dados**: crie uma nova conta de armazenamento ou use uma conta de armazenamento existente como a conta de armazenamento padrão para o cluster HDInsight. A localização deve ser a mesma que a das duas VNets.  A localização também é a localização do cluster HDInsight.
   * **Preço**: selecione o número de nós de trabalho do seu cluster.
   * **Configurações avançadas**: 
     
     * **Ingresso no domínio e Sub-rede/VNet**: 
       
       * **Configurações de domínio**: 
         
         * **Nome de domínio**: contoso.onmicrosoft.com
         * **Nome de usuário de domínio**: insira um nome de usuário de domínio. Esse domínio deve ter os seguintes privilégios: adicionar computadores ao domínio e colocá-los na unidade organizacional especificada durante a criação do cluster; criar entidades de serviço dentro da unidade organizacional especificada durante a criação de cluster; criar entradas de DNS reverso. Esse usuário de domínio se tornará o administrador deste cluster HDInsight ingressado no domínio.
         * **Senha de domínio**: insira a senha de usuário de domínio.
         * **Unidade Organizacional**: insira o nome diferenciado da UO que você deseja usar com o cluster HDInsight. Por exemplo: OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com. Se essa UO não existir, o cluster HDInsight tentará criá-la. Verifique se a UO já existe ou se a conta de domínio tem permissões para criar uma nova. Se você usar a conta de domínio que faz parte dos administradores AADDC, ela terá as permissões necessárias para criar a UO.
         * **URL LDAPS**: ldaps://contoso.onmicrosoft.com:636
         * **Acessar grupo de usuários**: especifique o grupo de segurança cujos usuários você deseja sincronizar com o cluster. Por exemplo, HiveUsers.
           
           Clique em **Selecionar** para salvar as alterações.
           
           ![Configuração de domínio para configurar o portal do HDInsight ingressado no domínio](./media/apache-domain-joined-configure/hdinsight-domain-joined-portal-domain-setting.png)
       * **Rede virtual**: contosohdivnet
       * **Subrede**: Subnet1
         
         Clique em **Selecionar** para salvar as alterações.        
         Clique em **Selecionar** para salvar as alterações.
   * **Grupo de Recursos**: selecione o grupo de recursos usado para a VNet HDInsight (contosohdirg).
4. Clique em **Criar**.  

Outra opção para criar o cluster HDInsight de domínio é usar um modelo do Azure Resource Manager. O procedimento a seguir mostra como fazer isso:

**Para criar um cluster HDInsight ingressado no domínio usando um modelo do Azure Resource Manager**

1. Clique na imagem a seguir para abrir um modelo do Resource Manager no Portal do Azure. O modelo do Resource Manager está localizado em um contêiner de blob público. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-domain-joined-hdinsight-cluster.json" target="_blank"><img src="./media/apache-domain-joined-configure/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Na folha **Parâmetros**, insira os seguintes valores:
   
   * **Assinatura**: (Selecione sua assinatura do Azure).
   * **Grupo de recursos**: clique em **Usar existente** e especifique o mesmo grupo de recursos que você vem usando.  Por exemplo, contosohdirg. 
   * **Localização**: especifique uma localização para o grupo de recursos.
   * **Nome do Cluster**: insira um nome para o cluster Hadoop que você criará. Por exemplo, contosohdicluster.
   * **Tipo de Cluster**: selecione um tipo de cluster.  O valor padrão é **hadoop**.
   * **Localizaçao**: selecione uma localização para o cluster.  A conta de armazenamento padrão usa a mesma localização.
   * **Contagem de Nós de Trabalho do Cluster**: selecione o número de nós de trabalho.
   * **Nome e senha de logon do cluster**: o nome de logon padrão é **admin**.
   * **Nome de usuário e senha SSH**: o nome de usuário padrão é **sshuser**.  Você pode renomeá-lo. 
   * **ID da Rede Virtual**: /subscriptions/&lt;SubscriptionID>/resourceGroups/&lt;ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/&lt;VNetName>
   * **Sub-rede da Rede Virtual**: /subscriptions/&lt;SubscriptionID>/resourceGroups/&lt;ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/&lt;VNetName>/subnets/Subnet1
   * **Nome de Domínio**: contoso.onmicrosoft.com
   * **DN da Unidade Organizacional**: OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com
   * **DNs do grupo de usuários do cluster**: [\"HiveUsers\"]
   * **LDAPUrls**: ["ldaps://contoso.onmicrosoft.com:636"]
   * **DomainAdminUserName**: (Insira o nome do usuário administrador do domínio)
   * **DomainAdminPassword**: (Insira a senha do usuário administrador do domínio)
   * **Concordo com os termos e condições declarados acima**: (Marcar)
   * **Fixar no painel**: (Marcar)
3. Clique em **Comprar**. Você verá um novo bloco intitulado **Implantando a implantação de modelo**. A criação de um cluster demora cerca de 20 minutos. Após a criação do cluster, você pode clicar na folha do cluster no portal para abri-la.

Depois de concluir o tutorial, talvez você queira excluir o cluster. Com o HDInsight, seus dados são armazenados no Armazenamento do Azure, assim você poderá excluir, com segurança, um cluster quando ele não estiver em uso. Você também é cobrado por um cluster HDInsight, mesmo quando ele não está em uso. Como os encargos para o cluster são muitas vezes maiores do que os encargos para armazenamento, faz sentido, do ponto de vista econômico, excluir os clusters quando não estiverem em uso. Para obter instruções sobre como excluir um cluster, confira [Gerenciar clusters Hadoop no HDInsight usando o Portal do Azure](../hdinsight-administer-use-management-portal.md#delete-clusters).

## <a name="next-steps"></a>Próximas etapas
* Para configurar políticas do Hive e executar consultas do Hive, confira [Configurar políticas do Hive para clusters HDInsight associados ao domínio](apache-domain-joined-run-hive.md).
* Para usar SSH para conectar-se a clusters do HDInsight Ingressados no Domínio, confira [Usar SSH com Hadoop baseado em Linux no HDInsight do Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

