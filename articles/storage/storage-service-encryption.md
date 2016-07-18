<properties
	pageTitle="Criptografia do Serviço de Armazenamento do Azure para dados em repouso (Visualização)| Microsoft Azure"
	description="Use o recurso de Criptografia do Serviço de Armazenamento do Azure para criptografar seu Armazenamento de Blobs do Azure no lado do serviço ao armazenar os dados, e descriptografá-lo ao recuperar os dados."
	services="storage"
	documentationCenter=".net"
	authors="robinsh"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/01/2016"
	ms.author="robinsh"/>

# Criptografia do Serviço de Armazenamento do Azure para dados em repouso (Visualização)

A SSE (Criptografia do Serviço de Armazenamento) do Azure para dados em repouso ajuda a proteger seus dados a fim de atender aos compromissos de conformidade e segurança da organização. Com esse recurso, o Armazenamento do Azure criptografa automaticamente seus dados antes de persistir no armazenamento e os descriptografa antes da recuperação. A criptografia, descriptografia e o gerenciamento de chaves é totalmente transparente para os usuários.

As seções a seguir fornecem orientações detalhadas sobre como usar os recursos de Criptografia do Serviço de Armazenamento, bem como os cenários com suporte e experiências do usuário.

## Visão geral


O Armazenamento do Azure fornece um conjunto abrangente de recursos de segurança que, juntos, permitem aos desenvolvedores criar aplicativos seguros. Os dados podem ser protegidos em trânsito, entre um aplicativo e o Azure, usando a [Criptografia do lado do cliente](storage-client-side-encryption.md), HTTPs ou SMB 3.0. A Criptografia do Serviço de Armazenamento é um novo recurso do Armazenamento do Azure que criptografará os dados quando eles forem gravados em Armazenamento do Azure, e possui suporte para blobs de bloco, blobs de página e blobs de acréscimo. Esse recurso pode ser habilitado para novas contas de armazenamento usando o modelo de implantação do Azure Resource Manager, e está disponível para todos os níveis de redundância (LRS, ZRS, GRS e RA-GRS). A Criptografia do Serviço de Armazenamento está disponível para o Armazenamento Standard e Premium, lidando com a criptografia, descriptografia e gerenciamento de chaves de uma forma totalmente transparente. Todos os dados são criptografados usando a [criptografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) de 256 bits, um dos codificadores de blocos mais potentes. A seção Visualização a seguir traz detalhes sobre como é possível entrar no programa de visualização da Criptografia do Serviço de Armazenamento.

Esta captura de tela mostra onde encontrar a configuração da Criptografia do Serviço de Armazenamento usando o [Portal do Azure](https://azure.portal.com). Nessa tela, clique em Criptografia para continuar.

![Captura de tela do Portal mostrando a opção Criptografia](./media/storage-service-encryption/image1.png)

Depois de clicar na configuração da Criptografia, habilite ou desabilite a Criptografia do Serviço de Armazenamento.

![Captura de tela do Portal mostrando as propriedades da Criptografia](./media/storage-service-encryption/image2.png)

##Disponibilidade

Para o Armazenamento Padrão, esse recurso está disponível atualmente em EUA Central, Ásia Oriental, Leste dos EUA 2, Europa Ocidental e Oeste dos EUA.

Para o Armazenamento Premium, esse recurso está disponível atualmente em EUA Central, Leste dos EUA 2, Leste do Japão e Oeste dos EUA.

Atualizaremos este documento à medida que lançarmos esse recurso em outras regiões.

##Cenários de criptografia

A Criptografia do Serviço de Armazenamento pode ser habilitada no nível da conta de armazenamento. Ela oferece suporte aos seguintes cenários de cliente:

-   Criptografia de blobs de bloco, blobs de acréscimo e blobs de páginas.

-   Criptografia de VHDs arquivados e modelos colocados no Azure a partir do local.

-   Criptografia de sistema operacional subjacente e discos de dados para VMs de IaaS criadas com seus VHDs.

A visualização pública tem as seguintes limitações:

-   Não há suporte para a criptografia de contas de armazenamento clássicas.

-   Não há suporte para a criptografia de contas de armazenamento clássicas migradas para as contas de armazenamento do Resource Manager.

-   Dados existentes - A SSE criptografa apenas os dados recém-criados após a habilitação da criptografia. Se, por exemplo, você criar uma nova conta de armazenamento do Resource Manager, mas não ativar a criptografia e, na sequência, carregar blobs ou VHDs arquivados nessa conta de armazenamento e então ativar a SSE, esses blobs não serão criptografados, a menos que sejam reconfigurados ou copiados.

-   Suporte do Marketplace - Habilite a criptografia de VMs criadas no Marketplace usando o (Portal do Azure)(https://portal.azure.com), o PowerShell e a CLI do Azure. Imagem base de VHD permanecerá não criptografada; no entanto, quaisquer gravações feitas após a rotação da VM serão criptografadas.

-   Dados de tabela, filas arquivos não serão criptografados.

##Visualização

Esse recurso tem suporte apenas para contas de armazenamento do Resource Manager recém-criadas; não há suporte para contas de armazenamento clássicas. Para usar esse recurso novo, você deve registrar sua assinatura usando os cmdlets do PowerShell. Após a aprovação de sua assinatura, você poderá habilitar o SSE para sua conta de armazenamento na assinatura aprovada. Assim como acontece com a maioria das versões de visualização, isso não deve ser usado para cargas de trabalho de produção até que o recurso seja disponibilizado para todos. Você pode entrar para o nosso grupo de Visualização da Criptografia do Serviço de Armazenamento no Yammer para fornecer comentários sobre sua experiência.

### Como registrar para Visualização

-   [Instale os cmdlets do Azure PowerShell](../powershell-install-configure.md).

-   No Windows 10, abra o PowerShell como Administrador.

-   Registre com o namespace do Provedor de Recursos de Armazenamento. Isso é necessário apenas para uma assinatura não esteja registrada com o SRP.

    `PS E:> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Storage" `

-   Para se registrar para o recurso, você pode usar o cmdlet do PowerShell Register-AzureRmProviderFeature.

    `Register-AzureRmProviderFeature -FeatureName "EncryptionAtRest" -ProviderNamespace "Microsoft.Storage"`

-   Para consultar o estado de seu registro a fim de ver se a assinatura foi aprovada, use o cmdlet Get-AzureRmProviderFeature do PowerShell.

    `Get-AzureRmProviderFeature -FeatureName "EncryptionAtRest" -ProviderNamespace "Microsoft.Storage"`

Quando o status do registro retornar como "Registrado", sua assinatura terá sido aprovada. Visite também nosso grupo de Visualização da Criptografia do Serviço de Armazenamento do Azure no Yammer.

##Introdução

###Etapa 1: [Inscrever-se para obter a Preview](#registering-for-preview).

###Etapa 2: [Criar uma nova conta de armazenamento](storage-create-storage-account.md).

###Etapa 3: Habilitar a criptografia.

Você pode habilitar a criptografia usando o [Portal do Azure](https://portal.azure.com).

> [AZURE.NOTE] Se você quiser habilitar ou desabilitar programaticamente a Criptografia do Serviço de Armazenamento em uma conta de armazenamento, use a [API REST do provedor de recursos de armazenamento do Azure](https://msdn.microsoft.com/library/azure/mt163683.aspx). Vamos adicionar essa capacidade à [Biblioteca de cliente do provedor de recursos de armazenamento para .NET](https://msdn.microsoft.com/library/azure/mt131037.aspx), ao Azure PowerShell e à CLI do Azure em breve.

###Etapa 4: Copiar dados para a conta de armazenamento

#### Como usar o AzCopy

O AzCopy é um utilitário de linha de comando do Windows projetado para copiar dados entre o Armazenamento de Blobs, de Arquivos e de Tabelas do Microsoft Azure usando comandos simples com o desempenho ideal. Você pode usar esse utilitário para copiar dados de uma conta de armazenamento de blobs existente para sua nova conta de armazenamento com o recurso de criptografia habilitado.

Para saber mais, visite [Transferir dados com o utilitário de linha de comando AzCopy](storage-use-azcopy.md).

#### Como usar as Bibliotecas de Cliente de Armazenamento

Você pode carregar e baixar dados no armazenamento de blobs usando nosso conjunto avançado de Bibliotecas de Cliente de Armazenamento, incluindo .NET, C++, Java, Android, Node.js, PHP, Python e Ruby.

Para saber mais, confira [Introdução ao armazenamento de blobs do Azure usando o .NET](storage-dotnet-how-to-use-blobs.md).

#### Como usar o Gerenciador de Armazenamento

Um Gerenciador de armazenamento pode ser usado para criar contas de armazenamento, carregar e baixar dados, exibir o conteúdo de blobs e navegar pelos diretórios. Muitos dão suporte tanto às contas de armazenamento clássicas quanto às do Resource Manager.

Você pode usar uma dessas opções para carregar blobs em sua conta de armazenamento com a criptografia habilitada. Com alguns gerenciadores de armazenamento, também é possível copiar dados de sua conta de armazenamento existente para sua nova conta de armazenamento com o SSE habilitado.

Para saber mais, visite [Pesquisadores de armazenamento do Azure](storage-explorers.md).

###Etapa 5: Consultar o status dos dados criptografados

Assim que o SSE estiver amplamente disponível, uma versão atualizada das Bibliotecas de Cliente de Armazenamento será implantada permitindo que você consulte o estado de um objeto para determinar se ele está criptografado ou não.

Enquanto isso, você pode chamar [Obter propriedades da conta](https://msdn.microsoft.com/library/azure/mt163553.aspx) para verificar se a conta de armazenamento tem a criptografia habilitada, ou para exibir as propriedades da conta de armazenamento no Portal do Azure.

##Fluxo de trabalho da criptografia e da descriptografia

Aqui está uma breve descrição de como funciona o fluxo de trabalho de criptografia/descriptografia:

-   O cliente habilita a criptografia na conta de armazenamento.

-   Quando o cliente grava novos dados (PUT Blob, PUT Block, PUT Page etc.) no armazenamento de blobs, todas as gravações são criptografadas usando a [criptografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) de 256 bits, uma das codificações de bloco mais fortes disponíveis.

-   Quando o cliente precisa acessar os dados (GET Blob etc.), esses dados são descriptografados automaticamente antes de retornar para o usuário.

-   Se a criptografia estiver desabilitada, as novas gravações não serão criptografadas, e os dados criptografados existentes permanecerão criptografados até que sejam regravados pelo usuário. Enquanto a criptografia estiver habilitada, as gravações no Armazenamento de Blobs serão criptografadas. O estado dos dados não muda com a habilitação/desabilitação da criptografia na conta de armazenamento.

-   Todas as chaves de criptografia são armazenadas, criptografadas e gerenciadas pela Microsoft.

##Perguntas frequentes sobre a Criptografia do Serviço de Armazenamento de dados em repouso

**P: Tenho uma conta de armazenamento clássica. Posso habilitar o SSE nela?**

R: Não, apenas contas de armazenamento do Resource Manager criadas recentemente em preview dão suporte ao SSE.

**P: Como posso criptografar os dados em minha conta de armazenamento clássica?**

R: Você pode criar uma nova conta de armazenamento do Resource Manager e copiar seus dados usando o [AzCopy](storage-use-azcopy.md) da conta de armazenamento clássica existente para sua nova conta de armazenamento do Resource Manager.

**P: posso ter uma conta de armazenamento do Resource Manager existente. Posso habilitar o SSE nela?**

R: Durante a versão de visualização do SSE, é necessário criar uma nova conta para acessar o novo recurso do SSE.

**P: Eu gostaria de criptografar os dados atuais em uma conta de armazenamento do Resource Manager existente?**

R: Se a sua conta de armazenamento do Resource Manager existente tiver sido criada antes deste comunicado de Preview, você poderá criar uma nova conta de armazenamento do Resource Manager e habilitar a criptografia. Em seguida, você poderá copiar os dados da conta de armazenamento anterior e eles serão criptografados automaticamente. No entanto, se sua conta de armazenamento do Resource Manager tiver sido criada após o comunicado de Preview e você decidir habilitar a criptografia posteriormente, isso poderá ser feito usando o Portal do Azure, e você poderá regravar seus dados não criptografados de volta para a conta de armazenamento.

**P: Estou usando o armazenamento Premium. Posso usar o SSE?**

R: Sim, o SSE recebe suporte no Armazenamento Standard e no Armazenamento Premium.

**P: Se eu criar uma nova conta de armazenamento, habilitar o SSE e depois criar uma nova VM usando a conta de armazenamento, isso significa que minha VM está criptografada?**

R: Sim. Todos os discos criados que usam a nova conta de armazenamento serão criptografados, desde que eles sejam criados após a habilitação do SSE. Se a VM tiver sido criada usando o Azure Marketplace, a imagem base do VHD permanecerá não criptografada; no entanto, quaisquer gravações feitas após a rotação da VM serão criptografadas.

**P: Posso criar novas contas de armazenamento com o SSE habilitado usando o Azure PowerShell e a CLI do Azure?**

R: Lançaremos esse recurso na próxima versão do Azure PowerShell e da CLI do Azure, prevista atualmente para o final de abril.

**P: Há algum custo adicional no Armazenamento do Azure se o SSE estiver habilitado?**

R: Não há qualquer custo adicional.

**P: Como posso me inscrever para a visualização?**

R: Você pode registrar-se para ter acesso à visualização usando o PowerShell. Após a aprovação de sua assinatura no recurso, você poderá usar o PowerShell para habilitar a Criptografia em Repouso.

**P: Quando eu me inscrever para a visualização usando o PowerShell, qual é o nome do recurso que precisarei registrar?**

R: EncryptionAtRest.

**P: Quem gerencia as chaves de criptografia?**

R: As chaves são gerenciadas pela Microsoft.

**P: Posso usar minhas próprias chaves de criptografia?**

R: Estamos trabalhando no fornecimento de recursos para que os clientes tragam suas próprias chaves de criptografia.

**P: Posso revogar o acesso às chaves de criptografia?**

R: Não no momento; as chaves são totalmente gerenciadas pela Microsoft.

**P: O SSE é habilitado por padrão quando eu crio uma nova conta de armazenamento?**

R: O SSE não está habilitado por padrão. Você pode usar o Portal do Azure para habilitá-lo. Você também pode habilitar esse recurso por meio de programação usando a API REST do Provedor de Recursos de Armazenamento.

**P: Qual é a diferença para a Criptografia de Unidade do Azure?**

R: Esse recurso é usado para criptografar dados no Armazenamento de Blobs do Azure. A Azure Disk Encryption é usada para criptografar o sistema operacional e discos de dados em VMs de IaaS. Para obter mais detalhes, visite nosso [Guia de segurança do armazenamento](storage-security-guide.md).

**P: E se eu habilitar o SSE e depois habilitar a Azure Disk Encryption nos discos?**

R: Isso funcionará perfeitamente. Os dados serão criptografados pelos dois métodos.

**P: Minha conta de armazenamento está configurada para ser replicada de forma geograficamente redundante. Se eu habilitar o SSE, minha cópia redundante também será criptografada?**

R: Sim, todas as cópias da conta de armazenamento são criptografadas, e todas as opções de redundância, ou seja, LRS (Armazenamento com Redundância Local), ZRS (Armazenamento com Redundância de Zona), GRS (Armazenamento com Redundância Geográfica) e RA-GRS (Armazenamento com Redundância Geográfica de Acesso de Leitura) recebem suporte.

**P: Não consigo habilitar a criptografia em minha conta de armazenamento.**

R: Quando você criou a conta de armazenamento? Durante a versão de visualização, você precisará registrar sua assinatura e também criar uma nova conta de armazenamento para poder usar o SSE; não é possível habilitar o SSE em contas de armazenamento criadas antes da versão de visualização.

**P: A visualização do SSE é permitida somente em regiões específicas?**

R: A preview do SSE está disponível na Ásia Oriental e na Europa Ocidental para o Armazenamento Standard e no Leste do Japão para o Armazenamento Premium. À medida que lançarmos para regiões adicionais nos próximos meses, atualizaremos este documento.

**P: Como devo contatar alguém se eu tiver problemas ou quiser enviar comentários?**

R: Entre em contato com [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) para falar sobre problemas relacionados à Criptografia do Serviço de Armazenamento.

##Próximas etapas

O Armazenamento do Azure fornece um conjunto abrangente de recursos de segurança que, juntos, permitem aos desenvolvedores criar aplicativos seguros. Para obter mais detalhes, visite o [Guia de segurança do armazenamento](storage-security-guide.md).

<!---HONumber=AcomDC_0706_2016-->