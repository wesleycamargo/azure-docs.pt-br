---
title: "Introdução às soluções pré-configuradas | Microsoft Docs"
description: "Siga este tutorial para aprender a implantar uma solução pré-configurada do Azure IoT Suite."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 6ab38d1a-b564-469e-8a87-e597aa51d0f7
ms.service: iot-suite
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 9e1bcba086a9f70c689a5d7d7713a8ecdc764492
ms.openlocfilehash: 8248e0a02cb0775a87f0c8130e53b98f8bcfe581
ms.lasthandoff: 02/27/2017


---
# <a name="tutorial-get-started-with-the-preconfigured-solutions"></a>Tutorial: introdução às soluções pré-configuradas
## <a name="introduction"></a>Introdução
As [soluções pré-configuradas][lnk-preconfigured-solutions] do Azure IoT Suite combinam vários serviços de IoT do Azure para fornecer soluções de ponta a ponta que implementam cenários comuns de negócios de IoT. A solução pré-configurada de *monitoramento remoto* conecta-se seus dispositivos e os monitora. Você pode usar a solução para analisar o fluxo de dados de dispositivos e melhorar os resultados de negócios fazendo com que os processos respondam automaticamente a esse fluxo de dados.

Este tutorial mostra como provisionar a solução pré-configurada de monitoramento remoto. Ele também explica os recursos básicos da solução pré-configurada. Você pode acessar muitos desses recursos no painel de solução que é implantado como parte da solução pré-configurada:

![Painel de solução pré-configurada de monitoramento remoto][img-dashboard]

Para concluir este tutorial, você precisa de uma assinatura ativa do Azure.

> [!NOTE]
> Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][lnk_free_trial].
> 
> 

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## <a name="view-the-solution-dashboard"></a>Exibir o painel de solução
O painel de solução permite que você gerencie a solução implantada. Por exemplo, você pode exibir telemetria, adicionar dispositivos e configurar regras.

1. Quando o provisionamento for concluído e o bloco da solução pré-configurada indicar **Pronto**, clique em **Iniciar** para abrir o portal de solução de monitoramento remoto em uma nova guia.
   
   ![Iniciar a solução pré-configurada][img-launch-solution]
2. Por padrão, o portal de solução mostra o *painel de solução*. Você pode selecionar outros modos de exibição usando o menu à esquerda.
   
   ![Painel de solução pré-configurada de monitoramento remoto][img-dashboard]

O painel exibe as seguintes informações:

* O mapa exibe o local de cada dispositivo conectado à solução. Quando você executa a solução, há quatro dispositivos simulados. Os dispositivos simulados são implementados como trabalhos Web do Azure e a solução usa a API do Bing Mapas para plotar informações no mapa.
* O painel **Histórico de Telemetria** plota telemetria de umidade e temperatura de um dispositivo selecionado em tempo quase real e exibe dados de agregação, como umidade média, mínima e máxima.
* O painel **Histórico de Alarme** mostra eventos recentes de alarme quando um valor de telemetria excedeu um limite. Você pode definir seus próprios alarmes além dos exemplos criados pela solução pré-configurada.
* O painel **Trabalhos** exibe informações sobre os trabalhos agendados. Você pode agendar seus próprios trabalhos na página **Trabalhos de gerenciamento**.

## <a name="view-the-device-list"></a>Exibir a lista de dispositivos
A *lista de dispositivos* mostra todos os dispositivos registrados na solução. Na lista de dispositivos, você pode exibir e editar os metadados do dispositivo, adicionar ou remover os dispositivos e chamar métodos nos dispositivos.

1. Clique em **Dispositivos** no menu à esquerda para mostrar a lista de dispositivos dessa solução.
   
   ![Lista de dispositivos no painel][img-devicelist]
2. A lista de dispositivos mostra inicialmente quatro dispositivos simulados criados pelo processo de provisionamento. Você pode adicionar dispositivos simulados e físicos extras à solução.
3. É possível personalizar as informações mostradas na lista de dispositivos clicando no **Editor de colunas**. Você pode adicionar e remover as colunas que exibem os valores de propriedade e marcação relatados. Também pode reorganizar e renomear as colunas:
   
   ![Editor de colunas no painel][img-columneditor]
4. Para exibir os detalhes do dispositivo, clique em um dispositivo na lista de dispositivos.
   
   ![Detalhes do dispositivo no painel][img-devicedetails]

O painel **Detalhes do Dispositivo** contém seis seções:

* Uma coleção de links que permite personalizar o ícone do dispositivo, desabilitar o dispositivo, adicionar uma regra, chamar um método ou enviar um comando. Para ter uma comparação de comandos (mensagens do dispositivo para nuvem) e métodos (métodos diretos), consulte [Orientação para comunicações entre o dispositivo e a nuvem][lnk-c2d-guidance].
* A seção **Dispositivo Gêmeo - Marcações** permite editar os valores da marcação para o dispositivo. Você pode exibir os valores da marcação na lista de dispositivos e usar tais valores para filtrar a lista de dispositivos.
* A seção **Dispositivo Gêmeo - Propriedades Desejadas** permite definir os valores da propriedade a ser enviados para o dispositivo.
* A seção **Dispositivo Gêmeo - Propriedades Relatadas** mostra os valores da propriedade enviados do dispositivo.
* A seção **Propriedades do Dispositivo** mostra as informações do registro de identidade, como a ID do dispositivo e as chaves de autenticação.
* A seção **Trabalhos Recentes** mostra as informações sobre qualquer trabalho destinado recentemente a esse dispositivo.

## <a name="customize-the-device-icon"></a>Personalizar o ícone do dispositivo

Você pode personalizar o ícone do dispositivo exibido na lista de dispositivos no painel **Detalhes do Dispositivo** como a seguir:

1. Clique no ícone de lápis para abrir o painel **Editar imagem** de um dispositivo:
   
   ![Abrir o editor de imagens do dispositivo][img-startimageedit]
2. Carregue uma nova imagem ou use uma das imagens existentes, em seguida, clique em **Salvar**:
   
   ![Editar o editor de imagens do dispositivo][img-imageedit]
3. A imagem selecionada agora é exibida na coluna **Ícone** do dispositivo.

> [!NOTE]
> A imagem é colocada no armazenamento de blobs. Uma marcação no dispositivo gêmeo contém um link para a imagem no armazenamento de blobs.
> 
> 

## <a name="invoke-a-method-on-a-device"></a>Chamar um método em um dispositivo
No painel **Detalhes do Dispositivo**, você pode chamar métodos no dispositivo. Quando um dispositivo inicia, ele envia informações sobre os comandos com suporte para a solução.

1. Clique em **Métodos** no painel **Detalhes do Dispositivo** do dispositivo selecionado:
   
   ![Métodos do dispositivo no painel][img-devicemethods]
2. Selecione **Reinicializar** na lista de métodos.
3. Clique em **Chamar o Método**.
4. Você pode ver o status da chamada do método no histórico de métodos.
   
   ![Status do método no painel][img-pingmethod]

A solução rastreia o status de cada método chamado. Quando o dispositivo concluir o método, você verá uma nova entrada na tabela do histórico de métodos.

Alguns métodos iniciam trabalhos assíncronos no dispositivo. Por exemplo, o método **InitiateFirmwareUpdate** inicia uma tarefa assíncrona para executar a atualização. O dispositivo usa as propriedades relatadas para informar o status de atualização do firmware à medida que progride.

## <a name="send-a-command-to-a-device"></a>Enviar um comando para um dispositivo
No painel **Detalhes do Dispositivo**, você pode enviar comandos ao dispositivo. Quando um dispositivo é iniciado, ele envia informações sobre os comandos a que ele dá suporte para a solução.

1. Clique em **Comandos** no painel **Detalhes do Dispositivo ** do dispositivo selecionado:
   
   ![Comandos de dispositivo no painel][img-devicecommands]
2. Selecione **PingDevice** na lista de comandos.
3. Clique em **Enviar Comando**.
4. Você pode ver o status do comando no histórico de comandos.
   
   ![Status do comando no painel][img-pingcommand]

A solução rastreia o status de cada comando enviado. Inicialmente, o resultado é **Pendente**. Quando o dispositivo relata que executou o comando, o resultado é definido como **Sucesso**.

## <a name="add-a-new-simulated-device"></a>Adicionar um novo dispositivo simulado
Ao implantar a solução pré-configurada, você provisiona automaticamente os quatro dispositivos de exemplo que pode ver na lista de dispositivos. Esses dispositivos são *dispositivos simulados* em execução em um Trabalho Web do Azure. Com os dispositivos simulados, você pode experimentar mais facilmente a solução pré-configurada sem a necessidade de implantar dispositivos físicos reais. Se você quiser conectar um dispositivo real à solução, confira o tutorial [Conectar o dispositivo à solução pré-configurada de monitoramento remoto][lnk-connect-rm].

As etapas a seguir mostram como adicionar um dispositivo simulado à solução:

1. Navegue de volta para a lista de dispositivos.
2. Para adicionar um dispositivo, clique em **+ Adicionar um Dispositivo** no canto inferior esquerdo.
   
   ![Adicionar um dispositivo à solução pré-configurada][img-adddevice]
3. Clique em **Adicionar Novo** no bloco **Dispositivo Simulado**.
   
   ![Definir novos detalhes do dispositivo no painel][img-addnew]
   
   Além de criar um novo dispositivo simulado, você também poderá adicionar um dispositivo físico se optar por criar um **Dispositivo Personalizado**. Para saber mais sobre como conectar dispositivos físicos à solução, confira [Conectar o dispositivo à solução pré-configurada de monitoramento remoto do IoT Suite][lnk-connect-rm].
4. Selecione **Deixe-me definir minha própria ID** de dispositivo e adicione um nome de ID de dispositivo exclusivo, como **mydevice_01**.
5. Clique em **Criar**.
   
   ![Salvar um novo dispositivo][img-definedevice]
6. Na etapa 3 de **Adicionar um dispositivo simulado**, clique em **Concluído** para retornar à lista de dispositivos.
7. Você pode exibir o dispositivo **Executando** na lista de dispositivos.
   
    ![Exibir novo dispositivo na lista de dispositivos][img-runningnew]
8. Você também pode exibir a telemetria simulada do novo dispositivo no painel:
   
    ![Exibir telemetria do novo dispositivo][img-runningnew-2]

## <a name="device-properties"></a>Propriedades do dispositivo
A solução pré-configurada do monitoramento remoto usa [dispositivos gêmeos][lnk-device-twin] para sincronizar os metadados do dispositivo entre os dispositivos e o back-end da solução. Um dispositivo gêmeo é um documento JSON armazenado no Hub IoT que armazena os valores da propriedade para um dispositivo individual. Os dispositivos costumam enviar metadados para o back-end da solução como *propriedades relatadas* para armazenar no dispositivo gêmeo. O back-end da solução pode definir as *propriedades desejadas* no dispositivo gêmeo para enviar atualizações de metadados para os dispositivos. As propriedades relatadas mostram os valores de metadados mais recentes enviados pelo dispositivo. Para obter mais informações, consulte [Registro de identidade do dispositivo, dispositivo gêmeo e DocumentDB][lnk-devicemetadata].

> [!NOTE]
> A solução também usa um banco de dados DocumentDB para armazenar os dados específicos do dispositivo relacionados a comandos e métodos.
> 
> 

1. Navegue de volta para a lista de dispositivos.
2. Selecione o novo dispositivo na **Lista de Dispositivos** e clique em **Editar** para editar o **Dispositivo Gêmeo - Propriedades do Dispositivo**:
   
   ![Editar as propriedades desejadas do dispositivo][img-editdevice]
3. Defina o **Nome da Propriedade Desejada** para **Latitude** e defina o valor para **47.639521**. Em seguida, clique em **Salvar Alterações no Registro do Dispositivo**:
   
    ![Editar a propriedade desejada do dispositivo][img-editdevice2]
4. No painel **Detalhes do Dispositivo**, o novo valor da latitude aparece inicialmente como uma propriedade desejada e o antigo valor aparece como uma propriedade relatada:
   
    ![Exibir propriedade relatada][img-editdevice3]
5. Atualmente, os dispositivos simulados na solução pré-configurada processa apenas as propriedades desejadas **Desired.Config.TemperatureMeanValue** e **Desired.Config.TelemetryInterval**. Um dispositivo real deve ler todas as propriedades desejadas no Hub IoT, fazer as alterações em sua configuração e informar os novos valores para o hub como propriedades relatadas.

No painel **Detalhes do Dispositivo**, você também pode editar o **Dispositivo Gêmeo - Marcações** da mesma maneira como edita o **Dispositivo Gêmeo - Propriedades Desejadas**. No entanto, ao contrário das propriedades desejadas, as marcações não são sincronizadas com o dispositivo. As marcações só existem no dispositivo gêmeo no Hub IoT. As marcações são úteis para criar filtros personalizados na lista de dispositivos.

## <a name="sort-the-device-list"></a>Classificar a lista de dispositivos

Você pode classificar a lista de dispositivos clicando ao lado de um título da coluna. O primeiro clique classifica em ordem ascendente, o segundo clique classifica em ordem descendente:

![Classificar a lista de dispositivos][img-sortdevices]

## <a name="filter-the-device-list"></a>Filtrar a lista de dispositivos

Na lista de dispositivos, você pode criar, salvar e recarregar os filtros para exibir uma lista personalizada de dispositivos conectados ao hub. Para criar um filtro:

1. Clique no ícone para editar o filtro acima da lista de dispositivos:
   
   ![Abrir o editor de filtro][img-editfiltericon]
2. No **Editor de filtro**, adicione os campos, operadores e valores para filtrar a lista de dispositivos. Você pode adicionar várias cláusulas para refinar seu filtro. Clique em **Filtrar** para aplicar o filtro:
   
   ![Crie um filtro][img-filtereditor]
3. Neste exemplo, a lista é filtrada pelo fabricante e número do modelo:
   
   ![Lista filtrada][img-filterelist]
4. Para salvar o filtro com um nome personalizado, clique no ícone **Salvar como**:
   
   ![Salvar um filtro][img-savefilter]
5. Para reaplicar um filtro salvo anteriormente, clique no ícone **Abrir filtro salvo**:
   
   ![Abrir um filtro][img-openfilter]

Você pode criar filtros com base na id do dispositivo, estado do dispositivo, propriedades desejadas, propriedades relatadas e marcações.

> [!NOTE]
> No **Editor de filtro**, você pode usar a **Exibição avançada** para editar o texto da consulta diretamente.
> 
> 

## <a name="add-a-rule-for-the-new-device"></a>Adicionar uma regra para o novo dispositivo
Não existem regras para o novo dispositivo recém-adicionado. Nesta seção, você adiciona uma regra que dispara um alarme quando a temperatura relatada pelo novo dispositivo excede 47 graus. Antes de começar, observe que o histórico de telemetria para o novo dispositivo no painel mostra que a temperatura do dispositivo nunca excede 45 graus.

1. Navegue de volta para a lista de dispositivos.
2. Para adicionar uma regra do dispositivo, selecione o novo dispositivo na **Lista de Dispositivos** e clique em **Adicionar regra**.
3. Crie uma regra que usa **Temperature** como o campo de dados e **AlarmTemp** como a saída quando a temperatura excede 47 graus:
   
    ![Adicionar uma regra de dispositivo][img-adddevicerule]
4. Para salvar suas alterações, clique em **Salvar e Exibir Regras**.
5. Clique em **Comandos** no painel de detalhes do novo dispositivo.
   
   ![Adicionar uma regra de dispositivo][img-adddevicerule2]
6. Selecione **ChangeSetPointTemp** na lista de comandos e defina **SetPointTemp** como 45. Em seguida, clique em **Enviar Comando**:
   
   ![Adicionar uma regra de dispositivo][img-adddevicerule3]
7. Navegue de volta para o painel da solução. Após um curto período, você verá uma nova entrada no painel **Histórico de Alarme** quando a temperatura relatada pelo seu novo dispositivo exceder o limite de 47 graus:
   
   ![Adicionar uma regra de dispositivo][img-adddevicerule4]
8. Você pode revisar e editar todas as regras na página **Regras** do painel:
   
    ![Listar regras de dispositivo][img-rules]
9. Você pode revisar e editar todas as ações que podem ser realizadas em resposta a uma regra na página **Ações** do painel:
   
    ![Listar ações de dispositivo][img-actions]

> [!NOTE]
> É possível definir ações que podem enviar uma mensagem de email ou SMS em resposta a uma regra ou se integrar com um sistema de linha de negócios por meio de um [Aplicativo Lógico][lnk-logic-apps]. Para obter mais informações, confira [Conectar o Aplicativo Lógico à solução pré-configurada de monitoramento remoto do Azure IoT Suite][lnk-logicapptutorial].
> 
> 

## <a name="disable-and-remove-devices"></a>Desabilitar e remover dispositivos
Você pode desabilitar um dispositivo e depois que ele for desabilitado, poderá removê-lo:

![Desabilitar e remover um dispositivo][img-disable]

## <a name="run-jobs"></a>Executar trabalhos
Você pode agendar trabalhos para executar operações em massa em seus dispositivos. Você cria um trabalho para uma lista de dispositivos. Essa lista pode conter todos os seus dispositivos ou ser uma lista filtrada que você criou usando as [ferramentas do filtro](#filter-the-device-list) na lista de dispositivos. Um trabalho pode chamar um método em cada dispositivo na lista ou pode atualizar o dispositivo gêmeo para cada dispositivo na lista de dispositivos.

### <a name="create-a-job-to-invoke-a-method"></a>Criar um trabalho para chamar um método

As etapas a seguir mostram como criar um trabalho que chama o método de atualização do firmware em cada dispositivo na lista. O método é chamado somente nos dispositivos que o suportam:

1. Use as ferramentas de filtro na lista de dispositivos para criar uma lista de dispositivos para receber a atualização do firmware:
   
   ![Abrir o editor de filtro][img-editfiltericon]
2. Na lista filtrada, clique em **Agendador de trabalhos**:
   
   ![Abrir o agendador de trabalhos][img-clickjobscheduler]
3. No painel **Agendador de trabalhos**, clique em **Chamar Método**.
4. Na página **Chamar Método**, insira os detalhes do método a chamar e clique em **Agendar**:
   
   ![Configurar método de trabalho][img-invokemethodjob]

O método **InitiateFirmwareUpdate** inicia uma tarefa assíncrona no dispositivo e retorna imediatamente. O processo de atualização do firmware, em seguida, usa as propriedades relatadas para informar o processo de atualização enquanto ele é executado.

### <a name="create-a-job-to-edit-the-device-twin"></a>Criar um trabalho para editar o dispositivo gêmeo

As etapas a seguir mostram como criar um trabalho que edita o dispositivo gêmeo em cada dispositivo na lista:

1. Use as ferramentas de filtro na lista de dispositivos para criar uma lista de dispositivos para receber as edições do dispositivo gêmeo:
   
   ![Abrir o editor de filtro][img-editfiltericon]
2. Na lista filtrada, clique em **Agendador de trabalhos**:
   
   ![Abrir o agendador de trabalhos][img-clickjobscheduler]
3. No painel **Agendar trabalho**, clique em **Editar Dispositivo Gêmeo**.
4. Na página **Editar Dispositivo Gêmeo**, insira os detalhes das **Propriedades Desejadas** e das **Marcações** a editar, em seguida, clique em **Agendar**:
   
   ![Configurar método de trabalho][img-edittwinjob]

### <a name="monitor-the-job"></a>Monitorar trabalho
Você pode monitorar o status dos trabalhos agendados na página **Trabalhos de Gerenciamento**. O painel **Detalhes do Trabalho** exibe informações sobre o trabalho selecionado:
   
   ![Exibir status do trabalho][img-jobstatus]

Você também pode exibir informações sobre os trabalhos no **Painel**:
   
   ![Exibir trabalhos no painel][img-jobdashboard]


## <a name="behind-the-scenes"></a>Nos bastidores
Ao implantar uma solução pré-configurada, o processo de implantação criará vários recursos na assinatura do Azure que você selecionou. Você pode exibir esses recursos no [portal][lnk-portal] do Azure. O processo de implantação cria um **grupo de recursos** com um nome baseado no nome escolhido para sua solução pré-configurada:

![Solução pré-configurada no portal do Azure][img-portal]

Você pode exibir as configurações de cada recurso selecionando-o na lista de recursos no grupo de recursos.

Você também pode exibir o código-fonte para a solução pré-configurada. O código-fonte da solução pré-configurada de monitoramento remoto está no repositório do GitHub [azure-iot-remote-monitoring][lnk-rmgithub]:

* A pasta **DeviceAdministration** contém o código-fonte para o painel.
* A pasta **Simulator** contém o código-fonte do dispositivo simulado.
* A pasta **EventProcessor** contém o código-fonte para o processo de back-end que manipula a telemetria de entrada.

Quando você terminar, poderá excluir a solução pré-configurada de sua assinatura do Azure no site [azureiotsuite.com][lnk-azureiotsuite]. Esse site permite que você exclua facilmente todos os recursos que foram provisionados quando criou a solução pré-configurada.

> [!NOTE]
> Para garantir que você excluirá tudo relacionado à solução pré-configurada, exclua-a no site [azureiotsuite.com][lnk-azureiotsuite] e não simplesmente exclua o grupo de recursos no portal.
> 
> 

## <a name="next-steps"></a>Próximas etapas
Agora que você implantou uma solução de trabalho pré-configurada, poderá continuar a introdução ao Suite IoT lendo os seguintes artigos:

* [Passo a passo da solução pré-configurada de monitoramento remoto][lnk-rm-walkthrough]
* [Conectar seu dispositivo à solução pré-configurada de monitoramento remoto][lnk-connect-rm]
* [Permissões no site azureiotsuite.com][lnk-permissions]

[img-launch-solution]: media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-devicelist]: media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-devicedetails]: media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-devicemethods]: media/iot-suite-getstarted-preconfigured-solutions/devicemethods.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
[img-pingmethod]: media/iot-suite-getstarted-preconfigured-solutions/pingmethod.png
[img-adddevice]: media/iot-suite-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: media/iot-suite-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: media/iot-suite-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: media/iot-suite-getstarted-preconfigured-solutions/runningnew.png
[img-runningnew-2]: media/iot-suite-getstarted-preconfigured-solutions/runningnew2.png
[img-rules]: media/iot-suite-getstarted-preconfigured-solutions/rules.png
[img-editdevice]: media/iot-suite-getstarted-preconfigured-solutions/editdevice.png
[img-editdevice2]: media/iot-suite-getstarted-preconfigured-solutions/editdevice2.png
[img-editdevice3]: media/iot-suite-getstarted-preconfigured-solutions/editdevice3.png
[img-adddevicerule]: media/iot-suite-getstarted-preconfigured-solutions/addrule.png
[img-adddevicerule2]: media/iot-suite-getstarted-preconfigured-solutions/addrule2.png
[img-adddevicerule3]: media/iot-suite-getstarted-preconfigured-solutions/addrule3.png
[img-adddevicerule4]: media/iot-suite-getstarted-preconfigured-solutions/addrule4.png
[img-actions]: media/iot-suite-getstarted-preconfigured-solutions/actions.png
[img-portal]: media/iot-suite-getstarted-preconfigured-solutions/portal.png
[img-disable]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_08.png
[img-columneditor]: media/iot-suite-getstarted-preconfigured-solutions/columneditor.png
[img-startimageedit]: media/iot-suite-getstarted-preconfigured-solutions/imagedit1.png
[img-imageedit]: media/iot-suite-getstarted-preconfigured-solutions/imagedit2.png
[img-sortdevices]: media/iot-suite-getstarted-preconfigured-solutions/sortdevices.png
[img-editfiltericon]: media/iot-suite-getstarted-preconfigured-solutions/editfiltericon.png
[img-filtereditor]: media/iot-suite-getstarted-preconfigured-solutions/filtereditor.png
[img-filterelist]: media/iot-suite-getstarted-preconfigured-solutions/filteredlist.png
[img-savefilter]: media/iot-suite-getstarted-preconfigured-solutions/savefilter.png
[img-openfilter]:  media/iot-suite-getstarted-preconfigured-solutions/openfilter.png
[img-clickjobscheduler]: media/iot-suite-getstarted-preconfigured-solutions/clickscheduler.png
[img-invokemethodjob]: media/iot-suite-getstarted-preconfigured-solutions/invokemethodjob.png
[img-edittwinjob]: media/iot-suite-getstarted-preconfigured-solutions/edittwinjob.png
[img-jobstatus]: media/iot-suite-getstarted-preconfigured-solutions/jobstatus.png
[img-jobdashboard]: media/iot-suite-getstarted-preconfigured-solutions/jobdashboard.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devicemetadata]: iot-suite-what-are-preconfigured-solutions.md#device-identity-registry-device-twin-and-documentdb
[lnk-logicapptutorial]: iot-suite-logic-apps-tutorial.md
[lnk-rm-walkthrough]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
