## <a name="what-is"> </a>O que é Armazenamento de Blob

Armazenamento de Blob do Azure é um serviço para armazenar grandes quantidades de dados
não estruturados que podem ser acessados de qualquer lugar do mundo por meio de
HTTP ou HTTPS. Um único blob pode ter centenas de gigabytes de tamanho.

Uma conta de armazenamento do Azure pode conter grandes quantidades de dados de blob, tabela e fila. Consulte [Escalabilidade e Metas de Desempenho do Armazenamento do Azure][] para obter detalhes sobre a capacidade da conta de armazenamento.

Usos comuns de armazenamento de Blob incluem:

-   Fornecer imagens ou documentos diretamente a um navegador
-   Armazenar arquivos para acesso distribuído
-   Transmitir por streaming áudio e vídeo
-   Executar recuperação de desastre e backup seguro
-   Armazenar dados para análise por um serviço local ou hospedado pelo
    Azure

Você pode usar o armazenamento de Blob para expor dados publicamente para o mundo ou
em particular para o armazenamento interno do aplicativo.

## <a name="concepts"> </a>Conceitos

O serviço Blob contém os seguintes componentes:

![Blob1][]

-   **Conta de Armazenamento:** Todo o acesso ao Armazenamento do Azure é feito por meio de uma conta de armazenamento. Consulte [Escalabilidade e Metas de Desempenho do Armazenamento do Azure][] para obter detalhes sobre a capacidade da conta de armazenamento.

-   **Contêiner:** Um contêiner fornece um agrupamento de um conjunto de blobs. Todos os blobs devem estar em um contêiner. Uma conta pode conter um número ilimitado de contêineres. Um contêiner pode armazenar um número ilimitado de blobs.

-   **Blob:** Um arquivo de qualquer tipo e tamanho. Existem dois tipos de blobs que podem ser armazenados no Armazenamento do Azure: blobs de blocos e de páginas. A maioria dos arquivos são blobs de blocos. Um único blob de blocos pode ter até 200 GB de tamanho. Este tutorial usa blobs de bloco. Os blobs de páginas, um outro tipo de blob, podem ter até 1 TB de tamanho e são mais eficientes quando os intervalos de bytes em um arquivo são modificados com frequência. Para obter mais informações sobre blobs, consulte [Noções gerais sobre blobs de bloco e blobs de página][].

-   **Formato de URL:** Blobs endereçáveis usando o seguinte formato de URL:
    <http://>`<storage account>`.blob.core.windows.net/`<container>`/`<blob>`

    A URL função Web a seguir poderia ser usada para endereçar um dos blobs no diagrama acima:
    `http://sally.blob.core.windows.net/movies/MOV1.AVI`

  [Escalabilidade e Metas de Desempenho do Armazenamento do Azure]: http://msdn.microsoft.com/en-us/library/dn249410.aspx
  [Blob1]: ./media/howto-blob-storage/blob1.jpg
