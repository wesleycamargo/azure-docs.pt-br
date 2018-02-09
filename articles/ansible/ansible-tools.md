---
title: Ferramentas para usar o Ansible com o Azure
description: Instalar e usar ferramentas individuais para Ansible com o Azure
ms.service: ansible
keywords: "ansible, azure, devops, ferramentas, vs code, visual studio code, extensão"
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 01/14/2018
ms.topic: article
ms.openlocfilehash: 73d31054bb0c40dd08bf4b4a93c31c59354b5bed
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2018
---
# <a name="tools-for-using-ansible-with-azure"></a>Ferramentas para usar o Ansible com o Azure

As seguintes ferramentas tornam o trabalho com o Ansible e o Azure mais fácil e eficiente.

## <a name="visual-studio-code-extension-for-ansible"></a>Extensão do Visual Studio Code para Ansible

A [extensão Visual Studio Code para Ansible](https://marketplace.visualstudio.com/items?itemName=vscoss.vscode-ansible) fornece vários recursos para usar o Ansible com o Visual Studio Code:

- Preenchimento automático de diretivas, módulos e plug-ins da documentação do Ansible conforme você digita.
- Trechos de código são exibidos ao clicar &lt;Ctrl>&lt;Espaço> durante a criação dos manuais do Ansible.
- O realce de sintaxe exibe o código do manual do Ansible em diferentes cores e fontes de acordo com a sintaxe YAML.
- Manuais do Ansible podem ser executados na janela no Terminal do Visual Studio Code.
    - (Somente Windows) O Ansible pode ser executado a partir de um contêiner do Docker.
    - (Linux e macOS) O Ansible pode ser executado a partir de um contêiner do Docker ou de uma instalação local do Ansible. 
- Os manuais Ansible podem ser executados no Azure Cloud Shell.