---
title: "Problemas conhecidos e Guia de solução de problemas | Microsoft Docs"
description: "Lista de problemas conhecidos e um guia para ajudar a solucioná-los"
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 5c7c15eacdf43d3623000ed228adfaeb55803c8f
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/29/2017
---
# <a name="azure-machine-learning-workbench---known-issues-and-troubleshooting-guide"></a>Azure Machine Learning Workbench – problemas conhecidos e Guia de solução de problemas 
Este artigo ajuda a localizar e corrigir os erros ou falhas encontrados como parte do uso do aplicativo Azure Machine Learning Workbench. 

## <a name="find-the-workbench-build-number"></a>Localize o número de build do Workbench
Ao se comunicar com a equipe de suporte, é importante ter em mãos o número de build do aplicativo do Workbench. No Windows, você pode encontrar o número de build clicando no menu **Ajuda** e escolhendo **Sobre o Azure ML Workbench**. No macOS, clique no menu **Azure ML Workbench** e escolha **About Azure ML Workbench**.

## <a name="machine-learning-msdn-forum"></a>Fórum MSDN do Machine Learning
Temos um fórum do MSDN em que é possível postar perguntas. A equipe de produto monitora o fórum ativamente. A URL do fórum é [https://aka.ms/azureml-forum](https://aka.ms/azureml-forum). 

## <a name="gather-diagnostics-information"></a>Coletar informações de diagnóstico
Às vezes, pode ser útil fornecer informações de diagnóstico ao pedir ajuda. Este é o local em que ficam os arquivos de log:

### <a name="installer"></a>Instalador
Se houver problemas durante a instalação, os arquivos de log do instalador estão aqui:

```
# Windows:
%TEMP%\amlinstaller\logs\*

# macOS:
/tmp/amlinstaller/logs/*
```
É possível compactar o conteúdo desses diretórios e enviá-los para diagnóstico.

### <a name="app-update"></a>Atualização de aplicativo 
#### <a name="no-update-notification-on-windows-desktop"></a>Nenhuma notificação de atualização na área de trabalho do Windows 
Esse problema será resolvido em uma próxima atualização. Enquanto isso, a solução alternativa é evitar iniciar o aplicativo pelo atalho fixado na barra de tarefas. Em vez de abrir o aplicativo usando o menu Iniciar ou a barra de pesquisa da página Iniciar ou seu respectivo atalho na área de trabalho (se houver). 

#### <a name="no-update-notification-on-an-ubuntu-data-sciece-virtual-machine-dsvm"></a>Nenhuma notificação de atualização em uma Máquina Virtual de Ciência de Dados (DSVM) do Ubuntu
Execute as seguintes etapas para baixar o aplicativo mais recente:   
   - remova a pasta \Users\AppData\Local\amlworkbench
   - remover o script `c:\dsvm\tools\setup\InstallAMLFromLocal.ps1`
   - remover o atalho da área de trabalho que inicia o script acima
   - instale corretamente usando [https://aka.ms/azureml-wb-msi](https://aka.ms/azureml-wb-msi)

### <a name="workbench-desktop-app"></a>Aplicativo da área de trabalho do Workbench
Se estiver tendo problemas para entrar ou se a área de trabalho do Workbench falhar, os arquivos de log poderão ser encontrados aqui:
```
# Windows
%APPDATA%\AmlWorkbench

# macOS
~/Library/Application Support/AmlWorkbench
``` 
É possível compactar o conteúdo desses diretórios e enviá-los para diagnóstico.

### <a name="experiment-execution"></a>Execução de experimento
Se um determinado script falhar durante o envio por meio de um aplicativo da área de trabalho, tente reenviá-lo pela CLI usando o comando `az ml experiment submit`. Você receberá a mensagem de erro completa no formato JSON e, mais importante, ela contém um valor **ID da operação**. Envie o arquivo JSON incluindo a **ID da operação** para que possamos ajudar a diagnosticar. 

Se um determinado script for bem-sucedido no envio, mas falhar na execução, ele deverá imprimir o **Executar ID** para identificar essa execução específica. É possível empacotar os arquivos de log relevantes usando o seguinte comando:

```azurecli
# Create a ZIP file that contains all the diagnostics information
$ az ml experiment diagnostics -r <run_id> -t <target_name>
```

O comando `az ml experiment diagnostics` gera um arquivo `diagnostics.zip` na pasta raiz do projeto. Este pacote ZIP contém a pasta do projeto inteiro no estado e no momento em que ele foi executado, além de informações de log. Remova informações confidenciais que você não deseja incluir antes de enviar o arquivo de diagnóstico.

## <a name="send-us-a-frown-or-a-smile"></a>Envie-nos um rosto triste (ou um sorridente)

Quando você estiver trabalhando no Azure Machine Learning Workbench, também é possível nos enviar um rosto triste (ou um sorridente) clicando no ícone de smiley no canto inferior esquerdo do shell do aplicativo. Outra opção é incluir seu endereço de email (para podermos lhe responder) e/ou uma captura de tela do estado atual. 

## <a name="known-service-limits"></a>Limites de serviço conhecidos
- Tamanho máximo permitido da pasta do projeto: 25 MB.
    >[!NOTE]
    >Esse limite não se aplica às pastas `.git`, `docs` e `outputs`. Esses nomes de pasta diferenciam maiúsculas de minúsculas. Se você estiver trabalhando com arquivos grandes, consulte [Como manter alterações e lidar com arquivos grandes](how-to-read-write-files.md).

- Tempo máximo de execução de experimento permitido: sete dias

- Tamanho máximo do arquivo controlado na pasta `outputs` após uma execução: 512 MB
  - Isso significa que se seu script gerar um arquivo maior do que 512 MB na pasta de saída, ele não será coletado nela. Se você estiver trabalhando com arquivos grandes, consulte [Como manter alterações e lidar com arquivos grandes](how-to-read-write-files.md).

- Não há suporte para chaves SSH ao se conectar a um computador remoto ou um cluster do Spark via SSH. No momento, há suporte somente para o modo de nome de usuário/senha.

- Quando se usa o cluster HDInsight como destino de computação, ele deve usar o blob do Azure como armazenamento primário. O uso do Armazenamento do Azure Data Lake não tem suporte.

- Não há suporte para transformações de clustering de texto no Mac.

- Há suporte para a biblioteca RevoScalePy apenas no Windows e no Linux (em contêineres do Docker). Ela não tem suporte no macOS.

## <a name="cant-update-workbench"></a>Não é possível atualizar o Workbench
Quando uma nova atualização estiver disponível, a home page do aplicativo Workbench exibirá uma mensagem informando sobre a nova atualização. Você verá uma notificação de atualização que aparece no canto inferior esquerdo do aplicativo, no ícone de sino. Clique na notificação e siga o assistente de instalação para instalar a atualização. 

![atualizar imagem](./media/known-issues-and-troubleshooting-guide/update.png)

Se você não vir a notificação, tente reiniciar o aplicativo. Se você ainda não vir a notificação de atualização após a reinicialização, pode haver algumas causas.

### <a name="you-are-launching-workbench-from-a-pinned-shortcut-on-the-task-bar"></a>Você está iniciando o Workbench de um atalho fixado na barra de tarefas
Talvez você já tenha instalado a atualização. Mas o atalho fixado ainda está apontando para os bits antigos no disco. Você pode verificar isso navegando até a pasta `%localappdata%/AmlWorkbench` e ver se você tem a versão mais recente instalada e examinar a propriedade do atalho fixado para ver para onde ele está apontando. Se verificado, basta remover o atalho antigo, iniciar o Workbench do menu Iniciar e, opcionalmente, criar um novo atalho fixado na barra de tarefas.

### <a name="you-installed-workbench-using-the-install-azure-ml-workbench-link-on-a-windows-dsvm"></a>Você instalou o Workbench usando o link "instalar o Azure ML Workbench" em um DSVM do Windows
Infelizmente, não há nenhuma correção fácil para esse. Você deve executar as etapas a seguir para remover os bits instalados e baixar o instalador mais recente para fazer uma nova instalação do Workbench: 
   - remover a pasta `C:\Users\<Username>\AppData\Local\amlworkbench`
   - remover o script `C:\dsvm\tools\setup\InstallAMLFromLocal.ps1`
   - remover o atalho da área de trabalho que inicia o script acima
   - baixe o instalador https://aka.ms/azureml-wb-msi e reinstale.

## <a name="cant-delete-experimentation-account"></a>Não é possível excluir a Conta de Experimentação
Você pode usar o CLI para excluir uma conta de experimentação, mas primeiro você deve excluir os espaços de trabalho filho e os projetos filho dentro desses espaços de trabalho filho. Caso contrário, você verá um erro.

```azure-cli
# delete a project
$ az ml project delete -g <resource group name> -a <experimentation account name> -w <worksapce name> -n <project name>

# delete a workspace 
$ az ml workspace delete -g <resource group name> -a <experimentation account name> -n <worksapce name>

# delete an experimentation account
$ az ml account experimentation delete -g <resource group name> -n <experimentation account name>
```

Você também pode excluir os projetos e espaços de trabalho de dentro do aplicativo do Workbench.

## <a name="cant-open-file-if-project-is-in-onedrive"></a>Não será possível abrir o arquivo se o projeto estiver no OneDrive
Se você tiver o Windows 10 Fall Creators Update, e se o seu projeto for criado em uma pasta local mapeada para o OneDrive, talvez descubra que não pode abrir qualquer arquivo no Workbench. Isso ocorre devido a um bug introduzido pela versão Fall Creators Update, que faz com que o código node.js falhe em uma pasta do OneDrive. O bug será corrigido em breve pelo Windows update, mas até lá, não crie projetos em uma pasta do OneDrive.

## <a name="file-name-too-long-on-windows"></a>Nome de arquivo muito longo no Windows
Se você usar o Workbench no Windows, poderá encontrar o limite máximo padrão do tamanho de nome do arquivo de 260 caracteres, que poderia aparecer como um erro "o sistema não pode localizar o caminho especificado". Você pode modificar uma configuração da chave do Registro para permitir nomes de caminho de arquivos muito mais longos. Leia [este artigo](https://msdn.microsoft.com/en-us/library/windows/desktop/aa365247%28v=vs.85%29.aspx?#maxpath) para ver mais detalhes sobre como definir a chave do Registro _MAX_PATH_.

## <a name="docker-error-read-connection-refused"></a>Erro de docker "leitura: conexão recusada"
Ao executar em um contêiner de Docker local, às vezes o seguinte erro poderá ocorrer: 
```
Get https://registry-1.docker.io/v2/: 
dial tcp: 
lookup registry-1.docker.io on [::1]:53: read udp [::1]:49385->[::1]:53: 
read: connection refused
```

É possível corrigi-lo alterando o servidor DNS do Docker de `automatic` para um valor fixo de `8.8.8.8`.

## <a name="remove-vm-execution-error-no-tty-present"></a>Remover o erro de execução de VM "sem tty presente"
Ao executar em um contêiner do Docker em um computador remoto do Linux, a seguinte mensagem de erro poderá ocorrer:
```
sudo: no tty present and no askpass program specified.
``` 
Isso poderá acontecer se você usar o Portal do Azure para alterar a senha raiz de uma VM do Linux Ubuntu. 

O Azure Machine Learning Workbench requer acesso de sudoers sem senha para execução em hosts remotos. A maneira mais simples de fazer isso é usar o _visudo_ para editar o arquivo a seguir (é possível criar o arquivo se ele não existir):

```
$ sudo visudo -f /etc/sudoers
```

>[!IMPORTANT]
>É importante editar o arquivo com o _visudo_ e não com outro comando. O _visudo_ verifica automaticamente a sintaxe de todos os arquivos de configuração do sudo e a falha na produção de sudoers sintaticamente corretos pode travar o sudo.

Insira a seguinte linha ao final do arquivo:

```
username ALL=(ALL) NOPASSWD:ALL
```

Em que _username_ é o nome que o Azure Machine Learning Workbench usará para fazer logon no seu host remoto.

A linha deve ser colocada após #includedir "/etc/sudoers.d", caso contrário, ele pode ser substituído por outra regra.

Se você tiver uma configuração de sudo mais complicada, poderá consultar a documentação do sudo para Ubuntu disponível em: https://help.ubuntu.com/community/Sudoers

O erro acima também poderá ocorrer se você não estiver usando uma VM do Linux baseada no Ubuntu no Azure como um destino de execução. Oferecemos suporte somente a VMs Linux baseadas em Ubuntu para execução remota. 

## <a name="vm-disk-is-full"></a>O disco da VM está cheio
Por padrão, ao criar uma nova VM do Linux no Azure, você obtém um disco de 30 GB para o sistema operacional. O mecanismo do docker usa, por padrão, o mesmo disco para a extração de imagens e contêineres em execução. Isso pode preencher o disco do sistema operacional e mostrar um erro dizendo "O disco da VM está cheio".

Uma correção rápida é remover todas as imagens do Docker que não mais usadas. O comando do Docker a seguir faz exatamente isso. (Obviamente, você precisa do SSH na VM para executar o comando Docker de um shell Bash.)

```
$ docker system prune -a
```

Também é possível adicionar um disco de dados e configurar o mecanismo do Docker para usar o disco de dados para armazenar imagens. Veja aqui [como adicionar um disco de dados](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/add-disk). Em seguida, é possível [alterar o local em que o Docker armazena imagens](https://forums.docker.com/t/how-do-i-change-the-docker-image-installation-directory/1169).

Outra opção é expandir o disco do sistema operacional, sem precisar mexer na configuração do mecanismo do Docker. Veja aqui [como expandir o disco do sistema operacional](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/add-disk).

## <a name="sharing-c-drive-on-windows"></a>Compartilhamento da unidade C no Windows
Se você estiver executando em um contêiner local do Docker no Windows, configurar `sharedVolumes` para `true` no arquivo `docker.compute` em `aml_config` pode melhorar o desempenho de execução. No entanto, isso exige o compartilhamento da unidade C no _Docker para Ferramenta Windows_. Se não for possível compartilhar a unidade C, tente o seguinte:

* Verifique o compartilhamento na unidade C usando o explorador de arquivos
* Abra as configurações do adaptador de rede e desinstale/reinstale "Compartilhamento de arquivo e impressora para redes Microsoft" para vEthernet
* Abra as configurações do docker e compartilhe a unidade C nas configurações do docker
* Alterações à senha do Windows afetam o compartilhamento. Abra o explorador de arquivos, compartilhe novamente a unidade C e digite a nova senha.
* Você também pode encontrar o problema de firewall ao tentar compartilhar sua unidade C com o Docker. Esta [postagem de Stack Overflow](http://stackoverflow.com/questions/42203488/settings-to-windows-firewall-to-allow-docker-for-windows-to-share-drive/43904051) pode ser útil.
* Ao compartilhar a unidade C usando as credenciais de domínio, o compartilhamento pode deixar de funcionar em redes em que o controlador de domínio não esteja acessível (por exemplo, redes domésticas, WiFi pública etc.). Para obter mais informações, confira [esta postagem](https://blogs.msdn.microsoft.com/stevelasker/2016/06/14/configuring-docker-for-windows-volumes/).

Também é possível evitar o problema de compartilhamento, comprometendo um pouco o desempenho, configurando `sharedVolumne` para `false` no arquivo `docker.compute`.

## <a name="some-useful-docker-commands"></a>Alguns comandos úteis do Docker

Aqui estão alguns comandos úteis do Docker:

```sh
# display all running containers
$ docker ps

# dislplay all containers (running or stopped)
$ docke ps -a

# display all images
$ docker images

# show Docker logs of a container
$ docker logs <container_id>

# create a new container and launch into a bash shell
$ docker run <image_id> /bin/bash

# launch into a bash shell on a running container
$ docker exec -it <container_id> /bin/bash

# stop an running container
$ docker stop <container_id>

# delete a container
$ docker rm <container_id>

# delete an image
$ docker rmi <image_id>

# delete all unussed Docker images 
$ docker system prune -a

```
