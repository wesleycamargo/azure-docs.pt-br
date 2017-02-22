## <a name="next-steps"></a>Próximas etapas
Depois de habilitar a integração do Cofre da Chave do Azure, você poderá habilitar a criptografia do SQL Server em sua VM do SQL. Primeiro, você precisará criar uma chave assimétrica dentro de seu cofre de chave e uma chave simétrica no SQL Server em sua VM. Em seguida, você poderá executar instruções T-SQL para habilitar a criptografia para seus bancos de dados e backups.

Há várias formas de criptografia das quais você pode tirar proveito:

* [Transparent Data Encryption (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)
* [Backups criptografados](https://msdn.microsoft.com/library/dn449489.aspx)
* [Criptografia de nível de coluna (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)

Os scripts Transact-SQL a seguir fornecem exemplos para cada uma dessas áreas.

> [!NOTE]
> Cada exemplo tem base em dois pré-requisitos: uma chave assimétrica de seu cofre de chaves chamado **CONTOSO_KEY** e uma credencial criada pelo recurso de Integração de AKV chamada **Azure_EKM_TDE_cred**.
> 
> 

### <a name="transparent-data-encryption-tde"></a>Transparent Data Encryption (TDE)
1. Crie um logon do SQL Server que será usado pelo Mecanismo de banco de dados para TDE e adicione a credencial a ele.
   
        USE master;
        -- Create a SQL Server login associated with the asymmetric key 
        -- for the Database engine to use when it loads a database 
        -- encrypted by TDE.
        CREATE LOGIN TDE_Login 
        FROM ASYMMETRIC KEY CONTOSO_KEY;
        GO
   
        -- Alter the TDE Login to add the credential for use by the 
        -- Database Engine to access the key vault
        ALTER LOGIN TDE_Login 
        ADD CREDENTIAL Azure_EKM_TDE_cred;
        GO
2. Crie a chave de criptografia do banco de dados que será usada para a TDE.
   
        USE ContosoDatabase;
        GO
   
        CREATE DATABASE ENCRYPTION KEY 
        WITH ALGORITHM = AES_128 
        ENCRYPTION BY SERVER ASYMMETRIC KEY CONTOSO_KEY;
        GO
   
        -- Alter the database to enable transparent data encryption.
        ALTER DATABASE ContosoDatabase 
        SET ENCRYPTION ON;
        GO

### <a name="encrypted-backups"></a>Backups criptografados
1. Crie um logon do SQL Server que será usado pelo Mecanismo de banco de dados para criptografia de backups e adicione a credencial a ele.
   
        USE master;
        -- Create a SQL Server login associated with the asymmetric key 
        -- for the Database engine to use when it is encrypting the backup.
        CREATE LOGIN Backup_Login 
        FROM ASYMMETRIC KEY CONTOSO_KEY;
        GO 
   
        -- Alter the Encrypted Backup Login to add the credential for use by 
        -- the Database Engine to access the key vault
        ALTER LOGIN Backup_Login 
        ADD CREDENTIAL Azure_EKM_Backup_cred ;
        GO
2. Faça o backup do banco de dados especificando a criptografia com a chave assimétrica armazenada no cofre de chave.
   
        USE master;
        BACKUP DATABASE [DATABASE_TO_BACKUP]
        TO DISK = N'[PATH TO BACKUP FILE]' 
        WITH FORMAT, INIT, SKIP, NOREWIND, NOUNLOAD, 
        ENCRYPTION(ALGORITHM = AES_256, SERVER ASYMMETRIC KEY = [CONTOSO_KEY]);
        GO

### <a name="column-level-encryption-cle"></a>Criptografia de nível de coluna (CLE)
Esse script cria uma chave simétrica protegida pela chave assimétrica no cofre de chave e usa a chave simétrica para criptografar dados no banco de dados.

    CREATE SYMMETRIC KEY DATA_ENCRYPTION_KEY
    WITH ALGORITHM=AES_256
    ENCRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;

    DECLARE @DATA VARBINARY(MAX);

    --Open the symmetric key for use in this session
    OPEN SYMMETRIC KEY DATA_ENCRYPTION_KEY 
    DECRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;

    --Encrypt syntax
    SELECT @DATA = ENCRYPTBYKEY(KEY_GUID('DATA_ENCRYPTION_KEY'), CONVERT(VARBINARY,'Plain text data to encrypt'));

    -- Decrypt syntax
    SELECT CONVERT(VARCHAR, DECRYPTBYKEY(@DATA));

    --Close the symmetric key
    CLOSE SYMMETRIC KEY DATA_ENCRYPTION_KEY;

## <a name="additional-resources"></a>Recursos adicionais
Para saber mais sobre como usar esses recursos de criptografia, consulte [Usando EKM com recursos de criptografia do SQL Server](https://msdn.microsoft.com/library/dn198405.aspx#UsesOfEKM).

Observe que as etapas neste artigo presumem que o SQL Server já está em execução em uma máquina virtual do Azure. Se não estiver, confira [Provisionar uma máquina virtual do SQL Server no Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md). Para obter orientação sobre como executar o SQL Server em VMs do Azure, consulte [Visão geral sobre SQL Server em máquinas virtuais do Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md).



<!--HONumber=Jan17_HO2-->


