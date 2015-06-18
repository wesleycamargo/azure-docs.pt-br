<properties 
	pageTitle="Como usar o AzCopy com o armazenamento do Microsoft Azure" 
	description="Saiba como usar o utilitário AzCopy para carregar, baixar e copiar blobs e conteúdo de arquivos." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor="cgronlun"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/05/2015" 
	ms.author="tamram"/>

# Introdução ao utilitário de linha de comando AzCopy

AzCopy é um utilitário de linha de comando projetado para upload, download e cópia de dados de alto desempenho do e para o armazenamento de blob, arquivo e tabela do Microsoft Azure. Este guia apresenta uma visão geral do uso do AzCopy.

> [AZURE.NOTE] Este guia pressupõe que você tenha o AzCopy 3.1.0 ou posterior instalado. AzCopy 3.x agora está em disponibilidade geral.
> 
> Este guia também aborda o uso do AzCopy 4.1.0, que é uma versão de visualização do AzCopy. Ao longo deste guia, funções fornecidas apenas na versão de visualização são designadas como  *visualização*.
> 
> Observe que, para o AzCopy 4.x, as opções de linha de comandos e funcionalidades podem ser alteradas em versões futuras.

##Sumário

- [Baixar e instalar o AzCopy](#install)
- [Entenda a sintaxe da linha de comando do AZCopy](#syntax)
- [Limite gravações simultâneas durante a cópia de dados](#limit-writes)
- [Copie blobs do Azure com o AzCopy](#copy-blobs)
- [Copie arquivos em um compartilhamento de arquivos do Azure com o AzCopy &#40;somente na versão de teste&#41;](#copy-files)
- [Copie entidades de tabela do Azure somente com a versão de visualização do AzCopy &#40;&#41;](#copy-entities)
- [Versões do AzCopy](#versions)
- [Próximas etapas](#next-steps)

##<a id="install"></a>Baixar e instalar o AzCopy

1. Baixe a [versão mais recente do AzCopy](http://aka.ms/downloadazcopy) ou a [última versão de teste](http://aka.ms/downloadazcopypr).
2. Execute a instalação. Por padrão, a instalação do AzCopy cria uma pasta chamada  `AzCopy` em  `%ProgramFiles(x86)%\Microsoft SDKs\Azure\` (em um computador sendo executado no Windows 64 bits) ou  `%ProgramFiles%\Microsoft SDKs\Azure\` (em um computador sendo executado no Windows 32 bits). No entanto, é possível alterar o caminho da instalação por meio do assistente de instalação.
3. Se quiser, você pode alterar o local da instalação do AzCopy para o caminho do sistema.

##<a id="syntax"></a>Entenda a sintaxe da linha de comando do AZCopy

Em seguida, abra uma janela de comando e navegue até o diretório de instalação do AzCopy no computador, onde o executável  `AzCopy.exe` está localizado. A sintaxe básica dos comandos do AzCopy é:

	AzCopy /Source:<source> /Dest:<destination> /Pattern:<filepattern> [Options]

> [AZURE.NOTE] A partir da versão 3.0.0 do AzCopy, a sintaxe da linha de comando do AzCopy requer que todos os parâmetros sejam especificados para incluir o nome do parâmetro, *e.g.*, `/ParameterName:ParameterValue`.

Os parâmetros do AzCopy são descritos na tabela abaixo. Também é possível digitar um dos comandos a seguir na linha de comando para obter ajuda no uso do AzCopy:

- Para obter ajuda detalhada da linha de comando para o AzCopy: `AzCopy /?`
- Para obter ajuda detalhada para qualquer parâmetro do AzCopy: `AzCopy /?:SourceKey`
- Para exemplos da linha de comando: `AzCopy /?:Samples` 


<table>
  <tr>
    <th>Nome da opção</th>
    <th>Descrição</th>
    <th>Aplicável ao armazenamento de blob (S/N)</th>
    <th>Aplicável ao armazenamento de arquivo (S/N) (somente na versão de teste)</th>
    <th>Aplicável ao armazenamento de tabela (S/N) (somente na versão de teste)</th>
  </tr>
  <tr>
    <td><b>/Source:&lt;origem&gt;</b></td>
    <td>Especifica os dados de origem para cópia. A origem pode ser um diretório do sistema de arquivos, um contêiner de blob, um diretório virtual de blob, um compartilhamento de arquivos de armazenamento, um diretório de arquivos de armazenamento ou uma tabela do Azure.</td>
    <td>S</td>
    <td>S<br /> (somente visualização)</td>
    <td>S<br /> (somente visualização)</td>
  </tr>
  <tr>
    <td><b>/Dest:&lt;destino&gt;</b></td>
    <td>Especifica o destino da cópia. O destino pode ser um diretório do sistema de arquivos, um contêiner de blob, um diretório virtual de blob, um compartilhamento de arquivos de armazenamento, um diretório de arquivos de armazenamento ou uma tabela do Azure.</td>
    <td>S</td>
    <td>S<br /> (somente visualização)</td>
    <td>S<br /> (somente visualização)</td>
  </tr>
  <tr>
    <td><b>/Pattern:&lt;padrão de arquivo&gt;</b></td>
      <td>
          Especifica um padrão de arquivo que indica quais arquivos devem ser copiados. O comportamento do parâmetro /Pattern é determinado pelo local dos dados de origem e pela presença da opção do modo recursivo. O modo recursivo é especificado pela opção /S.
          <br />
          Se a origem especificada for um diretório no sistema de arquivos, os curingas padrão estão em vigor, e o padrão de arquivo fornecido é comparado com os arquivos dentro do diretório. Se a opção /S for especificada, o AzCopy também compara o padrão especificado com todos os arquivos em todas as subpastas do diretório.
          <br />
          Se a origem especificada for um contêiner de blob ou um diretório virtual, os curingas não são aplicados. Se a opção &nbsp;/S&nbsp;for especificada, o AzCopy interpreta o padrão do arquivo especificado como um prefixo de blob. Se a opção &nbsp;/S&nbsp; não for especificada, o AzCopy compara o padrão do arquivo com os nomes de blob exatos.
          <br />
          Se a origem especificada for um compartilhamento de arquivos do Azure, você deve especificar o nome exato do arquivo (por exemplo, &nbsp;abc.txt) para copiar um único arquivo ou especificar a opção &nbsp;/S&nbsp; para copiar todos os arquivos recursivamente no compartilhamento. A tentativa de especificar, ao mesmo tempo, um padrão de arquivo e uma opção /S&nbsp; resultará em erro.
          <br />
          O padrão de arquivo usado quando nenhum padrão de arquivo é especificado é *.* para um local do sistema de arquivos, ou um prefixo vazio para um local de armazenamento do Azure. Não é possível especificar diversos padrões para os arquivos.</td>
    <td>S</td>
    <td>S<br /> (somente visualização)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/DestKey:&lt;chave de armazenamento&gt;</b></td>
    <td>Especifica a chave de conta de armazenamento do recurso de destino.</td>
    <td>S</td>
    <td>S<br /> (somente visualização)</td>
    <td>S<br /> (somente visualização)</td>
  </tr>
  <tr>
    <td class="auto-style1"><b>/DestSAS:&lt;token sas&gt;</b></td>
    <td class="auto-style1">Especifica uma SAS (Assinatura de Acesso Compartilhado) para o contêiner de destino (se aplicável). Coloque a SAS entre aspas duplas, porque ela pode conter caracteres de linha de comando especiais..<br />
        Se o recurso de destino for um blob, um contêiner ou uma tabela será possível especificar essa opção seguida do token da SAS ou a SAS como parte do URI do blob de destino, sem essa opção.<br />
        Se a origem e o destino forem blobs, ambos devem residir na mesma conta de armazenamento.</td>
    <td class="auto-style1">S</td>
    <td class="auto-style1">N</td>
    <td class="auto-style1">S<br /> (somente visualização)</td>
  </tr>
  <tr>
    <td><b>/SourceKey:&lt;chave de armazenamento&gt;</b></td>
    <td>Especifica a chave de conta de armazenamento para o recurso de origem.</td>
    <td>S</td>
    <td>S<br /> (somente visualização)</td>
    <td>S<br /> (somente visualização)</td>
  </tr>
  <tr>
    <td><b>/SourceSAS:&lt;token sas&gt;</b></td>
    <td>Especifica uma Assinatura de Acesso Compartilhado para o contêiner de origem (se aplicável). Coloque a SAS entre aspas duplas, porque ela pode conter caracteres de linha de comando especiais.
        <br />
        Se o recurso da fonte for um contêiner de blob e não for fornecida uma chave nem uma SAS, o contêiner será lido por meio de acesso anônimo.
        <br />
        Se a fonte for uma tabela, chave ou SAS, ela deverá ser fornecida.</td>
    <td>S</td>
    <td>N</td>
    <td>S<br /> (somente visualização)</td>
  </tr>
  <tr>
    <td><b>/S</b></td>
    <td>Especifica o modo recursivo para operações de cópia. No modo recursivo, o AzCopy copiará todos os blobs ou arquivos correspondentes ao padrão do arquivo, inclusive os presentes nas subpastas.</td>
    <td>S</td>
    <td>Y<br /> (somente visualização)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/BlobType:&lt;bloco | página&gt;</b></td>
    <td>Especifica se o destino é um blob de blocos ou um blob de páginas. Essa opção só é aplicável no carregamento de blobs. Do contrário, um erro é gerado. Se o destino for um blob e essa opção não estiver especificada, por padrão, o AzCopy criará um blob de blocos.</td>
    <td>S</td>
    <td>N</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/CheckMD5</b></td>
    <td>Calcula um hash MD5 para dados baixados e certifica que o hash MD5 armazenado no blob, ou a propriedade Content-MD5 do arquivo, corresponde ao hash calculado. Como, por padrão, a verificação MD5 permanece desativada, você deve especificar essa opção para realizar a verificação MD5 ao baixar os dados.
	<br />
    O armazenamento do Azure não assegura que o hash MD5 armazenado para o blob ou o arquivo esteja atualizado. É de responsabilidade do cliente atualizar o MD5 sempre que o blob ou o arquivo é modificado.
	<br />
    O AzCopy sempre define a propriedade Content-MD5 para um blob ou um arquivo do Azure depois de carregá-lo no serviço.</td>
    <td>S</td>
    <td>S<br /> (somente visualização)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/Snapshot</b></td>
    <td>Indica se é necessário transferir ou não os instantâneos. Essa opção só é válida quando a origem é um blob. 
        <br />
        Os instantâneos de blob transferidos são renomeados neste formato: [blob-name] (snapshot-time)[extensão]. 
        <br />
        Por padrão, os instantâneos não são copiados.</td>
    <td>S</td>
    <td>N</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/V:[verbose log-file]</b></td>
    <td>Produz mensagens de status detalhadas em um arquivo de log. Por padrão, o arquivo de log detalhado é chamado de <code>AzCopyVerbose.log</code> em <code>%LocalAppData%\Microsoft\Azure\AzCopy</code>. Se você especificar um local de arquivo existente para essa opção, o log detalhado será acrescentado a esse arquivo.</td>
    <td>S</td>
    <td>S<br /> (somente visualização)</td>
    <td>S<br /> (somente visualização)</td>
  </tr>
  <tr>
    <td><b>/Z:[journal-file-folder]</b></td>
    <td>Especifica uma pasta de arquivo de diário para retomar uma operação.<br />
        O AzCopy sempre dará suporte à retomada caso uma operação tenha sido interrompida.<br />
        Se essa opção não for especificada ou for especificada sem um caminho de pasta, o AzCopy criará o arquivo de diário no local padrão, que é <code>%LocalAppData%\Microsoft\Azure\AzCopy</code>.<br />
        Sempre que você emite um comando para o AzCopy, ele verifica se um arquivo de diário existe na pasta padrão ou se está em uma pasta especificada por meio dessa opção. Se o arquivo de diário não estiver em nenhum dos lugares, o AzCopy tratará a operação como nova e gerar um novo arquivo de diário.
        <br />
		Se o arquivo de diário existir, o AzCopy verificará se a linha de comando inserida corresponde à linha de comando no arquivo de diário. Se as duas linhas de comando forem correspondentes, o AzCopy retomará a operação incompleta. Se elas não forem correspondentes, será solicitado que você substitua o arquivo de diário para iniciar uma nova operação ou que cancele a operação atual. 
        <br />
        O arquivo de diário é excluído mediante a conclusão bem-sucedida da operação.
		<br />
		A retomada de uma operação de um arquivo de diário criado por uma versão anterior do AzCopy não é compatível.</td>
    <td>S</td>
    <td>S<br /> (somente visualização)</td>
    <td>S<br /> (somente visualização)</td>
  </tr>
  <tr>
    <td><b>/@:parameter-file</b></td>
    <td>Especifica um arquivo que contém parâmetros. O AzCopy processa os parâmetros no arquivo como se eles tivessem sido especificados na linha de comando.<br /> 
		Em um arquivo de resposta, é possível especificar vários parâmetros em um único arquivo ou especificar cada parâmetro na própria linha. Um parâmetro individual não pode abranger várias linhas. 
        <br />
		Os arquivos de resposta podem incluir linhas de comentários iniciadas pelo símbolo <code>#</code>. 
        <br />
        É possível especificar vários arquivos de resposta. No entanto, o AzCopy não permite arquivos de resposta aninhados.</td>
    <td>S</td>
    <td>S<br /> (somente visualização)</td>
    <td>S<br /> (somente visualização)</td>
  </tr>
  <tr>
    <td><b>/Y</b></td>
    <td>Suprime todas as solicitações de confirmação do AzCopy.</td>
    <td>S</td>
    <td>S<br /> (somente visualização)</td>
    <td>S<br /> (somente visualização)</td>
  </tr>
  <tr>
    <td><b>/L</b></td>
    <td>Especifica uma operação de listagem apenas. Nenhum dado é copiado.</td>
    <td>S</td>
    <td>S<br /> (somente visualização)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/MT</b></td>
    <td>Define a hora da última modificação do arquivo baixado como a mesma do blob de origem ou do arquivo.</td>
    <td>S</td>
    <td>S<br /> (somente visualização)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/XN</b></td>
    <td>Exclui um recurso de origem mais novo. O recurso não será copiado se a origem for mais nova do que o destino.</td>
    <td>S</td>
    <td>S<br /> (somente visualização)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/XO</b></td>
    <td>Exclui um recurso de origem mais antigo. O recurso não será copiado se o recurso de origem for mais antigo do que o destino.</td>
    <td>S</td>
    <td>S<br /> (somente visualização)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/A</b></td>
    <td>Carrega apenas arquivos que tenham o atributo Archive definido.</td>
    <td>S</td>
    <td>S<br /> (somente visualização)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/IA:[RASHCNETOI]</b></td>
    <td>Carrega apenas arquivos que tenham algum dos atributos especificados definido.<br />
        Entre os atributos disponíveis estão:  
        <br />
        R&nbsp;&nbsp;&nbsp;Arquivos somente leitura
        <br />
        A&nbsp;&nbsp;&nbsp;Arquivos prontos para arquivamento
        <br />
        S&nbsp;&nbsp;&nbsp;Arquivos de sistema
        <br />
        H&nbsp;&nbsp;&nbsp;Arquivos ocultos
        <br />
        C&nbsp;&nbsp;&nbsp;Arquivo compactado
        <br />
        N&nbsp;&nbsp;&nbsp;Arquivos normais
        <br />
        E&nbsp;&nbsp;&nbsp;Arquivos criptografados
        <br />
        T&nbsp;&nbsp;&nbsp;Arquivos temporários
        <br />
        O&nbsp;&nbsp;&nbsp;Arquivos offline
        <br />
        I&nbsp;&nbsp;&nbsp;Arquivos não indexados</td>
    <td>S</td>
    <td>S<br /> (somente visualização)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/XA:[RASHCNETOI]</b></td>
    <td>Exclui arquivos que tenham qualquer dos atributos especificados definido..<br />
        Entre os atributos disponíveis estão:  
        <br />
        R&nbsp;&nbsp;&nbsp;Arquivos somente leitura  
        <br />
        A&nbsp;&nbsp;&nbsp;Arquivos prontos para arquivamento  
        <br />
        S&nbsp;&nbsp;&nbsp;Arquivos de sistema  
        <br />
        H&nbsp;&nbsp;&nbsp;Arquivos ocultos  
        <br />
        C&nbsp;&nbsp;&nbsp;Arquivo compactado  
        <br />
        N&nbsp;&nbsp;&nbsp;Arquivos normais  
        <br />
        E&nbsp;&nbsp;&nbsp;Arquivos criptografados  
        <br />
        T&nbsp;&nbsp;&nbsp;Arquivos temporários  
        <br />
        O&nbsp;&nbsp;&nbsp;Arquivos offline  
        <br />
        I&nbsp;&nbsp;&nbsp;Arquivos não indexados</td>
    <td>S</td>
    <td>S<br /> (somente visualização)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/Delimiter:&lt;delimitador&gt;</b></td>
    <td>Indica o caractere delimitador usado para delimitar diretórios virtuais em um nome de blob.<br />
        Por padrão, o AzCopy usa / como o caractere delimitador. No entanto, o AzCopy dá suporte ao uso de qualquer caractere comum (como @, # ou %) como delimitador. Se precisar incluir um desses caracteres especiais na linha de comando, coloque o nome do arquivo entre aspas duplas. 
        <br />
        Essa opção só é aplicável para o download de blobs.</td>
    <td>S</td>
    <td>N</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/NC:&lt;número de operações simultâneas&gt;</b></td>
    <td>Especifica o número de operações simultâneas.
        <br />
        Por padrão, o AzCopy inicia uma determinada quantidade de operações simultâneas para aumentar a taxa de transferência de dados. O grande número de operações simultâneas em um ambiente com baixa largura de banda pode sobrecarregar a conexão de rede, evitando que as operações sejam concluídas. Limite operações simultâneas com base na largura de banda real de rede disponível.
        <br />
		O limite máximo de operações simultâneas é 512.</td>
    <td>S</td>
    <td>S<br /> (somente visualização)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/SourceType:Blob|Table</b></td>
    <td>Especifica se o recurso <code>origem</code> é um blob disponível no ambiente de desenvolvimento local, em execução no emulador de armazenamento.</td>
    <td>S</td>
    <td>N</td>
    <td>S<br /> (somente visualização)</td>
  </tr>
  <tr>
    <td><b>/DestType:Blob|Table</b></td>
    <td>Especifica se o recurso<code>destino</code>  é um blob disponível no ambiente de desenvolvimento local, em execução no emulador de armazenamento.</td>
    <td>S</td>
    <td>N</td>
    <td>S<br /> (somente visualização)</td>
  </tr>
  <tr>
    <td><strong>/PKRS:&lt;&quot;key1#key2#key3#...&quot;&gt;</strong></td>
    <td>Divide o intervalo de chaves de partição para possibilitar a exportação paralela dos dados, o que aumenta a velocidade dessa operação.
        <br />
        Se essa opção não for especificada, o AzCopy usa um único thread para exportar entidades de tabela. Por exemplo, se o usuário especificar /PKRS:&quot;aa#bb&quot;, então o AzCopy inicia três operações simultâneas.
        <br />
        Cada operação exporta um dos três intervalos de chaves de partição, como mostramos abaixo: 
        <br />
        &nbsp;&nbsp;&nbsp;&#91;&lt;primeira chave de partição&gt;, aa&#41; 
        <br />
        &nbsp;&nbsp;&nbsp;&#91;aa, bb&#41;
        <br />
        &nbsp;&nbsp;&nbsp;&#91;bb, &lt;última chave de partição&gt;&#93; </td>
    <td>N</td>
    <td>N</td>
    <td>S<br /> (somente visualização)</td>
  </tr>
  <tr>
    <td><strong>/SplitSize:</strong><file-size><strong>&lt;tamanho do arquivo&gt;</strong></td>
    <td>Especifica o tamanho da divisão do arquivo exportado, em MB.
        <br />
        Se essa opção não for especificada, o AzCopy exporta os dados da tabela para um único arquivo.
        <br />
        Se os dados da tabela forem exportados para um blob e o tamanho do arquivo exportado alcançar o limite de 200 GB, o AzCopy divide o arquivo exportado, mesmo que essa opção não seja especificada. </td>
    <td>N</td>
    <td>N</td>
    <td>S<br /> (somente visualização)</td>
  </tr>
  <tr>
    <td><b>/EntityOperation:&lt;InsertOrSkip | InsertOrMerge | InsertOrReplace&gt;
</b>
</td>
    <td>Especifica o comportamento da importação dos dados da tabela.
        <br />
        InsertOrSkip - Ignora uma entidade existente ou insere uma nova entidade, caso ela não exista na tabela.
        <br />
        InsertOrMerge - Mescla uma entidade existente ou insere uma nova entidade, caso ela não exista na tabela.
        <br />
        InsertOrReplace - Substitui uma entidade existente ou insere uma nova entidade, caso ela não exista na tabela. </td>
    <td>N</td>
    <td>N</td>
    <td>S<br /> (somente visualização)</td>
  </tr>
  <tr>
    <td><b>/Manifest:&lt;arquivo de manifesto&gt;</b></td>
    <td>Especifica o arquivo de manifesto para a operação de importação. <br />
        O arquivo de manifesto é gerado durante a operação de exportação.</td>
    <td>N</td>
    <td>N</td>
    <td>S<br /> (somente visualização)</td>
  </tr>
  <tr>
    <td><b>/SyncCopy</b></td>
    <td>Indica se devem ser copiados de forma síncrona blobs ou arquivos entre dois pontos de extremidade de armazenamento do Azure. <br />
		O AzCopy por padrão usa cópia assíncrona no servidor. Especifique essa opção para executar uma cópia síncrona, que baixa blobs ou arquivos para a memória local e, em seguida, carrega-as para o armazenamento do Azure. Você pode usar essa opção ao copiar arquivos no armazenamento de Blob no armazenamento de arquivo ou do armazenamento de Blob para armazenamento de arquivos ou vice-versa.</td>
    <td>S</td>
    <td>S<br /> (somente visualização)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/SetContentType:&lt;tipo de conteúdo&gt;</b></td>
    <td>Especifica o tipo de conteúdo MIME para blobs ou arquivos de destino. <br />
		O AzCopy define o tipo de conteúdo para um blob ou arquivo <code>application/octet-stream</code> por padrão. Você pode definir o tipo de conteúdo para todos os blobs ou arquivos explicitamente especificando um valor para essa opção. Se você especificar essa opção sem um valor, AzCopy definirá cada blob ou tipo de conteúdo do arquivo de acordo com a sua extensão de arquivo.</td>
    <td>S</td>
    <td>S<br /> (somente visualização)</td>
    <td>N</td>
  </tr>
</table>

<br/>

##<a id="limit-writes"></a>Limite gravações simultâneas durante a cópia de dados

Ao copiar blobs ou arquivos usando o AZCopy, lembre-se de que outro aplicativo pode estar modificando os dados enquanto você os copia. Se possível, verifique se os dados que está copiando não estão sendo modificados durante a cópia. Por exemplo, ao copiar um VHD associado a uma máquina virtual do Azure, verifique se nenhum outro aplicativo está gravando no VHD, no momento. Também é possível criar um instantâneo do VHD primeiro e, em seguida, copiar o instantâneo.

Se não for possível evitar que outros aplicativos gravem em blobs ou arquivos enquanto são copiados, lembre-se que, quando o trabalho terminar, os recursos copiados não poderão mais ter paridade total com os recursos de origem.

##<a id="copy-blobs"></a>Copie blobs do Azure com o AzCopy

Os exemplos abaixo demonstram vários cenários para copiar blobs com o AzCopy.

### Copiar um único blob

**Carregue um arquivo do sistema de arquivos no armazenamento de blob:**
	
	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt

**Baixe um blob do armazenamento de blob no sistema de arquivos:**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

Para obter mais informações sobre como trabalhar com as chaves de acesso de armazenamento, consulte [Exibir, copiar e regenerar chaves de acesso de armazenamento]( http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#regeneratestoragekeys).

### Copiar um blob por meio da cópia no servidor

Uma operação de cópia no servidor é realizada quando você copia um blob dentro de uma conta de armazenamento ou em contas de armazenamento. Para obter mais informações sobre operações de cópia no servidor, consulte [Introdução ao blob de cópia assíncrona entre contas](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx).

**Copie um blob dentro de uma conta de armazenamento:**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceKey:key /DestKey:key /Pattern:abc.txt 

**Copie um blob entre contas de armazenamento:**

	AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt
 
### Copiar um blob da região secundária 

Se a conta de armazenamento tiver armazenamento com redundância geográfica com acesso de leitura habilitado, será possível copiar dados da região secundária. 

**Copie um blob para a conta primária por meio da secundária:**

	AzCopy /Source:https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 /Dest:https://myaccount2.blob.core.windows.net/mynewcontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt

**Baixe um blob na secundária para um arquivo no sistema de arquivos:**

	AzCopy /Source:https://myaccount-secondary.blob.core.windows.net/mynewcontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

### Carregar um arquivo em um novo contêiner de blob ou diretório virtual

**Carregue um arquivo em um novo contêiner de blob**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mynewcontainer /DestKey:key /Pattern:abc.txt

Se o contêiner de destino especificado não existir, o AzCopy o criará e carregará o arquivo nele.

**Carregue um arquivo em um novo diretório virtual de blob**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer/vd /DestKey:key /Pattern:abc.txt

Se o diretório virtual especificado não existir, o AzCopy carregará o arquivo para incluir o diretório virtual no nome (*por exemplo*,  `vd/abc.txt` no exemplo acima).

### Baixar um blob em uma nova pasta

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

Se a pasta  `C:\myfolder` ainda não existir, o AzCopy a criará no sistema de arquivos e baixará `abc.txt ` para a nova pasta.

### Carregar arquivos e subpastas de um diretório em um contêiner de maneira recursiva

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /S

A especificação da opção  `/S` copia o conteúdo do diretório especificado para o armazenamento de blob de maneira recursiva, o que significa que todas as subpastas e seus arquivos serão copiados também. Por exemplo, suponhamos que os seguintes arquivos residam na pasta  `C:\myfolder`:

	C:\myfolder\abc.txt
	C:\myfolder\abc1.txt
	C:\myfolder\abc2.txt
	C:\myfolder\subfolder\a.txt
	C:\myfolder\subfolder\abcd.txt

Depois da operação de cópia, o contêiner incluirá os seguintes arquivos:

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

### Carregar arquivos de um diretório em um contêiner de maneira não recursiva

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key

Se você não especificar a opção  `/S` na linha de comando, o AzCopy não copiará de maneira recursiva. Apenas os arquivos no diretório especificado são copiados. Subpastas e seus arquivos NÃO são copiados. Por exemplo, suponhamos que os seguintes arquivos residam na pasta  `C:\myfolder`:

	C:\myfolder\abc.txt
	C:\myfolder\abc1.txt
	C:\myfolder\abc2.txt
	C:\myfolder\subfolder\a.txt
	C:\myfolder\subfolder\abcd.txt

Depois da operação de cópia, o contêiner incluirá os seguintes arquivos:

	abc.txt
	abc1.txt
	abc2.txt

### Baixar todos os blobs de um contêiner em um diretório no sistema de arquivos de maneira recursiva

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /S

Suponhamos que os seguintes blobs residam no contêiner especificado:  

	abc.txt
	abc1.txt
	abc2.txt
	vd1\a.txt
	vd1\abcd.txt

Depois da operação de cópia, o diretório  `C:\myfolder` incluirá os seguintes arquivos:

	C:\myfolder\abc.txt
	C:\myfolder\abc1.txt
	C:\myfolder\abc2.txt
	C:\myfolder\vd1\a.txt
	C:\myfolder\vd1\abcd.txt

### Baixar todos os blobs de um diretório de blob virtual em um diretório no sistema de arquivos de maneira recursiva

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer/vd1/ /Dest:C:\myfolder /SourceKey:key /S

Suponhamos que os seguintes blobs residam no contêiner especificado:

	abc.txt
	abc1.txt
	abc2.txt
	vd1\a.txt
	vd1\abcd.txt

Depois da operação de cópia, o diretório  `C:\myfolder` incluirá os seguintes arquivos. Apenas os blobs no diretório virtual são copiados:

	C:\myfolder\a.txt
	C:\myfolder\abcd.txt

### Carregar arquivos correspondentes ao padrão de arquivo especificado em um contêiner de maneira recursiva 

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:a* /S

Suponhamos que os seguintes arquivos residam na pasta  `C:\myfolder`:

	C:\myfolder\abc.txt
	C:\myfolder\abc1.txt
	C:\myfolder\abc2.txt
	C:\myfolder\xyz.txt
	C:\myfolder\subfolder\a.txt
	C:\myfolder\subfolder\abcd.txt

Depois da operação de cópia, o contêiner incluirá os seguintes arquivos:

	abc.txt
	abc1.txt
	abc2.txt
	subfolder\a.txt
	subfolder\abcd.txt
	
### Baixar blobs com o prefixo especificado no sistema de arquivos de maneira recursiva

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:a /S

Suponhamos que os blobs a seguir residam no contêiner especificado. Todos os blobs que começam com o prefixo `a` serão copiados:

	abc.txt
	abc1.txt
	abc2.txt
	xyz.txt
	vd1\a.txt
	vd1\abcd.txt

Depois da operação de cópia, a pasta  `C:\myfolder` incluirá os seguintes arquivos:

	C:\myfolder\abc.txt
	C:\myfolder\abc1.txt
	C:\myfolder\abc2.txt

O prefixo se aplica ao diretório virtual, que forma a primeira parte do nome do blob. No exemplo mostrado acima, como não corresponde ao prefixo especificado, o diretório virtual não é copiado.


### Copiar um blob e seus instantâneos para outra conta de armazenamento

	AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt /Snapshot

Depois da operação de cópia, o contêiner de destino incluirá o blob e seus instantâneos. Supondo que o blob no exemplo acima tenha dois instantâneos, o contêiner incluirá os seguintes blob e instantâneos:

	abc.txt
	abc (2013-02-25 080757).txt
	abc (2014-02-21 150331).txt


### Usar um arquivo de resposta para especificar parâmetros de linha de comando

	AzCopy /@:"C:\myfolder\abc.txt"

É possível incluir qualquer parâmetro de linha de comando do AZCopy em um arquivo de resposta. O AzCopy processa os parâmetros no arquivo como se eles tivessem sido especificados na linha de comando, realizando uma substituição direta pelo conteúdo do arquivo.

**Especificar um ou mais arquivos de resposta de linha única**

Aceita um arquivo de resposta chamado `source.txt` que especifica um contêiner de origem:

	/Source:http://myaccount.blob.core.windows.net/mycontainer

E um arquivo de resposta chamado  `dest.txt` que especifique uma pasta de destino no sistema de arquivos:

	/Dest:C:\myfolder

E um arquivo de resposta chamado `options.txt` que especifica opções para AzCopy:

	/S /Y

Para chamar o AzCopy usando esses arquivos de resposta, todos eles residindo em um diretório  `C:\responsefiles`, use este comando:

	AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /SourceKey:<sourcekey> /@:"C:\responsefiles\options.txt"   

O AzCopy processa esse comando assim como faria se você tivesse incluído todos os parâmetros individuais na linha de comando:

	AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

**Especifique um arquivo de resposta multilinhas**

Aceita um arquivo de resposta chamado  `copyoperation.txt`, que contém as seguintes linhas. Todo parâmetro do AzCopy é especificado na própria linha:

	/Source:http://myaccount.blob.core.windows.net/mycontainer
	/Dest:C:\myfolder
	/SourceKey:<sourcekey>
	/S
	/Y

Para chamar o AzCopy usando esses arquivos de resposta, use este comando:

	AzCopy /@:"C:\responsefiles\copyoperation.txt"

O AzCopy processa esse comando assim como faria se você tivesse incluído todos os parâmetros individuais na linha de comando:	

	AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

Cada parâmetro do AzCopy deve ser especificado todo em uma única linha. Por exemplo, o AzCopy falhará se você dividir o parâmetro em duas linhas, conforme mostrado aqui para a o parâmetro  `/sourcekey`:

	http://myaccount.blob.core.windows.net/mycontainer
 	C:\myfolder
	/sourcekey:
	[sourcekey]
	/S
	/Y

### Especificar uma SAS (Assinatura de Acesso Compartilhado)
	
**Especificar uma SAS para o contêiner de origem usando a opção /sourceSAS**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /DestC:\myfolder /SourceSAS:SAS /S

**Especificar uma SAS para o contêiner de origem no URI do contêiner de origem**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken /Dest:C:\myfolder /S

**Especificar uma SAS para o contêiner de destino usando a opção /destSAS**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer1 /DestSAS:SAS /Pattern:abc.txt

**Especificar uma SAS para os contêineres de origem e destino**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceSAS:SAS1 /DestSAS:SAS2 /Pattern:abc.txt

### Especificar uma pasta de arquivo de diário

Sempre que você emite um comando para o AzCopy, ele verifica se um arquivo de diário existe na pasta padrão ou se está em uma pasta especificada por meio dessa opção. Se o arquivo de diário não estiver em nenhum dos lugares, o AzCopy tratará a operação como nova e gerar um novo arquivo de diário.

Se o arquivo de diário existir, o AzCopy verificará se a linha de comando inserida corresponde à linha de comando no arquivo de diário. Se as duas linhas de comando forem correspondentes, o AzCopy retomará a operação incompleta. Se elas não forem correspondentes, será solicitado que você substitua o arquivo de diário para iniciar uma nova operação ou que cancele a operação atual. 

**Usar o local padrão para o arquivo de diário**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z

Se você omitir a opção  `/Z` ou especificar a opção  `/Z` sem o caminho da pasta, conforme mostrado acima, o AzCopy criará o arquivo de diário no local padrão, que é  `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`. Se o arquivo de diário já existir, o AzCopy retomará a operação com base no arquivo de diário. 

**Especificar um local padrão para o arquivo de diário**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z:C:\journalfolder\

Este exemplo criará o arquivo de diário se ele ainda não existir. Se ele existir, o AzCopy retomará a operação com base no arquivo de diário.

**Retomar uma operação AzCopy**

	AzCopy /Z:C:\journalfolder\

Este exemplo retoma a última operação, cuja conclusão pode ter falhado.


### Gerar um arquivo de log

**Gravar o arquivo de log detalhado no local padrão**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V

Se você especificar a opção  `/V` sem fornecer um caminho de arquivo para o log detalhado, o AzCopy criará o arquivo de log no local padrão, que é  `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`.

**Gravar o arquivo de log detalhado em um local personalizado**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V:C:\myfolder\azcopy1.log

Observe que, se você especificar um caminho relativo a seguinte opção `/V`, como `/V:test/azcopy1.log`, em seguida, o log detalhado é criado no diretório de trabalho atual em uma subpasta chamada `test`.


### Definir a hora da última modificação dos arquivos baixados como sendo a mesma dos blobs de origem

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT

### Excluir blobs da operação de cópia com base na hora da última modificação

Especifique a opção  `/MT` para comparar a hora da última modificação do blob de origem com a do arquivo de destino.

**Excluir blobs que sejam mais novos do que o arquivo de destino**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XN

**Excluir blobs que sejam mais antigos do que o arquivo de destino**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XO

### Especificar o número de operações simultâneas para começar

A opção  `/NC` especifica o número de operações de cópia simultâneas. Por padrão, o AzCopy começará operações simultâneas com oito vezes o número de processadores de núcleo disponíveis. Se estiver executando o AzCopy em uma rede de baixa largura de banda, você pode especificar um número menor para essa opção a fim de evitar uma falha causada pela concorrência de recursos.


### Executar o AzCopy em recursos de blob no emulador de armazenamento

	AzCopy /Source:https://127.0.0.1:10004/myaccount/myfileshare/ /Dest:C:\myfolder /SourceKey:key /SourceType:Blob /S

### Copiar blobs de forma síncrona entre dois pontos de extremidade do armazenamento do Azure

O AzCopy por padrão copia dados entre dois pontos de extremidade de armazenamento assincronamente. Portanto, a operação de cópia será executada em segundo plano usando capacidade de largura de banda extra sem nenhum SLA em termos da velocidade de como o blob será copiado e o AzCopy verificará periodicamente o status da cópia até que a cópia esteja concluída ou tenha ocorrido uma falha. 

A `/SyncCopy` opção na versão 3.1.0, garante que a operação de cópia obterá velocidade consistente. O AzCopy realiza a cópia síncrona baixando os blobs para copiar da fonte especificada para a memória local, e, em seguida, carregá-los para o destino de armazenamento de Blob.

	AzCopy /Source:https://myaccount1.blob.core.windows.net/myContainer/ /Dest:https://myaccount2.blob.core.windows.net/myContainer/ /SourceKey:key1 /DestKey:key2 /Pattern:ab /SyncCopy

Observe que /SyncCopy pode gerar custo de saída adicional comparando a cópia assíncrona, a abordagem recomendada é usar essa opção na VM do Azure que está na mesma região que a sua conta de armazenamento de origem para evitar o custo de saída.

### Especificar o tipo de conteúdo MIME de um blob de destino

Por padrão, AzCopy define o tipo de conteúdo de um blob de destino para `application/octet-stream`. A partir da versão 3.1.0, você pode especificar explicitamente o tipo de conteúdo por meio da opção `/SetContentType:[tipo de conteúdo]`. Essa sintaxe define o tipo de conteúdo para todos os blobs em uma operação de cópia.

	AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType:video/mp4

Se você especificar `/SetContentType` sem um valor, então o AzCopy definirá cada blob ou tipo de conteúdo do arquivo de acordo com a sua extensão de arquivo.

	AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType

##<a id="copy-files"></a> Copiar arquivos no armazenamento de arquivos do Azure com o AzCopy (somente na versão de teste)

Os exemplos abaixo demonstram vários cenários para copiar arquivos do Azure com o AzCopy.

### Baixar um arquivo de um compartilhamento de arquivos do Azure no sistema de arquivos

	AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/myfolder1/ /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

Observe que se a origem especificada é um compartilhamento de arquivos do Azure, então você deve especificar o nome exato do arquivo, (*por exemplo,* `abc.txt`) para copiar um único arquivo ou especificar a opção `/S` para copiar todos os arquivos do compartilhamento recursivamente. A tentativa de especificar ao mesmo tempo um padrão de arquivo e uma opção  `/S` resultará em um erro.

### Baixar arquivos e pastas de um compartilhamento de arquivos do Azure no sistema de arquivos de maneira recursiva

	AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceKey:key /S

Nenhuma pasta vazia será copiada.


### Carregar arquivos e pastas do sistema de arquivos no compartilhamento de arquivos do Azure de maneira recursiva

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S

Nenhuma pasta vazia será copiada.


### Carregar arquivos correspondentes ao padrão de arquivo especificado em um compartilhamento de arquivos do Azure de maneira recursiva

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:ab* /S


### Copiar arquivos de forma síncrona no armazenamento de arquivos do Azure

Com a nova opção /SyncCopy na versão de teste 4.1.0, usuário pode copiar arquivos de armazenamento de arquivos para armazenamento de arquivos, do armazenamento de arquivo para o armazenamento de Blob e do armazenamento de Blob ao armazenamento de arquivos.

	AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S

	AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare/ /Dest:https://myaccount2.blob.core.windows.net/mycontainer/ /SourceKey:key1 /DestKey:key2 /S
	
	AzCopy /Source:https://myaccount1.blob.core.windows.net/mycontainer/ /Dest:https://myaccount2.file.core.windows.net/myfileshare/ /SourceKey:key1 /DestKey:key2 /S

Durante a cópia de armazenamento de arquivos no armazenamento de Blob, o tipo de blob padrão é o blob de bloco, o usuário pode especificar a opção /BlobType:page para alterar o tipo de blob de destino.

Observe que o serviço de armazenamento do Azure não oferece suporte cópia assíncrona ainda quando a versão de teste AzCopy 4.1.0 foi lançada, portanto, as operações de cópia acima falharão se a opção /SyncCopy não estiver especificada.


##<a id="copy-entities"></a> Copiar entidades em uma tabela do Azure com o AzCopy (somente na versão de teste)

Os exemplos abaixo demonstram vários cenários para copiar entidades de tabela do Azure com o AzCopy.

### Exportar entidades para o sistema de arquivos local

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key

### Exportar entidades para um blob do Azure

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:https://myaccount.blob.core.windows.net/mycontainer/ /SourceKey:key1 /Destkey:key2

O AzCopy gera um arquivo de dados JSON na pasta local ou no contêiner do blob, seguindo esta convenção de nomenclatura:

	<account name>_<table name>_<timestamp>_<volume index>_<CRC>.json

O arquivo de dados JSON gerado segue o formato de carga para metadados mínimos. Para obter detalhes sobre esse formado de carga, confira [Formato de carga para operações do serviço Tabela](http://msdn.microsoft.com/library/azure/dn535600.aspx).

### Dividir os arquivos exportados

	AzCopy /Source:https://myaccount.table.core.windows.net/mytable/ /Dest:C:\myfolder /SourceKey:key /S /SplitSize:100

O AzCopy usa um  *índice de volume* nos nomes dos arquivos de dados da divisão para diferenciar diversos arquivos.  O índice do volume é composto por duas partes, um *índice de intervalo de chaves de partição* e um  *índice de arquivos de divisão*. Os dois índices são de base zero.

O índice do intervalo de chaves de partição será 0 se o usuário não especificar a opção  `/PKRS` (apresentada na seção a seguir).

Por exemplo, digamos que o AzCopy gere dois arquivos de dados depois que o usuário especificar a opção  `/SplitSize`. Os nomes dos arquivos de dados resultantes podem ser:

	myaccount_mytable_20140903T051850.8128447Z_0_0_C3040FE8.json
	myaccount_mytable_20140903T051850.8128447Z_0_1_0AB9AC20.json

Observe que o menor valor possível para a opção  `/SplitSize` é 32 MB. Se o destino especificado for um armazenamento blob, o AzCopy dividirá o arquivo de dados quando alcançar o tamanho limite do blob (200 GB), mesmo que o usuário não tenha especificado a opção  `/SplitSize`.

### Exportar entidades simultaneamente

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PKRS:"aa#bb"

O AzCopy inicia operações simultâneas para exportar entidades quando o usuário especifica a opção  `/PKRS`. Cada operação exporta um intervalor de chaves de partição.

Observe que a opção  `/NC` também contra a quantidade de operações simultâneas. O AzCopy usa a quantidade de processadores de núcleo como valor padrão de  `/NC` ao copiar entidades de tabela, mesmo que  `/NC` não tenha sido especificado. Quando o usuário especifica a opção  `/PKRS`, o AzCopy usa o menor valor - entre os intervalos de chaves de partição ou operações simultâneas especificadas implícita ou explicitamente - para terminar quantas operações simultâneas devem ser iniciadas. Para obter mais detalhes, digite  `AzCopy /?:NC` na linha de comando.

### Importar entidades simultaneamente

	AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.core.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace 

Quando você exporta entidades de tabelas, o AzCopy grava um arquivo de manifesto para a pasta de destino ou o contêiner do blob especificado. O arquivo de manifesto é usado pelo processo de importação para localizar os arquivos de dados necessários e executar a validação de dados durante o processo de importação. O arquivo de manifesto usa esta convenção de nomenclatura:

	<account name>_<table name>_<timestamp>.manifest

A opção  `/EntityOperation` indica como inserir entidades na tabela. Os valores possíveis são:

- `InsertOrSkip`: Ignora uma entidade existente ou insere uma nova entidade, caso ela não exista na tabela.
- `InsertOrMerge`: Mescla uma entidade existente ou insere uma nova entidade, caso ela não exista na tabela.
- `InsertOrReplace`: Substitui uma entidade existente ou insere uma nova entidade, caso ela não exista na tabela.

Observe que não é possível especificar a opção  `/PKRS` no cenário de importação. Diferente do cenário de importação, no qual é necessário especificar a opção  `/PKRS` para iniciar operações simultâneas, por padrão, o AzCopy inicia as operações simultâneas quando você importa as entidade. A quantidade padrão de operações iniciadas simultaneamente é igual à quantidade de processadores de núcleo. No entanto, você pode especificar outro valor com a opção  `/NC`. Para obter mais detalhes, digite  `AzCopy /?:NC` na linha de comando.


##<a id="versions"></a>Versões do AzCopy

| Versão | O que há de novo                                                                                      				|
|---------|-----------------------------------------------------------------------------------------------------------------|
| **V4.1.0**  | **Versão de teste atual. Inclui todas as funcionalidades da V3.1.0. Suporta cópia de blobs e arquivos de forma síncrona e especifica o tipo de conteúdo para blobs e arquivos de destino**	
| **V3.1.0** | **Versão da liberação atual. Suporta cópia de blobs de forma síncrona e especifica o tipo de conteúdo para blobs de destino.**
| V4.0.0 | Inclui todas as funcionalidades do V3.0.0. Também permite copiar arquivos de ou para o armazenamento de arquivos do Azure e copiar entidades de ou para o armazenamento de tabela do Azure.
| V3.0.0 | Modifica a sintaxe de linha de comando AzCopy para exigir nomes de parâmetros e recria a Ajuda de linha de comando. Essa versão dá suporte apenas copiar para e do armazenamento de blob do Azure.	
| V2.5.1 | Otimiza o desempenho com o uso das opções /xo e /xn. Corrige bugs relacionados a caracteres especiais nos nomes dos arquivos de origem e os danos nos arquivos de diário quando o usuário insere sintaxe errada na linha de comando.	
| V2.5.0 | Otimiza o desempenho para cenários de cópia em larga escala e apresenta diversos aperfeiçoamentos de usabilidade importantes.	
| V2.4.1 | Permite a especificação da pasta de destino no assistente de instalação.                     			
| V2.4.0 | Permite o upload e o download de arquivos para armazenamento de arquivo do Azure.                       				                              
| V2.3.0 | Permite contas de armazenamento com redundância geográfica com acesso de leitura.                                                  			|
| V2.2.2 | Atualizado para usar a versão da biblioteca do cliente de armazenamento do Azure 3.0.3.                                            				                    
| V2.2.1 | Problema de desempenho corrigido durante a cópia de arquivos muito grandes dentro da mesma conta de armazenamento.            				                                                
| V2.2    | Permite a configuração do delimitador de diretório virtual para nomes de blob. Permite a especificação do caminho de arquivo de diário.		|
| V 2.1    | Oferece mais de 20 opções para permitir operações de upload, download e cópia de blob de maneira eficiente.		|


##<a id="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Armazenamento do Azure e o AzCopy, consulte os recursos a seguir.

### Documentação do Armazenamento do Azure:

- [Introdução ao armazenamento do Azure](http://azure.microsoft.com/documentation/articles/storage-introduction/)
- [Armazenar arquivos no armazenamento de Blob](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/)
- [Cria um compartilhamento de arquivos SMB no Azure e no armazenamento de arquivo](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files/)

### Postagens de blog de armazenamento do Azure:
- [AzCopy: Introdução a cópia síncrona e tipo de conteúdo personalizado](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
- [AzCopy: Anúncio de disponibilidade geral do AzCopy 3.0 mais versão de teste do AzCopy 4.0 com suporte a Tabela e Arquivo](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
- [AzCopy: Otimizado para cenários de cópia em larga escala](http://go.microsoft.com/fwlink/?LinkId=507682)
- [Apresentando o serviço de arquivo do Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
- [AzCopy: Suporte à redundância geográfica com acesso de leitura](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
- [AzCopy: Transferir dados com modo reiniciável e token da SAS](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
- [AzCopy: Usando blob de cópia em várias contas](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
- [AzCopy: Carregando/baixando arquivos de blobs do Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)


<!--HONumber=42-->
 