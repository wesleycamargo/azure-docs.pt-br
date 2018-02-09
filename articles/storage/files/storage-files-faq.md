---
title: Perguntas frequentes sobre o Arquivos do Azure | Microsoft Docs
description: Encontre respostas para perguntas frequentes sobre o Arquivos do Azure.
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
ms.date: 12/04/2017
ms.author: renash
ms.openlocfilehash: 8762b2cca03f4c95f7543803a024bff4573927a1
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="frequently-asked-questions-about-azure-files"></a>Perguntas frequentes sobre o Arquivos do Azure
O serviço [Arquivos do Azure](storage-files-introduction.md) oferece compartilhamentos de arquivos totalmente gerenciados na nuvem, acessíveis por meio do protocolo [SMB (Service Message Block)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) padrão do setor (também conhecido como CIFS ou Common Internet File System). Você pode montar compartilhamentos de arquivos do Azure simultaneamente em implantações locais ou na nuvem do Windows, do Linux e do macOS. Você também pode armazenar em cache os compartilhamentos de arquivos do Azure nos computadores Windows Server usando a Sincronização de Arquivos do Azure (versão prévia) para acesso rápido perto de onde os dados são usados.

Este artigo responde perguntas frequentes sobre funcionalidades e recursos do serviço Arquivos do Azure, inclusive sobre o uso dele em combinação com a Sincronização de Arquivos do Azure. Se você não vir a resposta para sua pergunta aqui, poderá entrar em contato conosco pelos seguintes canais (em ordem progressiva):

1. A seção de comentários deste artigo.
2. O [Fórum do Armazenamento do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
3. O [UserVoice do Arquivos do Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. O Suporte da Microsoft. Para criar uma nova solicitação de suporte, no Portal do Azure, na guia **Ajuda**, selecione o botão **Ajuda + suporte** e, em seguida, selecione **Nova solicitação de suporte**.

## <a name="general"></a>Geral
* <a id="why-files-useful"></a>
**Por que o serviço Arquivos do Azure é útil?**  
   Você pode usar o serviço Arquivos do Azure para criar compartilhamentos de arquivos na nuvem sem ser responsável por gerenciar a sobrecarga de um servidor físico ou dispositivo. Nós fazemos o trabalho monótono para você, incluindo a aplicação de atualizações do sistema operacional e a substituição de discos defeituosos. Para saber mais sobre os cenários em que os Arquivos do Azure podem ajudar, confira [Por que o serviço Arquivos do Azure é útil](storage-files-introduction.md#why-azure-files-is-useful).

* <a id="file-access-options"></a>
**Quais são as diferentes maneiras de acessar arquivos nos Arquivos do Azure?**  
    Você pode montar o compartilhamento de arquivos no computador local usando o protocolo SMB 3.0 ou pode usar ferramentas como o [Gerenciador de Armazenamento](http://storageexplorer.com/) para acessar os arquivos no compartilhamento de arquivos. No aplicativo, você pode usar bibliotecas de cliente de armazenamento, APIs REST, o PowerShell ou a CLI do Azure para acessar os arquivos no compartilhamento de arquivos do Azure.

* <a id="what-is-afs"></a>
**O que é a Sincronização de arquivos do Azure?**  
    Você pode usar a Sincronização de Arquivos do Azure para centralizar os compartilhamentos de arquivos da sua organização no serviço Arquivos do Azure sem abrir mão da flexibilidade, do desempenho e da compatibilidade de um servidor de arquivos local. A Sincronização de Arquivos do Azure transforma computadores Windows Server em um cache rápido do seu compartilhamento de arquivos do Azure. Você pode usar qualquer protocolo disponível no Windows Server para acessar seus dados localmente, incluindo o SMB, o NFS (sistema de arquivos de rede) e o FTPS (serviço de protocolo de transferência de arquivos). Você pode ter tantos caches quantos precisar em todo o mundo.

* <a id="files-versus-blobs"></a>
**Qual é a comparação entre usar um compartilhamento de arquivos do Azure e o Armazenamento de Blobs do Azure para meus dados?**  
    Tanto o serviço Arquivos do Azure quanto o Armazenamento de Blobs do Azure fornecem uma maneira de armazenar grandes quantidades de dados na nuvem, mas são úteis para fins ligeiramente diferentes. 
    
    O Armazenamento de Blobs do Azure é útil para aplicativos de grande escala e nativos de nuvem que precisam armazenar dados não estruturados. Para maximizar o desempenho e o dimensionamento, o Armazenamento de Blobs do Azure é uma abstração de armazenamento mais simples do que um sistema de arquivos real. O Armazenamento de Blobs do Azure só pode ser acessado por bibliotecas de cliente baseadas em REST (ou diretamente por meio do protocolo baseado em REST).

    O serviço Arquivos do Azure é especificamente um sistema de arquivos. O serviço Arquivos do Azure tem todos os resumos de arquivo que você conhece e adora após anos de trabalho com sistemas operacionais locais. Assim como o Armazenamento de Blobs do Azure, o serviço Arquivos do Azure oferece uma interface REST e bibliotecas de cliente baseadas em REST. Ao contrário do Armazenamento de Blobs do Azure, o serviço Arquivos do Azure oferece acesso via SMB a compartilhamentos de arquivos do Azure. Usando SMB, você pode montar um compartilhamento dos Arquivos do Azure diretamente no Windows, no Linux ou no macOS, localmente ou em VMs na nuvem, sem escrever nenhum código nem anexar drivers especiais ao sistema de arquivos. Você também pode armazenar em cache os compartilhamentos de arquivos do Azure em servidores de arquivos locais usando a Sincronização de Arquivos do Azure para acesso rápido, perto de onde os dados são usados. 
   
    Para uma descrição mais detalhada sobre as diferenças entre os Arquivos do Azure e o Armazenamento de Blobs do Azure, confira [Decidindo quando usar o Armazenamento de Blobs do Azure, os Arquivos do Azure ou Discos do Azure](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Para saber mais sobre o Armazenamento de Blobs do Azure, confira [Introdução ao armazenamento de Blobs](../blobs/storage-blobs-introduction.md).

* <a id="files-versus-disks"></a>**Por que usar um compartilhamento de arquivos do Azure em vez dos Discos do Azure?**  
    Um disco nos Discos do Azure é simplesmente um disco. Um disco autônomo por si só não é muito útil. Para obter valor do serviço Discos do Azure, você deve anexar um disco a uma máquina virtual que está em execução no Azure. O serviço Discos do Azure pode ser usado para todos os fins para os quais você usaria um disco em um servidor local. Você pode usá-lo como um disco de sistema do SO, como espaço de permuta para um SO ou como armazenamento dedicado para um aplicativo. Um uso interessante do serviço Discos do Azure é a criação de um servidor de arquivos na nuvem para uso nos mesmos locais em que você usaria um compartilhamento de arquivos do Azure. Implantar um servidor de arquivos em Máquinas Virtuais do Azure é uma maneira fantástica e de alto desempenho de obter o armazenamento de arquivos no Azure quando você precisa de opções de implantação que atualmente não têm suporte pelo serviço Arquivos do Azure (como suporte a protocolo NFS ou armazenamento Premium). 

    No entanto, executar um servidor de arquivos com os Discos do Azure como armazenamento de back-end geralmente é muito mais caro do que usar um compartilhamento de arquivos do Azure, por vários motivos. Primeiro, além de pagar pelo armazenamento em disco, você também precisa pagar a despesa de execução de uma ou mais VMs do Azure. Em segundo lugar, você também deve gerenciar as VMs que são usadas para executar o servidor de arquivos. Por exemplo, você é responsável por atualizações do SO. Por fim, se precisar que dados sejam armazenados em cache localmente, caberá a você configurar e gerenciar as tecnologias de replicação, por exemplo, a DFSR (Replicação do Sistema de Arquivos Distribuído) para fazer com que isso aconteça.

    Uma abordagem para obter o melhor tanto do serviço Arquivos do Azure quando de um servidor de arquivos hospedado em Máquinas Virtuais do Azure (além de usar o serviço Discos do Azure como armazenamento de back-end) é instalar a Sincronização de Arquivos do Azure no servidor de arquivos hospedado em uma VM na nuvem. Se o compartilhamento dos Arquivos do Azure está na mesma região que o servidor de arquivos, você pode habilitar a disposição em camadas na nuvem e definir o percentual do volume de espaço livre para o máximo (99%). Isso garante o mínimo de duplicação de dados. Você também pode usar quaisquer aplicativos que desejar com seus servidores de arquivo, assim como aplicativos que exigem suporte a protocolo NFS.

    Para obter informações sobre uma opção para definição de um servidor de arquivos de alto desempenho e alta disponibilidade no Azure, confira [Implantando clusters convidados em VMs IaaS no Microsoft Azure](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/). Para uma descrição mais detalhada das diferenças entre os Arquivos do Azure e os Discos do Azure, confira [Decidindo quando usar o Armazenamento de Blobs do Azure, os Arquivos do Azure ou Discos do Azure](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Para saber mais sobre os Discos do Azure, confira [Visão geral do Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md).

* <a id="get-started"></a>
**Como posso começar a usar os Arquivos do Azure?**  
   É fácil começar a usar o serviço Arquivos do Azure. Primeiro, [crie um compartilhamento de arquivos](storage-how-to-create-file-share.md) e, em seguida, monte-o no seu sistema operacional preferido: 

    * [Montar no Windows](storage-how-to-use-files-windows.md)
    * [Montar no Linux](storage-how-to-use-files-linux.md)
    * [Montar no macOS](storage-how-to-use-files-mac.md)

   Para obter um guia mais detalhado sobre como implantar um compartilhamento dos Arquivos do Azure para substituir os compartilhamentos de arquivos de produção em sua organização, confira [Planejando a implantação dos Arquivos do Azure](storage-files-planning.md).

* <a id="redundancy-options"></a>
**Quais opções de redundância de armazenamento têm suporte pelos Arquivos do Azure?**  
    Atualmente, os Arquivos do Azure fornece para LRS (armazenamento com redundância local), ZRS (armazenamento com redundância de zona) e GRS (armazenamento com redundância geográfica). Planejamos dar suporte para RA-GRS (redundância geográfica com acesso de leitura) no futuro, mas não temos linhas do tempo para compartilhar nesse momento.

* <a id="tier-options"></a>
**Atualmente, quais camadas de armazenamento têm suporte no serviço Arquivos do Azure?**  
    Atualmente, o serviço Arquivos do Azure dá suporte apenas à camada de armazenamento padrão. Não temos linhas do tempo a compartilhar para suporte ao armazenamento premium e ao armazenamento esporádico no momento. 
    
    > [!NOTE]
    > Você não pode criar compartilhamentos de Arquivos do Azure de contas de armazenamento somente para Blob ou de contas de armazenamento Premium.

* <a id="give-us-feedback"></a>
**Quero muito ver o recurso específico adicionado ao serviço Arquivos do Azure. Você pode adicioná-lo?**  
    A equipe do serviço Arquivos do Azure quer ouvir todos os comentários que você tem a fazer sobre nosso serviço. Vote nas solicitações de recurso no [UserVoice do Arquivos do Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files)! Estamos ansiosos para surpreendê-lo com muitos recursos novos.

## <a name="azure-file-sync"></a>Sincronização de Arquivos do Azure

* <a id="afs-region-availability"></a>
**Quais regiões têm suporte para Sincronização de arquivos do Azure (versão prévia)?**  
    Atualmente, o Sincronização de arquivos do Azure está disponível no Leste da Austrália, Central do Canadá, Leste dos EUA, Sudeste Asiático, Sul do Reino Unido, Europa Ocidental e Oeste dos EUA. O suporte para regiões mais será adicionado conforme seguirmos trabalhando rumo à disponibilidade geral. Para saber mais, veja [Disponibilidade por região](storage-sync-files-planning.md#region-availability).

* <a id="cross-domain-sync"></a>
**É possível ter servidores ingressados e não ingressados no domínio no mesmo grupo de sincronização?**  
    Sim. Sim, um grupo de sincronização poderá conter pontos de extremidade do servidor com diferentes associações do Active Directory, mesmo se eles não forem ingressados no domínio. Embora essa configuração funcione tecnicamente, não recomendamos isso como uma configuração típica, já que as ACLs (listas de controle de acesso) que são definidas para arquivos e pastas em um servidor podem não ser impostas por outros servidores no grupo de sincronização. Para obter melhores resultados, recomendamos a sincronização entre servidores na mesma floresta do Active Directory entre servidores que estão em diferentes florestas do Active Directory mas têm relações de confiança estabelecidas ou então entre servidores que não estão em um domínio. Recomendamos que você evite usar uma mistura dessas configurações.

* <a id="afs-change-detection"></a>
**Criei um arquivo diretamente no meu compartilhamento de arquivos do Azure no SMB ou por meio do portal. Quanto tempo leva para que o arquivo seja sincronizado aos servidores no grupo de sincronização?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

* <a id="afs-conflict-resolution"></a>**Se o mesmo arquivo é alterado em dois servidores quase ao mesmo tempo, o que acontece?**  
    A Sincronização de Arquivos do Azure usa uma estratégia simples de resolução de conflitos: mantemos ambas as alterações aos arquivos que são alterados em dois servidores simultaneamente. A alteração gravada mais recentemente mantém o nome do arquivo original. O arquivo mais antigo tem o computador de "origem" e o número do conflito anexado ao nome. Ele segue esta taxonomia: 
   
    \<FileNameWithoutExtension\>-\<MachineName\>\[-#\].\<ext\>  

    Por exemplo, o primeiro conflito de CompanyReport.docx se tornaria CompanyReport-CentralServer.docx se CentralServer fosse o local em que a gravação mais antiga ocorreu. O segundo conflito seria nomeado CompanyReport-CentralServer-1.docx.

* <a id="afs-storage-redundancy"></a>
**Há suporte para armazenamento com redundância geográfica na Sincronização de arquivos do Azure?**  
    Sim, o serviço Arquivos do Azure dá suporte tanto ao LRS (armazenamento com redundância local) quanto ao GRS (armazenamento com redundância geográfica). Se ocorrer um failover de GRS entre regiões emparelhadas, será recomendável tratar a nova região somente como um backup de dados. A Sincronização de Arquivos do Azure não inicia automaticamente a sincronização com a nova região primária. 

* <a id="sizeondisk-versus-size"></a>
**Por que a propriedade *Tamanho em disco* de um arquivo não corresponde à propriedade *Tamanho* depois do uso da Sincronização de arquivos do Azure?**  
    O Explorador de Arquivos do Windows expõe duas propriedades para representar o tamanho de um arquivo: **Tamanho** e **Tamanho em disco**. O significado dessas propriedades difere um pouco. **Tamanho** representa o tamanho completo do arquivo. **Tamanho em disco** representa o tamanho do fluxo de arquivo que é armazenado no disco. Os valores dessas propriedades podem ser diferentes por vários motivos, como compactação, uso da eliminação de duplicação de dados ou da disposição em camadas na nuvem com a Sincronização de Arquivos do Azure. Se um arquivo está em camadas em um compartilhamento de arquivos do Azure, o tamanho em disco é zero porque o fluxo de arquivos está armazenado em seu compartilhamento de arquivos do Azure, não no disco. Também é possível que um arquivo esteja parcialmente em camadas (ou seja parcialmente em recall). Em um arquivo parcialmente hierárquico, parte do arquivo está no disco. Isso pode acontecer quando os arquivos são lidos parcialmente por aplicativos como players de multimídia ou utilitários de compactação. 

* <a id="is-my-file-tiered"></a>
**Como saber se um arquivo está em camadas?**  
    Há várias maneiras de verificar se um arquivo foi colocado em camadas no compartilhamento de arquivos do Azure:
    
   *  **Verifique os atributos de arquivo no arquivo.**
     Para fazer isso, clique com o botão direito do mouse em um arquivo, navegue até **Detalhes** e role para baixo até a propriedade **Atributos**. Um arquivo em camadas tem os seguintes atributos definidos:     
        
        | Carta de atributo | Atributo | Definição |
        |:----------------:|-----------|------------|
        | O  | Arquivo | Indica que o arquivo deve ter o backup feito pelo software de backup. Esse atributo é sempre definido independentemente de o arquivo estar em camadas ou totalmente armazenado no disco. |
        | P | Arquivos esparsos | Indica que o arquivo é um arquivo esparso. Um arquivo esparso é um tipo especializado de arquivo que o NTFS oferece para uso eficiente quando o arquivo no fluxo do disco está basicamente vazio. A Sincronização de Arquivos do Azure usa arquivos esparsos, porque um arquivo é totalmente em camadas ou parcialmente cancelado. Em um arquivo totalmente em camadas, o fluxo de arquivos é armazenado na nuvem. Em um arquivo que sofreu recall parcial, essa parte do arquivo já está no disco. Se o recall de um arquivo é feito totalmente em disco, a Sincronização de Arquivos do Azure o converte de um arquivo esparso em um arquivo regular. |
        | L | Ponto de nova análise | Indica que o arquivo tem um ponto de nova análise. Um ponto de nova análise é um ponteiro especial para ser usado por um filtro do sistema de arquivos. A Sincronização de Arquivos do Azure usa pontos de nova análise a fim de definir, para o filtro do sistema de arquivos da Sincronização de Arquivos do Azure (StorageSync.sys), o local na nuvem em que o arquivo está armazenado. Isso dá suporte a acesso contínuo. Os usuários não precisarão saber que a Sincronização de Arquivos do Azure está sendo usada ou como obter acesso ao arquivo em seu compartilhamento de arquivos do Azure. Quando o recall de um arquivo é totalmente feito, a Sincronização de Arquivos do Azure remove o ponto de nova análise do arquivo. |
        | O | Off-line | Indica que parte ou nenhum conteúdo do arquivo está armazenado em disco. Quando o recall de um arquivo é totalmente feito, a Sincronização de Arquivos do Azure remove esse atributo. |

        ![A caixa de diálogo Propriedades de um arquivo com a guia Detalhes selecionada](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        Você pode ver os atributos de todos os arquivos em uma pasta adicionando o campo **Atributos** à exibição de tabela do Explorador de Arquivos. Para fazer isso, clique com o botão direito do mouse em uma coluna existente (por exemplo, **Tamanho**), selecione **Mais** e depois **Atributos** na lista suspensa.
        
   * **Use `fsutil` para verificar se há pontos de nova análise em um arquivo.**
       Conforme descrito na opção anterior, um arquivo em camadas sempre tem um conjunto de pontos de nova análise. Um ponteiro de nova análise é um ponteiro especial para o filtro de sistema de arquivos de Sincronização de Arquivos do Azure (StorageSync.sys). Para verificar se um arquivo tem um ponto de nova análise, execute o utilitário `fsutil` em um prompt de comandos com privilégios elevados ou em uma janela do PowerShell:
    
        ```PowerShell
        fsutil reparsepoint query <your-file-name>
        ```

        Se o arquivo tiver um ponto de nova análise, você deverá ver um **Valor de Marca de Nova Análise: 0x8000001e**. Esse valor hexadecimal é o valor de ponto de nova análise pertencente à Sincronização de Arquivos do Azure. A saída também contém os dados da nova análise que representam o caminho para o arquivo em seu compartilhamento de arquivos do Azure.

        > [!WARNING]  
        > O comando do utilitário `fsutil reparsepoint` também tem a capacidade de excluir um ponto de nova análise. Não execute esse comando, a menos que a equipe de engenharia de Sincronização de Arquivos do Azure lhe solicite isso. A execução desse comando pode resultar em perda de dados. 

* <a id="afs-recall-file"></a>**Um arquivo que eu desejo usar foi dividido em camadas. Como é possível fazer o recall do arquivo no disco para usá-lo localmente?**  
    A maneira mais fácil de fazer o recall de um arquivo em disco é abri-lo. O filtro de sistema de arquivos da Sincronização de Arquivos do Azure (StorageSync.sys) baixa o arquivo do seu compartilhamento de arquivos do Azure perfeitamente, sem que você precise fazer nenhum trabalho. Para tipos de arquivo que podem ser lidos parcialmente, tais como arquivos zip ou multimídia, abrir um arquivo não resultará no download do arquivo inteiro.

    Você também pode usar o PowerShell para forçar o recall de um arquivo. Essa opção poderá ser útil se você quiser fazer o recall de vários arquivos ao mesmo tempo, por exemplo, todos os arquivos dentro de uma pasta. Abra uma sessão do PowerShell para o nó de servidor em que a Sincronização de Arquivos do Azure está instalada e então execute os seguintes comandos do PowerShell:
    
    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncFileRecall -Path <file-or-directory-to-be-recalled>
    ```

* <a id="afs-force-tiering"></a>
**Como posso forçar um arquivo ou diretório a ficar em camadas?**  
    Quando habilitado, o recurso de disposição em camadas na nuvem dispõe os arquivos em camadas automaticamente com base no último acesso e na última modificação para alcançar o percentual de espaço livre no volume especificado no ponto de extremidade de nuvem. Às vezes, no entanto, talvez você queira forçar um arquivo manualmente a ser dividido em camadas. Isso pode ser útil se você salva um arquivo grande que não pretende usar novamente por um longo período e agora deseja usar o espaço livre no volume para outros arquivos ou pastas. Você pode forçar a disposição em camadas com os seguintes comandos do PowerShell:

    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
    ```

* <a id="afs-effective-vfs"></a>
**Como o *espaço livre no volume* é interpretado quando há vários pontos de extremidade do servidor em um volume?**  
    Quando há mais de um ponto de extremidade do servidor em um volume, o limite de espaço livre no volume efetivo é o maior espaço livre do volume especificado em qualquer ponto de extremidade do servidor nesse volume. Os arquivos serão colocados em camadas de acordo com seus padrões de uso, independentemente do ponto de extremidade do servidor ao qual pertencem. Por exemplo, se houver dois pontos de extremidade do servidor em um volume, Endpoint1 e Endpoint2, em que o Endpoint1 tenha um limite de espaço livre no volume de 25% e o Endpoint2 tenha um limite de espaço livre no volume de 50%, o limite de espaço livre no volume dos dois pontos de extremidade do servidor será de 50%.

* <a id="afs-files-excluded"></a>
**Quais arquivos ou pastas são excluídas automaticamente pela Sincronização de arquivos do Azure?**  
    Por padrão, a Sincronização de Arquivos do Azure exclui os arquivos abaixo:
    * desktop.ini
    * thumbs.db
    * ehthumbs.db
    * ~$\*.\*
    * \*.laccdb
    * \*.tmp
    * 635D02A9D91C401B97884B82B3BCDAEA.\*

    As pastas abaixo também são excluídas por padrão:

    * \SystemVolumeInformation
    * \$RECYCLE. BIN
    * \SyncShareState

* <a id="afs-os-support"></a>
**Posso usar a Sincronização de arquivos do Azure com o Windows Server 2008 R2, Linux ou o dispositivo NAS (armazenamento conectado à rede)?**  
    Hoje, a Sincronização de Arquivos do Azure dá suporte apenas a Windows Server 2016 e a Windows Server 2012 R2. Neste momento, não há outros planos que possamos divulgar, mas gostaríamos de dar suporte a outras plataformas com base na demanda dos clientes. Informe-nos quais plataformas você deseja que tenham suporte no [UserVoice do Arquivos do Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files).

## <a name="security-authentication-and-access-control"></a>Segurança, autenticação e controle de acesso
* <a id="ad-support"></a>
**Há suporte para a autenticação baseada no Active Directory e para o controle de acesso nos Arquivos do Azure?**  
    O serviço Arquivos do Azure oferece duas maneiras para gerenciar o controle de acesso:

    - Você pode usar SAS (assinaturas de acesso compartilhado) para gerar tokens que têm permissões específicas e que são válidos para um intervalo de tempo especificado. Por exemplo, você pode gerar um token com acesso somente leitura a um arquivo específico que tem validade de 10 minutos. Qualquer pessoa que tem esse token enquanto ele é válido tem acesso somente leitura ao arquivo por 10 minutos. Atualmente, chaves de assinatura de acesso compartilhado têm suporte apenas via API REST ou em bibliotecas de cliente. Você deve montar o compartilhamento de arquivos do Azure no SMB usando as chaves de conta de armazenamento.

    - A Sincronização de Arquivos do Azure preserva e replica todas as ACLs discricionárias ou DACLs (sejam elas baseadas no Active Directory ou locais) para todos os pontos de extremidade do servidor aos quais ela é sincronizada. Já que o Windows Server já pode se autenticar com o Active Directory, a Sincronização de Arquivos do Azure pode ser uma medida preventiva eficaz até que o suporte completo para autenticação baseada em Active Directory e o suporte a ACL fiquem disponíveis.

    No momento, os Arquivos do Azure não dão suporte ao Active Directory diretamente.

* <a id="encryption-at-rest"></a>
**Como garantir que o compartilhamento de arquivos do Azure está criptografado em repouso?**  
    A criptografia do Serviço de Armazenamento do Microsoft Azure está em processo de ser habilitada por padrão em todas as regiões. Para essas regiões, você não precisa realizar nenhuma ação para habilitar a criptografia. Para outras regiões, consulte [Criptografia no servidor](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="access-via-browser"></a>
**Como posso fornecer acesso a um arquivo específico usando um navegador da Web?**  
    Você pode usar assinaturas de acesso compartilhado para gerar tokens que têm permissões específicas e que são válidos para um intervalo de tempo especificado. Por exemplo, você pode gerar um token que dá acesso somente leitura a um arquivo específico para um período de tempo definido. Qualquer pessoa que tem a URL pode acessar o arquivo diretamente em qualquer navegador da Web enquanto o token é válido. Você pode gerar uma chave de assinatura de acesso compartilhado facilmente por meio de uma interface do usuário como o Gerenciador de Armazenamento.

* <a id="file-level-permissions"></a>
**É possível especificar permissões somente leitura ou somente gravação em pastas no compartilhamento?**  
    Se você montar o compartilhamento de arquivos via SMB, você não terá controle em nível de pasta sobre as permissões. No entanto, se você criar uma assinatura de acesso compartilhado usando a API REST ou as bibliotecas de cliente, você poderá especificar permissões somente leitura ou somente gravação em pastas contidas no compartilhamento.

* <a id="ip-restrictions"></a>
**Posso implementar restrições de IP para um compartilhamento de Arquivos do Azure?**  
    Sim. O acesso ao compartilhamento de arquivos do Azure pode ser restringido ao nível de conta de armazenamento. Para saber mais, consulte [Configurar Redes Virtuais e Firewalls de Armazenamento do Azure](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="data-compliance-policies"></a>
**A quais políticas de conformidade de dados o serviço Arquivos do Azure dá suporte?**  
   O Arquivos do Azure é executado com base na mesma arquitetura de armazenamento usada em outros serviços de armazenamento no Armazenamento do Azure. O Arquivos do Azure aplica as mesmas políticas de conformidade de dados que são usadas em outros serviços de armazenamento do Azure. Para obter mais informações sobre a conformidade de dados do Armazenamento do Azure, baixe e consulte o [documento Proteção de dados do Microsoft Azure](http://go.microsoft.com/fwlink/?LinkID=398382&clcid=0x409) e vá para a [Central de Confiabilidade da Microsoft](https://www.microsoft.com/TrustCenter/default.aspx).

## <a name="on-premises-access"></a>Acesso local
* <a id="expressroute-not-required"></a>
**Preciso usar o Azure ExpressRoute para me conectar aos Arquivos do Azure ou para usar a Sincronização de arquivos do Azure localmente?**  
    Nº O ExpressRoute não é necessário para acessar um compartilhamento de arquivos do Azure. Se você está montando um compartilhamento de arquivos do Azure diretamente localmente, basta ter a porta 445 (TCP de saída) aberta para acesso à Internet (essa é a porta pela qual o SMB se comunica). Se você está usando a Sincronização de Arquivos do Azure, basta ter a porta 443 (TCP de saída) para acesso HTTPS (não é necessário usar SMB). No entanto, você *pode* usar ExpressRoute com qualquer uma dessas opções de acesso.

* <a id="mount-locally"></a>
**Como posso montar o compartilhamento de arquivos do Azure no meu computador local?**  
    Você poderá montar o compartilhamento de arquivos por meio do protocolo SMB, desde que a porta 445 (TCP de Saída) esteja aberta e o cliente dê suporte ao protocolo SMB 3.0 (por exemplo, se você estiver usando o Windows 10 ou o Windows Server 2016). Se a porta 445 está bloqueada pela política da sua organização ou por pelo ISP, você pode usar a Sincronização de Arquivos do Azure para acessar o compartilhamento de arquivos do Azure.

## <a name="backup"></a>Backup
* <a id="backup-share"></a>
**Como posso fazer backup do meu compartilhamento de arquivos do Azure?**  
    Você pode usar [instantâneos de compartilhamento (versão prévia)](storage-how-to-use-files-snapshots.md) periódicos para proteção contra exclusões acidentais. Use o AzCopy, o Robocopy ou uma ferramenta de backup de terceiros que possa fazer backup de um compartilhamento de arquivos montado. 

## <a name="share-snapshots"></a>Instantâneos de compartilhamento
### <a name="share-snapshots-general"></a>Instantâneos de compartilhamento: geral
* <a id="what-are-snaphots"></a>
**O que são os instantâneos de compartilhamento de arquivo?**  
    Você pode usar os instantâneos de compartilhamento de arquivos do Azure para criar versões somente leitura de seus compartilhamentos de arquivos. Você também pode usar o serviço Arquivos do Azure para copiar uma versão anterior do conteúdo para o mesmo compartilhamento ou para um local alternativo no Azure ou local para modificações adicionais. Para saber mais sobre instantâneos de compartilhamento, consulte a [Visão geral de instantâneos de compartilhamento](storage-snapshots-files.md).

* <a id="where-are-snapshots-stored"></a>
**Onde os instantâneos de compartilhamento são armazenados?**  
    Os instantâneos de compartilhamento são armazenados na mesma conta de armazenamento do compartilhamento de arquivos.

* <a id="snapshot-perf-impact"></a>
**Há qualquer implicações de desempenho para o uso de instantâneos de compartilhamento?**  
    Os instantâneos de compartilhamento não geram sobrecarga de desempenho.

* <a id="snapshot-consistency"></a>
**Os instantâneos de compartilhamento são consistentes com o aplicativo?**  
    Não, os instantâneos de compartilhamento não são consistentes com o aplicativo. O usuário deve liberar as gravações do aplicativo para o compartilhamento antes de tirar um instantâneo de compartilhamento.

* <a id="snapshot-limits"></a>
**Há limites para o número de instantâneos de compartilhamento que posso usar?**  
    Sim. O serviço Arquivos do Azure pode armazenar um máximo de 200 instantâneos de compartilhamento. Os instantâneos de compartilhamento não são considerados na cota de compartilhamento e, portanto, não há nenhum limite por compartilhamento no espaço total utilizado por todos os instantâneos de compartilhamento. Os limites de conta de armazenamento ainda se aplicam. Depois de 200 instantâneos de compartilhamento, os instantâneos mais antigos precisarão ser excluídos para que seja possível criar novos instantâneos de compartilhamento.

### <a name="create-share-snapshots"></a>Criar instantâneos de compartilhamento
* <a id="file-snaphsots"></a>
**Posso criar instantâneo de compartilhamento de arquivos individuais?**  
    Os instantâneos de compartilhamento são criados no nível do compartilhamento de arquivos. Você pode restaurar arquivos individuais do instantâneo de compartilhamento de arquivos, mas não é possível criar instantâneos de compartilhamento no nível do arquivo. No entanto, se você tirou um instantâneo no nível do compartilhamento e desejar listar instantâneos de compartilhamento em que determinado arquivo foi alterado, pode fazer isso em **Versões Anteriores** em um compartilhamento montado no Windows. 
    
    Se você precisar de um recurso de instantâneo de arquivo, informe-nos em [UserVoice do Arquivos do Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files).

* <a id="encypted-snapshots"></a>
**Posso criar instantâneos de um compartilhamento de arquivos criptografado?**  
    Você pode tirar um instantâneo de compartilhamento de compartilhamentos de arquivos do Azure que têm a opção de criptografia em repouso habilitada. Você pode restaurar arquivos de um instantâneo de compartilhamento em um compartilhamento de arquivos criptografado. Se o compartilhamento for criptografado, o instantâneo de compartilhamento também será criptografado.

* <a id="geo-redundant-snaphsots"></a>
**Meus instantâneos de compartilhamento têm redundância geográfica?**  
    O instantâneo de compartilhamento tem a mesma redundância que o compartilhamento de arquivos do Azure para o qual ele foi criado. Se você tiver selecionado o armazenamento com redundância geográfica para sua conta, o instantâneo de compartilhamento também será armazenado de forma redundante na região emparelhada.

### <a name="manage-share-snapshots"></a>Gerenciar instantâneos de compartilhamento
* <a id="browse-snapshots-linux"></a>
**Posso navegar pelos meus instantâneos de compartilhamento no Linux?**  
    Você pode usar a CLI do Azure 2.0 para criar, listar, procurar e restaurar instantâneos no Linux.

* <a id="copy-snapshots-to-other-storage-account"></a>
**Posso copiar os instantâneos de compartilhamento em uma conta de armazenamento diferente?**  
    Você pode copiar arquivos dos instantâneos de compartilhamento para outro local, mas não pode copiar os instantâneos compartilhamento propriamente ditos.

### <a name="restore-data-from-share-snapshots"></a>Restaurar dados de instantâneos de compartilhamento
* <a id="promote-share-snapshot"></a>
**Posso promover um instantâneo de compartilhamento a compartilhamento base?**  
    Você pode copiar dados de um instantâneo de compartilhamento para qualquer outro destino. Você não pode promover um instantâneo de compartilhamento para o compartilhamento base.

* <a id="restore-snapshotted-file-to-other-share"></a>
**Posso restaurar dados do meu instantâneo de compartilhamento em uma conta de armazenamento diferente?**  
    Sim. Arquivos de um compartilhamento de instantâneo podem ser copiados para o local original ou para um local alternativo que inclua a mesma conta de armazenamento ou uma conta de armazenamento diferente, na mesma região ou em regiões diferentes. Você também pode copiar arquivos para uma localização local ou para qualquer outra nuvem.    
  
### <a name="clean-up-share-snapshots"></a>Limpar instantâneos de compartilhamento
* <a id="delete-share-keep-snapshots"></a>
**Posso excluir meu compartilhamento sem excluir os instantâneos de compartilhamento?**  
    Se você tiver instantâneos de compartilhamento ativos no compartilhamento, não será possível excluí-lo. Você pode usar uma API para excluir compartilhamentos de instantâneos, juntamente com o compartilhamento. Você também pode excluir os instantâneos de compartilhamento e o compartilhamento no Portal do Azure.

* <a id="delete-share-with-snapshots"></a>
**O que acontece com meus instantâneos de compartilhamento se eu excluo minha conta de armazenamento?**  
    Se você excluir sua conta de armazenamento, os instantâneos de compartilhamento também serão excluídos.

## <a name="billing-and-pricing"></a>Cobrança e preços
* <a id="vm-file-share-network-traffic"></a>
**O tráfego de rede entre uma VM do Azure e um compartilhamento de arquivos do Azure conta como largura de banda externa cobrada na assinatura?**  
    Se o compartilhamento de arquivos e a VM estiverem na mesma região do Azure, não há encargos adicionais para o tráfego entre o compartilhamento de arquivos e a VM. Se o compartilhamento de arquivos e a VM estiverem em regiões diferentes, o tráfego entre eles será cobrado como largura de banda externa.

* <a id="share-snapshot-price"></a>
**Quanto custam os instantâneos de compartilhamento?**  
     Durante a versão prévia, não há nenhum encargo para a capacidade do compartilhamento de instantâneo. Os custos de saída e de transação do armazenamento Standard se aplicam. Após a disponibilidade geral, as assinaturas serão cobradas por capacidade e por transações de instantâneos de compartilhamento.
     
     Os instantâneos de compartilhamento são incrementais por natureza. O instantâneo de compartilhamento base é o próprio compartilhamento. Todos os instantâneos de compartilhamento subsequentes são incrementais e armazenam somente a diferença do instantâneo de compartilhamento anterior. Você será cobrado somente pelo conteúdo alterado. Se você tiver um compartilhamento com 100 GiB de dados, mas apenas 5 GiB foram alterados após o último instantâneo de compartilhamento, este consumirá apenas 5 GiB adicionais e você será cobrado por 105 GiB. Para obter mais informações sobre encargos de transações e de saída Standard, consulte a [Página de preços](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="scale-and-performance"></a>Escala e desempenho
* <a id="files-scale-limits"></a>
**Quais são os limites de escala dos Arquivos do Azure?**  
    Para obter informações sobre as metas de escalabilidade e desempenho do Arquivos do Azure, consulte [Metas de escalabilidade e desempenho do Arquivos do Azure](storage-files-scale-targets.md).

* <a id="need-larger-share"></a>
**Preciso de um compartilhamento de arquivos maior que o oferecido atualmente pelo serviço Arquivos do Azure. Posso aumentar o tamanho do meu compartilhamento de arquivos do Azure?**  
    Nº O tamanho máximo de um compartilhamento de arquivos do Azure é 5 TiB. Atualmente, esse é um limite fixo que não pode ser ajustado. Estamos trabalhando em uma solução para aumentar o tamanho do compartilhamento para 100 TiB, mas não temos uma previsão para compartilhar neste momento.

* <a id="open-handles-quota"></a>
**Quantos clientes podem acessar simultaneamente o mesmo arquivo?**   
    Há uma cota de 2.000 identificadores abertos em um único arquivo. Quando você tem 2.000 identificadores abertos, uma mensagem de erro é exibida informando que a cota foi atingida.

* <a id="zip-slow-performance"></a>
**O desempenho é lento quando eu descompacto arquivos no Arquivos do Azure. O que devo fazer?**  
    Para transferir grandes quantidades de arquivos para o Arquivos do Azure, recomendamos o uso do AzCopy (para Windows, em versão prévia para Linux e UNIX) ou do Azure PowerShell. Essas ferramentas foram otimizadas para transferência de rede.

* <a id="slow-perf-windows-81-2012r2"></a>
**Por que o desempenho está lento após eu montar meu compartilhamento de arquivos do Azure no Windows Server 2012 R2 ou no Windows 8.1?**  
    Há um problema conhecido ao montar um compartilhamento de arquivos do Azure no Windows Server 2012 R2 e no Windows 8.1. O problema foi corrigido na atualização cumulativa de abril de 2014 para o Windows 8.1 e o Windows Server 2012 R2. Para um melhor desempenho, verifique se todas as instâncias do Windows Server 2012 R2 e do Windows 8.1 têm esse patch aplicado. (Você sempre deve receber patches do Windows por meio do Windows Update.) Para obter mais informações, consulte o artigo da Base de Dados de Conhecimento Microsoft associado, [Desempenho lento ao acessar o Arquivos do Azure no Windows 8.1 ou Server 2012 R2](https://support.microsoft.com/kb/3114025).

## <a name="features-and-interoperability-with-other-services"></a>Recursos e interoperabilidade com outros serviços
* <a id="cluster-witness"></a>
**É possível usar o compartilhamento de arquivos do Azure como uma *Testemunha de Compartilhamento de Arquivos* para o Cluster de Failover do Windows Server?**  
    Atualmente, em um compartilhamento de arquivos do Azure, não há suporte para essa configuração. Para saber mais sobre como configurar isso para o Armazenamento de Blobs do Azure, confira [Implantar uma testemunha de nuvem em um cluster de failover](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness).

* <a id="containers"></a>
**Posso montar um compartilhamento de arquivos do Azure em uma instância de Contêiner do Azure?**  
    Sim, os compartilhamentos de arquivos do Azure são uma opção fantástica para manter informações além do tempo de vida de uma instância de contêiner. Para obter mais informações, consulte [Montar um compartilhamento de arquivos do Azure com Instâncias de Contêiner do Azure](../../container-instances/container-instances-mounting-azure-files-volume.md).

* <a id="rest-rename"></a>
**Há uma operação de renomeação na API REST?**  
    Não no momento.

* <a id="nested-shares"></a>
**Eu posso configurar compartilhamentos aninhados? Em outras palavras, um compartilhamento em um compartilhamento?**  
    Nº O compartilhamento de arquivos *é* o driver virtual que você pode montar; portanto, não há suporte para compartilhamentos aninhados.

* <a id="ibm-mq"></a>
**Como posso usar os Arquivos do Azure com o IBM MQ?**  
    A IBM liberou um documento que ajuda clientes do IBM MQ a configurar o Arquivos do Azure juntamente com o serviço IBM. Para obter mais informações, consulte [Como configurar o gerenciador de filas com várias instâncias do IBM MQ com o serviço Arquivos do Microsoft Azure](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service).

## <a name="see-also"></a>Consulte também
* [Solucionar problemas do Arquivos do Azure no Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Solucionar problemas do Arquivos do Azure no Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Solucionar problemas da Sincronização de Arquivos do Azure (versão prévia)](storage-sync-files-troubleshoot.md)
