---
title: Sobre Chaves, Segredos e Certificados
description: Visão geral da interface REST e detalhes de desenvolvedor KV
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: abd1b743-1d58-413f-afc1-d08ebf93828a
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: 2c7dd89d9c2e5d50f2533101499a6e50e52047b3
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="about-keys-secrets-and-certificates"></a>Sobre Chaves, Segredos e Certificados
O Cofre da Chave do Azure permite aos usuários armazenar e usar chaves de criptografia no ambiente do Microsoft Azure. O Cofre de chaves dá suporte a vários tipos de chave e algoritmos e permite o uso de módulos de segurança de Hardware (HSM) para chaves de alto valor. Além disso, o Cofre de Chaves permite que os usuários armazenem segredos de forma segura. Os segredos são objetos de octetos de tamanho limitado sem semântica específica. O Cofre de Chaves também oferece suporte a certificados, que são criados sobre chaves e segredos e adicionam um recurso de renovação automática.

Para obter mais informações gerais sobre o Cofre da Chave do Azure, consulte [O que é o Cofre da Chave do Azure?](/azure/key-vault/key-vault-whatis)

**Detalhes gerais do Cofre de Chaves**

-   [Padrões de suporte](about-keys-secrets-and-certificates.md#BKMK_Standards)
-   [Tipos de dados](about-keys-secrets-and-certificates.md#BKMK_DataTypes)  
-   [Objetos, identificadores e controle de versão](about-keys-secrets-and-certificates.md#BKMK_ObjId)  

**Sobre as chaves**

-   [Chaves e tipos de chave](about-keys-secrets-and-certificates.md#BKMK_KeyTypes)  
-   [Algoritmos RSA](about-keys-secrets-and-certificates.md#BKMK_RSAAlgorithms)  
-   [Algoritmos RSA-HSM](about-keys-secrets-and-certificates.md#BKMK_RSA-HSMAlgorithms)  
-   [Proteção criptográfica](about-keys-secrets-and-certificates.md#BKMK_Cryptographic)
-   [Operações de chave](about-keys-secrets-and-certificates.md#BKMK_KeyOperations)  
-   [Atributos de chave](about-keys-secrets-and-certificates.md#BKMK_KeyAttributes)  
-   [Marcas de chave](about-keys-secrets-and-certificates.md#BKMK_Keytags)  

**Sobre segredos** 

-   [Como trabalhar com os segredos](about-keys-secrets-and-certificates.md#BKMK_WorkingWithSecrets)  
-   [Atributos de segredos](about-keys-secrets-and-certificates.md#BKMK_SecretAttrs)  
-   [Marcas de segredos](about-keys-secrets-and-certificates.md#BKMK_SecretTags)  
-   [Controle de acesso a segredo](about-keys-secrets-and-certificates.md#BKMK_SecretAccessControl)  

**Sobre certificados**

-   [Composição de um certificado](#BKMK_CompositionOfCertificate)  
-   [Marcas e atributos de certificado](#BKMK_CertificateAttributesAndTags)  
-   [Política do certificado](#BKMK_CertificatePolicy)  
-   [Emissor do certificado](#BKMK_CertificateIssuer)  
-   [Contatos de certificado](#BKMK_CertificateContacts)  
-   [Controle de acesso de certificado](#BKMK_CertificateAccessControl)  

--

## <a name="key-vault-general-details"></a>Detalhes gerais do Cofre de Chaves

As seções a seguir oferecem informações gerais aplicável entre a implementação do serviço de Cofre de Chaves do Azure.

###  <a name="BKMK_Standards"></a> Padrões de suporte

O JavaScript Object Notation (JSON) e especificações de criptografia e autenticação do objeto de JavaScript (JOSE) são informações importantes.  

-   [Chave da Web JSON (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [Criptografia de Web JSON (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [Algoritmos da Web JSON (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [Assinatura da Web JSON (JWS)](http://tools.ietf.org/html/draft-ietf-jose-json-web-signature)  

### <a name="BKMK_DataTypes"></a> Data Types

Consulte as [especificações JOSE](#BKMK_Standards) para tipos de dados relevantes para as chaves, criptografia e assinatura.  

-   **algoritmo** - um algoritmo com suporte para uma operação de chave, por exemplo, RSA1_5  
-   **valor de texto cifrado** - octetos de texto cifrado, codificado usando Base64URL  
-   **valor de Digest** - a saída de um algoritmo de hash, codificado usando Base64URL  
-   **tipo de chave** - um dos tipos de chave com suporte, por exemplo, RSA.  
-   **valor de texto sem formatação** - octetos de texto sem formatação, codificado usando Base64URL  
-   **valor de assinatura** - a saída de um algoritmo de assinatura, codificado usando Base64URL  
-   **base64URL** - um Base64URL [RFC4648] codificado valor binário  
-   **boolean** - seja true ou false  
-   **Identity** - uma identidade do Azure Active Directory (AAD).  
-   **IntDate** - um valor decimal de JSON que representa o número de segundos desde 1970-01-01T0:0:0Z UTC até a data/hora de UTC especificada. Consulte [RFC3339] para obter detalhes sobre data/hora em geral e o UTC em particular.  

###  <a name="BKMK_ObjId"></a> Objetos, identificadores e controle de versão

Objetos armazenados no Cofre de Chaves do Azure mantêm versões sempre que uma nova instância de um objeto é criada, e cada versão tem um identificador exclusivo e uma URL. Quando um objeto é criado pela primeira vez, ele recebe um identificador de versão exclusivo e é marcado como a versão atual do objeto. Criação de uma nova instância com o mesmo nome de objeto fornece ao novo objeto um identificador de versão exclusivo e faz com que ele se torne a versão atual.  

Objetos no Cofre de Chaves do Azure podem ser endereçados usando o identificador atual ou um identificador específico da versão. Por exemplo, dada uma chave com o nome "MasterKey", executar operações com o identificador atual faz com que o sistema use a versão mais recente disponível. Executar operações com o identificador de versão específico faz com que o sistema use essa versão específica do objeto.  

Os objetos são identificados exclusivamente no Cofre de Chaves do Azure usando uma URL que não há dois objetos no sistema, independentemente da localização geográfica, têm a mesma URL. A URL completa para um objeto é chamada de identificador de objeto e consiste em um prefixo que identifica o Cofre de Chaves, o tipo de objeto, um nome do objeto fornecido pelo usuário e uma versão do objeto. O nome do objeto é imutável e diferencia maiusculas de minúsculas. Identificadores que não incluem a versão do objeto são chamados de identificadores de base.  

Para mais informações, consulte [Autenticação, solicitações e respostas](authentication-requests-and-responses.md)

Um identificador de objeto tem o seguinte formato geral:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Em que:  

|||  
|-|-|  
|`keyvault-name`|O nome de um cofre de chaves no serviço do Microsoft Azure Key Vault.<br /><br /> Nomes de Cofre de Chaves são selecionados pelo usuário e são globalmente exclusivos.<br /><br /> O nome do Cofre de Chaves deve ser uma sequência de 3 a 24 caracteres que contenha somente 0 a 9, a a z, A a Z, e -.|  
|`object-type`|O tipo de objeto, "chaves" ou "segredos".|  
|`object-name`|Um `object-name` é um nome fornecido por usuário e deve ser exclusivo em um Cofre de Chaves. O nome deve ser uma sequência de 1 a 127 caracteres que contenha somente 0 a 9, a a z, A a Z e -.|  
|`object-version`|Um `object-version` é um identificador de cadeia de caracteres de 32 caracteres gerado pelo sistema, que é opcionalmente usado para direcionar uma versão exclusiva de um objeto.|  

## <a name="key-vault-keys"></a>Chaves do Cofre de Chaves

###  <a name="BKMK_KeyTypes"></a> Chaves e tipos de chave

As chaves de criptografia no Cofre de Chaves do Azure são representadas como objetos de chave da Web JSON [JWK]. As especificações JWK/JWA base também são estendidas para permitir tipos de chave exclusivos para a implementação do Cofre de Chaves do Azure, por exemplo, a importação de chaves no Cofre de Chaves do Azure usando o empacotamento específico (Thales) de fornecedor do HSM para habilitar um transporte seguro de chaves, que só pode ser usados nos HSMs do Cofre de Chaves do Azure.  

A versão inicial do Cofre de Chaves do Azure oferece suporte somente a chaves RSA. As versões futuras podem dar suporte a outros tipos de chave, como curva elíptica e simétrica.  

-   **RSA**: uma chave RSA de 2048 bits. Esta é uma chave "soft", que é processada no software pelo Cofre de Chaves, mas é armazenada criptografada em repouso usando uma chave do sistema que está em um HSM. Os clientes podem importar uma chave RSA existente ou solicitar que o Cofre de Chaves do Azure gere uma.  
-   **RSA-HSM**: uma chave RSA que é processada em um HSM. As chaves RSA-HSM são protegidas em um dos mundos de segurança HSM do Cofre de Chaves do Azure (há um mundo de segurança por localização geográfica para manter o isolamento). Os clientes podem importar uma chave RSA, no formato soft ou por meio da exportação de um dispositivo do HSM compatível, ou solicitar que o Cofre de chaves do Azure gere uma. Esse tipo de chave adiciona o atributo T para o JWK obter para transportar o material de chave do HSM.  

     Para obter mais informações sobre fronteiras geográficas, consulte [Microsoft Azure Trust Center](https://azure.microsoft.com/en-us/support/trust-center/privacy/)  

###  <a name="BKMK_RSAAlgorithms"></a> Algoritmos RSA  
 Os seguintes identificadores de algoritmo são compatíveis com chaves RSA no Cofre de Chaves do Azure.  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, CRIPTOGRAFAR/DESCRIPTOGRAFAR

-   **RSA1_5** - criptografia de chave RSAES-PKCS1-V1_5 [RFC3447]  
-   **RSA-OAEP** - RSAES usando Preenchimento de Criptografia Assimétrica Ideal (OAEP) [RFC3447], com os parâmetros padrão especificados por RFC 3447 na seção 2.1. Esses parâmetros padrão estão usando uma função de hash de SHA-1 e uma função de geração de máscara de MGF1 com SHA-1.  

#### <a name="signverify"></a>SIGN/VERIFY

-   **RS256** - RSASSA-PKCS-v1_5 usando SHA-256. O valor do resumo fornecido pelo aplicativo deve ser calculado usando SHA-256 e deve ter 32 bytes de comprimento.  
-   **RS384** - RSASSA-PKCS-v1_5 usando SHA-384. O valor do resumo fornecido pelo aplicativo deve ser calculado usando SHA-384 e deve ter 48 bytes de comprimento.  
-   **RS512** - RSASSA-PKCS-v1_5 usando SHA-512. O valor do resumo fornecido pelo aplicativo deve ser calculado usando SHA-512 e deve ter 64 bytes de comprimento.  
-   **RSNULL** - consulte [RFC2437], um caso de uso especializado para permitir determinados cenários TLS.  

###  <a name="BKMK_RSA-HSMAlgorithms"></a> Algoritmos RSA-HSM  
Os seguintes identificadores de algoritmo são compatíveis com chaves RSA-HSM no Cofre de Chaves do Azure.  

### <a name="BKMK_Cryptographic"></a> Proteção criptográfica

Os módulos de criptografia usado pelo Cofre de Chaves do Azure, se o HSM ou software, são validados do FIPS. Você não precisa fazer nada especial para executar no modo FIPS. Se você **criar** ou **importar** chaves como protegida por HSM, elas são garantidas de serem processadas dentro dos HSMs validados para FIPS 140-2 nível 2 ou superior. Se você **criar** ou **importar** chaves como protegida por software, elas são processadas dentro de módulos criptográficos validados para FIPS 140-2 nível 1 ou superior. Para obter mais informações, confira [Chaves e tipos de chaves](about-keys-secrets-and-certificates.md#BKMK_KeyTypes).

#### <a name="wrapunwrap-encryptdecrypt"></a>WRAP/UNWRAP, CRIPTOGRAFAR/DESCRIPTOGRAFAR

-   **RSA1_5** - criptografia de chave RSAES-PKCS1-V1_5 [RFC3447]  
-   **RSA-OAEP** - RSAES usando Preenchimento de Criptografia Assimétrica Ideal (OAEP) [RFC3447], com os parâmetros padrão especificados por RFC 3447 na seção 2.1. Esses parâmetros padrão estão usando uma função de hash de SHA-1 e uma função de geração de máscara de MGF1 com SHA-1.  

 #### <a name="signverify"></a>SIGN/VERIFY  

-   **RS256** - RSASSA-PKCS-v1_5 usando SHA-256. O valor do resumo fornecido pelo aplicativo deve ser calculado usando SHA-256 e deve ter 32 bytes de comprimento.  
-   **RS384** - RSASSA-PKCS-v1_5 usando SHA-384. O valor do resumo fornecido pelo aplicativo deve ser calculado usando SHA-384 e deve ter 48 bytes de comprimento.  
-   **RS512** - RSASSA-PKCS-v1_5 usando SHA-512. O valor do resumo fornecido pelo aplicativo deve ser calculado usando SHA-512 e deve ter 64 bytes de comprimento.  
-   RSNULL: consulte [RFC2437], um caso de uso especializado para permitir determinados cenários TLS.  

###  <a name="BKMK_KeyOperations"></a> Operações de chave

O Cofre de Chaves do Azure suporta as seguintes operações em objetos de chave:  

-   **Criar**: permite que um cliente crie uma chave no Cofre de Chaves do Azure. O valor da chave é gerado pelo Cofre de Chaves do Azure e armazenado e não é liberado para o cliente. Chaves assimétricas (e no futuro, Simétricas e Curvas Elípticas) podem ser criadas no Cofre de Chaves do Azure.  
-   **Importar**: permite que um cliente importe uma chave existente para o Cofre de Chaves do Azure. Assimétricas (e no futuro, simétricas e Curvas Elípticas) chaves podem ser importadas para o Cofre de Chaves do Azure usando um número de métodos diferentes de empacotamento dentro de uma construção JWK.  
-   **Atualização**: permite que um cliente com permissões suficientes modifique os metadados (atributos de chave) associados com uma chave armazenada anteriormente no Cofre de Chaves do Azure.  
-   **Excluir**: permite que um cliente com permissões suficientes exclua uma chave do Cofre de Chaves do Azure.  
-   **Listar**: permite que um cliente liste todas as chaves em um determinado Cofre de Chaves do Azure.  
-   **Listar versões**: permite que um cliente liste todas as versões de uma determinada chave em um determinado Cofre de Chaves do Azure.  
-   **Obter**: permite que um cliente recupere as partes públicas de uma determinada chave em um cofre de Chaves do Azure.  
-   **Backup**: exporta uma chave de forma protegida.  
-   **Restaurar**: importa uma chave de backup anterior.  

Para obter mais informações, consulte [Operações de chave](/rest/api/keyvault/key-operations.md).  

Quando uma chave tiver sido criada no Cofre de Chaves do Azure, as seguintes operações criptográficas podem ser executadas usando a chave:  

-   **Assinar e verificar**: estritamente, essa operação é "Assinar hash" ou "Verificar hash" como o Cofre de Chaves do Azure não oferece suporte a hash de conteúdo como parte da criação da assinatura. Aplicativos devem fazer hash dos dados para se conectarem localmente e, em seguida, solicitar assinar o hash do Cofre de Chaves do Azure. A verificação de hashes assinados tem suporte como uma operação de conveniência para aplicativos que podem não ter acesso ao material de chave [pública]; é recomendável que, para melhor desempenho do aplicativo, verifique se as operações são executadas localmente.  
-   **Criptografia / encapsulamento de chave**: uma chave armazenada no Cofre de Chaves do Azure pode ser usada para proteger outra chave, normalmente uma chave de criptografia de conteúdo simétrico (CEK). Quando a chave no Cofre de Chaves do Azure é assimétrica, é usada a criptografia de chave, por exemplo, RSA-OAEP e as operações WRAPKEY/UNWRAPKEY são equivalentes a ENCRYPT/DECRYPT. Quando a chave no Cofre de Chaves do Azure for simétrica, é usado o encapsulamento de chave; por exemplo, AES-KW. A operação WRAPKEY tem suporte como uma conveniência para aplicativos que podem não ter acesso ao material de chave [pública]; é recomendável que, para melhor desempenho do aplicativo, as operações WRAPKEY sejam executadas localmente.  
-   **Criptografar e descriptografar**: uma chave armazenada no Cofre de Chaves do Azure pode ser usada para criptografar ou descriptografar um único bloco de dados, o tamanho deste é determinado pelo tipo de chave e algoritmo de criptografia selecionado. A operação Criptografar é fornecida para conveniência para aplicativos que podem não ter acesso ao material de chave [pública]; é recomendável que, para melhor desempenho do aplicativo, as operações de criptografia sejam executadas localmente.  

Embora WRAPKEY/UNWRAPKEY usando chaves assimétricas possa parecer supérfluo já que a operação é equivalente a ENCRYPT/DECRYPT, o uso das operações distintas é considerado importante para fornecer semântica e separação de autorização dessas operações e consistência quando outros tipos de chave têm suporte pelo serviço.  

O Cofre de Chaves do Azure não oferece suporte a operações de exportação: quando uma chave é fornecida no sistema não pode ser extraída ou que seu material de chave seja modificado.  No entanto, os usuários do Cofre de Chaves do Azure que precisem de sua chave para outros casos de uso, ou depois que ela tenha sido excluída no Cofre de Chaves do Azure, podem usar as operações de BACKUP e RESTAURAÇÃO para exportar/importar a chave de forma protegida. As chaves criadas pela operação de BACKUP não são utilizáveis fora do Cofre de Chaves do Azure. Como alternativa, a operação de IMPORTAR pode ser usada em várias instâncias do Cofre de chaves do Azure.  

Os usuários podem restringir qualquer uma das operações de criptografia que o Cofre de Chaves do Azure suporta em uma base por chave usando a propriedade key_ops do objeto JWK.  

Para obter mais informações sobre objetos JWK, consulte [Chave de Web JSON (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key).  

###  <a name="BKMK_KeyAttributes"></a> Atributos de chave

Além do material de chave, os seguintes atributos podem ser especificados. Em uma solicitação de JSON, a palavra-chave e as chaves, '{' '}', são necessárias, mesmo se não houver nenhum atributo especificado.  

- *habilitado*: booliano, opcional, o padrão é **true**. Especifica se a chave está habilitada e pode ser utilizada para operações de criptografia. O atributo *habilitado* é usado em conjunto com *nbf* e *exp*. Quando ocorre uma operação entre *nbf* e *exp*, ele só será permitido se *habilitado* estiver definido como **true**. As operações fora da janela *nbf* / *exp* não são automaticamente permitidas, exceto para determinados tipos de operação em [determinadas condições](about-keys-secrets-and-certificates.md#BKMK_key-date-time-ctrld-ops).
- *nbf*: IntDate, opcional, o padrão é agora. O atributo *nbf* (not before) identifica o tempo anterior que a chave NÃO DEVE ser usada para operações de criptografia, exceto para determinados tipos de operação em [determinadas condições](about-keys-secrets-and-certificates.md#BKMK_key-date-time-ctrld-ops). O processamento do atributo *nbf* requer que a data/hora atual DEVE ser posterior ou igual a data/hora não-anterior listada no atributo *nbf*. Cofre de Chaves do Azure PODE fornecer alguma reserva pequena, geralmente não mais do que alguns minutos, para compensar distorção do relógio. Seu valor DEVE ser um número que contenha um valor de IntDate.  
- *EXP*: IntDate, opcional, o padrão é “infinito”. O atributo *exp* (expiration time) identifica o tempo de expiração em que ou depois que a chave NÃO DEVE ser usada para operações de criptografia, exceto para determinados tipos de operação em [determinadas condições](about-keys-secrets-and-certificates.md#BKMK_key-date-time-ctrld-ops). O processamento do atributo *exp* requer que a data/hora atual DEVE ser anterior a data/hora de expiração listada no atributo *exp*. Cofre de Chaves do Azure PODE fornecer alguma reserva pequena, geralmente não mais do que alguns minutos, para compensar distorção do relógio. Seu valor DEVE ser um número que contenha um valor de IntDate.  

Há mais atributos somente leitura que são incluídos em qualquer resposta que inclui os atributos de chave:  

- *criado*: IntDate, opcional. O atributo *criado* indica quando esta versão da chave foi criada. Esse valor é nulo para chaves criadas antes da adição deste atributo. Seu valor DEVE ser um número que contenha um valor de IntDate.  
- *atualizado*: IntDate, opcional. O atributo *atualizado* indica quando esta versão da chave foi atualizada. Esse valor é nulo para chaves que foram atualizadas antes da adição deste atributo. Seu valor DEVE ser um número que contenha um valor de IntDate.  

Para obter mais informações sobre IntDate e outros tipos de dados, consulte [Tipos de dados](about-keys-secrets-and-certificates.md#BKMK_DataTypes)  

#### <a name="BKMK_key-date-time-ctrld-ops"></a> Operações de data e hora controladas

Chaves expiradas e ainda não válidas, aquelas que estão fora da janela *nbf* / *exp*, funcionarão para operações **descriptografar**, **unwrap** e **verificar** (não retornará 403, proibido). A lógica para usar o estado de não válido ainda é permitir que uma chave seja testada antes do uso de produção. A lógica para usar o estado expirado é permitir operações de recuperação de dados que foram criados quando a chave era válida. Além disso, você pode desabilitar o acesso a uma chave usando políticas de Cofre de Chaves, ou atualizando o atributo de chave *habilitado* para **false**.

Para obter mais informações sobre tipos de dados, consulte [Tipos de dados](about-keys-secrets-and-certificates.md#BKMK_DataTypes).

Para obter mais informações sobre outros atributos possíveis, consulte a [Chave de Web JSON (JWK)](http://tools.ietf.org/html/draft-ietf-jose-json-web-key).

### <a name="BKMK_Keytags"></a> Marcas de chave

Você pode especificar mais metadados específicos do aplicativo na forma de marcas. O Cofre de Chaves do Azure oferece suporte a até 15 marcas, cada um deles pode ter um nome de 256 caracteres e um valor de 256 caracteres.  

>[!Note]
>As marcas podem ser lidas por um chamador se ele tiver a permissão *listar* ou *obter* para esse tipo de objeto; chaves, segredos ou certificados.

###  <a name="BKMK_KeyAccessControl"></a> Controle de acesso a chave

O controle de acesso para chaves gerenciados pelo Cofre de Chaves é fornecido no nível de um Cofre de Chaves que atua como o contêiner de chaves. Há uma política de controle de acesso para chaves que é diferente da política de controle de acesso para segredos no mesmo Cofre de Chaves. Os usuários podem criar um ou mais cofres para armazenar chaves e são necessários para manter a segmentação e gerenciamento de chaves apropriados do cenário. O controle de acesso para chaves é independente do controle de acesso para segredos.  

As seguintes permissões podem ser concedidas, por usuário / serviço, na entrada de controle de acesso de chaves em um cofre. Essas permissões refletem com maior exatidão as operações permitidas em um objeto de chave:  

-   *criar*: criar novas chaves
-   *obter*: ler a parte pública de uma chave, além de seus atributos
-   *listar*: listar as chaves ou versões de uma chave armazenada em um cofre de chaves
-   *atualizar*: atualizar os atributos de uma chave
-   *excluir*: excluir o objeto de chave
-   *assinar*: usar a chave para assinar resumos
-   *verificar*: usar a chave para verificar resumos
-   *wrapKey*: usar a chave para proteger uma chave simétrica
-   *unwrapKey*: usar a chave para desproteger uma chave simétrica encapsulada
-   *criptografar*: usar a chave para proteger uma sequência arbitrária de bytes
-   *descriptografar*: usar a chave para desproteger uma sequência de bytes
-   *importar*: importar uma chave para um cofre de chaves
-   *backup*: fazer Backup de uma chave em um cofre de chaves
-   *restaurar*: restaurar um backup da chave em um cofre de chave
-   *todas*: todas as permissões

## <a name="key-vault-secrets"></a>Segredo do Cofre de Chaves 

###  <a name="BKMK_WorkingWithSecrets"></a> Trabalhando com os segredos

Os segredos no Cofre de Chaves do Azure são sequências de octeto com um tamanho máximo de 25 k bytes cada. O serviço de Cofre de Chaves do Azure não fornece nenhuma semântica para segredos; ele simplesmente aceita os dados, criptografa e armazena, retornando um identificador de secredo, "id", que pode ser usado para recuperar o segredo mais tarde.  

Para dados altamente confidenciais, os clientes devem considerar camadas adicionais de proteção para dados armazenados no Cofre de Chaves do Azure; por exemplo, criptografando previamente os dados usando uma chave de proteção separados.  

O Cofre de Chaves do Azure também oferece suporte a um campo contentType para segredos. Os clientes podem especificar o tipo de conteúdo, "contentType", de um segredo para ajudar a interpretar os dados de segredo quando são recuperados. O comprimento máximo deste campo é de 255 caracteres. Não existem valores predefinidos. O uso sugerido é como uma dica para interpretar os dados de secredos. Por exemplo, uma implementação pode armazenar senhas e certificados como segredos e usar esse campo para indicar qual. Não existem valores predefinidos.  

###  <a name="BKMK_SecretAttrs"></a> Atributos de segredos

Além dos dados de segredo, os seguintes atributos podem ser especificados:  

- *exp*: IntDate, opcional, o padrão é **infinito**. O atributo *exp* (tempo de expiração) identifica o tempo de expiração em que ou depois que os dados de segredo NÃO DEVEM ser recuperados, exceto em [determinadas situações](about-keys-secrets-and-certificates.md#BKMK_secret-date-time-ctrld-ops). O processamento do atributo *exp* requer que a data/hora atual DEVE ser anterior a data/hora de expiração listada no atributo *exp*. Cofre de Chaves do Azure PODE fornecer alguma reserva pequena, geralmente não mais do que alguns minutos, para compensar distorção do relógio. Seu valor DEVE ser um número que contenha um valor de IntDate.  
- *nbf*: IntDate, opcional, o padrão é **agora**. O atributo *nbf* (not before) identifica o tempo anterior que os dados de segredo NÃO DEVEM ser recuperados, exceto em [determinadas situações](about-keys-secrets-and-certificates.md#BKMK_secret-date-time-ctrld-ops). O processamento do atributo *nbf* requer que a data/hora atual DEVE ser posterior ou igual a data/hora não-anterior listada no atributo *nbf*. Cofre de Chaves do Azure PODE fornecer alguma reserva pequena, geralmente não mais do que alguns minutos, para compensar distorção do relógio. Seu valor DEVE ser um número que contenha um valor de IntDate.  
- *habilitado*: booliano, opcional, o padrão é **true**. Esse atributo especifica se os dados secretos podem ser recuperados. O atributo habilitado é usado em conjunto com *exp* quando ocorre uma operação entre exp, ele só será permitido se habilitado estiver definido como **true**. As operações fora da janela *nbf* e *exp* são automaticamente não permitidas, exceto em [determinadas situações](about-keys-secrets-and-certificates.md#BKMK_secret-date-time-ctrld-ops).  

Há mais atributos somente leitura que são incluídos em qualquer resposta que inclui os atributos de segredo:  

- *criado*: IntDate, opcional. O atributo criado indica quando esta versão do segredo foi criada. Esse valor é nulo para segredos criados antes da adição deste atributo. Seu valor deve ser um número que contenha um valor de IntDate.  
- *atualizado*: IntDate, opcional. O atributo atualizado indica quando esta versão do segredo foi atualizada. Esse valor é nulo para segredos que foram atualizados antes da adição deste atributo. Seu valor deve ser um número que contenha um valor de IntDate.

#### <a name="BKMK_secret-date-time-ctrld-ops"></a> Operações de data e hora controladas

Uma operação **obter** do segredo funcionará para segredos ainda não válidos e expirados, fora da janela *nbf* / *exp*. Chamar uma operação **obter** do segredo, para um segredo ainda não válido, pode ser usada para fins de teste. Recuperar (**obter**) um segredo expirado, pode ser usado para operações de recuperação.

Para obter mais informações sobre tipos de dados, consulte [Tipos de dados](about-keys-secrets-and-certificates.md#BKMK_DataTypes).  

###  <a name="BKMK_SecretAccessControl"></a> Controle de acesso a segredo

O Controle de Acesso para segredos gerenciados pelo Cofre de Chaves do Azure é fornecido no nível de um Cofre de Chaves que atua como o contêiner desses segredos. Há uma política de controle de acesso para segredos que é diferente da política de controle de acesso para chaves no mesmo Cofre de Chaves. Os usuários podem criar um ou mais cofres para armazenar segredos e são necessários para manter a segmentação e gerenciamento de segredos apropriados do cenário. O controle de acesso para segredos é independente do controle de acesso para chaves.  

As seguintes permissões podem ser usadas, por entidade de segurança, na entrada de controle de acesso segredos em um cofre e refletem com maior exatidão as operações permitidas em um objeto de segredo:  

-   *definir*: criar novos segredos  
-   *obter*: ler um segredo  
-   *listar*: listar os segredos ou versões de um segredo armazenado em um cofre de chaves  
-   *excluir*: excluir o segredo  
-   *todas*: todas as permissões  

Para obter mais informações sobre como trabalhar com segredos, consulte [Operações de segredo](/rest/api/keyvault/secret-operations.md).  

###  <a name="BKMK_SecretTags"></a> Marcas de segredos  
Você pode especificar mais metadados específicos do aplicativo na forma de marcas. O Cofre de Chaves do Azure oferece suporte a até 15 marcas, cada um deles pode ter um nome de 256 caracteres e um valor de 256 caracteres.  

>[!Note]
>As marcas podem ser lidas por um chamador se ele tiver a permissão *listar* ou *obter* para esse tipo de objeto; chaves, segredos ou certificados.

## <a name="key-vault-certificates"></a>Certificados do Cofre de Chaves

Os certificados do Cofre de Chaves oferecem suporte ao gerenciamento de seus certificados x509 e dos comportamentos a seguir:  

-   Permite que um proprietário de certificado crie um certificado por meio de um processo de criação de Cofre de Chaves ou pela importação de um certificado existente. Isso inclui certificados autoassinados e gerados por Autoridades de Certificação.
-   Permite que um proprietário de certificado do Cofre de Chaves implemente o armazenamento seguro e o gerenciamento de certificados X509 sem interagir com o material da chave privada.  
-   Permite que um proprietário de certificado crie uma política que direciona o Cofre de Chaves para gerenciar o ciclo de vida de um certificado.  
-   Permite que os proprietários de certificado forneçam informações de contato para a notificação sobre eventos de ciclo de vida de validade e renovação de certificados.  
-   Oferece suporte à renovação automática com emissores selecionados - provedores de certificado X509/autoridades de certificação parceiros do Cofre de Chaves.

>[!Note]
>Provedores/autoridades que não têm parceria também têm permissão, mas, não oferecerão suporte ao recurso de renovação automática.

###  <a name="BKMK_CompositionOfCertificate"></a> Composição de um certificado

Quando um certificado de Cofre de Chaves é criado, uma chave endereçável e o segredo também são criados com o mesmo nome. A chave de Cofre de Chaves permite operações de chave e o segredo do Cofre de Chaves permite a recuperação do valor do certificado como um segredo. Um certificado do Cofre de Chaves também contém metadados do certificado x509 público.  

O identificador e a versão de certificados é semelhante à de chaves e segredos. Uma versão específica de uma chave endereçável e segredo criados com a versão de certificado do Cofre de Chaves está disponível na resposta de certificado do Cofre de Chaves.
 
![Os certificados são objetos complexos](media/azure-key-vault.png)

###  <a name="BKMK_CertificateExportableOrNonExportableKey"></a> Chave exportável ou não exportável

Quando um certificado de Cofre de Chaves é criado, ele pode ser recuperado do segredo endereçável com a chave privada no formato PFX ou PEM se a política usada para criar o certificado indicou que a chave é exportável. Se a política usada para criar o Cofre de Chaves de certificado indicou que a chave é não exportável, a chave privada não é uma parte do valor quando recuperado como um segredo.  

A chave endereçável fica mais relevante com certificados KV não exportáveis. As operações da chave KV endereçável são mapeadas do campo *keyusage* da política de certificado KV usada para criar o certificado KV.  

Há suporte para dois tipos de chave – *RSA* ou *RSA HSM* com certificados. Exportável só é permitido com RSA, RSA HSM não tem suporte.  

###  <a name="BKMK_CertificateAttributesAndTags"></a> Marcas e atributos de certificado

Além dos metadados do certificado, uma chave endereçável e um segredo endereçável, um certificado do Cofre de Chaves também contém marcas e atributos.  

#### <a name="attributes"></a>Atributos

Os atributos de certificado são espelhados para atributos de chave endereçável e segredo criado quando o certificado KV é criado.  

Um certificado do Cofre de Chaves tem os seguintes atributos:  

-   *habilitado*: booliano, opcional, o padrão é **true**. Esse atributo pode ser especificado para indicar se os dados de certificado podem ser recuperados como segredo ou operável como uma chave. Isso é usado em conjunto com *nbf* e *exp* quando ocorre uma operação entre *nbf* e exp, ele só será permitido se habilitado estiver definido como true. As operações fora da janela *nbf* e*exp* são automaticamente proibidas.  

Há mais atributos somente leitura que são incluídos em resposta:

-   *criado*: IndDate indica quando esta versão do certificado foi criada.  
-   *atualizado*: IndDate indica quando esta versão do certificado foi atualizada.  
-   *exp*: IntDate: contém o valor da data de expiração do certificado x509.  
-   *nbf*: IntDate: contém o valor da data do certificado x509.  

> [!Note] 
> Se um certificado do Cofre de Chaves expirar, sua chave e segredo endereçáveis ficam inoperantes.  

#### <a name="tags"></a>Marcas

 O dicionário de pares de valor de chave especificado pelo cliente, semelhantes às marcas em chaves e segredos.  

 > [!Note]
> As marcas podem ser lidas por um chamador se ele tiver a permissão *listar* ou *obter* para esse tipo de objeto; chaves, segredos ou certificados.

###  <a name="BKMK_CertificatePolicy"></a> Política de certificado

Uma política de certificado contém informações sobre como criar e gerenciar o ciclo de vida de um certificado KV. Quando um certificado com a chave privada é importado para o Cofre de Chaves, uma política padrão é criada lendo o certificado x509.  

Quando um certificado KV é criado do zero, é necessário fornecer uma política para o Cofre de Chaves para informá-lo sobre como criar este certificado KV ou a próxima versão de certificado KV. Após o estabelecimento de uma política, não é necessária operações sucessivas para criar as próximas versões de certificado KV.  

Há apenas uma instância de uma política para todas as versões de um certificado KV.  

Em um nível alto, uma política de certificado contém o seguinte:  

-   Propriedades do certificado X509: contém o nome de assunto, nomes de entidade alternativo etc. usado para criar uma solicitação de certificado x509.  
-   Propriedades de chave: contém o tipo de chave, comprimento da chave e campos de chave reutilizáveis e exportáveis. Esses campos instruem o Cofre de Chaves sobre como gerar uma chave.  
-   Propriedades de segredo: contém propriedades secretas como tipo de conteúdo de segredo endereçável para gerar o valor de segredo, para recuperar o certificado como um segredo.  
-   Ações de tempo de vida: contém ações de tempo de vida do certificado KV. Cada ação de tempo de vida contém:  

     - Gatilho: especificado por meio de dias antes da expiração ou tempo de vida de porcentagem de alcance  

     - Ação: especificar o tipo de ação – *emailContacts* ou *renovar automaticamente*  

-   Emissor: parâmetros sobre o emissor do certificado a ser usado para emitir certificados x509.  
-   Atributos de política: contém os atributos associados à política  

#### <a name="x509-to-key-vault-usage-mapping"></a>X509 para mapeamento de uso do Cofre de Chaves

A tabela a seguir representa o mapeamento de política de uso de chave x509 para operações de chave efetivas de uma chave criada como parte da criação de certificado do Cofre de Chaves.

|**Sinalizadores de Utilização de Chave x509**|**Operações de chave do Cofre de Chaves**|**Comportamento padrão**|
|----------|--------|--------|
|DataEncipherment|criptografar, descriptografar| N/D |
|DecipherOnly|descriptografar| N/D  |
|DigitalSignature|fazer logon, verificar| Padrão de Cofre de Chaves sem uma especificação de uso no momento da criação de certificado | 
|EncipherOnly|encrypt| N/D |
|KeyCertSign|fazer logon, verificar|N/D|
|KeyEncipherment|wrapKey, unwrapKey| Padrão de Cofre de Chaves sem uma especificação de uso no momento da criação de certificado | 
|Não repúdio|fazer logon, verificar| N/D |
|crlsign|fazer logon, verificar| N/D |

###  <a name="BKMK_CertificateIssuer"></a> Emissor do certificado

Um objeto de certificado do Cofre de Chaves contém uma configuração usada para se comunicar com um provedor de emissor do certificado selecionado para solicitar certificados x509.  

-   Parceiros de Cofre de Chaves com as seguintes provedores de emissor do certificado para certificados SSL

|**Nome do Provedor**|**Locais**|
|----------|--------|
|DigiCert|Suporte em todos os locais de serviço de Cofre de Chaves na nuvem pública e o Azure Government|
|GlobalSign|Suporte em todos os locais de serviço de Cofre de Chaves na nuvem pública e o Azure Government|

Antes que um emissor de certificado possa ser criado em um Cofre de Chaves, as seguintes etapas 1 e 2 de pré-requisitos devem ser concluídas com êxito.  

1. Integrado aos provedores de Autoridade de Certificação (CA)  

    -   Um administrador da organização deve integrar sua empresa (ex. Contoso) com pelo menos um provedor de autoridade de certificação.  

2. O administrador cria as credenciais do solicitante para que o Cofre de Chaves registre (e renove) certificados SSL  

    -   Fornece a configuração a ser usado para criar um objeto de emissor do provedor no cofre de chaves  

Para obter mais informações sobre como criar objetos de emissor do portal de certificados, consulte o [blog de certificados de Cofre de Chave](http://aka.ms/kvcertsblog)  

O Cofre de Chaves permite a criação de vários objetos de emissor com configuração de provedor de emissor diferente. Quando um objeto de emissor é criado, seu nome pode ser referenciado em uma ou várias políticas de certificado. Referenciar o objeto emissor instrui o Cofre de Chaves para usar a configuração conforme especificado no objeto emissor ao solicitar o certificado x509 do provedor de autoridade de certificação durante a criação e renovação do certificado.  

Objetos de emissor são criados no cofre e só podem ser usados com certificados KV no mesmo cofre.  

###  <a name="BKMK_CertificateContacts"></a> Contatos de certificado

Contatos de certificado contêm informações de contato para enviar notificações disparadas por eventos de tempo de vida do certificado. As informações de contatos são compartilhadas por todos os certificados no cofre de chaves. Uma notificação é enviada a todos os contatos especificados para um evento para qualquer certificado no cofre de chaves.  

Se uma política de certificado for definida como renovação automática, uma notificação é enviada nos eventos a seguir.  

-   Antes da renovação de certificado
-   Após a renovação do certificado, indicar se o certificado foi renovado com êxito ou se houve um erro, exigindo a renovação manual do certificado.  

 Se uma política de certificado for definida ser renovada manualmente (somente por e-mail), uma notificação é enviada quando for o momento de renovar o certificado.  

###  <a name="BKMK_CertificateAccessControl"></a> Controle de acesso de certificado

 O controle de acesso para certificados é gerenciado pelo Cofre de Chaves e é fornecido no nível de um Cofre de Chaves que atua como o contêiner desses certificados. Há uma política de controle de acesso para certificados que é diferente da política de controle de acesso para chaves e segredos no mesmo Cofre de Chaves. Os usuários podem criar um ou mais cofres para armazenar certificados e são necessários para manter a segmentação e gerenciamento de certificados apropriados do cenário.  

 As seguintes permissões podem ser usadas, por entidade de segurança, na entrada de controle de acesso segredos em um cofre de chaves e refletem com maior exatidão as operações permitidas em um objeto de segredo:  

-   *obter*: permite obter a versão atual do certificado ou qualquer versão de um certificado 
-   *listar*: permite listar certificados atuais ou versões de um certificado  
-   *excluir*: permite excluir um certificado, sua política e todas as suas versões  
-   *criar*: permite criar um certificado de Chave de Cofre.  
-   *importar*: permite a importação de material de certificado em um Certificado de Cofre de Chaves.  
-   *atualizar*: permite a atualização de um certificado.  
-   *manageconnects*: permite o gerenciamento de contatos de certificados de Cofre de Chaves  
-   *getissuers*: permite obter de emissores de um certificado  
-   *listissuers*: permite listar emissores do certificado  
-   *setissuers*: permite criar ou atualizar emissores de certificado do Cofre de Chaves  
-   *deletissuers*: permite excluir emissores de certificado do Cofre de Chaves  
-   *todas*: concede todas as permissões  

## <a name="additional-information-for-certificates"></a>Informações adicionais sobre certificados

- [Certificados e políticas](/rest/api/keyvault/certificates-and-policies.md)
- [Emissores de certificado](/rest/api/keyvault/certificate-issuers.md)
- [Contatos de certificado](/rest/api/keyvault/certificate-contacts.md)

## <a name="see-also"></a>Veja também

- [Autenticação, solicitações e respostas](authentication-requests-and-responses.md)
- [Versões do Cofre de Chaves](key-vault-versions.md)
- [Guia do Desenvolvedor do Cofre de Chaves](/azure/key-vault/key-vault-developers-guide)
