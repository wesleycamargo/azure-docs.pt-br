---
title: Gerenciar o Azure Cosmos DB no Gerenciador de Armazenamento do Azure
description: Aprenda como gerenciar o Azure Cosmos DB no Gerenciador de Armazenamento do Azure.
Keywords: Azure Cosmos DB, Azure Storage Explorer, MongoDB
services: cosmos-db
documentationcenter: ''
author: Jejiang
manager: omafnan
editor: ''
tags: Azure Cosmos DB
ms.assetid: ''
ms.service: cosmos-db
ms.custom: Azure Cosmos DB active
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2018
ms.author: jejiang
ms.openlocfilehash: c593eb2b49d15d20a26ef735d1032d5dea45f125
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cosmos-db-in-storage-explorer-troubleshooting-guide-overview"></a>Visão geral do guia de solução de problemas do Azure Cosmos DB no Gerenciador de Armazenamento

[Azure Cosmos DB no Gerenciador de Armazenamento](https://docs.microsoft.com/en-us/azure/cosmos-db/storage-explorer) é um aplicativo autônomo que permite que você se conecte a contas do Azure Cosmos DB hospedadas nas nuvens do Azure e soberanas do Windows, do macOS ou do Linux. Ele permite a você gerenciar entidades do Azure Cosmos DB, manipular dados, atualizar procedimentos armazenados e gatilhos junto com outras entidades do Azure, como blobs e filas do Armazenamento.

Este guia resume as soluções de problemas comuns do Azure Cosmos DB encontradas no Gerenciador de Armazenamento.

- [Problemas de entrada](#Sign-in-issues)
  - [Certificado autoassinado na cadeia confiável](#Self-signed-certificate-in-certificate-chain)
  - [Não é possível recuperar as assinaturas](#Unable-to-retrieve-subscriptions)
  - [Não é possível ver a página de autenticação](#Unable-to-see-the-authentication-page)
  - [Não é possível remover a conta](#Cannot-remove-account)
- [Problema de proxy HTTP/HTTPS](#Http/Https-proxy-issue)
- [Problema de nó "Desenvolvimento" em "Local e Anexado"](#Development-node-under-Local-and-Attached-node-issue)
- [Erro de anexação de conta do Azure Cosmos DB ao nó "Local e Anexado"](#Attaching-Azure-Cosmos-DB-account-in-Local-and-Attached-node-error)
- [Erro de expansão do nó do Azure Cosmos DB](#Expand-Azure-Cosmos-DB-node-error)
- [Próximas etapas](#Next-steps)

<h2 id="Sign-in-issues">Problemas de entrada</h2>

Antes de continuar, tente reiniciar o aplicativo e ver se os problemas podem ser corrigidos.

<h2 id="Self-signed-certificate-in-certificate-chain">Certificado autoassinado na cadeia confiável</h2>

Há alguns motivos que podem exibir esse erro. Os dois mais comuns são:

1. Você está por trás de um "proxy transparente", o que significa que alguém (por exemplo, o departamento de TI) está interceptando o tráfego HTTPS, descriptografando-o e criptografando-o usando um certificado autoassinado.

2. Você está executando software, por exemplo, um software antivírus, que está injetando certificados SSL autoassinados nas mensagens HTTPS recebidas.

Quando o Gerenciador de Armazenamento encontrar um desses "certificados autoassinados", ele pode não saber se a mensagem HTTPS recebida foi adulterada. No entanto, se você tiver uma cópia do certificado autoassinado, poderá ordenar o Gerenciador de Armazenamento para confiar nele. Se você não tiver certeza de quem está injetando o certificado, tente localizá-lo por conta própria seguindo as etapas abaixo:

1. Instalar o Open SSL
     - [Windows](https://slproweb.com/products/Win32OpenSSL.html) (qualquer uma das versões leves serve)
     - Mac e Linux: deve estar incluído com o sistema operacional
2. Executar Open SSL
    - Windows: vá para o diretório de instalação; em seguida, para **/bin/** e clique duas vezes em **openssl.exe**.
    - Mac e Linux: execute **openssl** em um terminal
3. Execute `s_client -showcerts -connect microsoft.com:443`
4. Procurar certificados autoassinados. Se você não tiver certeza quais são autoassinados, procure onde o assunto ("s") e o emissor ("i") são os mesmos.
5.  Depois de encontrar os certificados autoassinados, copie e cole tudo desde, e incluindo, **---BEGIN CERTIFICATE---** até **---END CERTIFICATE---** em um novo arquivo .cer para cada um.
6.  Abra o Gerenciador de Armazenamento e vá para **Editar** > **Certificados SSL** > **Importar Certificados**. Usando o seletor de arquivos, encontre, selecione e abra os arquivos .cer criados.

Se você não conseguir localizar todos os certificados autoassinados usando as etapas acima, envie comentários para obter mais ajuda.

<h2 id="Unable-to-retrieve-subscriptions">Não é possível recuperar as assinaturas</h2>

Se não for possível recuperar suas assinaturas depois de entrar com êxito:

- Verifique se sua conta tem acesso às assinaturas entrando no [Portal do Azure](http://portal.azure.com/)
- Verifique se você entrou usando o ambiente certo ([Azure](http://portal.azure.com/), [Azure China](https://portal.azure.cn/), [Azure Alemanha](https://portal.microsoftazure.de/), [Azure US Government](http://portal.azure.us/) ou Ambiente Personalizado/Azure Stack)
- Se você estiver atrás de um proxy, verifique se configurou o proxy do Gerenciador de Armazenamento apropriadamente
- Tentar remover e readicionar a conta
- Tente excluir os seguintes arquivos do diretório inicial (por exemplo,C:\Users\ContosoUser) e adicione a conta novamente:
  - .adalcache
  - .devaccounts
  - .extaccounts
- Veja se aparecem mensagens de erro no console de ferramentas de desenvolvedor (F12) ao entrar

![console](./media/troubleshoot-cosmosdb/console.png)

<h2 id="Unable-to-see-the-authentication-page">Não é possível ver a página de autenticação</h2>  

Se você não conseguir ver a página de autenticação:

- Dependendo da velocidade de sua conexão, talvez demore algum tempo para a página de entrada carregar. Aguarde pelo menos um minuto antes de fechar o diálogo de autenticação
- Se você estiver atrás de um proxy, verifique se configurou o proxy do Gerenciador de Armazenamento apropriadamente
- Exiba o console do desenvolvedor pressionando a tecla F12. Veja as respostas no console do desenvolvedor e tente encontrar alguma dica do motivo do não funcionamento da autenticação

<h2 id="Cannot-remove-account">Não é possível remover a conta</h2>

Se você não conseguir remover uma conta, ou se o link de nova autenticação não fizer nada

- Tente excluir os seguintes arquivos do diretório inicial e adicione a conta novamente:
  - .adalcache
  - .devaccounts
  - .extaccounts
- Se você quiser remover SAS associado a recursos de Armazenamento, exclua:
  - Pasta %AppData%/StorageExplorer para Windows
  - /Usuários/<seu_nome>/Library/Applicaiton SUpport/StorageExplorer para Mac
  - ~/.config/StorageExplorer para Linux
  - **Será necessário reinserir todas as suas credenciais** se você excluir esses arquivos


<h2 id="Http/Https-proxy-issue">Problema de proxy HTTP/HTTPS</h2>

Não é possível listar os nós do Azure Cosmos DB na árvore à esquerda na hora de configurar o proxy HTTP/HTTPS no ASE. Esse é um problema conhecido e será corrigido na próxima versão. Você pode usar o Data Explorer do Azure Cosmos DB no Portal do Azure como uma solução alternativa no momento. 

<h2 id="Development-node-under-Local-and-Attached-node-issue">Problema de nó "Desenvolvimento" em "Local e Anexado"</h2>

Não há nenhuma resposta depois de clicar no nó "Desenvolvimento" no nó "Local e Anexado" na árvore à esquerda.  O comportamento é esperado. O emulador de local do Azure Cosmos DB terá suporte a partir da próxima versão.

![Nó de desenvolvimento](./media/troubleshoot-cosmosdb/development.png)

<h2 id="Attaching-Azure-Cosmos-DB-account-in-Local-and-Attached-node-error">Erro de anexação de conta do Azure Cosmos DB ao nó "Local e Anexado"</h2>

Se você vir o erro abaixo depois de anexar a conta do Azure Cosmos DB ao nó "Local e Anexado", verifique se você está usando a cadeia de conexão certa.

![Erro de anexação do Azure Cosmos DB a "Local e Anexado"](./media/troubleshoot-cosmosdb/attached-error.png)

<h2 id="Expand-Azure-Cosmos-DB-node-error">Erro de expansão do nó do Azure Cosmos DB</h2>

Você pode ver o erro abaixo ao tentar expandir os nós da árvore à esquerda. 

![Erro de expansão](./media/troubleshoot-cosmosdb/expand-error.png)

Tente as seguintes sugestões:

- Verifique se a conta do Azure Cosmos DB está sendo provisionada e tente novamente quando a conta for criada com êxito.
- Se a conta estiver no nó "Acesso Rápido" ou "Local e Anexado", verifique se a conta foi excluída. Nesse caso, você precisará remover o nó manualmente.

<h2 id="Next-steps">Próximas etapas</h2>

Se nenhuma das soluções funcionar, envie um email à equipe das Ferramentas de Desenvolvimento do Azure Cosmos DB ([cosmosdbtooling@microsoft.com](mailto:cosmosdbtooling@microsoft.com)) com detalhes sobre o problema, para que possa ser corrigido.





