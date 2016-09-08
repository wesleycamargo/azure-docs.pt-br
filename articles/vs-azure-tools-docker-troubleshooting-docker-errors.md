<properties
   pageTitle="Solucionando problemas de erros do cliente Docker no Windows usando o Visual Studio | Microsoft Azure"
   description="Solucione os problemas encontrados na utilização do Visual Studio para criar e implantar aplicativos Web no Docker ou no Windows usando o Visual Studio."
   services="azure-container-service"
   documentationCenter="na"
   authors="allclark"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/18/2016"
   ms.author="allclark" />

# Solucionar problemas de desenvolvimento do Docker do Visual Studio

Ao trabalhar com o Visual Studio Tools para Docker Preview, você pode encontrar alguns problemas devido à natureza de versão de visualização. Veja a seguir alguns problemas comuns e suas resoluções.

##Falha ao configurar Program.cs a fim de fornecer suporte ao Docker

Ao adicionar suporte ao docker, o `.UseUrls(Environment.GetEnvironmentVariable("ASPNETCORE_URLS"))` deve ser adicionado ao WebHostBuilder(). Se a função `Main()` ou uma nova classe WebHostBuilder não for encontrada em `.UseUrls()`, um aviso será exibido. `Program.cs` é necessário para permitir que Kestrel ouça o tráfego de entrada, além de localhost quando executado dentro de um contêiner do docker. Após a conclusão, o código terá a seguinte aparência:

```
public class Program
{
    public static void Main(string[] args)
    {
        var host = new WebHostBuilder()
            .UseUrls(Environment.GetEnvironmentVariable("ASPNETCORE_URLS") ?? String.Empty)
            .UseKestrel()
            .UseContentRoot(Directory.GetCurrentDirectory() ?? "")
            .UseIISIntegration()
            .UseStartup<Startup>()
            .Build();

        host.Run();
    }
}
```

UseUrls() configurou o WebHost para escutar o tráfego da URL de entrada. As [Ferramentas do Docker para Visual Studio](http://aka.ms/DockerToolsForVS) configurarão uma variável de ambiente no modo dockerfile.debug/release da seguinte maneira:

```
# Configure the listening port to 80
ENV ASPNETCORE_SERVER.URLS http://*:80
```

## O mapeamento de volume não está funcionando
Para habilitar os recursos de Editar e Atualizar, o mapeamento do volume está configurado para compartilhar o código-fonte de seu projeto na pasta .app dentro do contêiner. Quando os arquivos forem alterados em seu computador host, o diretório /app dos contêineres usará o mesmo diretório. Em docker-compose.debug.yml, a configuração a seguir permite o mapeamento do volume

```
volumes:
    - ..:/app
```

Para testar se o mapeamento do volume está funcionando, tente o seguinte comando:

**No Windows**

```
docker run -it -v /c/Users/Public:/wormhole busybox
/ # ls
```

Você deverá ver uma listagem de diretórios da pasta Usuários/Público. Se nenhum arquivo for exibido, e sua pasta /c/Usuários/Público não estiver vazia, o mapeamento de volume não estará configurado corretamente.

```
bin       etc       proc      sys       usr       wormhole
dev       home      root      tmp       var
```

Mude para o diretório wormhole para ver o conteúdo do diretório `/c/Users/Public`:

```
/ # cd wormhole/
/wormhole # ls
AccountPictures  Downloads        Music            Videos
Desktop          Host             NuGet.Config     a.txt
Documents        Libraries        Pictures         desktop.ini
/wormhole #
```

> [AZURE.NOTE] Ao trabalhar com as VMs do Linux, o sistema de arquivos do contêiner diferencia maiúsculas de minúsculas.

Se você não conseguir ver o conteúdo, tente o seguinte:

**Docker para a versão beta do Windows**
- Verifique se o aplicativo da área de trabalho Docker para Windows está em execução procurando o ícone `moby` na bandeja do sistema e verificando se ele está branco e funcional.
- Verifique se o mapeamento do volume está configurado clicando com o botão direito no ícone `moby` na bandeja do sistema, selecionando as configurações e clicando em **Gerenciar unidades compartilhadas...**

**Caixa de ferramentas do Docker com VirtualBox**

Por padrão, o VirtualBox compartilha `C:\Users` como `c:/Users`. Se possível, mova o projeto abaixo desse diretório. Caso contrário, adicione manualmente ao VirtualBox as [Pastas compartilhadas](https://www.virtualbox.org/manual/ch04.html#sharedfolders).
	
##Criação: falha ao criar a imagem, erro ao verificar a conexão TLS: o host não está em execução

- Verifique se o host do Docker padrão está em execução. Consulte o artigo [Configurar o cliente Docker](./vs-azure-tools-docker-setup.md).

##Usar o Microsoft Edge como o navegador padrão

Se você estiver usando o navegador Microsoft Edge, o site pode não abrir se o Edge considerar que o endereço IP não é seguro. Para corrigir esse problema, execute as seguintes etapas:

1. Vá para **Opções da Internet**.
    - No Windows 10, você pode digitar `Internet Options` na caixa Executar do Windows.
    - No Internet Explorer, você pode ir para o menu **Configurações** e selecionar **Opções da Internet**.
1. Selecione **Opções da Internet** quando forem exibidas.
1. Selecione a guia **Segurança**.
1. Selecione a zona **Intranet Local**.
1. Selecione **Sites**.
1. Adicione o IP da máquina virtual (nesse caso, o Host do Docker) à lista.
1. Atualize a página no Edge e verá o site em funcionamento.
1. Para obter mais informações sobre esse problema, visite a postagem do blog de Scott Hanselman, [O Microsoft Edge não pode ver nem abrir sites da Web locais hospedados no VirtualBox](http://www.hanselman.com/blog/FixedMicrosoftEdgeCantSeeOrOpenVirtualBoxhostedLocalWebSites.aspx).

##Solução de problemas da versão 0.15 ou anterior


###A execução do aplicativo faz com que o PowerShell abra, exiba um erro e feche. A página do navegador não abre.

A falha ao abrir o navegador pode ser um erro durante `docker-compose-up`. Para exibir o erro, execute as seguintes etapas:

1. Abra o arquivo `Properties\launchSettings.json`
1. Localize a entrada do Docker.
1. Localize a linha que começa assim:

    ```
    "commandLineArgs": "-ExecutionPolicy RemoteSigned …”
    ```
	
1. Adicione o parâmetro `-noexit` para que a linha agora se pareça com o seguinte. Esse código manterá o PowerShell aberto para que você possa exibir o erro.

    ```
	"commandLineArgs": "-noexit -ExecutionPolicy RemoteSigned …”
    ```

<!---HONumber=AcomDC_0824_2016-->