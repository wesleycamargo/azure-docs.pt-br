<properties urlDisplayName="Blitline Image Processing Service" pageTitle="Como usar o Blitline para imagem do guia de recursos de processamento - Azure " metaKeywords ="" description="Saiba como usar o serviço Blitline para processar imagens em um aplicativo do Azure." metaCanonical="" services="" documentationCenter=".NET" title="How to use Blitline with Azure and Azure Storage" authors="jason@blitline.com" solutions="" manager="jason@blitline.com" editor="jason@blitline.com" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/09/2014" ms.author="support@blitline.com" />






# Como usar o Blitline com o Azure e o Armazenamento do Azure

Este guia explicará como acessar os serviços do Blitline e enviar trabalhos para o Blitline.

## Sumário

[O que é Blitline?][]
[O que o Blitline NÃO é][]
[Criar uma conta do Blitline][]
[Como criar um trabalho do Blitline][]
[Como salvar uma imagem em seu armazenamento do Azure][]
[Próximas etapas][]

## <a id="whatis"></a>O que é Blitline?

Blitline é um serviço de processamento de imagens com base na nuvem que fornece processamento de imagens de nível empresarial a uma fração do preço que custaria para você mesmo fazer a compilação.

O fato é que o processamento de imagens foi realizado repetidamente, normalmente recompilado desde o início para cada site. Sabemos disso porque os compilamos milhões de vezes também. Um dia, decidimos que talvez fosse o momento de simplesmente fazê-lo para todas as pessoas. Nós sabemos como fazê-lo de modo rápido e eficiente, além de poupar o trabalho das pessoas nesse meio tempo.

Para obter mais informações, consulte [http://www.blitline.com (a página pode estar em inglês)](http://www.blitline.com).

## <a id="whatisnot"></a>O que o Blitline NÃO é...

Para esclarecer a utilidade do Blitline, geralmente é mais fácil identificar o que o Blitline NÃO faz antes de seguir adiante.

- O Blitline NÃO tem widgets HTML para carregar imagens. Você deve ter imagens disponíveis publicamente ou com permissões restritas disponíveis para o Blitline.

- O Blitline NÃO faz o processamento de imagens ao vivo, como o Aviary.com.

- O Blitline NÃO aceita carregamentos de imagens, e não é possível enviar suas imagens por push para o Blitline diretamente. Você deve enviá-las ao Armazenamento do Azure ou a outros locais com suporte do Blitline e, em seguida, informar ao Blitline onde obtê-las.

- O Blitline é massivamente paralelo e NÃO faz nenhum processamento síncrono. Ou seja, você deve nos fornecer um postback_url para que possamos informar a conclusão do processamento.

## <a id="createaccount"></a>Criar uma conta do Blitline

[WACOM.INCLUDE [blitline-signup](../includes/blitline-signup.md)]

## <a id="createjob"></a>Como criar um trabalho do Blitline

O Blitline usa JSON para definir as ações que você deseja executar em uma imagem. Esse JSON é composto de alguns campos simples.

O exemplo mais simples é o seguinte:

	    json : '{
       "application_id": "MY_APP_ID",
       "src" : "http://cdn.blitline.com/filters/boys.jpeg",
       "functions" : [ {
           "name": "resize_to_fit",
           "params" : { "width": 240, "height": 140 },
           "save" : { "image_identifier" : "external_sample_1" }
       } ]
    }'

Aqui temos um JSON que pegará uma imagem "src" "...boys.jpeg" e, em seguida, a redimensionará para 240x140.

A ID do aplicativo você pode encontrar na guia **INFORMAÇÕES DE CONEXÃO** ou **GERENCIAR** no Azure. É o seu identificador secreto que permite que você execute trabalhos no Blitline.

O parâmetro "save" identifica informações sobre onde você deseja colocar a imagem depois do processamento. Neste caso trivial, não definimos um local. Se nenhum local for definido, o Blitline irá armazená-la localmente (e temporariamente) em um local exclusivo na nuvem. Você poderá obter esse local no JSON retornado pelo Blitline quando fizer o Blitline. O identificador "image" é necessário e é retornado ao identificar essa imagem salva em particular.

Você pode encontrar mais informações sobre as *funções* com suporte aqui: <http://www.blitline.com/docs/functions>

Também pode encontrar documentação sobre as opções de trabalho aqui: <http://www.blitline.com/docs/api>

Quando tiver seu JSON, tudo o que deverá fazer será **POSTÁ-LO** em "http://api.blitline.com/jobs"

O JSON de volta será semelhante a isto:

    {
     "results":
         {"images":
            [{
              "image_identifier":"external_sample_1",
              "s3_url":"https://s3.amazonaws.com/dev.blitline/2011110722/YOUR_APP_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg"
            }],
          "job_id":"4eb8c9f72a50ee2a9900002f"
         }
    }


Isso informa a você que o Blitline recebeu sua solicitação, a colocou em uma fila de processamento e, quando ela for concluída, a imagem será disponibilizada em:
**https://s3.amazonaws.com/dev.blitline/2011110722/YOUR\_APP\_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg**

## <a id="saveazure"></a>Como salvar uma imagem em sua conta de armazenamento do Azure

Se tiver uma conta de armazenamento do Azure, o Blitline poderá enviar por push as imagens processadas para seu contêiner do Azure com facilidade. Ao adicionar um "azure_destination", você define o local e as permissões para o Blitline enviar por push.

Aqui está um exemplo:

    job : '{
      "application_id": "YOUR_APP_ID",
      "src" : "http://www.google.com/logos/2011/houdini11-hp.jpg",
         "functions" : [{
         "name": "blur",
         "save" : {
             "image_identifier" : "YOUR_IMAGE_IDENTIFIER",
             "azure_destination" : {
                 "account_name" : "YOUR_AZURE_CONTAINER_NAME",
                 "shared_access_signature" : "SAS_THAT_GIVES_BLITLINE_PERMISSION_TO_WRITE_THIS_OBJECT_TO_CONTAINER",
               }
           }
         }]
       }'


Ao preencher os valores EM LETRAS MAIÚSCULAS com seus próprios valores, você pode enviar esse JSON para http://api.blitline.com/job e a imagem "src" será processada com um filtro de desfoque e, em seguida, enviada por push ao destino do Azure.

<h3>Observação:</h3>

As SAS devem conter a URL inteira de SAS, incluindo o nome do arquivo de destino.

Exemplo:

    http://blitline.blob.core.windows.net/sample/image.jpg?sr=b&sv=2012-02-12&st=2013-04-12T03%3A18%3A30Z&se=2013-04-12T04%3A18%3A30Z&sp=w&sig=Bte2hkkbwTT2sqlkkKLop2asByrE0sIfeesOwj7jNA5o%3D


Você também pode ler a última edição de documentos do armazenamento do Azure do Blitline [aqui (a página pode estar em inglês)](http://www.blitline.com/docs/azure_storage).


## <a id="nextsteps"></a>Próximas etapas

Acesse blitline.com para ler sobre todos os nossos outros recursos (as páginas podem estar em inglês):

* Documentos de ponto de extremidade da API do Blitline <http://www.blitline.com/docs/api>
* Funções da API do Blitline <http://www.blitline.com/docs/functions>
* Exemplos da API do Blitline <http://www.blitline.com/docs/examples>
* Biblioteca de Nuget de terceiros <http://nuget.org/packages/Blitline.Net>


  [Próximas etapas]: #nextsteps
  [O que é Blitline?]: #whatis
  [O que o Blitline NÃO é]: #whatisnot
  [Criar uma conta do Blitline]: #createaccount
  [Como criar um trabalho do Blitline]: #createjob
  [Como salvar uma imagem em seu armazenamento do Azure]: #saveazure


<!--HONumber=35.2-->
