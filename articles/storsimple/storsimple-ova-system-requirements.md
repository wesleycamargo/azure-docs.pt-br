<properties
   pageTitle="Requisitos do sistema da StorSimple Virtual Array"
   description="Aprender sobre os requisitos de software e de rede para sua StorSimple Virtual Array"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/01/2016"
   ms.author="alkohli"/>

# Requisitos do sistema da StorSimple Virtual Array

## Visão geral

Este artigo descreve os requisitos de sistema importantes para sua Matriz Virtual Microsoft Azure StorSimple (também conhecido como dispositivo virtual local StorSimple ou dispositivo virtual StorSimple) e para os clientes de armazenamento que acessam a matriz. Recomendamos que você examine as informações com atenção antes de implantar o sistema StorSimple e consulte-as, quando necessário, durante a implantação e operação subsequente.

Os requisitos do sistema incluem:

-   **Requisitos de software para clientes de armazenamento** - descreve as plataformas de virtualização, navegadores da Web, iniciadores iSCSI e clientes SMB para os quais há suporte, os requisitos mínimos de dispositivo virtual e quaisquer requisitos adicionais para esses sistemas operacionais.

-   **Requisitos de rede para o dispositivo StorSimple** - fornece informações sobre as portas que precisam ser abertas no firewall para permitir o tráfego iSCSI, de nuvem ou de gerenciamento.

As informações de requisitos de sistema do StorSimple publicadas neste artigo se aplica somente a Matrizes Virtuais StorSimple.

- Para dispositivos série 8000, vá para [requisitos de sistema para o dispositivo StorSimple série 8000](storsimple-system-requirements.md).
 
- Para dispositivos série 7000, vá para [Requisitos de sistema para o dispositivo StorSimple série 5000-7000](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements).


## Requisitos de software

Os requisitos de software incluem as informações sobre os navegadores da Web, versões SMB e plataformas de virtualização para os quais há suporte, além dos requisitos mínimos de dispositivo virtual.

### Plataformas de virtualização com suporte


| **Hipervisor** | **Versão** |
|----------------|--------------------------------------|
| Hyper-V | Windows Server 2008 R2 SP1 e posterior |
| VMware ESXi | 5\.5 e posterior |

### Requisitos de dispositivo virtual

| **Componente** | **Requisito** |
|----------------------------------------------|----------------------------|
| Número mínimo de processadores virtuais (núcleos) | 4 |
| Mínimo de memória (RAM) | 8 GB |
| Espaço em disco<sup>1</sup> | Disco do sistema operacional - 80 GB <br></br>Disco de dados - 500 GB a 8 TB|
| Número mínimo de interface(s) de rede | 1 |
| Largura de banda mínima da Internet<sup>2</sup> | 5 Mbps |

<sup>1</sup> - Com provisionamento dinâmico

<sup>2</sup> - Os requisitos de rede podem variar dependendo da taxa de alteração diária. Por exemplo, se um dispositivo precisa realizar backup de 10 GB ou mais de alterações durante um dia, o backup diário por uma conexão de 5 Mbps pode levar até 4,25 horas (caso não tenha sido possível comprimir ou eliminar a duplicação dos dados).

### Navegadores da Web com suporte

| **Componente** | **Versão** | **Requisitos/observações adicionais** |
|-------------------|-----------------|-----------------------------------|
| Microsoft Edge | Última versão | |
| Internet Explorer | Última versão | Testado com o Internet Explorer 11 |
| Google Chrome | Última versão | Testado com o Chrome 46 |

### Versões do SMB com suporte

| **Versão** |
|-------------|
| SMB 2.x |
| SMB 3.0 |
| SMB 3.02 |

## Requisitos de rede 

A tabela a seguir lista as portas que devem ser abertas no firewall para permitir o tráfego de gerenciamento, de nuvem, SMB ou iSCSI. Nesta tabela, *entrada* ou *de entrada* refere-se à direção a partir da qual as solicitações de cliente acessam o dispositivo. *Saída* ou *de saída* refere-se à direção na qual seu dispositivo StorSimple envia dados externamente, além da implantação: por exemplo, saída para a Internet.

| **Porta nº<sup>1</sup>** | **Entrada ou saída** | **Escopo da porta** | **Obrigatório** | **Observações** |
|--------------------------|---------------|----------------|---------------------------|----------------------------------------------------------------------------------------------------------------------|
| TCP 80 (HTTP) | Saída | WAN | Não | A porta de saída é usada para acesso à Internet para recuperar atualizações. <br></br>O proxy Web de saída é configurável pelo usuário. |
| TCP 443 (HTTPS) | Saída | WAN | Sim | A porta de saída é usada para acessar dados na nuvem. <br></br>O proxy Web de saída é configurável pelo usuário. |
| UDP 53 (DNS) | Saída | WAN | Em alguns casos; consulte as observações. | Esta porta só será necessária se você estiver usando um servidor DNS baseado na Internet. <br></br> **Observação**: se estiver implantando um servidor de arquivos, recomendamos usar o servidor DNS local.|
| UDP 123 (NTP) | Saída | WAN | Em alguns casos; consulte as observações. | Esta porta é necessária apenas se estiver usando um servidor NTP baseado na Internet.<br></br> **Observação:** se estiver implantando um servidor de arquivos, recomendamos sincronizar a hora com os controladores de Domínio do Active Directory. |
|TCP 9354 | Saída | WAN | Sim | A porta de saída é usada pelo dispositivo StorSimple para se comunicar com o serviço StorSimple Manager.|
| TCP 80 (HTTP) | No | LAN | Sim | Essa é a porta de entrada para a interface do usuário local no dispositivo StorSimple para gerenciamento local. <br></br> **Observação**: o acesso à interface do usuário local em HTTP será automaticamente redirecionado para HTTPS.|
| TCP 443 (HTTPS) | No | LAN | Sim | Essa é a porta de entrada para a interface do usuário local no dispositivo StorSimple para gerenciamento local.|
| TCP 3260 (iSCSI) | No | LAN | Não | Esta porta é usada para acessar dados em iSCSI.|

<sup>1</sup> Nenhuma porta de entrada precisa estar aberta na Internet pública.

## Próxima etapa

-   [Preparar o portal para implantar sua StorSimple Virtual Array](storsimple-ova-deploy1-portal-prep.md)

<!---HONumber=AcomDC_0302_2016-->