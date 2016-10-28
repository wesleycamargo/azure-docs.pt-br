<properties
   pageTitle="Introdução ao FreeBSD no Azure | Microsoft Azure"
   description="Saiba como usar máquinas virtuais FreeBSD no Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="KylieLiang"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/27/2016"
   ms.author="kyliel"/>

# Introdução ao FreeBSD no Azure
Este tópico fornece uma visão geral da execução da máquina virtual FreeBSD no Azure.

## Visão geral
O FreeBSD para Microsoft Azure é um sistema operacional avançado usado para capacitar servidores modernos, desktops e plataformas incorporadas. A imagem do FreeBSD 10.3 é fornecida pela Microsoft Corporation e está disponível no Azure. Ela se baseia na versão do FreeBSD 10.3 e o Agente Convidado da VM do Azure [2\.1.4](https://github.com/Azure/WALinuxAgent/releases/tag/v2.1.4) está instalado. O agente é responsável pela comunicação entre a VM FreeBSD e a malha Azure para operações como provisionamento da VM no primeiro uso (nome de usuário, senha, nome de host etc.) e pela habilitação da funcionalidade para extensões de VM seletivas. Para versões futuras do FreeBSD, a estratégia é manter-se atualizado e disponibilizar as versões mais recentes logo após o lançamento pela equipe de engenharia de versão do FreeBSD. A próxima versão é a [FreeBSD 11](https://www.freebsd.org/releases/11.0R/schedule.html).

## Implantando uma máquina virtual FreeBSD
A implantação de uma máquina virtual FreeBSD é um processo simples que usa uma imagem do [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/).

## Extensões de VM para FreeBSD
Os itens a seguir têm suporte de extensões de VM no FreeBSD.

### VMAccess

A extensão [VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) pode:

- Redefinir a senha do usuário sudo original.
- Criar um novo usuário sudo com a senha especificada.
- Definir a chave pública do host com a chave fornecida.
- Redefinir a chave pública do host fornecida durante o provisionamento da VM se a chave do host não for fornecida.
- Abrir a porta (22) SSH e restaurar o sshd\_config se reset\_ssh estiver definido como true.
- Remover o usuário existente.
- Verificar os discos.
- Reparar o disco adicionado.

### CustomScript

A extensão [CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) pode:

- Se for fornecida, baixar os scripts personalizados do Armazenamento do Azure ou do armazenamento público externo (por exemplo, GitHub).
- Executar o script de ponto de entrada.
- Oferecer suporte ao comando embutido.
- Converter automaticamente o estilo newline do Windows em scripts de Shell e Python.
- Remover automaticamente BOM em scripts de Shell e Python.
- Proteger dados confidenciais em CommandToExecute.

## Autenticação: nomes de usuário, senhas e chaves SSH
Ao criar uma máquina virtual FreeBSD usando o Portal do Azure, você deve fornecer um nome de usuário, uma senha ou uma chave pública SSH. Nomes de usuário para implantar uma máquina virtual de FreeBSD no Azure não devem corresponder aos nomes de contas do sistema (UID < 100) já presentes na máquina virtual ("raiz", por exemplo). Atualmente, há suporte apenas para a chave RSA SSH. Uma chave SSH com várias linhas deve começar com “--- BEGIN SSH2 PUBLIC KEY ---” e terminar com “--- END SSH2 PUBLIC KEY ---”.

## Obtendo privilégios de superusuário
A conta de usuário especificada durante a implantação da instância de máquina virtual no Azure é uma conta privilegiada. O pacote do sudo foi instalado na imagem de FreeBSD publicada. Depois de fazer logon usando essa conta de usuário, você poderá executar comandos como raiz usando a sintaxe de comando.

    # sudo <COMMAND>

Opcionalmente, você pode obter um shell de root usando sudo -s.

## Próximas etapas
- Acesse o [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/) para criar uma VM FreeBSD.
- Se você quiser levar seu próprio FreeBSD para o Azure, veja [Criar e carregar um VHD FreeBSD no Azure](../virtual-machines-linux-classic-freebsd-create-upload-vhd.md).

<!---HONumber=AcomDC_0914_2016-->