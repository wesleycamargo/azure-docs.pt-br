---
title: Consultar dados do Avro usando o Azure Data Lake Analytics | Microsoft Docs
description: Use propriedades do corpo de mensagem a telemetria de dispositivo de rota para o armazenamento de Blob e consultar os dados de formato Avro gravados no armazenamento de Blob.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: asrastog
ms.openlocfilehash: a17df39c55b5c02c83e3f0b74a91d7109ddb4d3d
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43188937"
---
# <a name="query-avro-data-by-using-azure-data-lake-analytics"></a>Consultar dados Avro usando a análise do Azure Data Lake Analytics

Este artigo discute como consultar dados da Avro para roteamento eficiente de mensagens do Azure Hub Iot aos serviços do Azure. Conforme anunciamos na postagem de blog [Roteamento de mensagens de IoT Hub do Azure: agora com o roteamento no corpo da mensagem], IoT Hub dá suporte a roteamento em propriedades ou o corpo da mensagem. Para saber mais, confira [Roteamento de corpos de mensagem][Routing on message bodies]. 

O desafio foi que quando o Azure IoT Hub roteia mensagens para o armazenamento de Blob do Microsoft Azure, o Hub IoT grava o conteúdo no formato Avro, que tem o corpo da mensagem e propriedades da mensagem. O Hub IoT oferece suporte a gravação de dados para armazenamento de BLOBs no formato de dados Avro, e esse formato não é usado para outros pontos de extremidade. Para obter mais informações, consulte [Quando usar contêineres de armazenamento do Armazenamento do Microsoft Azure][When using Azure storage containers]. Embora o formato Avro seja ótimo para preservação de dados e mensagem, é um desafio usá-lo para consultar dados. Em comparação, o formato JSON ou CSV é muito mais fácil para consultar dados.

Para resolver necessidades de dados grandes não relacionadas e formatos e superar esse desafio você pode usar muitos padrões de dados grandes para transformar e escalonar dados. Um dos padrões, "pagar por consulta," é o Azure Data Lake Analytics, que é o foco deste artigo. Embora facilmente você possa executar a consulta no Hadoop ou outras soluções,o Data Lake Analytics é geralmente mais adequado para essa abordagem "pagar por consulta". 

Há um "extrator" para Avro em U-SQL. Para saber mais, consulte o exemplo [Exemplo de Avro U-SQL].

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Consultar e exportar dados da Avro para um arquivo CSV
Nesta seção, você consulta os dados Avro e exporta em um arquivo CSV no armazenamento de Blobs do Azure, embora você pode colocar facilmente os dados em outros repositórios ou armazenamento de dados.

1. Configure o Azure Hub IoT para dados de rota para um ponto de extremidade de armazenamento de BLOBs do Azure usando uma propriedade no corpo da mensagem para selecionar as mensagens.

    ![Seção “Ponto de extremidade personalizados”][img-query-avro-data-1a]

    ![Comando Rotas][img-query-avro-data-1b]

2. Certifique-se de que o dispositivo tenha a codificação, o tipo de conteúdo e os dados necessários no corpo da mensagem, conforme mencionado na documentação do produto ou as propriedades. Ao exibir esses atributos no Device Explorer, conforme exibido aqui, você pode verificar se estão definidos corretamente.

    ![Painel de Dados do Hub de eventos][img-query-avro-data-2]

3. Configure uma instância do Azure Data Lake Store e uma instância de análise do Azure Data Lake Analytics. O Hub IoT do Azure não faz roteamento para uma instância do repositório Azure Data Lake Store, mas uma instância de análise Azure Data Lake Analytics requer um.

    ![Instâncias do repositório Azure Data Lake Data Lake Store instâncias do Data Lake Analytics][img-query-avro-data-3]

4. No Data Lake Analytics, configure o armazenamento de BLOBs do Azure como um armazenamento adicional, o mesmo armazenamento de Blob do Azure Hub IoT  encaminha dados.

    ![Painel “Fonte de dados”][img-query-avro-data-4]
 
5. Conforme discutido no exemplo [Exemplo de Avro U-SQL], você precisa de quatro arquivos DLL. Carregue esses arquivos para um local no seu Azure Data Lake Store.

    ![Quatro arquivos DLL carregados][img-query-avro-data-5] 

6. No Visual Studio, crie um projeto de U-SQL.
 
    ![Crie um projeto U-SQL][img-query-avro-data-6]

7. Cole o conteúdo do script a seguir e cole- arquivo no arquivo recém-criado. Modifique as três seções realçadas: sua conta do Data Lake Analytics, caminhos das DLLs associadas e o caminho correto para sua conta de armazenamento.
    
    ![As três seções a serem modificadas][img-query-avro-data-7a]

    O script U-SQL real para uma saída simples para o arquivo CSV:
    
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

    O Data Lake Analytics levou cinco minutos para executar o script a seguir, que foi limitado a 10 unidades analíticas e processados 177 arquivos. O resultado é mostrado na saída do arquivo CSV que é exibida na imagem a seguir:
    
    ![Resultados para o arquivo CSV][img-query-avro-data-7b]

    ![Saída convertida para arquivo CSV][img-query-avro-data-7c]

    Vá para JSON, continue para a etapa 8.
    
8. A maioria das mensagens de IoT estão no formato de arquivo JSON. Ao adicionar linhas a seguir, você pode analisar a mensagem em um arquivo JSON, que permite que você adicione ONDE as cláusulas e saída apenas dos dados necessários.

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

    A saída exibe uma coluna para cada item de `SELECT` comando. 
    
    ![Saindo mostrando uma colunada para cada item][img-query-avro-data-8]

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como consultar dados Avro para roteamento eficiente de mensagens do Azure Hub IoT aos serviços do Azure.

Para exemplos de soluções completas que usam o Hub IoT, consulte [Acelerador de solução de Monitoramento Remoto do Azure IoT][lnk-iot-sa-land].

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

[lnk-iot-sa-land]: ../iot-accelerators/index.yml
[Guia do desenvolvedor do Hub IoT]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
