<properties
   pageTitle="Solucionando problemas de erros do cliente Docker no Windows usando o Visual Studio | Microsoft Azure"
   description="Solucione os problemas encontrados na utilização do Visual Studio para criar e implantar aplicativos Web no Docker ou no Windows usando o Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="12/18/2015"
   ms.author="tarcher" />

# Solucionando problemas do Docker

Depois de definir todas as configurações do contêiner de Docker do seu aplicativo, você deve verificar se as configurações e os caminhos estão corretos. O Visual Studio fornece um botão Validar na caixa de diálogo Publicar para ajudar você a fazer isso.

Este tópico ajuda a diagnosticar e corrigir ou contornar os problemas mais comuns que você encontrará ao hospedar um aplicativo do Visual Studio no Docker. Mais problemas serão adicionados a este tópico à medida que forem encontrados.

## Você recebe uma mensagem de falha quando tenta validar a conexão com o host do Docker na caixa de diálogo Publicar Web

Veja aqui algumas possíveis soluções para esse problema.

- Na guia **Conexão** da caixa de diálogo **Publicar**, verifique se a **URL do Servidor** está correta e se à direita `:<port_number>` da **URL do Servidor** está a porta de escuta do daemon do Docker.

- Na guia **Conexão** da caixa de diálogo **Publicar**, expanda a seção **Opções Avançadas do Docker** e garanta que as Opções de **Autenticação** corretas estejam sendo especificadas.
  - Se o daemon do Docker no servidor estiver configurado para usar a segurança TLS, a interface de linha de comando do Docker no Windows (docker.exe) buscará a chave do cliente (key.pem) e o certificado (cert.pem) por padrão na pasta `<%userprofile%>\.docker`. Se esses itens não estiverem presentes, será necessário gerá-los usando OpenSSL. Para saber mais sobre como configurar o Docker para TLS, consulte [Protegendo o soquete daemon do Docker com HTTPS](https://docs.docker.com/articles/https/).

	Uma forma de garantir que o Docker esteja sendo autenticado corretamente do cliente do Windows para o servidor Linux é copiar o conteúdo da caixa de texto Visualizar em uma nova janela de comando e alterar `<command>` para "info", desta maneira:

    ```
    // This example assumes the Docker daemon is configured to use the default port
    // of 2376 to listen for connections.docker.

    --tls -H tcp://contoso.cloudapp.net:2376 info
    ```

    Como uma alternativa a copiar os arquivos de chave e certificado do cliente na pasta .docker, você pode alterar as Opções de **Autenticação** adicionando os seguintes parâmetros:

    ```
    --tls --tlscert=C:\mycert\cert.pem --tlskey=C:\mycert\key.pem
    ```
- Verifique se o daemon do Docker no computador host do Docker está na versão 1.6 ou posterior.

## Erro de tempo limite ao usar os próprios certificados sem certificado de cliente na pasta do Docker

Se você optar por usar seus próprios certificados ao criar o host do Docker no Visual Studio (ou seja, se desmarcar a caixa de seleção **Gerar certificados do Docker automaticamente** na caixa de diálogo **Criar máquina virtual no Microsoft Azure**), você precisará copiar os arquivos de chave e o certificado de cliente (cert.pem e key.pem) na pasta do Docker (`<%userprofile%>\.docker`). Caso contrário, ao publicar seu projeto, você obterá um erro de tempo limite em uma hora e haverá falha na operação de publicação.

## É necessário o PowerShell 3.0 para publicar em contêineres do Docker

Se seu sistema operacional for o Windows 7 ou Windows Server 2008, você precisará instalar o PowerShell 3.0 para publicar em contêineres do Docker. O PowerShell 3.0 está incluído no [Windows Management Framework 3.0](https://www.microsoft.com/pt-BR/download/details.aspx?id=34595). Você precisará reinicializar o sistema depois de instalá-lo.

Como uma solução alternativa, é possível atualizar para o Windows 8.1 ou Windows 10, que já tem o PowerShell 3.0.

## A janela do PowerShell não fecha automaticamente

Às vezes, depois de criar uma VM, a janela do PowerShell não fecha automaticamente. Fechar essa janela também fechará o Visual Studio. Como a janela não afeta os recursos de ferramentas do Visual Studio ou do Docker, deixe-a aberta até terminar seu trabalho.

## Perguntas frequentes

P: Como criar uma nova máquina Linux habilitada para Docker no Azure usando as ferramentas do Visual Studio?

R: Consulte [Hospedando aplicativos Web no Docker](vs-azure-tools-docker-hosting-web-apps-in-docker.md) para obter informações sobre como fazer isso.

P: Quais modelos de projeto do Visual Studio têm suporte de publicação em um contêiner do Docker no Linux?

R: Atualmente o Visual Studio oferece suporte ao Aplicativo de Console (Pacote) no C# e aos modelos de Web ASP.NET 5 Preview no C#, incluindo:

- Vazio

- API Web

- Aplicativo Web

P: Como posso publicar meu projeto de console ou Web ASP.NET 5 no Docker usando MSBUILD na linha de comando?

R: Use o seguinte comando MSBuild:

    `msbuild <projectname.xproj> /p:deployOnBuild=true;publishProfile=<profilename>`

P: Como posso publicar meu projeto de console ou Web ASP.NET 5 no Docker usando o PowerShell na linha de comando?

R: Use o seguinte comando do PowerShell:

```
.\contoso-Docker-publish.ps1 -packOutput $env:USERPROFILE\AppData\Local\Temp\PublishTemp -pubxmlFile .\contoso-Docker.pubxml
```

P: Tenho meu próprio servidor Linux com o Docker instalado, como especificar isso na caixa de diálogo **Publicação Web**?

R: Consulte a seção **Fornecer um Host do Docker Personalizado** no tópico [Hospedando aplicativos Web no Docker](vs-azure-tools-docker-hosting-web-apps-in-docker.md).

P: Estou usando meu próprio servidor Linux com o Docker instalado. Como gero chaves e certificados para configurar a autenticação usando TLS?

R: Uma maneira é usar o OpenSSL no servidor para gerar os certificados e chaves necessários para a Autoridade de Certificação, o servidor e o cliente. Você pode usar um software de terceiros para estabelecer uma conexão SSH/SFTP e, em seguida, copiar os certificados no computador de desenvolvimento local do Windows. Por padrão, o Docker (CLI) tentará usar certificados localizados na pasta `<userprofile>\.docker`.

Outra opção é baixar o OpenSSL para Windows e gerar os certificados e as chaves necessários e, em seguida, carregar a Autoridade de Certificação, os certificados de servidor e as chaves no computador Linux. Para saber mais sobre como estabelecer uma conexão segura com o Docker, consulte [Protegendo o soquete daemon do Docker com HTTPS](https://docs.docker.com/articles/https/).

<!---HONumber=AcomDC_1223_2015-->