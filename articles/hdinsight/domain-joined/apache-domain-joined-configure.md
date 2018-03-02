---
title: "Configurar clusters do HDInsight ingressados no domínio – Azure | Microsoft Docs"
description: "Saiba como instalar e configurar clusters HDInsight ingressados no domínio"
services: hdinsight
documentationcenter: 
author: saurinsh
manager: cgronlun
editor: cgronlun
tags: 
ms.assetid: 0cbb49cc-0de1-4a1a-b658-99897caf827c
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/15/2018
ms.author: saurinsh
ms.openlocfilehash: b4d71eeb0aab75e67e851f867f194ed7578d0d1c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="configure-domain-joined-hdinsight-sandbox-environment"></a>Configurar ambiente de área restrita de HDInsight associada ao domínio

Saiba como configurar um cluster do Azure HDInsight com o Azure Active Directory autônomo e [Apache Ranger](http://hortonworks.com/apache/ranger/) para tirar proveito das fortes políticas de autenticação e RBAC (controle de acesso avançado baseado em função). Para obter mais informações, consulte [Introduzir clusters HDInsight ingressados no domínio](apache-domain-joined-introduction.md). 

> [!IMPORTANT]
> Por padrão, essa configuração somente pode ser utilizada com o uso de contas de Armazenamento do Microsoft Azure. Para usar com o Azure Data Lake Store, sincronize o Active Directory para um novo Azure Active Directory.

Sem o cluster do HDInsight associado ao domínio, cada cluster só pode ter uma conta de usuário do Hadoop HTTP e uma conta de usuário SSH.  A autenticação de vários usuários pode ser obtida usando:

-   Um Active Directory autônomo em execução no Azure IaaS.
-   Azure Active Directory.

Usar um Active Directory autônomo na execução no Azure IaaS é abordado neste artigo. É a arquitetura mais simples que um cliente pode seguir para obter suporte a vários usuários no HDInsight. Este artigo inclui duas abordagens para essa configuração:

- Opção 1: Use um modelo de gerenciamento de recursos do Azure para criar o active directory autônomo e o cluster do HDInsight.
- Opção 2: Todo o processo é dividido nas etapas a seguir:
    - Crie um Active Directory usando um modelo.
    - Configure LDAPS.
    - Crie usuários e grupos AD
    - Criar cluster HDInsight

> [!IMPORTANT]
> 
> O Oozie não está habilitado no HDInsight ingressado no domínio.

## <a name="prerequisite"></a>Pré-requisito
* Assinatura do Azure

## <a name="option-1-one-step-approach"></a>Opção 1: abordagem de uma etapa
Nesta seção, você pode abrir um modelo de gerenciamento de recursos do Azure no portal do Azure. O modelo é usado para criar um Active Directory autônomo e um cluster do HDInsight. No momento, você pode criar o cluster do Hadoop associado ao domínio, o cluster do Spark e o cluster de consulta interativa.

1. Clique na imagem a seguir para abrir o modelo no portal do Azure. O modelo está localizado em [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/).
   
    Para criar um cluster Spark:

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/http%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fdomain-joined%2Fspark%2Ftemplate.json" target="_blank"><img src="../hbase/media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Para criar um cluster de Consulta Interativa:

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/http%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fdomain-joined%2Finteractivequery%2Ftemplate.json" target="_blank"><img src="../hbase/media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Para criar um cluster Hadoop:

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/http%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fdomain-joined%2Fhadoop%2Ftemplate.json" target="_blank"><img src="../hbase/media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Insira os valores, selecione **Concordo com os termos e condições declarados acima**, selecione **Fixar no painel**e, em seguida, clique em **Comprar**. Focalize o cursor do mouse no sinal de explicação ao lado dos campos para ver as descrições. A maioria dos valores foram populados. Você pode usar os valores padrão ou seus próprios valores.

    - **Grupo de recursos**: Digite um nome de grupo de recursos do Azure.
    - **Local**: Escolha um local perto de você.
    - **Novo nome de conta de armazenamento**: Insira um nome de conta de Armazenamento do Microsoft Azure. Essa nova conta de armazenamento é usada pelo controlador de domínio primário, BDC e o cluster do HDInsight como a conta de armazenamento padrão.
    - **Nome de usuário administrador**: Insira o nome de usuário administrador do domínio.
    - **Senha do administrador**: Insira a senha do administrador do domínio.
    - **Nome de domínio**: O nome padrão é *contoso.com*.  Se você alterar o nome de domínio, você também deve atualizar o campo **Certificado de LDAP Seguro** e o campo **DN de Unidade Organizacional**.
    - **Prefixo DNS**: insira o prefixo DNS para o endereço IP público usado pelo Load Balancer.
    - **Nome do cluster**: Insira o nome do cluster do HDInsight.
    - **Tipo de cluster**: Não altere esse valor. Se você quiser alterar o tipo de cluster, use o modelo específico na última etapa.
    - **Proteger a Senha do Certificado LDAP**: use o valor padrão, a menos que você altere o campo do certificado LDAP Seguro.
    Alguns dos valores são embutidos no modelo, por exemplo, a contagem de instâncias de nós do operador é dois.  Para alterar os valores embutidos, clique em **Editar modelo**.

    ![Editar modelo do cluster do HDInsight associado ao domínio](./media/apache-domain-joined-configure/hdinsight-domain-joined-edit-template.png)

Depois que o modelo for concluído com êxito, há 23 recursos criados no grupo de recursos.

## <a name="option-2-multi-step-approach"></a>Opção 2: método de várias etapas

Há quatro etapas nesta seção:

1. Crie um Active Directory usando um modelo.
2. Configure LDAPS.
3. Crie usuários e grupos AD
4. Criar cluster HDInsight

### <a name="create-an-active-directory"></a>Criar um Active Directory

O modelo do Azure Resource Manager facilita a criação de recursos do Azure. Nesta seção, você deve usar um [Modelo de início rápido do Azure](https://azure.microsoft.com/resources/templates/active-directory-new-domain-ha-2-dc/) para criar uma nova floresta e domínio com duas máquinas virtuais. As duas máquinas virtuais funcionam como um controlador de domínio primário e um controlador de domínio de backup.

**Para criar um domínio com dois controladores de domínio**

1. Clique na imagem a seguir para abrir o modelo no portal do Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Factive-directory-new-domain-ha-2-dc%2Fazuredeploy.json" target="_blank"><img src="./media/apache-domain-joined-configure/deploy-to-azure.png" alt="Deploy to Azure"></a>

    O modelo é semelhante a:

    ![O domínio associado do HDInsight cria máquinas virtuais de domínio de floresta](./media/apache-domain-joined-configure/hdinsight-domain-joined-create-arm-template.png)

2. Insira os valores a seguir:

    - **Assinatura**: selecione uma assinatura do Azure.
    - **Nome do grupo de recursos**: Digite um nome de grupo de recursos.  Um grupo de recursos é usado para gerenciar os recursos do Azure que são relacionados a um projeto.
    - **Local**: Selecione um local do Azure perto de você.
    - **Nome de usuário administrador**: Este é o nome de usuário administrador do domínio. Este usuário não é a conta de usuário HTTP do seu cluster do HDInsight. Essa é a conta que você usa em todo o tutorial.
    - **Senha do administrador**: Insira a senha do administrador do domínio.
    - **Nome de domínio**: O nome de domínio deve ser um nome de duas partes. Por exemplo: contoso.com, ou contoso. local, ou hdinsight.test.
    - **Prefixo DNS**: Digite um prefixo de DNS
    - **Porta RDP do controlador de domínio primário**: (use o valor padrão para este tutorial)
    - **Porta RDP do BDC**: (use o valor padrão para este tutorial)
    - **Localização de artefatos**: (use o valor padrão para este tutorial)
    - **Token SAS da localização de artefatos**: (Deixe em branco para este tutorial).

Demora cerca de 20 minutos para criar os recursos.

### <a name="setup-ldaps"></a>Configurar LDAPS

O LDAP é usado para ler e gravar no AD.

**Para se conectar ao controlador de domínio primário usando a área de trabalho remota**

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Abra o grupo de recursos e, em seguida, abra a máquina controlador de domínio primário (PDC). O nome do controlador de domínio primário é adPDC. 
3. Clique em **Conectar** para se conectar ao controlador de domínio primário usando a área de trabalho remota.

    ![Domínio associado do HDInsight se conecta à área de trabalho retoma do controlador de domínio primário](./media/apache-domain-joined-configure/hdinsight-domain-joined-remote-desktop-pdc.png)


**Para adicionar os Serviços de Certificados do Active Directory**

4. Abra o **Gerenciador do Servidor** se ainda não estiver aberto.
5. Clique em **Gerenciar** e, em seguida, clique em **Adicionar funções e recursos**.

    ![O domínio associado do HDInsight adiciona funções e recursos](./media/apache-domain-joined-configure/hdinsight-domain-joined-add-roles.png)
5. Em “Antes de começar”, clique em **Avançar**.
6. Selecione **Instalação baseada em função ou recurso** e clique em **Próximo**.
7. Selecione um controlador de domínio primário e clique em **Avançar**.  O nome do controlador de domínio primário é adPDC.
8. Selecione **Serviços de Certificados do Active Directory**.
9. Clique em **Adicionar recursos** na caixa de diálogo pop-up.
10. Siga o assistente, use as configurações padrão para o restante do procedimento.
11. Clique em **Fechar** para fechar o assistente.

**Para configurar o certificado AD**

1. No Gerenciador do Servidor, clique no ícone de notificação amarelo e, em seguida, clique em **Configurar serviços de certificado do Active Directory**.

    ![Domínio associado do HDInsight configura o certificado AD](./media/apache-domain-joined-configure/hdinsight-domain-joined-configure-ad-certificate.png)

2. Clique em **Serviços de função** à esquerda, selecione **Autoridade de certificação** e, em seguida, clique em **Avançar**.
3. Siga o assistente, use as configurações padrão para o restante do procedimento (clique em **Configurar** na última etapa).
4. Clique em **Fechar** para fechar o assistente.

### <a name="optional-create-ad-users-and-groups"></a>(Opcional) Crie usuários e grupos AD

**Para criar usuários e grupos no AD**
1. Conecte-se ao controlador de domínio primário usando a área de trabalho remota
1. Abra **Computadores e Usuários do Active Directory**.
2. Selecione o seu nome de domínio no painel esquerdo.
3. Clique no ícone **Criar um novo usuário no contêiner atual** no menu superior.

    ![Domínio associado do HDInsight cria usuários](./media/apache-domain-joined-configure/hdinsight-domain-joined-create-ad-user.png)
4. Siga as instruções para criar alguns usuários. Por exemplo, hiveuser1 e hiveuser2.
5. Clique no ícone **Criar um novo grupo no contêiner atual** no menu superior.
6. Siga as instruções para criar um grupo chamado **HDInsightUsers**.  Esse grupo será usado quando você criar um cluster do HDInsight mais tarde neste tutorial.

> [!IMPORTANT]
> Você deve reinicializar a máquina virtual do controlador de domínio primário antes de criar um cluster do HDInsight associado a um domínio.

### <a name="create-an-hdinsight-cluster-in-the-vnet"></a>Crie um cluster do HDInsight no VNet

Nesta seção, você pode usar o portal do Azure para adicionar um cluster do HDInsight na rede virtual que você criou usando o modelo do Gerenciador de Recursos anteriormente no tutorial. Este artigo aborda apenas as informações específicas de configuração do cluster associado ao domínio.  Para informações gerais, consulte [Criar clusters baseados em Linux no HDInsight usando o portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md).  

**Para criar um cluster HDInsight associado a um domínio**

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Abra o grupo de recursos que você criou usando o Modelo do Gerenciador de Recursos anteriormente no tutorial.
3. Adicione um cluster do HDInsight no grupo de recursos.
4. Selecione a opção **Personalizar**:

    ![Opção de criar personalização do domínio associado do HDInsight](./media/apache-domain-joined-configure/hdinsight-domain-joined-portal-custom-configuration-option.png)

    Há seis seções usando a opção de configuração personalizada: Básica, Armazenamento, Aplicativo, Tamanho de Cluster, Configurações Avançadas e Resumo.
5. Na seção **Básico**:

    - Tipo de cluster: Selecione **Pacote de segurança de Enterprise**. Atualmente, o pacote de segurança de Enterprise só pode ser habilitado para os seguintes tipos de cluster: Hadoop, Consulta Interativa e Spark.

        ![Pacote de segurança de Enterprise associada ao domínio do HDInsight](./media/apache-domain-joined-configure/hdinsight-creation-enterprise-security-package.png)
    - Nome de Usuário de Logon do Cluster: Este é o usuário do HTTP do Hadoop. Essa conta é diferente da conta de administrador do domínio.
    - Grupo de recursos: Selecione o grupo de recursos que você criou anteriormente usando o modelo do Gerenciador de Recursos.
    - Local: O local deve ser o mesmo que o usado quando você criou a vnet e os DCs usando o modelo do Gerenciador de Recursos.

6. Na seção **Configurações Avançadas** :

    - Configurações de domínio:

        ![Domínio de configurações avançadas de domínio associado do HDInsight](./media/apache-domain-joined-configure/hdinsight-domain-joined-portal-advanced-domain-settings.png)
        
        - Nome de domínio: Insira o nome de domínio usado em [Criar um Active Directory](#create-an-active-directory).
        - Nome de usuário de domínio: Insira o nome de usuário de administrador do AD usado em [Criar um Active Directory](#create-an-active-directory).
        - Unidade organizacional: Consulte a captura de tela para obter um exemplo.
        - URL de LDAPS: Consulte a captura de tela para obter um exemplo
        - Grupo de usuários de acesso: insira o nome do grupo de usuários criados em [Criar usuários e grupos do AD](#optionally-createad-users-and-groups)
    - Rede virtual: Selecione a rede virtual que você criou em [Criar um Active Directory](#create-an-active-directory). O nome padrão usado no modelo é **adVNET**.
    - Subnet: O nome padrão usado no modelo é **adSubnet**.



Depois de concluir o tutorial, talvez você queira excluir o cluster. Com o HDInsight, seus dados são armazenados no Armazenamento do Azure, assim você poderá excluir, com segurança, um cluster quando ele não estiver em uso. Você também é cobrado por um cluster HDInsight, mesmo quando ele não está em uso. Como os encargos para o cluster são muitas vezes maiores do que os encargos para armazenamento, faz sentido, do ponto de vista econômico, excluir os clusters quando não estiverem em uso. Para obter instruções sobre como excluir um cluster, confira [Gerenciar clusters Hadoop no HDInsight usando o Portal do Azure](../hdinsight-administer-use-management-portal.md#delete-clusters).

## <a name="next-steps"></a>Próximas etapas
* Para configurar políticas do Hive e executar consultas do Hive, confira [Configurar políticas do Hive para clusters HDInsight associados ao domínio](apache-domain-joined-run-hive.md).
* Para usar SSH para conectar-se a clusters do HDInsight Ingressados no Domínio, confira [Usar SSH com Hadoop baseado em Linux no HDInsight do Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

