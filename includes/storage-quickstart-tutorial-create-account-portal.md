## <a name="create-a-storage-account-by-using-the-azure-portal"></a>Criar uma conta de armazenamento usando o Portal do Azure

Primeiro, crie uma nova conta de armazenamento de uso geral a ser usada para este guia de início rápido. 

1. Acesse o [Portal do Azure](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM) e entre usando sua conta do Azure. 
2. Insira um nome exclusivo para sua conta de armazenamento. Lembre-se dessas regras ao nomear sua conta de armazenamento:
    - O nome deve ter entre 3 e 24 caracteres.
    - O nome pode conter apenas números e letras minúsculas.
3. Certifique-se de que os seguintes valores padrão estão definidos: 
    - O **Modelo de implantação** está definido como **Resource Manager**.
    - O **Tipo de conta** está definido como **Uso geral**.
    - O **Desempenho** está definido como **Padrão**.
    - A **Replicação** está definida como **LRS (Armazenamento com Redundância Local)**.
4. Selecione sua assinatura. 
5. Para o **Grupo de recursos**, crie um novo e dê a ele um nome exclusivo. 
6. Selecione o local a ser usado para sua conta de armazenamento.
7. Selecione **Fixar no painel** e clique em **Criar** para criar sua conta de armazenamento. 

Depois que a conta de armazenamento é criada, ela é fixada ao painel. Selecione-a para abri-la. Em **Configurações**, selecione **Chaves de acesso**. Selecione a chave primária e copie a cadeia de conexão associada para a área de transferência. Em seguida, cole a cadeia de caracteres em um editor de texto para uso posterior.
