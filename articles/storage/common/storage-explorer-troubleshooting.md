---
title: Guia de solução de problemas do Gerenciador de Armazenamento do Azure | Microsoft Docs
description: Visão geral dos dois recursos de depuração do Azure
services: virtual-machines
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/08/2017
ms.author: delhan
ms.openlocfilehash: f58fb5090aba3c5052d1bbdec76225d0ae50e8f2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Guia de solução de problemas do Gerenciador de Armazenamento do Azure

O Gerenciador de Armazenamento do Microsoft Azure é um aplicativo autônomo que permite que você trabalhe facilmente com dados do Armazenamento do Azure no Windows, macOS e Linux. O aplicativo pode conectar contas de armazenamento hospedadas no Azure, Nuvens Nacionais e no Microsoft Azure Stack.

Este guia resume as soluções de problemas comuns encontrados no Gerenciador de Armazenamento.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Erro: Certificado autoassinado na cadeia confiável (e erros semelhantes)

Erros de certificado são causados por uma das duas seguintes situações:

1. O aplicativo está conectado por meio de um "proxy transparente", o que significa que um servidor (como o servidor de sua empresa) está interceptando o tráfego HTTPS, descriptografando-o e, em seguida, criptografando usando um certificado autoassinado.
2. Você está executando um aplicativo que está injetando um certificado SSL autoassinado para as mensagens HTTPS recebidas. Exemplos de aplicativos que injetam certificados incluem software de inspeção de tráfego de antivírus e rede.

Quando o Gerenciador de Armazenamento vê um assinatura de autoatendimento ou o certificado não confiável, ele não sabe se a mensagem recebida do HTTPS foi alterada. Se você tiver uma cópia do certificado autoassinado, poderá permitir que o Gerenciador de Armazenamento confie nele realizando as seguintes etapas:

1. Obter a cópia codificada de Base 64 x. 509 (. cer) do certificado
2. Clique em **Editar** > **Certificados SSL** > **Importar Certificados** e, depois, use o seletor de arquivo para localizar, selecionar e abrir os arquivos .cer

Esse problema também pode ser o resultado de vários certificados (intermediário e raiz). Ambos os certificados devem ser adicionados para superar o erro.

Se você não tiver certeza de que o certificado é proveniente, você pode tentar estas etapas para encontrá-lo:

1. Instalar o Open SSL

    * [Windows](https://slproweb.com/products/Win32OpenSSL.html) (qualquer uma das versões leves deve ser suficiente)
    * Mac e Linux: deve estar incluído com o sistema operacional
2. Executar Open SSL

    * Windows: abra o diretório de instalação, clique em **/bin/** e clique duas vezes em **openssl.exe**.
    * Mac e Linux: execute **openssl** de um terminal.
3. Execute `s_client -showcerts -connect microsoft.com:443`
4. Procurar certificados autoassinados. Se você não tiver certeza quais são autoassinados, procure onde o assunto `("s:")` e o emissor `("i:")` são os mesmos.
5. Depois de encontrar os certificados autoassinados, para cada um deles, copie e cole tudo a partir, e incluindo, **---BEGIN CERTIFICATE---** até **---END CERTIFICATE---** em um novo arquivo .cer.
6. Abra o Gerenciador de Armazenamento, clique em **Editar** > **Certificados SSL** > **Importar Certificados** e, depois, use o seletor de arquivo para localizar, selecionar e abrir os arquivos .cer que você criou.

Se você não conseguir encontrar certificados autoassinados usando as etapas anteriores, entre em contato conosco por meio da ferramenta de comentários para obter mais ajuda. Como alternativa, você pode optar por iniciar o Gerenciador de Armazenamento na linha de comando com o `--ignore-certificate-errors` sinalizador. Quando iniciado com esse sinalizador, o Gerenciador de Armazenamento irá ignorar erros de certificado.

## <a name="sign-in-issues"></a>Problemas de entrada

Se você não conseguir entrar, tente os seguintes métodos:

* Reiniciar o Gerenciador de Armazenamento
* Se a janela de autenticação estiver em branco, aguarde pelo menos um minuto antes de fechar a caixa de diálogo de autenticação.
* Certifique-se de que as configurações de proxy e de certificado estão configuradas adequadamente no seu computador e no Gerenciador de Armazenamento
* Se você estiver no Windows e ter acesso ao Visual Studio de 2017 no mesmo computador e logon, tente entrar no Visual Studio de 2017

Se nenhum desses métodos funcionar [abra um problema no GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="unable-to-retrieve-subscriptions"></a>Não é possível recuperar as assinaturas

Se não for possível recuperar as assinaturas após a entrada bem-sucedida, tente os métodos de solução de problemas a seguir:

* Verifique se sua conta tem acesso às assinaturas que você espera. Você pode verificar se você tem acesso ao entrar no portal para o ambiente do Azure que você está tentando usar.
* Verifique se você entrou usando o ambiente certo (Azure, Azure China, Azure Alemanha, Azure US Government nos ou Ambiente Personalizado).
* Se você estiver atrás de um proxy, verifique se você configurou o proxy do Gerenciador de Armazenamento apropriadamente.
* Tente remover e readicionar a conta.
* Assista o console de ferramentas de desenvolvedor (Ajuda > Ferramentas de desenvolvedor de alternância) enquanto o Gerenciador de Armazenamento está carregando assinaturas. Procure por mensagens de erro (texto vermelho) ou qualquer mensagem que contém o texto "Não foi possível carregar as assinaturas de locatários." Se você vir alguma mensagem a respeito, [abra um problema no GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cannot-remove-attached-account-or-storage-resource"></a>Não é possível remover o recurso de conta ou armazenamento anexado

Se não for possível remover uma conta anexada ou um recurso de armazenamento por meio da interface do usuário, exclua manualmente todos os recursos anexados, excluindo as seguintes pastas:

* Windows: `%AppData%/StorageExplorer`
* macOS - `/Users/<your_name>/Library/Applicaiton Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
>  Feche o Gerenciador de Armazenamento antes de excluir as pastas acima.

> [!NOTE]
>  Se você já importou todos os certificados SSL e o conteúdo de backup do diretório `certs`. Posteriormente, você pode usar o backup para reimportar os certificados SSL.

## <a name="proxy-issues"></a>Problemas de proxy

Primeiro, certifique-se de que as seguintes informações inseridas estejam corretas:

*O URL do proxy e o número de porta *Nome de usuário e senha, caso seja solicitado pelo proxy

### <a name="common-solutions"></a>Soluções comuns

Se você ainda tiver problemas, tente os seguintes métodos:

* Se você puder se conectar à Internet sem usar o proxy, verifique se o Gerenciador de Armazenamento funciona sem as configurações de proxy habilitadas. Se esse for o caso, talvez haja um problema com as configurações de proxy. Trabalhe com seu administrador de proxy para identificar os problemas.
* Verifique se outros aplicativos estão usando o servidor proxy conforme o esperado.
* Verifique se você pode conectar ao portal para o ambiente do Azure que você está tentando usar
* Verifique se que você pode receber respostas de seus pontos de extremidade de serviço. Insira uma das suas URLs de ponto de extremidade em seu navegador. Se você puder se conectar, deverá receber um InvalidQueryParameterValue ou resposta XML semelhante.
* Se outra pessoa também estiver usando o Gerenciador de Armazenamento com o servidor proxy, verifique se eles podem se conectar. Se eles puderem se conectar, talvez seja necessário entrar em contato com o administrador de seu servidor proxy.

### <a name="tools-for-diagnosing-issues"></a>Ferramentas para diagnosticar problemas

Se você tiver ferramentas de rede, como o Fiddler para Windows, poderá diagnosticar os problemas da seguinte maneira:

* Se você tiver que trabalhar por meio do proxy, será necessário configurar a ferramenta de rede para se conectar por meio do proxy.
* Verifique o número da porta usado por sua ferramenta de rede.
* Insira a URL do host local e o número de porta da ferramenta de rede como configurações de proxy no Gerenciador de Armazenamento. Quando feito corretamente, a ferramenta de rede passará a registrar solicitações de rede feitas pelo Gerenciador de Armazenamento para pontos de extremidade de serviço e de gerenciamento. Por exemplo, insira https://cawablobgrs.blob.core.windows.net/do ponto de extremidade de blob em um navegador e você receberá uma resposta semelhante à seguinte, que sugere que o recurso existe, embora não seja possível acessá-lo.

![exemplo de código](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>Entre em contato com o administrador do servidor proxy

Se as configurações de proxy estiverem corretas, talvez seja necessário entrar em contato com seu administrador de servidor proxy e

* Verifique se o seu proxy não bloqueia o tráfego nos pontos de extremidade de gerenciamento ou recurso do Azure.
* Verifique o protocolo de autenticação usado por seu servidor proxy. No momento, o Gerenciador de Armazenamento não dá suporte a proxies NTLM.

## <a name="unable-to-retrieve-children-error-message"></a>Mensagem de erro "Não é Possível Recuperar Filhos"

Se você estiver conectado ao Azure por meio de um proxy, verifique se as configurações do proxy estão corretas. Se você tiver recebido acesso a um recurso do proprietário da assinatura ou conta, verifique se você tem permissões de leitura ou de lista para esse recurso.

### <a name="issues-with-sas-url"></a>Problemas com a URL SAS
Se você estiver se conectando a um serviço usando uma URL SAS e enfrentando este erro:

* Verifique se a URL fornece as permissões necessárias para ler ou lista recursos.
* Verifique se a URL não expirou.
* Se a URL SAS tiver base em uma política de acesso, verifique se a política de acesso não foi revogada.

Se você anexou acidentalmente usando uma URL da SAS inválida e não é possível desanexá-la, execute as seguintes etapas:
1.  Ao executar o Gerenciador de Armazenamento, pressione F12 para abrir a janela de ferramentas para desenvolvedores.
2.  Clique na guia Aplicativo e clique em Armazenamento Local > file:// na árvore à esquerda.
3.  Localize a chave associada ao tipo de serviço do URI SAS problemático. Por exemplo, se o URI da SAS incorreta for para um contêiner de blob, procure a chave nomeada `StorageExplorer_AddStorageServiceSAS_v1_blob`.
4.  O valor da chave deve ser uma matriz JSON. Localize o objeto associado ao URI inválido e remova-o.
5.  Pressione Ctrl+R para recarregar o Gerenciador de Armazenamento.

## <a name="linux-dependencies"></a>Dependências do Linux

Para distribuições Linux diferentes do Ubuntu 16.04, talvez seja necessário instalar algumas dependências manualmente. Em geral, são necessários os seguintes pacotes:
* [.NET Core 2.x](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)
* `libsecret`
* `libgconf-2-4`
* GCC atualizado

Dependendo da sua distribuição, talvez seja necessário instalar outros pacotes. O Gerenciador de Armazenamento[Notas de Versão](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409) contém etapas específicas para algumas distribuições.

## <a name="next-steps"></a>Próximas etapas

Se nenhuma das soluções funcionar para então [abra um problema no GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues). Você pode também obter rapidamente GitHub, usando o botão "Relatar problema ao GitHub" no canto inferior esquerdo.

![Comentários](./media/storage-explorer-troubleshooting/feedback-button.PNG)
