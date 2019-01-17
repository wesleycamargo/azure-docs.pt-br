---
title: Armazenar blob de blocos nos dispositivos – Azure IoT Edge | Microsoft Docs
description: Implante um módulo do Armazenamento de Blobs do Azure no dispositivo do IoT Edge para armazenar dados na borda.
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 01/04/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: b5dcc4295467aee82547bc2a0894320bd110998f
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/07/2019
ms.locfileid: "54065810"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>Armazenar dados na borda com o Armazenamento de Blobs do Azure no IoT Edge (versão prévia)

O Armazenamento de Blob do Azure no IoT Edge fornece uma solução de armazenamento de [blob de blocos](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) na borda. Um módulo do armazenamento de blobs no dispositivo do IoT Edge comporta-se como um serviço de blobs de blocos do Azure, mas os blobs de blocos são armazenados localmente no dispositivo do IoT Edge. É possível acessar os blobs usando os mesmos métodos do SDK do armazenamento do Azure ou as chamadas à API do blob de blocos que você já está acostumado. 

Cenários em que os dados, como vídeos, imagens, dados financeiros, dados hospitalares ou todos os dados que precisam ser armazenados localmente mais tarde, os quais podem ser processados localmente ou transferidos para a nuvem são bons exemplos para usar este módulo.

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

Existes várias maneiras de implantar módulos em um dispositivo do IoT Edge, sendo que todas elas funcionam para o Armazenamento de Blobs do Azure nos módulos do IoT Edge. Os dois métodos mais simples são utilizar o portal do Azure ou os modelos do Visual Studio Code. 

### <a name="azure-portal"></a>Portal do Azure

O Azure Marketplace fornece módulos do IoT Edge que podem ser implantados diretamente em seus dispositivos IoT Edge, incluindo no Armazenamento de Blobs do Azure no IoT Edge. Siga estas etapas para implantar o módulo do portal do Azure.

1. No [Portal do Azure](https://portal.azure.com), pesquise por “Armazenamento de Blobs do Azure no IoT Edge”. E **selecione** o resultado da pesquisa do Marketplace.

   ![Criar um módulo da pesquisa do Marketplace](./media/how-to-store-data-blob/marketplace-module.png)

2. Escolha um dispositivo IoT Edge para receber esse módulo. Na página **Dispositivos de Destino para o Módulo do IoT Edge**, forneça as seguintes informações:

   1. Selecione a **Assinatura** que contém o hub IoT que está sendo usado.

   2. Selecione seu **Hub IoT**.

   3. Se você souber o **Nome do Dispositivo do IoT Edge**, insira-o na caixa de texto. Ou selecione **Encontrar Dispositivo** para escolher um em uma lista de dispositivos IoT Edge no hub IoT. 
   
   4. Selecione **Criar**.

   Agora que escolheu um módulo do IoT Edge do Azure Marketplace e escolheu um dispositivo IoT Edge para receber o módulo, você será levado para um assistente de três etapas que ajudará você a definir exatamente como o módulo será implantado.

3. Na etapa **Adicionar Módulos** do assistente de definição de módulos, observe que o módulo **AzureBlobStorageonIoTEdge** já está listado em **Módulos de Implantação**. 

2. Selecione o módulo de armazenamento de blob na lista de módulos de implantação para abrir os detalhes do módulo. 

   ![Selecione o nome do módulo para abrir os detalhes do módulo](./media/how-to-store-data-blob/open-module-details.png)

3. Na página **Módulos personalizados do IoT Edge**, atualize o Armazenamento de Blobs do Azure no módulo do IoT Edge com as seguintes etapas:

   1. Altere o **Nome** do módulo para que ele fique em letras minúsculas. Você pode renomear o módulo se desejar, ou usar `azureblobstorageoniotedge`. 

      >[!IMPORTANT]
      >O Azure IoT Edge diferencia maiúsculas de minúsculas quando você faz chamadas para módulos e o SDK de Armazenamento usa minúsculas por padrão. Para garantir que suas conexões com o Armazenamento de Blobs do Azure no módulo do IoT Edge não sejam interrompidas, dê um nome com minúsculas. 

   2. As **Opções de Criação de Contêiner** padrão usam as associações de porta de que seu contêiner precisa, mas você também precisa adicionar informações de sua conta de armazenamento e uma associação para o diretório de armazenamento no dispositivo. Substitua o JSON no portal pelo JSON abaixo:
    
      ```json
      {
          "Env":[
              "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
              "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
          ],
          "HostConfig":{
              "Binds":[
                  "<storage directory bind>"
              ],
              "PortBindings":{
                  "11002/tcp":[{"HostPort":"11002"}]
              }
          }
      }
      ```   
   3. Atualize o JSON que você copiou com as seguintes informações: 

      * Substitua `<your storage account name>` por um nome de que você possa se lembrar. Os nomes da conta devem ter de três a vinte quatro caracteres, com letras minúsculas e números.
      * Substitua `<your storage account key>` por uma chave base64 de 64 bytes. É possível gerar uma chave com ferramentas como [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Você utilizará essas credenciais para acessar o armazenamento de blobs a partir de outros módulos.
      * Substitua `<storage directory bind>` dependendo do sistema operacional do contêiner. Forneça o nome de um [volume](https://docs.docker.com/storage/volumes/) ou o caminho absoluto para um diretório no dispositivo do IoT Edge no qual você quer que o módulo do blob armazene os dados. A associação do diretório de armazenamento mapeia um local em seu dispositivo que você fornece para um local definido no módulo. 

         * Contêineres do Linux: **\<storage path>:/blobroot**. Por exemplo, /srv/containerdata:/blobroot. Ou my-volume:/blobroot. 
         * Contêineres do Windows: **\<storage path>:C:/BlobRoot**. Por exemplo, C:/ContainerData:C:/BlobRoot. Ou my-volume:C:/blobroot.
   
      > [!IMPORTANT]
      > Não altere a segunda metade do valor da associação do diretório de armazenamento, que aponta para um local específico no módulo. A associação do diretório de armazenamento sempre deve terminar com **:/blobroot** para contêineres Linux e com **:C:/BlobRoot** para contêineres Windows.

      ![Atualizar as opções de criar contêiner do módulo - portal](./media/how-to-store-data-blob/edit-module.png)

   4. Clique em **Salvar**.

4. Selecione **Avançar** para continuar e ir para a próxima etapa do assistente.
5. Na etapa **Especificar Rotas** do assistente, selecione **Avançar**.
6. No assistente, na etapa **Revisar implantação**, selecione **Enviar**.
7. Depois de enviar a implantação, você volta para a página do **IoT Edge** de seu hub IoT. Selecione o dispositivo do IoT Edge ao qual você direcionou a implantação para abrir os detalhes. 
8. Nos detalhes do dispositivo, verifique se o módulo de armazenamento de blob é listado como **Especificado na implantação** e **Relatado por dispositivo**. Pode levar alguns instantes para que o módulo seja iniciado no dispositivo e, em seguida, seja relatado de volta para o Hub IoT. Atualize a página para ver o status atualizado. 

### <a name="visual-studio-code-templates"></a>Modelos do Visual Studio Code

O Azure IoT Edge disponibiliza modelos no Visual Studio Code para ajudar você a desenvolver soluções de borda. Essas etapas requerem que você tenha o [Visual Studio Code](https://code.visualstudio.com/) instalado no computador de desenvolvimento, além de estar configurado com as [Ferramentas do Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

Execute as etapas a seguir para criar uma nova solução do IoT Edge com o módulo de armazenamento de blobs e, em seguida, configure o manifesto de implantação. 

1. Selecione **Exibir** > **Paleta de Comandos**. 

2. Na paleta de comandos, digite e execute o comando **Azure IoT Edge: nova solução do IoT Edge**. Siga os prompts na paleta de comandos para criar sua solução.

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha o local no computador de desenvolvimento em que o VS Code criará os arquivos de solução. |
   | Fornecer um nome para a solução | Insira um nome descritivo para a solução ou aceite o padrão **EdgeSolution**. |
   | Selecionar modelo do módulo | Escolha **Módulo Existente (Inserir a URL de imagem completa)**. |
   | Fornecer um nome de módulo | Insira um nome totalmente em minúsculas para seu módulo, como **azureblobstorage**.<br><br>É importante usar um nome em minúsculas para o Armazenamento de Blobs do Azure no módulo do IoT Edge. O IoT Edge diferencia maiúsculas de minúsculas ao se referir a chamadas para módulos e o SDK de Armazenamento usa minúsculas por padrão. |
   | Fornecer a imagem do Docker para o módulo | Forneça o URI da imagem: **mcr.microsoft.com/azure-blob-storage:latest** |

   O VS Code usa as informações fornecidas, cria uma solução do IoT Edge e, em seguida, a carrega em uma nova janela. O modelo da solução cria um modelo do manifesto de implantação que inclui a imagem do módulo do armazenamento de blobs, mas é necessário configurar as opções de criação do módulo. 

3. Abra **deployment.template.json** no workspace da nova solução e encontre a seção **módulos**. Faça as seguintes alterações de configuração:

   1. Exclua o módulo **tempSensor**, pois ele é desnecessário para esta implantação. 

   2. Copie e cole o código a seguir no campo **createOptions** do módulo do armazenamento de blobs: 

      ```json
      "Env": [
        "LOCAL_STORAGE_ACCOUNT_NAME=$STORAGE_ACCOUNT_NAME","LOCAL_STORAGE_ACCOUNT_KEY=$STORAGE_ACCOUNT_KEY"
      ],
      "HostConfig":{
        "Binds": ["<storage directory bind>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![Atualizar o módulo createOptions - VS Code](./media/how-to-store-data-blob/create-options.png)

4. No JSON das opções de criação, atualize `<storage directory bind>` dependendo do sistema operacional do seu contêiner. Forneça o nome de um [volume](https://docs.docker.com/storage/volumes/) ou o caminho absoluto para um diretório no dispositivo do IoT Edge no qual você quer que o módulo do blob armazene os dados. A associação do diretório de armazenamento mapeia um local em seu dispositivo que você fornece para um local definido no módulo.  

   * Contêineres do Linux: **\<storage path>:/blobroot**. Por exemplo, /srv/containerdata:/blobroot. Ou my-volume:/blobroot.
   * Contêineres do Windows: **\<storage path>:C:/BlobRoot**. Por exemplo, C:/ContainerData:C:/BlobRoot. Ou my-volume:C:/blobroot.
   
   > [!IMPORTANT]
   > Não altere a segunda metade do valor da associação do diretório de armazenamento, que aponta para um local específico no módulo. A associação do diretório de armazenamento sempre deve terminar com **:/blobroot** para contêineres Linux e com **:C:/BlobRoot** para contêineres Windows.

5. Salve o arquivo **deployment.template.json**.

6. Abra o arquivo **.env** no seu workspace da solução. 

7. O arquivo .env está configurado para receber credenciais de registro de contêiner, mas você não precisa disso para a imagem do armazenamento de blob, pois ele está disponível publicamente. Em vez disso, substitua o arquivo por duas novas variáveis de ambiente: 

   ```env
   STORAGE_ACCOUNT_NAME=
   STORAGE_ACCOUNT_KEY=
   ```

8. Forneça um valor para `STORAGE_ACCOUNT_NAME`, os nomes da conta devem ter de três a vinte quatro caracteres, com letras minúsculas e números. Forneça uma chave de base64 de 64 bytes para o `STORAGE_ACCOUNT_KEY`. É possível gerar uma chave com ferramentas como [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Você utilizará essas credenciais para acessar o armazenamento de blobs a partir de outros módulos. 

   Não inclua espaços ou aspas ao redor dos valores que você fornecer. 

9. Salve o arquivo **.env**. 

10. Clique com o botão direito do mouse em **deployment.template.json** e selecione **Gerar manifesto de implantação do IoT Edge**. 

11. O Visual Studio Code coleta as informações fornecidas no deployment.template.json e .env e as usa para criar um novo arquivo de manifesto de implantação. O manifesto de implantação é criado em uma nova pasta **config** no workspace da solução. Ao obter esse arquivo, você pode seguir as etapas em [Implantar módulos do Azure IoT Edge a partir do Visual Studio Code](how-to-deploy-modules-vscode.md) ou [Implantar módulos do Azure IoT Edge com a CLI 2.0 do Azure](how-to-deploy-modules-cli.md).

## <a name="connect-to-your-blob-storage-module"></a>Conectar ao módulo do armazenamento de blobs

É possível usar o nome da conta e a chave de conta que você configurou para o módulo para acessar o armazenamento de blobs no seu dispositivo do IoT Edge. 

Especifique o dispositivo do IoT Edge como ponto de extremidade do blob para quaisquer solicitações de armazenamento que você faz para esse dispositivo. É possível [Criar uma cadeia de conexão para um ponto de extremidade explícito](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) usando as informações do dispositivo do IoT Edge e do nome da conta que você configurou. 

1. Para módulos que são implantados no mesmo dispositivo de borda em que "Azure Blob Storage no IoT Edge" estiver em execução, o ponto de extremidade do blob é: `http://<module name>:11002/<account name>`. 
2. Para módulos que são implantados no dispositivo de borda diferente, que o dispositivo de borda em que "Azure Blob Storage no IoT Edge" está em execução e, em seguida, dependendo de sua configuração de ponto de extremidade do blob é: `http://<device IP >:11002/<account name>` `http://<IoT Edge device hostname>:11002/<account name>` ou `http://<FQDN>:11002/<account name>`

## <a name="logs"></a>Logs

Você pode encontrar os logs de dentro do contêiner, em: 
* Para Linux: /blobroot/logs/platformblob.log

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
* Coloque o bloco:-o bloco deve ser menor ou igual a 4 MB de tamanho
* Colocar e obter lista de blocos

Sem suporte:
* Colocar bloco pela URL

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [Armazenamento de Blobs do Azure](../storage/blobs/storage-blobs-introduction.md)

