---
title: "Conectar o Intel Edison (C) ao IoT do Azure - Lição 1: configurar dispositivo | Microsoft Docs"
description: Configurar o Intel Edison para o primeiro uso.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "configuração do arduino, conectar arduino ao computador, configurar arduino, placa arduino"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-intel-edison-kit-c-get-started
ms.assetid: bb8aa45b-d3ff-4438-b9d6-a9725a45ade1
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 475b25f02715a60493e79ecd2170854019dfc4ac
ms.openlocfilehash: 800f1aed6d30d2bb871a6a9b55b6b95308932211
ms.contentlocale: pt-br
ms.lasthandoff: 01/25/2017

---
<a id="configure-your-intel-edison" class="xliff"></a>

# Configurar seu Intel Edison
<a id="what-you-will-do" class="xliff"></a>

## O que você fará
Configure o Intel Edison para o primeiro uso montando a placa, ligando-a e instalando a ferramenta de configuração no sistema operacional da área de trabalho para atualizar o firmware do Edison, definir sua senha e conectá-lo ao Wi-Fi. Se você tiver problemas, procure por soluções na [página de solução de problemas][troubleshooting].

<a id="what-you-will-learn" class="xliff"></a>

## O que você aprenderá
Neste artigo, você aprenderá:

* Como montar a placa do Edison e ligá-la.
* Como atualizar o firmware do Edison, definir a senha e conectar ao Wi-Fi.

<a id="what-you-need" class="xliff"></a>

## O que você precisa
Para concluir esta operação, você precisará das seguintes partes do seu Kit de início do Intel Edison:

* Módulo do Intel® Edison
* Placa de expansão Arduino
* As barras separadoras ou parafusos incluídos no pacote, incluindo dois parafusos para fixar o módulo à placa de expansão e quatro conjuntos de parafusos e espaçadores plásticos.
* Um cabo USB do tipo Micro B para Tipo A
* Uma fonte de alimentação CC (corrente contínua). A fonte de alimentação deve ser classificada da seguinte maneira:
  - 7-15V CC
  - Pelo menos 1500 mA
  - O pino central/interno deve ser o polo positivo da fonte de alimentação

  ![Coisas em seu Kit de Início](media/iot-hub-intel-edison-lessons/lesson1/kit.png)

Você também precisará de:

* Um computador executando o Windows, Mac ou Linux.
* Uma conexão sem fio com que o Edison se conectará.
* Uma conexão com a Internet para baixar a ferramenta de configuração.

<a id="assemble-your-board" class="xliff"></a>

## Montar sua placa

Esta seção contém etapas para anexar o módulo Intel® Edison à sua placa de expansão.

1. Coloque o módulo Intel® Edison dentro do contorno branco na placa de expansão, alinhando os orifícios no módulo aos parafusos na placa de expansão.

2. Pressione o módulo logo abaixo das palavras `What will you make?` até sentir um clique.

   ![montar a placa 2](media/iot-hub-intel-edison-lessons/lesson1/assemble_board2.jpg)

3. Use as duas porcas sextavadas (incluídas no pacote) para prender o módulo à placa de expansão.

   ![montar a placa 3](media/iot-hub-intel-edison-lessons/lesson1/assemble_board3.jpg)

4. Insira um parafuso em um dos quatro orifícios nos cantos na placa de expansão. Gire e aperte um dos espaçadores plásticos brancos no parafuso.

   ![montar a placa 4](media/iot-hub-intel-edison-lessons/lesson1/assemble_board4.jpg)

5. Repita para os espaçadores dos outros três cantos.

   ![montar a placa 5](media/iot-hub-intel-edison-lessons/lesson1/assemble_board5.jpg)

Agora, sua placa está montada.

   ![montar a placa](media/iot-hub-intel-edison-lessons/lesson1/assembled_board.jpg)

<a id="power-up-edison" class="xliff"></a>

## Ligar o Edison

1. Conecte à fonte de alimentação.

   ![Conectar à fonte de alimentação](media/iot-hub-intel-edison-lessons/lesson1/plug_power.jpg)

2. Um LED verde (rotulado como DS1 na placa de expansão Arduino*) deve acender e permanecer aceso.

3. Aguarde um minuto para a placa concluir a inicialização.

   > [!NOTE]
   > Se não tiver uma fonte de alimentação CC, você ainda poderá ligar a placa usando uma porta USB. Confira a seção `Connect Edison to your computer` para obter detalhes. Ligar sua placa dessa maneira pode resultar em um comportamento imprevisível da placa, especialmente ao usar Wi-Fi ou motores de acionamento.

<a id="connect-edison-to-your-computer" class="xliff"></a>

## Conectar o Edison ao computador

1. Mude a posição do microcomutador para baixo, na direção das duas portas micro USB, para que o Edison fique no modo de dispositivo. Veja [aqui](https://software.intel.com/en-us/node/628233#usb-device-mode-vs-usb-host-mode) as diferenças entre o modo de dispositivo e o modo de host.

   ![Mudar a posição do microcomutador para baixo](media/iot-hub-intel-edison-lessons/lesson1/toggle_down_microswitch.jpg)

2. Conecte o cabo micro USB à porta micro USB superior.

   ![Porta micro USB superior](media/iot-hub-intel-edison-lessons/lesson1/top_usbport.jpg)

3. Conecte a outra extremidade do cabo USB ao computador.

   ![USB do computador](media/iot-hub-intel-edison-lessons/lesson1/computer_usb.jpg)

4. Você saberá que a placa foi inicializada completamente quando o computador montar uma nova unidade (muito semelhante a inserir um cartão SD no computador).

<a id="download-and-run-the-configuration-tool" class="xliff"></a>

## Baixe e execute a ferramenta de configuração
Obtenha a ferramenta de configuração mais recente [deste link](https://software.intel.com/en-us/iot/hardware/edison/downloads), listada sob o título `Installers`. Execute a ferramenta e siga as instruções na tela, clicando em Avançar quando necessário

<a id="flash-firmware" class="xliff"></a>

### Atualizar o firmware
1. Na página `Set up options`, clique em `Flash Firmware`.
2. Selecione a imagem para atualizar sua placa seguindo um destes procedimentos:
   - Para baixar e atualizar sua placa com a imagem de firmware mais recente disponível da Intel, selecione `Download the latest image version xxxx`.
   - Para atualizar sua placa com uma imagem que você já salvou no computador, selecione `Select the local image`. Navegue e selecione a imagem com que você quer atualizar a placa.
3. A ferramenta de configuração tentará atualizar a placa. Todo o processo de atualização pode levar até 10 minutos.

<a id="set-password" class="xliff"></a>

### Definir senha
1. Na página `Set up options`, clique em `Enable Security`.
2. É possível definir um nome personalizado para sua placa Intel® Edison. Isso é opcional.
3. Digite uma senha para a placa e clique em `Set password`.
4. Marque a senha, que será usada mais tarde.

<a id="connect-wi-fi" class="xliff"></a>

### Conectar ao Wi-Fi
1. Na página `Set up options`, clique em `Connect Wi-Fi`. Aguarde até um minuto para que seu computador identifique as redes Wi-Fi disponíveis.
2. Na lista suspensa `Detected Networks`, selecione a rede.
3. Na lista suspensa `Security`, selecione o tipo de segurança da rede.
4. Forneça suas informações de logon e senha e clique em `Configure Wi-Fi`.
5. Marque o endereço IP, que será usado mais tarde.

> [!NOTE]
> Verifique se o Edison está conectado à mesma rede que o computador. Seu computador se conecta ao Edison usando o endereço IP.

Parabéns! Você configurou o Edison com êxito.

<a id="summary" class="xliff"></a>

## Resumo
Neste artigo, você aprendeu como montar a placa Edison, atualizar o firmware, definir a senha e conectá-la ao Wi-Fi usando a ferramenta de configuração. Observe que o LED ainda não está aceso. A tarefa seguinte é instalar as ferramentas e o software necessários para preparar a execução de um aplicativo de exemplo no Edison.

<a id="next-steps" class="xliff"></a>

## Próximas etapas
[Obter as ferramentas][get-the-tools]
<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-c-troubleshooting.md
[get-the-tools]: iot-hub-intel-edison-kit-c-lesson1-get-the-tools-win32.md
