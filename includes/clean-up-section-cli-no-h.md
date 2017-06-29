Nas etapas anteriores, você criou os recursos do Azure em um grupo de recursos. Se você não espera precisar desses recursos no futuro, poderá excluí-los ao excluir o grupo de recursos.
 
1. Execute o seguinte comando para verificar se o grupo de recursos não contém todos os recursos que você deseja salvar:

  ```azurecli
  az group show --name myResourceGroup
  ```

2. Se os recursos mostrados forem aqueles que deseja excluir, execute o seguinte comando:
 
  ```azurecli
  az group delete --name myResourceGroup
  ```
