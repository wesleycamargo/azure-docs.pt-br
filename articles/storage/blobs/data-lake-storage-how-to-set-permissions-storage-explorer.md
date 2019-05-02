---
title: Definir permissões para o Data Lake Storage Gen2 com o Gerenciador de Armazenamento do Azure
description: Nestas instruções, você aprenderá a definir permissões com o Gerenciador de Armazenamento do Azure em arquivos e diretórios dentro da conta de armazenamento com capacidade para Azure Data Lake Storage Gen2.
services: storage
author: normesta
ms.custom: mvc
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 12/11/2018
ms.author: normesta
ms.openlocfilehash: 5f0211765c96cad668abaad7d42da87ec88298c3
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939319"
---
# <a name="set-file-and-directory-level-permissions-using-azure-storage-explorer-with-azure-data-lake-storage-gen2"></a>Definir permissões no nível do arquivo e do diretório usando o Gerenciador de Armazenamento do Azure com o Azure Data Lake Storage Gen2

Arquivos armazenados no Azure Data Lake Storage Gen2 são compatíveis com permissões refinadas e gerenciamento de ACL (lista de controle de acesso). Juntos, permissões refinadas e gerenciamento de ACL permitem que você gerencie o acesso aos seus dados em um nível muito granular.

Neste artigo, você aprenderá a usar o Gerenciador de Armazenamento do Azure para:

> [!div class="checklist"]
> * Definir permissões em nível de arquivo
> * Definir permissões no nível do diretório
> * Adicionar usuários ou grupos a uma lista de controle de acesso

## <a name="prerequisites"></a>Pré-requisitos

Para melhor descrevem o processo, solicitamos que você conclua nosso [Início rápido do Gerenciador de Armazenamento do Azure](data-lake-storage-Explorer.md). Isso garante que sua conta de armazenamento estará no estado mais apropriado (sistema de arquivos criado e os dados carregados para ele).

## <a name="managing-access"></a>Gerenciando o acesso

Você pode definir permissões na raiz do seu sistema de arquivos. Para fazer isso, você deve estar conectado ao Gerenciador de Armazenamento do Azure com sua conta individual com direitos para fazê-lo (se comparado com uma cadeia de caracteres de conexão). Clique com o botão direito do mouse no sistema de arquivos e selecione **Gerenciar Permissões** abrindo a caixa de diálogo **Gerenciar Permissão**.

![Gerenciador de Armazenamento do Microsoft Azure – gerenciar o acesso ao diretório](media/storage-quickstart-blobs-storage-Explorer/manageperms.png)

A caixa de diálogo **Gerenciar Permissão** permite gerenciar permissões para o proprietário e o grupo de proprietários. Ele também permite adicionar novos usuários e grupos à lista de controle de acesso para os quais você então pode gerenciar permissões.

Para adicionar um novo usuário ou grupo à lista de controle de acesso, selecione o campo **Adicionar usuário ou grupo**.

Insira a entrada correspondente do AAD (Azure Active Directory) que deseja adicionar à lista e, em seguida, selecione **Adicionar**.

O usuário ou grupo agora aparecerão no campo **Usuários e grupos:**, permitindo que você comece a gerenciar suas permissões.

> [!NOTE]
> É uma melhor prática e recomendamos criar um grupo de segurança no AAD e manter as permissões no grupo, em vez de usuários individuais. Para obter detalhes sobre essa recomendação, bem como outras melhores práticas, confira [melhores práticas para o Data Lake Storage Gen2](data-lake-storage-best-practices.md).

Há duas categorias de permissões que você pode atribuir: ACLs de acesso e ACLs padrão.

* **Acesso**: ACLs de acesso controlam o acesso a um objeto. Arquivos e diretórios têm ambos ACLs de acesso.

* **Padrão**: Um modelo de ACLs associadas a um diretório que determina as ACLs de acesso para todos os itens filhos criados nesse diretório. Arquivos não têm ACLs padrão.

Dentro dessas categorias, há três permissões que você pode atribuir em arquivos ou diretórios: **Ler**, **Gravar** e **Executar**.

>[!NOTE]
> Fazer seleções aqui não definirá permissões em nenhum item existente no momento dentro do diretório. Você deverá ir para cada item individual e definir as permissões manualmente se o arquivo já existir.

Você pode gerenciar permissões em diretórios individuais, bem como arquivos individuais, que são o que permite o controle de acesso refinado. O processo para gerenciar permissões para diretórios e arquivos é o mesmo descrito acima. Clique com o botão direito do mouse no arquivo ou diretório cujas permissões você deseja gerenciar no e siga o mesmo processo.

## <a name="next-steps"></a>Próximas etapas

Neste passo a passo, você aprendeu a definir permissões em arquivos e diretórios usando o **Gerenciador de Armazenamento do Azure**. Para saber mais sobre ACLs, incluindo ACLs padrão, ACLs de acesso, seu comportamento e suas permissões correspondentes, siga para nosso artigo conceitual sobre o assunto.

> [!div class="nextstepaction"]
> [Controle de acesso no Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
