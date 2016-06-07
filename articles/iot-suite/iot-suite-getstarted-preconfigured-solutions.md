<properties
	pageTitle="Introdução às soluções pré-configuradas | Microsoft Azure"
	description="Siga este tutorial para aprender a implantar uma solução pré-configurada do Azure IoT Suite."
	services=""
    suite="iot-suite"
	documentationCenter=""
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="05/25/2016"
     ms.author="dobett"/>

# Tutorial: introdução às soluções pré-configuradas

## Introdução

As [soluções pré-configuradas][lnk-preconfigured-solutions] do Pacote do Azure IoT combinam vários serviços de IoT do Azure para fornecer soluções de ponta a ponta que implementam cenários comuns de negócios de IoT.

Este tutorial mostra como provisionar a solução pré-configurada de *monitoramento remoto*. Ele também explica os recursos básicos da solução pré-configurada de monitoramento remoto.

Para concluir este tutorial, você precisará de uma assinatura ativa do Azure.

> [AZURE.NOTE]  Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][lnk_free_trial].

## Provisionamento de uma solução pré-configurada de monitoramento remoto

1.  Faça logon em [azureiotsuite.com][lnk-azureiotsuite] usando as credenciais de sua conta do Azure e clique em **+** para criar uma nova solução.

    > [AZURE.NOTE] Se você estiver tendo problemas com as permissões necessárias para provisionar uma solução, dê uma olhada em [Permissões no site azureiotsuite.com][lnk-permissions] para obter orientação.

2.  Clique em **Selecionar** no bloco **Monitoramento remoto**.

3.  Digite um **Nome de solução** para a solução pré-configurada de monitoramento remoto.

4.  Selecione a **Região** e a **Assinatura** você deseja usar para provisionar a solução.

5.  Clique em **Criar Solução** para iniciar o processo de provisionamento. Isso normalmente leva vários minutos para ser executado.

## Aguarde o processo de provisionamento ser concluído

1. Clique no bloco para sua solução com o status **provisionamento**.
 
2. Observe que o **Status provisionamento** como serviços do Azure é implantado em sua assinatura do Azure.

3. Após o provisionamento ser concluído, o status é alterado para **Pronto**.

4. Clique no bloco e você verá os detalhes da solução no painel à direita.

> [AZURE.NOTE] Se estiver encontrando problemas para implantar a solução pré-configurada, dê uma olhada em [Permissões no site do azureiotsuite.com][lnk-permissions] e [Perguntas frequentes][lnk-faq]. Se os problemas persistirem, crie um tíquete de serviço no [portal][lnk-portal].

Há detalhes que você esperaria ver e que não estão listados para sua solução? Envie sugestões de recursos em [User Voice](https://feedback.azure.com/forums/321918-azure-iot).

## Exibir o painel de solução de monitoramento remoto

O painel de solução permite que você gerencie a solução implantada. Por exemplo, você pode exibir telemetria, adicionar dispositivos e configurar regras.

1.  Quando o provisionamento for concluído e o bloco da solução pré-configurada indicar **Pronto**, clique em **Iniciar** para abrir o portal de solução de monitoramento remoto em uma nova guia.

    ![][img-launch-solution]

2.  Por padrão, o portal de solução mostra o *painel de solução*. Você pode selecionar outros modos de exibição usando o menu à esquerda.

    ![][img-dashboard]

O painel exibe as seguintes informações:

- O mapa exibe o local de cada dispositivo conectado à solução. Quando você executa a solução, há quatro dispositivos simulados. Os dispositivos simulados são implementados como trabalhos Web do Azure e a solução usa a API do Bing Mapas para plotar informações no mapa.
- O painel **Histórico de Telemetria** plota telemetria de umidade e temperatura de um dispositivo selecionado em tempo quase real e exibe dados de agregação, como umidade média, mínima e máxima.
- O painel **Histórico de Alarme** mostra eventos recentes de alarme quando um valor de telemetria excedeu um limite. Você pode definir seus próprios alarmes além dos exemplos criados pela solução pré-configurada.

## A lista de dispositivos de solução é exibida

A lista de dispositivos mostra todos os dispositivos registrados na solução. Você exibe e edita os metadados de dispositivo, adiciona ou remove dispositivos e envia comandos para dispositivos.

1.  Clique em **Dispositivos** no menu à esquerda para mostrar a *lista de dispositivos* dessa solução.

    ![][img-devicelist]

2.  A lista de dispositivos mostra que há quatro dispositivos simulados criados pelo processo de provisionamento.

3.  Clique em um dispositivo na lista de dispositivos para exibir os respectivos detalhes.

    ![][img-devicedetails]

O painel **Detalhes do Dispositivo** contém três seções:

- A seção **Ações** lista as ações que você pode executar no dispositivo. Se você desabilitar o dispositivo, ele não poderá enviar telemetria ou receber comandos. Se você desabilitar um dispositivo, poderá habilitá-lo novamente. Você pode adicionar uma regra associada ao dispositivo que dispara um alarme quando um valor de telemetria excede um limite. Você também pode enviar um comando a um dispositivo. Quando um dispositivo se conecta pela primeira vez, ele informa à solução os comandos que ele pode responder.
- A seção **Propriedades do Dispositivo** lista os metadados do dispositivo. Alguns metadados vêm do próprio dispositivo (por exemplo, o fabricante) e outros são gerados pela solução (como a hora de criação). Você pode editar os metadados do dispositivo a partir daqui.
- A seção **Chaves de Autenticação** lista as chaves que o dispositivo pode usar para autenticar na solução.

## Enviar um comando para um dispositivo

O painel de detalhes do dispositivo mostra todos os comandos aos quais determinado dispositivo dá suporte e permite que você envie comandos para um dispositivo. Quando um dispositivo é iniciado, ele envia informações sobre os comandos a que ele dá suporte para a solução.

1.  Clique em **Comandos** no painel de detalhes do dispositivo selecionado.

    ![][img-devicecommands]

2.  Selecione **PingDevice** na lista de comandos.

3.  Clique em **Enviar Comando**.

4.  Você pode ver o status do comando no histórico de comandos.

    ![][img-pingcommand]

A solução rastreia o status de cada comando enviado. Inicialmente, o resultado é **Pendente**. Quando o dispositivo reporta que executou o comando, o resultado é definido como **Sucesso**.

## Adicionar um novo dispositivo simulado

1.  Navegue de volta para a lista de dispositivos.

2.  Clique em **+ Adicionar um Dispositivo** no canto inferior esquerdo para adicionar um novo dispositivo.

    ![][img-adddevice]

3.  Clique em **Adicionar Novo** no bloco **Dispositivo Simulado**.

    ![][img-addnew]
    
    Além de criar um novo dispositivo simulado, você também poderá adicionar um dispositivo físico se optar por criar um **dispositivo personalizado**. Para saber mais sobre isso, confira [Conectar seu dispositivo à solução pré-configurada de monitoramento remoto do IoT Suite][lnk-connecting-devices].

4.  Selecione **Deixe-me definir minha própria ID de dispositivo** e adicione um nome de ID de dispositivo exclusivo, como **mydevice\_01**.

5.  Clique em **Criar**.

    ![][img-definedevice]

6. Na etapa 3 de **Adicionar um dispositivo simulado**, clique em **Concluído** para retornar à lista de dispositivos.

7. Você pode exibir o dispositivo **Executando** na lista de dispositivos.

    ![][img-runningnew]

8. Você também pode exibir a telemetria simulada do novo dispositivo no painel:

    ![][img-runningnew-2]

## Editar os metadados do dispositivo

1.  Navegue de volta para a lista de dispositivos.

2.  Selecione o novo dispositivo na **Lista de Dispositivos** e clique em **Editar** para editar as **propriedades do dispositivo**:

    ![][img-editdevice]

3. Role para baixo e altere os valores de latitude e longitude. Em seguida, clique em **Salvar alterações do registro de dispositivo**.

    ![][img-editdevice2]

4. Navegue de volta para o painel. O local do dispositivo foi alterado no mapa:

    ![][img-editdevice3]

## Adicionar uma regra para o novo dispositivo

Não existem regras para o novo dispositivo recém-adicionado. Nesta seção, você adicionará uma regra que dispara um alarme quando a temperatura relatada pelo novo dispositivo excede 47 graus. Antes de começar, observe que o histórico de telemetria para o novo dispositivo no painel mostra que a temperatura do dispositivo nunca excede 45 graus.

1.  Navegue de volta para a lista de dispositivos.

2.  Selecione o novo dispositivo na **Lista de Dispositivos** e clique em **Adicionar regra** para adicionar uma nova regra para o dispositivo.

3. Crie uma regra que usa **Temperature** como o campo de dados e **AlarmTemp** como a saída quando a temperatura excede 47 graus:

    ![][img-adddevicerule]

4. Clique em **Salvar e Exibir Regras** para salvar suas alterações.

5.  Clique em **Comandos** no painel de detalhes do novo dispositivo.

    ![][img-adddevicerule2]

6.  Selecione **ChangeSetPointTemp** na lista de comandos e defina **SetPointTemp** como 45. Em seguida, clique em** Enviar Comando**:

    ![][img-adddevicerule3]

7.  Navegue de volta para o painel da solução. Após um curto período, você verá uma nova entrada no painel **Histórico de Alarme** quando a temperatura relatada pelo seu novo dispositivo exceder o limite de 47 graus:

    ![][img-adddevicerule4]

8. Você pode revisar e editar todas as regras na página **Regras** do painel:

    ![][img-rules]

9. Você pode revisar e editar todas as ações que podem ser realizadas em resposta a uma regra na página **Ações** do painel:

    ![][img-actions]

> [AZURE.NOTE] É possível definir ações que podem enviar uma mensagem de email ou SMS em resposta a uma regra ou se integrar com um sistema de linha de negócios por meio de um [aplicativo lógico][lnk-logic-apps].

## Nos bastidores

Ao implantar uma solução pré-configurada, o processo de implantação criará vários recursos na assinatura do Azure que você selecionou. Você pode exibir esses recursos no [portal][lnk-portal] do Azure. O processo de implantação cria um **grupo de recursos** com um nome baseado no nome escolhido para sua solução pré-configurada:

![][img-portal]

Você pode exibir as configurações de cada recurso selecionando-o na lista de recursos no grupo de recursos. A captura de tela acima mostra as configurações para o Hub IoT usadas na solução pré-configurada.

Você também pode exibir o código-fonte para a solução pré-configurada. O código-fonte da solução pré-configurada de monitoramento remoto está em [azure-iot-remote-monitoring][lnk-rmgithub]\:

- A pasta **DeviceAdministration** contém o código-fonte para o painel.
- A pasta **Simulator** contém o código-fonte do dispositivo simulado.
- A pasta **EventProcessor** contém o código-fonte para o processo de back-end que manipula a telemetria de entrada.

Ao terminar, você poderá excluir a solução pré-configurada de sua assinatura do Azure no site [azureiotsuite.com][lnk-azureiotsuite]. Isso permitirá excluir facilmente todos os recursos que foram provisionados quando você criou a solução pré-configurada.

> [AZURE.NOTE] Para garantir que você excluirá tudo relacionado à solução pré-configurada, exclua-a de m.[azureiotsuite.com][lnk-azureiotsuite] e não simplesmente exclua o grupo de recursos no portal.

## Próximas etapas

Agora que você criou uma solução de trabalho pré-configurada, é possível passar para as instruções a seguir:

-   [Orientação sobre como personalizar soluções pré-configuradas][lnk-customize]
-   [Visão geral da solução pré-configurada de manutenção preditiva][lnk-predictive]

[img-launch-solution]: media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-devicelist]: media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-devicedetails]: media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
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

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-predictive]: iot-suite-predictive-overview.md
[lnk-connecting-devices]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-faq]: iot-suite-faq.md

<!---HONumber=AcomDC_0601_2016-->