---
title: Criptografia no Azure Data Lake Store | Microsoft Docs
description: "Entenda como a rotação de chaves e criptografia funciona no Azure Data Lake Store"
services: data-lake-store
documentationcenter: 
author: yagupta
manager: 
editor: 
ms.assetid: 
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 4/14/2017
ms.author: yagupta
ms.translationtype: Human Translation
ms.sourcegitcommit: db034a8151495fbb431f3f6969c08cb3677daa3e
ms.openlocfilehash: 9bd9996a4a22b2f57510b6f3b6625e22b3183b1c
ms.contentlocale: pt-br
ms.lasthandoff: 04/29/2017

---

# <a name="encryption-of-data-in-azure-data-lake-store"></a>Criptografia de dados no Azure Data Lake Store

## <a name="overview-of-encryption-in-azure-data-lake-store"></a>Visão geral de criptografia no Azure Data Lake Store

A criptografia no Azure Data Lake Store (ADLS) fornece a capacidade de proteger seus dados, implementar políticas de segurança da empresa e atender aos requisitos de conformidade normativa. Este artigo fornece uma visão geral do projeto e aborda aspectos técnicos, como o Data Lake Store implementa a criptografia de dados.

ADLS oferece suporte por padrão, de modo transparente, a criptografia de dados em repouso. Aqui está o que esses termos significam um pouco mais detalhadamente:

* Em por padrão: ao criar uma nova conta do Azure Data Lake Store, a configuração-padrão permite a criptografia. Depois disso, os dados armazenados no Data Lake Store são always encrypted antes de armazenar em mídia persistente. Esse é o comportamento para todos os dados e não pode ser alterado depois que uma conta é criada.
* Transparente: o ADLS criptografa automaticamente os dados antes da persistência e descriptografa os dados antes da recuperação. A criptografia é configurada e gerenciada no Data Lake Store por um administrador. Nenhuma alteração é feita às APIs de acesso a dados, assim nenhuma alteração é necessária nos aplicativos e serviços que interagem com o Data Lake Store devido à criptografia.

Dados em trânsito (também conhecido como dados em movimento) também são Always Encrypted no Data Lake Store. Além de criptografar os dados antes de armazenar a mídia persistente, os dados são sempre protegidos em trânsito ou em movimento usando HTTPS (HTTP sobre SSL). HTTPS é o único protocolo com suporte para as interfaces REST do Data Lake Store.

![A figura 1](./media/data-lake-store-encryption/fig1.png)


## <a name="setting-up-encryption-with-azure-data-lake-store"></a>Configuração da criptografia com o Azure Data Lake Store

A criptografia para o Azure Data Lake Store é configurada durante a criação da conta, ela está sempre habilitada por padrão. Os clientes têm a opção de gerenciar as chaves ou permitir que o Azure Data Lake Store (padrão) gerencie as chaves para eles.

Para saber como configurar a criptografia do Azure Data Lake Store, confira – [Introdução](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)

## <a name="under-the-hood--how-encryption-works-in-azure-data-lake-store"></a>Nos bastidores – como funciona a criptografia no Azure Data Lake Store

### <a name="master-encryption-keys"></a>Chaves de criptografia mestras

O Azure Data Lake Store oferece dois modos de gerenciamento de chaves de criptografia mestras (MEKs). O uso de chaves de criptografia mestras é explicado com mais detalhes abaixo. Agora, suponha que a chave de criptografia mestra é a chave de nível superior. O acesso à chave de criptografia mestra é necessário para descriptografar os dados armazenados no Data Lake Store.

Os dois modos para gerenciar a chave de criptografia mestra são da seguinte maneira:

1.    Chaves gerenciadas de serviço
2.    Chaves gerenciadas pelo cliente

Em ambos os modos, a chave de criptografia mestra é protegida, armazenando-a no Azure Key Vault. O Azure Key Vault é um serviço altamente seguro, totalmente gerenciado no Azure que pode ser usado para proteger chaves criptográficas. Você pode ler mais sobre o Azure Key Vault [aqui](https://azure.microsoft.com/services/key-vault)

Aqui está uma breve comparação dos recursos fornecidos por dois modos de gerenciar as MEKs.

|  | Chaves gerenciadas de serviço | Chaves gerenciadas do cliente |
| --- | --- | --- |
|Como os dados são armazenados?|Always encrypted antes de serem armazenados|Always encrypted antes de serem armazenados|
|Qual é a chave de criptografia mestra armazenada?|Cofre da Chave do Azure|Cofre da Chave do Azure|
|Qualquer chave de criptografia é armazenada de modo transparente fora do Azure Key Vault|Não|Não|
|A MEK pode recuperada pelo Azure Key Vault?|Não. Uma vez armazenado no cofre de chaves, só pode ser usado para criptografia e descriptografia.|Não. Uma vez armazenado no cofre de chaves, só pode ser usado para criptografia e descriptografia.|
|Quem possui o Azure Key Vault e a MEK?|Serviço do Azure Data Lake Store.|O cliente possui o Azure Key Vault, que pertence a sua própria assinatura do Azure. A MEK no cofre de chaves pode ser um software ou hardware (HSM) gerenciado.|
|O cliente pode revogar o acesso para a MEK para o serviço do Azure Data Lake Store?|Não|Sim. Eles podem gerenciar listas de controle de acesso no Azure Key Vault e remover entradas de controle de acesso para a identidade do serviço para o serviço do Azure Data Lake Store.|
|O cliente pode excluir permanentemente a MEK?|Não|Sim. Se o cliente exclui a MEK do Azure Key Vault, os dados na conta do ADLS não podem ser descriptografados por qualquer pessoa, incluindo o serviço do Azure Data Lake Store. <br><br> Se um backup da MEK foi feito explicitamente pelo cliente antes da exclusão do Azure Key Vault, ela poderá ser restaurada e os dados poderão ser recuperados. No entanto, se um backup da MEK não foi realizado pelo cliente antes da exclusão do Azure Key Vault, os dados na conta do ADLS nunca poderão ser descriptografados.|


Além da diferença de nível superior, de quem gerencia a MEK e o Key Vault no qual ela reside, o restante do projeto é o mesmo para ambos os modos.

Há alguns aspectos importantes para lembrar relacionados ao escolher o modo para as chaves de criptografia mestras.

1.    Você pode escolher usar chaves gerenciadas pelo cliente ou chaves gerenciadas pelo ADLS quando você provisionar uma conta ADLS
2.    Depois que uma conta ADLS é configurada, o modo não pode ser alterado

### <a name="encryption-and-decryption-of-data"></a>Criptografia e descriptografia de dados

Há três tipos de chaves que são usadas no projeto da criptografia de dados para o Azure Data Lake. A tabela a seguir resume como eles participam:

| Chave                   | Abreviação | Associadas com | Local de armazenamento                             | Tipo       | Observações                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| Chave de criptografia mestra | MEK          | Uma conta do ADLS | Cofre da Chave do Azure                              | Assimétrica | Pode ser gerenciado pelo cliente ou gerenciado pelo ADLS                                                              |
| Chave de criptografia de dados   | DEK          | Uma conta do ADLS | Armazenamento persistente – gerenciado pelo serviço do ADLS | Simétrica  | O DEK é criptografado pela MEK e o DEK criptografado é o que é armazenado em mídia persistente do serviço |
| Chave de criptografia de bloco  | BEK          | Um bloco de dados | Nenhum                                         | Simétrica  | O BEK é derivado do DEK e do bloco de dados                                                      |

O diagrama a seguir ilustra esses conceitos:

![Figura2](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>Pseudoalgoritmo quando um arquivo será descriptografado:
1.    Verifique se o DEK para a conta do ADLS é armazenado em cache e está pronto para uso.
    * Caso contrário, leia o DEK criptografado de armazenamento persistente e envie-o para o Azure Key Vault a ser descriptografado. Coloque o DEK descriptografado na memória de cache e ele estará pronto para uso.
2.    Para cada bloco de dados no arquivo
    * Leia o bloco de dados criptografado do armazenamento persistente
    * Gerar o BEK do DEK e o bloco de dados criptografado
    * Use o BEK para descriptografar dados
#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>Pseudoalgoritmo quando um bloco de dados será descriptografado:
1.    Verifique se o DEK para a conta do ADLS é armazenado em cache e está pronto para uso.
    * Caso contrário, leia o DEK criptografado de armazenamento persistente e envie-o para o Azure Key Vault a ser descriptografado. Coloque o DEK descriptografado na memória de cache e ele estará pronto para uso.
2.    Gere um BEK exclusivo para o bloco de dados do DEK.
3.    Criptografe o bloco de dados com o BEK usando a criptografia AES-256.
4.    Armazenar o bloco de dados criptografados no armazenamento persistente

> [!NOTE] 
> Por motivos de desempenho, a chave de criptografia de dados (DEK) não criptografada é armazenada de forma efêmera em cache na memória por um curto período e apagada imediatamente após o término desta duração. Na mídia persistente, é sempre armazenado criptografado pela chave de criptografia mestra (MEK).

## <a name="key-rotation"></a>Rotação de chaves

O Azure Data Lake Store permite a rotação de chave de criptografia mestra (MEK) ao usar chaves gerenciadas pelo cliente. Para saber como configurar uma conta ADLS com chaves gerenciadas pelo cliente, confira a página [Introdução](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

### <a name="pre-requisites"></a>Pré-requisitos

Ao configurar a conta do Azure Data Lake, os clientes decidiram usar suas próprias chaves. Essa opção não pode ser alterada depois que a conta foi criada. Se você usar as opções padrão para criptografia, os dados sempre serão criptografados usando chaves gerenciadas pelo Azure Data Lake, nessa opção o cliente não tem a capacidade de rotação de chaves, uma vez que elas são gerenciadas pelo Azure Data Lake. As etapas a seguir pressupõem que você está usando as chaves gerenciadas pelo cliente (suas próprias chaves escolhidas do seu cofre de chaves).

### <a name="how-to-rotate-the-key-mek-in-azure-data-lake-store"></a>Como alterar a chave (MEK) no Azure Data Lake Store

1. Entre no novo [Portal do Azure](https://portal.azure.com/)
2. Navegue até o Key Vault que armazena as chaves associadas à sua conta do Azure Data Lake Store e selecione as Chaves.

    ![simétricas](./media/data-lake-store-encryption/keyvault.png)

3.    Selecione a chave associada à sua conta do Azure Data Lake Store e criar uma versão nova dessa chave.
  
   Neste ponto, o Azure Data Lake só oferece suporte para a rotação de chaves para uma versão nova de uma chave, não há suporte para girar para uma chave diferente

   ![newversion](./media/data-lake-store-encryption/keynewversion.png)

4.    Navegue até a conta de armazenamento do Azure Data Lake Store e selecione Criptografia

    ![newversion](./media/data-lake-store-encryption/select-encryption.png)

5.    Você verá uma observação informando que uma versão nova da chave está disponível e um botão para alterar a chave para essa versão nova. Clique em alterar chave para atualizar a chave para a versão nova.

    ![concluído](./media/data-lake-store-encryption/rotatekey.png)

6. Esta operação deve levar menos de 2 minutos e não há nenhum tempo de inatividade esperado devido à rotação de chaves. Quando a operação for concluída, a nova versão da chave estará em uso.

