---
title: Perguntas Frequentes do Microsoft Azure Data Box Disk | Microsoft Docs nos dados
description: Contém perguntas frequentes e respostas sobre o Azure Data Box Disk, uma solução de nuvem que permite transferir grandes quantidades de dados para o Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: overview
ms.date: 02/14/2019
ms.author: alkohli
ms.openlocfilehash: 007c63c163b6e972bd59189569c92c99d2718cbe
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301478"
---
# <a name="what-is-azure-data-box-disk"></a>O que é o Azure Data Box Disk?

A solução de nuvem do Microsoft Azure Data Box Disk permite que você envie terabytes de dados do Azure de maneira rápida, barata e confiável. Estas Perguntas frequentes contêm respostas para perguntas que podem surgir durante o uso dos Discos do Data Box no portal do Azure. 

As perguntas e respostas são organizadas nas seguintes categorias:

- Sobre o serviço
- Configuração e conexão 
- Rastreamento de status
- Migrar dados 
- Verificação e carregamento de dados 


## <a name="about-the-service"></a>Sobre o serviço

### <a name="q-what-is-azure-data-box-service"></a>P. O que é o serviço Azure Data Box? 
a.  O serviço Azure Data Box foi criado para a ingestão de dados offline. Esse serviço gerencia uma variedade de produtos e é personalizado para o transporte de dados em diferentes capacidades de armazenamento. 

### <a name="q-what-are-azure-data-box-disks"></a>P. O que são os Discos do Azure Data Box?
a. Os Discos do Azure Data Box permitem uma transferência de terabytes de dados rápida, barata e segura entre a plataforma do Azure. A Microsoft fornece de um a cinco discos, com capacidade de armazenamento máxima de 35 TB. Você pode configurar, conectar e desbloquear facilmente esses discos por meio do serviço Data Box no Portal do Azure.  

Os discos são criptografados usando a Criptografia de Unidade de Disco BitLocker da Microsoft e suas chaves de criptografia são gerenciadas no portal do Azure. Em seguida, você copia os dados dos servidores do cliente. No datacenter, a Microsoft migra os dados da unidade para a nuvem usando um link de carregamento rápido de rede privada e os carrega para o Azure.

### <a name="q-when-should-i-use-data-box-disks"></a>P. Quando devo usar Discos do Data Box?
a. Se você tiver 40 TB de dados (ou menos) que deseja transferir para o Azure, os Discos do Data Box serão uma boa opção.

### <a name="q-what-is-the-price-of-data-box-disks"></a>P. Qual é o preço dos Discos do Data Box?
a. Para saber mais sobre o preço do Data Box Disk, acesse a [página Preços](https://azure.microsoft.com/pricing/details/storage/databox/disk/).

### <a name="q-how-do-i-get-data-box-disks"></a>P. Como fazer para obter Discos do Data Box? 
a.  Para obter Azure Data Box Disks, faça logon no portal do Azure e crie um pedido de discos do Data Box. Forneça detalhes de notificação e informações de contato. Depois de fazer um pedido, com base na disponibilidade, os discos serão enviados para você no prazo de 10 dias.

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-disks-in-one-instance"></a>P. Qual é a quantidade máxima de dados que posso transferir com Discos do Data Box em uma instância?
a. Com cinco discos, cada um com 8 TB (7 TB de capacidade utilizável), a capacidade utilizável máxima é de 35 TB. Portanto, você pode transferir 35 TB de dados em uma instância. Para transferir mais dados, você precisa solicitar mais discos.

### <a name="q-how-can-i-check-if-data-box-disks-are-available-in-my-region"></a>P. Como verificar se os Discos do Data Box estão disponíveis na minha região? 
a.  Atualmente, os Data Box Disks estão disponíveis nos EUA, no Canadá, na Austrália e em todos os países da União Europeia.  

### <a name="q-which-regions-can-i-store-data-in-with-data-box-disks"></a>P. Em que regiões posso armazenar dados com Discos do Data Box?
a. O Data Box Disk tem suporte em todas as regiões nos EUA, no Canadá, na Austrália e na Europa Ocidental e na Europa Setentrional. Somente as regiões de nuvem pública do Azure têm suporte. Não há suporte para o Azure Governamental ou outras nuvens soberanas.

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues--with-data-box-disks"></a>P. Quem devo contatar se eu encontrar problemas com Discos do Data Box?
a. Caso tenha problemas com os Data Box Disks, [contate o Suporte da Microsoft](https://docs.microsoft.com/azure/databox/data-box-disk-contact-microsoft-support).

## <a name="configure-and-connect"></a>Configuração e conexão
 
### <a name="q-can-i-specify-the-number-of-data-box-disks-in-the-order"></a>P. Posso especificar o número de Discos do Data Box no pedido?
a.   Não. Você obtém os discos de 8 TB (máximo de até cinco discos) dependendo do tamanho de seus dados e da disponibilidade dos discos.  

### <a name="q-how-do-i-unlock-the-data-box-disks"></a>P. Como faço para desbloquear os Discos do Data Box? 
a.  No portal do Azure, vá para o pedido do Data Box Disk e navegue até **Detalhes do dispositivo**. Copie a chave de acesso. Baixe e extraia a ferramenta de desbloqueio do Data Box Disk do portal do Azure para seu sistema operacional. Execute a ferramenta no computador que contém os dados que você deseja copiar para os discos. Forneça a chave de acesso para desbloquear os discos. A mesma chave de acesso desbloqueia todos os discos. 

Para obter instruções passo a passo, acesse [Desbloquear discos em um cliente do Windows](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client) ou [Desbloquear discos em um cliente do Linux](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client).

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box-disks"></a>P. Posso usar um computador de host do Linux para me conectar e copiar os dados para Discos do Data Box?
a.  Sim. Os clientes do Linux e do Windows podem ser usados para conectar-se e copiar dados em Discos do Data Box. Para obter mais informações, vá para a lista de [sistemas operacionais com suporte](data-box-disk-system-requirements.md) de seu computador host.

### <a name="q-my-disks-are-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>P. Meus discos já foram enviados, mas agora quero cancelar o pedido. Por que o botão Cancelar não está disponível?
a.  Pedidos só podem ser cancelados após a realização do pedido de discos e antes do envio. Depois que os discos são expedidos, você não pode cancelar o pedido. No entanto, você pode devolver seus discos pagando um encargo. 

### <a name="q-can-i-connect-multiple-data-box-disks-at-the-same-to-the-host-computer-to-transfer-data"></a>P. Posso conectar vários Discos do Data Box ao mesmo tempo ao computador host para transferir dados?
a. Sim. Vários Discos do Data Box podem ser conectados ao mesmo computador host para transferir dados, e vários trabalhos de cópia podem ser executados paralelamente.

## <a name="track-status"></a>Rastreamento de status

### <a name="q-how-do-i-track-the-disks-from-when-i-placed-the-order-to-shipping-the-disks-back"></a>P. Como rastrear os discos desde a realização do pedido até a devolução? 
a.  Você pode acompanhar o status do pedido de Data Box Disk no portal do Azure. Ao criar o pedido, é necessário também fornecer um email para notificações. Através desse email você será notificado sobre todas as alterações de status do pedido. Para obter mais informações, consulte como [configurar emails de notificação](data-box-portal-ui-admin.md#edit-notification-details).

### <a name="q-how-do-i-return-the-disks"></a>P. Como fazer para retornar os discos? 
a.  A Microsoft fornece uma etiqueta de remessa com os Discos do Data Box no pacote de envio. Cole a etiqueta na embalagem para envio e leve o pacote lacrado à transportadora responsável. Se o rótulo estiver danificado ou for perdido, vá para **Visão geral > Baixar etiqueta de remessa** e baixe um novo rótulo de remessa de devolução.

## <a name="migrate-data"></a>Migrar dados

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box-disks"></a>P. Qual é o tamanho máximo de dados que pode ser usado com Discos do Data Box?  
a.  A solução Discos do Data Box pode ter até cinco discos com capacidade máxima utilizável de 35 TB. Os discos em si têm 8 TB (7 TB utilizáveis).

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box-disks"></a>P. Quais são os tamanhos máximos de blob de página e de bloco compatíveis com os Discos do Data Box? 
a.  Os tamanhos máximos são regidos pelos limites do Armazenamento do Azure. O blob de blocos máximo é de aproximadamente 4,768 TiB e o tamanho do blob de página máximo é 8 TiB. Para obter mais informações, vá para [Metas de desempenho e escalabilidade do Armazenamento do Azure](../storage/common/storage-scalability-targets.md).

### <a name="q-what-is-the-data-transfer-speed-for-data-box-disks"></a>P. Qual é a velocidade de transferência de dados dos Discos do Data Box?
a. Quando testado com discos conectados via USB 3.0, o desempenho do disco foi de até 430 MB/s. Os números reais variam dependendo do tamanho do arquivo usado. Para arquivos menores, o desempenho poderá ser mais baixo.

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>P. Como fazer para saber se meus dados estão protegidos durante o trânsito? 
a.  Os Discos do Data Box são criptografados usando a criptografia BitLocker AES de 128 bits e a chave de acesso só está disponível no portal do Azure. Faça logon no portal do Azure usando suas credenciais de conta para obter a chave de acesso. Forneça essa chave de acesso quando executar a ferramenta de desbloqueio do Data Box Disk.

### <a name="q-how-do-i-copy-the-data-to-the-data-box-disks"></a>P. Como fazer para copiar os dados para os Discos do Data Box? 
a.  Use uma ferramenta de cópia SMB, como o Robocopy, o Diskboss ou até mesmo o arrastar e soltar do Explorador de Arquivos do Windows para copiar os dados nos discos.

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>P. Existem dicas para acelerar a cópia dos dados?
a.  Para acelerar o processo de cópia:

- Use diversos fluxos de cópia de dados. Por exemplo, com o Robocopy, use a opção de vários threads. Para obter mais informações sobre o comando exato usado, acesse [Tutorial: copiar dados para o Azure Data Box Disk e verificar](data-box-disk-deploy-copy-data.md#copy-data-to-disks).
- Use várias sessões.
- Em vez de copiar arquivos por compartilhamento de rede (onde você poderia ser limitado pelas velocidades da rede), tenha os dados no mesmo computador em que os discos estão conectados.
- Verifique se você está usando USB 3.0 ou posterior em todo o processo de cópia. Baixe e use a [ferramenta USBView](https://docs.microsoft.com/windows-hardware/drivers/debugger/usbview) para identificar os controladores USB e dispositivos USB conectados ao computador.
- Avalie o desempenho do computador usado para copiar os dados. Baixe e use a [ferramenta FIO Bluestop](https://bluestop.org/fio/) para avaliar o desempenho do hardware do servidor.

### <a name="q-how-to-speed-up-the-data-if-the-source-data-has-small-files-kbs-or-few-mbs"></a>P. Como acelerar os dados se a fonte de dados tiver arquivos pequeno (KBs ou alguns MBs)?
a.  Para acelerar o processo de cópia:

- Crie um VHDx local em armazenamento rápido ou crie um VHD vazio em HDD/SSD (mais lento).
- Monte-o em uma VM.
- Copie arquivos para o disco da VM.

### <a name="q-can-i-use-multiple-storage-accounts-with-data-box-disks"></a>P. Posso usar várias contas de armazenamento com Discos do Data Box?
a.   Não. Apenas uma conta de armazenamento geral ou clássica tem suporte atualmente para os Discos do Data Box. Há suporte para o blob frequente e esporádico. Atualmente, há suporte apenas para as contas de armazenamento dos EUA, da Europa Ocidental e da Europa Setentrional na nuvem pública do Azure.

### <a name="q-what-is-the-toolset-available-for-my-data-with-data-box-disks"></a>P. O que é o conjunto de ferramentas disponível para meus dados com Data Box Disks?
a. O conjunto de ferramentas disponível com o Data Box Disk contém três ferramentas:
 - **Ferramenta de desbloqueio do Data Box Disk**: Use essa ferramenta para desbloquear os discos criptografados que são enviados pela Microsoft. Ao desbloquear os discos usando a ferramenta, você precisa fornecer uma chave de acesso disponível no pedido do Data Box Disk no portal do Azure. 
 - **Ferramenta de validação do Data Box Disk**: Use essa ferramenta para validar tamanho, formato e nomes de blob de acordo com o Azure convenções de nomenclatura. Ela também gera somas de verificação para os dados copiados que, em seguida, são usados para verificar os dados carregados no Azure.
 - **Ferramenta de cópia de divisão do Data Box Disk**: Use essa ferramenta quando você usar vários discos e tiver um grande conjunto de dados que precisa ser dividido e copiado entre todos os discos. Essa ferramenta já está disponível para Windows.

O conjunto de ferramentas está disponível para Windows e Linux. Você pode baixar o conjunto de ferramentas aqui:
 - [Baixe o conjunto de ferramentas do Data Box Disk para Windows](https://aka.ms/databoxdisktoolswin) 
 - [Baixe o conjunto de ferramentas do Data Box Disk para Linux](https://aka.ms/databoxdisktoolslinux)
 
 ### <a name="q-can-i-use-data-box-disk-to-transfer-data-to-azure-files-and-then-use-the-data-with-azure-file-sync"></a>P. Posso usar o Data Box Disk para transferir dados para os Arquivos do Azure e, em seguida, usar os dados com a Sincronização de Arquivos do Azure? 
 a. Não há suporte para os Arquivos do Azure no Data Box Disk. Os metadados também não serão mantidos se os dados de arquivo forem usados posteriormente com a Sincronização de Arquivos do Azure.


## <a name="verify-and-upload"></a>Verificar e carregar

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-disks-back"></a>P. Quanto tempo demora até eu poder acessar meus dados no Azure depois que os discos são devolvidos? 
a.  Depois que o status do pedido de cópia de dados aparece como concluído, você deve conseguir acessar os dados imediatamente.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>P. Onde meus dados estão localizados no Azure após o carregamento?
a.  Quando você copia os dados nas pastas *BlockBlob* e *PageBlob* no disco, um contêiner é criado na conta de armazenamento do Azure para cada subpasta nas pastas *BlockBlob* e *PageBlob*. Se você copiou os arquivos nas pastas *BlockBlob* e *PageBlob* diretamente, eles estarão em um contêiner padrão *$root* na conta de armazenamento do Azure.

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>P. Acabei de ver que não segui os requisitos de nomenclatura do Azure para meus contêineres. Meus dados serão carregados no Azure?
a. Se os nomes de contêiner têm uma letra maiúscula, elas serão automaticamente convertidas em minúsculas. Se os nomes não estiverem de acordo com de outros requisitos (caracteres especiais, outros idiomas e assim por diante), o carregamento falhará. Para obter mais informações, acesse [Convenções de nomenclatura do Azure](data-box-disk-limits.md#azure-block-blob-and-page-blob-naming-conventions).

### <a name="q-how-do-i-verify-the-data-i-copied-onto-multiple-data-box-disks"></a>P. Como fazer para verificar os dados que copiei em vários Discos do Data Box?
a.  Depois que a cópia de dados for concluída, você poderá executar `DataBoxDiskValidation.cmd` fornecido na pasta *DataBoxDiskImport* para gerar somas de verificação para validação. Se você tiver vários discos, precisará abrir uma janela de comando por disco e executar esse comando. Tenha em mente que a operação pode levar muito tempo (~ horas) dependendo do tamanho dos dados.

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-disks"></a>P. O que acontece com meus dados após eu devolver os discos?
a.  Depois que a cópia de dados para o Azure for concluída, os dados dos discos serão apagados com segurança de acordo com as diretrizes NIST SP 800-88 Revision 1.  

### <a name="q-how-is-my-data-protected-during-transit"></a>P. Como meus dados são protegidos durante o trânsito? 
a.  Os Discos do Data Box são criptografados com criptografia AES-128 Microsoft BitLocker. Eles exigem uma chave de acesso única para desbloquear todos os discos e acessar os dados.

### <a name="q-do-i-need-to-rerun-checksum-validation-if-i-add-more-data-to-the-data-box-disks"></a>P. Preciso executar novamente a validação da soma de verificação se eu adicionar mais dados aos Discos do Data Box?
a. Sim. Se você decidir validar seus dados (é recomendável que você o faça!), precisará executar a validação novamente caso tenha adicionado mais dados aos discos.

### <a name="q-i-used-all-my-disks-to-transfer-data-and-need-to-order-more-disks-is-there-a-way-to-quickly-place-the-order"></a>P. Eu usei todos os meus discos para transferir dados e preciso solicitar mais discos. Há uma maneira de fazer o pedido rapidamente?
a. Você pode clonar o pedido anterior. A clonagem cria o mesmo pedido de antes e permite que você edite somente os detalhes do pedido, sem a necessidade de digitar detalhes de endereço, contato e notificação. 

## <a name="next-steps"></a>Próximas etapas

- Reveja os [requisitos de sistema do Data Box](data-box-disk-system-requirements.md).
- Entenda os [limites do Data Box](data-box-disk-limits.md).
- Implante rapidamente o [Azure Data Box Disk](data-box-disk-quickstart-portal.md) no portal do Azure.
