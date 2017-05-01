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
ms.date: 04/06/2017
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 767698f12a5cb58fafc70c58e3d36a65194c2999
ms.lasthandoff: 04/07/2017


---
# <a name="use-the-azure-documentdb-emulator-for-development-and-testing"></a>Usar o Emulador do Azure DocumentDB para desenvolvimento e teste

[**Baixar o Emulador**](https://aka.ms/documentdb-emulator)

O Emulador do Azure DocumentDB fornece um ambiente local que emula o serviço do Azure DocumentDB para fins de desenvolvimento. Com o Emulador do DocumentDB, você pode desenvolver e testar seu aplicativo localmente sem criar uma assinatura do Azure ou incorrer em custos. Quando você estiver satisfeito com o funcionamento de seu aplicativo no Emulador do DocumentDB, alterne para uma conta do DocumentDB do Azure na nuvem.

É recomendável começar assistindo ao vídeo a seguir, onde Kirill Gavrylyuk mostra como começar a usar o Emulador do DocumentDB.

> [!VIDEO https://channel9.msdn.com/Events/Connect/2016/192/player]
> 
> 

## <a name="system-requirements"></a>Requisitos do sistema
O Emulador do DocumentDB tem os seguintes requisitos de hardware e software:

* Requisitos de software
  * Windows Server 2012 R2, Windows Server 2016 ou Windows 10
*    Requisitos mínimos de hardware
  *    2 GB de RAM
  *    Espaço disponível no disco rígido de 10 GB

## <a name="installation"></a>Instalação
Você pode baixar e instalar o Emulador do DocumentDB no [Centro de Download da Microsoft](https://aka.ms/documentdb-emulator). 

> [!NOTE]
> Para instalar, configurar e executar o Emulador do DocumentDB, você deve ter privilégios administrativos no computador.

## <a name="running-on-docker-for-windows"></a>Executar no Docker para Windows

O emulador do DocumentDB pode ser executado no Docker para Windows. O emulador não funciona no Docker para Oracle Linux.

Depois de ter instalado o [Docker para Windows](https://www.docker.com/docker-windows) é possível, do Hub do Docker, efetuar pull da imagem do emulador executando o comando a seguir do seu shell favorito (cmd.exe, PowerShell, etc.).

```      
docker pull mominag/documentdb_emulator 
```
Para iniciar a imagem, execute os comandos a seguir.

``` 
md %LOCALAPPDATA%\DocumentDBEmulatorCert 2>nul
docker run -v %LOCALAPPDATA%\DocumentDBEmulatorCert:c:\DocumentDBEmulator\DocumentDBEmulatorCert -P -t -i mominag/documentdb_emulator
```

A resposta tem aparência semelhante à seguinte:

```
Starting Emulator
Emulator Endpoint: https://172.20.229.193:8081/
Master Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
Exporting SSL Certificate
You can import the SSL certificate from an administrator command prompt on the host by running:
cd /d %LOCALAPPDATA%\DocumentDBEmulatorCert
powershell .\importcert.ps1
--------------------------------------------------------------------------------------------------
Starting interactive shell
``` 

Fechar o shell interativo depois de o emulador ter sido iniciado desligará o contêiner do emulador.

Use o ponto de extremidade e a chave mestra da resposta no seu cliente e importe o certificado SSL no seu host. Para importar o certificado SSL, faça o seguinte em um prompt de comando do administrador:

```
cd %LOCALAPPDATA%\DocumentDBEmulatorCert
powershell .\importcert.ps1
```

## <a name="checking-for-updates"></a>Procurando atualizações
O Emulador do DocumentDB inclui um Data Explorer interno do Azure DocumentDB para procurar dados armazenados no DocumentDB, criar novas coleções e permitir que você saiba quando uma nova atualização está disponível para download. 

> [!NOTE]
> Não há garantias de que os dados criados em uma versão do Emulador do DocumentDB possam ser acessados em outras versão. Se você precisar persistir seus dados por longo prazo, é recomendável armazená-los em uma conta do Azure DocumentDB, e não no Emulador do DocumentDB. 

## <a name="how-the-emulator-works"></a>Como o emulador funciona
O Emulador do DocumentDB fornece uma emulação de alta fidelidade do serviço DocumentDB. Ele oferece suporte à funcionalidade idêntica ao Azure DocumentDB, incluindo suporte para criar e consultar documentos JSON, provisionar e dimensionar coleções, bem como executar procedimentos armazenados e gatilhos. Você pode desenvolver e testar aplicativos usando o Emulador do DocumentDB e implantá-los no Azure em escala global, simplesmente fazendo uma única mudança de configuração no ponto de extremidade de conexão do DocumentDB.

Embora tenhamos criado uma emulação local de alta fidelidade do serviço DocumentDB real, a implementação do Emulador do DocumentDB é diferente da implementação do serviço. Por exemplo, o Emulador do DocumentDB usa componentes padrão do SO, como sistema de arquivos local para persistência e pilha de protocolo HTTPS para conectividade. Isso significa que algumas funcionalidades que dependem da infraestrutura do Azure, como replicação global, latência de único dígito em milissegundos para leituras/gravações e níveis de consistência ajustáveis, não estão disponíveis pelo Emulador do DocumentDB.


## <a name="authenticating-requests"></a>Autenticar solicitações
Assim como no Documento do Azure na nuvem, cada solicitação feita no Emulador do DocumentDB deve ser autenticada. O Emulador do DocumentDB dá suporte a uma única conta fixa e a uma chave de autenticação conhecida para autenticação de chave mestra. Essa conta e a chave são as únicas credenciais permitidas para uso com o Emulador do DocumentDB. Eles são:

    Account name: localhost:<port>
    Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==

> [!NOTE]
> A chave mestra com suporte do Emulador do DocumentDB destina-se ao uso somente com o emulador. Você não pode usar sua conta e chave de produção do DocumentDB com o Emulador do DocumentDB. 

Além disso, assim como o serviço Azure DocumentDB, o Emulador do DocumentDB permite apenas comunicação segura via SSL.

## <a name="start-and-initialize-the-emulator"></a>Iniciar e inicializar o emulador

Para iniciar o Emulador do Azure DocumentDB, selecione o botão Iniciar ou pressione a tecla Windows. Comece digitando **Emulador do DocumentDB** e selecione o emulador na lista de aplicativos. 

![Selecionar o botão Iniciar ou pressionar a tecla Windows, começar digitando **Emulador do DocumentDB** e selecionar o emulador na lista de aplicativos](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-start.png)

Quando o emulador estiver em execução, você verá um ícone na área de notificação da barra de tarefas do Windows. O Emulador do DocumentDB, por padrão, é executado no computador local ("localhost") escutando na porta 8081.

![Notificação na barra de tarefas do emulador local de DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-taskbar.png)

O Emulador do DocumentDB é instalado por padrão no diretório `C:\Program Files\DocumentDB Emulator`. Você também pode iniciar e parar o emulador pela linha de comando. Veja [Referência da ferramenta de linha de comando](#command-line) para obter mais informações.

## <a name="start-data-explorer"></a>Iniciar o Data Explorer

Quando o emulador do DocumentDB é iniciado, ele abre automaticamente o Data Explorer do DocumentDB no seu navegador. O endereço aparecerá como [https://localhost:8081/_explorer/index.html](https://localhost:8081/_explorer/index.html). Se você fechar o Explorer e quiser reabri-lo mais tarde, é possível abrir a URL no navegador ou iniciá-lo no Emulador do DocumentDB no Ícone de Bandeja do Windows, como mostrado abaixo.

![Iniciador do Data Explorer do emulador local do DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-data-explorer-launcher.png)

## <a name="developing-with-the-emulator"></a>Desenvolver com o Emulador
Depois que o Emulador do DocumentDB estiver em execução na área de trabalho, você poderá usar qualquer [SDK do DocumentDB](documentdb-sdk-dotnet.md) com suporte ou a [API REST do DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) para interagir com o Emulador. O Emulador do DocumentDB também inclui um Data Explorer interno que permite criar coleções, exibir e editar documentos sem precisar escrever qualquer código. 

    // Connect to the DocumentDB Emulator running locally
    DocumentClient client = new DocumentClient(
        new Uri("https://localhost:8081"), 
        "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

Se você estiver usando o [suporte ao protocolo do DocumentDB para MongoDB](documentdb-protocol-mongodb.md), use a seguinte cadeia de conexão:

    mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10250/admin?ssl=true&3t.sslSelfSignedCerts=true

Você pode usar ferramentas existentes, como o [DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio), para se conectar ao Emulador do DocumentDB. Também é possível migrar dados entre o Emulador do DocumentDB e o serviço Azure DocumentDB usando a [Ferramenta de Migração de Dados do DocumentDB](https://github.com/azure/azure-documentdb-datamigrationtool).

Usando o emulador do DocumentDB, por padrão, você pode criar até 25 coleções de partição única ou uma coleção particionada. Para saber mais sobre como alterar esse valor, veja [Definição do valor de PartitionCount](#set-partitioncount).

## <a name="export-the-ssl-certificate"></a>Exportar o certificado SSL

As linguagens .NET e o tempo de execução usam o Repositório de Certificados do Windows para conexão segura com o emulador local do DocumentDB. Outras linguagens têm seu próprio método de gerenciar e usar certificados. O Java usa seu próprio [repositório de certificados](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html) enquanto o Python usa [wrappers de soquete](https://docs.python.org/2/library/ssl.html).

Para obter um certificado para uso com linguagens e tempos de execução que não se integram ao Repositório de Certificados do Windows, você precisará exportá-lo usando o Gerenciador de Certificados do Windows. Você pode iniciá-lo executando certlm.msc ou seguir as instruções passo a passo em [Exportar os certificados do Emulador do DocumentDB](./documentdb-nosql-local-emulator-export-ssl-certificates.md). Depois que o gerenciador de certificados estiver em execução, abra os Certificados Pessoais, conforme mostrado abaixo, e exporte o certificado com o nome amigável "DocumentDBEmulatorCertificate" como um arquivo X.509 codificado em BASE-64 (.cer).

![Certificado SSL do emulador local do DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-ssl_certificate.png)

O certificado X.509 pode ser importado no repositório de certificados Java seguindo as instruções em [Adicionando um certificado ao repositório de certificados de AC do Java](https://docs.microsoft.com/en-us/azure/java-add-certificate-ca-store). Depois que o certificado é importado para o repositório cacerts, os aplicativos Java e MongoDB poderão se conectar ao Emulador do DocumentDB.

Durante a conexão do emulador de SDKs Python e Node.js, a verificação de SSL é desabilitada.

## <a id="command-line"></a>Referência da ferramenta de linha de comando
No local da instalação, você pode usar a linha de comando para iniciar e interromper o emulador, configurar opções e executar outras operações.

### <a name="command-line-syntax"></a>Sintaxe da linha de comando

    DocumentDB.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/?]

Para exibir a lista de opções, digite `DocumentDB.Emulator.exe /?` no prompt de comando.

<table>
<tr>
  <td><strong>Opção</strong></td>
  <td><strong>Descrição</strong></td>
  <td><strong>Comando</strong></td>
  <td><strong>Argumentos</strong></td>
</tr>
<tr>
  <td>[No arguments]</td>
  <td>Inicia o Emulador do DocumentDB com as configurações padrão.</td>
  <td>DocumentDB.Emulator.exe</td>
  <td></td>
</tr>
<tr>
  <td>[Ajuda]</td>
  <td>Exibe a lista de argumentos de linha de comando com suporte.</td>
  <td>DocumentDB.Emulator.exe /?</td>
  <td></td>
</tr>
<tr>
  <td>Shutdown</td>
  <td>Desliga o Emulador do DocumentDB.</td>
  <td>DocumentDB.Emulator.exe /Shutdown</td>
  <td></td>
</tr>
<tr>
  <td>DataPath</td>
  <td>Especifica o caminho no qual armazenar os arquivos de dados. O padrão é % LocalAppdata%\DocumentDBEmulator.</td>
  <td>DocumentDB.Emulator.exe /DataPath=&lt;caminhodedados&gt;</td>
  <td>&lt;datapath&gt;: um caminho acessível</td>
</tr>
<tr>
  <td>Porta</td>
  <td>Especifica o número da porta a ser usada para o emulador.  O padrão é 8081.</td>
  <td>DocumentDB.Emulator.exe /Port=&lt;porta&gt;</td>
  <td>&lt;port&gt;: único número de porta</td>
</tr>
<tr>
  <td>MongoPort</td>
  <td>Especifica o número da porta a ser usada para API de compatibilidade do MongoDB. O padrão é 10250.</td>
  <td>DocumentDB.Emulator.exe /MongoPort=&lt;portamongo&gt;</td>
  <td>&lt;mongoport&gt;: único número de porta</td>
</tr>
<tr>
  <td>DirectPorts</td>
  <td>Especifica as portas a serem usadas para conectividade direta. Os padrões são 10251,10252,10253,10254.</td>
  <td>DocumentDB.Emulator.exe /DirectPorts:&lt;portasdiretas&gt;</td>
  <td>&lt;directports&gt;: lista de 4 portas delimitadas por vírgula</td>
</tr>
<tr>
  <td>Chave</td>
  <td>Chave de autorização para o emulador. A chave deve ser a codificação de base 64 de um vetor de 64 bytes.</td>
  <td>DocumentDB.Emulator.exe /Key:&lt;chave&gt;</td>
  <td>&lt;key&gt;: a chave de ser a codificação base-64 de um vetor de 64 bytes</td>
</tr>
<tr>
  <td>EnableRateLimiting</td>
  <td>Especifica que o comportamento de limitação da taxa de solicitação está habilitado.</td>
  <td>DocumentDB.Emulator.exe /EnableRateLimiting</td>
  <td></td>
</tr>
<tr>
  <td>DisableRateLimiting</td>
  <td>Especifica que o comportamento de limitação da taxa de solicitação está desabilitado.</td>
  <td>DocumentDB.Emulator.exe /DisableRateLimiting</td>
  <td></td>
</tr>
<tr>
  <td>NoUI</td>
  <td>Não mostra o emulador de interface do usuário.</td>
  <td>DocumentDB.Emulator.exe /NoUI</td>
  <td></td>
</tr>
<tr>
  <td>NoExplorer</td>
  <td>Não mostra o gerenciador de documentos na inicialização.</td>
  <td>DocumentDB.Emulator.exe /NoExplorer</td>
  <td></td>
</tr>
<tr>
  <td>PartitionCount</td>
  <td>Especifica o número máximo de coleções particionadas. Veja [Alterar o número de coleções](#set-partitioncount) para saber mais.</td>
  <td>DocumentDB.Emulator.exe /PartitionCount=&lt;contagemdepartições&gt;</td>
  <td>&lt;contagemdepartições&gt;: número máximo permitido de coleções de partição única. O padrão é 25. O máximo permitido é 250.</td>
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

## <a id="set-partitioncount"></a>Alterar o número de coleções

Por padrão, você pode criar até 25 coleções de partição única ou uma coleção particionada usando o emulador do DocumentDB. Modificando o valor **PartitionCount**, você pode criar até 250 coleções de partição única ou 10 coleções particionadas ou qualquer combinação dos dois que não excedam 250 partições únicas (onde 1 coleção particionada = 25 coleções de partição única).

Se você tentar criar uma coleção depois que a contagem de partição atual tiver sido excedida, o emulador lançará uma exceção de ServiceUnavailable, com a mensagem de erro a seguir.

    Sorry, we are currently experiencing high demand in this region, 
    and cannot fulfill your request at this time. We work continuously 
    to bring more and more capacity online, and encourage you to try again. 
    Please do not hesitate to email docdbswat@microsoft.com at any time or 
    for any reason. ActivityId: 29da65cc-fba1-45f9-b82c-bf01d78a1f91

Para alterar o número de coleções disponíveis para o Emulador do DocumentDB, faça o seguinte:

1. Exclua todos os dados locais do Emulador do DocumentDB clicando com o ícone do **Emulador do DocumentDB** na bandeja do sistema e clicando em **Redefinir Dados...** .
2. Exclua todos os dados de emulador desta pasta C:\Users\user_name\AppData\Local\DocumentDBEmulator.
3. Saia de todas as instâncias clicando no ícone do **Emulador do DocumentDB** na bandeja do sistema e clicando em **Sair**. Pode levar um minuto para que todas as instâncias saiam.
4. Instale a versão mais recente do [Emulador do DocumentDB](https://aka.ms/documentdb-emulator).
5. Inicie o emulador com o sinalizador PartitionCount definindo um valor <= 250. Por exemplo: `C:\Program Files\DocumentDB Emulator>DocumentDB.Emulator.exe /PartitionCount=100`.

## <a name="troubleshooting"></a>Solucionar problemas

Use as dicas a seguir para ajudar a solucionar problemas encontrados com o emulador do DocumentDB:

- Se o emulador do DocumentDB falhar, colete os arquivos de despejo na pasta c:\Users\user_name\AppData\Local\CrashDumps, compacte-os e anexe-os a um email que deve ser enviado para [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com).

- Se você enfrentar falhas em DocumentDB.StartupEntryPoint.exe, execute o seguinte comando em um prompt de comando de administrador: `lodctr /R` 

- Se você encontrar um problema de conectividade, [colete os arquivos de rastreamento](#trace-files), compacte-os e anexe-os a um email que deve ser enviado para [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com).

### <a id="trace-files"></a>Coletar arquivos de rastreamento

Para coletar rastreamentos de depuração, execute os seguintes comandos em um prompt de comando administrativo:

1. `cd /d "%ProgramFiles%\DocumentDB Emulator"`
2. `DocumentDB.Emulator.exe /shutdown`. Confira a bandeja do sistema para ter certeza de que o programa foi desligado. Isso pode levar um minuto. Você também pode simplesmente clicar em **Sair** na interface do usuário do emulador do DocumentDB.
3. `DocumentDB.Emulator.exe /starttraces`
4. `DocumentDB.Emulator.exe`
5. Reproduza o problema. Se o Data Explorer não está funcionando, você só precisa aguardar que o navegador abra por alguns segundos para capturar o erro.
5. `DocumentDB.Emulator.exe /stoptraces`
6. Navegue até `%ProgramFiles%\DocumentDB Emulator` e localize o arquivo docdbemulator_000001.etl.
7. Envie o arquivo .etl junto com as etapas reproduzidas para [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com) para fins de depuração.


## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre o DocumentDB, veja [Introdução ao Azure DocumentDB](documentdb-introduction.md)
* Para começar a desenvolver no Emulador do DocumentDB, baixe um dos [SDKs do DocumentDB com suporte](documentdb-sdk-dotnet.md).

