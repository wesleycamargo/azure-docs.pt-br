---
title: Criar um ambiente do Azure Time Series Insights
description: Saiba como criar um ambiente de Time Series Insights, preenchido com dados de dispositivos simulados.
services: time-series-insights
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 06/04/2018
ms.author: bryanla
ms.openlocfilehash: b57e688b5f830c8c7d34315a68be94e4e4c99466
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295840"
---
# <a name="tutorial-create-an-azure-time-series-insights-environment"></a>Tutorial: Criar um ambiente do Azure Time Series Insights

Este tutorial guiará você pelo processo de criação de um ambiente Time Series Insight (TSI), preenchido com dados de dispositivos simulados. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um ambiente TSI 
> * Criar uma solução de simulação de dispositivo que contém um Hub IoT
> * Conectar o ambiente TSI ao Hub IoT
> * Executar uma simulação de dispositivo para transmitir dados ao ambiente TSI
> * Verificar os dados telemétricos simulados

## <a name="prerequisites"></a>pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. 

Sua conta de logon do Azure também precisa ser um membro da função de “Proprietário” de assinatura. Para obter detalhes, consulte [Adicionar ou alterar administradores de assinatura do Azure](/azure/billing/billing-add-change-azure-subscription-administrator)

## <a name="overview"></a>Visão geral

O ambiente TSI é onde os dados do dispositivo são coletados e armazenados. Após armazenadas no ambiente TSI, você pode usar o [TSI Explorer](time-series-quickstart.md) e [API de consulta de TSI](/rest/api/time-series-insights/time-series-insights-reference-queryapi) para consultar e analisar os dados.

Como todos os dispositivos, simulados ou físicos, o Hub IoT é o ponto de conexão usado por dispositivos para se conectar com segurança e transmitir dados na nuvem do Azure. Como discutido na [Visão geral de TSI](time-series-insights-overview.md), o Hub IoT também serve como uma origem do evento para transmitir dados para o ambiente TSI. 

Este tutorial também usa um [acelerador de solução de IoT](/azure/iot-accelerators/) para gerar e transmitir dados telemétricos de exemplo para o Hub IoT. Os aceleradores de solução de IoT fornecem soluções empresariais pré-configuradas que permitem acelerar o desenvolvimento de soluções personalizadas de IoT. 

## <a name="create-a-tsi-environment"></a>Criar um ambiente TSI

Primeiro, crie um ambiente TSI na sua assinatura do Azure:

1. Entre no [Portal do Azure](https://portal.azure.com) usando sua conta da assinatura do Azure.  
2. Selecione **+ Criar um recurso** no canto superior esquerdo.  
3. Selecione a categoria **Internet das Coisas** e depois **Time Series Insights**.  
   
   [![Selecione o recurso de ambiente Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png)](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi.png#lightbox)

4. Na página **Ambiente Time Series Insights**, preencha os parâmetros necessários:
   
   Parâmetro|DESCRIÇÃO
   ---|---
   **Nome do ambiente** | Escolha um nome exclusivo para ambiente TSI. O nome é usado pelo TSI Explorer e a API de Consulta.
   **Assinatura** | As assinaturas são contêineres para recursos do Azure. Escolha a assinatura em que deseja criar o ambiente TSI.
   **Grupo de recursos** | Um grupo de recursos é um contêiner para os recursos do Azure. Escolha um grupo de recursos existente ou crie um novo para o recurso do ambiente TSI.
   **Localidade** | Escolha uma região do data center para seu ambiente TSI. Para evitar custos e latência adicionais de largura de banda, é melhor manter o ambiente TSI na mesma região que outros recursos da IoT.
   **SKU de preço** | Escolha a taxa de transferência necessária. Para custo mais baixo e a capacidade inicial, selecione S1.
   **Capacidade** | A capacidade é o multiplicador aplicado à taxa de ingresso, à capacidade de armazenamento e ao custo associado com a SKU selecionada.  Você pode alterar a capacidade de um ambiente após a criação. Para custo mais baixo, selecione uma capacidade de 1. 

   Ao concluir, clique em **Criar** para iniciar o processo de provisionamento.

   ![Criar um recurso de ambiente Time Series Insights](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-params.png)

5. É possível verificar o painel **Notificações** para monitorar a conclusão da implantação, o que deve levar menos de um minuto:  

   ![A implantação do ambiente do Time Series Insights foi bem-sucedida](media/tutorial-create-populate-tsi-environment/ap-create-resource-tsi-deployment-succeeded.png)

## <a name="create-a-device-simulation"></a>Criar uma simulação de dispositivo

Em seguida, crie a solução de simulação do dispositivo, que gerará dados de teste para preencher o seu ambiente TSI:

1. Em uma janela/guia separada, vá até https://www.azureiotsolutions.com, entre usando a mesma conta de assinatura do Azure e selecione o acelerador de “Simulação de dispositivo”:

   ![Executar o acelerador de Simulação de dispositivo](media/tutorial-create-populate-tsi-environment/sa-main.png)

2. Insira os parâmetros necessários na página **Criar solução de Simulação de dispositivo**:

   Parâmetro|DESCRIÇÃO
   ---|---
   **Nome da solução** | Um valor exclusivo usado para a criação de um novo grupo de recursos. Os recursos do Azure listados são criados e atribuídos ao grupo de recursos.
   **Assinatura** | Especifique a mesma assinatura usada para a criação de seu ambiente TSI, na seção anterior.
   **Região** | Especifique a mesma região usada para a criação de seu ambiente TSI, na seção anterior. 
   **Implantar recursos opcionais do Azure** | Deixe a opção **Hub IoT** marcada, pois os dispositivos simulados usarão para se conectar/transmitir dados.

   Ao terminar, clique em **Criar solução** para provisionar os recursos do Azure da solução, que levará aproximadamente de 6 a 7 minutos para ser concluída:

   ![Provisionar a solução de simulação de dispositivo](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution.png)

3. Ao terminar o provisionamento, o texto acima da sua nova solução será alterado de “Provisionamento…” para “Pronto”:

   >[!IMPORTANT]
   > Não clique no botão **Iniciar** ainda! Mas mantenha essa página da Web aberta, pois você retornará a ela mais tarde.

   ![Provisionamento da solução de simulação de dispositivos concluído](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard-ready.png)

4. Agora volte ao portal do Azure e inspecione os recursos criados recentemente na sua assinatura. No página do portal do **Grupos de recursos**, você verá que um novo grupo de recursos foi criado usando o **Nome da solução** fornecido na última etapa. Observe também todos os recursos criados para oferecer suporte à solução de simulação do dispositivo:

   [![Recursos da solução de simulação de dispositivo](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/ap-device-sim-solution-resources.png#lightbox)

## <a name="connect-the-tsi-environment-to-the-iot-hub"></a>Conectar o ambiente TSI ao Hub IoT 

Neste ponto, você aprendeu como criar dois conjuntos de recursos, cada um em seu próprio grupo de recursos: 
- Um ambiente TSI vazio 
- Recursos de solução de simulação do dispositivo, incluindo um Hub IoT, gerado por um acelerador de solução

Lembre-se de que os dispositivos simulados precisam se conectar a um Hub IoT para transmitir dados de dispositivo. Para transmitir dados para o ambiente TSI, você precisa fazer alterações de configuração para o Hub IoT e o ambiente TSI. 

### <a name="iot-hub-configuration-define-a-consumer-group"></a>Configuração do Hub IoT: definir um grupo de consumidores

O Hub IoT fornece vários pontos de extremidade para compartilhar a funcionalidade para outros atores. O ponto de extremidade de “Eventos” fornece uma maneira para outros aplicativos consumirem dados, uma vez que ele é transmitido para uma instância do Hub IoT. Especificamente, os “grupos de consumidores” fornecem um mecanismo para aplicativos ouvirem e receberem dados do Hub IoT. 

Em seguida, você definirá uma nova propriedade de “grupo de consumidores”, no ponto de extremidade de “Eventos” do Hub IoT da solução de simulação de dispositivo. 

1. No portal do Azure, vá até a página **Visão geral** do grupo de recursos que você criou para a solução de simulação do dispositivo e selecione o recurso de Hub IoT:

   [![Grupo de recursos da solução de simulação de dispositivo](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-view-rg.png#lightbox)

   Também anote o **Nome** do recurso Hub IoT gerado para a solução, pois você vai se referir a ele mais tarde.

2. Role para baixo e selecione a página **Pontos de extremidade**, depois selecione o ponto de extremidade de **Eventos**. Na página do ponto de extremidade **Propriedades**, insira um nome exclusivo para o ponto de extremidade sob o grupo de consumidores “$Default” e clique em **Salvar**:

   [![Pontos de extremidade do Hub IoT da solução de simulação de dispositivo](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png)](media/tutorial-create-populate-tsi-environment/ap-add-iot-hub-consumer-group-create.png#lightbox)

### <a name="tsi-environment-configuration-define-an-event-source"></a>Configuração do ambiente TSI: definir uma origem do evento

Agora conecte o novo ponto de extremidade de evento do Hub IoT “grupo de consumidores” para o ambiente TSI como uma “origem do evento”.

1. Vá até a página **Visão geral** do grupo de recursos que você criou para o ambiente TSI e selecione o ambiente TSI:

   [![Ambiente e grupo de recursos do ambiente TSI](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png)](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-view-rg.png#lightbox)

2. Na página do ambiente TSI, selecione **Origens do evento**, depois clique em **+ Adicionar**:

   ![Visão geral do ambiente TSI](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add.png)

3. Insira os parâmetros necessários na página **Nova origem do evento**: 

   Parâmetro|DESCRIÇÃO
   ---|---
   **Nome da origem do evento** | Requer um valor exclusivo, que é usado para nomear a origem do evento.
   **Fonte** | Selecione “Hub IoT”.
   **Importar opção** | Usar o padrão “Usar Hub IoT nas assinaturas disponíveis”. Essa opção fará com que a próxima lista suspensa seja preenchida com as assinaturas disponíveis.
   **Assinatura** | Selecione a mesma assinatura em que você criou o ambiente TSI e os recursos de Simulação de dispositivo.
   **Nome do Hub IoT** | Deve ser usado como padrão para o nome do Hub IoT que você anotou anteriormente. Caso contrário, selecione o Hub IoT correto.
   **Nome da política do Hub IoT** | Deixe como o valor padrão de “iothubowner”.
   **Grupo de consumidores do Hub IoT** | Deve ser usado como padrão para o nome do grupo de consumidores do Hub IoT que você criou anteriormente. Caso contrário, selecione o nome do grupo de consumidores correto. 
   **Formato de serialização do evento** | Deixe como o valor padrão de “JSON”.
   **Nome da propriedade carimbo de data/hora** | Especifique como “timestamp”.

   Ao concluir, clique em **Criar** para adicionar a origem do evento. Ao retornar para a página **Visão geral** do grupo de recursos, junto com seu recurso de ambiente TSI você vê um novo recurso de “Origem do evento de Time Series Insights”.

   ![Nova origem do evento do ambiente TSI](media/tutorial-create-populate-tsi-environment/ap-add-env-event-source-add-event-source.png)

## <a name="run-a-device-simulation-to-stream-data-into-the-tsi-environment"></a>Executar uma simulação de dispositivo para transmitir dados ao ambiente TSI

Agora que todo o trabalho de configuração está concluído, é hora de preencher o ambiente TSI com dados de exemplo dos dispositivos simulados.

Você pode se lembrar de [Criar uma seção de simulação de dispositivo](#create-a-device-simulation), vários recursos do Azure foram criados pelo acelerador para oferecer suporte à solução. Junto com o Hub IoT discutido anteriormente, um aplicativo Web do Serviço de Aplicativo do Azure foi gerado para criar e transmitir a telemetria do dispositivo simulado.

1. Volte para o [Painel de aceleradores da solução](https://www.azureiotsolutions.com/Accelerators#dashboard). Entre novamente se necessário, usando a mesma conta do Azure que você usou neste tutorial. Agora você pode clicar no botão **Iniciar** em sua solução de “Simulação de dispositivo”:

     ![Painel dos aceleradores de solução](media/tutorial-create-populate-tsi-environment/sa-create-device-sim-solution-dashboard.png)

2. O aplicativo Web do dispositivo de simulação será iniciado nesse ponto e pode levar alguns segundos após o carregamento inicial. Também será solicitado o consentimento para dar ao aplicativo Web a permissão para “Conectar você e ler seu perfil”. Essa permissão possibilita que o aplicativo recupere as informações de perfil de usuário necessárias para oferecer suporte ao funcionamento do aplicativo:

     ![Consentimento do aplicativo Web da simulação de dispositivo](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)

3. Assim que a página **Instalação da simulação** carregar, insira os parâmetros necessários: 

   Parâmetro|DESCRIÇÃO
   ---|---
   **Hub IoT de destino** | Selecione “Usar o Hub IoT previamente provisionado”.
   **Modelo do dispositivo** | Selecione “Resfriador”.
   **Número de dispositivos**  | Insira 1000 em **Quantidade**.
   **Frequência de telemetria** | Insira 10 segundos.
   **Duração da simulação** | Selecione **Terminar em:** e insira 5 minutos.

   Quando terminar, clique em **Começar a simulação**. A simulação será executada por um total de 5 minutos, gerando dados de 1000 dispositivos simulados a cada 10 segundos.  

   ![Instalação da Simulação de dispositivo](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)

4. Enquanto a simulação é executada, você verá a atualização dos campos **Total de mensagens** e **Mensagens por segundo** aproximadamente a cada 10 segundos. A simulação terminará após cerca de 5 minutos, levando você de volta à **Instalação da simulação**.

   ![Simulação de dispositivo em execução](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)

## <a name="verify-the-telemetry-data"></a>Verificar os dados telemétricos

Nesta seção final, verifique se os dados telemétricos foram gerados e armazenados no ambiente TSI. Para verificar os dados, use o explorer do Time Series Insights, que é usado para consultar e analisar dados telemétricos.

1. Volta para a página **Visão geral** do grupo de recursos do ambiente TSI e selecione o ambiente TSI novamente:

   [![Ambiente e grupo de recursos do ambiente TSI](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

2. Na página **Visão geral** do ambiente TSI, clique em **URL do explorer do Time Series Insights** para abrir o explorer do TSI:

   [![Explorer do TSI](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

3. O explorer do TSI carregará e autenticará usando sua conta do portal do Azure. Após a exibição inicial, você pode ver na área do gráfico que o ambiente TSI realmente foi preenchido com dados telemétricos simulados. Para filtrar um intervalo mais estreito de tempo, selecione na lista suspensa no canto superior esquerdo. Depois insira um intervalo de tempo grande o suficiente para abranger a duração da simulação de dispositivo. Depois clique no ícone de lupa de pesquisa:

   [![Filtro de intervalo de tempo do explorer do TSI ](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

4. Restringir o intervalo de tempo permite que o gráfico amplie as intermitências distintas da transferência de dados para o Hub IoT e o ambiente TSI. Observe também o texto **Transmissão completa** no canto superior direito, que mostra o número total de eventos encontrados. Também é possível arrastar o controle deslizante do **Tamanho de intervalo** para controlar a granularidade de plotagem do gráfico:

   [![Exibição filtrada do intervalo de tempo do explorer do TSI](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

5. Por fim, também é possível clicar em uma região para filtrar um intervalo e, depois, clicar com botão direito do mouse e usar o **Explorar eventos** para mostrar os detalhes do evento na exibição em tabela de **Eventos**:

   [![Exibição filtrada e eventos do intervalo de tempo do explorer do TSI](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>Limpar recursos

Este tutorial cria vários serviços que executam o Azure, para oferecer suporte à solução de ambiente TSI e simulação de dispositivo. Caso queira abandonar e/ou atrasar a conclusão desta série de tutoriais, é recomendável excluir todos os recursos para evitar incorrer em custos desnecessários. 

No menu à esquerda no portal do Azure:

1. Clique no ícone de **Grupos de recursos**, depois selecione o grupo de recursos que você criou para o ambiente TSI. Na parte superior da página, clique em **Excluir grupo de recursos**, insira o nome do grupo de recursos e depois clique em **Excluir**. 
2. Clique no ícone de **Grupos de recursos**, depois selecione o grupo de recursos que foi criado com o acelerador de solução de simulação do dispositivo. Na parte superior da página, clique em **Excluir grupo de recursos**, insira o nome do grupo de recursos e depois clique em **Excluir**. 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar um ambiente TSI 
> * Criar uma solução de simulação de dispositivo que contém um Hub IoT
> * Conectar o ambiente TSI ao Hub IoT
> * Executar uma simulação de dispositivo para transmitir dados ao ambiente TSI
> * Verificar os dados telemétricos simulados

Agora que você sabe como criar seu próprio ambiente TSI, saiba como criar um aplicativo Web que recebe dados de um ambiente TSI:

> [!div class="nextstepaction"]
> [Criar um aplicativo Web de página única do Azure Time Series Insights](tutorial-create-tsi-sample-spa.md)


