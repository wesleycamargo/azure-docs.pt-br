## <a name="overview"></a>Visão geral
O armazenamento de Arquivos do Azure é um serviço que oferece compartilhamentos de arquivos na nuvem usando o [Protocolo SMB](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx)padrão. Há suporte ao SMB 2.1 e ao 3.0 SMB. Com o armazenamento de Arquivos do Azure, você pode migrar aplicativos herdados que dependem de compartilhamentos de arquivos para o Azure rapidamente e sem regravações caras. Os aplicativos executados em máquinas virtuais do Azure ou serviços de nuvem ou em clientes locais podem montar um compartilhamento de arquivos na nuvem, exatamente como um aplicativo de desktop monta um compartilhamento SMB típico. Qualquer quantidade de componentes de aplicativos pode montar e acessar o compartilhamento de armazenamento de arquivos simultaneamente.

Já que um compartilhamento do Armazenamento de arquivos é um compartilhamento de arquivos SMB padrão, os aplicativos executados no Azure podem acessar dados no compartilhamento por meio de APIs de E/S do sistema de arquivos. Os desenvolvedores podem, portanto, utilizar seus códigos e habilidades existentes para migrar aplicativos existentes. Profissionais de TI podem usar cmdlets do PowerShell para criar, montar e gerenciar compartilhamentos de armazenamento de Arquivo como parte da administração de aplicativos Azure.

Você pode criar compartilhamentos de arquivos do Azure usando o [Portal do Azure](https://portal.azure.com), os cmdlets do PowerShell do Armazenamento do Azure, as bibliotecas de cliente do Armazenamento do Azure ou a API REST do Armazenamento do Azure. Além disso, como esses compartilhamentos de arquivos são compartilhamentos do SMB, você pode acessá-los por meio de APIs padrão e familiares do sistema de arquivos.



<!--HONumber=Nov16_HO2-->


