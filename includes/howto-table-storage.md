## <a name="what-is"> </a>O que é o serviço Tabela

O serviço de armazenamento Tabela do Windows Azure armazena grandes quantidades de dados estruturados. O serviço é um repositório de dados NoSQL que aceita chamadas autenticadas de dentro e de fora da nuvem do Azure. As tabelas do Azure são ideais para armazenar dados estruturados não relacionais. Os usos comuns do serviço Tabela incluem:

-   Armazenamento de TBs de dados estruturados capazes de atender a aplicativos de dimensionamento da Web
-   Armazenamento de conjuntos de dados que não exigem junções complexas, chaves estrangeiras ou procedimentos armazenados e que podem ser desnormalizados para acesso rápido
-   Consulta rápida de dados usando um índice clusterizado
-   Acesso a dados usando o protocolo OData e consultas LINQ com bibliotecas WCF Data Service .NET

Você pode usar o serviço Tabela para armazenar e consultar grandes conjuntos de dados estruturados não relacionais, e suas tabelas serão dimensionadas conforme a demanda aumentar.

## <a name="concepts"> </a>Conceitos

O serviço Tabela contém os seguintes componentes:

![Table1][Table1]

-   **Formato de URL:** o código endereça as tabelas de uma conta usando este formato de endereço:
    http://`<storage account>`.table.core.windows.net/`<table>`     
    Você pode endereçar as tabelas do Azure diretamente usando esse endereço com o protocolo OData. Para obter mais informações, consulte [OData.org][]

-   **Conta de armazenamento:** todo o acesso ao armazenamento do Azure é feito por meio de uma conta de armazenamento. Consulte [Metas de desempenho e escalabilidade do armazenamento do Azure](http://msdn.microsoft.com/pt-br/library/dn249410.aspx) para obter detalhes sobre a capacidade da conta de armazenamento.

-   **Tabela**: Uma tabela é uma coleção de entidades. As tabelas não impõem um esquema nas entidades, o que significa que uma única tabela pode conter entidades com diferentes conjuntos de propriedades. O número de tabelas que uma 
	conta de armazenamento pode conter é limitado apenas pelo limite de capacidade da conta de armazenamento.

-   **Entidade**: Uma entidade é um conjunto de propriedades, similar a uma linha do banco de dados. Uma entidade pode ser de até 1MB.

-   **Propriedades**: uma propriedade é um par nome-valor. Cada entidade pode incluir até 252 propriedades para armazenar dados. Cada entidade possui também 3 propriedades do sistema que especificam uma chave de partição, uma chave de linha e um carimbo de hora. As entidades com a mesma chave de partição podem ser consultadas mais rapidamente e inseridas/atualizadas em operações atômicas. A chave de linha de uma entidade é seu identificador exclusivo dentro de uma partição.


  
  [Table1]: ./media/storage-java-how-to-use-table-storage/table1.png
  [OData.org]: http://www.odata.org/
<!--HONumber=42-->
