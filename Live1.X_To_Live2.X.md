## 科技平台1.0到2.0升级文档

### 废弃接口(直接删除相关代码)

废弃接口: setLiveEnvironment: 如果APP中有此相关代码，直接删除即可。

```objc
/**
 * 设置环境变量,用于内部调试用,要在 [PALiveManager initSdk:accountType:]之前调用
 */
+ (void)setLiveEnvironment:(int)envStg;

```

### 适配接口

1. initSdk:accountType: ---> initSdkWithEnvironment:
  
	sdk初始化接口做了简化处理，app只需初始化运行环境即可。
	
	*示例：demo AppDelegate.m
	
	```objc
	- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
		...
	    
	    //初始化SDK
	    if (ENVIRONMENT_TEST)
	    {
	        //测试环境(stg1)
	        [[PALiveManager sharedInstance] initSdkWithEnvironment:EnvironmentTypeStg1];
	    }
	    else
	    {
	        //生产环境
	        [[PALiveManager sharedInstance] initSdkWithEnvironment:EnvironmentTypeStg1];
	    }
	    ...
	    return YES;
	}
```

2. setPAServerHost:userId:PAHttptoken ---> setPAUserId:PAHttptoken:

	此接口简化了参数，静默登录成功后直接设置，知鸟后台返回的用户userId和sid即可。

	*示例：demo LoginViewController.m
	
	```objc
	- (void)login:(NSString *)identifier passward:(NSString *)pwd
	{
	    LoadView *loginWaitView = [LoadView loadViewWith:@"正在登录"];
	    [self.view addSubview:loginWaitView];
	    
	    __weak typeof(self) ws = self;
	    
	    //登录知鸟直播业务服务器
	    [[httpLoginManager sharedInstance] login:identifier pwd:pwd completion:^(BOOL isSuccess, NSError *error) {
	        
	        [loginWaitView removeFromSuperview];
	        if (isSuccess)
	        {
	  			//登录成功后设置sdk当前用户信息
	            [[PALiveManager sharedInstance] setPAUserId:[httpLoginManager sharedInstance].userId PAHttptoken:[httpLoginManager sharedInstance].token];
	            [ws enterMainUI];
	        }
	    }];
	    
	}
