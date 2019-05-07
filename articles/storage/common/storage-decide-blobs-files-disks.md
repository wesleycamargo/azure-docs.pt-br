---
title: Decidindo quando usar Blobs do Azure, Arquivos do Azure ou Discos do Azure
description: Saiba mais sobre as diferentes maneiras de armazenar e acessar dados no Azure para ajudá-lo a decidir qual tecnologia será usada.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 11/28/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: f66a2699b6d29f10633b4853801240f0590ff918
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65147634"
---
# <a name="deciding-when-to-use-azure-blobs-azure-files-or-azure-disks"></a>Decidindo quando usar Blobs do Azure, Arquivos do Azure ou Discos do Azure
O Microsoft Azure fornece vários recursos no Armazenamento do Azure para armazenar e acessar seus dados na nuvem. Este artigo aborda os Arquivos, Blobs e Discos do Azure e foi elaborado para ajudá-lo a escolher entre esses recursos.

## <a name="scenarios"></a>Cenários
A tabela a seguir compara os Arquivos, Blobs e Discos, e mostra cenários de exemplo apropriados para cada um.

| Recurso | DESCRIÇÃO | Quando usar |
|--------------|-------------|-------------|
| **Arquivos do Azure** | Fornece uma interface SMB, bibliotecas de clientes e uma [interface REST](/rest/api/storageservices/file-service-rest-api) que permite o acesso aos arquivos armazenados em qualquer lugar. | Você deseja migrar por lift-and-shift um aplicativo para a nuvem que já usa as APIs do sistema de arquivos nativo para compartilhar dados entre ele e outros aplicativos em execução no Azure.<br/><br/>Você deseja armazenar ferramentas de desenvolvimento e depuração que precisam ser acessadas em várias máquinas virtuais. |
| **Blobs do Azure** | Fornece bibliotecas de clientes e uma [interface REST](/rest/api/storageservices/blob-service-rest-api) que permite que os dados não estruturados sejam armazenados e acessados em grande escala em blobs de blocos.<br/><br/>Também dá suporte ao [Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) para soluções de análise de big data do enterprise. | Você deseja que o aplicativo dê suporte a cenários de streaming e de acesso aleatório.<br/><br/>Você deseja poder acessar dados do aplicativo em qualquer lugar.<br/><br/>Você deseja criar um lago de dados empresariais no Azure e executar análise de big data. |
| **Discos do Azure** | Fornece bibliotecas de clientes e uma [interface REST](/rest/api/compute/manageddisks/disks/disks-rest-api) que permite que os dados sejam armazenados de forma persistente e acessados em um disco rígido virtual anexado. | Você deseja migrar por lift-and-shift aplicativos que usam APIs do sistema de arquivos nativo para ler e gravar dados em discos persistentes.<br/><br/>Você deseja armazenar os dados que não precisam ser acessados fora da máquina virtual à qual o disco está anexado. |

## <a name="comparison-files-and-blobs"></a>Comparação: Arquivos e Blobs
A tabela a seguir compara os Arquivos do Azure com os Blobs do Azure.  
  
||||  
|-|-|-|  
|**Atributo**|**Blobs do Azure**|**Arquivos do Azure**|  
|Opções de durabilidade|LRS, ZRS, GRS, RA-GRS|LRS, ZRS, GRS|  
|Acessibilidade|APIs REST|APIs REST<br /><br /> SMB 2.1 e SMB 3.0 (APIs do sistema de arquivos padrão)|  
|Conectividade|APIs REST – No mundo todo|APIs REST – No mundo todo<br /><br /> SMB 2.1 – Na região<br /><br /> SMB 3.0 – No mundo todo|  
|Pontos de extremidade|`http://myaccount.blob.core.windows.net/mycontainer/myblob`|`\\myaccount.file.core.windows.net\myshare\myfile.txt`<br /><br /> `http://myaccount.file.core.windows.net/myshare/myfile.txt`|  
|Diretórios|Namespace simples|Objetos do diretório verdadeiros|  
|Diferenciação entre maiúsculas e minúsculas de nomes|Diferencia maiúsculas de minúsculas|Sem diferenciação entre maiúsculas e minúsculas, mas com preservação de maiúsculas e minúsculas|  
|Capacity|Limite de conta do PiB até 2 |Compartilhamentos de arquivos de 5 TiB|  
|Produtividade|Até 60 MiB/s por blob de blocos|Até 60 MiB/s por compartilhamento|  
|Tamanho do objeto|Até aproximadamente 4,75 TiB por blob de blocos|Até 1 TiB por arquivo|  
|Capacidade cobrada|Com base nos bytes gravados|Com base no tamanho do arquivo|  
|Bibliotecas de cliente|Vários idiomas|Vários idiomas|  
  
## <a name="comparison-files-and-disks"></a>Comparação: Arquivos e Discos
Os Arquivos do Azure complementam os Discos do Azure. Um disco pode ser anexado somente a uma Máquina Virtual do Azure por vez. Os discos são VHDs de formato fixo armazenados como blobs de páginas no Armazenamento do Azure e são usados pela máquina virtual para armazenar dados duráveis. Os compartilhamentos de arquivos nos Arquivos do Azure podem ser acessados da mesma forma como o disco local é acessado (usando APIs do sistema de arquivos nativo) e podem ser compartilhados entre várias máquinas virtuais.  
 
A tabela a seguir compara os Arquivos do Azure com os Discos do Azure.  
 
||||  
|-|-|-|  
|**Atributo**|**Discos do Azure**|**Arquivos do Azure**|  
|Scope|Exclusivo a uma única máquina virtual|Acesso compartilhado entre várias máquinas virtuais|  
|Instantâneos e cópia|Sim|Sim|  
|Configuração|Conectado na inicialização da máquina virtual|Conectado após a inicialização da máquina virtual|  
|Authentication|Interno|Configurar com net use|  
|Acesso com a REST|Não é possível acessar os arquivos no VHD|É possível acessar os arquivos armazenados em um compartilhamento|  
|Tamanho máx.|Disco de 4 TiB|Compartilhamento de Arquivos de 5 TiB e arquivo de 1 TiB no compartilhamento|  
|IOps máxima|500 IOPS|1.000 IOPS|  
|Produtividade|Até 60 MiB/s por disco|O destino é 60 MiB/s por Compartilhamento do Arquivo (pode ficar maior para tamanhos de E/S maiores)|  

## <a name="next-steps"></a>Próximas etapas
Ao tomar decisões sobre como os dados são armazenados e acessados, você também deve considerar os custos envolvidos. Para obter mais informações, consulte [Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).
  
Alguns recursos do SMB não são aplicáveis à nuvem. Para obter mais informações, consulte [Recursos sem suporte no serviço Arquivo do Azure](/rest/api/storageservices/features-not-supported-by-the-azure-file-service).
  
Para saber mais sobre discos, confira [Introdução aos discos gerenciados](../../virtual-machines/windows/managed-disks-overview.md) e [Como anexar um disco de dados a uma máquina virtual Windows](../../virtual-machines/windows/attach-managed-disk-portal.md).
