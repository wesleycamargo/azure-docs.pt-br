---
title: Configurar NUC da Intel como gateway IoT do Azure | Microsoft Docs
description: "Configure a NUC da Intel para funcionar como um gateway IoT entre um sensor e o Hub IoT do Azure para coletar informações do sensor e enviá-las para o Hub IoT."
services: iot-hub
documentationcenter: 
author: shizn
manager: yjianfeng
tags: 
keywords: gateway IoT, NUC da Intel, computador NUC, DE3815TYKE
ms.assetid: 917090d6-35c2-495b-a620-ca6f9c02b317
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 1c4f44787a7200a1c3634b258df32d30152daa90
ms.openlocfilehash: ef479f503ac4e911f3a9d311d0f436d95396eedd


---
# <a name="set-up-intel-nuc-as-an-iot-gateway"></a>Configurar a NUC da Intel como um gateway IoT

## <a name="what-you-will-do"></a>O que você fará

- Configure a NUC da Intel como um gateway IoT.
- Instale o pacote SDK do gateway IoT do Azure na NUC da Intel.
- Execute um aplicativo de exemplo "hello_world" na NUC da Intel para verificar a funcionalidade do gateway.
Se você tiver problemas, procure as soluções na [página de solução de problemas](iot-hub-gateway-kit-c-troubleshooting.md).

## <a name="what-you-will-learn"></a>O que você aprenderá

Nesta lição, você aprenderá a:

- Como conectar a NUC da Intel com periféricos.
- Como instalar e atualizar os pacotes necessários na NUC da Intel usando o Smart Package Manager.
- Como executar o aplicativo de exemplo "hello_world" para verificar a funcionalidade do gateway.

## <a name="what-you-need"></a>O que você precisa

- Um Kit DE3815TYKE da NUC da Intel com o Pacote de Software do Gateway IoT da Intel (Wind River Linux *7.0.0.13) pré-instalado.
- Um cabo Ethernet.
- Um teclado.
- Um cabo HDMI ou VGA.
- Um monitor com uma porta HDMI ou VGA.

![Kit de Gateway](media/iot-hub-gateway-kit-lessons/lesson1/kit.png)

## <a name="connect-intel-nuc-with-the-peripherals"></a>Conectar a NUC da Intel com os periféricos

A imagem abaixo é um exemplo de NUC da Intel que está conectada com vários periféricos:

1. Conectada a um teclado.
2. Conectada ao monitor por um cabo VGA ou HDMI.
3. Conectada a uma rede com fio por um cabo Ethernet.
4. Conectado à fonte de alimentação por um cabo de alimentação.

![NUC da Intel conectada a periféricos](media/iot-hub-gateway-kit-lessons/lesson1/nuc.png)

## <a name="connect-to-the-intel-nuc-system-from-host-computer-via-secure-shell-ssh"></a>Conectar-se ao sistema da NUC da Intel do computador host via SSH (Secure Shell)

Aqui, você precisa de teclado e o monitor para obter o endereço IP do dispositivo NUC. Se você já souber o endereço IP, você poderá pular para a etapa 3 nesta seção.

1. Ative a NUC da Intel pressionando o botão de energia e faça logon no sistema.

   O nome de usuário padrão e a senha são ambos `root`.

2. Obtenha o endereço IP da NUC executando o comando `ifconfig`. Esta etapa é realizada no dispositivo NUC.

   Aqui está um exemplo da saída do comando.

   ![saída ifconfig mostrando o IP da NUC](media/iot-hub-gateway-kit-lessons/lesson1/ifconfig.png)

   Neste exemplo, o valor após `inet addr:` é o endereço IP necessário quando você planeja se conectar remotamente de um computador host à NUC da Intel.

3. Use um dos seguintes clientes SSH do seu computador host para se conectar à NUC da Intel.

   - [PuTTY](http://www.putty.org/) para Windows.
   - O cliente SSH integrado no Ubuntu ou macOS.

   É mais eficiente e produtivo operar na NUC da Intel desde um computador host. Você precisa do endereço IP, nome de usuário e senha para conectar a NUC via cliente SSH. Aqui está o exemplo de uso de cliente SSH no macOS.
   ![Cliente SSH em execução no macOS](media/iot-hub-gateway-kit-lessons/lesson1/ssh.png)

## <a name="install-the-azure-iot-gateway-sdk-package"></a>Instalar o pacote SDK do gateway IoT do Azure

O pacote do SDK do Gateway IoT do Azure contém os binários pré-compilados do SDK e suas dependências. Esses binários são o SDK do Gateway IoT do Azure, o SDK de IoT do Azure e as ferramentas correspondentes. O pacote também contém um aplicativo de exemplo "hello_world" usado para validar a funcionalidade de gateway. O SDK é a parte principal do gateway. Para instalar o pacote, siga estas etapas:

1. Adicione o repositório de nuvem IoT executando os seguintes comandos em uma janela de terminal:

   ```bash
   rpm --import http://iotdk.intel.com/misc/iot_pub.key
   smart channel --add IoT_Cloud type=rpm-md name="IoT_Cloud" baseurl=http://iotdk.intel.com/repos/iot-cloud/wrlinux7/rcpl13/ -y
   ```

   O comando `rpm` importa a chave de rpm. O comando `smart channel` adiciona o canal de rpm para o Smart Package Manager. Antes de executar o comando `smart update`, você verá uma saída semelhante à mostrada abaixo.

   ![saída de comandos de canal inteligente e rpm](media/iot-hub-gateway-kit-lessons/lesson1/rpm_smart_channel.png)

   ```bash
   smart update
   ```

2. Execute o comando a seguir para instalar o pacote:

   ```bash
   smart install packagegroup-cloud-azure -y
   ```

   `packagegroup-cloud-azure` é o nome do pacote. O comando `smart install` é usado para instalar o pacote.

   Depois que o pacote é instalado, espera-se que a NUC da Intel funcione como um gateway.

## <a name="run-the-azure-iot-gateway-sdk-helloworld-sample-application"></a>Execute o aplicativo de exemplo "hello_world" do SDK do Gateway IoT do Azure

Vá para `azureiotgatewaysdk/samples` e execute o aplicativo de exemplo "hello_world". Este aplicativo de exemplo cria um gateway do arquivo `hello_world.json` e usa os componentes fundamentais da arquitetura do SDK do Gateway IoT do Azure para registrar uma mensagem hello world em um arquivo a cada 5 segundos.

Implantar e executar o aplicativo de exemplo "hello_world" executando o seguinte comando:

```bash
cd /usr/share/azureiotgatewaysdk/samples/hello_world/
./hello_world hello_world.json
```

O aplicativo de exemplo produzirá a seguinte saída se a funcionalidade de gateway estiver funcionando corretamente:

![saída do aplicativo](media/iot-hub-gateway-kit-lessons/lesson1/hello_world.png)

Se você tiver problemas, procure as soluções na [página de solução de problemas](iot-hub-gateway-kit-c-troubleshooting.md).

## <a name="summary"></a>Resumo

Parabéns! Você terminou de configurar a NUC da Intel como um gateway. Agora você está pronto para passar para a próxima lição para configurar o computador host, criar um Hub IoT do Azure e registrar o dispositivo lógico do Hub IoT do Azure.

## <a name="next-steps"></a>Próximas etapas
[Prepare o computador host e o Hub IoT do Azure](iot-hub-gateway-kit-c-lesson2-get-the-tools-win32.md)



<!--HONumber=Dec16_HO3-->


