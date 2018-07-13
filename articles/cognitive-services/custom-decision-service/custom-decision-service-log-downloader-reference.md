---
title: LogDownloader - Serviços Cognitivos do Azure | Microsoft Docs
description: Baixe arquivos de log produzidos pelo Serviço de Decisão Personalizada do Azure.
services: cognitive-services
author: marco-rossi29
manager: marco-rossi29
ms.service: cognitive-services
ms.topic: article
ms.date: 05/09/2018
ms.author: marossi
ms.openlocfilehash: 783b534b3b3f4bb7f5d9f073f491690759edfea5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364408"
---
# <a name="logdownloader"></a>LogDownloader

Baixe arquivos de log produzidos pelo Serviço de Decisão Personalizada do Azure e gere os arquivos *.gz* usados pela Experimentação.

## <a name="prerequisites"></a>pré-requisitos

- Python 3: instalado e no caminho. É recomendável a versão de 64 bits para manipular arquivos grandes.
- O repositório *Microsoft/mwt-ds*: [clone o repositório](https://github.com/Microsoft/mwt-ds).
- O pacote *azure-storage-blob*: para detalhes de instalação, acesse a [Biblioteca de Armazenamento do Microsoft Azure para Python](https://github.com/Azure/azure-storage-python#option-1-via-pypi).
- Insira a cadeia de conexão de armazenamento do Azure em *mwt-ds/DataScience/ds.config*: siga o modelo *my_app_id: my_connectionString*. É possível especificar várias `app_id`. Ao executar `LogDownloader.py`, se a entrada `app_id` não for localizada em `ds.config`, `LogDownloader.py` usará a cadeia de conexão `$Default`.

## <a name="usage"></a>Uso

Vá para `mwt-ds/DataScience` e execute `LogDownloader.py` com os argumentos relevantes, conforme detalhado no código a seguir:

```cmd
python LogDownloader.py [-h] -a APP_ID -l LOG_DIR [-s START_DATE]
                        [-e END_DATE] [-o OVERWRITE_MODE] [--dry_run]
                        [--create_gzip] [--delta_mod_t DELTA_MOD_T]
                        [--verbose] [-v VERSION]
```

### <a name="parameters"></a>parâmetros

| Entrada | DESCRIÇÃO | Padrão |
| --- | --- | --- |
| `-h`, `--help` | Mostrar a mensagem de ajuda e sair. | |
| `-a APP_ID`, `--app_id APP_ID` | A ID do aplicativo (ou seja, o nome do contêiner de Armazenamento de Blobs do Azure). | Obrigatório |
| `-l LOG_DIR`, `--log_dir LOG_DIR` | O diretório básico para download de dados (uma subpasta é criada).  | Obrigatório |
| `-s START_DATE`, `--start_date START_DATE` | A data de início do download (incluída), no formato *AAAA/MM/DD*. | `None` |
| `-e END_DATE`, `--end_date END_DATE` | A data de término do download (incluída), no formato *AAAA/MM/DD*. | `None` |
| `-o OVERWRITE_MODE`, `--overwrite_mode OVERWRITE_MODE` | O modo de substituição para usar. | |
| | `0`: nunca substitua; pergunte ao usuário se os blobs estão atualmente em uso. | Padrão | |
| | `1`: pergunte ao usuário como proceder quando os arquivos tiverem tamanhos diferentes ou quando os blobs estiverem sendo usados atualmente. | |
| | `2`: sempre substitua; baixe os blobs atualmente em uso. | |
| | `3`: nunca substitua, e acrescente se o tamanho for maior, sem perguntar; baixe os blobs atualmente em uso. | |
| | `4`: Nunca substitua, e acrescente se o tamanho for maior, sem perguntar; ignore os blobs atualmente em uso. | |
| `--dry_run` | Imprima quais blobs teriam sido baixados, sem baixar. | `False` |
| `--create_gzip` | Crie um arquivo *gzip* para Vowpal Wabbit. | `False` |
| `--delta_mod_t DELTA_MOD_T` | A janela de tempo, em segundos, para detectar se um arquivo está em uso no momento. | `3600` seg (`1` hora) |
| `--verbose` | Imprima mais detalhes. | `False` |
| `-v VERSION`, `--version VERSION` | A versão do downloader de log a ser usada. | |
| | `1`: para logs não processados (somente para compatibilidade com versões anteriores). | Preteridos |
| | `2`: para logs processados. | Padrão |

### <a name="examples"></a>Exemplos

Para uma simulação de download de todos os dados no contêiner de Armazenamento de Blobs do Azure, use o código a seguir:
```cmd
python LogDownloader.py -a your_app_id -l d:\data --dry_run
```

Para baixar apenas os logs criados desde 1º de janeiro de 2018 com `overwrite_mode=4`, use o código a seguir:
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4
```

Para criar um arquivo *gzip* mesclando todos os arquivos baixados, use o código a seguir:
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4 --create_gzip
```