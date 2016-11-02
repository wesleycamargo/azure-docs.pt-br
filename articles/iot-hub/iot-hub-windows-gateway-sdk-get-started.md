<properties
    pageTitle="Introdução ao SDK do Gateway do Hub IoT | Microsoft Azure"
    description="Passo a passo do SDK do Gateway do Hub IoT do Azure usando o Windows para ilustrar os principais conceitos que você deve compreender ao usar o SDK do Gateway do Hub IoT do Azure."
    services="iot-hub"
    documentationCenter=""
    authors="chipalost"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/25/2016"
     ms.author="andbuc"/>



# <a name="iot-gateway-sdk-(beta)---get-started-using-windows"></a>SDK do Gateway IoT (beta) - Introdução usando o Windows

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Como criar a amostra

Antes de começar, é necessário [configurar seu ambiente de desenvolvimento][lnk-setupdevbox] para funcionar com o SDK no Windows.

1. Abra um prompt de comando **Prompt de Comando do Desenvolvedor para VS2015** .
2. Navegue até a pasta raiz de sua cópia local do repositório **azure-iot-gateway-sdk** .
3. Execute o scpript **tools\\build.cmd**. Este script cria um arquivo de solução do Visual Studio, compila a solução e executa os testes. É possível encontrar a solução do Visual Studio na pasta **build** na cópia local do repositório **azure-iot-gateway-sdk**.

## <a name="how-to-run-the-sample"></a>Como executar a amostra

1. O script **build.cmd** cria uma pasta chamada **build** na cópia local do repositório. Esta pasta contém os dois módulos usados nesta amostra.

    O script de build coloca **logger_hl.dll** na pasta **build\\modules\\logger\\Debug** e **hello_world_hl.dll** na pasta **build\\modules\\hello_world\\Debug**. Use esses caminhos para o valor de **caminho do módulo** , conforme mostrado no arquivo de configurações do JSON abaixo.

2. O arquivo **hello_world_win.json** na pasta **samples\\hello_world\\src** é um arquivo de configurações do JSON de exemplo para o Windows que pode ser usado para executar a amostra. As definições do JSON de exemplo mostradas abaixo pressupõem que você clonou o repositório do SDK do Gateway na raiz da unidade **C:** . Se você o baixou em outro local, precisará ajustar os valores de **caminho do módulo** no arquivo **samples\\hello_world\\src\\hello_world_win.json** de acordo.

3. Para o módulo **logger_hl**, na seção **args**, defina o valor de **filename** para o nome e caminho do arquivo que conterá os dados de log.

    Este é um exemplo de um arquivo de configurações do JSON para o Windows que gravará no **log.txt**, na raiz da unidade **C:**.

    ```
    {
      "modules" :
      [
        {
          "module name" : "logger_hl",
          "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\logger\\Debug\\logger_hl.dll",
          "args" : 
          {
            "filename":"C:\\log.txt"
          }
        },
        {
          "module name" : "hello_world",
          "module path" : "C:\\azure-iot-gateway-sdk\\build\\\\modules\\hello_world\\Debug\\hello_world_hl.dll",
          "args" : null
        }
      ],
      "links" :
      [
        {
          "source": "hello_world",
          "sink": "logger_hl"
        }
      ]
    }
    ```

3. No prompt de comando, navegue até a pasta raiz de sua cópia local do repositório **azure-iot-gateway-sdk** .
4. Execute o comando a seguir:
  
  ```
  build\samples\hello_world\Debug\hello_world_sample.exe samples\hello_world\src\hello_world_win.json
  ```

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md


<!--HONumber=Oct16_HO2-->


