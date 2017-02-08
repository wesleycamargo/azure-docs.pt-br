---
title: Saiba como usar o Data Lake Analytics e o U-SQL usando os tutoriais interativos do portal do Azure | Microsoft Docs
description: "Início rápido para aprender a usar a Análise Data Lake e o U-SQL. "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 31399d47-e937-4c43-ab0a-6aea8875378d
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: 194b5d79505afbfd0208f63dd182a0e03227ba69
ms.openlocfilehash: 36677be6bc5599f55f1f15bc145c59033ad20e0a


---
# <a name="use-azure-data-lake-analytics-interactive-tutorials"></a>Usar tutoriais interativos da Análise Azure Data Lake
O portal do Azure fornece um tutorial interativo para você começar a usar o Data Lake Analytics. Este artigo mostra como acompanhar o tutorial para analisar logs de site.

> [!NOTE]
> Se desejar acompanhar o mesmo tutorial usando o Visual Studio, veja [Analisar logs de site usando a Análise Data Lake](data-lake-analytics-analyze-weblogs.md).
> Mais tutoriais interativos serão adicionados ao portal.
> 
> 

Para ver outros tutoriais, confira:

* [Introdução à Análise do Data Lake usando o portal do Azure](data-lake-analytics-get-started-portal.md)
* [Introdução à Análise Data Lake usando o Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Introdução à Análise Data Lake usando o SDK do .NET](data-lake-analytics-get-started-net-sdk.md)
* [Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md) 

**Pré-requisitos**

Antes de começar este tutorial, você deve ter o seguinte:

* **Uma conta da Análise Data Lake**.  Confira [Introdução ao Azure Data Lake Analytics usando o portal do Azure](data-lake-analytics-get-started-portal.md).

## <a name="create-data-lake-analytics-account"></a>Criar conta da Análise Data Lake
Você deve ter uma conta da Análise Data Lake antes de executar trabalhos.

Cada conta do Data Lake Analytics tem uma dependência da conta do [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md), a conta padrão do Data Lake Store.  Neste tutorial, você criará a conta padrão do Data Lake Store com uma conta do Analytics, mas você pode criá-la com antecedência também.

**Para criar uma conta da Análise Data Lake**

1. Entre no [portal do Azure](https://portal.azure.com/signin/index/?Microsoft_Azure_Kona=true&Microsoft_Azure_DataLake=true&hubsExtension_ItemHideKey=AzureDataLake_BigStorage%2cAzureKona_BigCompute).
2. Clique em **Microsoft Azure** no canto superior esquerdo para abrir o quadro inicial.
3. Clique no bloco **Marketplace** .  
4. Digite **Azure Data Lake Analytics** na caixa de pesquisa na folha **Tudo** e pressione a tecla **ENTER**. Você deverá ver **Análise Azure Data Lake** na lista.
5. Clique em **Análise Azure Data Lake** na lista.
6. Clique em **Criar** na parte inferior da folha.
7. Digite ou selecione:
   
    ![Folha do portal da Análise Azure Data Lake](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-create-adla.png)
   
   * **Nome**: nomeie a conta da Análise.
   * **Repositório Data Lake**: cada conta da Análise Data Lake tem uma conta dependente do Repositório Data Lake. A conta da Análise Data Lake e a conta dependente do Repositório Data Lake devem ser colocadas no mesmo data center do Azure. Siga as instruções para criar uma conta do Data Lake Store ou selecione uma existente.
   * **Assinatura**: escolha a assinatura do Azure usada para a conta da Análise.
   * **Grupo de Recursos**. Selecione um Grupo de Recursos do Azure existente ou crie um novo. Os aplicativos normalmente são compostos por vários componentes, como, por exemplo, um aplicativo Web, banco de dados, servidor de banco de dados, armazenamento e serviços de terceiros. O Gerenciador de Recursos do Azure (ARM) permite trabalhar com os recursos do seu aplicativo como um grupo, designado um Grupo de Recursos do Azure. Você pode implantar, atualizar, monitorar ou excluir os recursos do seu aplicativo com uma única operação coordenada. Usar um modelo para a implantação e esse modelo pode ser útil para ambientes diferentes, como teste, preparação e produção. Você pode esclarecer a cobrança para sua organização exibindo os custos acumulados para todo o grupo. Para saber mais, consulte [Visão geral do Gerenciador de Recursos do Azure](../azure-resource-manager/resource-group-overview.md). 
   * **Local**. Selecione um datacenter do Azure para a conta da Análise Data Lake. 
8. Selecione **Fixar no quadro inicial**. Isso é necessário para acompanhar este tutorial.
9. Clique em **Criar**. Ele leva você para o quadro inicial do portal. Um novo bloco é adicionado à Home page com o rótulo mostrando “Implantação do Azure Data Lake Analytics”. A criação de uma conta da Análise Data Lake demora alguns minutos. Após a criação da conta, o portal abrirá a conta em uma nova folha.
   
    ![Folha do portal da Análise Azure Data Lake](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-blade.png)

## <a name="run-website-log-analysis-interactive-tutorial"></a>Executar o tutorial interativo da Análise de log de site
**Para abrir o tutorial interativo da Análise de log de site**

1. No Portal, clique em **Microsoft Azure** no menu à esquerda para abrir o quadro inicial.
2. Clique no bloco que está vinculado à sua conta da Análise Data Lake.
3. Clique em **Explorar tutoriais interativos** na barra **Noções Básicas**.
   
    ![Tutoriais interativos da Análise Data Lake](./media/data-lake-analytics-use-interactive-tutorials/data-lake-analytics-explore-interactive-tutorials.png)
4. Se você receber um aviso laranja informando “Exemplos não configurados, clique em...”, clique em **Copiar Dados de Exemplo** para copiar os dados de exemplo na conta padrão do Repositório Data Lake. O tutorial interativo precisa de dados para ser executado.
5. Na folha **Tutoriais Interativos**, clique em **Log Analytics do Site**. O portal abre o tutorial em uma nova folha do portal.
6. Clique em **Introdução** e siga as instruções

## <a name="see-also"></a>Consulte também
* [Visão geral da Análise do Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* [Introdução à Análise do Data Lake usando o portal do Azure](data-lake-analytics-get-started-portal.md)
* [Introdução à Análise Data Lake usando o Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Analisar logs de site usando a Análise Azure Data Lake](data-lake-analytics-analyze-weblogs.md)




<!--HONumber=Dec16_HO2-->


