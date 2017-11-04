---
title: "Recomendações de segurança para as imagens do Azure Marketplace | Microsoft Docs"
description: "Este artigo fornece recomendações para as imagens incluídas no Marketplace"
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: barclayn
ms.openlocfilehash: 4ae36f87c29975c82bb99f713893a9dc78a249e6
ms.sourcegitcommit: d6ad3203ecc54ab267f40649d3903584ac4db60b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2017
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Recomendações de segurança para as imagens do Azure Marketplace

É recomendável que cada solução esteja em conformidade com as seguintes recomendações de configuração de segurança. Isso ajuda a manter um alto nível de segurança para as imagens de solução de parceiro no Azure Marketplace.

Essas recomendações também podem ser úteis para organizações que não possuem imagens no Azure Marketplace. Verifique as configurações de imagem do Windows e Linux da sua empresa em relação às diretrizes encontradas nas tabelas abaixo.

## <a name="open-source-based-images"></a>Abrir imagens com base na origem

|||
|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Categoria**                                                 | **Verificação**                                                                                                                                                                                                                                                                              |
| Segurança                                                     | Todos os patches de segurança mais recentes para a distribuição Linux estão instalados.                                                                                                                                                                                                              |
| Segurança                                                     | As diretrizes do setor para proteger a imagem da VM para a distribuição Linux específica foram seguidas.                                                                                                                                                                                     |
| Segurança                                                     | Limite a superfície de ataque mantendo somente o conteúdo mínimo necessário das funções, recursos, serviços e portas de rede do Windows Server.                                                                                                                                               |
| Segurança                                                     | Faça a verificação do código-fonte e da imagem da VM em relação à presença de malware.                                                                                                                                                                                                                                   |
| Segurança                                                     | A imagem VHD inclui apenas as contas bloqueadas necessárias, que não tenham senhas padrão que permitiriam um logon interativo; sem backdoors.                                                                                                                                           |
| Segurança                                                     | As regras de firewall estão desabilitadas, a menos que a funcionalidade do aplicativo dependa delas, como um dispositivo de firewall.                                                                                                                                                                             |
| Segurança                                                     | Todas as informações confidenciais foram removidas da imagem VHD, tais como chaves SSH de teste, arquivo de hosts conhecidos, arquivos de log e certificados desnecessários.                                                                                                                                       |
| Segurança                                                     | É recomendável que a LVM não seja usada.                                                                                                                                                                                                                                            |
| Segurança                                                     | As versões mais recentes das bibliotecas necessárias devem ser incluídas: </br> - OpenSSL v1.0 ou superior </br> - Python 2.5 ou superior (Python 2.6+ é altamente recomendável) </br> - Pacote pyasn1 do Python, se já não estiver instalado </br> - d.OpenSSL v 1.0 ou superior                                                                |
| Segurança                                                     | As entradas de histórico do shell/busca devem ser apagadas                                                                                                                                                                                                                                             |
| Rede                                                   | O servidor SSH deve ser incluído por padrão. Defina o SSH keep alive para a configuração sshd com a seguinte opção: ClientAliveInterval 180                                                                                                                                                        |
| Rede                                                   | A imagem não deve conter nenhuma configuração de rede personalizada. Exclua o resolv.conf: `rm /etc/resolv.conf`                                                                                                                                                                                |
| Implantação                                                   | O Agente Linux do Azure mais recente deve ser instalado </br> - O agente deve ser instalado usando o pacote RPM ou Deb.  </br> - Você também pode usar o processo de instalação manual, mas os pacotes do instalador são recomendados e preferenciais. </br> - Se instalar o agente manualmente a partir do repositório github, primeiro copie o arquivo `waagent` para `/usr/sbin` e execute (como raiz): </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>O arquivo de configuração do agente será colocado em `/etc/waagent.conf`.    |
| Implantação                                                   | Garante que o Suporte do Azure pode fornecer aos nossos parceiros uma saída de console serial quando necessário, e fornece o tempo limite adequado para montagem do disco de sistema operacional a partir do armazenamento em nuvem. A imagem deve ter adicionado os seguintes parâmetros à linha de inicialização do Kernel: `console=ttyS0 earlyprintk=ttyS0 rootdelay=300` |
| Implantação                                                   | Não troque a partição no disco do sistema operacional. A troca pode ser solicitada para a criação no disco de recurso local pelo Agente do Linux.         |
| Implantação                                                   | É recomendável que uma partição de raiz única seja criada para o disco do sistema operacional.      |
| Implantação                                                   | Somente sistema operacional de 64 bits.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Imagens baseadas no Windows Server

|||
|-------------| -------------------------|
| **Categoria**                                                     | **Verificação**                                                                                                                                                                |
| Segurança                                                         | Use uma imagem de base segura do sistema operacional. O VHD usado para a origem de qualquer imagem baseada no Windows Server deve estar entre as imagens do sistema operacional do Windows Server fornecidas pelo Microsoft Azure. |
| Segurança                                                         | Instale todas as últimas atualizações de segurança.                                                                                                                                     |
| Segurança                                                         | Os aplicativos não devem ter uma dependência de nomes de usuário restritos, como Administrador, raiz e admin.                                                                |
| Segurança                                                         | Não há suporte para Criptografia de Unidade de Disco BitLocker no disco rígido do sistema operacional. O BitLocker pode ser usado nos discos de dados.                                                            |
| Segurança                                                         | Limite a superfície de ataque mantendo somente o conteúdo mínimo necessário das funções, recursos, serviços e portas de rede do Windows Server habilitado.                         |
| Segurança                                                         | Faça a verificação do código-fonte e da imagem da VM em relação à presença de malware.                                                                                                                     |
| Segurança                                                         | Configure a atualização de segurança de imagens do Windows Server para atualização automática.                                                                                                                |
| Segurança                                                         | A imagem VHD inclui apenas as contas bloqueadas necessárias, que não tenham senhas padrão que permitiriam um logon interativo; sem backdoors.                             |
| Segurança                                                         | As regras de firewall estão desabilitadas, a menos que a funcionalidade do aplicativo dependa delas, como um dispositivo de firewall.                                                               |
| Segurança                                                         | Todas as informações confidenciais foram removidas da imagem VHD. Por exemplo, arquivo de HOSTS, arquivos de log e certificados desnecessários devem ser removidos.                                              |
| Implantação                                                       | Somente sistema operacional de 64 bits.                            |
