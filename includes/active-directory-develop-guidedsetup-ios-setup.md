---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 09/19/2018
ms.author: dadobali
ms.custom: include file
ms.openlocfilehash: e72c4b0cf8f77a057ff07f8bce7acae4e834e28d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59503000"
---
## <a name="setting-up-your-ios-application"></a>Configurando seu aplicativo iOS

Esta seção fornece instruções passo a passo para criar um novo projeto para demonstrar como integrar um aplicativo iOS (Swift) ao *Login com a Microsoft*, para que possa consultar APIs da Web que exigem um token.

> Prefere baixar este projeto de exemplo do XCode? [Baixe um projeto](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip) e vá para a [Etapa de configuração](#register-your-application) para configurar o exemplo de código antes de executá-lo.

## <a name="install-carthage-to-download-and-build-msal"></a>Instale o Carthage para baixar e criar a MSAL

O gerenciador de pacotes Carthage é usado durante o período de versão prévia da MSAL – ele se integra ao XCode enquanto mantém a capacidade da Microsoft de fazer alterações na biblioteca.

- Baixe e instale a versão mais recente do Carthage [aqui](https://github.com/Carthage/Carthage/releases "URL de download do Carthage").

## <a name="creating-your-application"></a>Criando seu aplicativo

1. Abra o Xcode e selecione **Crie um novo projeto Xcode**.
2. Selecione **iOS > aplicativo de exibição única** e selecione **próxima**.
3. Dê um nome de produto e selecione **próxima**.
4. Selecione uma pasta para criar seu aplicativo e clique em *Criar*

## <a name="build-the-msal-framework"></a>Criar a estrutura da MSAL

Siga as instruções abaixo para efetuar o pull e, em seguida, criar a versão mais recente das bibliotecas MSAL usando o Carthage:

1. Abra o terminal bash e vá para a pasta raiz do aplicativo.
2. Copiar o que está abaixo e cole no terminal de busca para criar um arquivo “Cartfile”:

```bash
echo "github \"AzureAD/microsoft-authentication-library-for-objc\" \"master\"" > Cartfile
```
<!-- Workaround for Docs conversion bug -->
<ol start="3">
<li>
Copie e cole o que está abaixo. Este comando busca dependências em uma pasta do Carthage/de check-outs e cria a biblioteca MSAL:
</li>
</ol>

```bash
carthage update
```

> O processo acima é usado para baixar e criar a MSAL (Biblioteca de Autenticação da Microsoft). A MSAL trata da aquisição, do armazenamento em cache e da atualização de tokens do usuário usados para acessar APIs protegidas pela plataforma de identidade da Microsoft.

## <a name="add-the-msal-framework-to-your-application"></a>Adicionar a estrutura da MSAL ao seu aplicativo

1. No Xcode, abra a guia **Geral**.
2. Vá para o **estruturas e bibliotecas vinculadas** seção e selecione **+**.
3. Selecione **Adicionar outro...**.
4. Selecione **Carthage> Build> iOS> MSAL.framework** e, em seguida, selecione **Open**. Você deve ver `MSAL.framework` adicionado à lista.
5. Vá para o **fases de compilação** guia, selecione o **+** ícone e, em seguida, selecione **nova fase de Script para executar**.
6. Adicione o seguinte conteúdo à *área de script*:

```text
/usr/local/bin/carthage copy-frameworks
```

<!-- Workaround for Docs conversion bug -->
<ol start="7">
<li>
Adicione o seguinte a <code>Input Files</code> clicando em <code>+</code>:
</li>
</ol>

```text
$(SRCROOT)/Carthage/Build/iOS/MSAL.framework
```

## <a name="creating-your-applications-ui"></a>Criando a interface do usuário de seu aplicativo

Um arquivo Main.storyboard deve ser criado automaticamente como parte do modelo de projeto. Siga as instruções abaixo para criar a interface do usuário do aplicativo:

1.  Pressione CTRL e clique em `Main.storyboard` para abrir o menu de contexto e, em seguida, clique em: `Open As` > `Source Code`
2.  Substitua o nó `<scenes>` pelo código a seguir:

```xml
 <scenes>
    <scene sceneID="tne-QT-ifu">
        <objects>
            <viewController id="BYZ-38-t0r" customClass="ViewController" customModule="MSALiOS" customModuleProvider="target" sceneMemberID="viewController">
                <layoutGuides>
                    <viewControllerLayoutGuide type="top" id="y3c-jy-aDJ"/>
                    <viewControllerLayoutGuide type="bottom" id="wfy-db-euE"/>
                </layoutGuides>
                <view key="view" contentMode="scaleToFill" id="8bC-Xf-vdC">
                    <rect key="frame" x="0.0" y="0.0" width="375" height="667"/>
                    <autoresizingMask key="autoresizingMask" widthSizable="YES" heightSizable="YES"/>
                    <subviews>
                        <label opaque="NO" userInteractionEnabled="NO" contentMode="left" horizontalHuggingPriority="251" verticalHuggingPriority="251" fixedFrame="YES" text="Microsoft Authentication Library" textAlignment="natural" lineBreakMode="tailTruncation" baselineAdjustment="alignBaselines" adjustsFontSizeToFit="NO" translatesAutoresizingMaskIntoConstraints="NO" id="ifd-fu-zjm">
                            <rect key="frame" x="64" y="28" width="246" height="21"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <fontDescription key="fontDescription" type="system" pointSize="17"/>
                            <nil key="textColor"/>
                            <nil key="highlightedColor"/>
                        </label>
                        <label opaque="NO" userInteractionEnabled="NO" contentMode="left" horizontalHuggingPriority="251" verticalHuggingPriority="251" fixedFrame="YES" text="Logging" textAlignment="natural" lineBreakMode="tailTruncation" baselineAdjustment="alignBaselines" adjustsFontSizeToFit="NO" translatesAutoresizingMaskIntoConstraints="NO" id="98g-dc-BPL">
                            <rect key="frame" x="16" y="277" width="62" height="21"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <fontDescription key="fontDescription" type="system" pointSize="17"/>
                            <nil key="textColor"/>
                            <nil key="highlightedColor"/>
                        </label>
                        <button opaque="NO" contentMode="scaleToFill" fixedFrame="YES" contentHorizontalAlignment="center" contentVerticalAlignment="center" buttonType="roundedRect" lineBreakMode="middleTruncation" translatesAutoresizingMaskIntoConstraints="NO" id="2rX-Vv-T1i">
                            <rect key="frame" x="87" y="100" width="200" height="30"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <state key="normal" title="Call Microsoft Graph API"/>
                            <connections>
                                <action selector="callGraphButton:" destination="BYZ-38-t0r" eventType="touchUpInside" id="Kx0-JL-Bv9"/>
                            </connections>
                        </button>
                        <textView clipsSubviews="YES" multipleTouchEnabled="YES" contentMode="scaleToFill" fixedFrame="YES" editable="NO" textAlignment="natural" selectable="NO" translatesAutoresizingMaskIntoConstraints="NO" id="qXW-2z-J7K">
                            <rect key="frame" x="16" y="324" width="343" height="291"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <color key="backgroundColor" white="1" alpha="1" colorSpace="calibratedWhite"/>
                            <accessibility key="accessibilityConfiguration">
                                <accessibilityTraits key="traits" updatesFrequently="YES"/>
                            </accessibility>
                            <fontDescription key="fontDescription" type="system" pointSize="14"/>
                            <textInputTraits key="textInputTraits" autocapitalizationType="sentences"/>
                        </textView>
                        <button opaque="NO" contentMode="scaleToFill" fixedFrame="YES" contentHorizontalAlignment="center" contentVerticalAlignment="center" buttonType="roundedRect" lineBreakMode="middleTruncation" translatesAutoresizingMaskIntoConstraints="NO" id="9u4-b8-vmX">
                            <rect key="frame" x="137" y="138" width="100" height="30"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <state key="normal" title="Sign Out"/>
                            <connections>
                                <action selector="signoutButton:" destination="BYZ-38-t0r" eventType="touchUpInside" id="kZT-P8-0Zy"/>
                            </connections>
                        </button>
                    </subviews>
                    <color key="backgroundColor" red="1" green="1" blue="1" alpha="1" colorSpace="custom" customColorSpace="sRGB"/>
                </view>
                <connections>
                    <outlet property="loggingText" destination="qXW-2z-J7K" id="uqO-Yw-AsK"/>
                    <outlet property="signoutButton" destination="9u4-b8-vmX" id="OCh-qk-ldv"/>
                </connections>
            </viewController>
            <placeholder placeholderIdentifier="IBFirstResponder" id="dkx-z0-nzr" sceneMemberID="firstResponder"/>
        </objects>
        <point key="canvasLocation" x="140" y="137.18140929535232"/>
    </scene>
</scenes>
```
