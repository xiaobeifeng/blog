## js 调用 原生

1. 原生端需要配置

```objc
_configuration = [[WKWebViewConfiguration alloc] init];
_configuration.userContentController = [WKUserContentController new];
[_configuration.userContentController addScriptMessageHandler:self name:@"requestOcr"];
```

在 WKWebView 代理中: message.name -> 方法名  message.body -> 参数

```obcj
- (void)userContentController:(WKUserContentController *)userContentController didReceiveScriptMessage:(WKScriptMessage *)message {
    
    NSLog(@"%@", message);
    if ([message.name isEqualToString:@"requestOcr"]) {
        
        NSLog(@"%@", message.body);
        
    }
    
}
```

具体实现参照：Snippet/js与WKWebView交互示例

2. JS 写法

```
window.webkit.messageHandlers.requestOcr.postMessage("0");
```

## 原生调用 js

1. 原生端写法

```objc
        /// 传值给h5
        NSString *j2j = [NSString stringWithFormat:@"onResponseForNative('%@')", cardDataClearString];
        
        /// 执行JS
        [self.webView evaluateJavaScript:j2j completionHandler:^(id _Nullable response, NSError * _Nullable error) {
            NSLog(@"value: %@ error: %@", response, error);
        }];
```

2. js端获取参数

```
function onResponseForNative(value) {
                document.getElementById("myTextArea").innerText = value;
        }
```


参考：

[WKWebView的使用之JS调用OC](https://www.jianshu.com/p/9b4f7f6d47da)

PS: 如下为html写法示例

```html
<!DOCTYPE html>
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html;charset=utf-8">
</head>
 <body>
 	<p>身份证检测 H5 测试页</p>
 	<p><button onclick="handlePortraitEvent()">人像面</button></p>
	<p><button onclick="handleNationalEmblemEvent()">国徽面</button></p>
    <p><textarea id= "myTextArea" cols= "80 " rows= "10 "></textarea></p>
 	<script>
 		function handlePortraitEvent() { 
 			window.webkit.messageHandlers.requestOcr.postMessage("0");
 		}
        function handleNationalEmblemEvent() {
            window.webkit.messageHandlers.requestOcr.postMessage("1");
        }
        function onResponseForNative(value) {
                document.getElementById("myTextArea").innerText = value;
        }
 	</script>
 </body>
<html>

```


