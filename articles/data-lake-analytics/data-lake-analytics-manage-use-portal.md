---
title: Gerenciar o Azure Data Lake Analytics usando o portal do Azure | Microsoft Docs
description: "Saiba como gerenciar contas, fontes de dados, usuários e trabalhos da Análise Data Lake."
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: a0e045f1-73d6-427f-868d-7b55c10f811b
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/06/2016
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 289f6e91458a1a4799941ccea46f7ee6d1a296c4


---
# <a name="manage-azure-data-lake-analytics-using-azure-portal"></a>Gerenciar o Azure Data Lake Analytics usando o portal do Azure
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Saiba como gerenciar contas, fontes de dados da conta, usuários e trabalhos do Azure Data Lake Analytics usando o portal do Azure. Para ver os tópicos de gerenciamento usando outras ferramentas, clique no seletor no alto da página.

**Pré-requisitos**

Antes de começar este tutorial, você deve ter os seguintes itens:

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-accounts"></a>Gerenciar contas
Antes de executar qualquer trabalho da Análise Data Lake, você deve ter uma conta da Análise Data Lake. Ao contrário do Azure HDInsight, você paga apenas para uma conta do Data Lake Analytics ao executar um trabalho.  Você paga apenas pelo tempo em que um trabalho é executado.  Para saber mais, consulte [Visão geral sobre a Análise Azure Data Lake](data-lake-analytics-overview.md).  

**Para criar uma conta da Análise Data Lake**

1. Entre no [portal do Azure](https://portal.azure.com).
2. Clique em **Novo**, **Inteligência + Análises**, em seguida, clique em **Data Lake Analytics**.
3. Digite ou selecione os valores a seguir:
   
    ![Folha do portal da Análise Azure Data Lake](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-create-adla.png)
   
   * **Nome**: o nome da conta do Data Lake Analytics.
   * **Assinatura**: escolha a assinatura do Azure usada para a conta da Análise.
   * **Grupo de Recursos**. Selecione um Grupo de Recursos do Azure existente ou crie um novo. O Gerenciador de Recursos do Azure permite trabalhar com os recursos do seu aplicativo como um grupo. Para saber mais, consulte [Visão geral do Gerenciador de Recursos do Azure](../azure-resource-manager/resource-group-overview.md). 
   * **Local**. Selecione um datacenter do Azure para a conta da Análise Data Lake. 
   * **Repositório Data Lake**: cada conta da Análise Data Lake tem uma conta dependente do Repositório Data Lake. A conta da Análise Data Lake e a conta dependente do Repositório Data Lake devem ser colocadas no mesmo data center do Azure. Siga as instruções para criar uma nova conta do Repositório Data Lake ou selecione uma existente.
4. Clique em **Criar**. Isso leva você até a página inicial do portal. Um novo bloco é adicionado ao Quadro inicial com o rótulo mostrando “Implantando a Análise Data Lake do Azure”. A criação de uma conta da Análise Data Lake demora alguns minutos. Após a criação da conta, o portal abrirá a conta em uma nova folha.

Após a criação de uma conta da Análise Data Lake, é possível adicionar outras contas do Repositório Data Lake e contas de Armazenamento do Azure. Para obter instruções, consulte [Gerenciar as fontes de dados da conta da Análise Data Lake](data-lake-analytics-manage-use-portal.md#manage-account-data-sources).

<a name="access-adla-account"></a> **Para acessar/abrir uma conta da Análise Data Lake**

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Clique em **Data Lake Analytics** no menu à esquerda.  Se você não o vir, clique em **Mais serviços** e clique em **Data Lake Analytics** em **Inteligência + Análise**.
3. Clique na conta da Análise Data Lake que você deseja acessar. A conta será aberta em uma nova folha.

**Para excluir uma conta da Análise Data Lake**

1. Abra a conta da Análise Data Lake que você deseja excluir. Para obter instruções, consulte [Acessar contas do Data Lake Analytics](#access-adla-account).
2. Clique em **Excluir** no menu de botões na parte superior da folha.
3. Digite o nome da conta e clique em **Excluir**.

A exclusão de uma conta do Data Lake Analytics não excluirá as contas dependentes do Data Lake Store. Para obter instruções sobre como excluir contas do Repositório Data Lake, consulte [Excluir conta do Repositório Data Lake](../data-lake-store/data-lake-store-get-started-portal.md#delete-azure-data-lake-store-account).

<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-account-data-sources"></a>Gerenciar as fontes de dados da conta
No momento, a Análise Data Lake dá suporte às seguintes fontes de dados:

* [Repositório Azure Data Lake](../data-lake-store/data-lake-store-overview.md)
* [Armazenamento do Azure](../storage/storage-introduction.md)

Quando você cria uma conta da Análise Data Lake, é necessário indicar uma conta do Repositório Azure Data Lake como a conta de armazenamento padrão. A conta padrão do Repositório Data Lake é usada para armazenar metadados de trabalho e logs de auditoria de trabalho. Depois de criar uma conta da Análise Data Lake, é possível adicionar outras contas do Repositório Data Lake e/ou uma conta de Armazenamento do Azure. 

<a name="default-adl-account"></a>**Para encontrar a conta de armazenamento padrão do Data Lake**

* Abra a conta da Análise Data Lake que você deseja gerenciar. Para obter instruções, consulte [Acessar contas do Data Lake Analytics](#access-adla-account). O armazenamento padrão do Data Lake é mostrado em **Essencial**:
  
    ![O Azure Data Lake Analytics adiciona fonte de dados](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-default-adl-storage-account.png)

**Para adicionar outras fontes de dados**

1. Abra a conta da Análise Data Lake que você deseja gerenciar. Para obter instruções, consulte [Acessar contas do Data Lake Analytics](#access-adla-account).
2. Clique em **Configurações** e em **Fontes de Dados**. Você deverá ver a conta padrão do Repositório Data Lake na lista. 
3. Clique em **Adicionar Fonte de Dados**.
   
    ![Análise Data Lake do Azure - adicionar fonte de dados](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-add-data-source.png)
   
    Para adicionar uma conta do Azure Data Lake Store, você precisará do nome da conta e do acesso à conta para poder consultá-la.
    Para adicionar um armazenamento de Blobs do Azure, você precisará da conta de armazenamento e da chave da conta, que pode ser encontrada navegando até a conta de armazenamento no Portal.

<a name="explore-data-sources"></a>**Para explorar fontes de dados**    

1. Abra a conta da Análise que você deseja gerenciar. Para obter instruções, consulte [Acessar contas do Data Lake Analytics](#access-adla-account).
2. Clique em **Configurações** e em **Gerenciador de Dados**. 
   
    ![Análise Data Lake do Azure - gerenciador de dados](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-data-explorer.png)
3. Clique em uma conta do Data Lake Store para abri-la.
   
    ![Análise Data Lake do Azure - conta de armazenamento do gerenciador de dados](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-explore-adls.png)
   
    Para cada conta do Repositório Data Lake, você pode:
   
   * **Nova Pasta**: adicionar uma nova pasta.
   * **Carregar**: carregar arquivos na conta de Armazenamento de sua estação de trabalho.
   * **Acesso**: configurar as permissões de acesso.
   * **Renomear Pasta**: renomear uma pasta.
   * **Propriedades da pasta**: mostrar as propriedades de arquivos ou pastas, como o caminho WASB, o caminho WEBHDFS, a hora da última modificação e assim por diante.
   * **Excluir Pasta**: excluir uma pasta.

<a name="upload-data-to-adls"></a> **Para carregar arquivos na conta do Repositório Data Lake**

1. No Portal, clique em **Procurar** no menu à esquerda e clique em **Data Lake Store**.
2. Clique na conta do Repositório Data Lake na qual você deseja carregar dados. Para encontrar a conta de armazenamento padrão do Data Lake, clique [aqui](#default-adl-account).
3. Clique em **Gerenciador de Dados** no menu superior.
4. Clique em **Novo Diretório** para criar uma nova pasta ou clique no nome de uma pasta para alterar a pasta.
5. Clique em **Carregar** no menu superior para carregar o arquivo.

<a name="upload-data-to-wasb"></a> **Para carregar arquivos na conta de Armazenamento de Blob do Azure**

Consulte [Carregar dados em trabalhos do Hadoop no HDInsight](../hdinsight/hdinsight-upload-data.md).  As informações se aplicam à Análise Data Lake.

## <a name="manage-users"></a>Gerenciar usuários
A Análise Data Lake usa o controle de acesso com base em funções com o Active Directory do Azure. Quando você cria uma conta do Data Lake Analytics, uma função "Administradores de Assinatura" é adicionada à conta. Você pode adicionar outros usuários e grupos de segurança com as seguintes funções:

| Função | Descrição |
| --- | --- |
| Proprietário |Permite que você gerencie tudo, incluindo o acesso aos recursos. |
| Colaborador |Acessar o portal; enviar e monitorar trabalhos. Para poder enviar trabalhos, um colaborador precisa de permissão de leitura ou de gravação nas contas do Data Lake Store. |
| DataLakeAnalyticsDeveloper |Enviar, monitorar e cancelar trabalhos.  Esses usuários só podem cancelar seus próprios trabalhos. Ele não pode gerenciar sua própria conta, por exemplo, adicionar usuários, alterar permissões ou excluir a conta. Para poder executar trabalhos, o usuário precisa de acesso de leitura ou de gravação nas contas do Repositório Data Lake |
| Leitor |Permite que você veja tudo, mas não faça alterações. |
| Usuário do DevTest Labs |Permite que você veja tudo e se conecte, inicie, reinicie e desligue máquinas virtuais. |
| Administrador de Acesso do Usuário |Permite que você gerencie o acesso do usuário aos recursos do Azure. |

Para saber mais sobre como criar usuários e grupos de segurança do Active Directory do Azure, consulte [O que é o Active Directory do Azure](../active-directory/active-directory-whatis.md).

**Para adicionar usuários ou grupos de segurança a uma conta da Data Lake Analytics**

1. Abra a conta da Análise que você deseja gerenciar. Para obter instruções, consulte [Acessar contas do Data Lake Analytics](#access-adla-account).
2. Clique em **Configurações** e em **Usuários**. Você também pode clicar em **Acesso** na barra de títulos **Fundamentos**, como mostra a captura de tela abaixo:
   
    ![Análise Data Lake do Azure - adicionar usuários à conta](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-access-button.png)
3. Na folha **Usuário**, clique em **Adicionar**.
4. Selecione uma função e adicione um usuário, em seguida clique em **OK**.

**Observação: se esse usuário ou grupo de segurança precisar enviar trabalhos, será necessário conceder a permissão no Data Lake Store. Para obter mais informações, consulte [Proteger dados armazenados no Data Lake Store](../data-lake-store/data-lake-store-secure-data.md).**

<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-jobs"></a>Gerenciar trabalhos
Você deve ter uma conta do Data Lake Analytics antes de executar trabalhos de U-SQL.  Para saber mais, consulte [Gerenciar contas da Análise Data Lake](#manage-data-lake-analytics-accounts).

<a name="create-job"></a>**Para criar um trabalho**

1. Abra a conta da Análise que você deseja gerenciar. Para obter instruções, consulte [Acessar contas do Data Lake Analytics](#access-adla-account).
2. Clique em **Novo Trabalho**.
   
    ![criar trabalhos de U-SQL da Análise Data Lake do Azure](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-create-job-button.png)
   
    Você verá uma nova folha semelhante a:
   
    ![criar trabalhos de U-SQL da Análise Data Lake do Azure](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-new-job.png)
   
    Para cada trabalho, você pode configurar

    |Nome|Descrição|
    |----|-----------|
    |Nome do Trabalho|Digite o nome do trabalho.|
    |Prioridade|O número mais baixo recebe a prioridade mais alta. Se dois trabalhos estiverem enfileirados, aquele com o número de prioridade mais baixo será executado primeiro|
    |Paralelismo |Número máximo de processos de computação que podem ocorrer ao mesmo tempo. O aumento desse número pode melhorar o desempenho, mas também pode aumentar o custo.|
    |Script|Insira o script U-SQL para o trabalho.|

    Usando a mesma interface, você também pode explorar os links de fontes de dados e adicionar outros arquivos às fontes de dados vinculadas. 
1. Clique em **Enviar Trabalho** se quiser enviar o trabalho.

**Para enviar um trabalho**

Consulte [Criar trabalhos da Análise Data Lake](#create-job)

<a name="monitor-jobs"></a>**Para monitorar trabalhos**

1. Abra a conta da Análise que você deseja gerenciar. Para obter instruções, consulte [Acessar contas do Data Lake Analytics](#access-adla-account). O painel Gerenciamento de Trabalho mostra as informações básicas do trabalho:
   
    ![gerenciar trabalhos de U-SQL da Análise Data Lake do Azure](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-manage-jobs.png)
2. Clique em **Gerenciamento de Trabalho** conforme mostra a captura de tela anterior.
   
    ![gerenciar trabalhos de U-SQL da Análise Data Lake do Azure](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-manage-jobs-details.png)
3. Clique em um trabalho nas listas. Ou clique em **Filtro** para obter ajuda com a localização dos trabalhos:
   
    ![filtrar trabalhos de U-SQL da Análise Data Lake do Azure](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-filter-jobs.png)
   
    Você pode filtrar trabalhos por **Intervalo de Tempo**, **Nome do Trabalho** e **Autor**.
4. Clique em **Reenviar** se você quiser reenviar o trabalho.

**Para reenviar um trabalho**

Consulte [Monitorar trabalhos de Análise Data Lake](#monitor-jobs).

## <a name="monitor-account-usage"></a>Monitorar o uso da conta
**Para monitorar o uso da conta**

1. Abra a conta da Análise que você deseja gerenciar. Para obter instruções, consulte [Acessar contas do Data Lake Analytics](#access-adla-account). O painel Uso mostra a utilização:
   
    ![monitorar o uso da Análise Data Lake do Azure](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-monitor-usage.png)
2. Clique duas vezes no painel para ver mais detalhes.

## <a name="view-u-sql-catalog"></a>Exibir o catálogo do U-SQL
O [catálogo do U-SQL](data-lake-analytics-use-u-sql-catalog.md) é usado para estruturar dados e código de modo que possam ser compartilhados por scripts U-SQL. O catálogo possibilita o melhor desempenho possível com dados no Azure Data Lake. No portal do Azure, você poderá exibir o catálogo do U-SQL.

**Para navegar pelo catálogo de U-SQL**

1. Abra a conta da Análise que você deseja gerenciar. Para obter instruções, consulte [Acessar contas do Data Lake Analytics](#access-adla-account).
2. Clique em **Gerenciador de Dados** no menu superior.
3. Expanda **Catálogo**, expanda **mestre**, expanda **Tabelas, ou** Funções com Valor de Tabela** ou **Assemblies**. A captura de tela a seguir mostra uma função com valor de tabela.
   
    ![Análise Data Lake do Azure - conta de armazenamento do gerenciador de dados](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-explore-catalog.png)

<!-- ################################ -->
<!-- ################################ -->
## <a name="use-azure-resource-manager-groups"></a>Usar os grupos do Gerenciador de Recursos do Azure
Aplicativos normalmente são compostos por vários componentes, como, por exemplo, um aplicativo Web, banco de dados, servidor de banco de dados, armazenamento e serviços de terceiros. O Azure Resource Manager permite trabalhar com os recursos do seu aplicativo como um grupo, designado um Grupo de Recursos do Azure. Você pode implantar, atualizar, monitorar ou excluir todos os recursos do seu aplicativo com uma única operação coordenada. Usar um modelo para a implantação e esse modelo pode ser útil para ambientes diferentes, como teste, preparação e produção. Você pode esclarecer a cobrança para sua organização exibindo os custos acumulados para todo o grupo. Para saber mais, consulte [Visão geral do Gerenciador de Recursos do Azure](../azure-resource-manager/resource-group-overview.md). 

Um serviço de Análise Data Lake pode incluir os seguintes componentes:

* Conta da Análise Data Lake do Azure
* Conta padrão do Repositório Azure Data Lake obrigatória
* Outras contas do Armazenamento Azure Data Lake
* Contas do Armazenamento do Azure adicionais

Você pode criar todos esses componentes em um grupo Gerenciamento de Recursos para torná-los mais fáceis de serem gerenciados.

![Conta e armazenamento da Análise Azure Data Lake](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Uma conta da Análise Data Lake e as contas de armazenamento dependentes devem ser colocadas no mesmo data center do Azure.
No entanto, o grupo Gerenciamento de Recursos pode estar localizado em um data center diferente.  

## <a name="see-also"></a>Consulte também
* [Visão geral da Análise do Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* [Introdução à Análise do Data Lake usando o portal do Azure](data-lake-analytics-get-started-portal.md)
* [Gerenciar a Análise Azure Data Lake usando o Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Monitorar e solucionar problemas em trabalhos do Azure Data Lake Analytics usando o portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)




<!--HONumber=Nov16_HO3-->


