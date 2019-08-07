# AutoBuryPoint
android 通过自定义gradle插件，在apk打包过程中使用ASM对class文件进行字节码插桩，轻量级AOP实现自动埋点技术。


# 项目中使用
### 1.在根目录的gradle文件中增加下配置：
build.gradle

```
repositories {
    jcenter()
    google()
    maven {
        url uri('https://raw.githubusercontent.com/zhang-hai/AutoBuryPoint/master')
    }
}

buildscript {
    repositories {
        jcenter()
        google()
        maven {
            url uri('https://raw.githubusercontent.com/zhang-hai/AutoBuryPoint/master')
        }
    }
    dependencies {
        classpath 'com.zhang-hai.burypoint:burypoint-plugin:0.1'
    }
}
```


### 2.在app项目的gradle文件中使用插件及api库
app.gradle
```
apply plugin: 'com.zhang-hai.burypoint'

dependencies {
    ...

    implementation 'com.zhang-hai.burypoint:burypoint-api:0.1'
}
```

### 3.项目中使用
##### 1.注解FastClickIntercept，拦截防按钮重复点击
该注解作用的method上，并且该method的要有一个View参数，否则不会插入拦截字节码。
```
@FastClickIntercept
@Override
public void onItemChildClick(BaseQuickAdapter adapter, View view, int position) {
    ...
}
```
##### 2.注解MethodTraceTime,检测方法执行耗时
该注解同样作用在method上，该注解无变量，仅用于检测方法耗时，在方法上增加此注解，在编译生成class阶段会自动插入字节码技术方法耗时。
```
@MethodTraceTime
private void init(){
    int p = 0;
    for (int i = 0;i < 30000;i++){
        p += i;
    }
    Log.i("Tag","(0 + 1 + 2 + ... + 30000) = " + p );
}
```

##### 3.注解AutoLog，自动增加日志
该注解作用在method上，有三个变量分别为：method、tag、msg，其中method是Android系统Log对应的静态方法名，tag即日志的TAG，msg即日志的内容。
```
@AutoLog(method = AutoLog.E,tag = "MainActivity",msg = "init "+content)
private void init(){
    int p = 0;
    for (int i = 0;i < 30000;i++){
        p += i;
    }
    Log.i("Tag","(0 + 1 + 2 + ... + 30000) = " + p );
}
```
目前该日志只能插入指定的内容，因为注解中msg变量无法采用像变量那样，内容动态设置，这个是该项目日志插桩的缺陷，目前也没想到更好的方法。
