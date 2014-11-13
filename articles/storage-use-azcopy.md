<properties urlDisplayName="AzCopy" pageTitle="Como usar o AzCopy com o armazenamento do Microsoft Azure" metaKeywords="Get started Azure AzCopy   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage   Azure file   Azure file storage   Azure file share   AzCopy" description="Saiba como usar o utilit&aacute;rio AzCopy para carregar, baixar e copiar blobs e conte&uacute;do de arquivos." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter="" title="Como usar o AzCopy com o armazenamento do Microsoft Azure" authors="tamram" manager="adinah" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/17/2014" ms.author="tamram" />

# Introdução ao utilitário de linha de comando AzCopy

AzCopy é um utilitário de linha de comando projetado para upload, download e cópia de dados de alto desempenho do e para o armazenamento de blob, arquivo e tabela do Microsoft Azure. Este guia apresenta uma visão geral do uso do AzCopy.

> [WACOM.NOTE] Este guia pressupõe que você tenha o AzCopy 3.0.0 ou posterior instalado. O AzCopy 3.x já está disponível.
> Este guia também cobre o uso do AzCopy 4.0.0, que é uma versão de teste do AzCopy. Neste guia, as funções presentes somente nas versões de teste são designadas como *teste*.
> Observe que para o AzCopy 4.x, a funcionalidade e as opções da linha de comando podem mudar posteriormente.

## Sumário

-   [Baixe e instale o AzCopy][Baixe e instale o AzCopy]
-   [Compreenda a sintaxe da linha de comando do AZCopy][Compreenda a sintaxe da linha de comando do AZCopy]
-   [Limite gravações simultâneas durante a cópia de dados][Limite gravações simultâneas durante a cópia de dados]
-   [Copie blobs do Azure com o AzCopy][Copie blobs do Azure com o AzCopy]
-   [Copie arquivos em um compartilhamento de arquivos do Azure com o AzCopy (somente na versão de teste)][Copie arquivos em um compartilhamento de arquivos do Azure com o AzCopy (somente na versão de teste)]
-   [Copie as entidades de tabela do Azure com o AzCopy (somente na versão de teste)][Copie as entidades de tabela do Azure com o AzCopy (somente na versão de teste)]
-   [Versões do AzCopy][Versões do AzCopy]
-   [Próximas etapas][Próximas etapas]

## <span id="install"></span></a> Baixar e instalar o AzCopy

1.  Baixe a [versão mais recente do AzCopy][versão mais recente do AzCopy] ou a [última versão de teste][última versão de teste].
2.  Execute a instalação. Por padrão, a instalação do AzCopy cria uma pasta chamada `AzCopy` em `%ProgramFiles(x86)%\Microsoft SDKs\Azure\` (em um computador sendo executado no Windows 64 bits) ou `%ProgramFiles%\Microsoft SDKs\Azure\` (em um computador sendo executado no Windows 32 bits). No entanto, é possível alterar o caminho da instalação por meio do assistente de instalação.
3.  Se quiser, você pode alterar o local da instalação do AzCopy para o caminho do sistema.

## <span id="syntax"></span></a> Entenda a sintaxe da linha de comando do AZCopy

Em seguida, abra uma janela de comando e navegue até o diretório de instalação do AzCopy no computador, onde o executável `AzCopy.exe` está localizado. A sintaxe básica dos comandos do AzCopy é:

    AzCopy /Source:<source> /Dest:<destination> /Pattern:<filepattern> [Options]

> [WACOM.NOTE] A partir da versão 3.0.0 do AzCopy, a sintaxe da linha de comando do AzCopy requer que todos os parâmetros sejam especificados para incluir o nome do parâmetro (*por exemplo*, `/ParameterName:ParameterValue`).

Os parâmetros do AzCopy são descritos na tabela abaixo. Também é possível digitar um dos comandos a seguir na linha de comando para obter ajuda no uso do AzCopy:

-   Para obter ajuda detalhada da linha de comando para o AzCopy: `AzCopy /?`
-   Para obter ajuda detalhada para qualquer parâmetro do AzCopy: `AzCopy /?:SourceKey`
-   Para exemplos da linha de comando: `AzCopy /?:Samples`

| Nome da opção                                                           | Descrição                                                                                                                                                                                                                                                                                                                                                                                       | Aplicável ao armazenamento de blob (S/N) | Aplicável ao armazenamento de arquivo (S/N) (somente na versão de teste) | Aplicável ao armazenamento de tabela (S/N) (somente na versão de teste) |
|-------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------|--------------------------------------------------------------------------|-------------------------------------------------------------------------|
| **/Source:\<source\>**                                                  | Especifica os dados de origem para cópia. A origem pode ser um diretório do sistema de arquivos, um contêiner de blob, um diretório virtual de blob, um compartilhamento de arquivos de armazenamento, um diretório de arquivos de armazenamento ou uma tabela do Azure.                                                                                                                        | S                                        | S                                                                        
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         (somente na versão de teste)                                             | S                                                                       
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    (somente na versão de teste)                                            |
| **/Dest:\<destination\>**                                               | Especifica o destino da cópia. O destino pode ser um diretório do sistema de arquivos, um contêiner de blob, um diretório virtual de blob, um compartilhamento de arquivos de armazenamento, um diretório de arquivos de armazenamento ou uma tabela do Azure.                                                                                                                                  | S                                        | S                                                                        
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         (somente na versão de teste)                                             | S                                                                       
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    (somente na versão de teste)                                            |
| **/Pattern:\<file-pattern\>**                                           | Especifica um padrão de arquivo que indica quais arquivos devem ser copiados. O comportamento do parâmetro /Pattern é determinado pelo local dos dados de origem e pela presença da opção do modo recursivo. O modo recursivo é especificado pela opção /S.                                                                                                                                     
                                                                            Se a origem especificada for um diretório no sistema de arquivos, os curingas padrão estão em vigor, e o padrão de arquivo fornecido é comparado com os arquivos dentro do diretório. Se a opção /S for especificada, o AzCopy também compara o padrão especificado com todos os arquivos em todas as subpastas do diretório.                                                                   
                                                                            Se a origem especificada for um contêiner de blob ou um diretório virtual, os curingas não são aplicados. Se a opção /S for especificada, o AzCopy interpreta o padrão do arquivo especificado como um prefixo de blob. Se a opção /S não for especificada, o AzCopy compara o padrão do arquivo com os nomes de blob exatos.                                                                   
                                                                            Se a origem especificada for um compartilhamento de arquivos do Azure, você deve especificar o nome exato do arquivo (por exemplo, abc.txt) para copiar um único arquivo ou especificar a opção /S para copiar todos os arquivos recursivamente no compartilhamento. A tentativa de especificar, ao mesmo tempo, um padrão de arquivo e uma opção /S resultará em erro.                         
                                                                            O padrão de arquivo usado quando nenhum padrão de arquivo é especificado é \*.\* para um local do sistema de arquivos, ou um prefixo vazio para um local de armazenamento do Azure. Não é possível especificar diversos padrões para os arquivos.                                                                                                                                               | S                                        | S                                                                        
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         (somente na versão de teste)                                             | N                                                                       |
| **/DestKey:\<storage-key\>**                                            | Especifica a chave de conta de armazenamento do recurso de destino.                                                                                                                                                                                                                                                                                                                             | S                                        | S                                                                        
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         (somente na versão de teste)                                             | S                                                                       
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    (somente na versão de teste)                                            |
| **/DestSAS:\<sas-token\>**                                              | Especifica uma SAS (Assinatura de Acesso Compartilhado) para o contêiner de destino (se aplicável). Coloque a SAS entre aspas duplas, porque ela pode conter caracteres de linha de comando especiais.                                                                                                                                                                                          
                                                                            Se o recurso de destino for um blob, um contêiner ou uma tabela será possível especificar essa opção seguida do token da SAS ou a SAS como parte do URI do blob de destino, sem essa opção.                                                                                                                                                                                                     
                                                                            Se a origem e o destino forem blobs, ambos devem residir na mesma conta de armazenamento.                                                                                                                                                                                                                                                                                                       | S                                        | N                                                                        | S                                                                       
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    (somente na versão de teste)                                            |
| **/SourceKey:\<storage-key\>**                                          | Especifica a chave de conta de armazenamento para o recurso de origem.                                                                                                                                                                                                                                                                                                                          | S                                        | S                                                                        
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         (somente na versão de teste)                                             | S                                                                       
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    (somente na versão de teste)                                            |
| **/SourceSAS:\<sas-token\>**                                            | Especifica uma SAS para o contêiner de origem (se aplicável). Coloque a SAS entre aspas duplas, porque ela pode conter caracteres de linha de comando especiais.                                                                                                                                                                                                                                
                                                                            Se o recurso da fonte foir um contêiner de blob e não for fornecida uma chave nem uma SAS, o contêiner será lido por meio de acesso anônimo.                                                                                                                                                                                                                                                    
                                                                            Se a fonte for uma tabela, chave ou SAS, ela deverá ser fornecida.                                                                                                                                                                                                                                                                                                                              | S                                        | N                                                                        | S                                                                       
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    (somente na versão de teste)                                            |
| **/S**                                                                  | Especifica o modo recursivo para operações de cópia. No modo recursivo, o AzCopy copiará todos os blobs ou arquivos correspondentes ao padrão do arquivo, inclusive os presentes nas subpastas.                                                                                                                                                                                                 | S                                        | S                                                                        
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         (somente na versão de teste)                                             | N                                                                       |
| **/BlobType:\<block | page\>**                                          | Especifica se o destino é um blob de blocos ou um blob de páginas. Essa opção só é aplicável no carregamento de blobs. Do contrário, um erro é gerado. Se o destino for um blob e essa opção não estiver especificada, por padrão, o AzCopy criará um blob de blocos.                                                                                                                           | S                                        | N                                                                        | N                                                                       |
| **/CheckMD5**                                                           | Calcula um hash MD5 para dados baixados e certifica que o hash MD5 armazenado no blob, ou a propriedade Content-MD5 do arquivo, corresponde ao hash calculado. Como, por padrão, a verificação MD5 permanece desativada, você deve especificar essa opção para realizar a verificação MD5 ao baixar os dados.                                                                                   
                                                                            O Azure Storage não assegura que o hash MD5 armazenado para o blob ou o arquivo esteja atualizado. É de responsabilidade do cliente atualizar o MD5 sempre que o blob ou o arquivo é modificado.                                                                                                                                                                                                
                                                                            O AzCopy sempre define a propriedade Content-MD5 para um blob ou um arquivo do Azure depois de carregá-lo no serviço.                                                                                                                                                                                                                                                                           | S                                        | S                                                                        
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         (somente na versão de teste)                                             | N                                                                       |
| **/Snapshot**                                                           | Indica se é necessário transferir ou não os instantâneos. Essa opção só é válida quando a origem é um blob.                                                                                                                                                                                                                                                                                     
                                                                            Os instantâneos de blob transferidos são renomeados neste formato: [nome\_do\_blob] (hora\_do\_instantâneo)[extensão].                                                                                                                                                                                                                                                                          
                                                                            Por padrão, os instantâneos não são copiados.                                                                                                                                                                                                                                                                                                                                                   | S                                        | N                                                                        | N                                                                       |
| **/V:[verbose log-file]**                                               | Produz mensagens de status detalhadas em um arquivo de log. Por padrão, o arquivo de log detalhado é chamado de `AzCopyVerbose.log` em `%LocalAppData%\Microsoft\Azure\AzCopy`. Se você especificar um local de arquivo existente para essa opção, o log detalhado será acrescentado a esse arquivo.                                                                                            | S                                        | S                                                                        
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         (somente na versão de teste)                                             | S                                                                       
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    (somente na versão de teste)                                            |
| **/Z:[journal-file-folder]**                                            | Especifica uma pasta de arquivo de diário para retomar uma operação.                                                                                                                                                                                                                                                                                                                            
                                                                            O AzCopy sempre dará suporte à retomada caso uma operação tenha sido interrompida.                                                                                                                                                                                                                                                                                                              
                                                                            Se essa opção não for especificada ou for especificada sem um caminho de pasta, o AzCopy criará o arquivo de diário no local padrão, que é `%LocalAppData%\Microsoft\Azure\AzCopy`.                                                                                                                                                                                                             
                                                                            Sempre que você emite um comando para o AzCopy, ele verifica se um arquivo de diário existe na pasta padrão ou se está em uma pasta especificada por meio dessa opção. Se o arquivo de diário não estiver em nenhum dos lugares, o AzCopy tratará a operação como nova e gerar um novo arquivo de diário.                                                                                       
                                                                            Se o arquivo de diário existir, o AzCopy verificará se a linha de comando inserida corresponde à linha de comando no arquivo de diário. Se as duas linhas de comando forem correspondentes, o AzCopy retomará a operação incompleta. Se elas não forem correspondentes, será solicitado que você substitua o arquivo de diário para iniciar uma nova operação ou que cancele a operação atual.  
                                                                            O arquivo de diário é excluído mediante a conclusão bem-sucedida da operação.                                                                                                                                                                                                                                                                                                                   
                                                                            A retomada de uma operação de um arquivo de diário criado por uma versão anterior do AzCopy não é compatível.                                                                                                                                                                                                                                                                                   | S                                        | S                                                                        
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         (somente na versão de teste)                                             | S                                                                       
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    (somente na versão de teste)                                            |
| **/@:parameter-file**                                                   | Especifica um arquivo que contém parâmetros. O AzCopy processa os parâmetros no arquivo como se eles tivessem sido especificados na linha de comando.                                                                                                                                                                                                                                           
                                                                            Em um arquivo de resposta, é possível especificar vários parâmetros em um único arquivo ou especificar cada parâmetro na própria linha. Um parâmetro individual não pode abranger várias linhas.                                                                                                                                                                                                
                                                                            Os arquivos de resposta podem incluir linhas de comentários iniciadas pelo símbolo `#`.                                                                                                                                                                                                                                                                                                         
                                                                            É possível especificar vários arquivos de resposta. No entanto, o AzCopy não permite arquivos de resposta aninhados.                                                                                                                                                                                                                                                                            | S                                        | S                                                                        
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         (somente na versão de teste)                                             | S                                                                       
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    (somente na versão de teste)                                            |
| **/Y**                                                                  | Suprime todas as solicitações de confirmação do AzCopy.                                                                                                                                                                                                                                                                                                                                         | S                                        | S                                                                        
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         (somente na versão de teste)                                             | S                                                                       
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    (somente na versão de teste)                                            |
| **/L**                                                                  | Especifica uma operação de listagem apenas. Nenhum dado é copiado.                                                                                                                                                                                                                                                                                                                              | S                                        | S                                                                        
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         (somente na versão de teste)                                             | N                                                                       |
| **/MT**                                                                 | Define a hora da última modificação do arquivo baixado como a mesma do blob de origem ou do arquivo.                                                                                                                                                                                                                                                                                            | S                                        | S                                                                        
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         (somente na versão de teste)                                             | N                                                                       |
| **/XN**                                                                 | Exclui um recurso de origem mais novo. O recurso não será copiado se a origem for mais nova do que o destino.                                                                                                                                                                                                                                                                                   | S                                        | S                                                                        
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         (somente na versão de teste)                                             | N                                                                       |
| **/XO**                                                                 | Exclui um recurso de origem mais antigo. O recurso não será copiado se o recurso de origem for mais antigo do que o destino.                                                                                                                                                                                                                                                                    | S                                        | S                                                                        
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         (somente na versão de teste)                                             | N                                                                       |
| **/A**                                                                  | Carrega apenas arquivos que tenham o atributo Archive definido.                                                                                                                                                                                                                                                                                                                                 | S                                        | S                                                                        
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         (somente na versão de teste)                                             | N                                                                       |
| **/IA:[RASHCNETOI]**                                                    | Carrega apenas arquivos que tenham algum dos atributos especificados definido.                                                                                                                                                                                                                                                                                                                  
                                                                            Entre os atributos disponíveis estão:                                                                                                                                                                                                                                                                                                                                                           
                                                                            R   Arquivos somente leitura                                                                                                                                                                                                                                                                                                                                                                    
                                                                            A   Arquivos prontos para arquivamento                                                                                                                                                                                                                                                                                                                                                          
                                                                            S   Arquivos de sistema                                                                                                                                                                                                                                                                                                                                                                         
                                                                            H   Arquivos ocultos                                                                                                                                                                                                                                                                                                                                                                            
                                                                            C   Arquivo compactado                                                                                                                                                                                                                                                                                                                                                                          
                                                                            N   Arquivos normais                                                                                                                                                                                                                                                                                                                                                                            
                                                                            E   Arquivos criptografados                                                                                                                                                                                                                                                                                                                                                                     
                                                                            T   Arquivos temporários                                                                                                                                                                                                                                                                                                                                                                        
                                                                            O   Arquivos offline                                                                                                                                                                                                                                                                                                                                                                            
                                                                            I   Arquivos não indexados                                                                                                                                                                                                                                                                                                                                                                      | S                                        | S                                                                        
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         (somente na versão de teste)                                             | N                                                                       |
| **/XA:[RASHCNETOI]**                                                    | Exclui arquivos que tenham qualquer dos atributos especificados definido.                                                                                                                                                                                                                                                                                                                       
                                                                            Entre os atributos disponíveis estão:                                                                                                                                                                                                                                                                                                                                                           
                                                                            R   Arquivos somente leitura                                                                                                                                                                                                                                                                                                                                                                    
                                                                            A   Arquivos prontos para arquivamento                                                                                                                                                                                                                                                                                                                                                          
                                                                            S   Arquivos de sistema                                                                                                                                                                                                                                                                                                                                                                         
                                                                            H   Arquivos ocultos                                                                                                                                                                                                                                                                                                                                                                            
                                                                            C   Arquivo compactado                                                                                                                                                                                                                                                                                                                                                                          
                                                                            N   Arquivos normais                                                                                                                                                                                                                                                                                                                                                                            
                                                                            E   Arquivos criptografados                                                                                                                                                                                                                                                                                                                                                                     
                                                                            T   Arquivos temporários                                                                                                                                                                                                                                                                                                                                                                        
                                                                            O   Arquivos offline                                                                                                                                                                                                                                                                                                                                                                            
                                                                            I   Arquivos não indexados                                                                                                                                                                                                                                                                                                                                                                      | S                                        | S                                                                        
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         (somente na versão de teste)                                             | N                                                                       |
| **/Delimiter:\<delimiter\>**                                            | Indica o caractere delimitador usado para delimitar diretórios virtuais em um nome de blob.                                                                                                                                                                                                                                                                                                     
                                                                            Por padrão, o AzCopy usa / como o caractere delimitador. No entanto, o AzCopy dá suporte ao uso de qualquer caractere comum (como @, \# ou %) como delimitador. Se precisar incluir um desses caracteres especiais na linha de comando, coloque o nome do arquivo entre aspas duplas.                                                                                                           
                                                                            Essa opção só é aplicável para o download de blobs.                                                                                                                                                                                                                                                                                                                                             | S                                        | N                                                                        | N                                                                       |
| **/NC:\<number-of-concurrents\>**                                       | Especifica o número de operações simultâneas.                                                                                                                                                                                                                                                                                                                                                   
                                                                            Por padrão, o AzCopy inicia uma determinada quantidade de operações simultâneas para aumentar a taxa de transferência dos dados. O grande número de operações simultâneas em um ambiente com baixa largura de banda pode sobrecarregar a conexão de rede, evitando que as operações sejam concluídas. Limite operações simultâneas com base na largura de banda real de rede disponível.        
                                                                            O limite máximo de operações simultâneas é 512.                                                                                                                                                                                                                                                                                                                                                 | S                                        | S                                                                        
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         (somente na versão de teste)                                             | N                                                                       |
| **/SourceType:Blob|Table**                                              | Especifica se o recurso `source` é um blob disponível no ambiente de desenvolvimento local, em execução no emulador de armazenamento.                                                                                                                                                                                                                                                           | S                                        | N                                                                        | S                                                                       
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    (somente na versão de teste)                                            |
| **/DestType:Blob|Table**                                                | Especifica se o recurso `destination` é um blob disponível no ambiente de desenvolvimento local, em execução no emulador de armazenamento.                                                                                                                                                                                                                                                      | S                                        | N                                                                        | S                                                                       
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    (somente na versão de teste)                                            |
| **/PKRS:\<"key1\#key2\#key3\#..."\>**                                   | Divide o intervalo de chaves de partição para possibilitar a exportação paralela dos dados, o que aumenta a velocidade dessa operação.                                                                                                                                                                                                                                                          
                                                                            Se essa opção não for especificada, o AzCopy usa um único thread para exportar entidades de tabela. Por exemplo, se o usuário especifica /PKRS:"aa\#bb", o AzCopy inicia três operações simultâneas.                                                                                                                                                                                            
                                                                            Cada operação exporta um dos três intervalos de chaves de partição, como mostramos abaixo:                                                                                                                                                                                                                                                                                                      
                                                                               [\<primeira chave da partição\>, aa)                                                                                                                                                                                                                                                                                                                                                         
                                                                               [aa, bb)                                                                                                                                                                                                                                                                                                                                                                                     
                                                                               [bb, \<última chave da partição\>]                                                                                                                                                                                                                                                                                                                                                           | N                                        | N                                                                        | S                                                                       
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    (somente na versão de teste)                                            |
| **/SplitSize:**<file-size>**\<file-size\>**                             | Especifica o tamanho da divisão do arquivo exportado, em MB.                                                                                                                                                                                                                                                                                                                                    
                                                                            Se essa opção não for especificada, o AzCopy exporta os dados da tabela para um único arquivo.                                                                                                                                                                                                                                                                                                  
                                                                            Se os dados da tabela forem exportados para um blob e o tamanho do arquivo exportado alcançar o limite de 200 GB, o AzCopy divide o arquivo exportado, mesmo que essa opção não seja especificada.                                                                                                                                                                                              | N                                        | N                                                                        | S                                                                       
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    (somente na versão de teste)                                            |
| **/EntityOperation:\<InsertOrSkip | InsertOrMerge | InsertOrReplace\>** | Especifica o comportamento da importação dos dados da tabela.                                                                                                                                                                                                                                                                                                                                   
                                                                            InsertOrSkip — Ignora uma entidade existente ou insere uma nova entidade, caso ela não exista na tabela.                                                                                                                                                                                                                                                                                        
                                                                            InsertOrMerge — Mescla uma entidade existente ou insere uma nova entidade, caso ela não exista na tabela.                                                                                                                                                                                                                                                                                       
                                                                            InsertOrReplace — Substitui uma entidade existente ou insere uma nova entidade, caso ela não exista na tabela.                                                                                                                                                                                                                                                                                  | N                                        | N                                                                        | S                                                                       
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    (somente na versão de teste)                                            |
| **/Manifest:\<manifest-file\>**                                         | Especifica o arquivo de manifesto para a operação de importação.                                                                                                                                                                                                                                                                                                                                
                                                                            O arquivo de manifesto é gerado durante a operação de exportação.                                                                                                                                                                                                                                                                                                                               | N                                        | N                                                                        | S                                                                       
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    (somente na versão de teste)                                            |

## <span id="limit-writes"></span></a> Limite gravações simultâneas durante a cópia de dados

Ao copiar blobs ou arquivos usando o AZCopy, lembre-se de que outro aplicativo pode estar modificando os dados enquanto você os copia. Se possível, verifique se os dados que está copiando não estão sendo modificados durante a cópia. Por exemplo, ao copiar um VHD associado a uma máquina virtual do Azure, verifique se nenhum outro aplicativo está gravando no VHD, no momento. Também é possível criar um instantâneo do VHD primeiro e, em seguida, copiar o instantâneo.

Se não for possível evitar que outros aplicativos gravem em blobs ou arquivos enquanto são copiados, lembre-se que, quando o trabalho terminar, os recursos copiados não poderão mais ter paridade total com os recursos de origem.

## <span id="copy-blobs"></span></a> Copie blobs do Azure com o AzCopy

Os exemplos abaixo demonstram vários cenários para copiar blobs com o AzCopy.

### Copiar um único blob

**Carregue um arquivo do sistema de arquivos no armazenamento de blob:**

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt

**Baixe um blob do armazenamento de blob no sistema de arquivos:**

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

### Copiar um blob por meio da cópia no servidor

Uma operação de cópia no servidor é realizada quando você copia um blob dentro de uma conta de armazenamento ou em contas de armazenamento. Para obter mais informações sobre operações de cópia no servidor, consulte [Introdução ao blob de cópia assíncrona entre contas][Introdução ao blob de cópia assíncrona entre contas].

**Copie um blob dentro de uma conta de armazenamento:**

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceKey:key /DestKey:key /Pattern:abc.txt 

**Copie um blob entre contas de armazenamento:**

    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt

### Copiar um blob da região secundária

Se a conta de armazenamento tiver armazenamento de redundância geográfica com acesso de leitura habilitado, será possível copiar dados da região secundária.

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

Se o diretório virtual especificado não existir, o AzCopy carregará o arquivo para incluir o diretório virtual no nome (*por exemplo*, `vd/abc.txt` no exemplo acima).

### Baixar um blob em uma nova pasta

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

Se a pasta `C:\myfolder` ainda não existir, o AzCopy a criará no sistema de arquivos e baixará `abc.txt` na nova pasta.

### Carregar arquivos e subpastas de um diretório em um contêiner de maneira recursiva

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /S

A especificação da opção `/S` copia o conteúdo do diretório especificado para o armazenamento de blob de maneira recursiva, o que significa que todas as subpastas e seus arquivos serão copiados também. Por exemplo, suponhamos que os seguintes arquivos residam na pasta `C:\myfolder`:

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

Se você não especificar a opção `/S` na linha de comando, o AzCopy não copiará de maneira recursiva. Apenas os arquivos no diretório especificado são copiados. Subpastas e seus arquivos NÃO são copiados. Por exemplo, suponhamos que os seguintes arquivos residam na pasta `C:\myfolder`:

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

Depois da operação de cópia, o diretório `C:\myfolder` incluirá os seguintes arquivos:

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

Depois da operação de cópia, o diretório `C:\myfolder` incluirá os seguintes arquivos. Apenas os blobs no diretório virtual são copiados:

    C:\myfolder\a.txt
    C:\myfolder\abcd.txt

### Carregar arquivos correspondentes ao padrão de arquivo especificado em um contêiner de maneira recursiva

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:a* /S

Suponhamos que os seguintes arquivos residam na pasta `C:\myfolder`:

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

Suponhamos que os blobs a seguir residam no contêiner especificado. Todos os blobs que começarem com o prefixo `a` serão copiados:

    abc.txt
    abc1.txt
    abc2.txt
    xyz.txt
    vd1\a.txt
    vd1\abcd.txt

Depois da operação de cópia, a pasta `C:\myfolder` incluirá os seguintes arquivos:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

O prefixo se aplica ao diretório virtual, que forma a primeira parte do nome do blob. No exemplo mostrado acima, como não corresponde ao prefixo especificado, o diretório virtual não é copiado.

### Copiar um blob e seus instantâneos para outra conta de armazenamento

    AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt /Snapshot

Depois da operação de cópia, o contêiner de destino incluirá o blob e seus instantâneos. Supondo que o blob no exemplo acima tenha dois instantâneos, o contêiner incluirá os seguintes blob e instantâneos:

    abc.txt
    abc (2013-02-25 080757).txt
    abc (2014-02-21 150331).txt

### Usar um arquivo de resposta para especificar parâmetros de linha de comando

    AzCopy /@:"C:\myfolder\abc.txt"

É possível incluir qualquer parâmetro de linha de comando do AZCopy em um arquivo de resposta. O AzCopy processa os parâmetros no arquivo como se eles tivessem sido especificados na linha de comando, realizando uma substituição direta pelo conteúdo do arquivo.

**Especificar um ou mais arquivos de resposta de linha única**

Suponhamos um arquivo de resposta chamado `source.txt` que especifique um contêiner de origem:

    /Source:http://myaccount.blob.core.windows.net/mycontainer

E um arquivo de resposta chamado `dest.txt` que especifique uma pasta de destino no sistema de arquivos:

    /Dest:C:\myfolder

E um arquivo de resposta chamado `options.txt` que especifique opções para o AzCopy:

    /S /Y

Para chamar o AzCopy usando esses arquivos de resposta, todos eles residindo em um diretório `C:\responsefiles`, use este comando:

    AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /SourceKey:<sourcekey> /@:"C:\responsefiles\options.txt"   

O AzCopy processa esse comando assim como faria se você tivesse incluído todos os parâmetros individuais na linha de comando:

    AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

**Especifique um arquivo de resposta multilinhas**

Suponhamos um arquivo de resposta chamado `copyoperation.txt` que contenha as seguintes linhas. Todo parâmetro do AzCopy é especificado na própria linha:

    /Source:http://myaccount.blob.core.windows.net/mycontainer
    /Dest:C:\myfolder
    /SourceKey:<sourcekey>
    /S 
    /Y

Para chamar o AzCopy usando esses arquivos de resposta, use este comando:

    AzCopy /@:"C:\responsefiles\copyoperation.txt"

O AzCopy processa esse comando assim como faria se você tivesse incluído todos os parâmetros individuais na linha de comando:

    AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

Cada parâmetro do AzCopy deve ser especificado todo em uma única linha. Por exemplo, o AzCopy falhará se você dividir o parâmetro em duas linhas, conforme mostrado aqui para a o parâmetro `/sourcekey`:

    http://myaccount.blob.core.windows.net/mycontainer
    C:\myfolder
    /sourcekey:
    [sourcekey]
    /S 
    /Y

### Especificar uma SAS

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

Se você omitir a opção `/Z` ou especificar a opção `/Z` sem o caminho da pasta, conforme mostrado acima, o AzCopy criará o arquivo de diário no local padrão, que é `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`. Se o arquivo de diário já existir, o AzCopy retomará a operação com base no arquivo de diário.

**Especificar um local padrão para o arquivo de diário**

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z:C:\journalfolder\

Este exemplo criará o arquivo de diário se ele ainda não existir. Se ele existir, o AzCopy retomará a operação com base no arquivo de diário.

**Retomar uma operação do AzCopy**

    AzCopy /Z:C:\journalfolder\

Este exemplo retoma a última operação, cuja conclusão pode ter falhado.

### Gerar um arquivo de log

**Gravar o arquivo de log detalhado no local padrão**

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V

Se você especificar a opção `/V` sem fornecer um caminho de arquivo para o log detalhado, o AzCopy criará o arquivo de log no local padrão, que é `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`.

**Gravar o arquivo de log detalhado em um local personalizado**

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V:C:\myfolder\azcopy1.log

Se você especificar um caminho relativo depois da opção `/V`, como `/V:test/azcopy1.log`, o log detalhado será criado no diretório de trabalho atual dentro de uma subpasta chamada `test`.

### Definir a hora da última modificação dos arquivos baixados como sendo a mesma dos blobs de origem

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT

### Excluir blobs da operação de cópia com base na hora da última modificação

Especifique a opção `/MT` para comparar a hora da última modificação do blob de origem com a do arquivo de destino.

**Excluir blobs que sejam mais novos do que o arquivo de destino**

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XN

**Excluir blobs que sejam mais antigos do que o arquivo de destino**

    AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XO

### Especificar o número de operações simultâneas para começar

A opção `/NC` especifica o número de operações de cópia simultâneas. Por padrão, o AzCopy começará operações simultâneas com oito vezes o número de processadores de núcleo disponíveis. Se estiver executando o AzCopy em uma rede de baixa largura de banda, você pode especificar um número menor para essa opção a fim de evitar uma falha causada pela concorrência de recursos.

### Executar o AzCopy em recursos de blob no emulador de armazenamento

    AzCopy /Source:https://127.0.0.1:10004/myaccount/myfileshare/ /Dest:C:\myfolder /SourceKey:key /SourceType:Blob /S

## <span id="copy-files"></span></a> Copiar arquivos no armazenamento de arquivos do Azure com o AzCopy (somente na versão de teste)

Os exemplos abaixo demonstram vários cenários para copiar arquivos do Azure com o AzCopy.

### Baixar um arquivo de um compartilhamento de arquivos do Azure no sistema de arquivos

    AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/myfolder1/ /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

Se a origem especificada for um compartilhamento de arquivos do Azure, você deve especificar o nome do arquivo exato, (*ex.:* `abc.txt`) para copiar um único arquivo, ou especificar a opção `/S` para copiar todos os arquivos no compartilhamento de maneira recursiva. A tentativa de especificar ao mesmo tempo um padrão de arquivo e uma opção `/S` resultará em um erro.

### Baixar arquivos e pastas de um compartilhamento de arquivos do Azure no sistema de arquivos de maneira recursiva

    AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceKey:key /S

Nenhuma pasta vazia será copiada.

### Carregar arquivos e pastas do sistema de arquivos no compartilhamento de arquivos do Azure de maneira recursiva

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S

Nenhuma pasta vazia será copiada.

### Carregar arquivos correspondentes ao padrão de arquivo especificado em um compartilhamento de arquivos do Azure de maneira recursiva

    AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:ab* /S

## <span id="copy-entities"></span></a> Copie entidades em uma tabela do Azure com o AzCopy (somente na versão de teste)

Os exemplos abaixo demonstram vários cenários para copiar entidades de tabela do Azure com o AzCopy.

### Exportar entidades para o sistema de arquivos local

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:D:\test\ /SourceKey:key

### Exportar entidades para um blob do Azure

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:https://myaccount.blob.core.windows.net/mycontainer/ /SourceKey:key1 /Destkey:key2

O AzCopy gera um arquivo de dados JSON na pasta local ou no contêiner do blob, seguindo esta convenção de nomenclatura:

    <account name>_<table name>_<timestamp>_<volume index>_<CRC>.json

O arquivo de dados JSON gerado segue o formato de carga para metadados mínimos. Para obter detalhes sobre esse formado de carga, confira [Formato de carga para operações do serviço Tabela][Formato de carga para operações do serviço Tabela].

### Dividir os arquivos exportados

    AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceKey:key /S /SplitSize:100

O AzCopy usa um *índice de volume* nos nomes dos arquivos de dados da divisão para diferenciar diversos arquivos. O índice do volume é composto por duas partes: um *índice de intervalo de chaves de partição* e um *índice de arquivos de divisão*. Os dois índices são de base zero.

O índice do intervalo de chaves de partição será 0 se o usuário não especificar a opção `/PKRS` (apresentada na seção a seguir).

Por exemplo, digamos que o AzCopy gere dois arquivos de dados depois que o usuário especificar a opção `/SplitSize`. Os nomes dos arquivos de dados resultantes podem ser:

    myaccount_mytable_20140903T051850.8128447Z_0_0_C3040FE8.json
    myaccount_mytable_20140903T051850.8128447Z_0_1_0AB9AC20.json

Observe que o menor valor possível para a opção `/SplitSize` é 32 MB. Se o destino especificado for um armazenamento blob, o AzCopy dividirá o arquivo de dados quando alcançar o tamanho limite do blob (200 GB), mesmo que o usuário não tenha especificado a opção `/SplitSize` .

### Exportar entidades simultaneamente

    AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:D:\test\ /SourceKey:key /PKRS:"aa#bb"

O AzCopy inicia operações simultâneas para exportar entidades quando o usuário especifica a opção `/PKRS`. Cada operação exporta um intervalor de chaves de partição.

Observe que a opção `/NC` também contra a quantidade de operações simultâneas. O AzCopy usa a quantidade de processadores de núcleo como valor padrão de `/NC` ao copiar entidades de tabela, mesmo que `/NC` não tenha sido especificado. Quando o usuário especifica a opção `/PKRS`, o AzCopy usa o menor valor — entre os intervalos de chaves de partição ou operações simultâneas especificadas implícita ou explicitamente — para terminar quantas operações simultâneas devem ser iniciadas. Para obter mais detalhes, digite `AzCopy /?:NC` na linha de comando.

### Importar entidades simultaneamente

    AzCopy /Source:D:\test\ /Dest:https://myaccount.table.core.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace 

Quando você exporta entidades de tabelas, o AzCopy grava um arquivo de manifesto para a pasta de destino ou o contêiner do blob especificado. O arquivo do manifesto é usado pelo processo de importação para localizar os arquivos de dados necessários e executar a validação de dados durante o processo de importação. Os arquivos de manifesto seguem esta convenção de nomenclatura:

    <account name>_<table name>_<timestamp>.manifest

A opção `/EntityOperation` indica como inserir entidades na tabela. Os valores possíveis são:

-   `InsertOrSkip`: Ignora uma entidade existente ou insere uma nova entidade, caso ela não exista na tabela.
-   `InsertOrMerge`: Mescla uma entidade existente ou insere uma nova entidade, caso ela não exista na tabela.
-   `InsertOrReplace`: Substitui uma entidade existente ou insere uma nova entidade, caso ela não exista na tabela.

Observe que não é possível especificar a opção `/PKRS` no cenário de importação. Diferente do cenário de importação, no qual é necessário especificar a opção `/PKRS` para iniciar operações simultâneas, por padrão, o AzCopy inicia as operações simultâneas quando você importa as entidade. A quantidade padrão de operações iniciadas simultaneamente é igual à quantidade de processadores de núcleo. No entanto, você pode especificar outro valor com a opção `/NC`. Para obter mais detalhes, digite `AzCopy /?:NC` na linha de comando.

## <span id="versions"></span></a> Versões do AzCopy

| Versão     | O que há de novo                                                                                                                                                                                                                                   |
|------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **V4.0.0** | **Versão de teste atual. Inclui todas as funcionalidades do V3.0.0. Também permite copiar arquivos de ou para o armazenamento de arquivos do Azure e copiar entidades de ou para o armazenamento de tabela do Azure.**                             |
| **V3.0.0** | **Versão atual. Modifica a sintaxe da linha de comando do AzCopy para solicitar os nomes do parâmetro e redefine a ajuda da linha de comando. Essa versão suporta apenas a cópia de e para o armazenamento de blob do Azure.**                     |
| V2.5.1     | Versão atual. Otimiza o desempenho com o uso das opções /xo e /xn. Corrige bugs relacionados a caracteres especiais nos nomes dos arquivos de origem e os danos nos arquivos de diário quando o usuário insere sintaxe errada na linha de comando. |
| V2.5.0     | Otimiza o desempenho para cenários de cópia em larga escala e apresenta diversos aperfeiçoamentos de usabilidade importantes.                                                                                                                      |
| V2.4.1     | Permite a especificação da pasta de destino no assistente de instalação.                                                                                                                                                                           |
| V2.4.0     | Permite o upload e o download de arquivos para armazenamento de arquivo do Azure.                                                                                                                                                                  |
| V2.3.0     | Permite contas de armazenamento de redundância geográfica com acesso de leitura.                                                                                                                                                                   |
| V2.2.2     | Atualizado para usar a versão da biblioteca do cliente de armazenamento do Azure 3.0.3.                                                                                                                                                            |
| V2.2.1     | Problema de desempenho corrigido durante a cópia de arquivos muito grandes dentro da mesma conta de armazenamento.                                                                                                                                 |
| V2.2       | Permite a configuração do delimitador de diretório virtual para nomes de blob. Permite a especificação do caminho de arquivo de diário.                                                                                                            |
| V2.1       | Oferece mais de 20 opções para permitir operações de upload, download e cópia de blob de maneira eficiente.                                                                                                                                        |

## <span id="next-steps"></span></a>Próximas etapas

Para obter mais informações sobre o Armazenamento do Azure e o AzCopy, consulte os recursos a seguir.

### Documentação do Armazenamento do Azure:

-   [Introdução ao Armazenamento do Azure][Introdução ao Armazenamento do Azure]
-   [Armazenar arquivos no armazenamento de blob][Armazenar arquivos no armazenamento de blob]
-   [Cria um compartilhamento de arquivos SMB no Azure e no armazenamento de arquivo][Cria um compartilhamento de arquivos SMB no Azure e no armazenamento de arquivo]

### Postagens de blog de armazenamento do Azure:

-   [Apresentando o serviço de arquivo do Microsoft Azure][Apresentando o serviço de arquivo do Microsoft Azure]
-   [AzCopy 2.5: Otimizado para cenários de cópia em larga escala][AzCopy 2.5: Otimizado para cenários de cópia em larga escala]
-   [AzCopy: Suporte à redundância geográfica com acesso de leitura][AzCopy: Suporte à redundância geográfica com acesso de leitura]
-   [AzCopy: Transferir dados com modo reiniciável e token da SAS][AzCopy: Transferir dados com modo reiniciável e token da SAS]
-   [AzCopy: Usando blob de cópia em várias contas][AzCopy: Usando blob de cópia em várias contas]
-   [AzCopy: Carregando/baixando arquivos de blobs do Microsoft Azure][AzCopy: Carregando/baixando arquivos de blobs do Microsoft Azure]

  [Baixe e instale o AzCopy]: #install
  [Compreenda a sintaxe da linha de comando do AZCopy]: #syntax
  [Limite gravações simultâneas durante a cópia de dados]: #limit-writes
  [Copie blobs do Azure com o AzCopy]: #copy-blobs
  [Copie arquivos em um compartilhamento de arquivos do Azure com o AzCopy (somente na versão de teste)]: #copy-files
  [Copie as entidades de tabela do Azure com o AzCopy (somente na versão de teste)]: #copy-entities
  [Versões do AzCopy]: #versions
  [Próximas etapas]: #next-steps
  [versão mais recente do AzCopy]: http://aka.ms/downloadazcopy
  [última versão de teste]: http://aka.ms/downloadazcopypr
  [Introdução ao blob de cópia assíncrona entre contas]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx
  [Formato de carga para operações do serviço Tabela]: http://msdn.microsoft.com/library/azure/dn535600.aspx
  [Introdução ao Armazenamento do Azure]: http://azure.microsoft.com/pt-br/documentation/articles/storage-introduction/
  [Armazenar arquivos no armazenamento de blob]: http://azure.microsoft.com/pt-br/documentation/articles/storage-dotnet-how-to-use-blobs/
  [Cria um compartilhamento de arquivos SMB no Azure e no armazenamento de arquivo]: http://azure.microsoft.com/pt-br/documentation/articles/storage-dotnet-how-to-use-files/
  [Apresentando o serviço de arquivo do Microsoft Azure]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx
  [AzCopy 2.5: Otimizado para cenários de cópia em larga escala]: http://go.microsoft.com/fwlink/?LinkId=507682
  [AzCopy: Suporte à redundância geográfica com acesso de leitura]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx
  [AzCopy: Transferir dados com modo reiniciável e token da SAS]: http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx
  [AzCopy: Usando blob de cópia em várias contas]: http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx
  [AzCopy: Carregando/baixando arquivos de blobs do Microsoft Azure]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx
