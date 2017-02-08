---
title: "Microsoft Azure Storage Explorer 0.8.7 (visualização) | Microsoft Docs"
description: "Notas de versão do Microsoft Azure Storage Explorer 0.8.7 (visualização)"
services: storage
documentationcenter: na
author: cawa
manager: paulyuk
editor: 
ms.assetid: 
ms.service: storage
ms.devlang: multiple
ms.topic: release-notes
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/18/2017
ms.author: cawa
translationtype: Human Translation
ms.sourcegitcommit: bf47274a97342253dfd4ebfa9f1029a7c18b81eb
ms.openlocfilehash: fc3620ca19d4824bc8ffb3bbe89ef47c5127b9d1


---
# <a name="microsoft-azure-storage-explorer-087-preview"></a>Gerenciador de Armazenamento do Microsoft Azure 0.8.7 (Visualização)
## <a name="overview"></a>Visão geral
Este artigo contém as notas de versão para versão de visualização do Azure Storage Explorer 0.8.7.

O [Gerenciador de Armazenamento do Microsoft Azure (Visualização)](./vs-azure-tools-storage-manage-with-storage-explorer.md) é um aplicativo autônomo que permite que você trabalhe facilmente com dados do Armazenamento do Azure no Windows, no macOS e no Linux.

## <a name="azure-storage-explorer-087-preview"></a>Gerenciador de Armazenamento do Azure 0.8.7 (Visualização)
### <a name="download-azure-storage-explorer-087-preview"></a>Baixar o Gerenciador de Armazenamento do Azure 0.8.7 (Visualização)
- [Armazenamento do Azure Explorer 0.8.7 Preview para Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Gerenciador de Armazenamento do Azure 0.8.7 Visualização para Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Visualização de 0.8.7 do Gerenciador de armazenamento do Azure para Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new-updates"></a>Novas atualizações
* Você pode escolher como resolver conflitos no início de uma atualização, download ou sessão de cópia no **atividades** janela.
* Passe o mouse sobre uma guia para ver o caminho completo do recurso de armazenamento.
* Quando você clica em uma guia, sincronizar com seu local no painel de navegação do lado esquerdo.

### <a name="fixes"></a>Correções
* Fixo: Storage Explorer agora é um aplicativo confiável no macOS.
* Fixo: Ubuntu 14.04 novamente é suportado.
* Corrigido: Às vezes, a interface de usuário de conta adicionar pisca ao carregar assinaturas.
* Fixo: Às vezes, nem todos os recursos de armazenamento foram listados no painel de navegação do lado esquerdo.
* Corrigido: O painel de ações, às vezes, exibido ações vazias.
* Fixo: O tamanho da janela da última sessão fechada agora é retido.
* Fixo: Você pode abrir várias guias para o mesmo recurso usando o menu de contexto.

### <a name="known-issues"></a>Problemas conhecidos
* Acesso rápido só funciona com itens baseados em assinatura. Recursos locais ou conectados por meio de chave ou o token SAS de recursos não têm suporte nesta versão.
* Acesso rápido pode demorar um pouco para navegar para o recurso de destino, dependendo de quantos recursos.
* Ter mais de três grupos de blobs ou arquivos Carregando ao mesmo tempo pode causar erros.
* Identificadores de pesquisa pesquisar em nós aproximadamente 50.000 - depois disso, o desempenho pode ser afetado ou pode causar exceções sem tratamento.
* Pela primeira vez usando o Gerenciador de armazenamento em macOS, você poderá ver vários prompts solicitando permissão do usuário acessar o conjunto de chaves. Sugerimos que você selecione **permitir sempre** para o prompt não exibe novamente

## <a name="previous-releases"></a>Versões anteriores
### <a name="features"></a>Recursos
#### <a name="main-features"></a>Principais recursos
* macOS, Linux e versões do Windows
* Entre para exibir as contas de armazenamento, agrupados por assinatura:
    * Use sua conta Org Account da Microsoft, 2FA, etc.
    * Configurar e gerenciar configurações de proxy
    * Remover contas ao sair
* Conecte-se ao uso de contas de armazenamento:
    * Nome e chave da conta
    * Pontos de extremidade personalizados (incluindo o Azure China)
    * URI SAS para contas de armazenamento
* Suporte ao Azure Resource Manager e ao Armazenamento Clássico
* Gerar chaves SAS para contêineres de blob, blobs, filas, tabelas ou compartilhamentos de arquivos
* Conectar contêineres de blob, filas, tabelas ou compartilhamentos de arquivos com chave de assinaturas de acesso compartilhado (SAS)
* Gerenciar políticas de acesso armazenado para contêineres de blob, filas, tabelas ou compartilhamentos de arquivos
* Armazenamento de desenvolvimento local com o emulador de armazenamento (somente Windows)
* Criar e excluir tabelas, filas ou contêineres de blob
* Exibição $logs contêineres de blob e tabelas de $metrics
* Procurar específicos blobs, filas, tabelas ou compartilhamentos de arquivos
* Compartilha links diretos para contas de armazenamento ou contêineres, filas, tabelas ou arquivo para compartilhar e acessar facilmente os recursos
* Renomear os contêineres de blob, tabelas, compartilhamentos de arquivos
* Capacidade de gerenciar e configurar regras de CORS
* Copiar facilmente as chaves primária e secundária para contas de armazenamento
* Verificações de MD5 no carregamento e download de integridade de dados e consistência
* Pesquise seus contêineres de blob, tabelas, filas, compartilhamentos de arquivos ou contas de armazenamento da caixa de pesquisa
* Você pode agora pin usados com mais frequência serviços para o acesso rápido para facilitar a navegação
* Agora você pode abrir vários editores em guias diferentes. Clique para abrir uma guia temporária; Clique duas vezes para abrir uma guia permanente. Você também pode clicar na guia temporária para torná-lo uma guia permanente
* Aperfeiçoamentos de desempenho e estabilidade para carregamentos e downloads, especialmente para grandes arquivos em máquinas rápidas
* Nós são reintroduzir a pesquisa com escopo aprimorada e adicionado o conceito de escopo. Digite o caminho para um nó por meio do ícone de foco, clique com botão direito-> "Pesquisa de aqui", ou manualmente para definir o escopo desse nó. Depois que o escopo, você pode adicionar um termo de pesquisa no final do caminho de pesquisa detalhada a partir do nó
* Adicionamos vários temas: luz (padrão), escuro, preto em alto contraste e branco em alto contraste.
* Vá para editar-> temas para alterar sua preferência de temas
* No Linux, um sistema operacional de 64 bits agora é necessário
* Atualizamos nosso logotipo!
#### <a name="blobs"></a>Blobs
* Exibir blobs e navegar pelos diretórios
* Carregar, baixar, excluir e copiar blobs e pastas
* Abrir e exibir os blobs de texto e imagem de conteúdo
* Exibir e editar propriedades e metadados de blob
* Procure blobs pelo prefixo
* Criar e quebrar concessões para blobs e contêineres de blob
* Arraste ' n soltar arquivos para carregar
* Renomear pastas e blobs
* Pastas "virtuais" vazias agora podem ser criadas em contêineres de blob
* Você pode modificar as propriedades de Blob e arquivo
#### <a name="tables"></a>Tabelas
* Exibir e consultar entidades com o ODATA ou usar o construtor de consultas para criar consultas complexas
* Adicionar, editar e excluir entidades
* Importar e exportar o conteúdo da tabela no formato CSV (inclusive exportar resultados de consulta)
* Personalizar a ordem das colunas
* Capacidade de salvar consultas
#### <a name="queues"></a>Filas de
* Inspecionar 32 mensagens mais recentes
* Adicionar, remover, exibir mensagens
* Limpar fila
* Fizemos isso para que você decida se deseja codificação ou decodificação de mensagens da fila
#### <a name="file-shares"></a>Compartilhamentos de arquivos
* Exibir arquivos e navegue pelos diretórios
* Carregar, baixar, excluir e copiar arquivos e diretórios
* Exibir propriedades de arquivo
* Renomear arquivos e diretórios

### <a name="bug-fixes"></a>Correções de bug
* Corrigido: Tela problemas congelamento
* Corrigido: Segurança aprimorada

### <a name="known-issues"></a>Problemas conhecidos
* Pesquisa alças pesquisar em nós aproximadamente 50.000 - depois disso, o desempenho pode ser afetados macOS instalação pode exigir permissões elevadas
* Painel de configurações de conta pode mostrar que você precisa reinserir as credenciais para filtrar as assinaturas
* Renomear blobs (individualmente ou dentro de um contêiner de blob renomeado) não preserva os instantâneos. Todas as outras propriedades e metadados de blobs, arquivos e entidades são preservadas durante uma renomeação
* O Azure Stack atualmente não suporta arquivos, então tentar expandir o **arquivos** nó resulta em um erro
* Instalação do Linux 14.04 precisa gcc versão atualizada ou atualizado. As etapas a seguir ilustram como atualizar:

```
sudo add-apt-repository ppa:ubuntu-toolchain-r/test
sudo apt-get update
sudo apt-get upgrade
sudo apt-get dist-upgrade
```

### <a name="previous-versions"></a>Versões anteriores
#### <a name="october-2016-release-version-085"></a>De 2016 de outubro (versão 0.8.5)
* [Baixar para Windows](https://go.microsoft.com/fwlink/?LinkId=809306)
* [Baixar para Mac](https://go.microsoft.com/fwlink/?LinkId=809307)
* [Baixar para Linux](https://go.microsoft.com/fwlink/?LinkId=809308)



<!--HONumber=Jan17_HO3-->


