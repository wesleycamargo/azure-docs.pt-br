---
title: Copiar dados para sua caixa de dados do Microsoft Azure | Microsoft Docs
description: Aprenda a copiar dados para sua caixa de dados do Azure
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/10/2018
ms.author: alkohli
ms.openlocfilehash: d606b7b536147b7e1d96bfb6cebf2522e5b4f85f
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078833"
---
# <a name="tutorial-copy-data-to-azure-data-box"></a>Tutorial: Copiar dados para a caixa de dados do Azure 

Este tutorial descreve como conectar-se e copiar dados do computador host usando a interface do usuário da web local e, em seguida, preparar para enviar a caixa de dados.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Conectar-se à caixa de dados
> * Copiar dados para caixa de dados
> * Prepare-se para enviar a caixa de dados.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se:

1. Você concluiu o [Tutorial: Configurar a Caixa de Dados do Azure](data-box-deploy-set-up.md).
2. Você recebeu sua caixa de dados e o status do pedido no portal está **Entregue**.
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
- Compartilhamento para o blob de blocos: *Mystoracct_BlockBlob/my-contêiner/blob*
- Compartilhamento para o blob de página: *Mystoracct_PageBlob/my-contêiner/blob*
- Compartilhamento de arquivo: *Mystoracct_AzFile/my-compartilhar.*

Dependendo se sua caixa de dados está conectada a um computador host do Windows Server ou a um host Linux, as etapas para conectar e copiar podem ser diferentes.

### <a name="connect-via-smb"></a>Conecte-se via SMB 

Se você estiver usando um computador de host do Windows Server, execute as seguintes etapas para conectar-se para o Data Box.

1. O primeiro passo é autenticar e iniciar uma sessão. Vá para **conectar e copiar**. Clique em **Obter credenciais** para obter as credenciais de acesso aos compartilhamentos associados à sua conta de armazenamento. 

    ![Obter as credenciais de compartilhamento 1](media/data-box-deploy-copy-data/get-share-credentials1.png)

2. No compartilhamento de acesso e caixa de diálogo de dados de cópia, copie o **nome de usuário** e o **senha** correspondente para o compartilhamento. Clique em **OK**.
    
    ![Obter as credenciais de compartilhamento 1](media/data-box-deploy-copy-data/get-share-credentials2.png)

3. Acesse os compartilhamentos associados à sua conta de armazenamento (Mystoracct no exemplo a seguir). Use o `\\<IP of the device>\ShareName` caminho para acessar os compartilhamentos. Dependendo do seu formato de dados, conecte-se aos compartilhamentos (use o nome do compartilhamento) no seguinte endereço: 
    - *\\<IP address of the device>\Mystoracct_Blob*
    - *\\<IP address of the device>\Mystoracct_Page*
    - *\\<IP address of the device>\Mystoracct_AzFile*
    
    Para se conectar aos compartilhamentos do computador host, abra uma janela de comando. No prompt de comando, digite:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Quando solicitado, digite a senha do compartilhamento. A amostra a seguir mostra a conexão a um compartilhamento por meio do comando anterior.

    ```
    C:\Users\Databoxuser>net use \\10.126.76.172\devicemanagertest1_BlockBlob /u:devicemanagertest1
    Enter the password for 'devicemanagertest1' to connect to '10.126.76.172':
    The command completed successfully.
    ```

4. Pressione Windows + R. Na janela **Executar**, especifique o `\\<device IP address>`. Clique em **OK**. O File Explorer será aberto. Agora você deve conseguir ver os compartilhamentos como pastas.
    
    ![Conecte-se para compartilhar via File Explorer 2](media/data-box-deploy-copy-data/connect-shares-file-explorer1.png)

5.  **Sempre crie uma pasta para os arquivos que você pretende copiar no compartilhamento e, em seguida, copie os arquivos para a pasta**. Ocasionalmente, as pastas podem mostrar uma cruz cinza. A cruz não denota uma condição de erro. As pastas são sinalizadas pelo aplicativo para rastrear o status.
    
    ![Conecte-se para compartilhar via File Explorer 2](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png) ![Conecte-se para compartilhar via File Explorer 2](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png) 

### <a name="connect-via-nfs"></a>Conectar-se por meio de NFS 

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

### <a name="copy-data-via-smb"></a>Copiar dados via SMB

Depois de se conectar ao compartilhamento SMB, inicie uma cópia de dados. 

Você pode usar qualquer ferramenta de cópia de arquivos compatível com SMB, como o Robocopy, para copiar seus dados. Vários trabalhos de cópia podem ser iniciados usando o Robocopy. Use o seguinte comando:
    
    robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile> 
  
 Os atributos são descritos na tabela a seguir.
    
|Atributo  |DESCRIÇÃO  |
|---------|---------|
|/e     |Copia subdiretórios, inclusive diretórios vazios.         |
|/r:     |Especifica o número de repetições em cópias com falha.         |
|/w:     |Especifica o tempo de espera entre as tentativas, em segundos.         |
|/is     |Inclui os mesmos arquivos.         |
|/nfl     |Especifica que os nomes de arquivo não devem ser registrados.         |
|/ndl    |Especifica que os nomes de diretório não devem ser registrados.        |
|/np     |Especifica que o progresso da operação de cópia (o número de arquivos ou diretórios copiados até o momento) não será exibido. Exibindo o progresso reduz significativamente o desempenho.         |
|/MT     | Use multithreading, recomendado de 32 ou 64 threads. Esta opção não é usada com arquivos criptografados. Você pode precisar separar arquivos criptografados e não criptografados. No entanto, a única cópia encadeada reduz significativamente o desempenho.           |
|/fft     | Use para reduzir a granularidade do registro de data e hora para qualquer sistema de arquivos.        |
|/b     | Copia os arquivos no modo de Backup.        |
|/z    | Copia os arquivos no modo de reinicialização, use essa opção se o ambiente estiver instável. Esta opção reduz o rendimento devido ao log adicional.      |
| /zb     | Usa o modo de reinicialização. Se o acesso for negado, esta opção usa o modo Backup. Esta opção reduz o rendimento devido ao ponto de verificação.         |
|/efsraw     | Copia todos os arquivos criptografados em modo raw do EFS. Use somente com arquivos criptografados.         |
|log+:<LogFile>| Acrescenta a saída ao arquivo de log existente.|    
 
A amostra a seguir mostra a saída do comando robocopy para copiar arquivos para a Caixa de Dados.
    
    C:\Users>robocopy
        -------------------------------------------------------------------------------
        ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
        Started : Thursday, March 8, 2018 2:34:53 PM
            Simple Usage :: ROBOCOPY source destination /MIR
    
                    source :: Source Directory (drive:\path or \\server\share\path).
            destination :: Destination Dir  (drive:\path or \\server\share\path).
                    /MIR :: Mirror a complete directory tree.
    
        For more usage information run ROBOCOPY /?    
    
    ****  /MIR can DELETE files as well as copy them !
    
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:32
    -------------------------------------------------------------------------------
        ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
        Started : Thursday, March 8, 2018 2:34:58 PM
        Source : C:\Git\azure-docs-pr\contributor-guide\
            Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\
    
        Files : *.*
    
        Options : *.* /DCOPY:DA /COPY:DAT /MT:32 /R:5 /W:60
    
    ------------------------------------------------------------------------------
    
    100%        New File                 206        C:\Git\azure-docs-pr\contributor-guide\article-metadata.md
    100%        New File                 209        C:\Git\azure-docs-pr\contributor-guide\content-channel-guidance.md
    100%        New File                 732        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-index.md
    100%        New File                 199        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pr-criteria.md
                New File                 178        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-co100%  .md
                New File                 250        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-et100%  e.md
    100%        New File                 174        C:\Git\azure-docs-pr\contributor-guide\create-images-markdown.md
    100%        New File                 197        C:\Git\azure-docs-pr\contributor-guide\create-links-markdown.md
    100%        New File                 184        C:\Git\azure-docs-pr\contributor-guide\create-tables-markdown.md
    100%        New File                 208        C:\Git\azure-docs-pr\contributor-guide\custom-markdown-extensions.md
    100%        New File                 210        C:\Git\azure-docs-pr\contributor-guide\file-names-and-locations.md
    100%        New File                 234        C:\Git\azure-docs-pr\contributor-guide\git-commands-for-master.md
    100%        New File                 186        C:\Git\azure-docs-pr\contributor-guide\release-branches.md
    100%        New File                 240        C:\Git\azure-docs-pr\contributor-guide\retire-or-rename-an-article.md
    100%        New File                 215        C:\Git\azure-docs-pr\contributor-guide\style-and-voice.md
    100%        New File                 212        C:\Git\azure-docs-pr\contributor-guide\syntax-highlighting-markdown.md
    100%        New File                 207        C:\Git\azure-docs-pr\contributor-guide\tools-and-setup.md
    ------------------------------------------------------------------------------
    
                    Total    Copied   Skipped  Mismatch    FAILED    Extras
        Dirs :         1         1         1         0         0         0
        Files :        17        17         0         0         0         0
        Bytes :     3.9 k     3.9 k         0         0         0         0          
    C:\Users>
       

Para otimizar o desempenho, use os seguintes parâmetros de robocopy ao copiar os dados.

|    Plataforma    |    Principalmente pequenos arquivos < 512 KB                           |    Principalmente arquivos médios 512 KB - 1 MB                      |    Principalmente grandes arquivos > 1 MB                             |   
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|---|
|    Data Box         |    Sessões de Robocopy 2 <br> 16 threads por sessões    |    Sessões de Robocopy 3 <br> 16 threads por sessões    |    Sessões de Robocopy 2 <br> 24 threads por sessões    |  |


Para mais informações sobre o comando Robocopy, vá para [Robocopy e alguns exemplos](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx).

Abra a pasta de destino para exibir e verificar os arquivos copiados. Se você tiver algum erro durante o processo de cópia, baixe os arquivos de erro para solução de problemas.

Para garantir a integridade dos dados, a soma de verificação é computada em linha à medida que os dados são copiados. Quando a cópia estiver concluída, verifique o espaço usado e o espaço livre no seu dispositivo.
    
   ![Verificar o espaço livre e usado no painel](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)

### <a name="copy-data-via-nfs"></a>Copiar dados por meio de NFS

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

Etapa final é preparar o dispositivo para o envio. Nesta etapa, todos os compartilhamentos do dispositivo são colocados offline. As ações não podem ser acessadas depois que você começar a preparar o dispositivo para enviar.
1. Vá para **Preparar para enviar** e clique em **Iniciar preparação**. 
   
    ![Preparar para o envio 1](media/data-box-deploy-copy-data/prepare-to-ship1.png)

2. Por padrão, as somas de verificação são computadas embutidas ao preparar para o envio. O cálculo da soma de verificação pode levar algum tempo dependendo do tamanho dos seus dados. Clique em **iniciar preparação**.
    1. Os compartilhamentos de dispositivos ficam off-line e o dispositivo fica bloqueado quando nos preparamos para enviar.
        
        ![Preparar para o envio 1](media/data-box-deploy-copy-data/prepare-to-ship2.png) 
   
    2. O status do dispositivo é atualizado para *Pronto para envio* após a conclusão da preparação do dispositivo. 
        
        ![Preparar para o envio 1](media/data-box-deploy-copy-data/prepare-to-ship3.png)

    3. Faça o download da lista de arquivos (manifesto) que foram copiados nesse processo. Posteriormente, você poderá usar essa lista para verificar os arquivos carregados no Azure.
        
        ![Preparar para o envio 1](media/data-box-deploy-copy-data/prepare-to-ship4.png)

3. Desligar o dispositivo. Vá para **desligar ou reiniciar** da página e clique em **desligar**. Quando solicitado a confirmação, clique em **OK** para continuar.
4. Remova os cabos. A próxima etapa é enviar o dispositivo à Microsoft.

 
<!--## Appendix - robocopy parameters

This section describes the robocopy parameters used when copying the data to optimize the performance.

|    Platform    |    Mostly small files < 512 KB                           |    Mostly medium  files 512 KB-1 MB                      |    Mostly large files > 1 MB                             |   
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|---|
|    Data Box         |    2 Robocopy sessions <br> 16 threads per sessions    |    3 Robocopy sessions <br> 16 threads per sessions    |    2 Robocopy sessions <br> 24 threads per sessions    |  |
|    Data Box Heavy     |    6 Robocopy sessions <br> 24 threads per sessions    |    6 Robocopy sessions <br> 16 threads per sessions    |    6 Robocopy sessions <br> 16 threads per sessions    |   
|    Data Box Disk         |    4 Robocopy sessions <br> 16 threads per sessions             |    2 Robocopy sessions <br> 16 threads per sessions    |    2 Robocopy sessions <br> 16 threads per sessions    |   
-->

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos do Azure Data Box como:

> [!div class="checklist"]
> * Conectar-se à caixa de dados
> * Copiar dados para caixa de dados
> * Preparar para o envio de dados de caixa

Avance para o próximo tutorial para aprender a configurar e copiar dados em sua caixa de dados.

> [!div class="nextstepaction"]
> [Envie o Azure Data Box para a Microsoft](./data-box-deploy-picked-up.md)

