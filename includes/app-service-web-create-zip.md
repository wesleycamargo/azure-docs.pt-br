## <a name="create-a-project-zip-file"></a>Criar um projeto de arquivo zip

Verifique se você ainda está no diretório raiz do projeto de exemplo. Criar um arquivo zip de tudo em seu projeto. O comando a seguir usa a ferramenta padrão em seu terminal:

```
# Bash
zip -r myAppFiles.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath myAppFiles.zip
``` 

Posteriormente, você carrega o arquivo ZIP para o Azure e o implanta no Serviço de Aplicativo.