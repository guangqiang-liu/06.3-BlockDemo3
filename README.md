# 06.3-block的copy

在`ARC`环境下，编译器会根据情况自动地将栈内存上的block拷贝到堆内存上，以下几种情况编译器都会自动将栈上的block拷贝到堆

* block作为函数返回值
* 将block赋值给__strong修饰的强指针对象
* block作为Cocoa API中方法名含有usingBlock的方法参数
* block作为GCD API的方法参数

1、block作为函数的返回值时，代码如下：

```
typedef void(^MyBlock)(void);

MyBlock blockFun() {
    int a = 10;
    return ^{
        NSLog(@"%d", a); // 10
    };
}

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        // insert code here...
        MyBlock block = blockFun();
        
        // 执行block
        block();
        
        NSLog(@"%@", [block class]); // __NSMallocBlock__
    }
    return 0;
}
```

2、有强指针引用着block时，代码如下：

```
typedef void(^MyBlock)(void);

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        // insert code here...
        
        int a = 10;
        
        // 强指针block引用着^{NSLog(@"%d", a);}
        MyBlock block = ^{
            NSLog(@"%d", a); // 10
        };
        
        NSLog(@"%@", [block class]); // __NSMallocBlock__
        
        block();
        
        // 没有任何指针引用着^{NSLog(@"%d", a);}
        NSLog(@"%@", [^{
            NSLog(@"%d", a);
        } class]); // __NSStackBlock__
    }
    return 0;
}
```

3、block作为Cocoa API中方法名含有usingBlock的方法参数时，代码如下：

```
int main(int argc, const char * argv[]) {
    @autoreleasepool {
        // insert code here...
        
        NSArray *arr = @[@"1", @"2", @"3"];
        
        [arr enumerateObjectsUsingBlock:^(id  _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
            NSLog(@"%@", obj);
        }];
    }
    return 0;
}
```

4、block作为GCD API的方法参数时，代码如下：

```
int main(int argc, const char * argv[]) {
    @autoreleasepool {
        // insert code here...
        
        dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(1 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
            NSLog(@"111");
        });
    }
    return 0;
}
```

讲解示例代码Demo地址：[https://github.com/guangqiang-liu/06.3-BlockDemo3]()


## 更多文章
* ReactNative开源项目OneM(1200+star)：**[https://github.com/guangqiang-liu/OneM](https://github.com/guangqiang-liu/OneM)**：欢迎小伙伴们 **star**
* iOS组件化开发实战项目(500+star)：**[https://github.com/guangqiang-liu/iOS-Component-Pro]()**：欢迎小伙伴们 **star**
* 简书主页：包含多篇iOS和RN开发相关的技术文章[http://www.jianshu.com/u/023338566ca5](http://www.jianshu.com/u/023338566ca5) 欢迎小伙伴们：**多多关注，点赞**
* ReactNative QQ技术交流群(2000人)：**620792950** 欢迎小伙伴进群交流学习
* iOS QQ技术交流群：**678441305** 欢迎小伙伴进群交流学习