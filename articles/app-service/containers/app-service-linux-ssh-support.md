---
title: Suporte de SSH para o Serviço de Aplicativo do Azure no Linux | Microsoft Docs
description: Saiba como usar SSH com o Serviço de Aplicativo do Azure no Linux.
keywords: serviço de aplicativo do azure, aplicativo web, linux, oss
services: app-service
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: wesmc
ms.openlocfilehash: 631933647e27428349fc1efeb17f62f4614f7f64
ms.sourcegitcommit: 42405ab963df3101ee2a9b26e54240ffa689f140
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47423299"
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>Suporte de SSH para o Serviço de Aplicativo do Azure no Linux

[Secure Shell (SSH)](https://wikipedia.org/wiki/Secure_Shell) normalmente é usado para executar comandos administrativos remotamente a partir de um terminal de linha de comando. O Serviço de Aplicativo no Linux fornece suporte de SSH no contêiner de aplicativo com cada uma das imagens internas do Docker usadas para a Pilha em Tempo de Execução de novos aplicativos Web. 

![Pilhas de tempo de execução](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

Para imagens personalizadas de Docker, ao configurar o servidor SSH em sua imagem personalizada.

Você também pode se conectar ao contêiner diretamente do seu computador de desenvolvimento local usando o SSH e SFTP.

## <a name="open-ssh-session-in-browser"></a>Abra a sessão SSH aberta no navegador

Para fazer uma conexão de cliente SSH com o contêiner, seu aplicativo deve estar em execução.

Cole a seguinte URL no seu navegador e substitua \<app_name > pelo nome do aplicativo:

```
https://<app_name>.scm.azurewebsites.net/webssh/host
```

Se ainda não estiver autenticado, você precisará se autenticar usando sua assinatura do Azure para se conectar. Uma vez autenticado, consulte um shell no navegador, onde você pode executar comandos dentro de seu contêiner.

![Conexão SSH](./media/app-service-linux-ssh-support/app-service-linux-ssh-connection.png)

## <a name="use-ssh-support-with-custom-docker-images"></a>Use o suporte de SSH com imagens personalizadas do Docker

Para que uma imagem personalizada do Docker dê suporte à comunicação SSH entre o contêiner e o cliente no portal do Azure, execute as seguintes etapas para a imagem do Docker.

Essas etapas são mostradas no repositório do Serviço de Aplicativo do Azure como [um exemplo](https://github.com/Azure-App-Service/node/blob/master/6.9.3/).

1. Inclua a instalação `openssh-server` na [instrução `RUN`](https://docs.docker.com/engine/reference/builder/#run) no Dockerfile para sua imagem e defina a senha da conta raiz como `"Docker!"`.

    > [!NOTE]
    > Essa configuração não permite conexões externas com o contêiner. O SSH só pode ser acessado por meio do site do Kudu/SCM, que é autenticado usando as credenciais de publicação.

    ```docker
    # ------------------------
    # SSH Server support
    # ------------------------
    RUN apt-get update \
        && apt-get install -y --no-install-recommends openssh-server \
        && echo "root:Docker!" | chpasswd
    ```

1. Adicione uma [instrução `COPY`](https://docs.docker.com/engine/reference/builder/#copy) ao Dockerfile para copiar um arquivo [sshd_config](http://man.openbsd.org/sshd_config) para o diretório */etc/ssh/*. Seu arquivo de configuração deve ser baseado no arquivo sshd_config no repositório Azure-App-Service do GitHub [aqui](https://github.com/Azure-App-Service/node/blob/master/8.2.1/sshd_config).

    > [!NOTE]
    > O arquivo *sshd_config* deve incluir o seguinte ou a conexão falhará: 
    > * `Ciphers` deve incluir pelo menos um dos itens a seguir: `aes128-cbc,3des-cbc,aes256-cbc`.
    > * `MACs` deve incluir pelo menos um dos itens a seguir: `hmac-sha1,hmac-sha1-96`.

    ```docker
    COPY sshd_config /etc/ssh/
    ```

1. Inclua a porta 2222 na [instrução `EXPOSE`](https://docs.docker.com/engine/reference/builder/#expose) para o Dockerfile. Embora a senha raiz seja conhecida, a porta 2222 não pode ser acessada da Internet. Ela é uma porta interna que pode ser acessada somente por contêineres dentro da rede ponte de uma rede virtual privada.

    ```docker
    EXPOSE 2222 80
    ```

1. Certifique-se de iniciar o serviço SSH usando um script de Shell (consulte o exemplo em [init_container.sh](https://github.com/Azure-App-Service/node/blob/master/6.9.3/startup/init_container.sh)).

    ```bash
    #!/bin/bash
    service ssh start
    ```

O Dockerfile usa a [instrução `ENTRYPOINT`](https://docs.docker.com/engine/reference/builder/#entrypoint) para executar o script.

    ```docker
    COPY init_container.sh /opt/startup
    ...
    RUN chmod 755 /opt/startup/init_container.sh
    ...
    ENTRYPOINT ["/opt/startup/init_container.sh"]
    ```

## <a name="open-ssh-session-from-remote-shell"></a>Abrir sessão SSH de shell remoto

> [!NOTE]
> Esse recurso está atualmente em versão prévia.
>

Usando o túnel TCP, você pode criar uma conexão de rede entre o computador de desenvolvimento e o Aplicativo Web para contêineres em uma conexão autenticada do WebSocket. Isso permite que você abra uma sessão SSH com o contêiner em execução no serviço de Serviço de Aplicativo do Azure do cliente de sua escolha.

Para começar, você precisa instalar a [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). Para ver como funciona sem instalar a CLI do Azure, abra [Azure Cloud Shell](../../cloud-shell/overview.md). 

Adicione a extensão de Serviço de Aplicativo do Azure mais recente, executando [Adicionar extensão az](/cli/azure/extension?view=azure-cli-latest#az-extension-add):

```azurecli-interactive
az extension add --name webapp
```

Se você já tiver executado `az extension add` antes, execute [atualização de extensão az](/cli/azure/extension?view=azure-cli-latest#az-extension-update) em vez disso:

```azurecli-interactive
az extension update --name webapp
```

Abra uma conexão remota para seu aplicativo usando o comando [criar az webapp remoto-conexão](/cli/azure/ext/webapp/webapp/remote-connection?view=azure-cli-latest#ext-webapp-az-webapp-remote-connection-create). Especifique _\<subscription\_id>_, _\<group\_name>_ e \_<app\_name>_ para o aplicativo.

```azurecli-interactive
az webapp remote-connection create --subscription <subscription_id> --resource-group <group_name> -n <app_name> &
```

> [!TIP]
> `&` no final do comando é apenas para conveniência, se você estiver usando o Azure Cloud Shell. Executa o processo em segundo plano para que você possa executar o próximo comando no mesmo shell.

A saída do comando fornece as informações que você precisa para abrir uma sessão SSH.

```
Port 21382 is open
SSH is available { username: root, password: Docker! }
Start your favorite client and connect to port 21382
```

Abra uma sessão SSH com o contêiner com o cliente de sua escolha, usando a porta local. O exemplo a seguir usa o comando padrão [ssh](https://ss64.com/bash/ssh.html):

```azurecli-interactive
ssh root@127.0.0.1 -p <port>
```

Ao ser solicitado, digite `yes` para continuar a se conectar. Você receberá uma solicitação de senha. Use `Docker!`, que foi mostrado anteriormente.

```
Warning: Permanently added '[127.0.0.1]:21382' (ECDSA) to the list of known hosts.
root@127.0.0.1's password:
```

Ao autenticar, você deverá ver a tela de boas-vindas da sessão.

```
  _____
  /  _  \ __________ _________   ____
 /  /_\  \___   /  |  \_  __ \_/ __ \
/    |    \/    /|  |  /|  | \/\  ___/
\____|__  /_____ \____/ |__|    \___  >
        \/      \/                  \/
A P P   S E R V I C E   O N   L I N U X

0e690efa93e2:~#
```

Agora você está conectado a seu conector. 

Tente executar o comando novamente [top](https://ss64.com/bash/top.html). Você deve ser capaz de ver o processo do aplicativo na lista de processos. No exemplo abaixo, é aquele com `PID 263`.

```
Mem: 1578756K used, 127032K free, 8744K shrd, 201592K buff, 341348K cached
CPU:   3% usr   3% sys   0% nic  92% idle   0% io   0% irq   0% sirq
Load average: 0.07 0.04 0.08 4/765 45738
  PID  PPID USER     STAT   VSZ %VSZ CPU %CPU COMMAND
    1     0 root     S     1528   0%   0   0% /sbin/init
  235     1 root     S     632m  38%   0   0% PM2 v2.10.3: God Daemon (/root/.pm2)
  263   235 root     S     630m  38%   0   0% node /home/site/wwwroot/app.js
  482   291 root     S     7368   0%   0   0% sshd: root@pts/0
45513   291 root     S     7356   0%   0   0% sshd: root@pts/1
  291     1 root     S     7324   0%   0   0% /usr/sbin/sshd
  490   482 root     S     1540   0%   0   0% -ash
45539 45513 root     S     1540   0%   0   0% -ash
45678 45539 root     R     1536   0%   0   0% top
45733     1 root     Z        0   0%   0   0% [init]
45734     1 root     Z        0   0%   0   0% [init]
45735     1 root     Z        0   0%   0   0% [init]
45736     1 root     Z        0   0%   0   0% [init]
45737     1 root     Z        0   0%   0   0% [init]
45738     1 root     Z        0   0%   0   0% [init]
```

## <a name="next-steps"></a>Próximas etapas

Você pode postar perguntas e problemas no [fórum do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

Para obter mais informações sobre o aplicativo Web para contêineres, consulte:

* [Introduzindo a depuração remota de aplicativos Node.js no Serviço de Aplicativo do Azure de aplicativo do Azure do código VS](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0)
* [Como usar uma imagem personalizada do Docker para o Aplicativo Web para Contêineres](quickstart-docker-go.md)
* [Usando o .NET Core no Serviço de Aplicativo do Azure no Linux](quickstart-dotnetcore.md)
* [Usando o Ruby no Serviço de Aplicativo do Azure no Linux](quickstart-ruby.md)
* [Perguntas frequentes sobre o Aplicativo Web para Contêineres do Serviço de Aplicativo do Azure](app-service-linux-faq.md)
