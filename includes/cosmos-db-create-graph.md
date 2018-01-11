Agora, você pode usar a ferramenta Data Explorer no portal do Azure para criar um banco de dados de grafo. 

1. No portal do Azure, no menu à esquerda, selecione **Data Explorer (Visualização)**.

2. Em **Data Explorer (Visualização)**, selecione **Novo Grafo**. Em seguida, preencha a página usando as seguintes informações:

    ![Data Explorer no Portal do Azure](./media/cosmos-db-create-graph/azure-cosmosdb-data-explorer.png)

    Configuração|Valor sugerido|DESCRIÇÃO
    ---|---|---
    ID do banco de dados|banco de dados de exemplo|Digite *banco de dados de exemplo* como o nome do novo banco de dados. Os nomes de banco de dados devem ter entre um e 255 caracteres e não podem conter `/ \ # ?` nem espaços à direita.
    Id do Grafo|grafo de exemplo|Digite *grafo de exemplo* como o nome da nova coleção. Os nomes de grafo têm os mesmos requisitos de caractere do que as IDs de banco de dados.
    Capacidade de armazenamento| 10 GB|Mantenha o valor padrão. Essa é a capacidade de armazenamento do banco de dados.
    Taxa de transferência|400 RUs|Mantenha o valor padrão. Você pode escalar verticalmente a taxa de transferência mais tarde se desejar reduzir a latência.

3. Depois que o formulário é preenchido, selecione **OK**.
