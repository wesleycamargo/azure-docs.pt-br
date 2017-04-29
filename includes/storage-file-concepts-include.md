## <a name="what-is-azure-file-storage"></a>O que é o armazenamento de arquivos do Azure?
O armazenamento de arquivos oferece armazenamento compartilhado para aplicativos que usem o protocolo padrão SMB 2.1 ou SMB 3.0. As máquinas virtuais e os serviços de nuvem do Microsoft Azure podem compartilhar dados de arquivos entre componentes de aplicativos por meio de compartilhamentos montados, e aplicativos locais podem acessar dados de arquivos em um compartilhamento por meio da API de armazenamento de arquivo.

Aplicativos executados em máquinas virtuais do Azure ou em serviços de nuvem podem montar um compartilhamento de armazenamento de arquivo para acessar dados de arquivos, assim como um aplicativo de área de trabalho montaria um compartilhamento SMB típico. Qualquer quantidade de máquinas virtuais ou funções do Azure podem montar e acessar o compartilhamento de armazenamento de arquivos simultaneamente.

Como um compartilhamento de Armazenamento de arquivos é um compartilhamento de arquivo padrão no Azure que usa o protocolo SMB, os aplicativos executados no Azure poderão acessar os dados no compartilhamento por meio das APIs de E/S de arquivo. Os desenvolvedores podem, portanto, utilizar seus códigos e habilidades existentes para migrar aplicativos existentes. Os profissionais de TI podem usar cmdlets do PowerShell para criar, montar e gerenciar compartilhamentos de armazenamento de arquivos como parte da administração de aplicativos do Azure. Este guia mostrará exemplos de ambos os casos.

Os usos comuns do armazenamento de arquivos incluem:

* Migrar aplicativos locais que dependem dos compartilhamentos de arquivos para serem executados em máquinas virtuais ou em serviços de nuvem do Azure, sem regravações caras
* Armazenar configurações de aplicativos compartilhados, por exemplo, em arquivos de configuração
* Armazenar dados de diagnóstico, como logs, métricas e despejos de falha em um local compartilhado 
* Armazenar ferramentas e utilitários necessários para desenvolver ou administrar máquinas virtuais ou serviços de nuvem do Azure

## <a name="file-storage-concepts"></a>Conceitos do armazenamento de arquivo
O armazenamento de arquivos contém os seguintes componentes:

![files-concepts](./media/storage-file-concepts-include/files-concepts.png)

* **Conta de Armazenamento:** todo o acesso ao Armazenamento do Azure é feito através de uma conta de armazenamento. Consulte [Escalabilidade e Metas de Desempenho do Armazenamento do Azure](../articles/storage/storage-scalability-targets.md) para obter detalhes sobre a capacidade da conta de armazenamento.
* **Compartilhamento** : um compartilhamento do armazenamento de arquivos é um compartilhamento de arquivos SMB no Azure. 
  Todos os arquivos e diretórios devem ser criados em um compartilhamento pai. Uma conta de armazenamento pode conter um número ilimitado de compartilhamentos e um compartilhamento pode armazenar um número ilimitado de arquivos, até a capacidade total de 5 TB do armazenamento de arquivos.
* **Diretório:** uma hierarquia opcional de diretórios. 
* **Arquivo:** um arquivo no compartilhamento. Um arquivo pode ter até 1 TB de tamanho.
* **Formato de URL:** arquivos são endereçáveis usando o seguinte formato de URL:   
  https://`<storage
  account>`.file.core.windows.net/`<share>`/`<directory/directories>`/`<file>`  
  
  A URL de exemplo a seguir poderia ser usada para endereçar um dos arquivos no diagrama acima:   
  `http://samples.file.core.windows.net/logs/CustomLogs/Log1.txt`

Para obter detalhes sobre como nomear compartilhamentos, diretórios e arquivos, consulte [Nomeando e fazendo referência a compartilhamentos, diretórios, arquivos e metadados](http://msdn.microsoft.com/library/azure/dn167011.aspx).
