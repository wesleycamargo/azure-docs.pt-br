Você pode criar filas de armazenamento do Azure usando o **Gerenciador de Servidores** do Visual Studio.

![Blobs do Gerenciador de Servidores][Image1]

1. No menu **Exibir**, escolha**Gerenciador de Servidores**.
2. No Gerenciador de Servidores, expanda o nó **Azure** de sua assinatura, o nó **Armazenamento** e o nó da conta de armazenamento que você especificou no serviço conectado do Armazenamento do Azure.
3. Selecione o nó **Filas** e escolha **Criar Fila** no menu de contexto.
4. Insira um nome para o contêiner e escolha **OK**.   

Por padrão, o novo contêiner é privado e você deve especificar sua chave de acesso do armazenamento para baixar blobs desse contêiner. Se você quiser tornar públicos os arquivos no contêiner, selecione o contêiner no **Gerenciador de Servidores** e pressione `F4` para exibir a janela **Propriedades**. Defina o **Acesso de Leitura Público** como **Blob**. Qualquer pessoa na Internet pode ver blobs em um contêiner público, mas você só pode modificar ou excluí-los se tiver a chave de acesso apropriada.


[Image1]: ./media/vs-create-blob-container-in-server-explorer/vs-storage-create-blob-containers-in-Server-Explorer.png

<!---HONumber=July15_HO5-->