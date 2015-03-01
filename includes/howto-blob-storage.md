## <a name="what-is"> </a>O que é Armazenamento de Blob

O armazenamento de Blob do Azure é um serviço para armazenar grandes quantidades dedados não estruturados, como texto ou dados binários, que podem ser acessados de qualquer lugar do mundo por meio de
HTTP ou HTTPS. Você pode usar o armazenamento de Blob para expor dados publicamente para o mundo ou para armazenar dados do aplicativo de forma privada.

Usos comuns de armazenamento de Blob incluem:

-   Fornecer imagens ou documentos diretamente a um navegador
-   Armazenar arquivos para acesso distribuído
-   Transmitir por streaming áudio e vídeo
-   Executar recuperação de desastre e backup seguro
-   Armazenar dados para análise por um serviço local ou hospedado pelo
    Azure

## <a name="concepts"> </a>Conceitos

O serviço Blob contém os seguintes componentes:

![Blob1][Blob1]

-   **Conta de armazenamento:** todo o acesso ao armazenamento do Azure é feito
    por meio de uma conta de armazenamento. Consulte [Metas de desempenho e escalabilidade do Armazenamento do Azure](http://msdn.microsoft.com/pt-br/library/dn249410.aspx) para obter detalhes sobre a capacidade da conta de armazenamento.

-   **Contêiner:** um contêiner fornece um agrupamento de conjunto de blobs.
    Todos os blobs devem ter um contêiner. Uma conta pode conter um
    número ilimitado de contêineres. Um contêiner pode armazenar um número
    ilimitado de blobs.

-   **Blob:** um arquivo de qualquer tipo e tamanho. Há dois tipos de blobs
    que podem ser armazenados no Armazenamento do Azure: blobs de blocos e de páginas.
    A maioria dos arquivos são blobs de bloco. Um único blob de bloco pode ter até 200 GB
    de tamanho. Este tutorial usa blobs de bloco. Blobs de página, outro tipo
    de blob, podem ter até 1 TB de tamanho e são mais eficientes quando intervalos
    de bytes em um arquivo são alterados com frequência. Para obter mais informações
    sobre blobs, consulte [Noções gerais sobre blobs de blocos e blobs de páginas][].

-   **Formato de URL:** os blobs são endereçáveis usando o seguinte formato
    de URL:   
    http://`<storage
    account>`.blob.core.windows.net/`<container>`/`<blob>`  
      
    A URL de exemplo a seguir poderia ser usada para endereçar um dos blobs no
    diagrama acima:  
    `http://sally.blob.core.windows.net/movies/MOV1.AVI`


[Blob1]: ./media/howto-blob-storage/blob1.jpg

<!--HONumber=41-->
