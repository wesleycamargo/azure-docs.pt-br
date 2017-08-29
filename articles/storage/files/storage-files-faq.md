---
title: Perguntas frequentes sobre o armazenamento de Arquivos do Azure | Microsoft Docs
description: Encontre respostas para perguntas frequentes sobre o armazenamento de Arquivos do Azure.
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
ms.date: 07/19/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 17868591e1056ff2bddde0e082695af529f58f02
ms.contentlocale: pt-br
ms.lasthandoff: 08/21/2017

---
# <a name="frequently-asked-questions-about-azure-file-storage"></a>Perguntas frequentes sobre o armazenamento de Arquivos do Azure

## <a name="general"></a>Geral
* **P. O que é o armazenamento de Arquivos do Azure?**  
   
    O armazenamento de Arquivos do Azure é um sistema de arquivos distribuído no Azure. Ele fornece uma interface do protocolo SMB que permite aos usuários montar o armazenamento como um compartilhamento nativo em uma Máquina Virtual do Azure ou em um computador local com suporte.

* **P. Por que o armazenamento de Arquivos do Azure é útil?**  
   
    O armazenamento de Arquivos do Azure fornece o acesso a dados compartilhados em várias VMs e plataformas. Consulte [Por que o armazenamento de Arquivos do Azure é útil](storage-files-introduction.md#why-azure-file-storage-is-useful).

* **P. Quando devo usar o Arquivo do Azure vs. Blob do Azure vs. Disco do Azure?**  
   
    O Microsoft Azure fornece várias maneiras de armazenar e acessar dados na nuvem.  
   
    Armazenamento de Arquivos do Azure – fornece uma interface SMB, bibliotecas de cliente e uma interface REST que permite o fácil acesso aos arquivos armazenados em qualquer lugar.  
   
    Blobs do Azure – fornecem bibliotecas de cliente e uma interface REST que permite que os dados não estruturados sejam armazenados e acessados em grande escala em blobs de blocos.  
   
    Discos de Dados do Azure – fornecem bibliotecas de cliente e uma interface REST que permite que os dados sejam armazenados de forma persistente e acessados em um disco rígido virtual anexado.  
   
    Saiba mais em [Decidindo quando usar Blobs do Azure, Arquivos do Azure ou Discos de Dados do Azure](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

* **P. Como fazer para começar a usar o armazenamento de Arquivos do Azure?**  
   
    Comece criando um compartilhamento de arquivos do Azure. É possível criar compartilhamentos de Arquivos do Azure usando o Portal do Azure, cmdlets do PowerShell do Armazenamento do Azure, bibliotecas de cliente do Armazenamento do Azure ou a API REST do Armazenamento do Azure. Neste tutorial, você aprenderá a:

    * [Saiba como criar um compartilhamento de Arquivos do Azure usando o Portal](storage-how-to-create-file-share.md#create-file-share-through-the-portal)
    * [Saiba como criar um compartilhamento de Arquivos do Azure usando o PowerShell](storage-how-to-create-file-share.md#create-file-share-through-powershell)
    * [Saiba como criar um compartilhamento de Arquivos do Azure usando a CLI](storage-how-to-create-file-share.md#create-file-share-through-command-line-interface-cli)

* **P. Para quais replicações há suporte no armazenamento de Arquivos do Azure?**  
   
    No momento, o armazenamento de Arquivos do Azure dá suporte apenas ao LRS ou GRS. Planejamos dar suporte a RA-GRS, mas ainda não há nenhum cronograma para compartilhar.

## <a name="security-authentication-and-access-control"></a>Segurança, autenticação e controle de acesso

* **P. Quais são as diferentes maneiras de acessar arquivos no armazenamento de Arquivos do Azure?**
    
    Você pode montar o compartilhamento de arquivos no computador local usando o protocolo SMB 3.0 ou usar ferramentas como o [Gerenciador de Armazenamento](http://storageexplorer.com/) para acessar os arquivos no compartilhamento de arquivos. No aplicativo, você pode usar bibliotecas de cliente de armazenamento, APIs REST ou o PowerShell para acessar os arquivos no compartilhamento de arquivos do Azure.

* **P. Como posso fornecer acesso a um arquivo específico usando um navegador da Web?**
    
    Com a SAS, você pode gerar tokens com permissões específicas que são válidas por um intervalo de tempo especificado. Por exemplo, você pode gerar um token de acesso somente leitura a um arquivo específico para um período de tempo específico. Qualquer pessoa que possui essa URL pode acessar o arquivo diretamente em qualquer navegador da Web enquanto ele é válido. Chaves SAS podem ser facilmente geradas na interface do usuário como o Gerenciador de armazenamento.

* **P. É possível especificar permissões somente leitura ou somente gravação em pastas no compartilhamento?**
    
    Você não terá esse nível de controle sobre as permissões se montar o compartilhamento de arquivos via SMB. No entanto, você pode conseguir isso criando uma SAS (assinatura de acesso compartilhado) por meio de bibliotecas de API REST ou de cliente.  

* **P. Como posso habilitar a criptografia do Servidor no armazenamento de Arquivos do Azure?**

    A [Criptografia do Servidor](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) do armazenamento de Arquivos do Azure está disponível em todas as regiões e nuvens públicas e nacionais. Habilite o SSE no armazenamento de Arquivos do Azure usando o [portal do Azure](https://portal.azure.com/), a [API do Provedor de Recursos do Armazenamento do Microsoft Azure](/rest/api/storagerp/storageaccounts), o [Azure PowerShell](https://msdn.microsoft.com/library/azure/mt607151.aspx) ou a [CLI do Azure](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
    
    Depois de habilitar o SSE no armazenamento de Arquivos do Azure, todos os novos dados gravados no armazenamento de arquivos na conta de armazenamento serão criptografados automaticamente. Esse recurso está disponível para todos os novos dados gravados em compartilhamentos novos ou existentes em uma conta de armazenamento nova ou existente. Não há nenhum custo adicional para habilitar esse recurso. Saiba mais sobre [como habilitar o SSE no armazenamento de Arquivos do Azure](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* **P. Há suporte para a autenticação baseada no Active Directory no armazenamento de Arquivos do Azure?**
   
    Atualmente não damos suporte a autenticação baseada no AD ou em ACLs, mas a temos em nossa lista de solicitações de recursos. Por enquanto, as chaves de conta de armazenamento do Azure são usadas para permitir a autenticação de compartilhamento de arquivos. Oferecemos uma solução alternativa usando SAS (assinaturas de acesso compartilhado) pela API REST ou pelas bibliotecas de cliente. Com a SAS, você pode gerar tokens com permissões específicas que são válidas por um intervalo de tempo especificado. Por exemplo, você pode gerar um token com acesso somente leitura a determinado arquivo com validade de 10 minutos. Qualquer pessoa que possui esse token enquanto ele é válido tem acesso somente leitura ao arquivo por 10 minutos.
   
    A SAS tem suporte somente via API REST ou bibliotecas de cliente. Quando você montar o compartilhamento de arquivos por meio do protocolo SMB, não poderá usar uma SAS para delegar o acesso ao seu conteúdo. 
    
* **P. Quais são as políticas de conformidade de dados com suporte no armazenamento de Arquivos do Azure?**

   O armazenamento de Arquivos do Azure é executado com base na mesma arquitetura de armazenamento de outros serviços de armazenamento no Armazenamento do Azure e aplica as mesmas políticas de conformidade de dados. Para obter mais informações sobre a conformidade de dados do Armazenamento do Azure, baixe e consulte o [documento Proteção de dados do Microsoft Azure](http://go.microsoft.com/fwlink/?LinkID=398382&clcid=0x409).

## <a name="on-premises-access"></a>Acesso local

* **P. É necessário usar o Azure ExpressRoute para se conectar ao armazenamento de Arquivos do Azure por meio de uma VM local?**
   
    Nº Se você não tiver um ExpressRoute, ainda poderá acessar o compartilhamento de arquivos no local, desde que a porta 445 (TCP de saída) esteja aberta para acesso à Internet. No entanto, se desejar, você pode usar o ExpressRoute com o armazenamento de Arquivos do Azure.

* **P. Como posso montar o compartilhamento de Arquivos do Azure no computador local?** 
    
    Você pode montar o compartilhamento de arquivos por meio do protocolo SMB, desde que a porta 445 (TCP de Saída) esteja aberta e o cliente dê suporte ao protocolo SMB 3.0 (por exemplo, você está usando o Windows 10 ou o Windows Server 2012). Trabalhe com seu provedor ISP local para desbloquear a porta. Enquanto isso, exiba os arquivos usando o [Gerenciador de Armazenamento](../../vs-azure-tools-storage-explorer-files.md#view-a-file-shares-contents).


## <a name="billing-and-pricing"></a>Cobrança e preços
* **P. O tráfego de rede entre uma VM do Azure e uma contagem de compartilhamento de arquivos conta como largura de banda externa cobrada na assinatura?**
   
    Se o compartilhamento de arquivos e a VM estiverem na mesma região do Azure, o tráfego entre eles será gratuito. Se eles estiverem em regiões diferentes, o tráfego entre eles será cobrado como largura de banda externa.

## <a name="backup"></a>Backup

* **P. Como fazer backup de meu Compartilhamento de Arquivos do Azure?**
    
    Use o AzCopy, o RoboCopy ou uma ferramenta de backup de terceiros que pode fazer backup de um compartilhamento de arquivos montado. Esperamos ter a capacidade de tirar instantâneos de compartilhamentos de Arquivos em um futuro próximo; você poderá usar esse recurso para fazer backup do compartilhamento de Arquivos do Azure.

## <a name="performance"></a>Desempenho

* **P. Quais são os limites de escala do armazenamento de Arquivos do Azure?**
    Para obter informações sobre as metas de escalabilidade e desempenho do armazenamento de Arquivos do Azure, consulte [Metas de escalabilidade e desempenho do Armazenamento do Azure](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#scalability-targets-for-blobs-queues-tables-and-files).

* **P. O desempenho era lento durante a tentativa de descompactar arquivos no armazenamento de Arquivos do Azure. O que devo fazer?**
    
    Para transferir grandes quantidades de arquivos para o armazenamento de Arquivos do Azure, recomendamos o uso do AzCopy (Windows, Versão Prévia para Linux/Unix) ou do Azure PowerShell, já que essas ferramentas foram otimizadas para transferência de rede.

* **P. Quais patches foram liberados para corrigir o problema de lentidão no desempenho com o armazenamento de Arquivos do Azure?**
    
    A equipe do Windows liberou recentemente um patch para corrigir um problema de lentidão no desempenho quando o cliente acessa o armazenamento de Arquivos do Azure no Windows 8.1 ou no Windows Server 2012 R2. Para obter mais informações, confira o artigo da base de dados associado, [Lentidão no desempenho ao acessar o armazenamento de Arquivos do Azure no Windows 8.1 ou no Windows Server 2012 R2](https://support.microsoft.com/kb/3114025).

## <a name="features-and-interoperability-with-other-services"></a>Recursos e interoperabilidade com outros serviços
* **P. Uma “Testemunha de Compartilhamento de Arquivos” de um cluster de failover é um dos casos de uso do armazenamento de Arquivos do Azure?**
   
    Atualmente, não há suporte para esse recurso.

* **P. Há uma operação de renomeação na API REST?**
   
    Não no momento.

* **P. É possível ter compartilhamentos aninhados ou seja, um compartilhamento em outro compartilhamento?**
    
    Nº O compartilhamento de arquivos é o driver virtual que você pode montar; portanto, não há suporte para compartilhamentos aninhados.

* **P. Como usar o armazenamento de Arquivos do Azure com o IBM MQ**
    
    A IBM liberou um documento para orientar os clientes do IBM MQ durante a configuração do armazenamento de Arquivos do Azure com seu serviço. Para saber mais, confira [Como configurar o gerenciador de filas com várias instâncias do IBM MQ com o Serviço de Arquivos do Microsoft Azure](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service).


## <a name="troubleshooting"></a>Solucionar problemas
* **P. Como fazer para solucionar erros do armazenamento de Arquivos do Azure?**
    
    Consulte o [artigo Solução de problemas do armazenamento de Arquivos do Azure](storage-troubleshoot-windows-file-connection-problems.md) para obter diretrizes completas de solução de problemas. 


## <a name="see-also"></a>Consulte também
Consulte estes links para obter mais informações sobre o armazenamento de arquivo do Azure.

### <a name="conceptual-articles-and-videos"></a>Artigos e vídeos conceituais
* [Armazenamento de Arquivos do Azure: um sistema de arquivos SMB de nuvem ininterrupto para Windows e Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [Como utilizar o Armazenamento de Arquivos do Azure com Linux](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>Suporte de ferramentas para o armazenamento de arquivos
* [Como usar o AzCopy com o Armazenamento do Microsoft Azure](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Usando a CLI do Azure com o Armazenamento do Azure](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Solução de problemas do armazenamento de Arquivos do Azure](storage-troubleshoot-linux-file-connection-problems.md)

### <a name="blog-posts"></a>Postagens no blog
* [O Armazenamento de arquivos do Azure agora está disponível ao público geral](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Por dentro do Armazenamento de Arquivos do Azure](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Apresentando o serviço de arquivo do Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Migrando dados para o armazenamento de Arquivos do Azure](https://azure.microsoft.com/blog/migrating-data-to-microsoft-azure-files/)

### <a name="reference"></a>Referência
* [Referência à Biblioteca de Cliente de Armazenamento para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [Referência à API REST do serviço de arquivos](http://msdn.microsoft.com/library/azure/dn167006.aspx)

