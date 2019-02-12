---
title: Failover manual de um hub IoT do Azure | Microsoft Docs
description: Mostraremos como executar um failover manual para um hub IoT do Azure
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: bd9cb76557c65832de5d249cdccdc36101edf646
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55821279"
---
# <a name="tutorial-perform-manual-failover-for-an-iot-hub-public-preview"></a>Tutorial: Executar failover manual para um hub IoT (versão prévia pública)

Failover manual é um recurso do serviço Hub IoT que permite que os clientes façam [failover](https://en.wikipedia.org/wiki/Failover) das operações de seu hub de uma região primária para a região do Azure com emparelhamento geográfico correspondente. O failover manual pode ser feito em caso de um desastre regional ou uma interrupção de serviço estendida. Você também pode executar um failover planejado para testar recursos de recuperação de desastre, embora seja recomendável usar um hub IoT de teste em vez de um na produção. O recurso de failover manual é oferecido aos clientes sem custo adicional.

Neste tutorial, você executa as seguintes tarefas:

> [!div class="checklist"]
> * Criar um hub IoT usando o portal do Azure. 
> * Executar um failover. 
> * Ver o hub em execução na localização secundária.
> * Executar um failback para devolver as operações do hub IoT para a localização primária. 
> * Confirmar se o hub está sendo executado corretamente no local certo.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-an-iot-hub"></a>Crie um hub IoT

1. Faça logon no [Portal do Azure](https://portal.azure.com). 

2. Clique em **+ Criar um recurso**, selecione **Internet das Coisas** e **Hub IoT**.

   ![Captura de tela mostrando a criação de um hub IoT](./media/tutorial-manual-failover/create-hub-01.png)

3. Selecione a guia **Noções Básicas**. Preencha os campos a seguir.

    **Assinatura**: selecione a assinatura do Azure que você quer usar.

    **Grupo de Recursos**: clique em **Criar novo** e especifique **ManlFailRG** como o nome do grupo de recursos.

    **Região**: selecione uma região perto de você que seja parte da versão prévia. Este tutorial usa `westus2`. Um failover só pode ser executado entre regiões do Azure emparelhadas geograficamente. A região geográfica emparelhada com westus2 é WestCentralUS.
    
   > [!NOTE]
   > Atualmente, o failover manual está em versão prévia pública e *não* está disponível nas seguintes regiões do Azure: Leste dos EUA, Oeste dos EUA, Europa Setentrional, Europa Ocidental, Sul do Brasil e Centro-Sul dos EUA.

   **Nome do Hub IoT**: especifique um nome para o hub IoT. O nome do hub deve ser globalmente exclusivo. 

   ![Captura de tela mostrando o painel Noções básicas para a criação de um hub IoT](./media/tutorial-manual-failover/create-hub-02-basics.png)

   Clique em **Revisar + Criar**. (Ele usa os padrões para tamanho e escala.) 

4. Examine as informações e clique em **Criar** para criar o hub IoT. 

   ![Captura de tela mostrando a última etapa da criação de um hub IoT](./media/tutorial-manual-failover/create-hub-03-create.png)

## <a name="perform-a-manual-failover"></a>Realizar failover manual

Observe que há um limite de dois failovers e dois failbacks por dia para um hub IoT.

1. Clique em **Grupos de recursos** e selecione o grupo de recursos **ManlFailRG**. Clique no hub na lista de recursos. 

2. Em **Resiliência** no painel do Hub IoT, clique em **Failover manual (versão prévia)**. Observe que se seu hub não estiver definido em uma região válida, a opção de failover manual será desabilitada.

   ![Captura de tela mostrando o painel de propriedades do Hub IoT](./media/tutorial-manual-failover/trigger-failover-01.png)

3. No painel Failover manual, você vê a **localização primária** e a **localização secundária** do Hub IoT. A localização primária é inicialmente definida como o local em que você especificou quando criou o hub IoT e sempre indica o local em que o hub está ativo no momento. A localização secundária é a [região do Azure emparelhada geograficamente ](../best-practices-availability-paired-regions.md) padrão que está emparelhada com o local principal. Você não pode alterar os valores de localização. Para este tutorial, a localização primária é `westus2` e a localização secundária é `WestCentralUS`.

   ![Captura de tela mostrando o painel Failover Manual](./media/tutorial-manual-failover/trigger-failover-02.png)

3. Na parte superior do painel Failover Manual, clique em **Iniciar failover**. Você verá o painel **Confirmar failover manual**. Preencha o nome do hub IoT para confirmar que ele será o objeto do failover. Para iniciar o failover, clique em **OK**.

   O tempo que leva para realizar o failover manual é proporcional ao número de dispositivos que estão registrados no hub. Por exemplo, se você tiver 100 mil dispositivos, pode levar 15 minutos, mas se tiver cinco milhões de dispositivos, pode levar uma hora ou mais.

4. No painel **Confirmar failover manual**, preencha o nome do hub IoT para confirmar que ele será o objeto do failover. Em seguida, para iniciar o failover, clique em OK. 

   ![Captura de tela mostrando o painel Failover Manual](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   Enquanto o processo de failover manual está em execução, há uma faixa no painel Failover Manual que informa que um failover manual está em andamento. 

   ![Captura de tela mostrando Failover Manual em andamento](./media/tutorial-manual-failover/trigger-failover-04-in-progress.png)

   Se você fechar o painel do Hub IoT e abri-lo novamente clicando no painel Grupo de Recursos, verá uma faixa informando que o hub não está ativo. 

   ![Captura de tela mostrando o Hub IoT inativo](./media/tutorial-manual-failover/trigger-failover-05-hub-inactive.png)

   Depois de concluído, as regiões primária e secundária na página Failover Manual são invertidas e o hub fica ativo novamente. Neste exemplo, a localização primária é agora `WestCentralUS` e a localização secundária, `westus2`. 

   ![Captura de tela mostrando que o failover foi concluído](./media/tutorial-manual-failover/trigger-failover-06-finished.png)

## <a name="perform-a-failback"></a>Executar um failback 

Depois de realizar um failover manual, você pode alternar as operações do hub para a região primária original; isso recebe o nome de failback. Se você acabou de executar um failover, precisará esperar aproximadamente uma hora antes de poder solicitar um failback. Se você tentar realizar o failback em um curto período de tempo, uma mensagem de erro será exibida.

Um failback é executado da mesma forma que um failover manual. Estas são as etapas: 

1. Para executar um failback, volte para o painel Hub Iot Hub de seu hub Iot.

2. Em **Resiliência** no painel do Hub IoT, clique em **Failover manual (versão prévia)**. 

3. Na parte superior do painel Failover Manual, clique em **Iniciar failover**. Você verá o painel **Confirmar failover manual**. 

4. No painel **Confirmar failover manual**, preencha o nome do hub IoT para confirmar que ele será o objeto do failback. Em seguida, para iniciar o failback, clique em OK. 

   ![Captura de tela da solicitação de failback manual](./media/tutorial-manual-failover/trigger-failback-01-regions.png)

   As faixas são exibidas conforme explicado na seção Executar um failover. Após concluir o failback, ele mostra novamente `westus2` como a localização principal e `WestCentralUS` como a localização secundária, conforme definido originalmente.

## <a name="clean-up-resources"></a>Limpar recursos 

Para remover os recursos que você criou para este tutorial, exclua o grupo de recursos. Essa ação também exclui todos os recursos contidos no grupo. Nesse caso, ele remove o hub IoT e o próprio grupo de recursos. 

1. Clique em **Grupos de Recursos**. 

2. Localize e selecione o grupo de recursos **ManlFailRG**. Clique nela para abri-la. 

3. Clique em **Excluir grupo de recursos**. Quando solicitado, insira o nome do grupo de recursos e clique em **Excluir** para confirmar. 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a configurar e executar um failover manual e a solicitar um failback executando as seguintes tarefas:

> [!div class="checklist"]
> * Criar um hub IoT usando o portal do Azure. 
> * Executar um failover. 
> * Ver o hub em execução na localização secundária.
> * Executar um failback para devolver as operações do hub IoT para a localização primária. 
> * Confirmar se o hub está sendo executado corretamente no local certo.

Avance para o próximo tutorial para aprender a gerenciar o estado de um dispositivo IoT. 

> [!div class="nextstepaction"]
[Gerenciar o estado de um dispositivo IoT](tutorial-device-twins.md)
