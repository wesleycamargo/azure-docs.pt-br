## O que é Blob Storage

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

## Nomeando e referenciando contêineres e blobs

Você pode endereçar um blob na conta de armazenamento usando o seguinte formato de URL:
   
    http://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>  
      
Por exemplo, eis uma URL que endereça um dos blobs no diagrama acima:

    http://sally.blob.core.windows.net/movies/MOV1.AVI

### Regras de nomeação de contêiner

Um nome de contêiner deve ser um nome DNS válido e estar em conformidade com as seguintes regras:

- Um nome de contêiner deve estar em letras minúsculas.
- Os nomes de contêiner devem começar com uma letra ou número e podem conter apenas letras, números e o caractere traço (-).
- Cada caractere traço (-) deve ser imediatamente precedido e seguido por uma letra ou número. Não são permitidos traços consecutivos em nomes de contêiner.
- Os nomes de contêiner devem ter de 3 a 63 caracteres.

### Regras de nomenclatura blob

Um nome blob deve obedecer às seguintes regras:

- Um nome blob pode conter qualquer combinação de caracteres.
- Um nome blob deve ter pelo menos um caractere e não pode ter mais de 1.024 caracteres.
- Os nomes blob diferenciam letras maiúsculas de minúsculas.
- Os caracteres reservados de URL devem ser substituídos corretamente.
- O número de segmentos de caminho que inclui o nome blob não pode exceder 254. Um segmento de caminho é a cadeia de caracteres entre caracteres delimitadores consecutivos (*por exemplo*, a barra '/') que corresponde ao nome de um diretório virtual.

O serviço Blob baseia-se em um esquema de armazenamento simples. Você pode criar uma hierarquia virtual especificando um delimitador de caractere ou de cadeia de caracteres no nome blob para criar uma hierarquia virtual. Por exemplo, a lista a seguir mostra alguns nomes blob válidos e exclusivos:

	/a
	/a.txt
	/a/b
	/a/b.txt

Você pode usar o caractere delimitador para listar blobs hierarquicamente.


[Blob1]: ./media/storage-blob-concepts-include/blob1.jpg

<!---HONumber=AcomDC_0218_2016-->