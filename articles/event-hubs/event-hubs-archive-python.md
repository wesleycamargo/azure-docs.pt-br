<properties
	pageTitle="Passo a passo do Arquivamento dos Hubs de Eventos do Azure | Microsoft Azure"
	description="Exemplo que usa o SDK do Python do Azure para demonstrar o uso do recurso Arquivamento dos Hubs de Eventos."
	services="event-hubs"
	documentationCenter=""
	authors="djrosanova"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/13/2016"
	ms.author="darosa;sethm"/>

# Passo a passo do Arquivamento dos Hubs de Eventos: Python

O Arquivamento dos Hubs de Eventos é um novo recurso dos Hubs de Eventos que permite que você envie automaticamente os dados de fluxo no Hub de Eventos a uma conta de Armazenamento de Blobs do Azure desejada. Isso facilita a execução de processamento em lote na transmissão de dados em tempo real. Este artigo descreve como usar o Arquivamento dos Hubs de Eventos com o Python. Para saber mais sobre Arquivamento dos Hubs de Eventos, confira o [artigo de visão geral](event-hubs-archive-overview.md).

Este exemplo usa o SDK do Python do Azure para demonstrar o uso do recurso Arquivamento. O sender.py envia telemetria de ambiente simulado para os Hubs de Eventos no formato JSON. O Hub de Eventos está configurado para usar o recurso Arquivamento a fim de gravar esses dados em armazenamento de blobs em lotes. Em seguida, o archivereader.py lê esses blobs e cria um arquivo de acréscimo por dispositivo e grava os dados em arquivos. csv.

O que será realizado

1.  Criar uma conta de Armazenamento de Blobs do Azure e um contêiner de blobs dentro dele, usando o portal do Azure

2.  Criar um namespace do Hub de Eventos usando o portal do Azure

3.  Criar um Hub de Eventos com o recurso Arquivamento habilitado, usando o portal do Azure

4.  Enviar dados para o Hub de Eventos com um script Python

5.  Ler os arquivos do arquivo e processá-los com outro script Python

Pré-requisitos

1.  Python 2.7.x

2.  Uma assinatura do Azure

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## Criar uma conta de Armazenamento do Azure

1.  Faça logon no [portal do Azure][].

2.  No painel de navegação esquerdo do portal, clique em Novo, clique em Dados + Armazenamento e em Conta de Armazenamento.

3.  Preencha os campos na folha da conta de armazenamento e clique em **Criar**.

    ![][1]

4.  Depois de ver a mensagem **Implantações Bem-sucedidas**, clique na nova conta de armazenamento e, na folha **Essentials**, clique em **Blobs**. Quando a folha **Serviço Blob** abrir, clique em **+ Contêiner** na parte superior. Nomeie o **arquivo** do contêiner e feche a folha **Serviço Blob**.

5.  Clique em **Chaves de acesso** na folha esquerda e copie o nome da conta de armazenamento e o valor de **key1**. Salve esses valores no Bloco de notas ou em outro local temporário.

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

## Criar um script Python para enviar eventos a seu Hub de Eventos

1.  Abra seu editor favorito do Python, como o [Visual Studio Code][].

2.  Crie um script chamado **sender.py**. Esse script enviará 200 eventos para seu Hub de Eventos. Eles são simples leituras de ambiente enviadas em JSON.

3.  Cole o seguinte código em sender.py:

	```
	import uuid
	import datetime
	import random
	import json
	from azure.servicebus import ServiceBusService
	
	sbs = ServiceBusService(service_namespace='INSERT YOUR NAMESPACE NAME', shared_access_key_name='RootManageSharedAccessKey', shared_access_key_value='INSERT YOUR KEY')
	devices = []
	for x in range(0, 10):
	    devices.append(str(uuid.uuid4()))
	
	for y in range(0,20):
	    for dev in devices:
	        reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
	        s = json.dumps(reading)
	        sbs.send\_event('myhub', s)
	    print y
	```
4.  Atualize o código anterior para usar o nome do namespace e os valores de chave que você obteve quando criou o namespace dos Hubs de Eventos.

## Criar um script Python para ler os arquivos no arquivo

1.  Preencha a folha e clique em **Criar**.

2.  Crie um script chamado **archivereader.py**. Esse script lerá os arquivos no arquivo e criará um arquivo por dispositivo para gravar os dados somente para esse dispositivo.

3.  Cole o código abaixo no archivereader.py:

	```
    import os
	import string
	import json
	import avro.schema
	from avro.datafile import DataFileReader, DataFileWriter
	from avro.io import DatumReader, DatumWriter
	from azure.storage.blob import BlockBlobService
	
	def processBlob(filename):
	    reader = DataFileReader(open(filename, 'rb'), DatumReader())
	    dict = {}
	    for reading in reader:
	        parsed\_json = json.loads(reading["Body"])
	        if not 'id' in parsed\_json:
	            return
	        if not dict.has\_key(parsed\_json['id']):
	        list = []
	        dict[parsed\_json['id']] = list
	    else:
	        list = dict[parsed\_json['id']]
	        list.append(parsed\_json)
	    reader.close()
	    for device in dict.keys():
	        deviceFile = open(device + '.csv', "a")
	        for r in dict[device]:
	            deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\\n')

	def startProcessing(accountName, key, container):
	    print 'Processor started using path: ' + os.getcwd()
	    block\_blob\_service = BlockBlobService(account\_name=accountName, account\_key=key)
	    generator = block\_blob\_service.list\_blobs(container)
	    for blob in generator:
	        if blob.properties.content\_length != 0:
	            print('Downloaded a non empty blob: ' + blob.name)
	            cleanName = string.replace(blob.name, '/', '\_')
	            block\_blob\_service.get\_blob\_to\_path(container, blob.name, cleanName)
	            processBlob(cleanName)
	            os.remove(cleanName)
	        block\_blob\_service.delete\_blob(container, blob.name)
	startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'archive')
    ```

4.  Não se esqueça de colar os valores apropriados do nome da sua conta de armazenamento e da chave na chamada para `startProcessing`.

## Executar os scripts

1.  Abra um prompt de comando com o Python em seu caminho e execute estes comandos para instalar os pacotes de pré-requisito do Python:

	```
    pip install azure-storage
	pip install azure-servicebus
	pip install avro
    ```
  
    Se você tiver uma versão anterior do Azure ou do Armazenamento do Azure, talvez precise usar a opção **– atualizar**

    Você também precisa executar o seguinte (desnecessário na maioria dos sistemas):

    ```
    pip install cryptography
    ```

2.  Altere o diretório para onde você salvou sender.py e archivereader.py e execute este comando:

    ```
    start python sender.py
    ```
    
    Isso inicia um novo processo de Python para executar o remetente.

3. Agora, aguarde alguns minutos para que o arquivamento seja executado. Em seguida, digite o seguinte comando na janela de comando original:

    ```
    python archivereader.py
    ```

Esse processador de arquivamento usa o diretório local para baixar todos os blobs do contêiner/da conta de armazenamento. Ele processa todos os que não estão vazios e grava os resultados como arquivos. csv no diretório local.

## Próximas etapas

Você pode saber mais sobre Hubs de Eventos visitando os links abaixo:

- Visão geral do [arquivamento dos Hubs de Eventos][]
- Um [aplicativo de exemplo completo que usa os Hubs de Evento][].
- O exemplo de [Escala horizontal do processamento de eventos com Hubs de Eventos][].
- [Visão geral de Hubs de Evento][]
 

[portal do Azure]: https://portal.azure.com/
[arquivamento dos Hubs de Eventos]: event-hubs-archive-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]: https://azure.microsoft.com/documentation/articles/storage-create-storage-account/
[Visual Studio Code]: https://code.visualstudio.com/
[Visão geral de Hubs de Evento]: event-hubs-overview.md
[aplicativo de exemplo completo que usa os Hubs de Evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Escala horizontal do processamento de eventos com Hubs de Eventos]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3

<!---HONumber=AcomDC_0914_2016-->