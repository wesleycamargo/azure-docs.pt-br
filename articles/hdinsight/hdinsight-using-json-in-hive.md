<properties
   pageTitle="Analisar e processar documentos JSON com o Hive no HDInsight | Microsoft Azure"
   description="Saiba como usar documentos JSON e analisá-los usando o Hive no HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="rashimg"
   manager="mwinkle"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/14/2015"
   ms.author="rashimg"/>


# Aprenda a processar e analisar documentos JSON no Hive

O JSON é um dos formatos mais usados na Web. Este tutorial o ajudará a compreender uma das dúvidas mais comuns encontradas pelos usuários do Hive: como usar documentos JSON documentos no Hive e como executar análise neles.

##Exemplo de JSON

Vejamos um exemplo. Suponhamos que temos o documento JSON mostrado abaixo. Nosso objetivo é ser capaz de analisar esse documento JSON para então executar consultas neste documento como buscar o valor de uma chave, agregação etc.

	  {
        "StudentId": "trgfg-5454-fdfdg-4346",
	    "Grade": 7,
        "StudentDetails": [
          {
            "FirstName": "Peggy",
            "LastName": "Williams",
            "YearJoined": 2012
          }
        ],
        "StudentClassCollection": [
          {
            "ClassId": "89084343",
            "ClassParticipation": "Satisfied",
            "ClassParticipationRank": "High",
            "Score": 93,
            "PerformedActivity": false
          },
          {
            "ClassId": "78547522",
            "ClassParticipation": "NotSatisfied",
            "ClassParticipationRank": "None",
            "Score": 74,
            "PerformedActivity": false
          },
          {
            "ClassId": "78675563",
            "ClassParticipation": "Satisfied",
            "ClassParticipationRank": "Low",
            "Score": 83,
            "PerformedActivity": true
          }
        ] 
      }

##Nivelar um documento JSON (etapa necessária somente em caso de JSON formatado)

Para podermos usar um operador de Hive para executar a análise, será necessário pré-processar o documento JSON para deixá-lo pronto para ser usado pelo Hive.

Todas as opções listadas na próxima seção supõem que o documento JSON está em uma única linha. Em outras palavras, é necessário nivelar o documento JSON em uma cadeia de caracteres para que ele possa ser usado pelos operadores de Hive.

**Se seu documento JSON já está nivelado e todo o documento está uma única linha, ignore esta etapa e vá diretamente para a próxima seção sobre Como analisar dados JSON.**

Vamos supor que seu documento JSON não nivelado de várias linhas está presente no seu armazenamento de Blobs do Azure na pasta */json/input/* no contêiner padrão. O código a seguir cria uma tabela jsonexample que aponta para o documento JSON não nivelado original.

    drop table jsonexample;
    CREATE EXTERNAL TABLE jsonexample (textcol string) stored as textfile location '/json/input/';

Em seguida, vamos criar uma nova tabela chamada one_line_json que apontará para o documento JSON nivelado e será armazenada no seu armazenamento de Blobs do Azure na pasta */json/temp/* no contêiner padrão.

    drop table if exists one_line_json;
    create external table one_line_json
    (
      json_body string
    )
    stored as textfile location '/json/temp';

Por fim, popularemos esta tabela com os dados JSON nivelados.

    insert overwrite table one_line_json
    select concat_ws(' ',collect_list(textcol)) as singlelineJSON 
      from (select INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE,textcol from jsonexample distribute by INPUT__FILE__NAME sort by BLOCK__OFFSET__INSIDE__FILE ) 
      x group by INPUT__FILE__NAME;

Neste momento, se você examinar a tabela recém-criada one_line_json, verá que ela contém o documento JSON em uma única linha

    select * from one_line_json;

Aqui está a saída dessa consulta:

![Nivelamento do documento JSON.][image-hdi-hivejson-flatten]

##Opções para analisar documentos JSON no Hive

Agora que temos o documento JSON em uma tabela com uma única coluna, estamos prontos para usar o Hive para executar consultas nesses dados. O Hive fornece três mecanismos diferentes para executar consultas em documentos JSON:

1.	Usando a UDF (Função Definida pelo Usuário): get_json_object
2.	Usando a UDF: json_tuple
3.	Usando SerDe personalizado

Vamos discutir cada uma delas em detalhes.

##UDF get_json_object
O Hive fornece uma UDF interna chamada [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) que pode executar consultas JSON durante o tempo de execução. Esse método requer dois argumentos: o nome da tabela e o nome do método que tem o documento JSON nivelado, e o campo JSON que precisa ser analisado. Vejamos um exemplo para ver como essa UDF funciona.

Obter o nome e o sobrenome de cada aluno

    select get_json_object(one_line_json.json_body, '$.StudentDetails.FirstName'), get_json_object(one_line_json.json_body, '$.StudentDetails.LastName') from one_line_json;

Aqui está a saída obtida ao executar essa consulta na janela do console.

![UDF get_json_object][image-hdi-hivejson-getjsonobject]

Essa UDF possui algumas limitações.


- Uma das principais é que essa UDF não apresenta alto desempenho, visto que cada campo na consulta exigirá reanálise da consulta, afetando o desempenho.
- Em segundo lugar, get_json_object() retorna a representação de cadeia de caracteres de uma matriz. Portanto, para converter isso em uma matriz do Hive, você precisará usar expressões regulares para substituir os colchetes “[” e “]” e depois também realizar a divisão para obter a matriz.


É por isso wiki do Hive recomenda o uso de json_tuple, que discutiremos a seguir.


##UDF json_tuple

A outra UDF fornecida pelo Hive é chamada [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple), que é mais eficaz do que a [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Esse método usa um conjunto de chaves e uma cadeia de caracteres JSON e retorna uma tupla de valores usando uma função. Vejamos um exemplo para ver como essa UDF funciona.

Vamos obter a id e nota do aluno do documento JSON

    select q1.StudentId, q1.Grade 
      from one_line_json jt
      LATERAL VIEW json_tuple(jt.json_body, 'StudentId', 'Grade') q1
        as StudentId, Grade;

Usamos a sintaxe [exibição lateral](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) no Hive que permite que json_tuple crie uma tabela virtual aplicando a função UDT para cada linha da tabela original.

Vejamos a saída deste script no console do Hive:

![UDF json_tuple][image-hdi-hivejson-jsontuple]

Uma das principais desvantagens desta UDF é que JSONs complexos se tornam muito complicados devido ao uso repetido de EXIBIÇÃO LATERAL. Inclusive, essa UDF não trabalha com JSONs aninhados.

##Usando SerDe personalizado

Para analisar documentos JSON aninhados, o SerDe é a **melhor opção**, pois você pode definir o esquema JSON, facilitando a execução de consultas relacionados a ele.

Abordaremos como usar um dos SerDe mais populares desenvolvido pela [rcongiu](https://github.com/rcongiu).

Etapa 1: verifique se você tem o [Java SE Development Kit 7u55 JDK 1.7.0_55](http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html#jdk-7u55-oth-JPR) instalado (Observação: se você tiver o JDK 1.8 instalado, ele não funcionará com esse SerDe).


- Se você pretende usar a implantação do Windows do HDInsight, escolha a versão do Windows x64 do JDK.
- Após a conclusão da instalação, vá para o Painel de Controle--> Adicionar variáveis do ambiente adicione uma nova variável de ambiente JAVA_HOME apontando para C:\Arquivos de Programas\Java\jdk1.7.0_55 ou onde quer que o JDK esteja instalado. As capturas de tela a seguir mostram como você pode definir a variável de ambiente.

![Definir valores de configuração correto para o JDK][image-hdi-hivejson-jdk]

Etapa 2: acesse [este](http://mirror.olnevhost.net/pub/apache/maven/maven-3/3.3.1/binaries/apache-maven-3.3.1-bin.zip) link e baixe o Maven 3.3.1. Descompacte o arquivo onde você deseja armazenar os binários. No meu caso, ele será descompactado em C:\Arquivos de Programas\Maven. Adicione a pasta do recipiente ao seu caminho acessando o Painel de Controle--> Editar as variáveis de sistema para as variáveis da sua conta Variáveis de ambiente. A captura de tela abaixo mostra como fazer isso.

![Configurando o Maven][image-hdi-hivejson-maven]

Etapa 3: clone o projeto do site do github [Hive-JSON-SerDe](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master). Você pode fazer isso clicando no botão "Baixar Zip" conforme mostrado na captura de tela abaixo.

![Clonar o projeto][image-hdi-hivejson-serde]

Etapa 4: vá para a pasta onde você baixou este pacote e digite "mvn package". Isso deverá criar os arquivos jar necessários que você poderá copiar para o cluster.

Etapa 5: em seguida, vá para a pasta de destino na pasta raiz onde você baixou o pacote. Carregue o arquivo json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar para o nó principal do seu cluster. Normalmente, eu o coloco na pasta de binários de hive: C:\apps\dist\hive-0.13.0.2.1.11.0-2316\bin ou algo semelhante.
 
Etapa 6: no prompt do hive, digite "add jar /path/to/json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar". Como o jar está na pasta C:\apps\dist\hive-0.13.x\bin no meu caso, posso adicionar o jar diretamente com o nome mostrado abaixo:

    add jar json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar;

![Adicionar JAR ao seu projeto][image-hdi-hivejson-addjar]

Agora, estamos prontos para usar o SerDe para executar consultas no documento JSON.

Primeiro, vamos criar a tabela com o esquema do documento JSON. Observe que podemos trabalhar com tipos muito mais avançados, incluindo mapas, matrizes e structs. Digite o código a seguir no console Hive para que ele crie uma tabela denominada json_table que pode ler o documento JSON com base no esquema abaixo.

    drop table json_table;
    create external table json_table (
      StudentId string, 
      Grade int,
      StudentDetails array<struct<
          FirstName:string,
          LastName:string,
          YearJoined:int
          >
      >,
      StudentClassCollection array<struct<
          ClassId:string,
          ClassParticipation:string,
          ClassParticipationRank:string,
          Score:int,
          PerformedActivity:boolean
          >
      >
    ) row format serde 'org.openx.data.jsonserde.JsonSerDe'
    location '/json/temp';

Agora que definimos o esquema, vamos executar alguns exemplos para ver como podemos consultar o documento JSON usando o Hive:

a) Listar o nome e sobrenome do aluno

    select StudentDetails.FirstName, StudentDetails.LastName from json_table;

Este é o resultado no console do Hive.

![Consulta SerDe 1][image-hdi-hivejson-serde_query1]

b) Esta consulta calcula a soma das pontuações do documento JSON

    select sum(scores)
    from json_table jt
      lateral view explode(jt.StudentClassCollection.Score) collection as scores;
       
A consulta anterior usa a UDF [explodir exibição lateral](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) para expandir a matriz de pontuações para que possam ser somados.

Esta é a saída do console do Hive.

![Consulta SerDe 2][image-hdi-hivejson-serde_query2]

c) Localizar em quais matérias um aluno específico obteve mais de 80 pontos selecionando a coleção jt.StudentClassCollection.ClassId from json_table jt lateral view explode(jt.StudentClassCollection.Score) como pontuação, sendo esta > 80;
      
A consulta acima retorna uma matriz de Hive diferente de get_json_object, que retorna uma cadeia de caracteres.

![Consulta SerDe 3][image-hdi-hivejson-serde_query3]

Se você quiser realizar skil em JSON malformado, conforme explicado no [página wiki](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) desse SerDe, isso pode ser feito digitando o código a seguir:

    ALTER TABLE json_table SET SERDEPROPERTIES ( "ignore.malformed.json" = "true");


##Outras opções
Além das opções listadas abaixo, você também podem escrever seu próprio código personalizado usando Python ou outras linguagens específicas ao seu cenário. Depois que seu script de python estiver pronto, consulte [este artigo][hdinsight-python] sobre como executar seu próprio código Python com o Hive.

##Resumo
Concluindo, o tipo de operador JSON no Hive que você escolher dependerá de seu cenário. Se você tiver um documento JSON simples e apenas um campo para pesquisar, poderá usar o UDF get_json_object do Hive. Se tiver mais de uma chave para pesquisar, poderá usar json_tuple. Se tiver um documento aninhado, deverá usar o SerDe JSON.

Nós do HDInsight estamos trabalhando para facilitar ainda mais o uso de mais formatos nativamente no Hive sem exigir grande esforço da sua parte. Atualizaremos este tutorial com mais detalhes quando tivermos mais novidades para compartilhar.

[hdinsight-python]: hdinsight-python.md

[image-hdi-hivejson-flatten]: ./media/hdinsight-using-json-in-hive/flatten.png
[image-hdi-hivejson-getjsonobject]: ./media/hdinsight-using-json-in-hive/getjsonobject.png
[image-hdi-hivejson-jsontuple]: ./media/hdinsight-using-json-in-hive/jsontuple.png
[image-hdi-hivejson-jdk]: ./media/hdinsight-using-json-in-hive/jdk.png
[image-hdi-hivejson-maven]: ./media/hdinsight-using-json-in-hive/maven.png
[image-hdi-hivejson-serde]: ./media/hdinsight-using-json-in-hive/serde.png
[image-hdi-hivejson-addjar]: ./media/hdinsight-using-json-in-hive/addjar.png
[image-hdi-hivejson-serde_query1]: ./media/hdinsight-using-json-in-hive/serde_query1.png
[image-hdi-hivejson-serde_query2]: ./media/hdinsight-using-json-in-hive/serde_query2.png
[image-hdi-hivejson-serde_query3]: ./media/hdinsight-using-json-in-hive/serde_query3.png
[image-hdi-hivejson-serde_result]: ./media/hdinsight-using-json-in-hive/serde_result.png
 

<!---HONumber=62-->