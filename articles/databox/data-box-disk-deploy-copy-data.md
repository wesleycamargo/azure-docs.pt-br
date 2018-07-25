---
title: Copiar dados para o Disco do Microsoft Azure Data Box | Microsoft Docs
description: Use este tutorial para aprender a copiar dados para o Disco do Azure Data Box
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
ms.date: 07/12/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: b0769ba70f495728df5c38b43bae4059b27de88b
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39010813"
---
# <a name="tutorial-copy-data-to-azure-data-box-disk-and-verify"></a>Tutorial: Copiar dados para o Disco do Azure Data Box e verificar

Este tutorial descreve como copiar dados do computador host e gerar somas de verificação para verificar a integridade dos dados.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Copiar dados para o Disco do Data Box
> * Verificar a integridade dos dados

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se:
- Você concluiu o [Tutorial: Instalar e configurar o Disco do Azure Data Box](data-box-disk-deploy-set-up.md).
- Os discos foram desempacotados e ativados.
- Você tem um computador host para copiar dados para os discos. O computador host deve
    - Executar um [Sistema operacional com suporte](data-box-disk-system-requirements.md).
    - Ter o [Windows PowerShell 4 instalado](https://www.microsoft.com/download/details.aspx?id=40855).
    - Ter o [.NET Framework 4.5 instalado](https://www.microsoft.com/download/details.aspx?id=30653).


## <a name="copy-data-to-disks"></a>Copiar dados para os discos

Execute as seguintes etapas para fazer a conexão e copiar dados do computador para o Disco do Data Box.

1. Exiba o conteúdo da unidade desbloqueada. 

    ![Exibir conteúdo da unidade](media/data-box-disk-deploy-copy-data/data-box-disk-content.png)
 
2. Copie os dados que precisam ser importados como blobs de blocos para a pasta BlockBlob. Da mesma forma, copie dados como VHD/VHDX para a pasta PageBlob. 

    Um contêiner é criado na conta de armazenamento do Azure para cada subpasta nas pastas BlockBlob e PageBlob. Todos os arquivos nas pastas BlockBlob e PageBlob são copiados para um contêiner padrão `$root` na conta de Armazenamento do Azure. Todos os arquivos no contêiner `$root` são sempre carregados como blobs de blocos.

    Se existirem arquivos e pastas no diretório raiz, você precisará movê-los para uma pasta diferente antes de começar a copiar os dados.

    Siga os requisitos de nomenclatura do Azure para nomes de contêiner e blob.

    |Entidade   |Convenções  |
    |---------|---------|
    |Nomes de contêiner de blob de blocos e blob de páginas     |É necessário iniciar com uma letra ou um número e pode conter apenas letras minúsculas, números e hifens. Cada hífen (-) precisa ser imediatamente precedido e seguido por uma letra ou um número. Hifens consecutivos não são permitidos em nomes. <br>Precisa ser um nome DNS válido com tamanho de 3 a 63 caracteres.          |
    |Nomes de blob para blobs de blocos e blobs de página    |Nomes de blob diferenciam maiúsculas de minúsculas e podem conter qualquer combinação de caracteres. <br>O nome do blob deve ter entre 1 e 1.024 caracteres.<br>Os caracteres reservados de URL precisam ser escapados corretamente.<br>O número de segmentos de caminho que incluem o nome do blob não pode exceder 254. Um segmento de caminho é a cadeia de caracteres entre caracteres delimitadores consecutivos (por exemplo, a barra '/') que correspondem ao nome de um diretório virtual.         |

    > [!IMPORTANT] 
    > Todos os contêineres e blobs devem obedecer às [convenções de nomenclatura do Azure](data-box-disk-limits.md#azure-block-blob-and-page-blob-naming-conventions). Se essas regras não forem seguidas, o carregamento de dados do Azure falhará.

3. Ao copiar arquivos, verifique se os arquivos não excedem ~4,7 TiB para blobs de bloco e ~8 TiB para blobs de página. 
4. Você pode arrastar e soltar com o Explorador de Arquivos para copiar os dados. Você também pode usar qualquer ferramenta de cópia de arquivo compatível com SMB, como Robocopy, para copiar seus dados. Vários trabalhos de cópia podem ser iniciados usando o seguinte comando Robocopy:

    `Robocopy <source> <destination>  * /MT:64 /E /R:1 /W:1 /NFL /NDL /FFT /Log:c:\RobocopyLog.txt` 
    
    Os parâmetros e as opções para o comando são tabulados da seguinte maneira:
    
    |Parâmetros/Opções  |DESCRIÇÃO |
    |--------------------|------------|
    |<Source>            | Especifica o caminho para o diretório de origem.        |
    |<Destination>       | Especifica o caminho para o diretório de destino.        |
    |/E                  | Copia subdiretórios, inclusive diretórios vazios. |
    |/MT[:N]             | Cria cópias com N threads, em que N é um inteiro entre 1 e 128. <br>O valor padrão para N é 8.        |
    |/R: <N>             | Especifica o número de repetições em cópias com falha. O valor padrão de N é 1.000.000 (um milhão de repetições).        |
    |/W: <N>             | Especifica o tempo de espera entre as tentativas, em segundos. O valor padrão de N é 30 (tempo de espera de 30 segundos).        |
    |/ NFL                | Especifica que os nomes de arquivo não devem ser registrados.        |
    |/NDL                | Especifica que os nomes de diretório não devem ser registrados.        |
    |/FFT                | Pressupõe tempos de arquivo FAT (precisão de dois segundos).        |
    |/Log:<Log File>     | Grava a saída de status no arquivo de log (substitui o arquivo de log existente).         |

    Vários discos podem ser usados em paralelo com vários trabalhos em execução em cada disco. 

6. Verifique o status da cópia quando o trabalho está em andamento. O exemplo a seguir mostra a saída do comando robocopy para copiar arquivos no Disco do Data Box.

    ```
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
    
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:64 /E /R:1 /W:1 /FFT 
    -------------------------------------------------------------------------------
       ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
      Started : Thursday, March 8, 2018 2:34:58 PM
       Source : C:\Git\azure-docs-pr\contributor-guide\
         Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\
    
        Files : *.*
    
      Options : *.* /DCOPY:DA /COPY:DAT /MT:8 /R:1000000 /W:30
    
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
       Times :   0:00:05   0:00:00                       0:00:00   0:00:00
        
       Speed :                5620 Bytes/sec.
       Speed :               0.321 MegaBytes/min.
       Ended : Thursday, March 8, 2018 2:34:59 PM
        
    C:\Users>
    ```
 
    
7. Abra a pasta de destino para exibir e verificar os arquivos copiados. Se você receber erros durante o processo de cópia, baixe os arquivos de log para solucionar problemas. Os arquivos de log estão localizados no local especificado no comando robobopy.
 


> [!IMPORTANT]
> - Você é responsável por copiar os dados para pastas que correspondam ao formato de dados apropriado. Por exemplo, copie os dados de blob de bloco para a pasta de blobs de blocos. Se o formato de dados não corresponder à pasta apropriada (tipo de armazenamento), o carregamento de dados do Azure falhará em uma etapa posterior.
> -  Durante a cópia de dados, verifique se o tamanho dos dados está em conformidade com os limites de tamanho descritos nos [limites do armazenamento do Azure e do Disco do Data Box](data-box-disk-limits.md). 
> - Se dados estiverem sendo carregados pelo Disco do Data Box e por outro aplicativo fora do Disco do Data Box ao mesmo tempo, isso poderá causar falha no trabalho de upload e corromper os dados.

## <a name="verify-data-integrity"></a>Verificar a integridade dos dados

Para verificar a integridade dos dados, execute as etapas a seguir.

1. Execute `AzureExpressDiskService.ps1` para validação de soma de verificação. No Explorador de Arquivos, vá para a pasta *AzureImportExport* da unidade. Clique com botão direito do mouse e selecione **Executar com o PowerShell**. 

    ![Executar soma de verificação](media/data-box-disk-deploy-copy-data/data-box-disk-checksum.png)

2. Dependendo do tamanho dos dados, esta etapa pode demorar um pouco. Um resumo do processo de verificação de integridade de dados e o tempo para concluir o processo são exibidos quando o script é concluído. Você pode pressionar **Enter** para sair da janela de comando.

    ![Saída da soma de verificação](media/data-box-disk-deploy-copy-data/data-box-disk-checksum-output.png)

3. Se estiver usando vários discos, execute o comando para cada disco.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos do Disco do Azure Data Box, como:

> [!div class="checklist"]
> * Copiar dados para o Disco do Data Box
> * Verificar a integridade dos dados

Avance para o próximo tutorial a fim de saber como devolver o Disco do Data Box e verificar o carregamento de dados do Azure.

> [!div class="nextstepaction"]
> [Devolver o Azure Data Box para a Microsoft](./data-box-disk-deploy-picked-up.md)

