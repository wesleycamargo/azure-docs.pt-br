---
title: "Personalizar a solução de fábrica Conectada – Azure | Microsoft Docs"
description: "Uma descrição de como personalizar o comportamento da solução pré-configurada de fábrica Conectada."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-suite
ms.devlang: c#
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/14/2017
ms.author: dobett
ms.openlocfilehash: 48c8036d0bc9534ce94529b96d32b004769246c1
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2017
---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>Personalizar como a solução de fábrica Conectada exibe dados dos servidores OPC UA

A solução de fábrica Conectada agrega e exibe dados dos servidores OPC UA conectados à solução. Você pode procurar e enviar comandos para os servidores OPC UA na solução. Para obter mais informações sobre o OPC UA, consulte as [Perguntas frequentes sobre fábrica conectada](iot-suite-faq-cf.md).

Exemplos de dados agregados na solução incluem a OEE (Eficiência Geral de Equipamentos) e os KPIs (Indicadores Chave de Desempenho) que podem ser exibidos no painel nos níveis da fábrica, da linha e da estação. A captura de tela a seguir mostra os valores de OEE e KPI da estação **Assembly** na **Linha de produção 1**, na fábrica de **Munique**:

![Exemplo de valores de OEE e KPI na solução][img-oee-kpi]

A solução permite exibir informações detalhadas de itens de dados específicos dos servidores OPC UA, chamados *estações*. A seguinte captura de tela mostra gráficos do número de itens fabricados de uma estação específica:

![Gráficos de número de itens fabricados][img-manufactured-items]

Se você clicar em um dos grafos, poderá explorar os dados ainda mais usando o TSI (Time Series Insights):

![Explorar dados usando o Time Series Insights][img-tsi]

Este artigo descreve:

- Como os dados são disponibilizados para as várias exibições da solução.
- Como você pode personalizar a maneira como a solução exibe os dados.

## <a name="data-sources"></a>Fontes de dados

A solução de fábrica Conectada exibe dados dos servidores OPC UA conectados à solução. A instalação padrão inclui vários servidores OPC UA que executam uma simulação de fábrica. Você pode adicionar seus próprios servidores OPC UA que [se conectam por meio de um gateway][lnk-connect-cf] à solução.

Você pode procurar itens de dados que um servidor OPC UA conectado pode enviar para a solução no painel:

1. Escolha **Procurar** para navegar para a exibição **Selecionar um servidor OPC UA**:

    ![Navegar para a exibição Selecionar um servidor OPC UA][img-select-server]

1. Selecione um servidor e clique em **Conectar**. Clique em **Continuar** quando o aviso de segurança for exibido.

    > [!NOTE]
    > Esse aviso só será exibido uma vez para cada servidor e estabelece uma relação de confiança entre o painel da solução e o servidor.

1. Agora você pode procurar itens de dados que o servidor pode enviar para a solução. Os itens que estão sendo enviados para a solução têm uma marca de seleção:

    ![Itens publicados][img-published]

1. Se você for um *Administrador* da solução, poderá optar por publicar um item de dados para disponibilizá-lo na solução de fábrica Conectada. Como Administrador, você também pode alterar o valor de itens de dados e chamar métodos no servidor OPC UA.

## <a name="map-the-data"></a>Mapear os dados

A solução de fábrica Conectada mapeia e agrega os itens de dados publicados do servidor OPC UA nas várias exibições da solução. A solução de fábrica Conectada é implantada em sua conta do Azure quando você provisiona a solução. Um arquivo JSON da solução de fábrica Conectada do Visual Studio armazena essas informações de mapeamento. Exiba e modifique esse arquivo de configuração JSON na solução de fábrica Conectada do Visual Studio. Você pode reimplantar a solução depois de fazer uma alteração.

Você pode usar o arquivo de configuração para:

- Editar as fábricas simuladas, linhas de produção e estações existentes.
- Mapear dados de servidores OPC UA reais que estão conectados à solução.

Para obter mais informações sobre o mapeamento e a agregação dos dados para atender às suas necessidades específicas, consulte [Como configurar a solução pré-configurada de fábrica Conectada](iot-suite-connected-factory-configure.md).

## <a name="deploy-the-changes"></a>Implantar as alterações

Quando você terminar de fazer alterações no arquivo **ContosoTopologyDescription.json**, deverá reimplantar a solução de fábrica Conectada à sua conta do Azure.

O repositório **azure-iot-connected-factory** inclui um script **build.ps1** do PowerShell que pode ser usado para recompilar e implantar a solução.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a solução pré-configurada de fábrica Conectada lendo os seguintes artigos:

* [Passo a passo de solução pré-configurada de fábrica conectada][lnk-rm-walkthrough]
* [Implantar um gateway para a fábrica Conectada][lnk-connect-cf]
* [Permissões no site azureiotsuite.com][lnk-permissions]
* [Perguntas frequentes do factory conectado](iot-suite-faq-cf.md)
* [Perguntas frequentes][lnk-faq]


[img-oee-kpi]: ./media/iot-suite-connected-factory-customize/oeenadkpi.png
[img-manufactured-items]: ./media/iot-suite-connected-factory-customize/manufactured.png
[img-tsi]: ./media/iot-suite-connected-factory-customize/tsi.png
[img-select-server]: ./media/iot-suite-connected-factory-customize/selectserver.png
[img-published]: ./media/iot-suite-connected-factory-customize/published.png
[img-munich]: ./media/iot-suite-connected-factory-customize/munich.png
[img-server-uris]: ./media/iot-suite-connected-factory-customize/serveruris.png
[lnk-kpi]: ./media/iot-suite-connected-factory-customize/kpidisplay.png

[lnk-rm-walkthrough]: iot-suite-connected-factory-sample-walkthrough.md
[lnk-connect-cf]: iot-suite-connected-factory-gateway-deployment.md
[lnk-permissions]: iot-suite-v1-permissions.md
[lnk-faq]: iot-suite-v1-faq.md