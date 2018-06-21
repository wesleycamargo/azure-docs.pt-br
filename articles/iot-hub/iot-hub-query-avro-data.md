---
title: Consultar dados do Avro usando o Azure Data Lake Analytics | Microsoft Docs
description: Use propriedades do corpo de mensagem a telemetria de dispositivo de rota para o armazenamento de blob e consultar os dados de formato Avro gravados no armazenamento de blob.
services: iot-hub
documentationcenter: ''
author: ksaye
manager: obloch
ms.service: iot-hub
ms.topic: article
ms.date: 05/29/2018
ms.author: Kevin.Saye
ms.openlocfilehash: 98a30155c73a937042b4bea6568543fb5152d748
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726596"
---
# <a name="query-avro-data-using-azure-data-lake-analytics"></a>Consultar dados Avro usando a análise do Azure Data Lake

Este artigo é sobre como consultar dados da Avro para roteamento eficiente de mensagens do Azure IoT Hub aos serviços do Azure. Após o lançamento de postagem de blog —[Roteamento de mensagens de IoT Hub do Azure: agora com o roteamento no corpo da mensagem], IoT Hub dá suporte a roteamento em propriedades ou o corpo da mensagem. Consulte também [roteamento em corpos de mensagens][Routing on message bodies]. 

O desafio foi que quando o Azure IoT Hub roteia mensagens para o armazenamento de blob, o IoT Hub grava o conteúdo no formato Avro, que tem o corpo da mensagem e propriedades da mensagem. Observe que o IoT Hub só oferece suporte a gravação de dados para armazenamento de BLOBs no formato de dados Avro, e esse formato não é usado para outros pontos de extremidade. Consulte [quando usar contêineres de armazenamento do Azure][When using Azure storage containers]. Embora o formato Avro é ótimo para preservação de dados/mensagem, é um desafio para consultar os dados. Em comparação, o formato JSON ou CSV é muito mais fácil para consultar dados.

Para resolver isso, você pode usar muitos dos padrões de dados grandes para transformar e dados de dimensionamento para lidar com necessidades grandes de dados não relacionais e formatos. Um dos padrões, um padrão de "pagar por consulta", é o Azure Data Lake Analytics (ADLA). É o foco deste artigo. Embora facilmente, você pode executar a consulta no Hadoop ou outras soluções, ADLA é geralmente mais adequado para essa abordagem "pagar por consulta". Há um "extrator" para Avro em U-SQL. Consulte [Exemplo de Avro U-SQL].

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Consultar e exportar dados da Avro para um arquivo CSV
A seção orienta você por consultando dados Avro e exportá-lo para um arquivo CSV em armazenamento de BLOBs do Azure, embora você pode facilmente colocar os dados em outros armazenamentos de dados ou repositórios.

1. Configure o Azure IoT Hub para dados de rota para um ponto de extremidade de armazenamento de BLOBs do Azure usando uma propriedade no corpo da mensagem para selecionar as mensagens.

    ![Captura de tela de etapa 1a][img-query-avro-data-1a]

    ![Captura de tela de etapa 1b][img-query-avro-data-1b]

2. Certifique-se de que o dispositivo tenha a codificação, o tipo de conteúdo e os dados necessários no corpo da mensagem, conforme mencionado na documentação do produto ou as propriedades. Quando exibido no Gerenciador de dispositivo (veja abaixo), você pode verificar se esses atributos estão definidos corretamente.

    ![Captura de tela para a etapa 2][img-query-avro-data-2]

3. Configure um Azure Data Lake ADLS (repositório) e uma instância de análise do Azure Data Lake. Enquanto o Azure IoT Hub não rotear para um repositório Azure Data Lake, ADLA requer que um.

    ![Captura de tela para a etapa 3][img-query-avro-data-3]

4. ADLA, configure o armazenamento de BLOBs do Azure como um armazenamento adicional, o mesmo armazenamento de Blob do Azure IoT Hub encaminha dados.

    ![Captura de tela para a etapa 4][img-query-avro-data-4]
 
5. Conforme discutido em [Exemplo de Avro U-SQL], há 4 DLLs que são necessários.  Carregar esses arquivos para um local no seu ADLS.

    ![Captura de tela para a etapa 5][img-query-avro-data-5] 

6. No Visual Studio, crie um projeto de U-SQL
 
    ![Captura de tela para a etapa 6][img-query-avro-data-6]

7. Copie o conteúdo do script a seguir e cole-o arquivo recém-criado. Modificar as seções realçadas 3: sua conta ADLA, caminhos das DLLs associadas e o caminho correto para sua conta de armazenamento.
    
    ![Captura de tela de etapa 7a][img-query-avro-data-7a]

    O script U-SQL real para uma saída simple para CSV:
    
    ```sql
        DROP ASSEMBLY IF EXISTS [Avro];
        CREATE ASSEMBLY [Avro] FROM @"/Assemblies/Avro/Avro.dll";
        DROP ASSEMBLY IF EXISTS [Microsoft.Analytics.Samples.Formats];
        CREATE ASSEMBLY [Microsoft.Analytics.Samples.Formats] FROM @"/Assemblies/Avro/Microsoft.Analytics.Samples.Formats.dll";
        DROP ASSEMBLY IF EXISTS [Newtonsoft.Json];
        CREATE ASSEMBLY [Newtonsoft.Json] FROM @"/Assemblies/Avro/Newtonsoft.Json.dll";
        DROP ASSEMBLY IF EXISTS [log4net];
        CREATE ASSEMBLY [log4net] FROM @"/Assemblies/Avro/log4net.dll";

        REFERENCE ASSEMBLY [Newtonsoft.Json];
        REFERENCE ASSEMBLY [log4net];
        REFERENCE ASSEMBLY [Avro];
        REFERENCE ASSEMBLY [Microsoft.Analytics.Samples.Formats];

        // Blob container storage account filenames, with any path
        DECLARE @input_file string = @"wasb://hottubrawdata@kevinsayazstorage/kevinsayIoT/{*}/{*}/{*}/{*}/{*}/{*}";
        DECLARE @output_file string = @"/output/output.csv";

        @rs =
        EXTRACT
        EnqueuedTimeUtc string,
        Body byte[]
        FROM @input_file

        USING new Microsoft.Analytics.Samples.Formats.ApacheAvro.AvroExtractor(@"
        {
        ""type"":""record"",
        ""name"":""Message"",
        ""namespace"":""Microsoft.Azure.Devices"",
        ""fields"":[{
        ""name"":""EnqueuedTimeUtc"",
        ""type"":""string""
        },
        {
        ""name"":""Properties"",
        ""type"":{
        ""type"":""map"",
        ""values"":""string""
        }
        },
        {
        ""name"":""SystemProperties"",
        ""type"":{
        ""type"":""map"",
        ""values"":""string""
        }
        },
        {
        ""name"":""Body"",
        ""type"":[""null"",""bytes""]
        }
        ]
        }");

        @cnt =
        SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
        FROM @rs;

        OUTPUT @cnt TO @output_file USING Outputters.Text(); 
    ```    

    Executar o script mostrado abaixo, ADLA levou 5 minutos quando limitado a 10 unidades analítica e processado 177 arquivos, resumir a saída para um arquivo CSV.
    
    ![Captura de tela de etapa 7b][img-query-avro-data-7b]

    Exibindo a saída, você pode ver que o conteúdo de Avro foi convertido para um arquivo CSV. Vá para a etapa 8 para analisar o JSON.
    
    ![Captura de tela para a etapa 7c][img-query-avro-data-7c]

    
8. A maioria das mensagens de IoT estão no formato JSON.  Adicionar linhas a seguir, você pode analisar a mensagem em JSON, você pode adicionar as cláusulas WHERE e apenas os dados necessários de saída.

    ```sql
       @jsonify = SELECT Microsoft.Analytics.Samples.Formats.Json.JsonFunctions.JsonTuple(Encoding.UTF8.GetString(Body)) AS message FROM @rs;
    
        /*
        @cnt =
            SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
            FROM @rs;
        
        OUTPUT @cnt TO @output_file USING Outputters.Text();
        */
        
        @cnt =
            SELECT message["message"] AS iotmessage,
                   message["event"] AS msgevent,
                   message["object"] AS msgobject,
                   message["status"] AS msgstatus,
                   message["host"] AS msghost
            FROM @jsonify;
            
        OUTPUT @cnt TO @output_file USING Outputters.Text();
    ```

9. Exibindo a saída, você verá colunas para cada item no comando select. 
    
    ![Captura de tela para a etapa 8][img-query-avro-data-8]

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como consultar dados Avro para roteamento eficiente de mensagens do Azure IoT Hub aos serviços do Azure.

Para ver exemplos de soluções completas que usam o Hub IoT, consulte [Acelerador de solução de Monitoramento Remoto do Azure IoT][lnk-iot-sa-land].

Para saber mais sobre como desenvolver soluções com o Hub IoT, consulte o [Guia do desenvolvedor do Hub IoT].

Para saber mais sobre o roteamento de mensagens no Hub IoT, confira [Enviar e receber mensagens com o Hub IoT][lnk-devguide-messaging].

<!-- Images -->
[img-query-avro-data-1a]: ./media/iot-hub-query-avro-data/query-avro-data-1a.png
[img-query-avro-data-1b]: ./media/iot-hub-query-avro-data/query-avro-data-1b.png
[img-query-avro-data-2]: ./media/iot-hub-query-avro-data/query-avro-data-2.png
[img-query-avro-data-3]: ./media/iot-hub-query-avro-data/query-avro-data-3.png
[img-query-avro-data-4]: ./media/iot-hub-query-avro-data/query-avro-data-4.png
[img-query-avro-data-5]: ./media/iot-hub-query-avro-data/query-avro-data-5.png
[img-query-avro-data-6]: ./media/iot-hub-query-avro-data/query-avro-data-6.png
[img-query-avro-data-7a]: ./media/iot-hub-query-avro-data/query-avro-data-7a.png
[img-query-avro-data-7b]: ./media/iot-hub-query-avro-data/query-avro-data-7b.png
[img-query-avro-data-7c]: ./media/iot-hub-query-avro-data/query-avro-data-7c.png
[img-query-avro-data-8]: ./media/iot-hub-query-avro-data/query-avro-data-8.png

<!-- Links -->
[Roteamento de mensagens de IoT Hub do Azure: agora com o roteamento no corpo da mensagem]: https://azure.microsoft.com/blog/iot-hub-message-routing-now-with-routing-on-message-body/

[Routing on message bodies]: iot-hub-devguide-query-language.md#routing-on-message-bodies
[When using Azure storage containers]:iot-hub-devguide-endpoints.md#when-using-azure-storage-containers

[Exemplo de Avro U-SQL]:https://github.com/Azure/usql/tree/master/Examples/AvroExamples

[lnk-iot-sa-land]: ../iot-accelerators/index.md
[Guia do desenvolvedor do Hub IoT]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
