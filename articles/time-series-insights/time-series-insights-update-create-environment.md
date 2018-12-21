---
title: Configuração de Versão Prévia do Azure Time Series Insights – Configurar um ambiente de Versão Prévia do Azure Time Series Insights | Microsoft Docs
description: Saiba como configurar seu ambiente na Versão Prévia do Azure Time Series Insights.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 12/12/2018
ms.custom: seodec18
ms.openlocfilehash: 9ad957d6378b1279f1ca51939eb4802b0ce7d78f
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53322611"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Tutorial: Configurar um ambiente de Versão Prévia do Azure Time Series Insights

Este tutorial orienta você pelo processo de criação de um ambiente de Versão Prévia (PAYG) de pagamento conforme o uso do Azure Time Series Insights. Neste tutorial, você aprenderá como:

* Criar um ambiente de Versão Prévia do Azure Time Series Insights.
* Conectar o ambiente de Versão Prévia do Azure Time Series Insights a um hub de eventos nos Hubs de Eventos do Azure.
* Executar uma simulação de parque eólico para transmitir dados para o ambiente de Versão Prévia do Azure Time Series Insights.
* Executar uma análise básica nos dados.
* Definir uma Hierarquia e um Tipo de Modelo de Série Temporal e associá-los às suas instâncias.

# <a name="create-a-device-simulation"></a>Criar uma simulação de dispositivo

Nesta seção, você criará três dispositivos simulados que enviarão dados para um Hub IoT.

1. Vá para a [página inicial de aceleradores de solução de IoT do Azure](https://www.azureiotsolutions.com/Accelerators). A página inicial de aceleradores de solução de IoT do Azure exibe vários exemplos pré-criados. Entre usando sua conta do Azure. Em seguida, selecione **Simulação de Dispositivo**.

   ![Página inicial de aceleradores de solução de IoT do Azure][1]

   Por fim, clique **Tentar Agora**.

1. Insira os parâmetros necessários na página de solução **Criar Simulação de Dispositivo**:

   | Parâmetro | DESCRIÇÃO |
   | --- | --- |
   | Nome da solução |    Um valor exclusivo usado para a criação de um novo grupo de recursos. Os recursos do Azure listados são | criados e atribuídos ao grupo de recursos. |
   | Assinatura | Especifique a mesma assinatura usada para a criação de seu ambiente de TSI |
   | Região |   Especifique a mesma região usada para a criação de seu TSI. |
   | Implantar Recursos do Azure opcionais    | Deixe a opção Hub IoT marcada, pois os dispositivos simulados a usarão para se conectar/transmitir dados. |

   Depois de inserir os parâmetros necessários, clique em **Criar Solução**. Aguarde aproximadamente 10 a 15 minutos para que sua solução seja implantada.

   ![Criar solução de simulação de dispositivo][2]

1. No seu **Painel de Acelerador de Solução**, clique no botão **Inicializar**:

   ![Inicializar a solução de simulação de dispositivo][3]

1. Você será redirecionado à página **Simulação de Dispositivo IoT do Microsoft Azure**. Clique em **+ Nova simulação** no canto superior direito da tela.

   ![Página de simulação de IoT do Azure][4]

1.  Preencha os parâmetros obrigatórios da seguinte maneira:

    ![Parâmetros a serem preenchidos][5]

    |||
    | --- | --- |
    | **Nome** | Insira um nome exclusivo para um simulador |
    | **Descrição** | Inserir uma definição |
    | **Duração da Simulação** | Definida como `Run indefinitely` |
    | **Modelo do Dispositivo** | **Nome**: Insira `Chiller` **Quantidade**: Inserir `3` |
    | **Hub IoT de destino** | Definida como `Use pre-provisioned IoT Hub` |

    Depois de preencher os parâmetros necessários, clique em **iniciar Simulação**.

1. No painel de simulação do dispositivo, confira **Dispositivos ativos** e **Mensagens por segundo**.

    ![Painel de simulação de IoT do Azure][6]

## <a name="list-device-simulation-properties"></a>Listar propriedades de simulação de dispositivo

Antes de criar um ambiente Azure Time Series Insights, você precisará dos nomes de seu Hub IoT, da assinatura e do nome do grupo de recursos.

1. Vá para o **Painel de Acelerador de Solução** e entre usando a mesma conta de assinatura do Azure. Encontre a simulação de dispositivo que você criou nas etapas anteriores.

1. Clique no simulador de dispositivo e em **Inicializar**. Clique no link **Portal de Gerenciamento do Azure** exibido no lado direito.

    ![Listagens de simulador][7]

1. Tome nota dos nomes do Hub IoT, da assinatura e grupo de recursos.

    ![Portal do Azure][8]

## <a name="create-a-time-series-insights-preview-payg-environment"></a>Criar um ambiente PAYG de Versão Prévia do Time Series Insights

Esta seção descreve como criar um ambiente de Versão Prévia do Azure Time Series Insights usando o [portal do Azure](https://portal.azure.com/).

1. Entre no portal do Azure usando sua conta da assinatura.

1. Selecione **Criar um recurso**.

1. Selecione a categoria **Internet das Coisas** e depois selecione **Time Series Insights**.

   ![Selecione Criar um recurso, selecione Internet das Coisas e, em seguida, selecione Time Series Insights][9]

1. Preencha os campos na página da seguinte maneira:

   | | |
   | --- | ---|
   | **Nome do ambiente** | Escolha um nome exclusivo para o ambiente de Versão Prévia do Azure Time Series Insights. |
   | **Assinatura** | Insira sua assinatura em que você deseja criar o ambiente de Versão Prévia do Azure Time Series Insights. É uma melhor prática usar a mesma assinatura que o restante de seus recursos de IoT criados pelo simulador de dispositivo. |
   | **Grupo de recursos** | Um grupo de recursos é um contêiner para os recursos do Azure. Escolha um grupo de recursos existente ou crie um novo para o recurso de ambiente de Versão Prévia do Azure Time Series Insights. É uma melhor prática usar o mesmo grupo de recursos que o restante de seus recursos de IoT criados pelo simulador de dispositivo. |
   | **Localidade** | Escolha uma região do data center para o seu ambiente de Versão Prévia do Azure Time Series Insights. Para evitar custos de largura de banda adicional e latência, é melhor manter o ambiente de Versão Prévia do Azure Time Series Insights na mesma região que outros recursos de IoT. |
   | **Camada** |  Selecione `PAYG` que representa pagamento conforme o uso. Essa é a SKU para o produto Versão Prévia do Azure Time Series Insights. |
   | **ID da Propriedade** | Identifica exclusivamente a sua série temporal. Observe que esse campo é imutável e não pode ser alterado posteriormente. Para este tutorial, defina o campo `iothub-connection-device-id`. Para saber mais sobre a ID de Série Temporal, leia [Como escolher uma ID de Série Temporal](./time-series-insights-update-how-to-id.md). |
   | **Nome da Conta de Armazenamento** | Insira um nome global exclusivo para uma nova conta de armazenamento ser criada. |

   Depois de preencher os campos acima, clique em **Avançar: Origem do Evento**.

   ![Clique em Avançar: Origem do Evento][10]

1. Na página, preencha os campos da seguinte maneira:

   | | |
   | --- | --- |
   | **Criar uma Fonte de Eventos?** | Inserir `Yes`|
   | **Nome** | Requer um valor exclusivo, que é usado para nomear a origem do evento.|
   | **Tipo de Fonte** | Inserir `IoT Hub` |
   | **Selecionar um Hub?** | Inserir `Select Existing` |
   | **Assinatura** | Insira a assinatura que você usou para o simulador de dispositivo. |
   | **Nome do Hub IoT** | Insira o nome do Hub IoT que você criou para o simulador de dispositivo. |
   | **Política de acesso do Hub IoT** | Inserir `iothubowner` |
   | **Grupo de consumidores do Hub IoT** | Você precisa de um grupo de consumidores exclusivo para uma Versão Prévia do Azure Time Series Insights. |
   | **Timestamp** | Esse campo é usado para identificar a propriedade de carimbo de data/hora em seus dados de telemetria de entrada. Para este tutorial, não preencha o campo. Esse simulador usa o carimbo de data/hora de entrada do Hub IoT, cujo padrão é o Time Series Insights.|

   Para criar um grupo de consumidores exclusivo:

   1. Clique em **Novo** ao lado do campo **Grupo de consumidores do Hub IoT**:

      ![Clique em Avançar: Origem do Evento][11]

   1. Dê ao grupo de consumidores um nome exclusivo e clique em **Adicionar**:

      ![Clique em Adicionar][12]

   Depois de preencher os campos acima, clique em **Examinar + criar**.

      ![Examinar e criar][13]

1. Examine todos os campos na página de análise e clique em **Criar**.

   ![Criar][14]

1. Você pode ver o status da implantação.

   ![Implantação concluída][15]

1. Você deverá receber acesso ao seu ambiente de série temporal se você tiver o locatário. Para verificar se você tem acesso:

   * Navegue até o seu ambiente de Versão Prévia do Azure Time Series Insights criado recentemente. Você pode fazer isso pesquisando seu grupo de recursos. Em seguida, clique no seu ambiente de série temporal:

      ![Implantação concluída][16]

   * Na página Versão Prévia do Azure Time Series Insights, navegue até **Políticas de Acesso a Dados**.

     ![Políticas de acesso de dados][17]

   * Verifique se suas credenciais estão listadas.

     ![Verificar suas credenciais][18]

   Se as credenciais não estiverem listadas, será preciso ter permissão para acessar o ambiente. Leia [Conceder Acesso a Dados](./time-series-insights-data-access.md) para saber mais sobre como configurar permissões.

## <a name="analyze-data-in-your-environment"></a>Analisar dados em seu ambiente

Nesta seção, você executa análise básica em seus dados de série temporal usando o [gerenciador de Versão Prévia do Azure Time Series Insights](./time-series-insights-update-explorer.md).

1. Vá para seu gerenciador de Versão Prévia do Azure Time Series Insights clicando na URL da página do recurso no [portal do Azure](https://portal.azure.com/).

   ![URL do gerenciador do Azure Time Series Insights][19]

1. No gerenciador, selecione os nós **Instâncias Órfãs** para ver toda a Versão Prévia do Azure Time Series Insights no ambiente.

   ![Lista de instâncias órfãs][20]

1. Na série temporal mostrada, clique na primeira instância. Em seguida, clique em **Mostrar Pressão Média**.

   ![Mostrar pressão média][21]

1. Um gráfico de série temporal deve aparecer à direita:

   ![Gráfico de série temporal][22]

1. Repita a **etapa 3** com as outras duas séries temporais. Todas as séries temporais então podem ser exibidas, conforme mostrado abaixo:

   ![Gráfico de toda a série temporal][23]

1. Modifique o **intervalo de tempo** para ver as tendências de séries temporais na última hora. Marque a caixa de opção **De**, conforme mostrado abaixo:

   ![Selecione a opção De][24]

1. Altere a hora dentro da caixa de opção **De** para exibir eventos da última hora:

   ![Selecione a opção De][25]

1. Em seguida, você pode comparar a pressão em todos os três dispositivos na última hora:

   ![Selecione a opção De][26]

## <a name="define-and-apply-a-model"></a>Definir e aplicar um modelo

Nesta seção, você aplicará um modelo para estruturar seus dados. Para concluir o modelo, você definirá Tipos, Hierarquias e Instâncias. Para saber mais sobre modelagem de dados, acesse [Modelos de Série Temporal](./time-series-insights-update-tsm.md).

1. No gerenciador, selecione a guia **Modelo**:

   ![Selecione a guia de modelo][27]

1. Em seguida, clique em **+ Adicionar** para adicionar um tipo. No lado direito, um editor de tipos será aberto.

   ![Clique em Adicionar][28]

1. Em seguida, defina três variáveis: Pressão, Temperatura e Umidade em um Tipo. Insira os seguintes campos:

   | | |
   | --- | ---|
   | **Nome** | Inserir `Chiller` |
   | **Descrição** | Inserir `This is a type definition of Chiller` |

   * Agora, defina a pressão com três variáveis:

      | | |
      | --- | ---|
      | **Nome** | Inserir `Avg Pressure` |
      | **Valor** | Selecione **pressão (Duplo)**. Observe que pode levar alguns minutos para esse campo ser preenchido depois que o Azure Time Series Insights começa a receber eventos |
      | **Operação de Agregação** | Selecione `AVG` |

      ![Adicionar uma variável][29]

      Clique em **+Variável** para adicionar a variável seguinte.

   * Agora, defina Temperatura:

      | | |
      | --- | ---|
      | **Nome** | Inserir `Avg Temperature` |
      | **Valor** | Selecione **temperatura (Duplo)**. Observe que pode levar alguns minutos para esse campo ser preenchido depois que o Azure Time Series Insights começa a receber eventos |
      | **Operação de Agregação** | Selecione `AVG`|

      ![Definir Temperatura][30]

   * Agora, defina Umidade:

      | | |
      | --- | ---|
      | **Nome** | Inserir `Max Humidity` |
      | **Valor** | Selecione **umidade (Duplo)**. Observe que pode levar alguns minutos para esse campo ser preenchido depois que o Azure Time Series Insights começa a receber eventos |
      | **Operação de Agregação** | Selecione `MAX`|

      ![Definir Temperatura][31]

   Depois de definir as variáveis, clique em **Criar**.

1. Você pode ver seu tipo adicionado:

   ![Veja o tipo adicionado][32]

1. A próxima etapa é adicionar uma Hierarquia. Na seção **Hierarquias**, selecione **+Adicionar** para criar uma nova hierarquia:

   ![Adicionar uma Hierarquia][33]

1. Definir a Hierarquia. Insira os campos da seguinte maneira:

   | | |
   | --- | ---|
   | **Nome** | Inserir `Location Hierarchy` |
   | **Nível 1** | Inserir `Country` |
   | **Nível 2** | Inserir `City` |
   | **Nível 3** | Inserir `Building` |

   Depois de preencher os campos acima, clique em **Criar**.

   ![Definir uma Hierarquia][34]

1. Você pode ver a Hierarquia criada:

   ![Veja sua Hierarquia][35]

1. Depois de definir sua hierarquia, clique em **Instâncias** à esquerda. Depois que as instâncias são exibidas, clique na primeira instância e selecione **Editar**:

   ![Editar uma instância][36]

1. No lado direito, aparecerá um editor de texto. Adicione os seguintes campos:

   | | |
   | --- | --- |
   | **Tipo** | Selecione `Chiller` |
   | **Descrição** | Inserir `Instance for Chiller-01.1` |
   | **Hierarquias** | Habilitar `Location Hierarchy` |
   | **País** | Inserir `USA` |
   | **Cidade** | Inserir `Seattle` |
   | **Prédio** | Inserir `Space Needle` |

    Depois de preencher os campos acima, clique em **Salvar**.

   ![Salvar um resfriador][37]

1. Repita a etapa anterior para os outros sensores. Use os seguintes campos:

   * Para o Resfriador 01.2:

     | | |
     | --- | --- |
     | **Tipo** | Selecione `Chiller` |
     | **Descrição** | Inserir `Instance for Chiller-01.2` |
     | **Hierarquias** | Habilitar `Location Hierarchy` |
     | **País** | Inserir `USA` |
     | **Cidade** | Inserir `Seattle` |
     | **Prédio** | Inserir `Pacific Science Center` |

   * Para o Resfriador 01.3:

     | | |
     | --- | --- |
     | **Tipo** | Selecione `Chiller` |
     | **Descrição** | Inserir `Instance for Chiller-01.1` |
     | **Hierarquias** | Habilitar `Location Hierarchy` |
     | **País** | Inserir `USA` |
     | **Cidade** | Inserir `New York` |
     | **Prédio** | Inserir `Empire State Building` |

1. Vá para a guia **Analisar** e atualize a página. Expanda todos os níveis de Hierarquia para localizar a série temporal.

   ![Exibir a guia analisar][38]

1. Para explorar a série temporal na última hora, altere **Tempos Rápidos** para a última hora:

   ![Explorar a última hora][39]

1. Clique na série temporal em **Centro de Ciência do Pacífico** e clique em **Mostrar Umidade Máxima**.

   ![Mostrar Umidade máxima][40]

1. A série temporal para **Umidade Máxima** com um tamanho de intervalo de 1 minuto será aberta. Clique com o botão esquerdo do mouse em uma região para filtrar um intervalo. Em seguida, clique com o botão direito do mouse para aplicar zoom para analisar eventos no período:

   ![Exibição, filtro e zoom][41]

   ![Exibição, filtro e zoom][42]

1. Você pode também clicar em uma região e, em seguida, clique com o botão direito do mouse para ver os detalhes do evento:

   ![Exibição, filtro e zoom][43]

   ![Exibição, filtro e zoom][44]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:  

* Criar e usar um acelerador de simulação de dispositivo.
* Crie um ambiente PAYG de Versão Prévia do Azure Time Series Insights.
* Conecte o ambiente de Versão Prévia do Azure Time Series Insights a um hub de eventos.
* Execute uma simulação de parque eólico para transmitir dados para o ambiente de Versão Prévia do Azure Time Series Insights.
* Execute uma análise básica dos dados.
* Defina uma hierarquia e um tipo de Modelo do Time Series e associe-os às suas instâncias.

Agora que você sabe como criar seu próprio ambiente de Versão Prévia do Azure Time Series Insights, saiba mais sobre os principais conceitos no Azure Time Series Insights.

Leia sobre a configuração de armazenamento do Azure Time Series Insights:

> [!div class="nextstepaction"]
> [Armazenamento e entrada da Versão Prévia do Azure Time Series Insights](./time-series-insights-update-storage-ingress.md)

Saiba mais sobre Modelos do Time Series:

> [!div class="nextstepaction"]
> [Modelagem de dados da Versão Prévia do Azure Time Series Insights](./time-series-insights-update-tsm.md)

<!-- Images -->
[1]: media/v2-update-provision/device-one-accelerator.png
[2]: media/v2-update-provision/device-two-create.png
[3]: media/v2-update-provision/device-three-launch.png
[4]: media/v2-update-provision/device-four-iot-sim-page.png
[5]: media/v2-update-provision/device-five-params.png
[6]: media/v2-update-provision/device-six-listings.png
[7]: media/v2-update-provision/device-seven-dashboard.png
[8]: media/v2-update-provision/device-eight-portal.png

[9]: media/v2-update-provision/payg-one-azure.png
[10]: media/v2-update-provision/payg-two-create.png
[11]: media/v2-update-provision/payg-three-new.png
[12]: media/v2-update-provision/payg-four-add.png
[13]: media/v2-update-provision/payg-five-event-source.png
[14]: media/v2-update-provision/payg-six-review.png
[15]: media/v2-update-provision/payg-seven-deploy.png
[16]: media/v2-update-provision/payg-eight-environment.png
[17]: media/v2-update-provision/payg-nine-data-access.png
[18]: media/v2-update-provision/payg-ten-verify.png

[19]: media/v2-update-provision/analyze-one-portal.png
[20]: media/v2-update-provision/analyze-two-unparented.png
[21]: media/v2-update-provision/analyze-three-show-pressure.png
[22]: media/v2-update-provision/analyze-four-chart.png
[23]: media/v2-update-provision/analyze-five-chart.png
[24]: media/v2-update-provision/analyze-six-from.png
[25]: media/v2-update-provision/analyze-seven-change-from.png
[26]: media/v2-update-provision/analyze-eight-all.png

[27]: media/v2-update-provision/define-one-model.png
[28]: media/v2-update-provision/define-two-add.png
[29]: media/v2-update-provision/define-three-variable.png
[30]: media/v2-update-provision/define-four-avg.png
[31]: media/v2-update-provision/define-five-humidity.png
[32]: media/v2-update-provision/define-six-type.png
[33]: media/v2-update-provision/define-seven-hierarchy.png
[34]: media/v2-update-provision/define-eight-add-hierarchy.png
[35]: media/v2-update-provision/define-nine-created.png
[36]: media/v2-update-provision/define-ten-edit.png
[37]: media/v2-update-provision/define-eleven-chiller.png
[38]: media/v2-update-provision/define-twelve.png
[39]: media/v2-update-provision/define-thirteen-explore.png
[40]: media/v2-update-provision/define-fourteen-show-max.png
[41]: media/v2-update-provision/define-fifteen-filter.png
[42]: media/v2-update-provision/define-sixteen.png
[43]: media/v2-update-provision/define-seventeen.png
[44]: media/v2-update-provision/define-eighteen.png