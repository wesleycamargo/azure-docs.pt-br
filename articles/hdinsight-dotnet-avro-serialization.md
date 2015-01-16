<properties urlDisplayName="HDInsight Microsoft .NET Library for Serialization with Avro" pageTitle="Serializar dados com a Biblioteca do Microsoft Avro | Azure" metaKeywords="" description="Saiba como o Azure HDInsight usa o Avro para serializar big data." metaCanonical="" services="hdinsight" documentationCenter="" title="Serialize data with the Microsoft Avro Library " authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/10/2014" ms.author="bradsev" />


# Serializar dados com a Biblioteca do Microsoft Avro

##Visão geral
Este tópico mostra como usar a <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Biblioteca do Microsoft Avro</a> na serialização de objetos e de outras estruturas de dados em fluxos de forma a conservá-los na memória, em um banco de dados ou em um arquivo, além de mostrar também como desserializá-los para recuperar os objetos originais. 

## Neste artigo

- [Apache Avro](#apacheAvro)
- [O cenário do Hadoop](#hadoopScenario)
- [Serialização na Biblioteca do Microsoft Avro](#serializationMAL) 
- [Pré-requisitos da Biblioteca do Microsoft Avro](#prerequisites)
- [Instalação da Biblioteca do Microsoft Avro](#installation)
- [Código-fonte da Biblioteca do Microsoft Avro](#sourceCode)
- [Compilando o esquema com a Biblioteca do Microsoft Avro](#compiling)
- [Guia para os exemplos da Biblioteca do Microsoft Avro](#samples)


##<a name="apacheAvro"></a>Apache Avro
A <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Biblioteca do Microsoft Avro</a> implementa o sistema de serialização de dados Apache Avro para o ambiente Microsoft.NET. O Apache Avro fornece um formato de intercâmbio de dados binários compactos para serialização. Ele utiliza o <a href="http://www.json.org" target="_blank">JSON</a> para definir o esquema com neutralidade de linguagem que subscreve a interoperabilidade da linguagem. Os dados serializados em uma linguagem podem ser lidos em outra diferente. Atualmente, há suporte para C, C++, C#, Java, PHP, Python e Ruby. Informações detalhadas sobre o formato podem ser encontradas na <a href="http://avro.apache.org/docs/current/spec.html" target="_blank">Especificação do Apache Avro</a>. Observe que a versão atual da biblioteca do Microsoft Avro não dá suporte à parte de RPCs (chamadas de procedimento remoto) destas especificações.

A representação serializada de um objeto no sistema Avro consiste em duas partes: esquema e o valor real. O esquema do Avro descreve o modelo de dados independente da linguagem dos dados serializados com JSON. Ele está presente lado a lado com uma representação binária de dados.  Colocar o esquema separado da representação binária permite que cada objeto seja programado sem sobrecargas por valor, tornando a serialização rápida e a representação pequena. 

##<a name="hadoopScenario"></a>O cenário do Hadoop 
O formato de serialização Apache Avro é amplamente usado no Azure HDInsight e em outros ambientes do Apache Hadoop. O Avro fornece um meio conveniente de representar estruturas de dados complexas em um trabalho MapReduce do Hadoop. O formato dos arquivos do Avro (arquivos contêiner de objetos do Avro) foi projetado para dar suporte ao modelo de programação distribuído do MapReduce. O recurso principal que habilita a distribuição é o fato de os arquivos serem divisíveis, no sentido de que uma pessoa pode buscar qualquer ponto em um arquivo e iniciar a leitura por meio de um bloco em específico. 
 
##<a name="serializationMAL"></a> Serialização na Biblioteca do Microsoft Avro
A Biblioteca do Microsoft .NET para Avro dá suporte a dois modos de serialização de objetos:

- **reflexão**: o esquema JSON dos tipos é criado automaticamente com base nos atributos do contrato de dados dos tipos .NET a serem serializados. 
- **registro genérico**: Um esquema JSON é especificado explicitamente em um registro representado pela classe [**AvroRecord**].(http://msdn.microsoft.com/pt-br/library/microsoft.hadoop.avro.avrorecord.aspx) quando nenhum tipo .NET está presente para descrever o esquema para os dados a serem serializados. 

Quando o esquema de dados é conhecido tanto pelo gravador quanto pelo leitor do fluxo, os dados podem ser enviados sem o seu esquema. Em casos em que um arquivo contêiner de objetos do Avro é usado, o esquema é armazenado dentro do arquivo. Outros parâmetros podem ser especificados, como o codec usado para compressão dos dados. Esses cenários são descritos com mais detalhes e ilustrados nos exemplos de código abaixo.


##<a name="prerequisites"></a> Pré-requisitos da Biblioteca do Microsoft Avro
- <a href="http://www.microsoft.com/pt-br/download/details.aspx?id=17851" target="_blank">Microsoft .NET Framework v4.0</a>
- <a href="http://james.newtonking.com/json" target="_blank">Newtonsoft Json.NET</a> (v6.0.4 ou posterior) 

Observe que a dependência Newtonsoft.Json.dll é baixada automaticamente com a instalação da Biblioteca do Microsoft Avro, cujo procedimento é descrito na seção a seguir.

##<a name="installation"></a> Instalação da Biblioteca do Microsoft Avro
A Biblioteca do Microsoft Avro é distribuída como um Pacote NuGet, o qual pode ser instalado pelo Visual Studio usando o procedimento a seguir: 

- Selecione a guia **Projeto** -> **Gerenciar Pacotes NuGet...**
- Pesquise "Microsoft.Hadoop.Avro" na caixa **Pesquisa Online**.
- Clique no botão **Instalar** próximo à **Biblioteca do Microsoft Avro**. 

Observe que a dependência Newtonsoft.Json.dll (>=6.0.4) também é baixada automaticamente com a Biblioteca do Microsoft Avro.

Você pode desejar visitar a <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">home page da Biblioteca do Microsoft Avro</a> para ler as Notas de versão atuais
 
##<a name="sourceCode"></a>Código-fonte da Biblioteca do Microsoft Avro

O código-fonte da Biblioteca do Microsoft Avro está disponível na <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">home page da Biblioteca do Microsoft Avro</a>.

##<a name="compiling"></a>Compilando o esquema com a Biblioteca do Microsoft Avro 

A Biblioteca do Microsoft Avro contém um utilitário de geração de código que permite criar tipos C# de maneira automática com base no esquema JSON definido anteriormente. O utilitário de geração de código não é distribuído como um executável binário, mas pode ser facilmente compilado usando o procedimento a seguir:

1. Baixe o arquivo ZIP com a versão mais recente do código-fonte do SDK do HDInsight no <a href="http://hadoopsdk.codeplex.com/SourceControl/latest" target="_blank">SDK do .NET da Microsoft para Hadoop</a>  (Clique no ícone de **Baixar**)
2. Extraia o SDK do HDInsight para um diretório do computador com o .NET Framework 4.0 instalado e conectado à Internet para baixar os Pacotes NuGet de dependência necessários. Vamos supor abaixo que o código-fonte é extraído para C:\SDK
3. Vá para a pasta C:\SDK\src\Microsoft.Hadoop.Avro.Tools e execute build.bat. (O arquivo chamará o MS Build da distribuição de 32 bits do .NET Framework. Se desejar usar a versão em 64 bits, edite o build.bat de acordo com os comentários dentro do arquivo) Garanta que a compilação seja executada com êxito. (Em alguns sistemas, o MS Build pode gerar avisos, porém, ele não afeta o utilitário se não houver erros de compilação)
4. O utilitário compilado está localizado em C:\SDK\Bin\Unsigned\Release\Microsoft.Hadoop.Avro.Tools


Para se familiarizar com o a sintaxe da linha de comando, execute o comando a seguir na pasta em que o utilitário de geração de código está localizado: `Microsoft.Hadoop.Avro.Tools help /c:codegen`

Para testar o utilitário, você pode gerar classes C# por meio do arquivo de esquema JSON de exemplo fornecido com o código-fonte. Execute o seguinte comando:

	Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:

Isso deve produzir dois arquivos C# no diretório atual: SensorData.cs e Location.cs.

Para entender a lógica que o utilitário de geração de código está usando ao converter o esquema JSON em tipos C#, consulte o arquivo GenerationVerification.feature localizado em C:\SDK\src\Microsoft.Hadoop.Avro.Tools\Doc

Observe que os namespaces são extraídos do esquema JSON usando a lógica descrita no arquivo mencionado no parágrafo anterior. Os namespaces extraídos do esquema têm prioridade sobre o que quer que seja fornecido com o parâmetro /n na linha de comando do utilitário. Se desejar substituir os namespaces que estão dentro do esquema, não deixe de usar o parâmetro /nf. Por exemplo, para alterar todos os namespaces de SampleJSONSchema.avsc para my.own.nspace, execute o comando a seguir:

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:. /nf:my.own.nspace

##<a name="samples"></a>Guia para os exemplos da Biblioteca do Microsoft Avro
Cada um dos seis exemplos fornecidos neste tópico ilustra um cenário diferente com suporte pela Biblioteca do Microsoft Avro. A Biblioteca do Microsoft Avro foi projetada para funcionar com qualquer fluxo. Nestes exemplos, os dados são manipulados usando fluxos de memória em vez de fluxos de arquivo ou bancos de dados por motivo de simplicidade e consistência. A abordagem usada em um ambiente de produção dependerá dos requisitos exatos do cenário, tais como fonte de dados e volume, restrições de desempenho e outros fatores.

Os primeiros dois exemplos mostram como serializar e desserializar os dados em buffers de fluxo de memória usando reflexão e registros genéricos. Presume-se que o esquema nesses dois casos é compartilhado entre os leitores e gravadores fora de banda. 

O terceiro e quarto exemplos mostram como serializar e desserializar os dados usando os arquivos contêiner de objetos do Avro. Quando os dados são armazenados em um arquivo contêiner do Avro, o esquema é sempre armazenado junto com o arquivo, uma vez que o esquema precisa ser compartilhado para a desserialização.

A amostra contendo os primeiros quatro exemplos pode ser baixada no site <a href="http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-86055923" target="_blank">Exemplos de código do</a> Azure.

O quinto exemplo mostra como usar um codec de compactação personalizado para arquivos contêiner de objetos do Avro. Uma amostra contendo o código para esse exemplo pode ser baixada no site de <a href="http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-67159111" target="_blank">Exemplos de código do</a> Azure.

O sexto exemplo mostra como usar a serialização do Avro para carregar dados no Armazenamento de Blob do Azure e analisá-la usando o Hive com um HDInsight (Hadoop). Você pode baixá-lo no site de <a href="https://code.msdn.microsoft.com/windowsazure/Using-Avro-to-upload-data-ae81b1e3" target="_blank">Exemplos de código do</a> Azure.

Aqui estão os links aos seis exemplos abordados neste tópico:

 * <a href="#Scenario1">**Serialização com reflexão**</a>: O esquema JSON dos tipos a serem serializados é criado automaticamente com base nos atributos do contrato de dados.
 * <a href="#Scenario2">**Serialização com registro genérico**</a>: O esquema JSON é especificado explicitamente em um registro quando não há nenhum tipo .NET disponível para reflexão.
 * <a href="#Scenario3">**Serialização usando arquivos contêiner de objetos com reflexão**</a>: O esquema JSON é compilado automaticamente e compartilhado junto com os dados serializados usando um arquivo contêiner de objetos do Avro.
 * <a href="#Scenario4">**Serialização usando arquivos contêiner de objetos com registro genérico**</a>: O esquema JSON é especificado explicitamente antes da serialização e compartilhado junto com os dados usando um arquivo contêiner de objetos do Avro.
 * <a href="#Scenario5">**Serialização usando arquivos contêiner de objetos com um codec de compactação personalizado**</a>: O exemplo mostra como criar um arquivo contêiner de objetos do Avro com uma implementação .NET customizada do codec de compactação dos dados Deflate.
 * <a href="#Scenario6">**Usando o Avro para carregar dados para o serviço Microsoft Azure HDInsight**</a>: Ilustra como a serialização do Avro interage com o serviço HDInsight. Uma assinatura ativa do Azure e acesso a um cluster Microsoft Azure HDInsight são necessários para executar este exemplo.

<h3> <a name="Scenario1"></a>Exemplo 1: Serialização com reflexão</h3>
 
O esquema JSON dos tipos pode ser compilado automaticamente pela Biblioteca do Microsoft Avro usando reflexão com base nos atributos do contrato de dados dos objetos C# a serem serializados. A Biblioteca do Microsoft Avro cria um [**IAvroSeralizer<T>**](http://msdn.microsoft.com/pt-br/library/dn627341.aspx) para identificar os campos a serem serializados.

Neste exemplo, os objetos (uma classe **SensorData** com uma estrutura-membro **Local**) são serializados para um fluxo de memória que, por sua vez, é desserializado. Em seguida, o resultado é comparado à instância inicial para confirmar que o objeto **SensorData** recuperado é idêntico ao original.

Presume-se que o esquema neste exemplo é compartilhado entre os leitores e gravadores, portanto, o formato do contêiner de objetos do Avro não é exigido. Para um exemplo de como serializar e desserializar dados em buffers de memória usando reflexão com o formato de contêiner de objetos quando o esquema precisa ser compartilhado com os dados, consulte <a href="#Scenario3">Serialização usando arquivos contêiner de objetos com reflexão.</a>

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;

        //Sample Class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serialize and deserialize sample data set represented as an object using Reflection
            //No explicit schema definition is required - schema of serialized objects is automatically built
            public void SerializeDeserializeObjectUsingReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION\n");
                Console.WriteLine("Serializing Sample Data Set...");

                //Create a new AvroSerializer instance and specify a custom serialization strategy AvroDataContractResolver
                //for serializing only properties attributed with DataContract/DateMember
                var avroSerializer = AvroSerializer.Create<SensorData>();

                //Create a Memory Stream buffer
                using (var buffer = new MemoryStream())
                {
                    //Create a data set using sample Class and struct 
                    var expected = new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } };

                    //Serialize the data to the specified stream
                    avroSerializer.Serialize(buffer, expected);


                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Deserialize data from the stream and cast it to the same type used for serialization
                    var actual = avroSerializer.Deserialize(buffer);

                    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                    //Finally, verify that deserialized data matches the original one
                    bool isEqual = this.Equal(expected, actual);

                    Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);

                }
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }



            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization to memory using Reflection
                Sample.SerializeDeserializeObjectUsingReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output: 
    // SERIALIZATION USING REFLECTION
    //
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


<h3> <a name="Scenario2"></a>Exemplo 2: Serialização com um registro genérico</h3>

Um esquema JSON pode ser especificado explicitamente em um registro genérico quando a reflexão não pode ser usada, pois os dados não podem ser representados usando classes .NET com um contrato de dados. Este método é geralmente mais lento do que usar reflexão. Nesses casos, o esquema para os dados também pode ser dinâmico, ou seja, ele não é conhecido até o momento da compilação. Os dados representados como arquivos CSV (valores separados por vírgulas), cujo esquema seja desconhecido até ser transformado no formato Avro em tempo de execução, são exemplos desse tipo de cenário dinâmico.

Este exemplo mostra como criar e usar um [**AvroRecord**](http://msdn.microsoft.com/pt-br/library/microsoft.hadoop.avro.avrorecord.aspx) para especificar explicitamente um esquema JSON, como preenchê-lo com dados para serializá-lo e desserializá-lo. Em seguida, o resultado é comparado à instância inicial para confirmar que o registro recuperado é idêntico ao original.

Presume-se que o esquema neste exemplo é compartilhado entre os leitores e gravadores, portanto, o formato do contêiner de objetos do Avro não é exigido. Para um exemplo de como serializar e desserializar dados em buffers de memória usando um registro genérico com o formato de contêiner de objeto quando o esquema precisa ser incluído com os dados serializados, consulte o exemplo <a href="#Scenario4">Serialização usando arquivos contêiner de objetos com registro genérico</a> genérico.


	namespace Microsoft.Hadoop.Avro.Sample
	{
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;

    //This class contains all methods demonstrating
    //the usage of Microsoft Avro Library
    public class AvroSample
    {

        //Serialize and deserialize sample data set using Generic Record.
        //Generic Record is a special class with the schema explicitly defined in JSON.
        //All serialized data should be mapped to the fields of Generic Record,
        //which in turn will be then serialized.
        public void SerializeDeserializeObjectUsingGenericRecords()
        {
            Console.WriteLine("SERIALIZATION USING GENERIC RECORD\n");
            Console.WriteLine("Defining the Schema and creating Sample Data Set...");

            //Define the schema in JSON
            const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        { 
                                            ""name"":""Location"", 
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

            //Create a generic serializer based on the schema
            var serializer = AvroSerializer.CreateGeneric(Schema);
            var rootSchema = serializer.WriterSchema as RecordSchema;

            //Create a Memory Stream buffer
            using (var stream = new MemoryStream())
            {
                //Create a generic record to represent the data
                dynamic location = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location.Floor = 1;
                location.Room = 243;

                dynamic expected = new AvroRecord(serializer.WriterSchema);
                expected.Location = location;
                expected.Value = new byte[] { 1, 2, 3, 4, 5 };

                Console.WriteLine("Serializing Sample Data Set...");

                //Serialize the data
                serializer.Serialize(stream, expected);

                stream.Seek(0, SeekOrigin.Begin);

                Console.WriteLine("Deserializing Sample Data Set...");

                //Deserialize the data into a generic record
                dynamic actual = serializer.Deserialize(stream);

                Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                //Finally, verify the results
                bool isEqual = expected.Location.Floor.Equals(actual.Location.Floor);
                isEqual = isEqual && expected.Location.Room.Equals(actual.Location.Room);
                isEqual = isEqual && ((byte[])expected.Value).SequenceEqual((byte[])actual.Value);
                Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);
            }
        }

        static void Main()
        {

            string sectionDivider = "---------------------------------------- ";

            //Create an instance of AvroSample Class and invoke methods
            //illustrating different serializing approaches
            AvroSample Sample = new AvroSample();

            //Serialization to memory using Generic Record
            Sample.SerializeDeserializeObjectUsingGenericRecords();

            Console.WriteLine(sectionDivider);
            Console.WriteLine("Press any key to exit.");
            Console.Read();
        }
    }
	}
    // The example is expected to display the following output: 
    // SERIALIZATION USING GENERIC RECORD
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


<h3> <a name="Scenario3"></a>Exemplo 3: Serialização usando arquivos contêiner de objetos e serialização com reflexão</h3>

Este exemplo é semelhante ao cenário do <a href="#Scenario1"> primeiro exemplo,</a> no qual o esquema é especificado implicitamente com reflexão, exceto pelo fato de que aqui, não se presume que o esquema seja conhecido pelo leitor que o desserializa. Os objetos **SensorData** a serem serializados e seu esquema especificado implicitamente são armazenados em um arquivo contêiner de objetos representado pela classe [**AvroContainer**](http://msdn.microsoft.com/pt-br/library/microsoft.hadoop.avro.container.avrocontainer.aspx) . 

O dados são serializados neste exemplo com [**SequentialWriter<SensorData>**](http://msdn.microsoft.com/pt-br/library/dn627340.aspx) e desserializados com [**SequentialReader<SensorData>**](http://msdn.microsoft.com/pt-br/library/dn627340.aspx). Em seguida, o resultado é comparado com as instâncias iniciais para garantir a identidade.

Os dados no arquivo contêiner de objetos são compactados usando o codec de compactação padrão [**Deflate**][deflate-100] do .NET Framework 4.0. Consulte o <a href="#Scenario5"> quinto exemplo</a> neste tópico para aprender como usar uma versão mais recente e superior do codec de compactação [**Deflate**][deflate-110], disponível no .NET Framework 4.5.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;

        //Sample Class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes sample data set using Reflection and Avro Object Container Files
            //Serialized data is compressed with Deflate codec
            public void SerializeDeserializeUsingObjectContainersReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro Object Container File
                string path = "AvroSampleReflectionDeflate.avro";

                //Create a data set using sample Class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file
                //Creating a Memory Stream buffer
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData which can serialize a sequence of SensorData objects to stream
                    //Data will be compressed using Deflate codec
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data
                //Creating a Memory Stream buffer
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from Object Container File
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader for type SensorData which will derserialize all serialized objects from the given stream
                    //It allows iterating over the deserialized objects because it implements IEnumerable<T> interface
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true)))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            bool isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual);
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content using given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using Reflection to Avro Object Container File
                Sample.SerializeDeserializeUsingObjectContainersReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.
  

<h3> <a name="Scenario4"></a>Exemplo 4: Serialização usando arquivos contêiner de objetos e serialização com registro genérico</h3>

Este exemplo é semelhante ao cenário do <a href="#Scenario2"> segundo exemplo,</a> no qual o esquema é especificado explicitamente com o JSON, exceto pelo fato de que aqui, não se presume que o esquema seja conhecido pelo leitor que o desserializa. 

O conjunto de dados de teste é coletado na lista de objetos [**AvroRecord**](http://msdn.microsoft.com/pt-br/library/microsoft.hadoop.avro.avrorecord.aspx)  usando um esquema JSON especificado explicitamente e armazenado em um arquivo contêiner de objetos representado pela classe [**AvroContainer**](http://msdn.microsoft.com/pt-br/library/microsoft.hadoop.avro.container.avrocontainer.aspx) . Esse arquivo contêiner cria um gravador usado para serializar os dados descompactados em um fluxo de memória que é salvo em um arquivo. É usado o parâmetro [**Codex.Null**](http://msdn.microsoft.com/pt-br/library/microsoft.hadoop.avro.container.codec.null.aspx) ao criar o leitor que especifica que esses dados não serão compactados. 

Em seguida, os dados são lidos no arquivo e desserializados em uma coleção de objetos. Essa coleção é comparada à lista inicial de registros do Avro para confirmar que são idênticos.


    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro.Schema;

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes sample data set using Generic Record and Avro Object Container Files
            //Serialized data is not compressed
            public void SerializeDeserializeUsingObjectContainersGenericRecord()
            {
                Console.WriteLine("SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro Object Container File
                string path = "AvroSampleGenericRecordNullCodec.avro";

                Console.WriteLine("Defining the Schema and creating Sample Data Set...");

                //Define the schema in JSON
                const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        { 
                                            ""name"":""Location"", 
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

                //Create a generic serializer based on the schema
                var serializer = AvroSerializer.CreateGeneric(Schema);
                var rootSchema = serializer.WriterSchema as RecordSchema;

                //Create a generic record to represent the data
                var testData = new List<AvroRecord>();

                dynamic expected1 = new AvroRecord(rootSchema);
                dynamic location1 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location1.Floor = 1;
                location1.Room = 243;
                expected1.Location = location1;
                expected1.Value = new byte[] { 1, 2, 3, 4, 5 };
                testData.Add(expected1);

                dynamic expected2 = new AvroRecord(rootSchema);
                dynamic location2 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location2.Floor = 1;
                location2.Room = 244;
                expected2.Location = location2;
                expected2.Value = new byte[] { 6, 7, 8, 9 };
                testData.Add(expected2);

                //Serializing and saving data to file
                //Create a MemoryStream buffer
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData which can serialize a sequence of SensorData objects to stream
                    //Data will not be compressed (Null compression codec)
                    using (var writer = AvroContainer.CreateGenericWriter(Schema, buffer, Codec.Null))
                    {
                        using (var streamWriter = new SequentialWriter<object>(writer, 24))
                        {
                            // Serialize the data to stream using the sequential writer
                            testData.ForEach(streamWriter.Write);
                        }
                    }

                    Console.WriteLine("Saving serialized data to file...");

                    //Save stream to file
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializng the data
                //Create a Memory Stream buffer
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from Object Container File
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader for type SensorData which will derserialize all serialized objects from the given stream
                    //It allows iterating over the deserialized objects because it implements IEnumerable<T> interface
                    using (var reader = AvroContainer.CreateGenericReader(buffer))
                    {
                        using (var streamReader = new SequentialReader<object>(reader))
                        {
                            var results = streamReader.Objects;

                            Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                            //Finally, verify the results
                            var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = (dynamic)serialized, actual = (dynamic)deserialized });
                            int count = 1;
                            foreach (var pair in pairs)
                            {
                                bool isEqual = pair.expected.Location.Floor.Equals(pair.actual.Location.Floor);
                                isEqual = isEqual && pair.expected.Location.Room.Equals(pair.actual.Location.Room);
                                isEqual = isEqual && ((byte[])pair.expected.Value).SequenceEqual((byte[])pair.actual.Value);
                                Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                                count++;
                            }
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content using given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using Generic Record to Avro Object Container File
                Sample.SerializeDeserializeUsingObjectContainersGenericRecord();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.




<h3> <a name="Scenario5"></a>Exemplo 5: Serialização usando arquivos contêiner de objetos com um codec de compactação personalizado</h3>

O quinto exemplo mostra como usar um codec de compactação personalizado para arquivos contêiner de objetos do Avro. Uma amostra contendo o código para esse exemplo pode ser baixada no site [Exemplos de código do](http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-67159111) Azure.

A [Especificação do Avro](http://avro.apache.org/docs/current/spec.html#Required+Codecs) permite o uso de um codec de compactação opcional (além dos padrões **Null** e **Deflate**). Este exemplo não implementa um codec totalmente novo como o Snappy (mencionado como um codec opcional com suporte na [Especificação do Avro](http://avro.apache.org/docs/current/spec.html#snappy)). Ele mostra como usar a implementação do .NET Framework 4.5 do codec [**Deflate**][deflate-110], o qual fornece um algoritmo de compactação baseado na biblioteca de compactação [zlib](http://zlib.net/) melhor do que a versão padrão do .NET Framework 4.0.


    // 
    // This code needs to be compiled with the parameter Target Framework set as ".NET Framework 4.5"
    // to ensure the desired implementation of Deflate compression algorithm is used
    // Ensure your C# Project is set up accordingly
    //

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.IO;
        using System.IO.Compression;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;

        #region Defining objects for serialization
        //Sample Class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }
        #endregion

        #region Defining custom codec based on .NET Framework V.4.5 Deflate
        //Avro.NET Codec class contains two methods 
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed)
        //which are the key ones for data compression.
        //To enable a custom codec one needs to implement these methods for the required codec

        #region Defining Compression and Decompression Streams
        //DeflateStream (class from System.IO.Compression namespace that implements Deflate algorithm)
        //can not be directly used for Avro because it does not support vital operations like Seek.
        //Thus one needs to implement two classes inherited from Stream
        //(one for compressed and one for decompressed stream)
        //that use Deflate compression and implement all required features 
        internal sealed class CompressionStreamDeflate45 : Stream
        {
            private readonly Stream buffer;
            private DeflateStream compressionStream;

            public CompressionStreamDeflate45(Stream buffer)
            {
                Debug.Assert(buffer != null, "Buffer is not allowed to be null.");

                this.compressionStream = new DeflateStream(buffer, CompressionLevel.Fastest, true);
                this.buffer = buffer;
            }

            public override bool CanRead
            {
                get { return this.buffer.CanRead; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return this.buffer.CanWrite; }
            }

            public override void Flush()
            {
                this.compressionStream.Close();
            }

            public override long Length
            {
                get { return this.buffer.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.buffer.Position;
                }

                set
                {
                    this.buffer.Position = value;
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.buffer.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                return this.buffer.Seek(offset, origin);
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                this.compressionStream.Write(buffer, offset, count);
            }

            protected override void Dispose(bool disposed)
            {
                base.Dispose(disposed);

                if (disposed)
                {
                    this.compressionStream.Dispose();
                    this.compressionStream = null;
                }
            }
        }

        internal sealed class DecompressionStreamDeflate45 : Stream
        {
            private readonly DeflateStream decompressed;

            public DecompressionStreamDeflate45(Stream compressed)
            {
                this.decompressed = new DeflateStream(compressed, CompressionMode.Decompress, true);
            }

            public override bool CanRead
            {
                get { return true; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return false; }
            }

            public override void Flush()
            {
                this.decompressed.Close();
            }

            public override long Length
            {
                get { return this.decompressed.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.decompressed.Position;
                }

                set
                {
                    throw new NotSupportedException();
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.decompressed.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                throw new NotSupportedException();
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                throw new NotSupportedException();
            }

            protected override void Dispose(bool disposing)
            {
                base.Dispose(disposing);

                if (disposing)
                {
                    this.decompressed.Dispose();
                }
            }
        }
        #endregion

        #region Define Codec
        //Define the actual codec class containing the required methods for manipulating streams:
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed)
        //Codec class uses classes for comressed and decompressed streams defined above
        internal sealed class DeflateCodec45 : Codec
        {

            //We merely use different IMPLEMENTATION of Deflate, so the CodecName remains "deflate"
            public static readonly string CodecName = "deflate";

            public DeflateCodec45()
                : base(CodecName)
            {
            }

            public override Stream GetCompressedStreamOver(Stream decompressed)
            {
                if (decompressed == null)
                {
                    throw new ArgumentNullException("decompressed");
                }

                return new CompressionStreamDeflate45(decompressed);
            }

            public override Stream GetDecompressedStreamOver(Stream compressed)
            {
                if (compressed == null)
                {
                    throw new ArgumentNullException("compressed");
                }

                return new DecompressionStreamDeflate45(compressed);
            }
        }
        #endregion

        #region Define modified Codec Factory
        //Define modified Codec Factory to be used in Reader
        //It will catch the attempt to use "deflate" and provide Custom Codec 
        //For all other cases it will rely on the base class (CodecFactory)
        internal sealed class CodecFactoryDeflate45 : CodecFactory
        {

            public override Codec Create(string codecName)
            {
                if (codecName == DeflateCodec45.CodecName)
                    return new DeflateCodec45();
                else
                    return base.Create(codecName);
            }
        }
        #endregion

        #endregion

        #region Sample Class with demonstration methods
        //This class contains methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes sample data set using Reflection and Avro Object Container Files
            //Serialized data is compressed with the Custom compression codec (Deflate of .NET Framework 4.5)
            //
            //This sample uses Memory Stream for all operations related to serialization, deserialization and
            //Object Container manipulation, though File Stream could be easily used.
            public void SerializeDeserializeUsingObjectContainersReflectionCustomCodec()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC\n");

                //Path for Avro Object Container File
                string path = "AvroSampleReflectionDeflate45.avro";

                //Create a data set using sample Class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file
                //Creating a Memory Stream buffer
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData which can serialize a sequence of SensorData objects to stream
                    //Here the custom Codec is introduced. For convenience the next commented code line shows how to use built-in Deflate.
                    //Note, that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations
                    //using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, new DeflateCodec45()))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data
                //Creating a Memory Stream buffer
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from Object Container File
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Because of SequentialReader<T> constructor signature an AvroSerializerSettings instance is required
                    //when Codec Factory is explicitly specified
                    //You may comment the line below if you want to use built-in Deflate (see next comment)
                    AvroSerializerSettings settings = new AvroSerializerSettings();

                    //Create a SequentialReader for type SensorData which will derserialize all serialized objects from the given stream
                    //It allows iterating over the deserialized objects because it implements IEnumerable<T> interface
                    //Here the custom Codec Factory is introduced.
                    //For convenience the next commented code line shows how to use built-in Deflate
                    //(no explicit Codec Factory parameter is required in this case).
                    //Note, that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations
                    //using (var reader = new SequentialReader<SensorData>(AvroContainer.CreateReader<SensorData>(buffer, true)))
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true, settings, new CodecFactoryDeflate45())))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        bool isEqual;
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }
        #endregion

            #region Helper Methods

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content using given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }
            #endregion

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using Reflection to Avro Object Container File using Custom Codec
                Sample.SerializeDeserializeUsingObjectContainersReflectionCustomCodec();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    //For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

<h3> <a name="Scenario6"></a> Exemplo 6: Usando o Avro para carregar dados para o serviço Microsoft Azure HDInsight</h3>

O sexto exemplo ilustra algumas técnicas de programação relacionadas à interação com o serviço Microsoft Azure HDInsight. Uma amostra contendo o código para esse exemplo pode ser baixada no site [Exemplos de código do](https://code.msdn.microsoft.com/windowsazure/Using-Avro-to-upload-data-ae81b1e3) Azure.

O exemplo a seguir faz o seguinte:

* Conecta-se a um cluster de serviço do HDInsight existente.
* Serializa diversos arquivos CSV e carrega o resultado na Armazenamento de Blob do Azure. Os arquivos CSV são distribuídos juntos com o exemplo e representam amostras de dados históricos da Bolsa de Valores AMEX distribuídos pela [Infochimps](http://www.infochimps.com/) para o período de 1970 a 2010. O exemplo lê os dados dos arquivos CSV, converte os registros em instâncias da classe de **ações** e os serializa usando reflexão.A definição do tipo de ação é criada com base na esquema JSON usando o utilitário de geração de código da Biblioteca do Microsoft Avro.
* Cria uma nova tabela externa chamada **Ações** no Hive a vincula aos dados carregados na etapa anterior.
* Executa uma consulta usando o Hive na tabela **Ações**.

Além disso, o exemplo realiza um procedimento de limpeza antes e depois da realização de operações mais importantes. Durante a limpeza, todos os dados e pastas relacionados ao Blob do Azure são removidos e a tabela Hive é ignorada. Você pode também invocar um procedimento de limpeza na linha de comando do exemplo. 

O exemplo tem os seguintes pré-requisitos:

* Uma assinatura do Microsoft Azure ativa e seu Certificado de
* Gerenciamento de ID da assinatura com a chave privada correspondente. O Certificado deve ser instalado no armazenamento privado do usuário atual no computador usado para executar o exemplo.
* Um cluster HDInsight ativo
* Uma conta de Armazenamento do Azure vinculada ao cluster HDInsight do pré-requisito anterior juntamente com a chave de acesso Primária ou Secundária correspondente.

Todas as informações dos pré-requisitos devem ser inseridas no arquivo de configuração do exemplo antes deste ser executado. Há duas maneiras possíveis de fazer isso:

* Editar o arquivo app.config no diretório raiz do exemplo e compilar o exemplo ou 
* Primeiro compilar o exemplo e editar o AvroHDISample.exe.config no diretório de compilação 

Em ambos os casos, todas as edições devem ser realizadas na seção de configurações **<appSettings>**.Siga os comentários no arquivo.
O exemplo é executado na linha de comando ao realizar o seguinte comando (em que se pressupôs que o arquivo ZIP com o exemplo foi extraído para C:\AvroHDISample; caso contrário, use o caminho de arquivo relevante).

    AvroHDISample run C:\AvroHDISample\Data

Para realizar a limpeza do cluster, execute o seguinte comando:

    AvroHDISample clean




[deflate-100]: http://msdn.microsoft.com/pt-br/library/system.io.compression.deflatestream(v=vs.100).aspx
[deflate-110]: http://msdn.microsoft.com/pt-br/library/system.io.compression.deflatestream(v=vs.110).aspx



<!--HONumber=35.1-->
