<properties linkid="manage-services-import-export" urlDisplayName="Serviço de Importação e Exportação do Azure" pageTitle="Usando a importação/exportação para transferir dados para o Armazenamento de Blob" metaKeywords="" description="Saiba como criar trabalhos de importação e exportação no Portal de Gerenciamento do Microsoft Azure para transferir dados para o armazenamento de blob." metaCanonical="" disqusComments="1" umbracoNaviHide="0" title="Usando o serviço de importação e exportação do Azure para transferir dados para o Armazenamento de Blob" authors="tamram" />


# Usar o serviço de importação e exportação do Azure para transferir dados para o Armazenamento de Blob

Você pode usar o serviço de importação e exportação do Azure para transferir grandes quantidades de dados de arquivo para o armazenamento de Blob do Azure em situações onde o carregamento pela rede é extremamente dispendioso ou inviável. Você também pode usar o serviço Importar/Exportar para transferir grandes quantidades de dados residentes no armazenamento de Blob para suas instalações locais no devido tempo e de forma economicamente viável.

Para transferir um grande conjunto de dados de arquivo para o armazenamento de Blob, você pode enviar um ou mais discos rígidos contendo esses dados para um data center do Azure, onde seus dados serão carregados em sua conta de armazenamento. Da mesma forma, para exportar dados do armazenamento de Blob, você pode enviar discos rígidos vazios para um data center do Azure, onde os dados do Blob de sua conta de armazenamento serão copiados para os discos rígidos e, em seguida, devolvidos para você. Antes de enviar uma unidade contendo dados, você criptografará os dados na unidade; quando o serviço Importar/Exportar exportar seus dados para enviar para você, os dados também serão criptografados antes do envio.

Você pode criar e gerenciar trabalhos de importação e exportação de duas maneiras:

- Usando o Portal de Gerenciamento do Azure.
- Usando uma interface REST para o serviço.

Este artigo apresenta uma visão geral do serviço Importar/Exportar e descreve como usar o Portal de Gerenciamento para funcionar com esse serviço. Para obter informações sobre a API REST, consulte a [Referência da API REST do serviço de importação e exportação do Azure](http://go.microsoft.com/fwlink/?LinkID=329099).

## Visão geral do serviço Importar/Exportar ##

Para começar o processo de importação para o armazenamento de Blob ou de exportação desse armazenamento, primeiro crie um *trabalho*, que pode ser um *trabalho de importação* ou um *trabalho de exportação*:

- Crie um trabalho de importação quando desejar transferir dados locais para blobs na sua conta de armazenamento do Azure.
- Crie um trabalho de exportação quando desejar transferir os dados armazenados atualmente como blobs na sua conta de armazenamento para discos rígidos que são enviados a você.

Ao criar um trabalho, você notifica o serviço de Importação/Exportação de que enviará um ou mais discos rígidos para um data center do Azure. Para um trabalho de importação, você enviará discos rígidos contendo dados de arquivo. Para um trabalho de exportação, você enviará discos rígidos vazios.

Para preparar seu disco rígido para envio para um trabalho de importação, você executará a ferramenta **WAImportExport**, que facilita a cópia dos seus dados para a unidade, criptografando os dados na unidade com BitLocker e gerando os arquivos de diário de unidade, que serão discutidos a seguir.

<div class="dev-callout">
<strong>Observação</strong>
<p>Os dados na unidade devem ser criptografados com a Criptografia de Unidade de Disco BitLocker. Isso protegerá os dados enquanto eles estiverem em trânsito. Para um trabalho de exportação, o serviço Importar/Exportar criptografará seus dados antes de enviar a unidade de volta para você.</p>
</div>

Ao criar um trabalho de importação ou de exportação, você também precisará da *ID da unidade*, que é o número de série atribuído pelo fabricante da unidade a um disco rígido específico. A ID de unidade é exibida na parte externa da unidade. 

<h3>Requisitos e escopo</h3>

1.	**Assinatura e contas de armazenamento:** você deve ter uma assinatura do Azure e uma ou mais contas de armazenamento existentes para usar o serviço de Importação/Exportação. Cada trabalho pode ser usado para transferir dados para ou a partir de apenas uma conta de armazenamento. Em outras palavras, um trabalho não pode se estender por várias contas de armazenamento. Para obter informações sobre como criar uma nova conta de armazenamento, consulte [Como criar uma conta de armazenamento](http://www.windowsazure.com/pt-br/manage/services/storage/how-to-create-a-storage-account/).
2.	**Discos rígidos:** somente discos rígidos SATA II de 3,5 polegadas têm suporte para uso com o serviço Importar/Exportar. Não há suporte para discos rígidos com mais de 4 TB com a versão de teste. Para trabalhos de importação, somente o primeiro volume de dados na unidade será processado. O volume de dados deve ser formatado com NTFS. Você pode anexar um disco SATA II externamente a maioria dos computadores usando um adaptador USB para SATA II.
3.	**Criptografia BitLocker:** todos os dados armazenados em discos rígidos devem ser criptografados usando o BitLocker com chaves de criptografia protegidas com senhas numéricas.
4.	**Destinos de armazenamento de blob:** é possível carregar dados para blobs de blocos e blobs de páginas e baixar dados desses blobs. 
5.	**Número de trabalhos:** um cliente pode ter até 20 trabalhos ativos por assinatura.
6.	**Tamanho máximo de um trabalho:** o tamanho de um trabalho é determinado pela capacidade dos discos rígidos usados e a quantidade máxima de dados que podem ser armazenados em uma conta de armazenamento. Cada trabalho pode conter, no máximo, 10 discos rígidos.

## Criar um trabalho de importação no Portal de Gerenciamento##

Crie um trabalho de importação para notificar o serviço Importar/Exportar de que você enviará uma ou mais unidades contendo dados para o data center e que eles deverão ser importados para a sua conta de armazenamento.

<h3>Preparar suas unidades</h3>

Antes de criar um trabalho de importação, prepare suas unidades com a [ferramenta WAImportExport](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409). Para obter mais detalhes sobre como usar a ferramenta de WAImportExport, consulte [WAImportExport ferramenta de referência](http://go.microsoft.com/fwlink/?LinkId=329032)
  
Para preparar as unidades, siga estas três etapas: 

1.	Determine os dados a serem importados e o número de unidades necessárias.
2.	Identifique os blobs de destino de seus dados no serviço Blob do Azure.
3.	Use a ferramenta WAImportExport para copiar seus dados para um ou mais discos rígidos.

A ferramenta WAImportExport gera um arquivo de *diário de unidade* para cada unidade à medida que ela for preparada. O arquivo de diário de unidade é armazenado no seu computador local, e não na própria unidade. Você poderá carregar o arquivo de diário ao criar o trabalho de importação. Um arquivo de diário de unidade inclui a ID de unidade e a chave do BitLocker, bem como outras informações sobre a unidade.  

<h3>Criar o trabalho de importação</h3>

1.	Depois de preparar a unidade, navegue até a conta de armazenamento no Portal de Gerenciamento e exiba o 	Painel. Em <strong>Visão Rápida</strong>, clique em <strong>Criar um Trabalho de Importação</strong>. 
 
2.	Na Etapa 1 do assistente, indique que você preparou a unidade e que tem o arquivo de diário de unidade 	disponível.
 
3.	Na Etapa 2, forneça as informações de contato da pessoa responsável por esse trabalho de importação. Se desejar salvar 	dados de log detalhados para o trabalho de importação, marque a opção para <strong>Salvar o log detalhado no meu contêiner de blob 'waimportexport' 	</strong>.

4.	Na Etapa 3, carregue os arquivos de diário de unidade obtidos durante a etapa de preparação de unidade. Você precisará 	carregar um arquivo para cada unidade preparada.

	![Criar o trabalho de importação -  Etapa 3][import-job-03]

5.	Na Etapa 4, digite um nome descritivo para o trabalho de importação. Observe que o nome somente poderá conter 	letras minúsculas, números, hifens e sublinhados, deverá começar com letra e não poderão conter espaços. 	Você usará o nome escolhido para acompanhar os trabalhos enquanto eles estiverem em andamento e quando eles estiverem concluídos.

	A região do data center indicará o data center para o qual você deverá enviar seu pacote. Consulte as Perguntas Frequentes abaixo para obter mais informações.

	Se já tiver o número de controle da FedEx, selecione <strong>Tenho meu número de controle e quero inseri-lo agora</strong>e vá para a próxima etapa. Se ainda não tiver um número de controle, escolha <strong>Fornecerei minhas informações de remessa para este trabalho de importação após enviar meu pacote</strong> e, em seguida, conclua o processo de importação.

6. 	Se já tiver o número de controle, na Etapa 5, insira o número de controle e confirme-a. 

## Criar um trabalho de exportação no Portal de Gerenciamento##

Crie um trabalho de exportação para notificar o serviço Importar/Exportar que você enviará uma ou mais unidades vazias para o data center, para que os dados podem ser exportados de sua conta de armazenamento para as unidades e, em seguida, as unidades sejam enviadas para você.

1. 	Para criar um trabalho de exportação, navegue até a sua conta de armazenamento no Portal de Gerenciamento e exiba o Painel. 	Em <strong>Visão Rápida</strong>, clique em <strong>Criar um Trabalho de Exportação</strong>e continue com o 	assistente.

2. 	Na Etapa 2, forneça as informações de contato da pessoa responsável por esse trabalho de exportação. Se desejar salvar 	dados de log detalhados para o trabalho de exportação, marque a opção para <strong>Salvar o log detalhado no meu contêiner de blob 'waimportexport' 	</strong>.

3.	Na Etapa 3, especifique quais dados de blob deseja exportar da sua conta de armazenamento para a(s) unidade(s) em branco 	. Você pode optar por exportar todos os dados de blob na conta de armazenamento ou especificar quais blobs 	ou 	conjuntos de blobs serão exportados.

	![Criar o trabalho de exportação - Etapa 3][export-job-03]

	- Para especificar um blob para exportação, use o seletor **Igual a** e especifique o caminho relativo do blob, começando pelo nome do contêiner. Use *$root* para especificar o contêiner raiz.
	- Para especificar todos os blob que começam com um prefixo, use o seletor **Começa com** e especifique o prefixo, começando com uma barra '/'. O prefixo pode ser do nome do contêiner, o nome do contêiner completo ou o nome do contêiner completo seguido do prefixo do nome do blob.

	A tabela mostra exemplos de caminhos de blob válidos:

	<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
		<tbody>
			<tr>
				<td><strong>Seletor</strong></td>
				<td><strong>Caminho do Blob</strong></td>
				<td><strong>Descrição</strong></td>
			</tr>
			<tr>
				<td>Começa com</td>
				<td>/</td>
				<td>Exporta todos os blobs na conta de armazenamento</td>
			</tr>
			<tr>
				<td>Começa com</td>
				<td>/$root/</td>
				<td>Exporta todos os blobs no contêiner raiz</td>
			</tr>
			<tr>
				<td>Começa com</td>
				<td>/book</td>
				<td>Exporta todos os blobs em qualquer contêiner que comece com o prefixo <strong>book</strong></td>
			</tr>
			<tr>
				<td>Começa com</td>
				<td>/music/</td>
				<td>Exporta todos os blobs no contêiner <strong>music</strong></td>
			</tr>
			<tr>
				<td>Começa com</td>
				<td>/music/love</td>
				<td>Exporta todos os blobs no contêiner <strong>music</strong> que comecem com o prefixo <strong>love</strong></td>
			</tr>
			<tr>
				<td>Igual a</td>
				<td>$root/logo.bmp</td>
				<td>Exporta o blob <strong>logo.bmp</strong> no contêiner raiz</td>
			</tr>
			<tr>
				<td>Igual a</td>
				<td>videos/story.mp4</td>
				<td>Exporta o blob <strong>story.mp4</strong> no contêiner <strong>videos</strong></td>
			</tr>
		</tbody>
	</table>


4.	Na Etapa 4, digite um nome descritivo para o trabalho de exportação. O nome digitado somente poderá conter 	letras minúsculas, números, hifens e sublinhados, deverá começar com letra e não poderão conter espaços.

	A região do data center indicará o data center para o qual você deverá enviar seu pacote. Consulte as Perguntas Frequentes abaixo para obter mais informações.

	Se já tiver o número de controle da FedEx, selecione <strong>Tenho meu número de controle e quero inseri-lo agora</strong>e vá para a próxima etapa. Se ainda não tiver um número de controle, escolha <strong>Fornecerei minhas informações de remessa para este trabalho de exportação após enviar meu pacote</strong> e, em seguida, conclua o processo de importação.

5. Na Etapa 5, insira seu número de controle e, em seguida, confirme-o. 


## Acompanhar o status do trabalho No Portal de Gerenciamento##

Você pode acompanhar o status dos seus trabalhos de importação ou exportação no Portal de Gerenciamento. No Portal de Gerenciamento, navegue até a sua conta de armazenamento e clique na guia **Importar/Exportar**. Uma lista dos seus trabalhos será exibida na página. Você pode filtrar a lista por status do trabalho, nome do trabalho, tipo de trabalho ou número de controle.

A tabela descreve o que significa cada designação de status do trabalho:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
	<tbody>
		<tr>
			<td><strong>Status do Trabalho</strong></td>
			<td><strong>Descrição</strong></td>
		</tr>
		<tr>
			<td>Criando</td>
			<td>Seu trabalho foi criado, mas você ainda não forneceu as informações de remessa.</td>
		</tr>
		<tr>
			<td>Remessa</td>
			<td>Seu trabalho foi criado e você forneceu as informações de remessa.</td>
		</tr>
		<tr>
			<td>Transferindo</td>
			<td>Os dados estão sendo transferidos do seu disco rígido (para um trabalho de importação) ou para o seu disco rígido (para um trabalho de exportação).</td>
		</tr>
		<tr>
			<td>Empacotamento</td>
			<td>A transferência dos dados foi concluída e o seu disco rígido está sendo preparado para enviar de volta para você.</td>
		</tr>
		<tr>
			<td>Concluído</td>
			<td>Seu disco rígido foi enviado de volta para você.</td>
		</tr>
	</tbody>
</table>

## Exibir Chaves do BitLocker para um trabalho de exportação ##

Para trabalhos de exportação, você pode exibir e copiar as chaves do BitLocker geradas pelo serviço para a sua unidade, para poder descriptografar os dados exportados ao receber as unidades do data center do Azure. No Portal de Gerenciamento, navegue até a sua conta de armazenamento e clique na guia **Importar/Exportar**. Selecione o trabalho de exportação na lista e clique no botão **Exibir Chaves**. As chaves do BitLocker serão exibidas desta forma:

![Exibir chaves do BitLocker para um trabalho de exportação][export-job-bitlocker-keys]

## Perguntas frequentes ##

<h3>Geral</h3>

**Qual é o preço do serviço Importar/Exportar?**

- Consulte a [página de preços](http://go.microsoft.com/fwlink/?LinkId=329033) para obter informações sobre preços.

**Quanto tempo levará para importar ou exportar meus dados?**

- Levará o tempo de envio dos discos, além de várias horas por TB de dados para copiar os dados.
 
**Quais tipos de interface têm suporte?**

- O serviço de Importação/Exportação oferece suporte a HDDs (unidades de disco rígido) SATA II de 3,5 polegadas. Você pode usar os seguintes conversores para transferir os dados nos dispositivos em USB para SATA antes da remessa:
	- Anker 68UPSATAA-02BU
	- Anker 68UPSHHDS-BU
	- Startech SATADOCK22UE 

**Se eu quiser importar ou exportar mais de 10 unidades, o que devo fazer?**

- Um trabalho de importação ou de exportação poderá fazer referência a apenas 10 unidades em um único trabalho durante a versão de visualização do serviço de Importação/Exportação. Se quiser enviar mais de 10 unidades, você poderá criar vários trabalhos.

**O que acontecerá se eu enviar por engano uma unidade de disco rígido que não esteja de acordo com os requisitos com suporte?**

- O data center do Azure devolverá a unidade que não esteja de acordo com os requisitos com suporte. Se apenas algumas unidades no pacote atenderem aos requisitos de suporte, elas serão processadas e as unidades que não atenderem aos requisitos serão retornadas para você.

<h3>Gerenciamento de trabalhos de importação/exportação</h3>

**O que acontece com meus trabalhos de importação e exportação se eu excluir minha conta de armazenamento do Azure?**

- Quando você exclui sua conta de armazenamento, todos os trabalhos de Importação/Exportação do Azure são excluídos junto com sua conta.  

**Posso cancelar meu trabalho?**

- Você pode cancelar um trabalho quando seu status for Criando ou Remessa.

**Durante quanto tempo consigo exibir o status dos trabalhos concluídos no Portal de Gerenciamento?**

- Você pode exibir o status dos trabalhos concluídos por até 90 dias. Todos os trabalhos concluídos serão arquivados após esse período.  Se precisar recuperar o status do trabalho concluído após 90 dias, você poderá contatar o atendimento ao cliente.

<h3>Remessa</h3>

**Quais serviços courier têm suporte?**

- 	Apenas o serviço FedEx (Federal Express) terá suporte durante a versão de teste.
- 	O(s) pacote (s) referente(s) a um trabalho de importação poderá(ão) ser enviado(s) por meio de FedEx Express ou FedEx Ground.
- 	Todos os pacotes serão retornados via FedEx Ground.

	<div class="dev-callout">
	<strong>Importante</strong>
	<p>Você deve fornecer seu número de controle ao serviço de Importação/Exportação do Azure; caso contrário, seu trabalho não poderá ser processado.</p>
	</div>

**Existe algum custo associado à remessa de retorno?**

- A remessa de devolução é gratuita durante a versão de visualização.

**De/para onde posso enviar meus dados?**

- O serviço de Importação/Exportação só pode aceitar remessas <strong>originadas</strong> de locais nos EUA e só pode devolver pacotes para endereços nos EUA. O serviço oferece suporte à importação/exportação de dados para/de contas de armazenamento nas seguintes regiões:
	- Leste dos EUA 
	- Oeste dos EUA 
	- Centro-Norte dos EUA 
	- Centro-Sul dos Estados Unidos 
	- Norte da Europa
	- Europa Ocidental
	- Ásia Oriental
	- Sudeste Asiático

- Se a sua conta de armazenamento residir em um data center nos EUA, você talvez precise enviar suas unidades a um data center em outra região, pois atualmente nem todos os data centers oferecem suporte ao serviço de Importação/Exportação. Você poderá incorrer em encargos de saída se o seu trabalho for processado em uma região diferente do local em que reside sua conta de armazenamento.

- Se a sua conta de armazenamento residir em um data center europeu ou asiático, você deverá enviar sua unidade para uma das regiões com suporte nos EUA e a remessa deverá ser enviada de dentro dos EUA. O serviço Importação/Exportação copiará os dados para/da sua conta de armazenamento na Europa ou na Ásia.  
	- Para um trabalho de importação, não haverá nenhum custo de entrada para a operação de cópia.
	- Para um trabalho de exportação, haverá taxas de transferências de dados para a cópia de dados entre data centers do Azure. Por exemplo, se a sua conta de armazenamento residir na Europa Ocidental e você enviar sua unidade para o data center no Leste dos EUA, você precisará pagar encargos de saída para mover os dados da Europa Ocidental para o Leste dos EUA e exportá-los.

	<div class="dev-callout">
	<strong>Importante</strong>
	<p>Os data centers do Azure não podem receber unidades fornecidas de locais fora dos EUA e recusarão a entrega desses pacotes.</p>
	</div>

**Pode adquirir unidades para os trabalhos de importação/exportação da Microsoft?**

- 	Não. Você precisará enviar suas próprias unidades para os trabalhos de importação e exportação.

<h3>Segurança</h3>

**A criptografia do Bitlocker é um requisito obrigatório?**

- Sim. Todas as unidades devem ser criptografadas com uma chave do BitLocker.

**Você formatar as unidades antes de retorná-las?**

- Não. Todas as unidades devem ser preparadas pelo BitLocker.


[import-job-03]: ./media/storage-import-export-service/import-job-03.png
[export-job-03]: ./media/storage-import-export-service/export-job-03.png
[export-job-bitlocker-keys]: ./media/storage-import-export-service/export-job-bitlocker-keys.png

