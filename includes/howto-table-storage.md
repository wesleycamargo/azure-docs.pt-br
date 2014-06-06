## <a name="what-is"> </a>O que é o serviço Tabela?

O serviço de armazenamento de tabela do Azure armazena grandes quantidades de dados estruturados. O serviço é um armazenamento de dados NoSQL que aceita chamadas autenticadas dentro e fora da nuvem do Azure. As tabelas do Azure são ideais para armazenar dados estruturados e não relacionais. Os usos comuns do serviço de tabela incluem:

-   Armazenamento de TBs de dados estruturados capazes de fornecer o dimensionamento de aplicativos Web
-   Armazenamento de conjuntos de dados que não exigem relações complexas, chaves estrangeiras ou procedimentos armazenados e podem ser desnormalizados para acesso rápido
-   Rapidamente consultar dados usando um índice de cluster
-   Acesso a dados usando o protocolo OData e consultas LINQ com bibliotecas WCF Data Service .NET

Você pode usar o serviço de tabela para armazenar e consultar grandes conjuntos de dados estruturados e não relacionais, e suas tabelas serão dimensionadas conforme a demanda aumentar.

## <a name="concepts"> </a>Conceitos

O serviço Tabela contém os seguintes componentes:

![Tabela1][Table1]

-   **Formato de URL:** o código aborda tabelas em uma conta usando esse formato de endereço:   
    http://`<storage account>`.table.core.windows.net/`<table>` 

    Você pode lidar com tabelas do Azure diretamente usando esse endereço com o protocolo OData. Para obter mais informações, consulte [OData.org][]

-   **Conta de Armazenamento:** todo o acesso ao Armazenamento do Azure é feito através de uma conta de armazenamento. Consulte [Metas de Desempenho e de Escalabilidade de Armazenamento do Azure](http://msdn.microsoft.com/pt-br/library/dn249410.aspx) para obter detalhes sobre a capacidade da conta de armazenamento.

-   **Tabela**: uma tabela é uma coleção de entidades. Tabelas não impõem um esquema em entidades, o que significa que uma única tabela pode conter entidades que possuem diferentes conjuntos de propriedades. O número de tabelas que uma 
	conta de armazenamento pode conter é limitado apenas pelo limite de capacidade da conta de armazenamento.

-   **Entidade**: uma entidade é um conjunto de propriedades, semelhante a uma linha do banco de dados. Uma entidade pode ser de até 1MB.

-   **Propriedades**: uma propriedade é um par de nome-valor. Cada entidade pode incluir até 252 propriedades para armazenar dados. Cada entidade possui também 3 propriedades do sistema que especificam uma chave de partição, uma chave de linha e um carimbo de hora. Entidades com a mesma chave de partição podem ser consultadas mais rapidamente e inseridas/atualizadas em operações atômicas. Uma chave de linha da entidade é seu identificador exclusivo dentro de uma partição.


  
  [Tabela1]: ./media/storage-java-how-to-use-table-storage/table1.png
  [OData.org]: http://www.odata.org/

