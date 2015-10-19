<properties
	pageTitle="Tutorial de introdução às soluções pré-configuradas do Microsoft Azure IoT Suite | Microsoft Azure"
	description="Siga este tutorial para aprender a implantar uma solução pré-configurada do Azure IoT Suite."
	services=""
	documentationCenter=".net"
	authors="aguilaaj"
	manager="timlt"
	editor=""/>

<tags
     ms.service="na"
     ms.devlang="na"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="tbd"
     ms.date="09/29/2015"
     ms.author="araguila"/>

# Introdução às soluções da IoT pré-configuradas

## Introdução

As soluções pré-configuradas do Pacote IoT do Azure conectam uma quantidade dos serviços IoT do Azure para exemplificar uma solução de ponta a ponta que atende um cenário de negócios da Internet das Coisas.

Esse tutorial mostra como provisionar uma solução pré-configurada, **Monitoramento remoto**. Isso também mostra como exibir os recursos básicos da solução de monitoramento remoto pré-configurado.

Para concluir este tutorial, você precisará do seguinte:

-   Uma assinatura ativa do Azure.

    Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][].

## Provisionar uma solução pré-configurada de monitoramento remoto

1.  Faça logon em https://www.azureiotsuite.com e clique em **+** para criar uma nova solução.

2.  Selecione **Monitoramento remoto** de acordo com o seu tipo de solução.

3.  Digite um **Nome de Solução** para a solução pré-configurada de monitoramento remoto.

4.  Valide a **Região** e **Assinatura** que você deseja para provisionar essa solução.

5.  Clique em **Criar solução**.

## Veja o painel de solução de monitoramento remoto

1.  Quando o provisionamento for concluído e o bloco da sua solução pré-configurada indicar **Pronto**, clique em **Iniciar** para abrir o painel de solução de monitoramento remoto em uma nova guia.

2.  Por padrão, o **Painel** está selecionado no menu esquerdo. Esse é o painel de solução.

## A lista de dispositivos de solução é exibida

1.  Clique em **Dispositivos** no menu à esquerda para navegar para a lista de dispositivos para essa solução.

2.  Mediante provisionamento você verá 4 dispositivos simulados provisionados.

3.  Clique um **dispositivo individual** na lista de dispositivos para exibir os detalhes de dispositivo associados a esse dispositivo.

## Enviar um comando para um dispositivo

1.  Clique em **Enviar comando** no painel de detalhes do dispositivo para o dispositivo simulado selecionado.

2.  Selecione **PingDevice** na lista de comandos.

3.  Clique em **Enviar comando**.

4.  Veja o status do comando como ele aparece no histórico de comandos.

## Adicionar um novo dispositivo simulado

1.  Clique na **←** (seta para voltar) para retornar à lista de dispositivos.

2.  Clique em **+ Adicionar um dispositivo** no canto inferior esquerdo para adicionar um novo dispositivo.

3.  Clique em **Adicionar novo** para o **Dispositivo simulado**.

4.  Selecione **Deixe-me definir minha própria ID de dispositivo** e adicione um nome de ID de dispositivo exclusivo.

5.  Clique em **Criar**.

6.  Clique na **←** (seta para voltar) para retornar à lista de dispositivos.

7.  Consulte o dispositivo **Executando** na lista de dispositivos.

## Exibir e editar regras de solução

1.  Observe a tabela **Histórico de alarme** no **Painel de solução.**

2.  Esses alarmes são acionados por uma saída de regra **AlarmTemp** especificada nas **Regras**.

3.  Clique em **Regras** no menu à esquerda para navegar nas regras para essa solução.

4.  Depois do provisionamento, você verá uma regra já habilitada.

5.  Clique em **regra** na lista de regras para exibir as propriedades de regra associadas.

6.  Clique em **Editar** no painel de propriedades da regra.

7.  Altere o **Limite** para 30 e mantenha todas as outras propriedades iguais.

8.  Clique em **Salvar e exibir regras**.

9.  Retorne à tabela do **Histórico de alarme** no **Painel de solução** e observe a alteração no gatilho devido à regra atualizada.

## Próximas etapas

Agora que você criou uma solução de trabalho pré-configurada, você pode passar para os seguintes cenários:

-   [Orientação sobre como personalizar soluções pré-configuradas][]

-   [Visão geral do IoT Suite][]

[Avaliação gratuita do Azure]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-BR%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F%20target=

[Orientação sobre como personalizar soluções pré-configuradas]: https://azure.microsoft.com/documentation/articles/iot-suite-guidance-on-customizing-preconfigured-solutions/
[Visão geral do IoT Suite]: https://azure.microsoft.com/documentation/articles/iot-suite-overview/

<!---HONumber=Oct15_HO2-->