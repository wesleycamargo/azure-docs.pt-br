<properties 
	pageTitle="Usando importação/exportação para transferir dados para o Armazenamento de Blob | Microsoft Azure" 
	description="Saiba como criar trabalhos de importação e exportação no Portal de Gerenciamento do Azure para transferir dados para o armazenamento de blob." 
	authors="tamram" 
	manager="adinah" 
	editor="" 
	services="storage" 
	documentationCenter=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/03/2015" 
	ms.author="tamram"/>


# Usar o serviço de Importação/Exportação do Microsoft Azure para transferir dados para o Armazenamento de Blob

## Visão geral

Você pode usar o serviço de Importação/Exportação do Microsoft Azure para transferir grandes quantidades de dados de arquivo para o armazenamento de Blob do Azure em situações onde o carregamento pela rede é extremamente dispendioso ou inviável. Você também pode usar o serviço Importar/Exportar para transferir grandes quantidades de dados residentes no armazenamento de Blob para suas instalações locais no devido tempo e de forma economicamente viável.

Para transferir um grande conjunto de dados de arquivo para o armazenamento de Blob, você pode enviar um ou mais discos rígidos contendo esses dados para um data center do Azure, onde seus dados serão carregados em sua conta de armazenamento. Da mesma forma, para exportar dados do armazenamento de Blob, você pode enviar discos rígidos vazios para um data center do Azure, onde os dados do Blob de sua conta de armazenamento serão copiados para os discos rígidos e, em seguida, devolvidos para você. Antes de enviar uma unidade contendo dados, você criptografará os dados na unidade; quando o serviço Importar/Exportar exportar seus dados para enviar para você, os dados também serão criptografados antes do envio.

Você pode criar e gerenciar trabalhos de importação e exportação de duas maneiras:

- Usando o Portal de Gerenciamento do Azure.
- Usando uma interface REST para o serviço.

Este artigo apresenta uma visão geral do serviço Importar/Exportar e descreve como usar o Portal de Gerenciamento para funcionar com esse serviço. Para obter informações sobre a API REST, consulte a [Referência da API REST do serviço de importação e exportação do Azure](http://go.microsoft.com/fwlink/?LinkID=329099).

## Introdução ao Serviço de Importação/Exportação ##

Para começar o processo de importação para o armazenamento de Blob ou de exportação desse armazenamento, primeiro crie um *trabalho*, que pode ser um *trabalho de importação* ou um *trabalho de exportação*:

- Crie um trabalho de importação quando desejar transferir dados locais para blobs na sua conta de armazenamento do Azure.
- Crie um trabalho de exportação quando desejar transferir os dados armazenados atualmente como blobs na sua conta de armazenamento para discos rígidos enviados a você.

Ao criar um trabalho, você notifica o serviço de Importação/Exportação de que enviará um ou mais discos rígidos para um data center do Azure. Para um trabalho de importação, você enviará discos rígidos contendo dados de arquivo. Para um trabalho de exportação, você enviará discos rígidos vazios.

Para preparar seu disco rígido para envio a um trabalho de importação, execute a **Ferramenta de Importação/Exportação do Microsoft Azure**, que facilita a cópia dos seus dados para a unidade, criptografando os dados na unidade com BitLocker e gerando os arquivos de diário de unidade, que serão discutidos a seguir.

> [AZURE.NOTE]Os dados na unidade devem ser criptografados com a Criptografia de Unidade de Disco BitLocker. Isso protegerá os dados enquanto eles estiverem em trânsito. Em um trabalho de exportação, o serviço Importar/Exportar criptografa seus dados antes de enviar a unidade de volta para você.

Ao criar um trabalho de importação ou de exportação, você também precisará da *ID da unidade*, que é o número de série atribuído pelo fabricante da unidade a um disco rígido específico. A ID de unidade é exibida na parte externa da unidade.

### Requisitos e escopo

1.	**Assinatura e contas de armazenamento:** você deve ter uma assinatura do Azure e uma ou mais contas de armazenamento existentes para usar o serviço de Importação/Exportação. Cada trabalho pode ser usado para transferir dados para apenas uma conta de armazenamento, ou por meio dela. Em outras palavras, um trabalho não pode se estender por várias contas de armazenamento. Para obter informações sobre como criar uma nova conta de armazenamento, consulte [Como criar uma conta de armazenamento](storage-create-storage-account.md).
2.	**Discos rígidos:** somente discos rígidos SATA II de 3,5 polegadas têm suporte para uso com o serviço Importação/Exportação. São suportados discos rígidos até 6 TB. Para trabalhos de importação, somente o primeiro volume de dados na unidade será processado. O volume de dados deve ser formatado com NTFS. Você pode anexar um disco SATA II/III externamente a maioria dos computadores usando um adaptador USB para SATA II/III.
3.	**Criptografia BitLocker:** todos os dados armazenados em discos rígidos devem ser criptografados usando o BitLocker com chaves de criptografia protegidas com senhas numéricas.
4.	**Destinos de armazenamento de blob:** é possível carregar dados para blobs de blocos e blobs de páginas e baixar dados desses blobs. 
5.	**Número de trabalhos:** um cliente pode ter até 20 trabalhos ativos por conta de armazenamento.
6.	**Tamanho máximo de um trabalho:** o tamanho de um trabalho é determinado pela capacidade dos discos rígidos usados e a quantidade máxima de dados que podem ser armazenados em uma conta de armazenamento. Cada trabalho pode conter, no máximo, 10 discos rígidos.

## Criar um trabalho de importação no Portal de Gerenciamento##

Crie um trabalho de importação para notificar o serviço Importar/Exportar de que você enviará uma ou mais unidades contendo dados para o data center e que eles deverão ser importados para a sua conta de armazenamento.

### Preparar suas unidades

Antes de criar um trabalho de importação, prepare suas unidades com a Ferramenta de Importação/Exportação do Microsoft Azure. Para obter mais detalhes sobre como usar a Ferramenta de Importação/Exportação do Microsoft Azure, consulte a [Referência da Ferramenta de Importação/Exportação do Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=329032). Você pode baixar a [Ferramenta de Importação/Exportação do Microsoft Azure](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) como um pacote autônomo.
  
Para preparar as unidades, siga estas três etapas:

1.	Determine os dados a serem importados e o número de unidades necessárias.
2.	Identifique os blobs de destino de seus dados no serviço Blob do Azure.
3.	Use a Ferramenta de Importação/Exportação do Microsoft Azure para copiar seus dados para um ou mais discos rígidos.

A Ferramenta de Importação/Exportação do Microsoft Azure gera um arquivo de *diário de unidade* para cada unidade à medida que ela for preparada. O arquivo de diário de unidade é armazenado no seu computador local, e não na própria unidade. Você poderá carregar o arquivo de diário ao criar o trabalho de importação. Um arquivo de diário de unidade inclui a ID de unidade e a chave do BitLocker, bem como outras informações sobre a unidade.

### Criar o trabalho de importação

1.	Depois de preparar a unidade, navegue até a conta de armazenamento no Portal de Gerenciamento e exiba o Painel. Em **Visão Rápida**, clique em **Criar um Trabalho de Importação**. 
 
2.	Na Etapa 1 do assistente, indique que você preparou a unidade e que o arquivo de diário de unidade está disponível.
 
3.	Na Etapa 2, forneça as informações de contato da pessoa responsável por esse trabalho de importação. Se desejar salvar dados de log detalhados para o trabalho de importação, marque a opção para **Salvar o log detalhado no meu contêiner de blob 'waimportexport'**.

4.	Na Etapa 3, carregue os arquivos de diário de unidade obtidos durante a etapa de preparação de unidade. Você precisa carregar um arquivo para cada unidade preparada.

	![Criar o trabalho de importação - Etapa 3][import-job-03]

5.	Na Etapa 4, digite um nome descritivo para o trabalho de importação. Observe que o nome fornecido pode conter somente letras minúsculas, números, hifens e sublinhados, deve começar com letra e não pode conter espaços. Você usará o nome escolhido para acompanhar os trabalhos enquanto eles estiverem em andamento e quando eles estiverem concluídos.

	Em seguida, selecione a região do data center na lista. A região do data center indica o data center e o endereço para o qual você deve enviar seu pacote. Consulte as Perguntas Frequentes abaixo para obter mais informações.

6. 	Na Etapa 5, selecione sua operadora de retorno na lista e insira o número da conta da operadora. A Microsoft irá usar esta conta para enviar suas unidades de volta para você após a conclusão do seu trabalho de importação.

	Se tiver um número de acompanhamento, selecione a transportadora na lista e insira o número de acompanhamento.

	Se ainda não tiver um número de controle, escolha **Fornecerei minhas informações de remessa para este trabalho de importação após enviar meu pacote** e, em seguida, conclua o processo de importação.

7. Para inserir o número de controle após enviar o pacote, volte à página **Importar/Exportar** da sua conta de armazenamento no Portal de Gerenciamento, selecione seu trabalho na lista e selecione **Informações de Remessa**. Navegue pelo assistente e insira o número de acompanhamento na Etapa 2.
	
	Se o número de acompanhamento não está atualizado em 2 semanas após a criação do trabalho, este irá expirar.

	Você também pode atualizar o número da conta da transportadora na Etapa 2 do assistente, caso o trabalho esteja nas fases Criando, Enviando ou Transferindo. Você não pode atualizar o número da conta da transportadora para o trabalho em questão a partir da fase de Empacotamento.

## Criar um trabalho de exportação no Portal de Gerenciamento##

Crie um trabalho de exportação para notificar o serviço Importar/Exportar que você enviará uma ou mais unidades vazias para o data center, para que os dados podem ser exportados de sua conta de armazenamento para as unidades e, em seguida, as unidades sejam enviadas para você.

1. 	Para criar um trabalho de exportação, navegue até a sua conta de armazenamento no Portal de Gerenciamento e exiba o Painel. Em **Visão Rápida**, clique em **Criar um Trabalho de Exportação** e continue com o assistente.

2. 	Na Etapa 2, forneça as informações de contato da pessoa responsável por esse trabalho de exportação. Se desejar salvar dados de log detalhados para o trabalho de exportação, marque a opção para **Salvar o log detalhado no meu contêiner de blob 'waimportexport'**.

3.	Na Etapa 3, especifique quais dados de blob deseja exportar da sua conta de armazenamento para a(s) unidade(s) em branco. Você pode optar por exportar todos os dados de blob na conta de armazenamento ou especificar quais blobs ou conjuntos de blobs serão exportados.

	![Criar o trabalho de exportação - Etapa 3][export-job-03]

	- Para especificar um blob para exportação, use o seletor **Igual a** e especifique o caminho relativo do blob, começando pelo nome do contêiner. Use *$root* para especificar o contêiner raiz.
	- Para especificar todos os blobs que começam com um prefixo, use o seletor **Começa com** e especifique o prefixo, começando com uma barra '/'. O prefixo pode ser do nome do contêiner, o nome do contêiner completo ou o nome do contêiner completo seguido do prefixo do nome do blob.

	A tabela mostra exemplos de caminhos de blob válidos:

	Seletor|Caminho do Blob|Descrição
	---|---|---
	Começa com|/|Exporta todos os blobs na conta de armazenamento
	Começa com|/$root/|Exporta todos os blobs no contêiner raiz
	Começa com|/book|Exporta todos os blobs em qualquer contêiner que comece com o prefixo **book**
	Começa com|/music/|Exporta todos os blobs no contêiner **music**
	Começa com|/music/love|Exporta todos os blobs no contêiner **music** que comecem com o prefixo **love**
	Igual a|$root/logo.bmp|Exporta o blob **logo.bmp** no contêiner raiz
	Igual a|videos/story.mp4|Exporta o blob **story.mp4** no contêiner **vídeos**


4.	Na Etapa 4, digite um nome descritivo para o trabalho de exportação. O nome fornecido pode conter somente letras minúsculas, números, hifens e sublinhados, deve começar por letra e não pode conter espaços.

	A região do data center indicará o data center para o qual você deverá enviar seu pacote. Consulte as Perguntas Frequentes abaixo para obter mais informações.

5. 	Na Etapa 5, selecione sua operadora de retorno na lista e insira o número da conta da operadora. A Microsoft irá usar esta conta para enviar suas unidades de volta para você após a conclusão do seu trabalho de exportação.

	Se tiver um número de acompanhamento, selecione a transportadora na lista e insira o número de acompanhamento.

	Se ainda não tiver um número de controle, escolha **Fornecerei minhas informações de remessa para este trabalho de exportação após enviar meu pacote** e, em seguida, conclua o processo de exportação.

6. Para inserir o número de controle após enviar o pacote, volte à página **Importar/Exportar** da sua conta de armazenamento no Portal de Gerenciamento, selecione seu trabalho na lista e selecione **Informações de Remessa**. Navegue pelo assistente e insira o número de acompanhamento na Etapa 2.
	
	Se o número de acompanhamento não está atualizado em 2 semanas após a criação do trabalho, este irá expirar.

	Você também pode atualizar o número da conta da transportadora na Etapa 2 do assistente, caso o trabalho esteja nas fases Criando, Enviando ou Transferindo. Você não poderá atualizar o número da conta da transportadora para o trabalho em questão quando ele estiver na fase de Empacotamento.

> [AZURE.NOTE]Se o blob a ser exportado estiver em uso no momento da cópia do disco rígido, o serviço de Importação/Exportação do Azure tira um instantâneo do blob e copia o instantâneo.

## Acompanhar o status do trabalho No Portal de Gerenciamento##

Você pode acompanhar o status dos seus trabalhos de importação ou exportação no Portal de Gerenciamento. No Portal de Gerenciamento, navegue até a sua conta de armazenamento e clique na guia **Importar/Exportar**. Uma lista dos seus trabalhos será exibida na página. Você pode filtrar a lista por status do trabalho, nome do trabalho, tipo de trabalho ou número de controle.

A tabela descreve o que significa cada designação de status do trabalho:

Status do Trabalho|Descrição
---|---
Criando|Seu trabalho foi criado, mas você ainda não forneceu as informações de remessa.
Remessa|Seu trabalho foi criado e você forneceu as informações de remessa.
Transferindo|Os dados estão sendo transferidos do seu disco rígido (para um trabalho de importação) ou para o seu disco rígido (para um trabalho de exportação).
Empacotamento|A transferência dos dados foi concluída e o seu disco rígido está sendo preparado para enviar de volta para você.
Concluído|Seu disco rígido foi enviado de volta para você.


## Exibir Chaves do BitLocker para um trabalho de exportação ##

Para trabalhos de exportação, você pode exibir e copiar as chaves do BitLocker geradas pelo serviço para a sua unidade, para poder descriptografar os dados exportados ao receber as unidades do data center do Azure. No Portal de Gerenciamento, navegue até a sua conta de armazenamento e clique na guia **Importar/Exportar**. Selecione o trabalho de exportação na lista e clique no botão **Exibir Chaves**. As chaves do BitLocker serão exibidas desta forma:

![Exibir chaves do BitLocker para um trabalho de exportação][export-job-bitlocker-keys]

## Perguntas frequentes ##

### Geral

**Qual é o preço do serviço Importação/Exportação?**

- Consulte a [página de preços](http://go.microsoft.com/fwlink/?LinkId=329033) para obter informações sobre preços.

**Quanto tempo levará para importar ou exportar meus dados?**

- Levará o tempo de envio dos discos, além de várias horas por TB de dados, para copiar os dados.
 
**Quais tipos de interface têm suporte?**

- - O serviço de Importação/Exportação dá suporte a HDDs (unidades de disco rígido) internos SATA II/III de 3,5 polegadas. Você pode usar os seguintes conversores para transferir os dados nos dispositivos em USB para SATA antes da remessa:
	- Anker 68UPSATAA-02BU
	- Anker 68UPSHHDS-BU
	- Startech SATADOCK22UE 

> [AZURE.NOTE]Se tiver um conversor que não está relacionado acima, você pode tentar executar a Ferramenta de Importação/Exportação do Microsoft Azure usando seu conversor para preparar a unidade e ver se funciona, antes de adquirir um conversor com suporte.

**Se eu quiser importar ou exportar mais de 10 unidades, o que devo fazer?**

- Um trabalho de importação ou de exportação pode fazer referência a apenas 10 unidades em um único trabalho para o serviço de Importação/Exportação. Se quiser enviar mais de 10 unidades, você poderá criar vários trabalhos.

**O que acontecerá se eu enviar por engano uma unidade de disco rígido que não esteja em conformidade com os requisitos com suporte?**

- O data center do Azure devolverá a unidade que não estiver em conformidade com os requisitos com suporte. Se apenas algumas unidades no pacote atenderem aos requisitos de suporte, elas serão processadas e as unidades que não atenderem aos requisitos serão retornadas para você.

### Gerenciamento de trabalhos de importação/exportação

**O que acontece com meus trabalhos de importação e exportação se eu excluir minha conta de armazenamento do Azure?**

- Ao excluir sua conta de armazenamento, todos os trabalhos de Importação/Exportação do Azure são excluídos junto com ela.  

**Posso cancelar meu trabalho?**

- Você pode cancelar um trabalho quando seu status for Criando ou Enviando.

**Durante quanto tempo consigo exibir o status dos trabalhos concluídos no Portal de Gerenciamento?**

- Você pode exibir o status dos trabalhos concluídos por até 90 dias. Todos os trabalhos concluídos serão excluídos após 90 dias.

**A criptografia do Bitlocker é um requisito obrigatório?**

- Sim. Todas as unidades devem ser criptografadas com uma chave do BitLocker.

**Você formatar as unidades antes de retorná-las?**

- Não. Todas as unidades devem estar preparadas para BitLocker.
 
**É necessário executar alguma preparação ao disco para criar um trabalho de exportação?** - Não, mas são recomendadas algumas verificações prévias. Verifique o número de discos necessários usando o comando [PreviewExport](https://msdn.microsoft.com/library/azure/dn722414.aspx) da ferramenta Importação/Exportação do Azure. A ferramenta ajuda você a visualizar o uso da unidade para os blobs que você selecionou, com base no tamanho das unidades que você pretende usar. Verifique também se você pode ler/gravar no disco rígido que será enviado para o trabalho de exportação.

### Remessa

**Quais serviços courier têm suporte?**

- As regiões dos EUA e Europa só dão suporte ao serviço [Federal Express](http://www.fedex.com/us/oadr/) (FedEx). Todos os pacotes serão retornados via FedEx Ground ou Fedex International Economy.

- As regiões na Ásia só dão suporte ao serviço [DHL](http://www.dhl-welcome.com/Tutorial/). Todos os pacotes serão retornados via DHL Express Worldwide.

	> [AZURE.IMPORTANT]Você deve fornecer seu número de controle ao serviço de Importação/Exportação do Azure; caso contrário, seu trabalho não poderá ser processado.

**Existe algum custo associado à remessa de retorno?**

- A Microsoft utiliza o número da conta da transportadora fornecida no momento da criação do trabalho para enviar as unidades para seu endereço de devolução do data center. Certifique-se de fornecer um número de conta de transportadora válido para a transportadora com suporte na região do data center. Você pode criar uma conta de transportadora da [FedEx](http://www.fedex.com/us/oadr/) (para os EUA e a Europa) ou da [DHL](http://www.dhl-welcome.com/Tutorial/) (Ásia) se não tiver uma.

- A taxa de remessa de retorno é cobrada na conta da sua transportadora e depende da transportadora.

**De/para onde posso enviar meus dados?**

- O serviço Importar/Exportar dá suporte à importação e exportação de dados para/de contas de armazenamento nas seguintes regiões:
	- Leste dos EUA 
	- Oeste dos EUA 
	- Centro-Norte dos EUA 
	- Centro-Sul dos Estados Unidos 
	- Norte da Europa
	- Europa Ocidental
	- Ásia Oriental
	- Sudeste Asiático

- Você receberá um endereço de remessa na região onde sua conta de armazenamento está localizada. Por exemplo, se morar nos EUA e sua conta de armazenamento estiver no data center da Europa Ocidental, você receberá um endereço de remessa na Europa para envio das unidades.

	> [AZURE.IMPORTANT]Observe que a mídia física que está enviando talvez precise cruzar fronteiras internacionais. Você é responsável por garantir que seus dados e mídia física sejam importados e/ou exportados de acordo com as leis aplicáveis. Antes de enviar a mídia física, verifique com seus consultores se a mídia e os dados podem ser enviados legalmente ao data center identificado. Isso ajudará a garantir que eles cheguem à Microsoft pontualmente.

- Ao enviar seus pacotes, você deve seguir os [Termos de Serviço do Microsoft Azure](http://azure.microsoft.com/support/legal/services-terms/).

**Pode adquirir unidades para os trabalhos de importação/exportação da Microsoft?**

- 	Não. Você precisará enviar suas próprias unidades para os trabalhos de importação e exportação.

**O que devo incluir no meu pacote?**

- Envie somente seus discos rígidos. Não inclua itens como cabos de alimentação ou cabos USB.



[import-job-03]: ./media/storage-import-export-service/import-job-03.png
[export-job-03]: ./media/storage-import-export-service/export-job-03.png
[export-job-bitlocker-keys]: ./media/storage-import-export-service/export-job-bitlocker-keys.png
 

<!---HONumber=Sept15_HO2-->