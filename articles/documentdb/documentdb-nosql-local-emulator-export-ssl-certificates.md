---
title: Exportar os certificados do Emulador do DocumentDB | Microsoft Docs
description: "Ao desenvolver em linguagens e em tempos de execução que não usam o Repositório de Certificados do Windows, você precisará exportar e gerenciar os certificados SSL. Esta postagem fornece instruções passo a passo."
services: documentdb
documentationcenter: 
keywords: Emulador do DocumentDB
author: voellm
manager: jhubbard
editor: 
ms.assetid: ef43deda-c2e9-4193-99e2-7f6a88a0319f
ms.service: documentdb
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2017
ms.author: tvoellm
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: 5f2fd8810041d47be2ef0b21e1487426a643d541
ms.lasthandoff: 03/07/2017


---

# <a name="export-the-documentdb-emulator-certificates-for-use-with-java-python-and-nodejs"></a>Exportar os certificados do Emulador do DocumentDB para uso com Java, Python e Node.js

[**Baixar o Emulador**](https://aka.ms/documentdb-emulator)

O Emulador do DocumentDB do Azure fornece um ambiente local que emula o serviço DocumentDB do Azure para fins de desenvolvimento, incluindo o uso de conexões SSL. Esta postagem demonstra como exportar os certificados SSL para uso em linguagens e em tempos de execução que não são integrados ao Repositório de Certificados do Windows, por exemplo, Java, que usa seu próprio [repositório de certificados](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html) e Python, que usa [wrappers de soquete](https://docs.python.org/2/library/ssl.html) e .Node.js que usa [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback). Leia mais sobre o emulador em [Usar o Emulador de DocumentDB do Azure para desenvolvimento e teste](./documentdb-nosql-local-emulator.md).

## <a name="certification-rotation"></a>Rotação de certificação

Os certificados no Emulador Local do DocumentDB são gerados na primeira vez que o emulador é executado. Há dois certificados. Um usado para conexão com o emulador do local, e outro para gerenciar segredos no emulador. O certificado que você quer exportar é o certificado de conexão com o nome amigável "DocumentDBEmulatorCertificate".

Os dois certificados podem ser gerados novamente clicando em **Redefinir Dados**, conforme mostrado abaixo no Emulador do DocumentDB em execução na Bandeja do Windows. Se você gerar novamente os certificados e instalá-los no repositório de certificados Java, ou usá-los em outro lugar, será necessário atualizá-los, caso contrário, seu aplicativo não conectará mais ao emulador local.

![Redefinir dados do emulador local do DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-reset-data.png)

## <a name="how-to-export-the-documentdb-ssl-certificate"></a>Como exportar o certificado SSL do DocumentDB

1. Inicie o Gerenciador de Certificados do Windows executando certlm.msc, navegue até a pasta Pessoal-> Certificados e abra o certificado com o nome amigável **DocumentDBEmulatorCertificate**.

    ![Etapa 1 da exportação do emulador local do DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-1.png)

2. Clique em **Detalhes** e depois em **OK**.

    ![Etapa 2 da exportação do emulador local do DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-2.png)

3. Clique em **Copiar para arquivo...**.

    ![Etapa 3 da exportação do emulador local do DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-3.png)

4. Clique em **Próximo**.

    ![Etapa 4 da exportação do emulador local do DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-4.png)

5. Clique em **Não, não exportar a chave privada** e, em seguida, clique em **Avançar**.

    ![Etapa 5 da exportação do emulador local do DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-5.png)

6. Clique em **X.509 codificado em Base-64 (.CER)** e depois em **Avançar**.

    ![Etapa 6 da exportação do emulador local do DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-6.png)

7. Dê um nome ao certificado. Nesse caso, **documentdbemulatorcert** e, em seguida, clique em **Avançar**.

    ![Etapa 7 da exportação do emulador local do DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-7.png)

8. Clique em **Concluir**.

    ![Etapa 8 da exportação do emulador local do DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-8.png)

## <a name="how-to-use-the-certificate-in-java"></a>Como usar o certificado em Java

Durante a execução de aplicativos Java ou MongoDB que usam o cliente Java, é mais fácil instalar o certificado no repositório de certificados Java padrão do que passar os sinalizadores "-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>. Por exemplo, o [aplicativo de demonstração Java](https://localhost:8081/_explorer/index.html) incluído depende do repositório de certificados padrão.

Siga as instruções em [Adicionar um certificado ao Repositório de Certificados de AC Java](https://docs.microsoft.com/en-us/azure/java-add-certificate-ca-store) para importar o certificado X.509 no repositório de certificados Java padrão. Lembre-se de que você trabalhará no diretório %JAVA_HOME% durante a execução da keytool.

Após a instalação do certificado SSL "DocumentDBEmulatorCertificate", seu aplicativo deverá ser capaz de se conectar e usar o Emulador local do DocumentDB. Se você continuar tendo problemas, siga o artigo [Depuração de conexões SSL/TLS](http://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html). É muito provável que o certificado não esteja instalado no repositório %JAVA_HOME%/jre/lib/security/cacerts. Por exemplo, se você tiver várias versões do Java instaladas, seu aplicativo poderá usar um repositório de cacerts diferente daquele que você atualizou.

## <a name="how-to-use-the-certificate-in-python"></a>Como usar o certificado em Python

Por padrão, o [SDK do Python (versão 2.0.0 ou superior)](https://docs.microsoft.com/en-us/azure/documentdb/documentdb-sdk-python) para DocumentDB não tentará usar o certificado SSL ao se conectar ao emulador local. Se, no entanto, você quiser usar a validação de SSL, siga os exemplos na documentação [Wrappers de soquete Python](https://docs.python.org/2/library/ssl.html).

## <a name="how-to-use-the-certificate-in-nodejs"></a>Como usar o certificado no Node.js

Por padrão, o [SDK do Node.js (versão 1.10.1 ou superior)](https://docs.microsoft.com/en-us/azure/documentdb/documentdb-sdk-node) para DocumentDB não tentará usar o certificado SSL ao se conectar ao emulador local. Se, no entanto, você quiser usar a validação de SSL, siga os exemplos na [Documentação do Node.js](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre o DocumentDB, veja [Introdução ao Azure DocumentDB](documentdb-introduction.md)
* Para começar a desenvolver no Emulador do DocumentDB, baixe um dos [SDKs do DocumentDB com suporte](documentdb-sdk-dotnet.md).

