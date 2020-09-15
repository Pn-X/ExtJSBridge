## Installation

Add the following line to Podfile, then run command 'pod install' at project path

```
pod 'ExtJSBridge'
```

### Start

Here a simple demo to help you start with ExtJSBridge and WKWebview

##### 0x0 Create Module

1. Create your own module

   ``` objective-c
   //EnvModule.h
   
   @interface EnvModule : ExtJSModule
   ```

2. Provide a module name

   ```objective-c
   //EnvModule.m
   
   + (NSString *)moduleName {
       return @"env";
   }
   ```

3. Add method

   ```objective-c
   //EnvModule.m
   
   //sync method must have 1 argument and return value
   EXT_JS_SYNC_METHOD(platform) {
     return "iOS";
   }
   
   //async method must have 2 arguments, return value is optional
   EXT_JS_ASYNC_METHOD(networkType) {
     dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(2 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
     	callback(ExtJSCallbackFunctionSuccess, "wifi");
     });
   }
   ```

4. Export methods 

   ```objective-c
   //EnvModule.m
   
   //no:async, yes:sync, you must set it correctly
   + (NSDictionary *)exportMethods {
       return @{
           @"platform":@YES, 
           @"networkType":@NO
       };
   }
   ```

##### 0x1 Register Module

1. Register module to ExtJSModuleFactory

   ``` objective-c
   //AppDelegate.m
   
   [[ExtJSModuleFactory singleton] registerModuleClass:[EnvModule class]];
   ```

2. Initialize bridge

   ``` objective-c
   //WebViewController.m
   
   - (void)viewDidLoad {
     [super viewDidLoad];
     [self.webView ext_initializeBridge];
     //load url
   }
   ```

##### 0x2 Use Module 

1. Import webview-runtime.min.js file

   ``` html
   //index.html
   <script type="text/javascript" src="webview-runtime.min.js"></script>
   ```

2. Call ext.loader.requireModule("env") to install the env module and create env module instance

   ```javascript
   //index.html
   const env = ext.loader.requireModule("env");
   ```

3. Call any method that exported form native!

   ``` html
   //index.html
   <!DOCTYPE html>
   <html lang="en">
   <head></head>
   <body>
       <script type="text/javascript" src="webview-runtime.min.js"></script>
       <script>
       const env = ext.loader.requireModule("env");
       
       let platform = env.platform();
       console.log("platform : " + platform);
       
       env.networkType({
           onSuccess:function(res) {
               console.log("network state : " + res);
           }
       });
       </script>
   </body>
   </html>
   ```

4. Safari console will output：

   ``` 
   platform : ios
   network state : wifi
   ```

### Advanced Usage

You can run the exmaple project or found it in [Wiki](https://github.com/Pn-X/ExtJSBridge/wiki) 
