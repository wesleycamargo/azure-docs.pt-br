---
title: Carregar arquivos dos dispositivos no Hub IoT do Azure com Python| Microsoft Docs
description: "Como carregar arquivos de um dispositivo para a nuvem usando o SDK do dispositivo IoT do Azure para Python. Os arquivos carregados são armazenados em um contêiner de Azure Storage Blob."
services: iot-hub
documentationcenter: python
author: msebolt
manager: timlt
editor: 
ms.assetid: 4759d229-f856-4526-abda-414f8b00a56d
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/16/2018
ms.author: v-masebo
ms.openlocfilehash: ac871a03ebb93dac3b91f7df2220cd5f4506498b
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>Carregar arquivos do seu dispositivo para a nuvem com o Hub IoT

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Este tutorial descreve como usar os [recursos de carregamento de arquivos do Hub IoT](iot-hub-devguide-file-upload.md) para carregar um arquivo para [armazenamento de blobs do Azure](../storage/index.yml). Este tutorial mostra como:

- Fornece um contêiner de armazenamento com segurança para carregar um arquivo.
- Use o cliente do Python para carregar um arquivo por meio de seu Hub IoT.

Os tutoriais [Introdução ao Hub IoT](iot-hub-node-node-getstarted.md) e [Enviar mensagens da nuvem para o dispositivo com o Hub IoT](iot-hub-node-node-c2d.md) mostram a funcionalidade básica de mensagens do dispositivo para a nuvem e da nuvem para o dispositivo do Hub IoT. No entanto, em alguns cenários você não pode mapear facilmente os dados que seus dispositivos enviam em mensagens relativamente menores do dispositivo para a nuvem que o Hub IoT aceita. Quando você precisar carregar arquivos de um dispositivo, ainda poderá usar a segurança e a confiabilidade do Hub IoT.

> [!NOTE]
> SDK de Python de Hub IoT atualmente suporta apenas carregar arquivos baseados em caracteres, como arquivos **.txt**.

Ao final deste tutorial, você executará o aplicativo de console do Python:

* **FileUpload.py**, que carrega um arquivo para o armazenamento usando o SDK do dispositivo Python.

> [!NOTE]
> O Hub IoT é compatível com muitas plataformas de dispositivo e linguagens (incluindo C, .NET, Javascript, Python e Java) por meio dos SDKs do dispositivo IoT do Azure. Confira a [Central de Desenvolvedores do IoT do Azure] para obter instruções passo a passo sobre como conectar seu dispositivo ao Hub IoT do Azure.

Para concluir este tutorial, você precisará do seguinte:

* [Python 2.x or 3.x][lnk-python-download]. Certifique-se de usar a instalação de 32 bits ou 64 bits conforme exigido pelo seu programa de instalação. Quando solicitado durante a instalação, certifique-se de adicionar Python à variável de ambiente específica da plataforma. Se você estiver usando o Python 2. x, talvez seja necessário [instalar ou atualizar o *pip*, o sistema de gerenciamento de pacotes do Python][lnk-install-pip].
* Se você estiver usando o sistema operacional Windows, então o [Pacote redistribuível do Visual C++][lnk-visual-c-redist] permite o uso de DLLs nativas do Python.
* Uma conta ativa do Azure. (Se você não tiver uma conta, poderá criar uma [conta gratuita](http://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)


[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]


## <a name="upload-a-file-from-a-device-app"></a>Carregar um arquivo de um aplicativo de dispositivo

Nesta seção, você criará o aplicativo de dispositivo para carregar um arquivo no hub IoT.

1. No prompt de comando, execute o seguinte comando para instalar o pacote **azure-iothub-device-client**:

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Usando um editor de texto, crie um arquivo **FileUpload.py** em sua pasta de trabalho.

1. Adicione as instruções `import` e variáveis a seguir ao início do arquivo **FileUpload.py**. Substitua `deviceConnectionString` pela cadeia de conexão do dispositivo do Hub IoT:

    ```python
    import time
    import sys
    import iothub_client
    import os
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError

    CONNECTION_STRING = "{deviceConnectionString}"
    PROTOCOL = IoTHubTransportProvider.HTTP

    FILENAME = 'sample.txt'
    ```

1. Criar um retorno de chamada para a função **upload_blob**:

    ```python
    def blob_upload_conf_callback(result, user_context):
        if str(result) == 'OK':
            print ( "...file uploaded successfully." )
        else:
            print ( "...file upload callback returned: " + str(result) )
    ```

1. Adicione o código a seguir para se conectar ao cliente e carregar o arquivo. Também inclua a rotina `main`:

    ```python
    def iothub_file_upload_sample_run():
        try:
            print ( "IoT Hub file upload sample, press Ctrl-C to exit" )
        
            client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

            client.upload_blob_async(FILENAME, FILENAME, os.path.getsize(FILENAME), blob_upload_conf_callback, 0)
        
            print ( "" )
            print ( "File upload initiated..." )
        
            while True:
                time.sleep(30)

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
        except:
            print ( "generic error" )
        
    if __name__ == '__main__':
        print ( "Simulating a file upload using the Azure IoT Hub Device SDK for Python" )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_file_upload_sample_run()
    ```

1. Salve e feche o arquivo **UploadFile.py**.

1. Copiar um arquivo de texto de exemplo para a pasta de trabalho e renomeie-o `sample.txt`.

    > [!NOTE]
    > SDK de Python de Hub IoT atualmente suporta apenas carregar arquivos baseados em caracteres, como arquivos **.txt**.


## <a name="run-the-application"></a>Executar o aplicativo

Agora você está pronto para executar o aplicativo.

1. Em um prompt de comando na pasta de trabalho, execute o seguinte comando:

    ```cmd/sh
    python FileUpload.py
    ```

1. A captura de tela a seguir mostra a saída do aplicativo **FileUpload**:

    ![Saída do aplicativo simulated-device](./media/iot-hub-python-python-file-upload/1.png)

1. Você pode usar o portal para exibir o arquivo carregado no contêiner de armazenamento configurado:

    ![Arquivo carregado](./media/iot-hub-python-python-file-upload/2.png)


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar os recursos de carregamento de arquivo do Hub IoT para simplificar os carregamentos de arquivos de dispositivos. Você pode continuar explorando os recursos e cenários do Hub IoT com os seguintes artigos:

* [Criar um Hub IoT de modo programático][lnk-create-hub]
* [Introdução ao SDK de C][lnk-c-sdk]
* [SDKs do Azure IoT][lnk-sdks]

<!-- Links -->
[Central de Desenvolvedores do IoT do Azure]: http://azure.microsoft.com/develop/iot

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/