---
title: Implantar no Azure Analysis Services | Microsoft Docs
description: Saiba como implantar um modelo de tabela em um servidor do Azure Analysis Services.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 5f1f0ae7-11de-4923-a3da-888b13a3638c
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/28/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 193c939065979dc48243d31e7f97cd87d96bf9a8
ms.openlocfilehash: 18d64f1ad4ef0dd41ae9302d08e02e94d1c608f5


---
# <a name="deploy-to-azure-analysis-services"></a>Implantar no Azure Analysis Services
Depois de criar um servidor em sua assinatura do Azure, você estará pronto para implantar nele um banco de dados de modelo de tabela. Você pode usar o SSDT (SQL Server Data Tools) para criar e implantar um projeto de modelo de tabela no qual você está trabalhando. Ou, você pode usar o SSMS (SQL Server Management Studio) para implantar um banco de dados do modelo de tabela existente a partir de uma instância do Analysis Services.

## <a name="before-you-begin"></a>Antes de começar
Para começar, você precisa do seguinte:

* **Servidor do Analysis Services** no Azure. Para saber mais, consulte [Criar um Analysis Services no Azure](analysis-services-create-server.md).
* **Projeto de modelo de tabela** no SSDT ou em um modelo de tabela existente no nível de compatibilidade 1200 em uma instância do Analysis Services. Nunca criou um? Experimente o [Tutorial da Adventure Works](https://msdn.microsoft.com/library/hh231691.aspx).
* **Gateway local** - se uma ou mais fontes de dados estiverem no local na rede de sua organização, você precisará instalar um [Gateway de dados local](analysis-services-gateway.md). O gateway é necessário para que seu servidor na nuvem conecte-se às suas fontes de dados locais, a fim de processar e atualizar os dados no modelo.

## <a name="to-deploy-a-tabular-model-from-ssdt"></a>Para implantar um modelo de tabela do SSDT
Para implantar no SSDT, verifique se você está usando a [versão mais recente](https://msdn.microsoft.com/library/mt204009.aspx), atualizada em 30 de setembro de 2016 ou depois.

> [!TIP]
> Antes de implantar, verifique se que você pode processar os dados nas tabelas. No SSDT, clique em **Modelo** > **Processo** > **Processar Tudo**. Se o processamento falhar, a implantação também falhará.
> 
> 

1. Antes de implantar, você precisa obter o nome do servidor. No **Portal do Azure** > servidor > **Visão geral** > **Nome do servidor**, copie o nome do servidor.
   
    ![Obter o nome do servidor no Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. No SSDT > **Gerenciador de Solução**, clique com o botão direito do mouse no projeto > **Propriedades**. Em seguida, em **Implantação** > **Servidor**, cole o nome do servidor.   
   
    ![Colar o nome do servidor na propriedade do servidor de implantação](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
3. No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Propriedades** e clique em **Implantar**. Talvez você receba uma solicitação para entrar no Azure.
   
    ![Implantar no servidor](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    O status da implantação aparecerá na janela Saída e em Implantar.
   
    ![Status da Implantação](./media/analysis-services-deploy/aas-deploy-status.png)

Isso é tudo!

## <a name="to-deploy-using-xmla-script"></a>Para implantar usando o script XMLA
1. No SSMS, clique com o botão direito no banco de dados de modelo de tabela que você quer implantar, clique em **Script** > **Gerar Script de Banco de Dados como** > **CRIAR para** e escolha um local.
2. Execute a consulta na instância do servidor no qual você quer implantar. Se você estiver implantando no mesmo servidor, altere pelo menos a propriedade **name** no script XMLA.  

## <a name="but-something-went-wrong"></a>Mas algo deu errado
Se a implantação falhar durante a implantação dos metadados, provavelmente o SSDT não conseguiu se conectar ao servidor. Verifique se você consegue se conectar ao servidor usando o SSMS. Em seguida, verifique se a propriedade do Servidor de Implantação do projeto está correta.

Se a implantação falhar em uma tabela, provavelmente o servidor não conseguiu se conectar a uma fonte de dados. Se a sua fonte de dados for local, na rede da sua organização, instale um [Gateway de dados local](analysis-services-gateway.md).

## <a name="next-steps"></a>Próximas etapas
Agora que seu modelo de tabela foi implantado em seu servidor, você está pronto para se conectar a ele. Você pode [conectar-se a ele com SSMS](analysis-services-manage.md) para gerenciá-lo. E, você pode [conectar-se a ele usando uma ferramenta de cliente](analysis-services-connect.md) como o Power BI, o Power BI Desktop ou o Excel, e começar a criar relatórios.




<!--HONumber=Nov16_HO3-->


