
## <a name="add-the-applications-registration-information-to-your-app"></a>Adicionar as informações de registro do aplicativo ao aplicativo

Nesta etapa, você precisa adicionar a ID do Aplicativo ao projeto:

1.  Em `ViewController.swift`, substitua a linha que começa com "`let kClientID`" por:
```swift
let kClientID = "[Enter the application Id here]"
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
Pressione CTRL e clique em <code>Info.plist</code> para abrir o menu contextual e, em seguida, clique em: <code>Open As</code> > <code>Source Code</code>
</li>
<li>
No nó raiz <code>dict</code>, adicione o seguinte:
</li>
</ol>

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>$(PRODUCT_BUNDLE_IDENTIFIER)</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msal[Enter the application Id here]</string>
            <string>auth</string>
        </array>
    </dict>
</array>
```
