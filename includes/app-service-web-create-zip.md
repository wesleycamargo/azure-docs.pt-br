## <a name="create-a-project-zip-file"></a>Criar um projeto do arquivo ZIP

Crie um arquivo ZIP com tudo do seu projeto. O comando a seguir usa a ferramenta padrão em seu terminal:

```
# Bash
zip -r myAppFiles.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath myAppFiles.zip
``` 

Posteriormente, você carrega o arquivo ZIP para o Azure e o implanta no Serviço de Aplicativo.