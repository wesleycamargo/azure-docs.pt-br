1. No **Gerenciador de Soluções** do Visual Studio, clique com o botão direito do mouse no projeto e selecione **Adicionar > Docker Support (Suporte do Docker)** no menu de contexto.

    ![Menu de contexto Adicionar Suporte ao Docker](media/vs-azure-tools-docker-add-docker-support/docker-support-context-menu.png)

1. Adicionar suporte ao Docker a um projeto Web ASP.NET 5 resulta na adição de vários arquivos relacionados ao Docker ao projeto, incluindo arquivos de Redação do Docker, scripts de implantação do Windows PowerShell e arquivos de propriedades do Docker.

    ![Arquivos do Docker adicionados ao projeto](media/vs-azure-tools-docker-add-docker-support/docker-files-added.png)
    
> [AZURE.NOTE] Se estiver usando o [Docker para Windows Beta](https://beta.docker.com), abra Properties\\Docker.props, remova o valor padrão e reinicie o Visual Studio para que o valor tenha efeito.
> 
> ```
> <DockerMachineName Condition="'$(DockerMachineName)'=="></DockerMachineName>
> ```

<!---HONumber=AcomDC_0622_2016-->