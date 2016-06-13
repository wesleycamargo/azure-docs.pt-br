<properties
   pageTitle="Visão geral da segurança do Armazenamento do Azure | Microsoft Azure"
   description="O Armazenamento do Azure é a solução de armazenamento em nuvem para aplicativos modernos que dependem de durabilidade, disponibilidade e escalabilidade para atender às necessidades dos clientes. Este artigo fornece uma visão geral dos recursos de segurança centrais do Azure que podem ser usados com o Armazenamento do Azure."
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/26/2016"
   ms.author="terrylan"/>

# Visão geral da segurança do Armazenamento do Azure

O Armazenamento do Azure é a solução de armazenamento em nuvem para aplicativos modernos que dependem de durabilidade, disponibilidade e escalabilidade para atender às necessidades dos clientes. O Armazenamento do Azure fornece um conjunto abrangente de funcionalidades de segurança:

- A conta de armazenamento pode ser protegida usando o Controle de Acesso Baseado em Função e o Azure Active Directory.
- Os dados podem ser protegidos em trânsito, entre um aplicativo e o Azure usando a Criptografia do cliente, HTTPs ou SMB 3.0.
- Os dados podem ser definidos para serem criptografados automaticamente quando gravados no Armazenamento do Azure usando a Criptografia do Serviço de Armazenamento.
- Os discos do SO e de dados usados pelas máquinas virtuais podem ser definidos para serem criptografados usando o Azure Disk Encryption.
- O acesso delegado aos objetos de dados no Armazenamento do Azure pode ser concedido usando Assinaturas de Acesso Compartilhado.

Este artigo fornecerá uma visão geral de cada um desses recursos de segurança centrais que podem ser usados com o Armazenamento do Azure. São fornecidos links para artigos que apresentarão detalhes de cada recurso para que você possa saber mais sobre eles.

Para obter uma visão mais detalhada sobre a segurança no Armazenamento do Azure, consulte o [Guia de segurança do Armazenamento do Azure](../storage/storage-security-guide.md).

Aqui estão os principais recursos que serão abordados neste artigo:

- Controle de Acesso Baseado em Função
- Gerenciamento de as chaves da conta de armazenamento
- Acesso delegado aos objetos de armazenamento
- Criptografia em trânsito
- Criptografia em repouso/criptografia do serviço de armazenamento
- Análise de Armazenamento

## RBAC (Controle de Acesso Baseado em Função)

Você pode proteger a conta de armazenamento com o RBAC (Controle de Acesso Baseado em Função). Cada assinatura do Azure está associada a um locatário do Azure AD (Active Directory). Usuários, grupos e aplicativos do diretório podem gerenciar recursos na assinatura do Azure. Esses direitos de acesso são concedidos atribuindo a função de RBAC apropriada a usuários, grupos e aplicativos em determinado escopo.

Você pode usar o RBAC para conceder acesso às operações de gerenciamento da sua conta de armazenamento do Azure. Usando o RBAC, você controla quem pode:

- Gerenciar a conta de armazenamento.
- Ler as chaves da conta de armazenamento e usá-las para acessar os blobs, filas, tabelas e arquivos.
- Regenerar chaves de armazenamento.

Saiba mais:

- [Controle de acesso baseado em função do Active Directory do Azure](../active-directory/role-based-access-control-configure.md)

## Gerenciamento de as chaves da conta de armazenamento

Quando você cria uma conta de armazenamento, o Azure gera duas chaves de acesso de armazenamento de 512 bits, que são usadas para autenticação quando a conta de armazenamento é acessada. Ao fornecer as duas chaves de acesso de armazenamento, o Azure permite que você regenere as chaves sem interrupção para o serviço de armazenamento ou o acesso a esse serviço. Você pode manter conexões com a conta de armazenamento usando uma chave de acesso enquanto regenera a outra.

As chaves da conta de armazenamento, com o nome da conta de armazenamento, podem ser usadas para acessar os objetos de dados armazenados na conta de armazenamento, por exemplo, blobs, entidades em uma tabela, mensagens na fila e arquivos em um compartilhamento de arquivos do Azure. Usando o RBAC, você controla o acesso às chaves da conta de armazenamento que controla o acesso aos objetos de dados em si.

Saiba mais:

- [Sobre as contas de armazenamento do Azure](../storage/storage-create-storage-account.md)

## Acesso delegado aos objetos de armazenamento

Uma SAS (Assinatura de Acesso Compartilhado) é uma cadeia de caracteres que contém um token de segurança que pode ser anexado a um URI que permite delegar acesso aos objetos de armazenamento e especificar restrições, como as permissões e o intervalo de data/hora de acesso.

Fornecer a chave da conta de armazenamento é como compartilhar as chaves do seu reino de armazenamento. Isto é, ela concede acesso a tudo. Com uma SAS, você pode dar a um cliente apenas as permissões necessárias por um período limitado. Você pode:

- Conceder acesso a blobs, contêineres, mensagens de fila, arquivos e tabelas. Com tabelas, na verdade, você pode conceder permissão para acessar um intervalo de entidades na tabela especificando os intervalos de chaves de linha e partição ao quais você quer que o usuário tenha acesso.
- Especificar que as solicitações feitas usando uma SAS sejam restritas a um determinado endereço IP ou intervalo de endereços IP fora do Azure.
- Exigir que as solicitações sejam feitas usando um protocolo específico (HTTPS ou HTTP/HTTPS). Isso significa que se você deseja permitir apenas o tráfego HTTPS, será possível definir o protocolo necessário para apenas HTTPS, e o tráfego HTTP será bloqueado.

Saiba mais:

- [Noções básicas sobre o modelo SAS](../storage/storage-dotnet-shared-access-signature-part-1.md)

## Criptografia em trânsito
A criptografia em trânsito é um mecanismo de proteção de dados quando eles são transmitidos entre redes. Com o Armazenamento do Azure, você pode proteger dados usando:

- Criptografia de nível de transporte, como HTTPS ao transferir dados dentro ou fora do Armazenamento do Azure.
- Criptografia na transmissão, como a criptografia SMB 3.0 para compartilhamentos de arquivos do Azure.
- Criptografia do cliente, que permite criptografar os dados antes que eles sejam transferidos para o armazenamento e descriptografá-los após serem transferidos para fora do armazenamento.

### Criptografia no nível de transporte

Para garantir a segurança dos dados do Armazenamento do Azure, você pode criptografá-los entre o cliente e o Armazenamento do Azure. Você pode [habilitar o HTTPS](../app-service-web/web-sites-configure-ssl-certificate.md) ao chamar as APIs REST ou ao acessar objetos no armazenamento. E também SAS ([Assinaturas de Acesso Compartilhado](../storage/storage-dotnet-shared-access-signature-part-1.md)), que podem ser usadas para delegar acesso a objetos do Armazenamento do Azure. Isso inclui um parâmetro opcional, "protocolo", que você pode usar para especificar que apenas o protocolo HTTPS pode ser usado ao usar SAS. Isso garante que qualquer pessoa enviando links com tokens de SAS usarão o protocolo apropriado.

### Criptografia na transmissão para o acesso ao compartilhamento de arquivos

O SMB 3.0 dá suporte à criptografia e pode ser usado com o Windows Server 2012 R2, o Windows 8, o Windows 8.1 e o Windows 10, permitindo o acesso entre regiões e até mesmo o acesso na área de trabalho.

Os Compartilhamentos de Arquivos do Azure podem ser usados com máquinas virtuais Linux. O cliente SMB Linux ainda não dá suporte à criptografia, de modo que o acesso é permitido apenas dentro da uma região do Azure. O suporte à criptografia para Linux está previsto. Quando eles adicionarem criptografia, você terá a mesma criptografia de nível de rede para o acesso aos Compartilhamentos de Arquivos do Azure no Linux que tem no Windows.

Saiba mais:

- [Como utilizar o armazenamento de arquivos do Azure com Linux](../storage/storage-how-to-use-files-linux.md)
- [Introdução ao Armazenamento de Arquivos do Azure no Windows](../storage/storage-dotnet-how-to-use-files.md)
- [Por dentro do Armazenamento de arquivos do Azure](https://azure.microsoft.com/blog/inside-azure-file-storage/)

### Criptografia do cliente

A criptografia do cliente ajuda a garantir que seus dados estejam seguros enquanto são transferidos entre um aplicativo cliente e o Armazenamento do Azure. Os dados são criptografados antes de serem transferidos para o Armazenamento do Azure. Na recuperação dos dados do Armazenamento do Azure, os dados são descriptografados depois que são recebidos no cliente. Mesmo que os dados estejam criptografados durante a transferência, você também pode usar o HTTPS para ajudar a reduzir os erros de rede que afetam a integridade dos dados.

Saiba mais:

- [Client-Side Encryption for Microsoft Azure Storage](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/) (Criptografia do cliente para o Armazenamento do Microsoft Azure)
- [Cloud security controls series: Encrypting Data in Transit](http://blogs.microsoft.com/cybertrust/2015/08/10/cloud-security-controls-series-encrypting-data-in-transit/) (Série sobre controles de segurança de nuvem: criptografando dados em trânsito)

## Criptografia em repouso

Há três recursos do Azure que fornecem criptografia de dados que estão “em repouso”:

- Criptografia do Serviço de Armazenamento
- Criptografia do cliente
- Criptografia de Disco do Azure

### Criptografia do Serviço de Armazenamento

A Criptografia do Serviço de Armazenamento permite solicitar que o serviço de armazenamento criptografe automaticamente os dados ao gravá-los no Armazenamento do Azure. Quando você lê os dados no Armazenamento do Azure, eles são descriptografados pelo serviço de armazenamento antes de serem retornados. Isso permite proteger os dados sem precisar modificar código nem adicionar código a nenhum aplicativo.

A [Criptografia do Serviço de Armazenamento do Azure](https://azure.microsoft.com/services/storage/) está disponível para [Armazenamento de Blobs do Azure](https://azure.microsoft.com/services/storage/blobs/). Para obter detalhes sobre outros tipos de armazenamento do Azure, consulte [Arquivo](https://azure.microsoft.com/services/storage/files/), [Disco (Armazenamento Premium)](https://azure.microsoft.com/services/storage/premium-storage/), [Tabela](https://azure.microsoft.com/services/storage/tables/) e [Fila](https://azure.microsoft.com/services/storage/queues/).

Saiba mais:

- [Criptografia do Serviço de Armazenamento do Azure para dados em repouso](../storage/storage-service-encryption.md)

### Criptografia do cliente

Mencionamos a criptografia do cliente ao abordar a criptografia em trânsito. Esse recurso permite criptografar os dados de modo programático em um aplicativo cliente antes de enviá-los para serem gravados no Armazenamento do Azure, assim como descriptografá-los de modo programático depois de recuperá-los no Armazenamento do Azure.

Ele fornece a criptografia em trânsito, mas também fornece o recurso de criptografia em repouso. Observe que, embora os dados sejam criptografados em trânsito, você pode usar HTTPS para aproveitar as verificações de integridade de dados internas, que ajudam a reduzir os erros de rede que afetam a integridade dos dados.

Saiba mais:

- [Criptografia do lado do cliente e o Cofre da Chave do Azure para o Armazenamento do Microsoft Azure](../storage/storage-client-side-encryption.md)

### Criptografia de Disco do Azure

O Azure Disk Encryption para VMs (Máquinas Virtuais) ajuda você a atender aos requisitos de conformidade e segurança organizacionais criptografando seus discos de VM (incluindo discos de dados e de inicialização) com chaves e políticas controladas no [Cofre de Chaves do Azure](https://azure.microsoft.com/services/key-vault/).

A criptografia de disco para VMs funciona para sistemas operacionais Windows e Linux. Ela também usa o Cofre de Chaves para ajudá-lo a proteger, gerenciar e auditar o uso de suas chaves de criptografia de disco. Todos os dados nos discos de VM são criptografados em repouso usando a tecnologia de criptografia padrão do setor em suas contas de Armazenamento do Azure. A solução de Criptografia de Disco para Windows é baseada na [Criptografia de Unidade de Disco BitLocker da Microsoft](https://technet.microsoft.com/library/cc732774.aspx) e a solução para Linux é baseada no [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

Saiba mais:

- [Azure Disk Encryption for Windows and Linux IaaS Virtual Machines (Azure Disk Encryption para máquinas virtuais IaaS Windows e Linux)](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## Análise de Armazenamento

Para cada conta de armazenamento, você pode habilitar a Análise de Armazenamento do Azure para realizar o registro em log e armazenar dados de métricas. Essa é uma excelente ferramenta para usar quando quiser verificar as métricas de desempenho ou solucionar problemas de desempenho de uma conta de armazenamento.

Outra parte dos dados que você pode ver nos logs da análise de armazenamento é o método de autenticação usado por uma pessoa quando ela acessa o armazenamento. Por exemplo, no Armazenamento de Blobs, você pode ver se a pessoa usou uma Assinatura de Acesso Compartilhado ou as chaves da conta de armazenamento, ou se o blob acessado era público.

Saiba mais:

- [Análise de Armazenamento](../storage/storage-analytics.md)
- [Formato de Log de análise de armazenamento](https://msdn.microsoft.com/library/azure/hh343259.aspx)
- [Monitorar uma conta de armazenamento no Portal do Azure](../storage/storage-monitor-storage-account.md)
- [Solução de problemas ponta a ponta usando Métricas de Armazenamento do Azure e Registro em Log, AzCopy e Analisador de Mensagem](../storage/storage-e2e-troubleshooting.md)

<!---HONumber=AcomDC_0601_2016-->