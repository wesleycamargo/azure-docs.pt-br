---
title: Desenvolver localmente com o Emulador do DocumentDB | Microsoft Docs
description: "Usando o Emulador do DocumentDB, você pode desenvolver e testar seu aplicativo no local gratuitamente, sem criar uma assinatura do Azure."
services: documentdb
documentationcenter: 
keywords: Emulador do DocumentDB
author: arramac
manager: jhubbard
editor: 
ms.assetid: 90b379a6-426b-4915-9635-822f1a138656
ms.service: documentdb
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2016
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 6c5bf8907a5f69e45e7b62fb466bdc53460e9029
ms.openlocfilehash: 86a5911e99e7631b09604afcb0f53ed2887b576b


---
# <a name="use-the-azure-documentdb-emulator-for-development-and-testing"></a>Usar o Emulador do Azure DocumentDB para desenvolvimento e teste

[**Baixar o Emulador**](https://aka.ms/documentdb-emulator)

O Emulador do Azure DocumentDB fornece um ambiente local que emula o serviço do Azure DocumentDB para fins de desenvolvimento. Com o Emulador do DocumentDB, você pode desenvolver e testar seu aplicativo localmente sem criar uma assinatura do Azure ou incorrer em custos. Quando você estiver satisfeito com o funcionamento de seu aplicativo no Emulador do DocumentDB, alterne para uma conta do DocumentDB do Azure na nuvem.

É recomendável começar assistindo ao vídeo a seguir, onde Kirill Gavrylyuk mostra como começar a usar o Emulador do DocumentDB.

> [!VIDEO https://channel9.msdn.com/Events/Connect/2016/192/player]
> 
> 

## <a name="documentdb-emulator-system-requirements"></a>Requisitos de sistema para o Emulador do DocumentDB
O Emulador do DocumentDB tem os seguintes requisitos de hardware e software:

* Requisitos de software
  * Windows Server 2012 R2, Windows Server 2016 ou Windows 10
*   Requisitos mínimos de hardware
  * 2 GB de RAM
  * Espaço disponível no disco rígido de 10 GB

## <a name="installing-the-documentdb-emulator"></a>Instalando o Emulador do DocumentDB
Você pode baixar e instalar o Emulador do DocumentDB no [Centro de Download da Microsoft](https://aka.ms/documentdb-emulator). 

> [!NOTE]
> Para instalar, configurar e executar o Emulador do DocumentDB, você deve ter privilégios administrativos no computador.

## <a name="checking-for-documentdb-emulator-updates"></a>Verificando se há atualizações do Emulador do DocumentDB
O Emulador do DocumentDB inclui um Data Explorer interno do Azure DocumentDB para procurar dados armazenados no DocumentDB, criar novas coleções e permitir que você saiba quando uma nova atualização está disponível para download. 

> [!NOTE]
> Não há garantias de que os dados criados em uma versão do Emulador do DocumentDB possam ser acessados em outras versão. Se você precisar persistir seus dados por longo prazo, é recomendável armazená-los em uma conta do Azure DocumentDB, e não no Emulador do DocumentDB. 

## <a name="how-the-documentdb-emulator-works"></a>Como o Emulador do DocumentDB funciona
O Emulador do DocumentDB fornece uma emulação de alta fidelidade do serviço DocumentDB. Ele oferece suporte à funcionalidade idêntica ao Azure DocumentDB, incluindo suporte para criar e consultar documentos JSON, provisionar e dimensionar coleções, bem como executar procedimentos armazenados e gatilhos. Você pode desenvolver e testar aplicativos usando o Emulador do DocumentDB e implantá-los no Azure em escala global, simplesmente fazendo uma única mudança de configuração no ponto de extremidade de conexão do DocumentDB.

Embora tenhamos criado uma emulação local de alta fidelidade do serviço DocumentDB real, a implementação do Emulador do DocumentDB é diferente da implementação do serviço. Por exemplo, o Emulador do DocumentDB usa componentes padrão do SO, como sistema de arquivos local para persistência e pilha de protocolo HTTPS para conectividade. Isso significa que algumas funcionalidades que dependem da infraestrutura do Azure, como replicação global, latência de único dígito em milissegundos para leituras/gravações e níveis de consistência ajustáveis, não estão disponíveis pelo Emulador do DocumentDB.


## <a name="authenticating-requests-against-the-documentdb-emulator"></a>Autenticando solicitações no Emulador do DocumentDB
Assim como no Documento do Azure na nuvem, cada solicitação feita no Emulador do DocumentDB deve ser autenticada. O Emulador do DocumentDB dá suporte a uma única conta fixa e a uma chave de autenticação conhecida para autenticação de chave mestra. Essa conta e a chave são as únicas credenciais permitidas para uso com o Emulador do DocumentDB. Eles são:

    Account name: localhost:<port>
    Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==

> [!NOTE]
> A chave mestra com suporte do Emulador do DocumentDB destina-se ao uso somente com o emulador. Você não pode usar sua conta e chave de produção do DocumentDB com o Emulador do DocumentDB. 

Além disso, assim como o serviço Azure DocumentDB, o Emulador do DocumentDB permite apenas comunicação segura via SSL.

## <a name="start-and-initialize-the-documentdb-emulator"></a>Iniciar e inicializar o Emulador do DocumentDB

Para iniciar o Emulador do Azure DocumentDB, selecione o botão Iniciar ou pressione a tecla Windows. Comece digitando **Emulador do DocumentDB** e selecione o emulador na lista de aplicativos. 

![Selecionar o botão Iniciar ou pressionar a tecla Windows, começar digitando **Emulador do DocumentDB** e selecionar o emulador na lista de aplicativos](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-start.png)

Quando o emulador estiver em execução, você verá um ícone na área de notificação da barra de tarefas do Windows. O Emulador do DocumentDB, por padrão, é executado no computador local ("localhost") escutando na porta 8081.

![Notificação na barra de tarefas do emulador local de DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-taskbar.png)

O Emulador do DocumentDB é instalado por padrão no diretório `C:\Program Files\DocumentDB Emulator`. Você também pode iniciar e parar o emulador pela linha de comando. Veja [Referência da ferramenta de linha de comando](#command-line) para obter mais informações.

## <a name="start-the-local-emulator-data-explorer"></a>Iniciar o Data Explorer do emulador local

Quando o emulador local é iniciado, ele abre automaticamente o Data Explorer do DocumentDB no seu navegador. O endereço aparecerá como [https://localhost:8081/_explorer/index.html](https://localhost:8081/_explorer/index.html). Se você fechar o Explorer e quiser reabri-lo mais tarde, é possível abrir a URL no navegador ou iniciá-lo no Emulador do DocumentDB no Ícone de Bandeja do Windows, como mostrado abaixo.

![Iniciador do Data Explorer do emulador local do DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-data-explorer-launcher.png)

## <a name="developing-with-the-documentdb-emulator"></a>Desenvolvendo com o Emulador do DocumentDB
Depois que o Emulador do DocumentDB estiver em execução na área de trabalho, você poderá usar qualquer [SDK do DocumentDB](documentdb-sdk-dotnet.md) com suporte ou a [API REST do DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) para interagir com o Emulador. O Emulador do DocumentDB também inclui um Data Explorer interno que permite criar coleções, exibir e editar documentos sem precisar escrever qualquer código. 

    // Connect to the DocumentDB Emulator running locally
    DocumentClient client = new DocumentClient(
        new Uri("https://localhost:8081"), 
        "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==",
        new ConnectionPolicy { EnableEndpointDiscovery = false });

> [!NOTE]
> Ao se conectar com o emulador, você deve definir EnableEndpointDiscovery = false na configuração da conexão.

Se você estiver usando o [suporte ao protocolo do DocumentDB para MongoDB](documentdb-protocol-mongodb.md), use a seguinte cadeia de conexão:

    mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10250/admin?ssl=true&3t.sslSelfSignedCerts=true

Você pode usar ferramentas existentes, como o [DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio), para se conectar ao Emulador do DocumentDB. Também é possível migrar dados entre o Emulador do DocumentDB e o serviço Azure DocumentDB usando a [Ferramenta de Migração de Dados do DocumentDB](https://github.com/azure/azure-documentdb-datamigrationtool).

## <a name="export-the-documentdb-emulator-ssl-certificate"></a>Exportar o certificado SSL do Emulador do DocumentDB

As linguagens e o tempo de execução .Net usam o Repositório de Certificados do Windows para conexão segura com o emulador local do DocumentDB. Outras linguagens têm seu próprio método de gerenciar e usar certificados. O Java usa seu próprio [repositório de certificados](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html) enquanto o Python usa [wrappers de soquete](https://docs.python.org/2/library/ssl.html).

Para obter um certificado para uso com linguagens e tempos de execução que não se integram ao Repositório de Certificados do Windows, você precisará exportá-lo usando o Gerenciador de Certificados do Windows. Você pode iniciá-lo executando certlm.msc ou seguir as instruções passo a passo na postagem "[Export the DocumentDB Emulator Certificates](./documentdb-nosql-local-emulator-export-ssl-certificates.md)" (Exportar os Certificados do Emulador do DocumentDB). Depois que o gerenciador de certificados estiver em execução, abra os Certificados Pessoais, conforme mostrado abaixo, e exporte o certificado com o nome amigável "DocumentDBEmulatorCertificate" como um arquivo X.509 codificado em BASE-64 (.cer).

![Certificado SSL do emulador local do DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-ssl_certificate.png)

O certificado X.509 pode ser importado no repositório de certificados Java seguindo as instruções na postagem "[Adicionar um certificado ao repositório de certificados Java CA](https://docs.microsoft.com/en-us/azure/java-add-certificate-ca-store)".  Depois que o certificado é importado no repositório cacerts, os aplicativos Java e MongoDB poderão se conectar ao Emulador Local do DocumentDB.

## <a name="a-idcommand-lineadocumentdb-emulator-command-line-tool-reference"></a><a id="command-line"></a>Referência da ferramenta de linha de comando do Emulador do DocumentDB
No local da instalação, você pode usar a linha de comando para iniciar e interromper o emulador, configurar opções e executar outras operações.

### <a name="command-line-syntax"></a>Sintaxe da linha de comando

    DocumentDB.Emulator.exe [/shutdown] [/datapath] [/port] [/mongoport] [/directports] [/key] [/?]

Para exibir a lista de opções, digite `DocumentDB.Emulator.exe /?` no prompt de comando.

<table>
<tr>
  <td><strong>Opção</strong></td>
  <td><strong>Descrição</strong></td>
  <td><strong>Command</strong></td>
  <td><strong>Argumentos</strong></td>
</tr>
<tr>
  <td>[No arguments]</td>
  <td>Inicia o Emulador do DocumentDB com configurações padrão</td>
  <td>DocumentDB.Emulator.exe</td>
  <td></td>
</tr>
<tr>
  <td>Shutdown</td>
  <td>Desliga o Emulador do DocumentDB</td>
  <td>DocumentDB.Emulator.exe /Shutdown</td>
  <td></td>
</tr>
<tr>
  <td>Ajuda</td>
  <td>Exibe a lista de argumentos da linha de comando</td>
  <td>DocumentDB.Emulator.exe /?</td>
  <td></td>
</tr>
<tr>
  <td>Datapath</td>
  <td>Especifica o caminho no qual armazenar os arquivos de dados</td>
  <td>DocumentDB.Emulator.exe /datapath=&lt;datapath&gt;</td>
  <td>&lt;datapath&gt;: um caminho acessível</td>
</tr>
<tr>
  <td>Porta</td>
  <td>Especifica o número da porta a ser usada para o emulador.  O padrão é 8081</td>
  <td>DocumentDB.Emulator.exe /port=&lt;port&gt;</td>
  <td>&lt;port&gt;: único número de porta</td>
</tr>
<tr>
  <td>MongoPort</td>
  <td>Especifica o número da porta a ser usada para API de compatibilidade do MongoDB. O padrão é 10250</td>
  <td>DocumentDB.Emulator.exe /mongoport=&lt;mongoport&gt;</td>
  <td>&lt;mongoport&gt;: único número de porta</td>
</tr>
<tr>
  <td>DirectPorts</td>
  <td>Especifica as portas a serem usadas para conectividade direta. Os padrões são 10251,10252,10253,10254</td>
  <td>DocumentDB.Emulator.exe /directports:&lt;directports&gt;</td>
  <td>&lt;directports&gt;: lista de 4 portas delimitadas por vírgula</td>
</tr>
<tr>
  <td>Chave</td>
  <td>Chave de autorização para o emulador. A chave deve ser a codificação base-64 de um vetor de 64 bytes</td>
  <td>DocumentDB.Emulator.exe /key:&lt;key&gt;</td>
  <td>&lt;key&gt;: a chave de ser a codificação base-64 de um vetor de 64 bytes</td>
</tr>
<tr>
  <td>EnableThrottling</td>
  <td>Especifica se o comportamento de limitação da solicitação está habilitada</td>
  <td>DocumentDB.Emulator.exe /enablethrottling</td>
  <td></td>
</tr>
<tr>
  <td>DisableThrottling</td>
  <td>Especifica se o comportamento de limitação da solicitação está desabilitada</td>
  <td>DocumentDB.Emulator.exe /disablethrottling</td>
  <td></td>
</tr>
</table>

## <a name="differences-between-the-documentdb-emulator-and-azure-documentdb"></a>Diferenças entre o Emulador do DocumentDB e o Azure DocumentDB 
Como o Emulador do DocumentDB é um ambiente emulado em execução em uma estação de trabalho do desenvolvedor local, há algumas diferenças na funcionalidade entre o emulador e a conta do Azure DocumentDB na nuvem:

* O Emulador do DocumentDB permite apenas uma única conta fixa e uma chave mestra conhecida.  Não é possível regenerar uma chave no Emulador do DocumentDB.
* O Emulador do DocumentDB não é um serviço escalonável e não suportará um grande número de coleções.
* O Emulador do DocumentDB não simula diferentes [níveis de consistência do DocumentDB](documentdb-consistency-levels.md).
* O Emulador do DocumentDB não simula [replicação de várias regiões](documentdb-distribute-data-globally.md).
* O Emulador do DocumentDB não dá suporte às substituições de cota de serviço que estão disponíveis no serviço Azure DocumentDB (por exemplo, limites de tamanho de documento, aumento do armazenamento de coleção particionado).
* Como a sua cópia do Emulador do DocumentDB pode não ser atualizada com as alterações mais recentes com o serviço Azure DocumentDB, use o [planejador de capacidade do DocumentDB](https://www.documentdb.com/capacityplanner) para estimar precisamente as necessidades de produtividade (RUs) do seu aplicativo.

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre o DocumentDB, veja [Introdução ao Azure DocumentDB](documentdb-introduction.md)
* Para começar a desenvolver no Emulador do DocumentDB, baixe um dos [SDKs do DocumentDB com suporte](documentdb-sdk-dotnet.md).



<!--HONumber=Dec16_HO3-->


