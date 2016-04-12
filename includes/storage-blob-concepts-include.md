## O que é Armazenamento de Blob?

A Armazenamento de Blob do Azure é um serviço para armazenar grandes quantidades de dados não estruturados, como texto ou dados binários, que podem ser acessados de qualquer lugar do mundo por meio de HTTP ou HTTPS. Você pode usar o armazenamento de Blob para expor dados publicamente para o mundo ou para armazenar dados do aplicativo de forma privada.

Usos comuns de armazenamento de Blob incluem:

-   Fornecer imagens ou documentos diretamente a um navegador
-   Armazenar arquivos para acesso distribuído
-   Transmitir por streaming áudio e vídeo
-   Executar recuperação de desastre e backup seguro
-   Armazenando dados para análise por um serviço local ou hospedado do Azure

## Conceitos do Serviço Blob

O serviço Blob contém os seguintes componentes:

![Blob1][Blob1]

-   **Conta de Armazenamento:** todo o acesso ao Armazenamento do Azure é feito através de uma conta de armazenamento. Consulte [Escalabilidade e Metas de Desempenho do Armazenamento do Azure](storage-scalability-targets.md) para obter detalhes sobre a capacidade da conta de armazenamento.

-   **Contêiner:** um contêiner fornece um agrupamento de conjunto de blobs. Todos os blobs devem ter um contêiner. Uma conta pode conter um número ilimitado de contêineres. Um contêiner pode armazenar um número ilimitado de blobs.

-   **Blob:** um arquivo de qualquer tipo e tamanho. O armazenamento do Azure oferece três tipos de blobs: blob de blocos, blob de páginas e blob de anexo.
    
	*Blobs de blocos* são ideais para armazenar arquivos de texto ou binários, como documentos e arquivos de mídia. *Blobs de anexo* são semelhantes aos blobs de blocos, pois são constituídos de blocos, mas são otimizados para anexas operações. Portanto, são úteis em cenários de registro em log. Um único blob de blocos ou acrescentar blob pode conter até 50.000 blocos de até 4 MB cada um, para um tamanho total de um pouco mais de 195 GB (4 MB X 50.000).
    
	*Blobs de páginas* podem ter até 1 TB e são mais eficientes para operações frequentes de leitura/gravação. Máquinas virtuais do Azure usam blobs de páginas como sistema operacional e discos de dados.

	Para obter mais informações sobre os blobs, confira [Compreendendo os Blobs de Bloco, Blobs de Anexo e Blobs de Página](https://msdn.microsoft.com/library/azure/ee691964.aspx).


[Blob1]: ./media/storage-blob-concepts-include/blob1.jpg

<!---HONumber=AcomDC_0406_2016-->