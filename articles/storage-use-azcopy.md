<properties linkid="storage-use-azcopy" urlDisplayName="AzCopy" pageTitle="How to use AzCopy with Microsoft Azure Storage" metaKeywords="Get started Azure AzCopy   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage   Azure file   Azure file storage   Azure file share   AzCopy" description="Learn how to use the AzCopy utility to upload, download, and copy blob and file content." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter="" title="How to use AzCopy with Microsoft Azure Storage" authors="tamram" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/06/2014" ms.author="tamram"></tags>

# Introdução ao utilitário de linha de comando AzCopy

AzCopy é um utilitário de linha de comando projetado para upload, download e cópia de dados de alto desempenho do e para o armazenamento de blob e arquivo do Microsoft Azure. Este guia apresenta uma visão geral do uso do AzCopy.

> [WACOM.NOTE] Este guia pressupõe que você tenha o AzCopy 2.5, ou posterior, instalado. Como o AzCopy está em pré-lançamento, as opções de linha de comando e suas funções poderão mudar em versões futuras.

## Sumário

-   [Baixe e instale o AzCopy][Baixe e instale o AzCopy]
-   [Compreenda a sintaxe da linha de comando do AZCopy][Compreenda a sintaxe da linha de comando do AZCopy]
-   [Limite gravações simultâneas durante a cópia de dados][Limite gravações simultâneas durante a cópia de dados]
-   [Copie blobs do Azure com o AzCopy][Copie blobs do Azure com o AzCopy]
-   [Copie arquivos em um compartilhamento de arquivos do Azure com o AzCopy][Copie arquivos em um compartilhamento de arquivos do Azure com o AzCopy]
-   [Versões do AzCopy][Versões do AzCopy]
-   [Próximas etapas][Próximas etapas]

## <span id="install"></span></a> Baixar e instalar o AzCopy

1.  Baixe a [versão mais recente do AzCopy][versão mais recente do AzCopy].
2.  Execute a instalação. Por padrão, a instalação do AzCopy cria uma pasta chamada `AzCopy` em `%ProgramFiles(x86)%\Microsoft SDKs\Azure\` (em um computador sendo executado no Windows 64 bits) ou `%ProgramFiles%\Microsoft SDKs\Azure\` (em um computador sendo executado no Windows 32 bits). No entanto, é possível alterar o caminho da instalação por meio do assistente de instalação.
3.  Se quiser, você pode alterar o local da instalação do AzCopy para o caminho do sistema.

## <span id="syntax"></span></a> Entenda a sintaxe da linha de comando do AZCopy

Em seguida, abra uma janela de comando e navegue até o diretório de instalação do AzCopy no computador, onde o executável `AzCopy.exe` está localizado. A sintaxe básica dos comandos do AzCopy é:

    AzCopy <source> <destination> [filepattern] [options]

-   O parâmetro `<source>` especifica os dados de origem para cópia. A origem pode ser um diretório do sistema de arquivos, um contêiner de blob, um diretório virtual de blob ou um compartilhamento de arquivos de armazenamento.

-   O parâmetro `<destination>` especifica o destino da cópia. O destino pode ser um diretório do sistema de arquivos, um contêiner de blob, um diretório virtual de blob ou um compartilhamento de arquivos de armazenamento.

-   O comportamento do parâmetro `filepattern` opcional é determinado pelo local dos dados de origem e pela presença da opção do modo recursivo. O modo recursivo é especificado pela opção `/S`.

    Se a origem especificada for um diretório no sistema de arquivos, os curingas padrão estão em vigor, e o padrão de arquivo fornecido é comparado com os arquivos dentro do diretório. Se a opção `/S` for especificada, o AzCopy também compara o padrão especificado com todos os arquivos em todas as subpastas abaixo da categoria.

    Se a origem especificada for um contêiner de blob ou um diretório virtual, os curingas não são aplicados. Se a opção `/S` for especificada, o AzCopy interpreta o padrão do arquivo especificado como um prefixo de blob. Se a opção `/S` não for especificada, o AzCopy compara o padrão do arquivo com os nomes de blob exatos.

    Se a origem especificada for um compartilhamento de arquivos do Azure, você deve especificar o nome do arquivo exato, (*ex.:* `abc.txt`) para copiar um único arquivo, ou especificar a opção `/S` para copiar todos os arquivos no compartilhamento de maneira recursiva. A tentativa de especificar ao mesmo tempo um padrão de arquivo e uma opção `/S` resultará em um erro.

    O padrão de arquivo usado quando nenhum padrão de arquivo é especificado é `*.*` para um diretório do sistema de arquivos, ou um prefixo vazio para um recurso de armazenamento de Blob ou Arquivo do Azure.

    > [WACOM.NOTE] Por questões de desempenho, o AzCopy versão 2.5 não permite mais vários padrões de arquivo em um único comando. Agora você tem que emitir vários comandos, cada um com um único padrão de arquivo, para resolver o cenário de vários padrões de arquivo.

-   As opções disponíveis para o parâmetro `options` são descritas na tabela abaixo. Também é possível digitar `AzCopy /?` na linha de comando para obter ajuda em relação às opções.

<table>
<tr><th> Nome da opção </th><th> Descrição  </th><th> Aplicável ao armazenamento de blob (S/N) </th><th> Aplicável ao armazenamento de arquivo (S/N) </th></tr>
<tr><td> <strong>/DestKey:&lt;storage-key&gt;</strong>     </td><td> Especifica a chave de conta de armazenamento do recurso de destino.                                                                                                                                                                                                                                                                                                                             </td><td> S                                        </td><td> S                                         </td></tr>
<tr><td> <strong>/DestSAS:&lt;container-SAS&gt;</strong>   </td><td> Especifica uma SAS (Assinatura de Acesso Compartilhado) para o contêiner de destino (se aplicável). Coloque a SAS entre aspas duplas, porque ela pode conter caracteres de linha de comando especiais.<br />Se o recurso de destino for um blob, será possível especificar essa opção seguida do token da SAS ou a SAS como parte do URI do blob de destino, sem essa opção.<br />Essa opção só é aplicável para o armazenamento de blob e só é válida durante o upload ou a cópia de blobs dentro da mesma conta de armazenamento.                                                                                                                                                                                                                                               </td><td> S                                        </td><td> N                                          </td></tr>
<tr><td> <strong>/SourceKey:&lt;storage-key&gt;</strong>   </td><td> Especifica a chave de conta de armazenamento para o recurso de origem.                                                                                                                                                                                                                                                                                                                          </td><td> S                                        </td><td> S                                          </td></tr>
<tr><td> <strong>/SourceSAS:&lt;container-SAS&gt;</strong> </td><td> Especifica uma SAS para o contêiner de origem (se aplicável). Coloque a SAS entre aspas duplas, porque ela pode conter caracteres de linha de comando especiais.<br />Se não for fornecida uma chave nem uma SAS, o contêiner será lido por meio de acesso anônimo.<br />Essa opção só é aplicável para armazenamento de blob.                                                                                                                                                                                                                                                                                                                                           </td><td> S                                        </td><td> N                                           </td></tr>
<tr><td> <strong>/S</strong>                           </td><td> Especifica o modo recursivo para operações de cópia. No modo recursivo, o AzCopy copiará todos os blobs ou arquivos correspondentes ao padrão do arquivo, inclusive os presentes nas subpastas.                                                                                                                                                                                                 </td><td> S                                        </td><td> S                                          </td></tr>
<tr><td> <strong>/BlobType:&lt;block | page\></strong>   </td><td> Especifica que o destino é um blob de blocos ou um blob de páginas. Essa opção só é aplicável quando o destino é um blob, do contrário, um erro é gerado. Se o destino for um blob e essa opção não estiver especificada, por padrão, o AzCopy poderá pressupor que um blob de blocos é desejado.                                                                                               </td><td> S                                        </td><td> N                                           </td></tr>
<tr><td> <strong>/CheckMd5</strong>                    </td><td> Calcula um hash MD5 para dados baixados e certifica que o hash MD5 armazenado no blob, ou a propriedade Content-MD5 do arquivo, corresponde ao hash calculado. Como, por padrão, a verificação MD5 permanece desativada, você deve especificar essa opção para realizar a verificação MD5 ao baixar os dados.<br />O Azure Storage não assegura que o hash MD5 armazenado para o blob ou o arquivo esteja atualizado. É de responsabilidade do cliente atualizar o MD5 sempre que o blob ou o arquivo é modificado.<br />O AzCopy sempre define a propriedade Content-MD5 para um blob ou um arquivo do Azure depois de carregá-lo no serviço.                                                                                                                                                                                                                                                                           </td><td> S                                        </td><td> S                                           </td></tr>
<tr><td> <strong>/Snapshot</strong>                    </td><td> Indica se é necessário transferir ou não os instantâneos. Essa opção só é válida quando a origem é um blob.<br />Os instantâneos de blob transferidos são renomeados neste formato: [nome_do_blob] (hora_do_instantâneo)[extensão]. <br />Por padrão, os instantâneos não são copiados.                                                                                                                                                                                                                                                                                                                                                   </td><td> S                                       </td><td> N                                           </td></tr>
<tr><td> <strong>/V:[verbose log-file]</strong>        </td><td> Produz mensagens de status detalhadas em um arquivo de log. Por padrão, o arquivo de log detalhado é chamado de <code>AzCopyVerbose.log</code> em <code>%LocalAppData%\Microsoft\Azure\AzCopy</code>. Se você especificar um local de arquivo existente para essa opção, o log detalhado será acrescentado a esse arquivo.                                                                                           </td><td> S                                        </td><td> S                                          </td></tr>
<tr><td> <strong>/Z:[journal-file-folder]</strong>     </td><td> Especifica uma pasta de arquivo de diário para retomar uma operação.<br />O AzCopy sempre dará suporte à retomada caso uma operação tenha sido interrompida.<br />Se essa opção não for especificada ou for especificada sem um caminho de pasta, o AzCopy criará o arquivo de diário no local padrão, que é <code>%LocalAppData%\Microsoft\Azure\AzCopy</code>.<br />Sempre que você emite um comando para o AzCopy, ele verifica se um arquivo de diário existe na pasta padrão ou se está em uma pasta especificada por meio dessa opção. Se o arquivo de diário não estiver em nenhum dos lugares, o AzCopy tratará a operação como nova e gerar um novo arquivo de diário. <br />Se o arquivo de diário existir, o AzCopy verificará se a linha de comando inserida corresponde à linha de comando no arquivo de diário. Se as duas linhas de comando forem correspondentes, o AzCopy retomará a operação incompleta. Se elas não forem correspondentes, será solicitado que você substitua o arquivo de diário para iniciar uma nova operação ou que cancele a operação atual.<br />O arquivo de diário é excluído mediante a conclusão bem-sucedida da operação.<br />A retomada de uma operação de um arquivo de diário criado por uma versão anterior do AzCopy não é compatível.                                                                                                                                                                                                                                                                                   </td><td> S                                        </td><td> S                                           </td></tr>
<tr><td> <strong>/@:response-file</strong>             </td><td> Especifica um arquivo que contém parâmetros. O AzCopy processa os parâmetros no arquivo como se eles tivessem sido especificados na linha de comando. <br />Em um arquivo de resposta, é possível especificar vários parâmetros em um único arquivo ou especificar cada parâmetro na própria linha. Um parâmetro individual não pode abranger várias linhas.<br />Os arquivos de resposta podem incluir linhas de comentários iniciadas pelo símbolo <code>#</code>.     <br />É possível especificar vários arquivos de resposta. No entanto, o AzCopy não permite arquivos de resposta aninhados.                                                                                                                                                                                                                                                                            </td><td> S                                        </td><td> S                                           </td></tr>
<tr><td> <strong>/Y</strong>                           </td><td> Suprime todas as solicitações de confirmação do AzCopy.                                                                                                                                                                                                                                                                                                                                         </td><td> S                                        </td><td> S                                           </td></tr>
<tr><td> <strong>/L</strong>                           </td><td> Especifica uma operação de listagem apenas. Nenhum dado é copiado.                                                                                                                                                                                                                                                                                                                              </td><td> S                                       </td><td> S                                           </td></tr>
<tr><td> <strong>/MT</strong>                          </td><td> Define a hora da última modificação do arquivo baixado como a mesma do blob de origem ou do arquivo.                                                                                                                                                                                                                                                                                            </td><td> S                                        </td><td> S                                           </td></tr>
<tr><td> <strong>/XN</strong>                          </td><td> Exclui um recurso de origem mais novo. O recurso não será copiado se a origem for mais nova do que o destino.                                                                                                                                                                                                                                                                                   </td><td> S                                        </td><td> S                                           </td></tr>
<tr><td> <strong>/XO</strong>                          </td><td> Exclui um recurso de origem mais antigo. O recurso não será copiado se o recurso de origem for mais antigo do que o destino.                                                                                                                                                                                                                                                                    </td><td> S                                       </td><td> S                                           </td></tr>
<tr><td> <strong>/A</strong>                           </td><td> Carrega apenas arquivos que tenham o atributo Archive definido.                                                                                                                                                                                                                                                                                                                                 </td><td> S                                        </td><td> S                                           </td></tr>
<tr><td> <strong>/IA:[RASHCNETOI]</strong>             </td><td> Carrega apenas arquivos que tenham algum dos atributos especificados definido.<br />Entre os atributos disponíveis estão:<br />R   Arquivos somente leitura<br />A   Arquivos prontos para arquivamento <br /> S   Arquivos de sistema <br />H   Arquivos ocultos <br />C   Arquivo compactado<br />N   Arquivos normais <br />E   Arquivos criptografados <br />T   Arquivos temporários<br />O   Arquivos offline<br />I   Arquivos não indexados                                                                                                                                                                                                                                                                                                                                                                      </td><td> S                                        </td><td> S                                           </td></tr>
<tr><td> <strong>/XA:[RASHCNETOI]</strong>             </td><td> Exclui arquivos que tenham qualquer dos atributos especificados definido.<br /> Entre os atributos disponíveis estão: <br /> R   Arquivos somente leitura<br />A   Arquivos prontos para arquivamento<br />S   Arquivos de sistema <br />H   Arquivos ocultos<br /> C   Arquivo compactado<br />N   Arquivos normais <br />E   Arquivos criptografados<br />T   Arquivos temporários<br /> O   Arquivos offline  <br />I   Arquivos não indexados                                                                                                                                                                                                                                                                                                                                                                      </td><td> S                                        </td><td> S                                           </td></tr>
<tr><td> <strong>/Delimiter:\<delimiter\></strong>     </td><td> Indica o caractere delimitador usado para delimitar diretórios virtuais em um nome de blob.<br />Por padrão, o AzCopy usa / como o caractere delimitador. No entanto, o AzCopy dá suporte ao uso de qualquer caractere comum (como @, \# ou %) como delimitador. Se precisar incluir um desses caracteres especiais na linha de comando, coloque o nome do arquivo entre aspas duplas.<br />Essa opção só é aplicável para o download de blobs.                                                                                                                                                                                                                                                                                                                                             </td><td> S                                       </td><td> N                                           </td></tr>
<tr><td> <strong>/NC</strong>                          </td><td> Especifica o número de operações simultâneas. <br />O número padrão é 8 vezes o número de processadores de núcleo em execução. Assim, se o computador tiver 8 núcleos, por padrão, o AzCopy iniciará 64 operações simultâneas.<br />O grande número de operações simultâneas em um ambiente com baixa largura de banca pode sobrecarregar a conexão de rede, evitando que as operações sejam totalmente concluídas. Limite operações simultâneas com base na largura de banda real de rede disponível.                                                                                                                              </td><td> S                                        </td><td> S                                           </td></tr>
<tr><td> <strong>/SourceType:Blob</strong>             </td><td> Especifica se o recurso `source` é um blob disponível no ambiente de desenvolvimento local, em execução no emulador de armazenamento.                                                                                                                                                                                                                                                           </td><td> S                                        </td><td> N                                           </td></tr>
<tr><td> <strong>/DestType:Blob</strong>               </td><td> Especifica se o recurso `destination` é um blob disponível no ambiente de desenvolvimento local, em execução no emulador de armazenamento.                                                                                                                                                                                                                                                      </td><td> S                                        </td><td> N                                           </td></tr>
</table>

## <span id="limit-writes"></span></a> Limite gravações simultâneas durante a cópia de dados

Ao copiar blobs ou arquivos usando o AZCopy, lembre-se de que outro aplicativo pode estar modificando os dados enquanto você os copia. Se possível, verifique se os dados que está copiando não estão sendo modificados durante a cópia. Por exemplo, ao copiar um VHD associado a uma máquina virtual do Azure, verifique se nenhum outro aplicativo está gravando no VHD, no momento. Também é possível criar um instantâneo do VHD primeiro e, em seguida, copiar o instantâneo.

Se não for possível evitar que outros aplicativos gravem em blobs ou arquivos enquanto são copiados, lembre-se que, quando o trabalho terminar, os recursos copiados não poderão mais ter paridade total com os recursos de origem.

## <span id="copy-blobs"></span></a> Copie blobs do Azure com o AzCopy

Os exemplos abaixo demonstram vários cenários para copiar blobs com o AzCopy.

### Copiar um único blob

**Carregue um arquivo do sistema de arquivos no armazenamento de blob:**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key abc.txt

**Baixe um blob do armazenamento de blob no sistema de arquivos:**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key abc.txt

### Copiar um blob por meio da cópia no servidor

Uma operação de cópia no servidor é realizada quando você copia um blob dentro de uma conta de armazenamento ou em contas de armazenamento. Para obter mais informações sobre operações de cópia no servidor, consulte [Introdução ao blob de cópia assíncrona entre contas][Introdução ao blob de cópia assíncrona entre contas].

**Copie um blob dentro de uma conta de armazenamento:**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer/mycontainer1 https://myaccount.blob.core.windows.net/mycontainer2 /sourcekey:key /destkey:key abc.txt 

**Copie um blob entre contas de armazenamento:**

    AzCopy https://sourceaccount.blob.core.windows.net/mycontainer/mycontainer1 https://destaccount.blob.core.windows.net/mycontainer2 /sourcekey:key1 /destkey:key2 abc.txt

### Copiar um blob da região secundária

Se a conta de armazenamento tiver armazenamento de redundância geográfica com acesso de leitura habilitado, será possível copiar dados da região secundária.

**Copie um blob para a conta primária por meio da secundária:**

    AzCopy https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 https://myaccount2.blob.core.windows.net/mynewcontainer2 /sourcekey:key1 /destkey:key2 abc.txt

**Baixe um blob na secundária para um arquivo no sistema de arquivos:**

    AzCopy https://myaccount-secondary.blob.core.windows.net/mynewcontainer C:\myfolder /sourcekey:key abc.txt

### Carregar um arquivo em um novo contêiner de blob ou diretório virtual

**Carregue um arquivo em um novo contêiner de blob**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mynewcontainer /destkey:key abc.txt

Se o contêiner de destino especificado não existir, o AzCopy o criará e carregará o arquivo nele.

**Carregue um arquivo em um novo diretório virtual de blob**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer/vd /destkey:key abc.txt

Se o diretório virtual especificado não existir, o AzCopy carregará o arquivo para incluir o diretório virtual no nome (*por exemplo*, `vd/abc.txt` no exemplo acima).

### Baixar um blob em uma nova pasta

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key abc.txt

Se a pasta `C:\myfolder` ainda não existir, o AzCopy a criará no sistema de arquivos e baixará `abc.txt` na nova pasta.

### Carregar arquivos e subpastas de um diretório em um contêiner de maneira recursiva

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key /S

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

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key

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

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key /S

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

    AzCopy https://myaccount.blob.core.windows.net/mycontainer/vd1/ C:\myfolder /sourcekey:key /S

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

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key a* /S

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

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key a /S

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

    AzCopy https://sourceaccount.blob.core.windows.net/mycontainer/mycontainer1 https://destaccount.blob.core.windows.net/mycontainer2 /sourcekey:key1 /destkey:key2 abc.txt /snapshot

Depois da operação de cópia, o contêiner de destino incluirá o blob e seus instantâneos. Supondo que o blob no exemplo acima tenha dois instantâneos, o contêiner incluirá os seguintes blob e instantâneos:

    abc.txt
    abc (2013-02-25 080757).txt
    abc (2014-02-21 150331).txt

### Usar um arquivo de resposta para especificar parâmetros de linha de comando

    AzCopy /@:"C:\myfolder\abc.txt"

É possível incluir qualquer parâmetro de linha de comando do AZCopy em um arquivo de resposta. O AzCopy processa os parâmetros no arquivo como se eles tivessem sido especificados na linha de comando, realizando uma substituição direta pelo conteúdo do arquivo.

**Especificar um ou mais arquivos de resposta de linha única**

Suponhamos um arquivo de resposta chamado `source.txt` que especifique um contêiner de origem:

    http://myaccount.blob.core.windows.net/mycontainer

E um arquivo de resposta chamado `dest.txt` que especifique uma pasta de destino no sistema de arquivos:

    C:\myfolder

E um arquivo de resposta chamado `options.txt` que especifique opções para o AzCopy:

    /S /Y

Para chamar o AzCopy usando esses arquivos de resposta, todos eles residindo em um diretório `C:\responsefiles`, use este comando:

    AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /sourcekey:[sourcekey] /@:"C:\responsefiles\options.txt"   

O AzCopy processa esse comando assim como faria se você tivesse incluído todos os parâmetros individuais na linha de comando:

    AzCopy http://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:[sourcekey] /S /Y

**Especifique um arquivo de resposta multilinhas**

Suponhamos um arquivo de resposta chamado `copyoperation.txt` que contenha as seguintes linhas. Todo parâmetro do AzCopy é especificado na própria linha:

    http://myaccount.blob.core.windows.net/mycontainer
    C:\myfolder
    /sourcekey:[sourcekey]
    /S 
    /Y

Para chamar o AzCopy usando esses arquivos de resposta, use este comando:

    AzCopy /@:"C:\responsefiles\copyoperation.txt"

O AzCopy processa esse comando assim como faria se você tivesse incluído todos os parâmetros individuais na linha de comando:

    AzCopy http://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:[sourcekey] /S /Y

Cada parâmetro do AzCopy deve ser especificado todo em uma única linha. Por exemplo, o AzCopy falhará se você dividir o parâmetro em duas linhas, conforme mostrado aqui para a o parâmetro `/sourcekey`:

    http://myaccount.blob.core.windows.net/mycontainer
    C:\myfolder
    /sourcekey:
    [sourcekey]
    /S 
    /Y

### Especificar uma SAS

**Especificar uma SAS para o contêiner de origem usando a opção /sourceSAS**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer1 C:\myfolder /sourceSAS:SAS /S

**Especificar uma SAS para o contêiner de origem no URI do contêiner de origem**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken C:\myfolder /S

**Especificar uma SAS para o contêiner de destino usando a opção /destSAS**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer1 /destSAS:SAS abc.txt

**Especificar uma SAS para os contêineres de origem e destino**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer1 https://myaccount.blob.core.windows.net/mycontainer2 /sourceSAS:SAS1 /destSAS:SAS2 abc.txt

### Especificar uma pasta de arquivo de diário

Sempre que você emite um comando para o AzCopy, ele verifica se um arquivo de diário existe na pasta padrão ou se está em uma pasta especificada por meio dessa opção. Se o arquivo de diário não estiver em nenhum dos lugares, o AzCopy tratará a operação como nova e gerar um novo arquivo de diário.

Se o arquivo de diário existir, o AzCopy verificará se a linha de comando inserida corresponde à linha de comando no arquivo de diário. Se as duas linhas de comando forem correspondentes, o AzCopy retomará a operação incompleta. Se elas não forem correspondentes, será solicitado que você substitua o arquivo de diário para iniciar uma nova operação ou que cancele a operação atual.

**Usar o local padrão para o arquivo de diário**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key /Z

Se você omitir a opção `/Z` ou especificar a opção `/Z` sem o caminho da pasta, conforme mostrado acima, o AzCopy criará o arquivo de diário no local padrão, que é `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`. Se o arquivo de diário já existir, o AzCopy retomará a operação com base no arquivo de diário.

**Especificar um local padrão para o arquivo de diário**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key /Z:C:\journalfolder\

Este exemplo criará o arquivo de diário se ele ainda não existir. Se ele existir, o AzCopy retomará a operação com base no arquivo de diário.

**Retomar uma operação do AzCopy**

    AzCopy /Z:C:\journalfolder\

Este exemplo retoma a última operação, cuja conclusão pode ter falhado.

### Gerar um arquivo de log

**Gravar o arquivo de log detalhado no local padrão**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key /V

Se você especificar a opção `/V` sem fornecer um caminho de arquivo para o log detalhado, o AzCopy criará o arquivo de log no local padrão, que é `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`.

**Gravar o arquivo de log detalhado em um local personalizado**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key /V:C:\myfolder\azcopy1.log

Se você especificar um caminho relativo depois da opção `/V`, como `/V:test/azcopy1.log`, o log detalhado será criado no diretório de trabalho atual dentro de uma subpasta chamada `test`.

### Definir a hora da última modificação dos arquivos baixados como sendo a mesma dos blobs de origem

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key /MT

### Excluir blobs da operação de cópia com base na hora da última modificação

Especifique a opção `/MT` para comparar a hora da última modificação do blob de origem com a do arquivo de destino.

**Excluir blobs que sejam mais novos do que o arquivo de destino**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key /MT /XN

**Excluir blobs que sejam mais antigos do que o arquivo de destino**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key /MT /XO

### Especificar o número de operações simultâneas para começar

A opção `/NC` especifica o número de operações de cópia simultâneas. Por padrão, o AzCopy começará operações simultâneas com oito vezes o número de processadores de núcleo disponíveis. Se estiver executando o AzCopy em uma rede de baixa largura de banda, você pode especificar um número menor para essa opção a fim de evitar uma falha causada pela concorrência de recursos.

### Executar o AzCopy em recursos de blob no emulador de armazenamento

    AzCopy https://127.0.0.1:10004/myaccount/myfileshare/ C:\myfolder /SourceKey:key /SourceType:Blob /S

## <span id="copy-files"></span></a> Copie arquivos em um compartilhamento de arquivos do Azure com o AzCopy

Os exemplos abaixo demonstram vários cenários para copiar arquivos do Azure com o AzCopy.

### Baixar um arquivo de um compartilhamento de arquivos do Azure no sistema de arquivos

    AzCopy https://myaccount.file.core.windows.net/myfileshare/myfolder1/ C:\myfolder /SourceKey:key abc.txt

Se a origem especificada for um compartilhamento de arquivos do Azure, você deve especificar o nome do arquivo exato, (*ex.:* `abc.txt`) para copiar um único arquivo, ou especificar a opção `/S` para copiar todos os arquivos no compartilhamento de maneira recursiva. A tentativa de especificar ao mesmo tempo um padrão de arquivo e uma opção `/S` resultará em um erro.

### Baixar arquivos e pastas de um compartilhamento de arquivos do Azure no sistema de arquivos de maneira recursiva

    AzCopy https://myaccount.file.core.windows.net/myfileshare/ C:\myfolder /SourceKey:key /S

Nenhuma pasta vazia será copiada.

### Carregar arquivos e pastas do sistema de arquivos no compartilhamento de arquivos do Azure de maneira recursiva

    AzCopy C:\myfolder https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S

Nenhuma pasta vazia será copiada.

### Carregar arquivos correspondentes ao padrão de arquivo especificado em um compartilhamento de arquivos do Azure de maneira recursiva

    AzCopy C:\myfolder https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key ab* /S

## <span id="versions"></span></a> Versões do AzCopy

| Versão     | O que há de novo                                                                                                                                |
|------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| **V2.5.0** | **Versão atual. Otimiza o desempenho para cenários de cópia em larga escala e apresenta diversos aperfeiçoamentos de usabilidade importantes.** |
| V2.4.1     | Permite a especificação da pasta de destino no assistente de instalação.                                                                        |
| V2.4.0     | Permite o upload e o download de arquivos para armazenamento de arquivo do Azure.                                                               |
| V2.3.0     | Permite contas de armazenamento de redundância geográfica com acesso de leitura.                                                                |
| V2.2.2     | Atualizado para usar a versão da biblioteca do cliente de armazenamento do Azure 3.0.3.                                                         |
| V2.2.1     | Problema de desempenho corrigido durante a cópia de arquivos muito grandes dentro da mesma conta de armazenamento.                              |
| V2.2       | Permite a configuração do delimitador de diretório virtual para nomes de blob. Permite a especificação do caminho de arquivo de diário.         |
| V2.1       | Oferece mais de 20 opções para permitir operações de upload, download e cópia de blob de maneira eficiente.                                     |

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
  [Copie arquivos em um compartilhamento de arquivos do Azure com o AzCopy]: #copy-files
  [Versões do AzCopy]: #versions
  [Próximas etapas]: #next-steps
  [versão mais recente do AzCopy]: http://aka.ms/downloadazcopy
  [Introdução ao blob de cópia assíncrona entre contas]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx
  [Introdução ao Armazenamento do Azure]: http://azure.microsoft.com/pt-br/documentation/articles/storage-introduction/
  [Armazenar arquivos no armazenamento de blob]: http://azure.microsoft.com/pt-br/documentation/articles/storage-dotnet-how-to-use-blobs/
  [Cria um compartilhamento de arquivos SMB no Azure e no armazenamento de arquivo]: http://azure.microsoft.com/pt-br/documentation/articles/storage-dotnet-how-to-use-files/
  [Apresentando o serviço de arquivo do Microsoft Azure]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx
  [AzCopy 2.5: Otimizado para cenários de cópia em larga escala]: http://go.microsoft.com/fwlink/?LinkId=507682
  [AzCopy: Suporte à redundância geográfica com acesso de leitura]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx
  [AzCopy: Transferir dados com modo reiniciável e token da SAS]: http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx
  [AzCopy: Usando blob de cópia em várias contas]: http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx
  [AzCopy: Carregando/baixando arquivos de blobs do Microsoft Azure]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx
