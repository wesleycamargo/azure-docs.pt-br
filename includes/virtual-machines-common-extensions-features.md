



Para obter detalhes sobre os Agentes de VM e como eles funcionam para dar suporte a Extensões de VM, veja [Visão geral de agente de VM e extensões de VM](../articles/virtual-machines/virtual-machines-windows-classic-manage-extensions.md).

## Extensões de VM do Azure
As extensões de VM implementam a maioria dos recursos essenciais que você deseja usar com suas VMs, incluindo funcionalidades básicas como a redefinição de senhas, configuração do RDP e muitos outros. Como novas extensões são adicionadas o tempo todo, o número de recursos possíveis para oferecer suporte às suas VMs no Azure continua a aumentar. Por padrão, várias extensões básicas de VM são instaladas quando você cria sua VM na Galeria de Imagens, incluindo **IaaSDiagnostics** (atualmente apenas nas VMs do Windows), **VMAccess** e **BGInfo** (também atualmente apenas no Windows). No entanto, nem todas as extensões são implementadas em Windows e Linux em qualquer momento específico, devido ao fluxo constante de novas extensões e atualizações de recursos.

## Conectividade e gerenciamento básico
As seguintes extensões são fundamentais para habilitar, habilitar novamente ou desabilitar a conectividade básica com as suas VMs quando elas são criadas e estão em execução.

| Nome da extensão da VM | Descrição do recurso | Mais informações |
| --- | --- | --- |
| VMAccessAgent (Windows) |Crie, atualize e redefina as informações de usuário e configurações de conexão RDP. |[Windows](../articles/virtual-machines/virtual-machines-windows-classic-extensions-customscript.md) |
| VMAccessForLinux (Linux) |Crie, atualize e redefina as informações de usuário e configurações de conexão SSH. |[Linux](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |

## Implantação e gerenciamento de configuração
As seguintes extensões oferecem suporte a diferentes tipos de recursos e cenários de gerenciamento de configuração e implantação. Consulte também a seção sobre Operações de VM e gerenciamento, abaixo.

| Nome da extensão da VM | Descrição do recurso | Mais informações |
| --- | --- | --- |
| **MSEnterpriseApplication** |Implementa recursos de suporte pelo Windows System Center. |[Funções de máquinas virtuais do System Center 2012 R2](http://social.technet.microsoft.com/wiki/contents/articles/18274.system-center-2012-r2-virtual-machine-role-authoring-guide-resource-extension-package.aspx) |
| **Implantar Octopus** (baseado na Extensão DSC) |Dá suporte à implantação automatizada de aplicativos da web ASP.NET e serviços do Windows em ambientes de desenvolvimento, teste e produção. |[Introdução à implantação do Octopus](http://docs.octopusdeploy.com/display/OD/Getting%20started) |
| **Gerenciador de Versão do Visual Studio** (baseado na Extensão DSC) |Dá suporte à implantação contínua com o Visual Studio. |[Automatizar implantações com o Release Management](https://msdn.microsoft.com/Library/vs/alm/Release/overview) |
| **CentosChefClient** | | |
| **ChefClient** |Cria um cliente chefe no Windows. (Também pode usar a extensão DSC abaixo.) |[Chef e Microsoft Azure](https://www.getchef.com/solutions/azure/) |
| **LinuxChefClient** | | |
| **DockerExtension** |Instala o daemon Docker para oferecer suporte a comandos Docker remotos. |[Como usar a Extensão de Máquina Virtual do Docker](../articles/virtual-machines/virtual-machines-linux-dockerextension.md)Para obter mais informações, veja o [Guia do usuário da Extensão de VM do Docker](https://github.com/Azure/azure-docker-extension/blob/master/README.md) |
| **DSC** |Extensão do PowerShell DSC (configuração de estado desejado). |[Extensão do Azure PowerShell DSC (configuração de estado desejado)](http://blogs.msdn.com/b/powershell/archive/2014/08/07/introducing-the-azure-powershell-dsc-desired-state-configuration-extension.aspx) |
| **PuppetEnterpriseAgent** |Implementa os recursos do Puppet Enterprise. |[Puppet no Azure](http://puppetlabs.com/solutions/microsoft) |
| **CustomScriptExtension** (Windows)**CustomScriptForLinux** (Linux) |Chama scripts personalizados na VM a qualquer momento: inicialização ou durante o tempo de vida. |[Extensão de script personalizado](../articles/virtual-machines/virtual-machines-windows-classic-extensions-customscript.md) |
| **AzureCATExtensionHandler** |Consome os dados de diagnóstico coletados pelo **IaaSDiagnostics** e algumas outras fontes de dados como as [Métricas de Análise do Armazenamento do Azure](https://msdn.microsoft.com/library/azure/hh343270.aspx) e os transforma em um conjunto de dados agregados apropriado para o consumo do processo de controle de Host do SAP |[Monitoramento aprimorado do Azure para SAP](https://azure.microsoft.com/blog/2014/06/04/azure-enhanced-monitoring-for-sap/) |

## Segurança e proteção
As extensões nesta seção fornecem recursos de segurança críticos para as VMs do Azure.

| Nome da extensão da VM | Descrição do recurso | Mais informações |
| --- | --- | --- |
| **CloudLinkSecureVMWindowsAgent** |Fornece aos clientes do Microsoft Azure a capacidade de criptografar seus dados da máquina virtual em uma infraestrutura compartilhada multilocatária e totalmente no controle das chaves de criptografia para seus dados criptografados em uma infraestrutura de armazenamento do Azure. |[Protegendo Máquinas Virtuais do Microsoft Azure utilizando a criptografia BitLocker e o sistema operacional nativo](http://www.cloudlinktech.com/azure) |
| **McAfeeEndpointSecurity** |Protege sua VM de software mal-intencionado. |[McAfee](https://www.mcafeeasap.com/MarketingContent/default.aspx) |
| **TrendMicroDSA** |Habilita o suporte de plataforma de Deep Security da TrendMicro para fornecer detecção de intrusão e prevenção, firewall, antimalware, reputação de web, inspeção de log e monitoramento de integridade. |[Como instalar e configurar o Trend Micro Deep Security as a Service em uma VM do Azure](../articles/virtual-machines/virtual-machines-windows-classic-install-trend.md) |
| **PortalProtectExtension** |Protege contra ameaças ao seu ambiente do Microsoft SharePoint. |[Protegendo sua implantação do SharePoint no Azure](http://blog.trendmicro.com/securing-sharepoint-deployment-azure/) |
| **IaaSAntimalware** |O antimalware da Microsoft para serviços de nuvem do Azure e máquinas virtuais é uma funcionalidade de proteção em tempo real que ajuda a identificar e remover vírus, spyware e outros softwares mal-intencionados, com alertas configuráveis quando se sabe que software mal-intencionado ou indesejado tenta se instalar ou executar no seu sistema. |[Antimalware para Serviços de Nuvem do Azure e máquinas virtuais](../articles/security/azure-security-antimalware.md) |
| **SymantecEndpointProtection** |O Symantec Endpoint Protection 12.1.4 permite a segurança e o desempenho em sistemas físicos e virtuais. |[Como instalar e configurar o Symantec Endpoint Protection em uma VM do Azure](../articles/virtual-machines/virtual-machines-windows-classic-install-symantec.md) |

## Gerenciamento e operações de VM
Dá suporte a comportamento e a recursos de gerenciamento de operações comuns. Consulte também a seção sobre implantação e gerenciamento de configuração acima.

| **Nome da extensão da VM** | Descrição do recurso | Mais informações |
| --- | --- | --- |
| **AzureVmLogCollector** |É possível usar a Extensão **AzureVMLogCollector** sob demanda para executar uma coleção avulsa de logs de uma ou mais VMs de Serviço de Nuvem (de funções Web e funções de trabalho) e transferir os arquivos coletados para uma conta de armazenamento do Azure – tudo sem precisar fazer logon remotamente em uma das VMs. |[Extensão AzureLogCollector](../articles/virtual-machines/virtual-machines-windows-log-collector-extension.md) |
| **IaaSDiagnostics** |Habilita, desabilita e configura o Diagnóstico do Azure e também é usado pelo **AzureCATExtensionHandler** para dar suporte ao monitoramento do SAP. |[Monitoramento de máquina virtual do Microsoft Azure com extensão de diagnóstico do Azure](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| **OSPatchingForLinux** |Habilita os administradores de VM do Azure a automatizar as atualizações de sistema operacional de VM com as configurações personalizadas. Você pode usar a Extensão OSPatching para configurar atualizações do sistema operacional para as máquinas virtuais, incluindo: especificar com que frequência e quando instalar patches do sistema operacional, especificar quais patches instalar e configurar o comportamento de reinicialização após atualizações |[Postagem no blog da extensão de patches do sistema operacional](https://azure.microsoft.com/blog/2014/10/23/automate-linux-vm-os-updates-using-ospatching-extension/). Veja também o arquivo Leiame e o código-fonte no GitHub em [Extensão de Aplicação de Patch do SO](https://github.com/Azure/azure-linux-extensions). |

## Desenvolver e depurar
Essas Extensões de VM são listadas aqui para integridade, pois dão suporte para recursos relacionados ao Visual Studio e não se destinam a ser usadas diretamente.

| Nome da extensão da VM | Descrição do recurso | Mais informações |
| --- | --- | --- |
| **VS14CTPDebugger** |Dá suporte à depuração remota do VS usando o SDK 2.4 do Azure |[Depuração remota no Visual Studio](https://msdn.microsoft.com/library/y7f5zaaa.aspx) |
| **VS2013Debugger** |Dá suporte à depuração remota do VS usando o SDK 2.4 do Azure | |
| **VS2012Debugger** |Dá suporte à depuração remota do VS usando o SDK 2.4 do Azure | |
| **RemoteDebugVS14CTP** |Dá suporte à depuração remota do VS usando o SDK 2.3 do Azure | |
| **RemoteDebugVS2013** |Dá suporte à depuração remota do VS usando o SDK 2.3 do Azure | |
| **RemoteDebugVS2012** |Dá suporte à depuração remota do VS usando o SDK 2.3 do Azure | |
| **WebDeployForVSDevTest** |Instala e configura o IIS e a implantação da Web no Windows Server. Não há suporte para removê-lo ou desabilitá-lo. | |

## Recursos diversos
Essas extensões dão suporte para outros recursos VM que podem ser úteis.

| Nome da extensão da VM | Descrição do recurso | Mais informações |
| --- | --- | --- |
| **BGInfo** |Apresenta uma visão consolidada útil das informações do servidor na área de trabalho ao usar o RDP. |[Extensão BGInfo](https://msdn.microsoft.com/library/mt589195.aspx) |
| **HpcVmDrivers** |Instala, configura e mantém os drivers de dispositivo de rede de acesso remoto direto à memória (RDMA) em uma VM de tamanho A8 ou A9 que executa o Windows Server 2012 R2 ou o Windows Server 2012. Habilita VMs A8 ou A9 clusterizadas para usar a rede RDMA ao executar aplicativos MPI paralelos. |[Sobre as instâncias de computação intensiva A8, A9, A10 e A11](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md) |

<!---HONumber=AcomDC_0608_2016-->