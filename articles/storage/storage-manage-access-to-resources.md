<properties 
	pageTitle="Gerenciar o acesso anônimo aos contêineres e blobs | Microsoft Azure" 
	description="Saiba como disponibilizar os contêineres e blobs para acesso anônimo." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="jdial" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/28/2015" 
	ms.author="micurd;tamram"/>

# Gerenciar o acesso aos recursos de Armazenamento do Azure

## Visão geral

Por padrão, somente o proprietário da conta de armazenamento pode acessar os recursos de armazenamento nessa conta. Se seu serviço ou aplicativo precisar tornar esses recursos disponíveis para outros clientes sem compartilhar sua chave de acesso, você tem as seguintes opções para permitir acesso:

- Definir permissões do contêiner para permitir o acesso de leitura anônimo para o contêiner e seus blobs. O acesso de leitura anônimo está disponível apenas para contêineres e blobs. 

- É possível expor um recurso através de uma assinatura de acesso compartilhado, que permite que você delegue acesso restrito a um contêiner, um blob, uma tabela, uma fila, um compartilhamento de arquivos ou um arquivo, especificando o intervalo no qual os recursos estarão disponíveis e as permissões que um cliente terá.

- É possível usar uma política de acesso armazenado para gerenciar assinaturas de acesso compartilhado para um contêiner ou seus blobs, uma fila, uma tabela ou um compartilhamento de arquivos ou seus arquivos. A política de acesso armazenado fornece uma medida adicional de controle sobre suas assinaturas de acesso compartilhado e também fornece um meio simples de revogá-los.

## Conceder permissões de usuários anônimos a contêineres e blobs

Por padrão, um contêiner e todos os blobs dentro dele podem ser acessados somente pelo proprietário da conta de armazenamento. Para dar aos usuários anônimos permissões de leitura a um contêiner e seus blobs, é possível definir as permissões de contêiner para permitir acesso público. Usuários anônimos podem ler blobs dentro de um contêiner publicamente acessível sem autenticar a solicitação.

Os contêineres fornecem as seguintes opções para gerenciar o acesso do contêiner:

- **Acesso completo de leitura pública:** os dados de contêiner e blob podem ser lidos por solicitação anônima. Os clientes podem enumerar os blobs no contêiner por meio de uma solicitação anônima, mas não podem enumerar os contêineres em uma conta de armazenamento.

- **Acesso de leitura pública somente para blobs:** os dados blob nesse contêiner podem ser lidos por solicitação anônima, mas os dados do contêiner não estão disponíveis. Os clientes não podem enumerar os blobs no contêiner por meio de uma solicitação anônima.

- **Sem acesso de leitura público:** os dados de contêiner e de blob podem ser lidos por apenas o proprietário da conta.

>[AZURE.NOTE]Se seu serviço exigir que você exerça controle mais granular sobre recursos de blob ou se você quiser fornecer permissões para operações diferentes de operações de leitura, é possível usar uma Assinatura de Acesso Compartilhado para tornar um recurso acessível aos usuários.

## Recursos disponíveis para usuários anônimos

A tabela a seguir mostra quais operações podem ser chamadas por usuários anônimos quando a ACL do contêiner é definida para permitir acesso público.

| Operação REST | Permissão com acesso de leitura público completo | Permissão de acesso de leitura público apenas para blobs |
|--------------------------------------------------------|-----------------------------------------|---------------------------------------------------|
| Listar contêineres | Somente proprietário | Somente proprietário |
| Create Container | Somente proprietário | Somente proprietário |
| Get Container Properties | Todos | Somente proprietário |
| Get Container Metadata | Todos | Somente proprietário |
| Set Container Metadata | Somente proprietário | Somente proprietário |
| Get Container ACL | Somente proprietário | Somente proprietário |
| Set Container ACL | Somente proprietário | Somente proprietário |
| Delete Container | Somente proprietário | Somente proprietário |
| Listar Blobs | Todos | Somente proprietário |
| Put Blob | Somente proprietário | Somente proprietário |
| Get Blob | Todos | Todos |
| Get Blob Properties | Todos | Todos |
| Set Blob Properties | Somente proprietário | Somente proprietário |
| Get Blob Metadata | Todos | Todos |
| Set Blob Metadata | Somente proprietário | Somente proprietário |
| Put Block | Somente proprietário | Somente proprietário |
| Obter lista de blocos (somente blocos confirmados) | Todos | Todos |
| Obter lista de blocos (somente blocos não confirmados ou todos os blocos) | Somente proprietário | Somente proprietário |
| Put Block List | Somente proprietário | Somente proprietário |
| Delete Blob | Somente proprietário | Somente proprietário |
| Copiar blob | Somente proprietário | Somente proprietário |
| Blob de instantâneo | Somente proprietário | Somente proprietário |
| Lease Blob | Somente proprietário | Somente proprietário |
| Put Page | Somente proprietário | Somente proprietário |
| Get Page Ranges | Todos | Todos |


## Consulte também

- [Autenticação para os Serviços de Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx)
- [Assinaturas de acesso compartilhado: noções básicas sobre o modelo SAS](storage-dotnet-shared-access-signature-part-1.md)
- [Delegando acesso com uma assinatura de acesso compartilhado](https://msdn.microsoft.com/library/azure/ee395415.aspx) 

<!---HONumber=Oct15_HO1-->