<properties
	pageTitle="Introdução às soluções pré-configuradas | Microsoft Azure"
	description="Siga este tutorial para aprender a implantar uma solução pré-configurada do Azure IoT Suite."
	services=""
	documentationCenter=""
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="na"
     ms.devlang="na"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="12/03/2015"
     ms.author="dobett"/>

# Tutorial: introdução às soluções da IoT pré-configuradas

## Introdução

As [soluções pré-configuradas][lnk-preconfigured-solutions] do Pacote do Azure IoT combinam vários serviços de IoT do Azure para fornecer soluções de ponta a ponta que implementam cenários comuns de negócios de IoT.

Este tutorial mostra como provisionar a solução pré-configurada de *monitoramento remoto*. Ele também explica os recursos básicos da solução pré-configurada de monitoramento remoto.

Para concluir este tutorial, você precisará de uma assinatura ativa do Azure.

> [AZURE.NOTE]Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][lnk_free_trial].

## Provisionamento de uma solução pré-configurada de monitoramento remoto

1.  Faça logon em [azureiotsuite.com][lnk-azureiotsuite] usando as credenciais de sua conta do Azure e clique em **+** para criar uma nova solução.

2.  Clique em **Selecionar** no bloco **Monitoramento remoto**.

3.  Digite um **Nome de solução** para a solução pré-configurada de monitoramento remoto.

4.  Selecione a **Região** e a **Assinatura** você deseja usar para provisionar a solução.

5.  Clique em **Criar Solução** para iniciar o processo de provisionamento. Isso normalmente leva vários minutos para ser executado.

## Exibir o painel de solução de monitoramento remoto

1.  Quando o provisionamento for concluído e o bloco da solução pré-configurada indicar **Pronto**, clique em **Iniciar** para abrir o portal de solução de monitoramento remoto em uma nova guia.

    ![][img-launch-solution]

2.  Por padrão, o portal de solução mostra o *painel de solução*. Você pode selecionar outros modos de exibição usando o menu à esquerda.

    ![][img-dashboard]

## A lista de dispositivos de solução é exibida

1.  Clique em **Dispositivos** no menu à esquerda para mostrar a *lista de dispositivos* dessa solução.

    ![][img-devicelist]

2.  Você pode ver que há quatro dispositivos simulados criados pelo processo de provisionamento.

3.  Clique em um dispositivo na lista de dispositivos para exibir os respectivos detalhes.

    ![][img-devicedetails]

## Enviar um comando para um dispositivo

1.  Clique em **Comandos** no painel de detalhes do dispositivo selecionado.

    ![][img-devicecommands]

2.  Selecione **PingDevice** na lista de comandos.

3.  Clique em **Enviar Comando**.

4.  Você pode ver o status do comando no histórico de comandos.

    ![][img-pingcommand]

## Adicionar um novo dispositivo simulado

1.  Navegue de volta para a lista de dispositivos.

2.  Clique em **+ Adicionar um Dispositivo** no canto inferior esquerdo para adicionar um novo dispositivo.

    ![][img-adddevice]

3.  Clique em **Adicionar Novo** no bloco **Dispositivo Simulado**.

    ![][img-addnew]

4.  Selecione **Deixe-me definir minha própria ID de dispositivo** e adicione um nome de ID de dispositivo exclusivo, como **mydevice\_01**.

5.  Clique em **Criar**.

    ![][img-definedevice]

6. Na etapa 3 de **Adicionar um dispositivo simulado**, clique em **Concluído** para retornar à lista de dispositivos.

7.  Você pode exibir o dispositivo **Executando** na lista de dispositivos.

    ![][img-runningnew]

## Exibir e editar regras de solução

1.  Retorne ao painel de solução e exiba o bloco **Histórico de Alarmes**.

    ![][img-alarmhistory]

2.  A regra **AlarmTemp** dispara esses alarmes.

3.  Clique em **Regras** no menu à esquerda para exibir as regras dessa solução.

    ![][img-rules]

4.  A solução pré-configurada provisiona duas regras.

5.  Clique na regra **Temperatura** na lista de regras para exibir as propriedades da regra.

6.  Clique em **Editar** no painel de propriedades da regra.

    ![][img-displayrule]

7.  Altere o **Limite** para 30 e mantenha todas as outras propriedades iguais.

8.  Clique em **Salvar e Exibir Regras**.

    ![][img-editrule]

9.  Retorne à tabela **Histórico de Alarmes** no **Painel de Solução** e observe a alteração no comportamento devido à regra atualizada.

    ![][img-newhistory]

## Próximas etapas

Agora que você criou uma solução de trabalho pré-configurada, você pode passar para os seguintes cenários:

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
[img-alarmhistory]: media/iot-suite-getstarted-preconfigured-solutions/alarmhistory.png
[img-rules]: media/iot-suite-getstarted-preconfigured-solutions/rules.png
[img-displayrule]: media/iot-suite-getstarted-preconfigured-solutions/displayrule.png
[img-editrule]: media/iot-suite-getstarted-preconfigured-solutions/editrule.png
[img-newhistory]: media/iot-suite-getstarted-preconfigured-solutions/newhistory.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-predictive]: iot-suite-predictive-overview.md

<!---HONumber=AcomDC_1210_2015-->