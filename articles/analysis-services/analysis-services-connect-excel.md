---
title: Conectar-se ao Azure Analysis Services com Excel | Microsoft Docs
description: Saiba como se conectar a um servidor Azure Analysis Services usando Excel.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: 7597792a525583497ec6a400e668eda2adc9a93e
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/02/2017
---
# <a name="connect-with-excel"></a>Conectar com o Excel

Depois de criar um servidor no Azure e implantar um modelo tabular, você estará pronto para se conectar e começar a explorar os dados.


## <a name="connect-in-excel"></a>Conectar-se no Excel

Há suporte para a conexão com um servidor no Excel usando Obter Dados no Excel 2016. Não há suporte para a conexão usando o Assistente para Importar tabela no Power Pivot. 

**Para se conectar no Excel 2016**

1. No Excel 2016, na faixa de opções **Dados**, clique em **Obter Dados Externos** > **De Outras Fontes** > **Do Analysis Services**.

2. No Assistente de Conexão de Dados, em **Nome do servidor**, insira o nome do servidor, incluindo protocolo e URI. Em seguida, em **Credenciais de logon**, selecione **Usar o seguinte Nome de Usuário e Senha** e digite o nome de usuário da organização, por exemplo nancy@adventureworks.com, e a senha.

    ![Conectar a partir do logon do Excel](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. Em **Selecionar Banco de Dados e Tabela**, selecione o banco de dados e um modelo ou perspectiva e, em seguida, clique em **Concluir**.
   
    ![Conectar a partir do modelo de seleção do Excel](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>Consulte também
[Bibliotecas do cliente](analysis-services-data-providers.md)   
[Gerenciar seu serviço](analysis-services-manage.md)     


