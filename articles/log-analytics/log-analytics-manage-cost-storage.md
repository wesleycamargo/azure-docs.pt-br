---
title: "Gerenciar o custo da retenção de dados no Azure Log Analytics | Microsoft Docs"
description: "Saiba como alterar o plano de preços e a política de retenção de dados para seu espaço de trabalho do Log Analytics no Portal do Azure."
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 5143abdde715424a41a53bb661db342acf817e0c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="manage-cost-of-data-retention-with-your-log-analytics-workspace"></a>Gerenciar o custo da retenção de dados no seu espaço de trabalho do Log Analytics
Quando você se inscreve no Log Analytics, dependendo do plano selecionado, há um limite de quanto tempo os dados gerados pelas suas fontes conectadas são armazenados no espaço de trabalho.  Este artigo destaca as considerações que podem influenciar os custos de reter esses dados por diferentes períodos e como configurar esse limite.   

Como o Log Analytics pode consumir grandes quantidades de dados de fontes locais, nuvem e ambientes híbridos, o custo de armazenar dados por certo período pode ser considerável dependendo dos seguintes fatores:

* Número de sistemas, componentes de infraestrutura, recursos de nuvem, etc. dos quais você coleta
* Tipo de dados criados pela origem, como filas de mensagens, logs, eventos, dados relacionados à segurança ou métricas de desempenho
* Volume de dados gerados por essas fontes 
* Número da solução de gerenciamento habilitada, fonte de dados e a frequência de coleta

> [!NOTE]
> Consulte a documentação de cada solução, pois ela fornece uma estimativa da quantidade de dados coletados.   

Se você estiver usando o plano *Gratuito*, a retenção dos dados será limitada a sete dias.  Para a camada *Autônoma* ou *Paga*, os dados coletados nos últimos 31 dias estão disponíveis.  

Ao usar o plano *Gratuito*, se você excede consistentemente os valores permitidos, é possível mudar seu espaço de trabalho para um plano pago para coletar dados além desse limite. 

> [!NOTE]
> Encargos se aplicam se você optar por selecionar um período de retenção mais longo para a camada paga. Você pode alterar seu tipo de plano a qualquer momento e, para obter mais informações sobre preços, consulte [detalhes de preços](https://azure.microsoft.com/pricing/details/log-analytics/). 

## <a name="change-the-data-retention-period"></a>Alterar o período de retenção de dados 

1. Faça logon no [Portal do Azure](http://portal.azure.com). 
2. Clique em **Todos os serviços**. Na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Log Analytics**.
3. No painel de assinaturas do Log Analytics, selecione o espaço de trabalho a ser modificado na lista.
4. Na página Espaço de trabalho, clique em **Retenção** no painel esquerdo.
5. No painel de retenção do espaço de trabalho, mova o controle deslizante para aumentar ou diminuir o número de dias de retenção e clique em **Salvar**.  Se você usar a camada *Gratuita*, não será possível modificar o período de retenção de dados, sendo necessário atualizar para a camada paga para controlar essa configuração.<br><br> ![Alterar a configuração de retenção de dados do espaço de trabalho](media/log-analytics-manage-cost/manage-cost-change-retention.png)

## <a name="next-steps"></a>Próximas etapas  

Para determinar a quantidade de dados coletada, quais fontes estão enviando os dados e os diferentes tipos de dados enviados para ajudar a gerenciar o consumo e os custos, consulte [Analisar o uso de dados no Log Analytics](log-analytics-usage.md)