---
title: Receber um email quando a porta é aberta usando o serviço SendGrid e Azure Functions | Microsoft Docs
description: Monitore o sensor magnético para detectar quando uma porta é aberta e use o Azure Functions para enviar uma notificação por email.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/19/2018
ms.author: liydu
ms.openlocfilehash: 80537bb817baf20831ad5020f0db775f21836f92
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34632128"
---
# <a name="door-monitor"></a>Monitor de porta          

O Kit de Desenvolvimento de IoT MXChip contém um sensor magnético integrado. Neste projeto, você detecta a presença ou ausência de um campo de magnético forte próximo – nesse caso, proveniente de um pequeno. ímã permanente.

## <a name="what-you-learn"></a>O que você aprenderá

Neste projeto, você aprenderá a:
- Como usar o sensor magnético do Kit de Desenvolvimento de IoT MXChip para detectar a movimentação de um ímã próximo.
- Como usar o serviço SendGrid para enviar uma notificação para seu endereço de email.

> [!NOTE]
> Para um uso prático deste projeto:
> - Fixe um ímã na borda de uma porta.
> - Monte o Kit de Desenvolvimento no batente da porta próximo ao ímã. A abertura ou o fechamento da porta irá disparar o sensor, resultando no recebimento de uma notificação por email do evento.

## <a name="what-you-need"></a>O que você precisa

Siga o [Guia de Introdução]({{"/docs/get-started/" | absolute_url }}) para:

* Ter seu DevKit conectado ao Wi-Fi
* Preparar o ambiente de desenvolvimento

Uma assinatura ativa do Azure. Se você não tiver uma, você pode registrar por meio de um desses métodos:

* Ative uma [conta de avaliação do Microsoft Azure por 30 dias](https://azure.microsoft.com/free/).
* Declare seu [crédito do Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) se você for assinante do MSDN ou o Visual Studio.

## <a name="deploy-sendgrid-service-in-azure"></a>Implementar o serviço SendGrid no Azure

[SendGrid](https://sendgrid.com/) é uma plataforma de entrega de email com base na nuvem. Esse serviço será usado para enviar notificações por email.

> [!NOTE]
> Se você já tiver implantado um serviço SendGrid, poderá continuar diretamente em [Implantar Hub IoT no Azure](#deploy-iot-hub-in-azure).

### <a name="sendgrid-deployment"></a>Implantação do SendGrid

Para provisionar os serviços do Azure, use o botão **Implantar no Azure**. Esse botão permite a implantação rápida e fácil de seus projetos de código-fonte aberto no Microsoft Azure.

Clique no botão **Implantar no Azure** abaixo. 

[![Implantar no Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2FSendGridDeploy%2Fazuredeploy.json)

Veja a página a seguir.

> [!NOTE]
> Se você não vir a página a seguir, poderá ter de primeiro entrar na sua conta do Azure.

Preencha o formulário de inscrição:

  * **Grupo de recursos**: criar um grupo de recursos para hospedar serviço SendGrid ou usar um existente. Veja [Uso dos grupos de recursos para gerenciar seus recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

  * **Nome**: O nome do serviço SendGrid. Escolha um nome exclusivo, diferente de outros serviços que você possa ter.

  * **Senha**: O serviço requer uma senha, a qual não será usada para nada mais neste projeto.

  * **Email**: O serviço SendGrid enviará a verificação para esse endereço de email.

  > [!NOTE]
  > Marque a opção **Fixar no painel** para facilitar a localização do aplicativo no futuro.
 
![Implantação do SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-deploy.png)

### <a name="sendgrid-api-key-creation"></a>Criação da Chave de API do SendGrid

Depois que implantação concluída com êxito, clique nela e, em seguida, clique no botão **Gerenciar**. Você será direcionado para a página do SendGrid e precisará verificar seu endereço de email.

![Gerenciamento do SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-manage.png)

Na página do SendGrid, clique em **Configurações** > **Chaves de API** > **Criar chave da API**. Insira o **Nome da chave da API** e clique em **Criar e exibir**.

![Criar primeira API do SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-first.png)

![Criar segunda API do SendGrid](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-second.png)

Sua chave de API é exibida apenas uma vez. Certifique-se de copiá-la e armazená-la com segurança, pois ela será usada na próxima etapa.

## <a name="deploy-iot-hub-in-azure"></a>Implantar um Hub IoT no Azure

As etapas a seguir provisionarão outros serviços relacionados a IoT do Azure e implantarão Azure Functions para esse projeto.

Clique no botão **Implantar no Azure** abaixo. 

[![Implantar no Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2Fazuredeploy.json)

Veja a página a seguir.

> [!NOTE]
> Se você não vir a página a seguir, poderá ter de primeiro entrar na sua conta do Azure.

Preencha o formulário de inscrição:

  * **Grupo de recursos**: criar um grupo de recursos para hospedar serviço SendGrid ou usar um existente. Veja [Uso dos grupos de recursos para gerenciar seus recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

  * **Nome do Hub IoT**: O nome para o hub IoT. Escolha um nome exclusivo, diferente de outros serviços que você possa ter.

  * **Sku do Hub IOT**: F1 (limitado a um por assinatura) é gratuito. Você pode ver mais informações sobre preços em [tipo de preço e dimensionamento](https://azure.microsoft.com/pricing/details/iot-hub/).

  * **Do Email**: Deve ser o mesmo endereço de email que você usou ao configurar o serviço SendGrid.

  > [!NOTE]
  > Marque a opção **Fixar no painel** para facilitar a localização do aplicativo no futuro.
 
![Implantação de Hub IOT](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/iot-hub-deploy.png)

## <a name="build-and-upload-the-code"></a>Compilação e carregamento do código

### <a name="start-vs-code"></a>Iniciar o VS Code

- Verifique se o Kit de Desenvolvimento **não** está conectado ao computador.
- Iniciar o VS Code.
- Conecte o DevKit ao computador.

> [!NOTE]
> Ao iniciar o VS Code, você receberá uma mensagem de erro que informa que não é possível localizar o Arduino IDE ou o pacote de quadro relacionado. Se você receber esse erro, feche o VS Code e inicialize o Arduino IDE uma vez, assim o VS Code localizará o caminho do Arduino IDE corretamente.

### <a name="open-arduino-examples-folder"></a>Abra a pasta de exemplos de Arduino

Expanda a seção **EXEMPLOS DO ARDUINO** no canto esquerdo, vá até **Exemplos para MXCHIP AZ3166 > AzureIoT** e selecione **DoorMonitor**. Essa ação abrirá uma nova janela do VS Code com a pasta de projeto nela.

![Exemplos de minisolução](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-examples.png)

> [!NOTE]
> Você também pode abrir o exemplo da paleta de comandos. Use `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) para abrir a paleta de comandos, digite **Arduino** e, em seguida, localize e selecione **Arduino: Exemplos**.

### <a name="provision-azure-services"></a>Provisionamento dos serviços do Azure

Na janela de solução, execute a tarefa de provisionamento de nuvem:
- Digite `Ctrl+P` (macOS: `Cmd+P`).
- Digite `task cloud-provision` na caixa de texto fornecida.

No terminal do VS Code, uma linha de comando interativa orienta você durante o provisionamento dos serviços do Azure necessários. Selecione todos os mesmos itens da lista solicitada que você forneceu anteriormente em [Implantar Hub IoT no Azure](#deploy-iot-hub-in-azure).

![Provisionamento de nuvem](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/cloud-provision.png)

> [!NOTE]
> Se a página trava no status de carregamento ao tentar entrar no Azure, consulte as [Perguntas Frequentes](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure) para resolver esse problema. 

### <a name="build-and-upload-the-device-code"></a>Compilação e carregamento do código de dispositivo

#### <a name="windows"></a>Windows

1. Use `Ctrl+P` para executar `task device-upload`.
2. O terminal solicita que você entre no modo de configuração. Para fazer isso, mantenha o botão A pressionado e, em seguida, solte o botão Reiniciar. A tela exibe o número de identificação do Kit de Desenvolvimento e a palavra *Configuração*.

Esse procedimento define a cadeia de caracteres de conexão que é recuperada da etapa [Provisionar serviços do Azure](#provision-azure-services).

O VS Code então inicia verificando e carregando o esboço Arduino para o Kit de Desenvolvimento:

![device-upload](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

O Kit de Desenvolvimento reinicia e começa a execução do código.

> [!NOTE]
> Ocasionalmente, você pode receber uma mensagem de erro "Erro: AZ3166: pacote desconhecido". Esse erro ocorre quando o índice do pacote de quadro não é atualizado corretamente. Para resolver esse erro, consulte as [Perguntas Frequentes](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

#### <a name="macos"></a>macOS

1. Coloque o Kit de Desenvolvimento no modo de configuração: mantenha o botão A pressionado e, em seguida, pressione e solte o botão Reiniciar. A tela exibe “Configuração”.
2. Use `Cmd+P` para executar `task device-upload`.

Esse procedimento define a cadeia de caracteres de conexão que é recuperada da etapa [Provisionar serviços do Azure](#provision-azure-services).

O VS Code então inicia verificando e carregando o esboço Arduino para o Kit de Desenvolvimento:

![device-upload](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

O Kit de Desenvolvimento reinicia e começa a execução do código.

> [!NOTE]
> Ocasionalmente, você pode receber uma mensagem de erro "Erro: AZ3166: pacote desconhecido". Esse erro ocorre quando o índice do pacote de quadro não é atualizado corretamente. Para resolver esse erro, consulte as [Perguntas Frequentes](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Teste do projeto

O programa primeiro inicializa quando o Kir de Desenvolvimento está na presença de um campo magnético estável.

Após a inicialização, `Door closed` é exibido na tela. Quando há uma alteração no campo magnético, o estado muda para `Door opened`. Cada vez que o estado da porta mudar, você receberá uma notificação por email. (Essas mensagens de email podem levar até cinco minutos para serem recebidas.)

![Ímãs perto do sensor: porta fechada](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-closed.jpg "Ímãs perto do sensor: porta fechada")

![Ímã afastado do sensor: porta aberta](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-opened.jpg "Ímã afastado do sensor: porta aberta")

## <a name="problems-and-feedback"></a>Comentários e problemas

Se você encontrar problemas, consulte [Perguntas frequentes](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou conecte-se usando canais abaixo:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Próximas etapas

Você aprendeu como conectar um dispositivo de Kit de Desenvolvimento ao acelerador de solução de Monitoramento Remoto do Azure IoT e usar o serviço SendGrid para enviar um email. Aqui estão sugestões para as próximas etapas:

* [Visão geral do acelerador de solução de Monitoramento Remoto do Azure IoT](https://docs.microsoft.com/azure/iot-suite/)
* [Conectar um dispositivo MXChip IoT DevKit ao seu aplicativo Azure IoT Central](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
