---
title:  WKWebView禁止放大缩小捏合手势
categories:  [iOS]
tags:  [wkwebview]
---

### 禁止放大缩小捏合手势

```
func webView(_ webView: WKWebView, didFinish navigation: WKNavigation!){
    let js = "var script = document.createElement('meta');"
    + "script.name = 'viewport';"
    + "script.content=\"width=device-width, user-scalable=no\";"
    + "document.getElementsByTagName('head')[0].appendChild(script);"
    webView.evaluateJavaScript(js, completionHandler: nil)
}
```

