<properties linkid="develop-python-blob-service" urlDisplayName="Serviço de Blob" pageTitle="Como usar armazenamento de blob (Python) | Microsoft Azure" metaKeywords="Serviço de blob Azure Python, Azure blobs Python" description="Aprenda a usar o serviço de Blobs Azure para upload, lista, download e excluir blobs." metaCanonical="" disqusComments="1" umbracoNaviHide="0" services="storage" documentationCenter="Python" title="Como usar o serviço Blob Python" authors="" videoId="" scriptId="" />

# Este guia mostra como executar cenários comuns usando o Serviço de Armazenamento de Blob Azure. As amostram são gravadas com a API do Python. Os cenários cobertos incluem **carregamento**, **listagem**, **download** e **exclusão** de blobs. Para obter mais informações sobre blobs,
consulte a seção [Próximas etapas][].

## Sumário

[O que é Armazenamento de Blob?][]   
 [Conceitos][]   
 [Criar uma conta de armazenamento do Azure][]   
 [Como criar um contêiner][]   
 [Como: carregar um blob em um contêiner][]   
 [Como listar os blobs em um contêiner][]   
 [Como baixar blobs][]   
 [Como: excluir um blob][]   
 [Como: carregar e baixar blobs grandes][]   
 [Próximas etapas][]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

## <a name="create-account"> </a>Criar uma conta de armazenamento do Azure

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="create-container"> </a>Como criar um contêiner

**Observação:** se você precisar instalar o Python ou as bibliotecas de cliente, consulte o [Guia de instalação do Python](../python-how-to-install/)


O objeto **Serviço de blob** permite que você trabalhe com contêineres e blobs. O código a seguir cria um objeto **BlobService**. Adicione o seguinte próximo à parte superior de qualquer arquivo Python no qual você deseje acessar o Armazenamento di Azure programaticamente:

	from azure.storage import *

O código a seguir cria um objeto **Serviço de blob** usando o nome da conta de armazenamento e a chave da conta.  Substitua 'Minha conta' e 'mykey' com a conta real e a chave.

	blob_service = BlobService(account_name='myaccount', account_key='mykey')

Todos os blobs de armazenamento residem em um contêiner. Você pode usar uma **BlobService** objeto para criar o contêiner, se ele não existir:

	blob_service.create_container('mycontainer')

Por padrão, o novo contêiner é particular; portanto, você deve especificar sua
chave de acesso de armazenamento (como anteriormente) para baixar blobs desse
contêiner. Se desejar disponibilizar os arquivos dentro do contêiner para todas as pessoas, você pode criar o contêiner e passar o nível de acesso público usando o seguinte código:

	blob_service.create_container('mycontainer', x_ms_blob_public_access='container') 

Como alternativa, você pode modificar um contêiner após você tê-la criado usando o seguinte código:

	blob_service.set_container_acl('mycontainer', x_ms_blob_public_access='container')

Após essa alteração, qualquer pessoa na internet pode ver blobs em um contêiner público, mas apenas você pode modificar ou excluí-los.

## <a name="upload-blob"> </a>Como carregar um blob em um contêiner

**Para carregar um arquivo em um blob, use o método **1colocar_blob** para criar o blob, usar um a cadeia de arquivo como o conteúdo de blob.
Primeiro, crie um arquivo chamado **task1.txt** (conteúdo arbitrário serve) e armazená-lo no mesmo diretório que o arquivo Python.

	myblob = open(r'task1.txt', 'r').read()
	blob_service.put_blob('mycontainer', 'myblob', myblob, x_ms_blob_type='BlockBlob')

## <a name="list-blob"> </a>Como listar os blobs em um contêiner

Para listar os blobs em um contêiner, use o método **listar_blobs** com um loop **para** exibir o nome de cada blob no contêiner. O código a seguir exibe o **nome** e **url** de cada blob em um contêiner para o console.

	blobs = blob_service.list_blobs('mycontainer')
	for blob in blobs:
		print(blob.name)
		print(blob.url)

## <a name="download-blobs"> </a>Como: baixar blobs

O exemplo a seguir usa o método **obter_blob** para transferir o conteúdo do blob para um objeto de fluxo que você pode persistir para um arquivo local.

	blob = blob_service.get_blob('mycontainer', 'myblob')
	with open(r'out-task1.txt', 'w') as f:
		f.write(blob)

## <a name="delete-blobs"> </a>Como: excluir um blob

Finalmente, para excluir um blob, chame **excluir_blob**.

	blob_service.delete_blob('mycontainer', 'myblob') 

## <a name="large-blobs"> </a>Como: carregar e baixar blobs grandes

O tamanho máximo de um blob de bloco é de 200 GB.  Para blobs menores que 64 MB, o blob podem ser carregado ou baixados com uma única chamada para **put\_blob** ou **get\_blob**, conforme mostrado anteriormente.  Para blobs maiores que 64 MB, o blob deve ser carregado ou descarregado em blocos de 4 MB ou menor.

O código a seguir mostra exemplos de funções para carregar ou baixar blobs de blocos de qualquer tamanho.

    import base64

    chunk_size = 4 * 1024 * 1024

    def upload(blob_service, container_name, blob_name, file_path):
        blob_service.create_container(container_name, None, None, False)
        blob_service.put_blob(container_name, blob_name, '', 'BlockBlob')

        block_ids = []
        index = 0
        with open(file_path, 'rb') as f:
            while True:
                data = f.read(chunk_size)
                if data:
                    length = len(data)
                    block_id = base64.b64encode(str(index))
                    blob_service.put_block(container_name, blob_name, data, block_id)
                    block_ids.append(block_id)
                    index += 1
                else:
                    break

        blob_service.put_block_list(container_name, blob_name, block_ids)

    def download(blob_service, container_name, blob_name, file_path):
        props = blob_service.get_blob_properties(container_name, blob_name)
        blob_size = int(props['content-length'])

        index = 0
        with open(file_path, 'wb') as f:
            while index < blob_size:
                chunk_range = 'bytes={}-{}'.format(index, index + chunk_size - 1)
                data = blob_service.get_blob(container_name, blob_name, x_ms_range=chunk_range)
                length = len(data)
                index += length
                if length > 0:
                    f.write(data)
                    if length < chunk_size:
                        break
                else:
                    break

Se você precisar de blobs mais de 200 GB, você pode usar um blob de página em vez de um blob de bloco.  O tamanho máximo de um blob de página é 1 TB com páginas que se alinham às quebras de página de 512 bytes.  Use **put\_blob** para criar um blob de página, **put\_page** para escrever para ele, e **get\_blob** para lê-lo.

## <a name="next-steps"> </a>Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de blogs, siga estes links para saber como fazer tarefas mais complexas de armazenamento.

-  Consulte a referência do MSDN: [Armazenando e acessando dados no Azure][]
-   Acesse o [Blog da Equipe de Armazenamento do Azure][]

  [Próximas etapas]: #next-steps
  [O que é Armazenamento de Blob?]: #what-is
  [Conceitos]: #concepts
  [Criar uma conta de armazenamento do Azure]: #create-account
  [Como criar um contêiner]: #create-container
  [Como: carregar um blob em um contêiner]: #upload-blob
  [Como listar os blobs em um contêiner]: #list-blob
  [Como baixar blobs]: #download-blobs
  [Como: excluir um blob]: #delete-blobs
  [Como: carregar e baixar blobs grandes]: #large-blobs
  [Armazenando e acessando dados no Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg433040.aspx
  [Blog da Equipe de Armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/

