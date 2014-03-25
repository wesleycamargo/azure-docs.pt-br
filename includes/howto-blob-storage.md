## <a name="what-is"> </a>O que é Blob Storage?

Armazenamento de Blob do Windows Azure é um serviço para armazenar grandes quantidades de dados não estruturados que podem ser acessados de qualquer lugar do mundo por meio de HTTP ou HTTPS. Um único blob pode ter centenas de gigabytes de tamanho e uma conta de armazenamento único pode conter até 200TB de blobs, se ele tiver sido criado em 8 de junho de 2012 ou depois; contas de armazenamento criadas antes dessa data podem conter até 100 TB de blobs. Consulte [Metas de Desempenho e de Escalabilidade de Armazenamento do Windows Azure](http://msdn.microsoft.com/pt-br/library/dn249410.aspx) para obter detalhes sobre a capacidade da conta de armazenamento.

Usos comuns de armazenamento de Blob incluem:

-   Fornecimento de imagens ou documentos diretamente em um navegador
-   Armazenamento de arquivos para acesso distribuído
-   Streaming de áudio e vídeo
-   Execução de backup de segurança e recuperação de desastres
-   Armazenamento de dados para análise por um serviço local ou hospedado pelo Windows Azure

Você pode usar o armazenamento de Blob para expor dados publicamente para o mundo ou em particular para o armazenamento interno do aplicativo.

## <a name="concepts"> </a>Conceitos

O serviço Blob contém os seguintes componentes:

![Blob1][Blob1]

-   **Conta de Armazenamento:** todo o acesso ao Armazenamento do Windows Azure é feito através de uma conta de armazenamento. Consulte [Metas de Desempenho e de Escalabilidade de Armazenamento do Windows Azure](http://msdn.microsoft.com/pt-br/library/dn249410.aspx) para obter detalhes sobre a capacidade da conta de armazenamento.

-   **Contêiner:** um contêiner fornece um agrupamento de um conjunto de blobs.
    Todos os blobs devem ter um contêiner. Uma conta pode conter um número ilimitado de contêineres. Um contêiner pode armazenar um número ilimitado de blobs.

-   **Blob:** um arquivo de qualquer tipo e tamanho. Há dois tipos de blobs que podem ser armazenados no Armazenamento do Windows Azure: blobs de página e de bloco.
    A maioria dos arquivos são blobs de bloco. Um blob de bloco único pode ser de até 200GB de tamanho. Este tutorial usa blobs de bloco. Blobs de página, outros tipos de blob, podem ter até 1TB de tamanho e são mais eficientes quando os intervalos de bytes em um arquivo são modificados com frequência. Para obter mais informações sobre os blobs, consulte [Compreendendo Blobs de bloco e Blobs de página][].

-   **Formato de URL:** Blobs são acessados usando o seguinte formato de URL:   
    http://'<storage
    account>'.blob.core.windows.net/'<container>'/'<blob>' 

     O exemplo a seguir de URL pode ser usado para lidar com um dos blobs no diagrama acima: 
     'http://sally.blob.core.windows.net/movies/MOV1.AVI'


  [Noções básicas sobre Blobs de Bloco e Blobs de Página]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee691964.aspx
[Blob1]: ./media/howto-blob-storage/blob1.jpg

