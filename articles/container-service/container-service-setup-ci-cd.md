---
title: "CI/CD com Serviço de Contêiner do Azure e DC/OS | Microsoft Docs"
description: "Como automatizar completamente a criação e a implantação de um aplicativo de vários contêineres Docker em um cluster do Serviço de Contêiner do Azure executando DC/SO."
services: container-service
documentationcenter: 
author: spboyer
manager: wpickett
editor: 
tags: acs, azure-container-service
keywords: "Docker, Contêineres, Microsserviços, Mesos, Azure"
ms.assetid: b16b767a-2eaa-418a-becc-8c032713a1f2
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: johnsta
translationtype: Human Translation
ms.sourcegitcommit: 831f585a9591338c2f404f7ec031d40937731eab
ms.openlocfilehash: dcf4c0b67bc7a6596070cdf44644a6c451e3afc1


---

# <a name="continuous-integration-and-deployment-of-multi-container-docker-applications-to-azure-container-service"></a>Implantação e integração contínuas de aplicativos de vários contêineres Docker ao Serviço de Contêiner do Azure 
Nesse tutorial, abordaremos como automatizar completamente a criação e a implantação de um aplicativo de vários contêineres Docker em um cluster do Serviço de Contêiner do Azure executando DC/SO. Enquanto os benefícios da CI/CD (implantação e integração contínuas) são conhecidos, há novas considerações ao integrar contêineres no fluxo de trabalho. Usando os novos comandos da CLI e do registro de Contêiner do Azure, criamos um fluxo de ponta a ponta, que você pode personalizar.

## <a name="get-started"></a>Introdução
Você pode executar este passo a passo no OS X, Windows ou Linux.
- É necessária uma assinatura do Azure. Se não tiver uma, você poderá se [inscrever em uma conta](https://azure.microsoft.com/).
- Instale as [ferramentas de linha de comando do Azure](https://github.com/Azure/azure-cli#microsoft-azure-cli-20---preview).

## <a name="what-well-create"></a>O que vamos criar
Vamos abordar alguns aspectos fundamentais do aplicativo e seu fluxo de implantação que estamos configurando:
* **O aplicativo é composto por vários serviços**. Ativos de docker, Dockerfile e docker-compose.yml, para definir os serviços em nosso aplicativo, cada um executando em contêineres separados. Eles permitem que partes do aplicativo sejam dimensionadas de maneira independente e cada serviço pode ser escrito em uma estrutura e linguagem de programação diferentes. O código do aplicativo pode ser hospedado em um ou mais repositórios de origem de Git (as ferramentas dão suporte, atualmente, a GitHub ou Visual Studio Team Services).

* O aplicativo é executado em um **cluster ACS configurado com o DC/SO**. O orquestrador de contêiner pode gerenciar a integridade de nosso cluster e garantir que nosso número necessário de instâncias de contêiner continue em execução. 

* O processo de **criação e implantação de imagens de contêiner é completamente automatizado sem tempo de inatividade**. Queremos que os desenvolvedores da equipe enviem por push o git para uma ramificação, o que automaticamente dispara um processo de integração. Isto é, criar e marcar imagens de contêiner, executar testes em cada contêiner e enviar por push essas imagens para um registro privado do Docker. Então, novas imagens são automaticamente implantadas em um ambiente de pré-produção compartilhado em um cluster ACS para outros testes.

* **Promover uma versão de um ambiente para o próximo**, por exemplo, de Desenvolvimento -> Teste -> Preparo -> Produção. Sempre que promovermos um ambiente de downstream, não precisaremos reconstruir nossas imagens de contêiner para garantir que possamos implantar as mesmas imagens testadas em um ambiente anterior. Esse processo é o conceito de *serviços imutáveis* e reduz a probabilidade de erros não detectados em produção.

* Para utilizar com mais eficiência os recursos de computação em nosso cluster ACS, utilizamos o mesmo cluster para executar as etapas de implantação e criação totalmente transportadas em contêineres das tarefas de criação. O cluster também hospeda nossos vários ambientes de desenvolvimento/teste/produção.


## <a name="create-an-azure-container-service-cluster-configured-with-dcos"></a>Criar um cluster do Serviço de Contêiner do Azure configurado com DC/SO

>[!IMPORTANT]
> Para criar um cluster seguro você passa o arquivo de chave pública SSH ao chamar `az acs create`. O Azure CLI 2.0 pode gerar as chaves para você e passá-las ao mesmo tempo usando a opção `--generate-ssh-keys`, ou você pode passar o caminho para suas chaves usando a opção `--ssh-key-value` (o local padrão no Linux é `~/.ssh/id_rsa.pub` e no Windows `%HOMEPATH%\.ssh\id_rsa.pub`, mas isso pode ser alterado).
<!---Loc Comment: What do you mean by "you pass your SSH public key file to pass"? Thank you.--->
> Para criar os arquivos de chave privada e pública SSH no Linux, consulte [Criar chaves SSH no Linux e Mac](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fcontainer-services%2ftoc.json). 
> Para criar os arquivos de chave privada e pública SSH no Windows, consulte [Criar chaves SSH no Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md?toc=%2fazure%2fcontainer-services%2ftoc.json). 

1. Primeiro, digite o comando [logon az](/cli/azure/#login) em uma janela de terminal para fazer logon em sua assinatura do Azure com o Azure CLI: 

    `az login`

1. Criar um grupo de recursos no qual podemos colocar nosso cluster usando [criar grupo de az](/cli/azure/group#create):
    
    `az group create --name myacs-rg --location westus`

    Você talvez queira especificar a [região do datacenter do Azure](https://azure.microsoft.com/regions) mais próxima de você. 

1. Crie um cluster ACS com as configurações padrão usando [criar az acs](/cli/azure/acs#create) e passando o caminho para seu arquivo de chave SSH pública: 

    ```azurecli
    az acs create \
    --resource-group myacs-rg 
    --name myacs \
    --dns-prefix myacs \
    --ssh-key-value ~/.ssh/id_rsa.pub
    ```
    
Esta etapa leva vários minutos, fique à vontade para prosseguir lendo.  O comando `acs create` retorna informações sobre o cluster recém-criado (ou você pode listar os clusters do ACS em sua assinatura com `az acs list`). Para obter mais opções de configuração do ACS, [leia mais sobre as informações de como criar e configurar um cluster ACS](container-service-deployment.md).

## <a name="set-up-sample-code"></a>Configuração do código de exemplo
Enquanto o cluster está sendo criado, podemos configurar o código de exemplo que é implantado no ACS.

1. [Separe](https://help.github.com/articles/fork-a-repo/) o repositório GitHub de exemplo para que você tenha sua própria cópia: [https://github.com/azure-samples/container-service-dotnet-continuous-integration-multi-container.git](https://github.com/azure-samples/container-service-dotnet-continuous-integration-multi-container.git). O aplicativo é essencialmente uma versão de vários contêineres de "hello world".
1. Depois de ter criado uma separação em sua própria conta do GitHub, clone localmente o repositório em seu computador:

    ```bash
    git clone  https://github.com/your-github-account/container-service-dotnet-continuous-integration-multi-container.git
    cd container-service-dotnet-continuous-integration-multi-container
    ```
    
Vamos examinar o código com mais detalhes:
* `/service-a` é um aplicativo Web baseado em Angular.js com um back-end Node.js.
* `/service-b` é um serviço .NET Core e é chamado pelo `service-a` via REST.
* `service-a` e `service-b` contêm um `Dockerfile` em cada um de seus diretórios que respectivamente descrevem imagens de contêiner baseadas em Node.js e .NET Core. 
* `docker-compose.yml` declara o conjunto de serviços que são criados e implantados.
* Além de `service-a` e `service-b`, um terceiro serviço chamado `cache` executa um cache Redis que `service-a` pode usar. `cache` difere dos dois primeiros serviços em que não temos código em nosso repositório de origem. Em vez disso, buscamos uma imagem `redis:alpine` previamente criada do Docker Hub e implantamos no ACS.
* `/service-a/server.js` contém código em que `service-a` chama `service-b` e `cache`. Observe que o código `service-a` faz referência a `service-b` e `cache` por como eles são nomeados em `docker-compose.yml`. Se executarmos esses serviços em nosso computador local por meio de `docker-compose`, o Docker garante que os serviços estejam todos em rede apropriadamente para encontrar uns aos outros por nome. Executar os serviços em um ambiente de cluster com rede com balanceamento de carga geralmente é muito mais complexo do que a execução local. A boa notícia é que os comandos do Azure CLI configuram um fluxo de CI/CD que garante que o código de descoberta do serviço direto continue a executar do modo em que se encontra no ACS. 

    ![Visão geral do aplicativo de exemplo de vários contêineres](media/container-service-setup-ci-cd/multi-container-sample-app-overview.PNG)

## <a name="set-up-continuous-integration-and-deployment"></a>Configuração da integração e implantação contínuas
1. Certifique-se de que o cluster ACS está pronto: execute a [lista az acs](/cli/azure/acs#list) e confirme se nosso cluster ACS está listado. (Observação: o ACS deve estar executando o DC/SO 1.8 ou posterior.)

1. [Crie um token de acesso pessoal do GitHub](https://help.github.com/articles/creating-an-access-token-for-command-line-use/), concedendo pelo menos o escopo `repo`.  Não se esqueça de copiar o token em sua área de transferência, pois o utilizaremos no próximo comando (configurará um [webhook](https://help.github.com/articles/about-webhooks/) em nosso repositório GitHub). 

1. Defina o diretório atual para a raiz do seu repositório de origem clonado e crie um pipeline de compilação e versão, usando o _&lt;GitHubPersonalAccessToken&gt; que você acabou de criar:
    
    `cd container-service-dotnet-continuous-integration-multi-container`

    ```azurecli
    az container release create \
    --target-name myacs \
    --target-resource-group myacs-rg \
    --remote-access-token <GitHubPersonalAccessToken>
    ```

    Em que `--target-name` é o nome do cluster do ACS e `--target-resource-group` é o nome do grupo de recursos do cluster do ACS.

Na primeira execução, esse comando pode demorar um minuto ou mais para ser concluído. Depois de concluído, informações importantes são retornadas sobre o pipeline de build e versão criado:
* `sourceRepo`: um [webhook](https://help.github.com/articles/about-webhooks/) está configurado para o repositório de origem para que o pipeline de build e versão seja automaticamente disparado sempre que o código-fonte for enviado para ele.  
* `vstsProject`: [O VSTS](https://www.visualstudio.com/team-services/) (Visual Studio Team Services) é configurado para *direcionar* o fluxo de trabalho (as tarefas de compilação e implantação reais são executadas dentro de contêineres no ACS). Se quiser usar um projeto e conta específicos do VSTS, você pode definir usando os parâmetros `--vsts-account-name` e `--vsts-project-name`.
* `buildDefinition`: define as tarefas executadas para cada build. Imagens de contêiner são geradas para cada serviço definido em docker-compose.yml e, em seguida, enviadas para um registro de contêiner do Docker. 
* `containerRegistry`: o Registro de Contêiner do Azure é um serviço gerenciado que executa o registro do contêiner Docker. Um novo Registro de Contêiner do Azure é criado com um nome padrão ou, como alternativa, você pode especificar um nome de Registro de Contêiner do Azure por meio do parâmetro `--registry-name`.
* `releaseDefinition`: define as tarefas que são executadas para cada implantação. As imagens de contêiner para os serviços definidos no docker-compose.yml são extraídas do registro de contêiner e implantadas no cluster do ACS. Por padrão, são criados três ambientes: *Desenvolvimento*, *Teste* e *Produção*. A definição de versão é configurada por padrão para implantar automaticamente para *Desenvolvimento* sempre que uma build for concluída com êxito. Uma versão pode ser promovida para *Teste* ou *Produção* manualmente sem exigir uma recompilação. O fluxo padrão pode ser personalizado no VSTS. 
* `containerService`: o cluster de destino do ACS (deve estar executando DC/SO 1.8).


O trecho a seguir está um comando de exemplo que você digitaria se já tivesse um Registro de Contêiner do Azure existente chamado `myregistry`. Crie e compile definições de versão com uma conta do VSTS em `myvstsaccount.visualstudio.com` e um projeto existente do VSTS `myvstsproject`:
        
```azurecli
az container release create \
--target-name myacs \
--target-resource-group myacs-rg \
--registry-name myregistry \
--vsts-account-name myvstsaccount \
--vsts-project-name myvstsproject \
--remote-access-token <GitHubPersonalAccessToken>
```

## <a name="view-deployment-pipeline-progress"></a>Exibir andamento do pipeline de implantação
Depois que o pipeline é criado, uma primeira implantação e compilação é iniciada automaticamente. Compilações subsequentes são acionadas sempre que o código for enviado para o repositório de origem. Você pode verificar o andamento de um build e/ou versão abrindo o navegador para a definição de build ou URLs de definição de versão.

Você sempre pode encontrar a URL de definição de versão associada a um cluster ACS executando este comando:

```azurecli
az container release list \
--target-name myacs \
--target-resource-group myacs-rg
``` 

![Build do VSTS](media/container-service-setup-ci-cd/vsts-build.PNG)

*Captura de tela do VSTS mostrando resultados de CI do nosso aplicativo de vários contêineres*

![Versão do VSTS](media/container-service-setup-ci-cd/vsts-release.PNG)

*A versão composta pelo Docker do VSTS com vários ambientes*

## <a name="view-the-application"></a>Exibir o aplicativo
Neste ponto, nosso aplicativo é implantado em nosso ambiente de desenvolvimento compartilhado e não será exposto publicamente. Enquanto isso, use o painel do DC/SO para exibir e gerenciar nossos serviços e [criar um túnel SSH para os pontos de extremidade relacionados ao DC/SO](https://azure.microsoft.com/documentation/articles/container-service-connect/) ou executar um comando de conveniência fornecido pelo Azure CLI.

> [!IMPORTANT]
> Em uma primeira implantação, confirme se a versão do VSTS foi implantada com êxito antes de continuar.

> [!NOTE]
> Windows: você precisa configurar [Pageant](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) para concluir esta seção.
> 
>* Inicie *PuttyGen* e carregue a chave privada do SSH usada para criar o cluster do ACS (% homepath%\id_rsa).
>* Salve a chave privada do SSH como `id_rsa.ppk` na mesma pasta.
>* Inicie o *Pageant* – Ele começará a executar e exibirá um ícone na bandeja do sistema na parte inferior direita.
>* Clique com o botão direito do mouse no ícone de bandeja do sistema e selecione *Adicionar Chave*.
>* Adicione o arquivo `id_rsa.ppk`.
> 
> 

1. Abra o painel do DC/OS do cluster do ACS usando o comando de conveniência do Azure CLI:
    
    `az acs dcos browse -g myacs-rg -n myacs`

    * `-g` é o nome do grupo de recursos do cluster de destino do ACS
    * `-n` é o nome do cluster de destino do ACS.
    * Será solicitada sua senha da conta local, já que este comando requer privilégios de administrador. O comando cria um túnel SSH para um ponto de extremidade do DC/SO, abre o navegador padrão para esse ponto de extremidade e configura temporariamente o proxy Web do navegador. 

    > [!TIP]
    > Se precisa pesquisar o nome do cluster do ACS, você pode listar todos os clusters do ACS em sua assinatura executando `az acs list`. 

1. No painel do DC/SO, clique em **Serviços** no menu de navegação à esquerda ([http://localhost/#/services](http://localhost/#/services)). Os serviços implantados por meio de nosso pipeline são agrupados em uma pasta raiz chamada *Desenvolvimento* (nomeada de acordo com o ambiente na definição da versão do VSTS). 

![Interface do usuário do Marathon](media/container-service-setup-ci-cd/marathon-ui.png)

Você pode executar muitas coisas úteis no painel do DC/SO

* acompanhamento do status da implantação para cada serviço
* exibição dos requisitos de CPU e Memória
* exibição de logs
* dimensionamento do número de instâncias para cada serviço

**Para exibir o aplicativo Web para um serviço-a**: inicie na pasta raiz *desenvolvimento* e faça uma busca detalhada na hierarquia de pasta até alcançar `service-a`. Esta exibição lista as tarefas em execução (ou instâncias de contêiner) para `service-a`.

![serviço a](media/container-service-setup-ci-cd/service-a.png)

Clique em uma tarefa para abrir a exibição, clique em um dos seus pontos de extremidade disponíveis.

![tarefa do serviço a](media/container-service-setup-ci-cd/service-a-task.PNG)

Nossas chamadas de aplicativo Web simples `service-a`, que chamam `service-b` e retornam uma mensagem do hello world. Um contador é incrementado no Redis sempre que uma solicitação é feita.

![aplicativo Web do serviço a](media/container-service-setup-ci-cd/service-a-web-app.PNG)

### <a name="optional-reaching-a-service-from-the-command-line"></a>(Opcional) Alcance de um serviço na linha de comando
Se você quiser alcançar um serviço por meio de rotação da linha de comando:

1. Execute `az acs dcos browse --verbose -g myacs-rg -n myacs` e observe a linha que diz "Proxy em execução no <endereço-ip>:<port>" depois de inserir sua senha.
1. Em uma nova janela de terminal, digite:

    `export http_proxy=http://<web-proxy-service-ip>:<portnumber>`

    Por exemplo: `export http_proxy=http://127.0.0.1:55405`

1. Agora você pode fazer rotação em relação a seu ponto de extremidade de serviço, `curl http://service-url`, em que `service-url` é o endereço que você vê ao navegar para o ponto de extremidade do serviço da interface do usuário Marathon. Para remover a variável http_proxy da sua linha de comando, digite `unset http_proxy`.
 

## <a name="scale-services"></a>Serviços de dimensionamento
Enquanto estamos no painel do DC/SO, vamos dimensionar nossos serviços.
1. Navegue até o aplicativo na subpasta *Desenvolvimento*.
1. Passe o mouse sobre `service-b`, clique no ícone de engrenagem e selecione **Dimensionar**.

    ![Menu Ação](media/container-service-setup-ci-cd/marathon-ui-action-menu.PNG)

1. Aumente o número para 3 e clique em **Serviço de Dimensionamento**.

    ![Serviços de dimensionamento](media/container-service-setup-ci-cd/marathon-ui-scale-service.png)

1. Navegue de volta para o aplicativo Web em execução e clique repetidamente no botão *Dizer Novamente*. Observe que as invocações `service-b` começam a fazer rodízio entre uma coleção de nomes de host, enquanto a instância única do `service-a` continua a relatar o mesmo host.   

## <a name="promote-a-release-to-downstream-environments-without-rebuilding-container-images"></a>Promover uma versão para ambientes de downstream sem recriar imagens de contêiner
Nosso pipeline de versão do VSTS release configura até três ambientes por padrão: *Desenvolvimento*, *Teste* e *Produção*. Até agora implantamos no *Desenvolvimento*. Vamos examinar como é possível promover uma liberação para o próximo ambiente de downstream, *Teste*, sem recriar nossas imagens de contêiner. Esse fluxo de trabalho garante que estejamos implantando as mesmas imagens testadas no ambiente anterior e é o conceito de *serviços imutáveis* e reduz a probabilidade de erros não detectados irem para produção.

1. Na interface do usuário da Web do VSTS, navegue até **Versões**

    ![Menu de versões do VSTS](media/container-service-setup-ci-cd/vsts-releases-menu.PNG)

1. Abra a versão mais recente.

1. Na barra de menus da definição de versão, clique em **Implantar**, em seguida, selecione **Teste** como o próximo ambiente que deseja implantar para iniciar uma nova implantação, reutilizando as mesmas imagens que foram implantadas anteriormente em *Desenvolvimento*. Clique em **Registros** se deseja acompanhar a implantação em mais detalhes.

    ![O VSTS promove versão](media/container-service-setup-ci-cd/vsts-promote-release.PNG)

Assim que a implantação em *Teste* for bem-sucedida, haverá uma nova pasta raiz na interface do usuário Marathon, chamada *teste*, que contém os serviços em execução para esse ambiente. 

![Subpastas para cada ambiente no DC/OS](media/container-service-setup-ci-cd/marathon-ui-dev-test-environments.png)

## <a name="trigger-a-new-build-and-deployment"></a>Disparar uma nova compilação e implantação
Vamos simular o que aconteceria se um desenvolvedor em nossa equipe enviasse uma alteração de código para o repositório de origem.

1. No editor de códigos, abra `service-a/public/index.html`. 
1. Modifique esta linha de código:

    `<h2>Server Says</h2>`

    para algo como:

    `<h2>Server Says Hello</h2>`

1. Salve o arquivo, confirme e envie por push a alteração de código para seu repositório de origem.

    ```bash
    git commit -am 'updated title'
    git push
    ```

A confirmação automaticamente inicia um novo build e uma nova versão a ser implantada em *Desenvolvimento*. Serviços em ambientes de downstream (*Teste* ou *Produção*) permanecem inalterados até decidirmos promover uma versão específica para o ambiente.

Se abrir a definição de build no VSTS, você verá algo assim: 

![Novo build disparado por push do git](media/container-service-setup-ci-cd/new-build.png)



## <a name="expose-public-endpoint-for-production"></a>Expor um ponto de extremidade público para produção

1. Adicione o seguinte código yaml a um novo arquivo chamado `docker-compose.env.production.yml` na pasta raiz do seu repositório de origem. Isso adiciona um rótulo que faz com que um ponto de extremidade público seja exposto para `service-a`. 
    
    ```yaml
    version: "2"
    services:
      service-a:
        labels:
          com.microsoft.acs.dcos.marathon.vhost: "<FQDN, or custom domain>"
    ```

    * Para o valor de rótulo, você pode especificar a URL do FQDN (nome de domínio totalmente qualificado) do seu agente de ACS ou domínio personalizado (por exemplo, app.contoso.com). Para localizar o FQDN do agente do ACS, execute o comando `az acs list` e verifique a propriedade para `agentPoolProfiles.fqdn`. Por exemplo: `myacsagents.westus.cloudapp.azure.com`.
    * Seguindo a convenção de nome de arquivo docker-compose.env.*environment-name*.yml, essas configurações afetam apenas o ambiente nomeado (nesse caso, o ambiente chamado *Produção*). Inspecione a definição de versão no VSTS. Cada tarefa de implantação do ambiente é configurada para ler um arquivo composto pelo Docker nomeado de acordo com essa convenção.

1. Confirme e envie por push o arquivo para o repositório de origem mestre para iniciar outra build.

    ```bash
    git add .
    git commit -am "expose public port for service-a"
    git push
    ```

1. Aguarde até que a atualização seja compilada e implantada em *Desenvolvimento*, então, promova-a em *Teste* e promova-a em *Produção*. (Para os fins deste tutorial, você pode implantar diretamente em *Produção*, mas é bom ter o costume de implantar somente no próximo ambiente de downstream.)

1. (Opcional) **Se você tiver especificado um domínio personalizado** para vhost (por exemplo, app.contoso.com), adicione um registro DNS nas configurações do seu provedor de domínio. Faça logon na interface do usuário administrativa do seu provedor de domínio e adicione um registro DNS da seguinte maneira:

    * Tipo: CNAME
    * Host: seu domínio personalizado, por exemplo, app.contoso.com
    * Resposta: FQDN do agente de ACS, por exemplo, myacsagents.westus.cloudapp.azure.com
    * TTL (opcional): às vezes, seu provedor de domínio oferece a capacidade de editar o TTL. Um valor mais baixo faz com que uma atualização de registro de DNS seja propagada mais rapidamente.   

1. Depois que o lançamento for implantado em *Produção*, essa versão será acessível a qualquer pessoa. Abra o navegador para a URL especificada para o rótulo `com.microsoft.acs.dcos.marathon.vhost`. (Observação: versões para ambientes de pré-produção continuam a ser privadas).

## <a name="summary"></a>Resumo
Parabéns! Você aprendeu como criar um cluster do ACS com DC/SO e a configurar um pipeline de compilação e implantação totalmente automatizado e em contêineres para um aplicativo de vários contêineres.

Algumas das próximas etapas para explorar:
* **Dimensionar Agentes VSTS.** Se precisar de mais taxa de transferência para a execução de tarefas de build e versão, você pode aumentar o número de instâncias do agente VSTS. Navegue até **Serviços** no Painel do DC/SO, abra a pasta vsts-agents e tente dimensionar o número de instâncias do agente VSTS.
* **Integre os testes de unidade.** Esse repositório do GitHub mostra como fazer testes de unidade e testes de integração executarem em contêineres e como inclui-los nas tarefas de build: [https://github.com/mindaro/sample-app](https://github.com/mindaro/sample-app). 
    * Dica: examine esses arquivos no repositório: `service-a/unit-tests.js`, `service-a/service-tests.js`, `docker-compose.ci.unit-tests.yml` e `docker-compose.ci.service-tests.yml`.

## <a name="clean-up"></a>Limpar
Para limitar os encargos de computação relacionados a este tutorial, execute o seguinte comando e anote os recursos de pipeline de implantação relacionados a um cluster do ACS:

```azurecli 
az container release list --resource-name myacs --resource-group myacs-rg
```

Excluir o cluster do ACS:
1. Entre no [Portal do Azure](https://portal.azure.com)
1. Procure o grupo de recursos que contém seu clusters do ACS.
1. Abra a interface do usuário da folha do grupo de recursos e clique em **Excluir** na barra de comandos da folha.

Excluir o Registro de Contêiner do Azure: No portal do Azure, pesquise o Registro de Contêiner do Azure e exclua-o. 

A [conta do Visual Studio Team Services oferece Nível de Acesso Básico gratuito para os cinco primeiros usuários](https://azure.microsoft.com/en-us/pricing/details/visual-studio-team-services/), mas você pode excluir as definições de build e versão.

Excluir a Definição de Build do VSTS:
        
1. Abra a URL de Definição de Compilação em seu navegador, clique no link **Definições de build** (ao lado do nome da definição de build que você está exibindo no momento).
2. Clique no menu de ação ao lado da definição de build que você deseja excluir e selecione **Excluir Definição**

`![Exclua a Definição de Build do VSTS](media/container-service-setup-ci-cd/vsts-delete-build-def.png) 

Excluir a Definição de Versão do VSTS:

1. Abra a URL de Definição de Versão no seu navegador.
2. Na lista Definições de Versão no lado esquerdo, clique na lista suspensa ao lado da definição de versão que você deseja excluir e selecione **Excluir**.

`![Exclua a Definição de Versão do VSTS](media/container-service-setup-ci-cd/vsts-delete-release-def.png)



<!--HONumber=Jan17_HO4-->


