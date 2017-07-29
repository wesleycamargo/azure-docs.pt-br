---
title: "Introdução ao armazenamento de Arquivos do Azure | Microsoft Docs"
description: "Uma visão geral do armazenamento de Arquivos do Azure, um serviço que permite criar e usar compartilhamentos de arquivos de rede no Microsoft Cloud usando o padrão do setor."
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/27/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: 0425da20f3f0abcfa3ed5c04cec32184210546bb
ms.openlocfilehash: 16fdd3aafef1a50554932a0e7843d347182c9b6a
ms.contentlocale: pt-br
ms.lasthandoff: 07/20/2017

---

# <a name="introduction-to-azure-file-storage"></a>Introdução ao armazenamento de Arquivos do Azure
O armazenamento de Arquivos do Azure oferece compartilhamentos de arquivos de rede na nuvem usando o padrão do setor [Protocolo SMB](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) e [Samba/Common Internet File System (CIFS)](https://technet.microsoft.com/library/cc939973.aspx). Os compartilhamentos de Arquivos do Azure podem ser montados simultaneamente por clientes como implantações locais do Windows, macOS, Linux ou por Máquinas Virtuais do Azure. Uma conta de armazenamento de uso geral fornece a você acesso ao Armazenamento de Arquivos do Azure e a outros erviços como Blobs, discos de máquina virtual do Azure e Filas em uma única conta.



## <a name="videos"></a>Vídeos
| Introdução ao armazenamento de Arquivos do Azure (27m) | Tutorial do armazenamento de Arquivos do Azure (5 minutos)  |
|-|-|
| [![Captura de tela do vídeo Introdução ao armazenamento de Arquivos do Azure - clique aqui para reproduzir!](media/storage-file-storage/azure-files-introduction-video-snapshot1.png)](https://www.youtube.com/watch?v=zlrpomv5RLs) | [![Captura de tela do Tutorial armazenamento de Arquivos do Azure - clique aqui para reproduzir!](media/storage-file-storage/azure-files-introduction-video-snapshot2.png)](https://channel9.msdn.com/Blogs/Azure/Azure-File-storage-with-Windows/) |

## <a name="why-azure-file-storage-is-useful"></a>Por que o armazenamento de Arquivos do Azure é útil
O armazenamento de Arquivos do Azure permite substituir servidores do Windows Server, Linux ou de arquivos baseados em NAS hospedados no local ou na nuvem por um compartilhamento do arquivo de nuvem sem sistema operacional. Ele oferece as seguintes vantagens:

* **Acesso compartilhado:**. Os compartilhamentos de Arquivos do Azure suportam o protocolo SMB padrão, ou seja, você pode substituir perfeitamente seus compartilhamentos de arquivos locais pelos compartilhamentos de Arquivos do Azure sem se preocupar com a compatibilidade dos aplicativos. Poder compartilhar um sistema de arquivos em vários computadores, aplicativos e instâncias é uma vantagem significativa com o armazenamento de Arquivos do Azure para aplicativos que precisam de compartilhamento. 
* **Totalmente Gerenciado**. Os compartilhamentos de Arquivos do Azure podem ser criados sem a necessidade de gerenciar um sistema operacional ou hardware. Isso significa que você não precisa lidar com a correção do sistema operacional do servidor com atualizações críticas de segurança ou com a substituição de discos rígidos com defeito.
* **Scripts e Ferramentas**. Os cmdlets do PowerShell e a CLI do Azure podem ser usados para criar, montar e gerenciar compartilhamentos do armazenamento de arquivos como parte da administração dos aplicativos do Azure. Você pode criar e gerenciar compartilhamentos de Arquivos do Azure usando o Portal do Azure e o Gerenciador de armazenamento do Azure. 
* **Resiliência**. O armazenamento de Arquivos do Azure foi criado do zero para estar sempre disponível. Substituir os compartilhamentos de arquivos locais pelo armazenamento de Arquivos do Azure significa que não é preciso estar ativado para lidar com interrupções locais de energia ou problemas de rede. 
* **Programação Familiar**. Os aplicativos executados no Azure podem acessar dados no compartilhamento por meio de [APIs de E/S do sistema](https://msdn.microsoft.com/library/system.io.file.aspx) de arquivos. Os desenvolvedores podem, portanto, utilizar seus códigos e habilidades existentes para migrar aplicativos existentes. Além das APIs de E/S do sistema, você pode usar as [Bibliotecas do Cliente de Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dn261237.aspx) ou a [API de REST do Armazenamento do Azure](/rest/api/storageservices/file-service-rest-api).

Os compartilhamentos de Arquivos do Azure podem ser usados para:

* **Substituir servidores de arquivos no local**:  
    O armazenamento de Arquivos do Azure pode ser usado para substituir completamente os compartilhamentos de arquivos em servidores de arquivos tradicionais no local ou em dispositivos NAS. Sistemas operacionais conhecidos, como o Linux, Windows e macOS podem facilmente montar um compartilhamento de Arquivos do Azure em qualquer lugar do mundo.

* **Aplicativos de "Deslocamento e comparação"**:  
    O armazenamento de Arquivos do Azure facilita a migração de aplicativos para a nuvem que utiliza os compartilhamentos de arquivos locais para compartilhar dados entre partes do aplicativo. Para que isso aconteça, cada VM conecta-se ao compartilhamento de arquivos e, em seguida, pode ler e gravar arquivos, exatamente como faria no caso de um compartilhamento de arquivos local.

* **Simplificar o Desenvolvimento na Nuvem**:  
    O armazenamento de Arquivos do Azure pode ser usado de diversas maneiras para simplificar novos projetos de desenvolvimento da nuvem.
    * **Configurações de Aplicativo Compartilhado**:  
        Um padrão comum para aplicativos distribuídos é fazer com que os arquivos de configuração fiquem em um local centralizado, de onde possam ser acessados por diversas máquinas virtuais diferentes. Esses arquivos de configuração podem ser armazenados em um compartilhamento de Arquivos do Azure e lido por todas as instâncias de aplicativo. Essas configurações também podem ser gerenciadas por meio da interface REST, que permite acesso global aos arquivos de configuração.

    * **Compartilhamento de Diagnóstico**:  
        Um compartilhamento de Arquivos do Azure também pode ser usado para salvar arquivos de diagnóstico como logs, métricas e despejos. Ter esses arquivos disponíveis tanto pela interface REST quanto pelo SMB permite que os aplicativos utilizem uma variedade de ferramentas de análise para processar e analisar os dados de diagnóstico.

    * **Desenv/Teste/Depuração**:  
        Quando desenvolvedores ou administradores estão trabalhando em máquinas virtuais na nuvem, eles frequentemente precisam de um conjunto de ferramentas ou utilitários. A instalação e a distribuição desses utilitários em cada máquina virtual onde eles são necessários pode demorar muito. Com o armazenamento de Arquivos do Azure, um desenvolvedor ou administrador pode armazenar suas ferramentas favoritas em um compartilhamento de arquivos e conectar-se a elas a partir de qualquer máquina virtual.
        
## <a name="how-does-it-work"></a>Como ele funciona?
Gerenciar compartilhamentos de Arquivos do Azure é muito mais simples do que gerenciar compartilhamentos de arquivos locais. O diagrama a seguir ilustra as construções do gerenciamento de armazenamento de Arquivos do Azure:

![Estrutura do Arquivo](../../includes/media/storage-file-concepts-include/files-concepts.png)

* **Conta de Armazenamento**: todo o acesso ao Armazenamento do Azure é feito através de uma conta de armazenamento. Confira Escalabilidade e Metas de Desempenho do Armazenamento do Azure para obter detalhes sobre a capacidade da conta de armazenamento.
* **Compartilhamento** : um compartilhamento do armazenamento de Arquivos é um compartilhamento de arquivos SMB no Azure. Todos os arquivos e diretórios devem ser criados em um compartilhamento pai. Uma conta de armazenamento pode conter um número ilimitado de compartilhamentos e um compartilhamento pode armazenar um número ilimitado de arquivos, até a capacidade total de 5 TB do armazenamento de arquivos.
* **Diretório**: uma hierarquia opcional de diretórios.
* **Arquivo**: um arquivo no compartilhamento. Um arquivo pode ter até 1 TB de tamanho.
* **Formato de URL**: arquivos são endereçáveis usando o seguinte formato de URL:  

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory/directories>/<file>
    ```
## <a name="next-steps"></a>Próximas etapas
* [Criar um Compartilhamento de Arquivos do Azure](storage-file-how-to-create-file-share.md)
* [Conexão e Montagem no Windows](storage-file-how-to-use-files-windows.md)
* [Conexão e Montagem no Linux](storage-how-to-use-files-linux.md)
* [Conexão e Montagem no MacOS](storage-file-how-to-use-files-mac.md)
* [Perguntas frequentes](storage-files-faq.md)
* [Solução de problemas](storage-troubleshoot-file-connection-problems.md)

### <a name="conceptual-articles-and-videos"></a>Artigos e vídeos conceituais
* [Armazenamento de Arquivos do Azure: um sistema de arquivos SMB de nuvem ininterrupto para Windows e Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)

### <a name="tooling-support-for-azure-file-storage"></a>Suporte de ferramentas para o armazenamento de Arquivos do Azure
* [Usando o PowerShell do Azure com o Armazenamento do Azure](storage-powershell-guide-full.md)
* [Como usar o AzCopy com o Armazenamento do Microsoft Azure](storage-use-azcopy.md)
* [Usando a CLI do Azure com o Armazenamento do Azure](storage-azure-cli.md#create-and-manage-file-shares)

### <a name="blog-posts"></a>Postagens no blog
* [O Armazenamento de arquivos do Azure agora está disponível ao público geral](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Por dentro do Armazenamento de Arquivos do Azure](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Apresentando o serviço de arquivo do Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Como migrar dados para o arquivo do Azure ](https://azure.microsoft.com/blog/migrating-data-to-microsoft-azure-files/)

### <a name="reference"></a>Referência
* [referência à Biblioteca de Cliente de armazenamento para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [Referência à API REST do serviço de arquivos](http://msdn.microsoft.com/library/azure/dn167006.aspx)

