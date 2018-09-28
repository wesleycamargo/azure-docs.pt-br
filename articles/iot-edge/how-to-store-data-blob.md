---
title: Armazenamento de Blobs do Azure em dispositivos do Azure IoT Edge | Microsoft Docs
description: Implante um módulo do Armazenamento de Blobs do Azure no dispositivo do IoT Edge para armazenar dados na borda.
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 09/20/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b9e48eba4b46f024b056fe53b3b3df24feec802e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46995662"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>Armazenar dados na borda com o Armazenamento de Blobs do Azure no IoT Edge (versão prévia)

O Armazenamento de Blobs do Azure no IoT Edge fornece uma solução de armazenamento de blob de blocos na borda. Um módulo do armazenamento de blobs no dispositivo do IoT Edge comporta-se como um serviço de blobs do Azure, mas os blobs de blocos são armazenados localmente no dispositivo do IoT Edge. É possível acessar os blobs usando os mesmos métodos do SDK do armazenamento do Azure ou as chamadas à API do blob de blocos que você já está acostumado. 

Este artigo fornece instruções para implantar um Armazenamento de Blobs do Azure no contêiner do IoT Edge que executa um serviço blob no dispositivo do IoT Edge. 

>[!NOTE]
>O Armazenamento de Blobs do Azure no IoT Edge está em [visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo do Azure IoT Edge:

* Você pode usar seu computador de desenvolvimento ou uma máquina virtual como um dispositivo do Edge seguindo as etapas no início rápido para os [dispositivos Linux](quickstart-linux.md) ou [Windows](quickstart.md).
* O Armazenamento de Blobs do Azure no módulo do IoT Edge dá suporte às seguintes configurações de dispositivo:

   | Sistema operacional | Arquitetura |
   | ---------------- | ------------ |
   | Ubuntu Server 16.04 | AMD64 |
   | Ubuntu Server 18.04 | AMD64 |
   | Windows 10 IoT Core (atualização de outubro) | AMD64 |
   | Windows 10 IoT Enterprise (atualização de outubro) | AMD64 |
   | Windows Server 2019 | AMD64 |
   | Raspbian Stretch | ARM32 |

Recursos de nuvem:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na camada padrão no Azure. 


## <a name="deploy-blob-storage-to-your-device"></a>Implantar um armazenamento de blobs no dispositivo

O Armazenamento de Blobs do Azure no IoT Edge fornece três imagens de contêiner padrão, sendo duas para contêineres do Linux (arquiteturas AMD64 e ARM32) e uma para contêineres do Windows (AMD64). Ao utilizar uma dessas imagens do módulo para implantar o armazenamento de blobs no dispositivo do IoT Edge, você fornece três informações para configurar a instância do módulo no dispositivo:

* Um **nome da conta** e **chave de conta**. Para permanecer consistente com o Armazenamento do Microsoft Azure, os módulos de armazenamento de blobs usam nomes da conta e chaves de conta para gerenciar o acesso. Os nomes da conta devem ter de três a vinte quatro caracteres, com letras minúsculas e números. Chaves de conta devem ser codificadas em base64 e ter tamanho de 64 bytes. É possível gerar uma chave com ferramentas como [GeneratePlus](https://generate.plus/en/base64).
* Uma **opção de armazenamento local**. O módulo de armazenamento de blobs armazena blobs localmente no dispositivo do IoT Edge, portanto, os blobs persistem se o módulo for interrompido ou reiniciado. Declare um [volume](https://docs.docker.com/storage/volumes
) existente ou caminho de pasta local onde os blobs devem ser armazenados no dispositivo. 

Existes várias maneiras de implantar módulos em um dispositivo do IoT Edge, sendo que todas elas funcionam para o Armazenamento de Blobs do Azure nos módulos do IoT Edge. Os dois métodos mais simples são utilizar o portal do Azure ou os modelos do Visual Studio Code. 

### <a name="azure-portal"></a>Portal do Azure

Para implantar do armazenamento de blobs por meio do portal do Azure, siga as etapas em [Implantar módulos do Azure IoT Edge pelo portal do Azure](how-to-deploy-modules-portal.md). Antes de prosseguir com a implantação do módulo, copie o URI da imagem e prepare as opções de criação de contêiner com base no sistema operacional do contêiner. Use estes valores na seção **Configurar um manifesto de implantação** no artigo de implantação. 

Forneça o URI da imagem para o módulo de armazenamento de blobs: **mcr.microsoft.com/azure-blob-storage**. 
   
Use o modelo JSON a seguir para o campo **Opções de Criação de Contêiner**. Configure o JSON com o nome da conta de armazenamento, a chave de conta de armazenamento e a associação do diretório de armazenamento.  
   
   ```json
   {
       "Env":[
           "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
           "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
       ],
       "HostConfig":[
           "Binds":[
               "<storage directory bind>"
           ],
           "PortBindings":{
               "11002/tcp":[{"HostPort":"11002"}]
           }
       ]
   }
   ```   
   
No JSON das opções de criação, atualize `\<your storage account name\>` com qualquer nome. Atualize `\<your storage account key\>` com uma chave base64 de 64 bytes. É possível gerar uma chave com ferramentas como [GeneratePlus](https://generate.plus/en/base64) que permite selecionar o tamanho em bytes. Você utilizará essas credenciais para acessar o armazenamento de blobs a partir de outros módulos.

No JSON das opções de criação, atualize `<storage directory bind>` dependendo do sistema operacional do seu contêiner. Forneça o nome de um [volume](https://docs.docker.com/storage/volumes/) ou o caminho absoluto para um diretório no dispositivo do IoT Edge no qual você quer que o módulo do blob armazene os dados.  

   * Contêineres do Linux: **\<storage path>:/blobroot**. Por exemplo, /srv/containerdata:/blobroot. Ou my-volume:/blobroot. 
   * Contêineres do Windows: **\<storage path>:C:/BlobRoot**. Por exemplo, C:/ContainerData:C:/BlobRoot. Ou my-volume:C:/blobroot. 


Não é necessário fornecer credenciais do registro para acessar o Armazenamento de Blobs do Azure no IoT Edge, sendo que você não precisa declarar quaisquer rotas para a implantação. 

### <a name="visual-studio-code-templates"></a>Modelos do Visual Studio Code

O Azure IoT Edge disponibiliza modelos no Visual Studio Code para ajudar você a desenvolver soluções de borda. Essas etapas requerem que você tenha o [Visual Studio Code](https://code.visualstudio.com/) instalado no computador de desenvolvimento, além de estar configurado com a [extensão do Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

Execute as etapas a seguir para criar uma nova solução do IoT Edge com o módulo de armazenamento de blobs e, em seguida, configure o manifesto de implantação. 

1. Selecione **Exibir** > **Paleta de Comandos**. 

2. Na paleta de comandos, insira e execute o comando **Azure IoT Edge: New IoT Edge Solution**. 

3. Siga os prompts para criar uma nova solução: 

   1. **Selecionar Pasta** - Navegue até a pasta na qual deseja criar a nova solução.  
   
   2. **Fornecer um nome da solução** - Forneça um nome para a solução ou aceite o padrão.
   
   3. **Selecionar modelo do módulo** - Escolha **Módulo Existente (Inserir a URL de imagem completa)**.
   
   4. **Fornecer um nome do módulo** - Insira um nome reconhecido para o módulo, como **azureBlobStorage**.
   
   5. **Fornecer imagem do Docker para o módulo** - Forneça o URI: **mcr.microsoft.com/azure-blob-storage**

O VS Code usa as informações fornecidas, cria uma solução do IoT Edge e, em seguida, a carrega em uma nova janela. 

O modelo da solução cria um modelo do manifesto de implantação que inclui a imagem do módulo do armazenamento de blobs, mas é necessário configurar as opções de criação do módulo. 

1. Abra **deployment.template.json** no espaço de trabalho da nova solução e encontre a seção **módulos**. 

2. Exclua o módulo **tempSensor**, pois ele é desnecessário para esta implantação. 

3. Copie e cole o código a seguir no campo **createOptions** do módulo do armazenamento de blobs: 

   ```json
   {\"Env\": [\"LOCAL_STORAGE_ACCOUNT_NAME=$STORAGE_ACCOUNT_NAME\",\" LOCAL_STORAGE_ACCOUNT_KEY=$STORAGE_ACCOUNT_KEY\"],\"HostConfig\": {\"Binds\": [\"<storage directory bind>\"],\"PortBindings\": {\"11002/tcp\": [{\"HostPort\":\"11002\"}]}}}
   ```

   ![Atualizar as opções de criação do módulo](./media/how-to-store-data-blob/create-options.png)

4. No JSON das opções de criação, atualize `<storage directory bind>` dependendo do sistema operacional do seu contêiner. Forneça o nome de um [volume](https://docs.docker.com/storage/volumes/) ou o caminho absoluto para um diretório no dispositivo do IoT Edge no qual você quer que o módulo do blob armazene os dados.  

   * Contêineres do Linux: **\<storage path>:/blobroot**. Por exemplo, /srv/containerdata:/blobroot. Ou my-volume:/blobroot.
   * Contêineres do Windows: **\<storage path>:C:/BlobRoot**. Por exemplo, C:/ContainerData:C:/BlobRoot. Ou my-volume:C:/blobroot.

5. Salve **deployment.template.json**.

6. Abra **.env** no espaço de trabalho da solução. 

7. Você não precisa inserir nenhum valor do registro de contêiner para a imagem do armazenamento de blobs porque ela está disponível publicamente. Em vez disso, adicione novas variáveis de ambiente: 

   ```env
   STORAGE_ACCOUNT_NAME=
   STORAGE_ACCOUNT_KEY=
   ```

8. Forneça qualquer nome para o nome da conta de armazenamento e depois forneça uma chave base64 de 64 bytes para a chave de chave de conta de armazenamento. É possível gerar uma chave com ferramentas como [GeneratePlus](https://generate.plus/en/base64). Você utilizará essas credenciais para acessar o armazenamento de blobs a partir de outros módulos. 

9. Salve **.env**. 

10. Clique com o botão direito do mouse em **deployment.template.json** e selecione **Gerar manifesto de implantação do IoT Edge**. 

O Visual Studio Code coleta as informações fornecidas no deployment.template.json e .env e as usa para criar um novo arquivo de manifesto de implantação. O manifesto de implantação é criado em uma nova pasta **config** no espaço de trabalho da solução. Ao obter esse arquivo, você pode seguir as etapas em [Implantar módulos do Azure IoT Edge a partir do Visual Studio Code](how-to-deploy-modules-vscode.md) ou [Implantar módulos do Azure IoT Edge com a CLI 2.0 do Azure](how-to-deploy-modules-cli.md).

## <a name="connect-to-your-blob-storage-module"></a>Conectar ao módulo do armazenamento de blobs

É possível usar o nome da conta e a chave de conta que você configurou para o módulo para acessar o armazenamento de blobs no seu dispositivo do IoT Edge. 

Especifique o dispositivo do IoT Edge como ponto de extremidade do blob para quaisquer solicitações de armazenamento que você faz para esse dispositivo. É possível [Criar uma cadeia de conexão para um ponto de extremidade explícito](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) usando as informações do dispositivo do IoT Edge e do nome da conta que você configurou. 

O ponto de extremidade do blob para o Armazenamento de Blobs do Azure no IoT Edge é `http://<IoT Edge device hostname>:11002/<account name>`. 

## <a name="deploy-multiple-instances"></a>Implantar várias instâncias

Se quiser implantar várias instâncias do Armazenamento de Blobs do Azure no IoT Edge, você só precisa alterar a HostPort com a qual o módulo está associado. Os módulos do armazenamento de blobs sempre expõem a porta 11002 no contêiner, mas é possível declarar a qual porta ele está associado no host. 

Editar as opções de criação do módulo para alterar o valor da HostPort:

```json
\"PortBindings\": {\"11002/tcp\": [{\"HostPort\":\"<port number>\"}]}
```

Ao conectar-se a módulos de armazenamento de blobs adicionais, altere o ponto de extremidade para indicar a porta do host atualizada. 

### <a name="try-it-out"></a>Experimentar

A documentação do Armazenamento de Blobs do Azure inclui guias de início rápido que fornecem códigos de exemplo em várias linguagens. É possível executar esses exemplos para testar o Armazenamento de Blobs do Azure no IoT Edge alterando o ponto de extremidade do blob para o módulo do armazenamento de blobs.

As seguintes guias de início rápido também contam com suporte do IoT Edge, assim é possível implantá-las como módulos do IoT Edge juntamente com o módulo do armazenamento de blobs:

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Java](../storage/blobs/storage-quickstart-blobs-java.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md)

## <a name="supported-storage-operations"></a>Operações de armazenamento com suporte

Os módulos do armazenamento de blobs no IoT Edge usam os mesmos SDKs do Armazenamento do Microsoft Azure, sendo consistentes com a versão 2018-03-28 da API de Armazenamento do Microsoft Azure para pontos de extremidade do blob de blocos. Versões posteriores são dependentes das necessidades do cliente. 

Nem todas as operações do Armazenamento de Blobs do Azure têm suporte do Armazenamento de Blobs do Azure no IoT Edge. As seções a seguir detalham quais operações têm suporte ou não. 

### <a name="account"></a>Conta

Com suporte: 
* Listar contêineres

Sem suporte: 
* Obter e definir propriedades do serviço Blob
* Solicitação de blob de simulação
* Obter estatísticas do serviço Blob
* Obter Informações da conta

### <a name="containers"></a>Contêineres

Com suporte: 
* Criar e excluir contêiner
* Obter Propriedades do Contêiner
* Listar blobs

Sem suporte: 
* Obter e definir ACL do contêiner
* Concessão de contêiner
* Definir Metadados do Contêiner

### <a name="blobs"></a>Blobs

Com suporte: 
* Colocar, obter e excluir blob
* Obter e definir propriedades do blob
* Obter e definir Metadados do Blob

Sem suporte: 
* Concessão de blob
* Instantâneo do blob
* Copiar e cancelar cópia do blob
* Cancelar exclusão do blob
* Definir camada do blob

### <a name="block-blobs"></a>Blobs de bloco

Com suporte: 
* Colocar bloco
* Colocar e obter lista de blocos

Sem suporte:
* Colocar bloco pela URL

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [Armazenamento de Blobs do Azure](../storage/blobs/storage-blobs-introduction.md)

