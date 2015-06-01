## O que é Blob Storage

A Armazenamento de Blob do Azure é um serviço para armazenar grandes quantidades de dados não estruturados, como texto ou dados binários, que podem ser acessados de qualquer lugar do mundo por meio de HTTP ou HTTPS. Você pode usar o armazenamento de Blob para expor dados publicamente para o mundo ou para armazenar dados do aplicativo de forma privada.

Usos comuns de armazenamento de Blob incluem:

-   Fornecer imagens ou documentos diretamente a um navegador
-   Armazenar arquivos para acesso distribuído
-   Transmitir por streaming áudio e vídeo
-   Executar recuperação de desastre e backup seguro
-   Armazenando dados para análise por um serviço local ou hospedado do Azure

## Conceitos do Serviço de Blob

O serviço Blob contém os seguintes componentes:

![Blob1][Blob1]

-   **Conta de Armazenamento:** todo o acesso ao Armazenamento do Azure é feito através de uma conta de armazenamento. Consulte [Escalabilidade e Metas de Desempenho do Armazenamento do Azure](storage-scalability-targets.md) para obter detalhes sobre a capacidade da conta de armazenamento.

-   **Contêiner:** um contêiner fornece um agrupamento de conjunto de blobs. Todos os blobs devem ter um contêiner. Uma conta pode conter um número ilimitado de contêineres. Um contêiner pode armazenar um número ilimitado de blobs.

-   **Blob:** um arquivo de qualquer tipo e tamanho. Existem dois tipos de blobs que podem ser armazenados no Armazenamento do Azure: blobs de blocos e de páginas. A maioria dos arquivos são blobs de bloco. Um único blob de blocos pode ter até 200 GB de tamanho. Este tutorial usa blobs de bloco. Os blobs de página, um outro tipo de blob, podem ter até 1 TB de tamanho e são mais eficientes quando os intervalos de bytes em um arquivo são modificados com frequência. Para obter mais informações sobre blobs, consulte [Noções gerais sobre blobs de blocos e blobs de páginas (a página pode estar em inglês)](https://msdn.microsoft.com/library/azure/ee691964.aspx).

-   **Formato da URL:** os blobs são endereçáveis usando o seguinte formato de URL: http://`<storage
    account>`.blob.core.windows.net/`<container>`/`<blob>`
      
    A URL de exemplo a seguir poderia ser usada para endereçar um dos blobs no diagrama acima: `http://sally.blob.core.windows.net/movies/MOV1.AVI`


[Blob1]: ./media/storage-blob-concepts-include/blob1.jpg


<!--HONumber=52-->
