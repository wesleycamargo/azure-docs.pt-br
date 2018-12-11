---
title: Tutorial do Azure Time Series Insights (versão prévia) | Microsoft Docs
description: Saiba mais sobre o Azure Time Series Insights (versão prévia)
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 11/26/2018
ms.openlocfilehash: ed25d03f7c592476b9284790ac12f9954661a42b
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52872298"
---
# <a name="azure-time-series-insights-preview-tutorial"></a>Tutorial do Azure Time Series Insights (versão prévia)

Este tutorial guiará você pelo processo de criação de um ambiente do Azure TSI (Time Series Insights), versão prévia, preenchido com dados de dispositivos simulados. Neste tutorial, você aprenderá como:

* Criar um ambiente do TSI (versão prévia).
* Conectar o ambiente do TSI (versão prévia) a um Hub de Eventos.
* Executar uma simulação de fazenda eólica para transmitir dados para o ambiente do TSI versão prévia.
* Executar uma análise básica nos dados.
* Definir uma hierarquia e um tipo de Modelo do Time Series e associá-los às suas instâncias.

## <a name="create-a-time-series-insights-preview-environment"></a>Criar um ambiente do Time Series Insights (versão prévia)

Esta seção descreve como criar um ambiente do Azure TSI (versão prévia) usando o [portal do Azure](https://portal.azure.com/).

1. Entre no portal do Azure usando sua conta de assinatura
1. Selecione **+ Criar um recurso** no canto superior esquerdo.
1. Selecione a categoria **Internet das Coisas** e depois **Time Series Insights**.

  ![tutorial-um][1]

1. Na página do ambiente do Time Series Insights, preencha os parâmetros necessários e clique em **Avançar: Origem do Evento**

  ![tutorial-dois][2]

1. Na página  **Origem do evento**, preencha os parâmetros necessários e clique em **Revisar + Criar**.

  ![tutorial-três][3]

1. Examine todos os detalhes e clique em **Criar** para iniciar o provisionamento do ambiente.

  ![tutorial-quatro][4]

1. Você receberá uma notificação após a conclusão da implantação.

  ![tutorial-cinco][5]

## <a name="send-events-to-your-tsi-environment"></a>Enviar eventos ao ambiente do TSI

Nesta seção, você usará um simulador de dispositivo de turbina eólica para enviar eventos ao seu ambiente do TSI por meio de um Hub de Eventos.

  1. No portal do Azure, navegue até seu recurso hub de eventos e conecte-o ao ambiente do TSI. Saiba [como conectar seu recurso a um Hub de Eventos existente](./time-series-insights-how-to-add-an-event-source-eventhub.md).

  1. Na página de recursos do Hub de Eventos, acesse **Políticas de Acesso Compartilhado** e **RootManageSharedAccessKey**. Copie o valor de **Cadeia de conexão – chave primária**  exibido aqui.

      ![tutorial-seis][6]

  1. Vá para [https://tsiclientsample.azurewebsites.net/windFarmGen.html]( https://tsiclientsample.azurewebsites.net/windFarmGen.html). Este aplicativo Web simula dispositivos de turbina eólica.
  1. Cole a cadeia de conexão copiada na etapa 3 na **Cadeia de Conexão do Hub de Eventos**

      ![tutorial-sete][7]

  1. Clique em **Clique para Iniciar** para enviar eventos ao seu Hub de Eventos. Nesse estágio, um arquivo chamado `instances.json` será baixado em seu computador. Salve esse arquivo, pois precisaremos dele mais tarde.

  1. Volte ao seu hub de eventos. Agora você deve ver os novos eventos que estão sendo recebidos pelo hub.

     ![tutorial-oito][8]

## <a name="analyze-data-in-your-environment"></a>Analisar dados em seu ambiente

Nesta seção, você realizará uma análise básica de seus dados de série temporal usando o gerenciador de atualização do Time Series Insights.

  1. Navegue até o gerenciador de atualização do Time Series Insights clicando na URL da página do recurso no portal do Azure.

      ![tutorial-nove][9]

  1. No gerenciador, clique nos nós **Instâncias Órfãs** para ver todas as instâncias do Time Series no ambiente.

     ![tutorial-dez][10]

  1. Neste tutorial, vamos analisar os dados enviados no último dia. Para isso, clique em **Tempos Rápidos** e selecione a opção **Últimas 24 Horas**.

     ![tutorial-onze][11]

  1. Selecione **Sensor_0** e escolha **Mostrar Valor Médio** para visualizar os dados que estão sendo enviados dessa instância de série temporal.

     ![tutorial-doze][12]

  1. Da mesma forma, você pode plotar dados provenientes de outras instâncias de série temporal a fim de executar uma análise básica.

     ![tutorial-treze][13]

## <a name="define-a-type--hierarchy"></a>Definir um tipo e uma hierarquia 

Nesta seção, você criará um tipo e uma hierarquia e os associará às instâncias de série temporal. Leia mais sobre [Modelos do Time Series](./time-series-insights-update-tsm.md).

  1. No gerenciador, clique na guia **Modelo** na barra de aplicativos.

     ![tutorial-quatorze][14]

  1. Na seção Tipos, clique em **+ Adicionar**. Isso permitirá que você crie um novo tipo de Modelo do Time Series.

     ![tutorial-quinze][15]

  1. No editor de tipo, insira um **Nome**, uma **Descrição** e crie variáveis para os valores **Média**, **Mín.** e **Máx.**, conforme mostrado abaixo. Clique em **Criar** para salvar o tipo.

     ![tutorial-dezesseis][16]

     ![tutorial-dezessete][17]

  1. Na seção **Hierarquias**, clique em **+ Adicionar**. Isso permitirá que você crie uma nova Hierarquia de Modelo do Time Series.

     ![tutorial-dezoito][18]

  1. No editor de hierarquia, insira um **Nome** e adicione níveis de hierarquia, conforme mostrado abaixo. Clique em **Criar** para salvar a hierarquia.

     ![tutorial-dezenove][19]

     ![tutorial-vinte][20]

  1. Na seção **Instâncias**, selecione uma instância e clique em **Editar**. Isso permitirá que você associe um tipo e uma hierarquia a essa instância.

     ![tutorial-vinte-e-um][21]

  1. No editor de instância, escolha o tipo e a hierarquia definidos nas etapas 3 e 5 acima.

     ![tutorial-vinte-e-dois][22]

  1. Como alternativa, para fazer isso para todas as instâncias ao mesmo tempo, edite o arquivo `instances.json` baixado anteriormente. Nesse arquivo, substitua todos os campos **typeId** e **hierarchyId** pela ID obtida nas etapas 3 e 5 acima.

  1. Na seção **Instâncias**, clique em **Carregar JSON** e carregue o arquivo `instances.json` editado, conforme mostrado abaixo.

     ![tutorial-vinte-e-três][23]

  1. Navegue até a guia **Análise** e atualize seu navegador. Agora, você deve ver todas as instâncias associadas ao tipo e à hierarquia definidos acima.

     ![tutorial-vinte-e-quatro][24]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:  

* Criar um ambiente do TSI (versão prévia).
* Conectar o ambiente do TSI (versão prévia) a um Hub de Eventos.
* Executar uma simulação de fazenda eólica para transmitir dados ao ambiente do TSI (versão prévia).
* Executar uma análise básica nos dados.
* Definir uma hierarquia e um tipo de Modelo do Time Series e associá-los às instâncias.

Agora que você sabe como criar seu próprio ambiente de atualização do TSI, aprenda mais sobre os principais conceitos do TSI.

Leia sobre a configuração de armazenamento do TSI:

> [!div class="nextstepaction"]
> [Entrada e armazenamento no Azure TSI (versão prévia)](./time-series-insights-update-storage-ingress.md)

Saiba mais sobre Modelos do Time Series:

> [!div class="nextstepaction"]
> [Modelagem de dados do Azure TSI (versão prévia)](./time-series-insights-update-tsm.md)

<!-- Images -->
[1]: media/v2-update-provision/tutorial-one.png
[2]: media/v2-update-provision/tutorial-two.png
[3]: media/v2-update-provision/tutorial-three.png
[4]: media/v2-update-provision/tutorial-four.png
[5]: media/v2-update-provision/tutorial-five.png
[6]: media/v2-update-provision/tutorial-six.png
[7]: media/v2-update-provision/tutorial-seven.png
[8]: media/v2-update-provision/tutorial-eight.png
[9]: media/v2-update-provision/tutorial-nine.png
[10]: media/v2-update-provision/tutorial-ten.png
[11]: media/v2-update-provision/tutorial-eleven.png
[12]: media/v2-update-provision/tutorial-twelve.png
[13]: media/v2-update-provision/tutorial-thirteen.png
[14]: media/v2-update-provision/tutorial-fourteen.png
[15]: media/v2-update-provision/tutorial-fifteen.png
[16]: media/v2-update-provision/tutorial-sixteen.png
[17]: media/v2-update-provision/tutorial-seventeen.png
[18]: media/v2-update-provision/tutorial-eighteen.png
[19]: media/v2-update-provision/tutorial-nineteen.png
[20]: media/v2-update-provision/tutorial-twenty.png
[21]: media/v2-update-provision/tutorial-twenty-one.png
[22]: media/v2-update-provision/tutorial-twenty-two.png
[23]: media/v2-update-provision/tutorial-twenty-three.png
[24]: media/v2-update-provision/tutorial-twenty-four.png