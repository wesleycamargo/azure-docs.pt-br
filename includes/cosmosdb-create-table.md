Agora você pode usar o Data Explorer para criar um contêiner de gráfico e adicionar dados ao banco de dados. 

1. No Portal do Azure, no menu de navegação, em **Tabelas**, clique em **Data Explorer**. 
2. Na folha Data Explorer, clique em **Nova Coleção** e, em seguida, preencha a página usando as informações a seguir.

    ![Data Explorer no Portal do Azure](./media/cosmosdb-create-graph/azure-cosmosdb-data-explorer.png)

    Configuração|Valor sugerido|Descrição
    ---|---|---
    ID do banco de dados|banco de dados de exemplo|A ID do novo banco de dados. Os nomes de banco de dados devem ter entre um e 255 caracteres e não podem conter `/ \ # ?` nem espaços à direita.
    ID da coleção|tabela de exemplo|A ID da nova coleção. Nomes de coleção têm os mesmos requisitos de caractere que IDs de banco de dados.
    Capacidade de Armazenamento| 10 GB|Mantenha o valor padrão. Essa é a capacidade de armazenamento do banco de dados.
    Taxa de transferência|400 RUs|Mantenha o valor padrão. Você pode escalar verticalmente a taxa de transferência mais tarde se desejar reduzir a latência.
    Chave de partição|/databases|Uma chave de partição que distribuirá dados uniformemente para cada partição. É importante selecionar a chave de partição correta ao criar uma coleção de alto desempenho, leia mais a respeito em [Design de particionamento](../articles/cosmos-db/partition-data.md#designing-for-partitioning).

3. Quando o formulário estiver preenchido, clique em **OK**.