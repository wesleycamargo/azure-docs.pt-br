## <a name="create-a-storage-account-using-the-azure-portal"></a>Criar uma conta de armazenamento usando o Portal do Azure

Primeiro, crie uma nova conta de armazenamento de uso geral a ser usada para este guia de início rápido. 

1. Acesse o [Portal do Azure](https://portal.azure.com) e faça logon usando sua conta do Azure. 
2. No menu Hub, selecione **Novo** > **Armazenamento** > **Conta de armazenamento – blob, arquivo, tabela, fila**. 
3. Insira um nome exclusivo para sua conta de armazenamento. Lembre-se dessas regras ao nomear sua conta de armazenamento:
    - O nome deve ter entre 3 e 24 caracteres.
    - O nome pode conter apenas números e letras minúsculas.
4. Certifique-se de que os seguintes valores padrão estão definidos: 
    - O **Modelo de implantação** está definido como **Resource Manager**.
    - O **Tipo de conta** está definido como **Uso geral**.
    - O **Desempenho** está definido como **Padrão**.
    - A **Replicação** está definida como **LRS (Armazenamento com Redundância Local)**.
5. Selecione sua assinatura. 
6. Para o **Grupo de recursos**, crie um novo e dê a ele um nome exclusivo. 
7. Selecione o **Local** a ser usado para sua conta de armazenamento.
8. Marque **Fixar no painel** e clique em **Criar** para criar sua conta de armazenamento. 

Depois que a conta de armazenamento é criada, ela é fixada ao painel. Clique nela para abri-la. Em **Configurações**, clique em **Chaves de acesso**. Selecione a chave primária e copie a **Cadeia de conexão** associada para a área de transferência e cole-a no editor de texto para uso posterior.
