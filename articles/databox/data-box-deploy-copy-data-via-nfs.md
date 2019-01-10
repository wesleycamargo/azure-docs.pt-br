---
title: Copiar dados para o Microsoft Azure Data Box por meio do NFS | Microsoft Docs
description: Aprenda a copiar dados para sua caixa de dados do Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 11/20/2018
ms.author: alkohli
ms.openlocfilehash: 7ba6bc2cf3cf5286719bc6da519aabb364302af3
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2018
ms.locfileid: "53550214"
---
# <a name="tutorial-copy-data-to-azure-data-box-via-nfs"></a>Tutorial: Copiar dados para o Azure Data Box por meio do NFS 

Este tutorial descreve como conectar-se e copiar dados do computador host usando a interface do usuário da web local e, em seguida, preparar para enviar a caixa de dados.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Conectar-se à caixa de dados
> * Copiar dados para caixa de dados
> * Prepare-se para enviar a caixa de dados.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se:

1. Você concluiu o [Tutorial: Configurar o Azure Data Box](data-box-deploy-set-up.md).
2. Você recebeu seu Data Box e o status do pedido no portal está como **Entregue**.
3. Você tem um computador host que tem os dados que você deseja copiar para o Data Box. O computador host deve
    - Executar um [Sistema operacional com suporte](data-box-system-requirements.md).
    - Estar conectado a uma rede de alta velocidade. É altamente recomendável que você tenha pelo menos uma conexão de 10 GbE. Se uma conexão de 10 GbE não estiver disponível, um link de dados de 1 GbE poderá ser usado, mas as velocidades de cópia serão afetadas. 

## <a name="connect-to-data-box"></a>Conectar-se à caixa de dados

Com base na conta de armazenamento selecionada, o Data Box cria até:
- Três compartilhamentos para cada conta de armazenamento associada para GPv1 e GPv2.
- Uma ação para a conta premium ou de armazenamento de blobs. 

Sob o blob de bloco e os compartilhamentos de blob de página, as entidades de primeiro nível são contêineres e as entidades de segundo nível são blobs. Em compartilhamentos de Arquivos do Azure, as entidades de primeiro nível são compartilhamentos e entidades de segundo nível são arquivos.

Considere o exemplo a seguir. 

- Conta de armazenamento: *Mystoracct*
- Compartilhamento de blob de blocos: *Mystoracct_BlockBlob/my-container/blob*
- Compartilhamento de blob de páginas: *Mystoracct_PageBlob/my-container/blob*
- Compartilhamento de arquivos: *Mystoracct_AzFile/my-share*

Se você estiver usando um computador host Linux, execute as seguintes etapas para configurar o Data Box para permitir o acesso aos clientes NFS.

1. Forneça os endereços IP dos clientes permitidos que podem acessar o compartilhamento. Na interface do usuário da web local, acesse **Conectar e copiar** a página. Sob **as configurações de NFS**, clique em **acesso para cliente NFS**. 

    ![Configurar o acesso de cliente NFS 1](media/data-box-deploy-copy-data/nfs-client-access.png)

2. Forneça o endereço IP do cliente NFS e clique em **Add**. Você pode configurar o acesso para vários clientes NFS repetindo essa etapa. Clique em **OK**.

    ![Configurar o acesso do cliente NFS 2](media/data-box-deploy-copy-data/nfs-client-access2.png)

2. Assegure-se de que o computador host Linux tenha uma [versão suportada](data-box-system-requirements.md) do cliente NFS instalado. Use a versão específica para sua distribuição do Linux. 

3. Depois que o cliente NFS for instalado, use o seguinte comando para montar o compartilhamento NFS em seu dispositivo Data Box:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    O exemplo a seguir mostra como se conectar via NFS a um compartilhamento Caixa de Dados. O IP do dispositivo Data Box é `10.161.23.130`, o compartilhamento `Mystoracct_Blob` é montado no ubuntuVM, sendo o ponto de montagem`/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`

## <a name="copy-data-to-data-box"></a>Copiar dados para caixa de dados

Quando você estiver conectado aos compartilhamentos de caixa de dados, a próxima etapa é copiar dados. Antes da cópia de dados, verifique as seguintes considerações:

- Assegure-se de copiar os dados para compartilhamentos que correspondam ao formato de dados apropriado. Por exemplo, copie os dados blob do bloco para o compartilhamento de blobs de bloco. Se o formato de dados não corresponder ao tipo de compartilhamento apropriado, em uma etapa posterior, o upload de dados para o Azure falhará.
-  Ao copiar dados, verifique se o tamanho dos dados está de acordo com os limites de tamanho descritos nos [armazenamento do Azure e nos limites da Caixa de Dados](data-box-limits.md). 
- Se os dados, que estão sendo carregados pelo Data Box, forem carregados simultaneamente por outros aplicativos fora do Data Box, isso poderá resultar em falhas de trabalho de upload e corrupção de dados.
- Recomendamos que você não use o SMB e o NFS simultaneamente ou copie os mesmos dados para o mesmo destino final no Azure. Em tais casos, o resultado final não pode ser determinado.

Se você estiver usando um computador host Linux, use um utilitário de cópia semelhante ao Robocopy. Algumas das alternativas disponíveis no Linux são [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) ou [Ultracopier](https://ultracopier.first-world.info/).  

O comando `cp` é uma das melhores opções para copiar um diretório. Para mais informações sobre o uso, vá para [cp man pages](http://man7.org/linux/man-pages/man1/cp.1.html).

Se usar a opção de rsync para obter uma cópia com multithread, siga estas diretrizes:

 - Instale o pacote **CIFS Utils** ou **NFS Utils**, dependendo do sistema de arquivos usado pelo seu cliente Linux.

    `sudo apt-get install cifs-utils`

    `sudo apt-get install nfs-utils`

 -  Instale **Rsync** e **Paralelo** (varia dependendo da versão distribuída do Linux).

    `sudo apt-get install rsync`
   
    `sudo apt-get install parallel` 

 - Crie um ponto de montagem.

    `sudo mkdir /mnt/databox`

 - Monte o volume.

    `sudo mount -t NFS4  //Databox IP Address/share_name /mnt/databox` 

 - Espelhe a estrutura de pastas.  

    `rsync -za --include='*/' --exclude='*' /local_path/ /mnt/databox`

 - Copie arquivos. 

    `cd /local_path/; find -L . -type f | parallel -j X rsync -za {} /mnt/databox/{}`

     onde j especifica o número de paralelização, X = número de cópias paralelas

     Recomendamos que você inicie com 16 cópias paralelas e aumente o número de segmentos, dependendo dos recursos disponíveis.

## <a name="prepare-to-ship"></a>Preparar para o envio

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos do Azure Data Box como:

> [!div class="checklist"]
> * Conectar-se à caixa de dados
> * Copiar dados para caixa de dados
> * Preparar para o envio de dados de caixa

Avance para o próximo tutorial para saber como enviar o Data Box novamente à Microsoft.

> [!div class="nextstepaction"]
> [Envie o Azure Data Box para a Microsoft](./data-box-deploy-picked-up.md)

