---
title: "Criar certificados autoassinados para conexões entre locais de rede virtual Ponto a Site usando makecert | Microsoft Docs"
description: "Este artigo contém etapas para usar o makecert para criar os certificados autoassinados no Windows 10."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 27b99f7c-50dc-4f88-8a6e-d60080819a43
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/22/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b44f46273c6ddb439ec3793f193986011c222ee3


---
# <a name="working-with-self-signed-certificates-for-point-to-site-connections"></a>Trabalhando com certificados autoassinados para conexões de Ponto a site
Este artigo ajuda você a criar um certificado autoassinado usando **makecert**e a gerar certificados de cliente por meio dele. As etapas a seguir são relacionadas ao makecert no Windows 10. O makecert foi validado para criar certificados compatíveis com conexões P2S. 

Para conexões P2S, o método preferencial para os certificados é usar a solução de certificado corporativo, certificando-se de emitir os certificados de cliente com o formato de valor de nome comum 'name@yourdomain.com',, em vez do formato "Nome de domínio NetBIOS\nome de usuário".

Se você não tiver uma solução corporativa, um certificado autoassinado será necessário para permitir que clientes P2S se conectem a uma rede virtual. Estamos cientes de que o makecert foi preterido, mas ele ainda é um método válido para criar certificados autoassinados compatíveis com conexões P2S. Estamos trabalhando em outra solução para a criação de certificados autoassinados, mas desta vez, makecert é o método preferencial.

## <a name="create-a-self-signed-certificate"></a>Crie um certificado autoassinado
O makecert é uma maneira de criar um certificado autoassinado. As etapas abaixo o orientarão na criação de um certificado autoassinado usando o makecert. Essas etapas não são específicas do modelo de implantação. Elas são válidas tanto para o Gerenciador de Recursos quanto para o clássico.

### <a name="to-create-a-self-signed-certificate"></a>Para criar um certificado autoassinado
1. Em um computador com Windows 10, baixe e instale o [SDK (Software Development Kit) do Windows para Windows 10](https://dev.windows.com/en-us/downloads/windows-10-sdk).
2. Após a instalação, localize o utilitário makecert.exe nesse caminho: C:\Program Files (x86)\Windows Kits\10\bin\<arch>. 
   
    Exemplo: `C:\Program Files (x86)\Windows Kits\10\bin\x64`
3. Em seguida, crie e instale um certificado no repositório de certificados Pessoal em seu computador. O exemplo a seguir cria um arquivo *.cer* correspondente que você carrega no Azure ao configurar P2S. Execute o seguinte comando, como administrador. Substitua  *ARMP2SRootCert* e *ARMP2SRootCert.cer* com o nome que você deseja usar para o certificado.<br><br>O certificado estará em seus Certificados - Usuário Atual\Pessoal\Certificados.
   
        makecert -sky exchange -r -n "CN=ARMP2SRootCert" -pe -a sha1 -len 2048 -ss My "ARMP2SRootCert.cer"

### <a name="a-namerootpublickeyato-obtain-the-public-key"></a><a name="rootpublickey"></a>Para obter a chave pública
Como parte da configuração do Gateway de VPN para conexões Ponto a Site, a chave pública do certificado raiz é carregada no Azure.

1. Para obter um arquivo .cer do certificado, abra **certmgr.msc**. Clique com o botão direito no certificado raiz autoassinado, clique em** todas as tarefas** e clique em **exportar**. Isso abre o **Assistente para Exportação de Certificados**.
2. No Assistente, clique em **Avançar**, escolha , **Não exportar a chave privada** e clique em **Avançar**.
3. Na página **Exportar Formato de Arquivo**, selecione **X.509 codificado em Base 64 (.CER).** Em seguida, clique em **Avançar**. 
4. Em **Arquivo a ser Exportado**, use **Procurar** para encontrar a localização para a qual você deseja exportar o certificado. Em **Nome do arquivo**, dê um nome ao arquivo de certificado. Em seguida, clique em **Próximo**.
5. Clique em **Concluir** para exportar o certificado.

### <a name="export-the-self-signed-certificate-optional"></a>Exportar o certificado autoassinado (opcional)
Convém exportar o certificado autoassinado e armazená-lo com segurança. Se precisar, você pode instalá-lo depois em outro computador e gerar mais certificados de cliente, ou exportar outro arquivo .cer. Qualquer computador com um certificado de cliente instalado e que também é definido com as configurações de cliente VPN adequadas podem se conectar à sua rede virtual por meio de P2S. Por esse motivo, convém verificar se os certificados de cliente são gerados e instalados apenas quando necessário, e se esse certificado autoassinado passa pelo armazenamento com segurança.

Para exportar o certificado autoassinado como um .pfx, selecione o certificado raiz e use as mesmas etapas, conforme descrito em [Exportar um certificado de cliente](#clientkey) para exportar.

## <a name="create-and-install-client-certificates"></a>Criar e instalar certificados de cliente
Não instale o certificado autoassinado diretamente no computador cliente. Você precisa gerar um certificado de cliente do certificado autoassinado. Em seguida, você exporta e instala o certificado de cliente no computador cliente. Essas etapas não são específicas ao modelo de implantação. Elas são válidas tanto para o Gerenciador de Recursos quanto para o clássico.

### <a name="part-1---generate-a-client-certificate-from-a-self-signed-certificate"></a>Parte 1 – Gerar um certificado de cliente de um certificado autoassinado
As etapas abaixo o orientarão quanto a uma maneira de gerar um certificado de cliente por meio de um certificado autoassinado. Você pode gerar vários certificados de cliente por meio do mesmo certificado. Cada certificado de cliente pode ser exportado e instalado no computador cliente. 

1. No mesmo computador usado para criar o certificado autoassinado, abra um prompt de comando como administrador.
2. Neste exemplo, "ARMP2SRootCert" refere-se ao certificado autoassinado que você gerou. 
   
   * Altere *"ARMP2SRootCert"* para o nome da raiz autoassinada a partir da qual você está gerando o certificado do cliente. 
   * Altere *ClientCertificateName* para o nome que você deseja dar a um certificado de cliente gerado. 

    Modifique e execute o exemplo para gerar um certificado de cliente. Se você executar o exemplo a seguir sem modificá-lo, o resultado será um certificado de cliente chamado ClientCertificateName em seu repositório de certificados Pessoal que foi gerado a partir do certificado raiz ARMP2SRootCert.

        makecert.exe -n "CN=ClientCertificateName" -pe -sky exchange -m 96 -ss My -in "ARMP2SRootCert" -is my -a sha1

1. Todos os certificados são armazenados no repositório ‘Certificado - Usuário Atual\Pessoal\Certificados’ do computador. Você pode gerar quantos certificados de cliente forem necessários com base neste procedimento.

### <a name="a-nameclientkeyapart-2---export-a-client-certificate"></a><a name="clientkey"></a>Parte 2 – Exportar um certificado de cliente
1. Para exportar um certificado de cliente, abra **certmgr.msc**. Clique com o botão direito no certificado do cliente que você deseja exportar, clique em **todas as tarefas** e, em seguida, clique em **exportar**. Isso abre o **Assistente para Exportação de Certificados**.
2. No Assistente, clique em **Avançar**, escolha **Sim, exportar a chave privada** e clique em **Avançar**.
3. Na página **Exportar Formato de Arquivo** , você pode deixar os padrões selecionados. Em seguida, clique em **Próximo**. 
4. Na página **Segurança** , você deve proteger a chave privada. Se você optar por usar uma senha, não deixe de anotar ou lembrar da senha definida para esse certificado. Em seguida, clique em **Próximo**.
5. Em **Arquivo a ser Exportado**, use **Procurar** para encontrar a localização para a qual você deseja exportar o certificado. Em **Nome do arquivo**, dê um nome ao arquivo de certificado. Em seguida, clique em **Próximo**.
6. Clique em **Concluir** para exportar o certificado.    

### <a name="part-3---install-a-client-certificate"></a>Parte 3 – Instalar um certificado de cliente
Cada cliente que você deseja conectar à sua rede virtual usando uma conexão Ponto a Site deve ter um certificado do cliente instalado. Esse certificado está além do pacote de configuração de VPN necessário. As etapas a seguir guiarão você pela instalação manual do certificado de cliente.

1. Localize e copie o arquivo *.pfx* no computador cliente. No computador cliente, clique duas vezes no arquivo *.pfx* para instalá-lo. Deixe **Localização do Repositório** como **Usuário Atual** e clique em **Avançar**.
2. Na página **Arquivo** a importar, não faça nenhuma alteração. Clique em **Próximo**.
3. Na página **Proteção da chave privada**, insira a senha do certificado, caso tenha usado uma, ou verifique se a entidade de segurança que está instalando o certificado está correta e clique em **Avançar**.
4. Na página **Repositório de Certificados**, deixe a localização padrão e clique em **Avançar**.
5. Clique em **Concluir**. No **Aviso de Segurança** da instalação do certificado, clique em **Sim**. O certificado foi importado com êxito.

## <a name="next-steps"></a>Próximas etapas
Continue com a configuração de Ponto a Site. 

* Para as etapas do modelo de implantação do **Gerenciador de Recursos** , veja [Configurar uma conexão Ponto a Site com uma VNet usando o PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md). 
* Para as etapas do modelo de implantação **clássico** , veja [Configurar uma conexão VPN Ponto a Site para uma VNet usando o portal clássico](vpn-gateway-point-to-site-create.md).




<!--HONumber=Nov16_HO3-->


