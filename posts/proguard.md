#  混淆详解

> Android Proguard(混淆): http://www.jianshu.com/p/60e82aafcfd0

> 资源混淆:美团 http://tech.meituan.com/mt-android-resource-obfuscation.html

> 微信:http://mp.weixin.qq.com/s?__biz=MzAwNDY1ODY2OQ==&mid=208135658&idx=1&sn=ac9bd6b4927e9e82f9fa14e396183a8f&scene=0#rd

## Tag 未整理
   ```
   -keepnames class com.packagename.ClassName$* {
    public <fields>;
    public <methods>;
}

这种方式可以禁止混淆内部类。
   ```

## 1.如何启用ProGuard
    在build.gradle中进行配置
```
android {
    buildTypes {
        release {
            minifyEnabled true
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
}
```
如上面的代码所示，在release打包时就会按照我们的配置进行混淆，注意，在我们平时的debug时是不会进行混淆的。

## 2.如何配置ProGuard

>具体详情请查看官网：http://proguard.sourceforge.net/

### 1.部分语法
>后面的文件名，类名，或者包名等可以使用占位符代替  
"?"表示一个字符
可以匹配多个字符，但是如果是一个类，不会匹配其前面的包名  
 "*"可以匹配多个字符，会匹配前面的包名。

  * 输入输出选项
    * -include filename
    从给定的文件中读取配置参数
    * -injars class_path  
    输入(即使用的) jar文件路径
    * -outjars class_path  
    输出 jar 路径
    * -libraryjars class_path  
    指定的jar将不被混淆
    * -skipnonpubliclibraryclasses  
    跳过(不混淆) jars中的 非public classes
    * -dontskipnonpubliclibraryclasses  
    不跳过(混淆) jars中的 非public classes   默认选项
    * -dontskipnonpubliclibraryclassmembers  
    不跳过 jars中的非public classes的members
    * -keepdirectories [directory_filter]  
    指定目录 keep 在 out jars中  
  * 保持不变的选项（混淆不进行处理的内容）
    * -keep {Modifier} {class_specification}  
    保护指定的类文件和类的成员
    * -keepclassmembers {modifier} {class_specification}  
    保护指定类的成员，如果此类受到保护他们会保护的更好
    * -keepclasseswithmembers {class_specification}
    保护指定的类和类的成员，但条件是所有指定的类和类成员是要存在。
    * -keepnames {class_specification}
    保护指定的类和类的成员的名称（如果他们不会压缩步骤中删除）
    * -keepclassmembernames {class_specification}
    保护指定的类的成员的名称（如果他们不会压缩步骤中删除）
    * -keepclasseswithmembernames {class_specification}
    保护指定的类和类的成员的名称，如果所有指定的类成员出席（在压缩步骤之后）
    * -printseeds {filename}
    列出类和类的成员-keep选项的清单，标准输出到给定的文件
  * 压缩选项
    * -dontshrink  
    不启用 shrink。shrink操作默认启用，主要的作用是将一些无效代码给移除，即没有被显示调用的代码。
    * -printusage [filename]  
    打印被移除的代码，在标准输出
    * -whyareyoukeeping class_specification  
    打印 在shrink过程中 为什么有些代码被 keep
  * 优化选项
    * -dontoptimize  
    该选项表示 不启用。optimization，默认启用  
    当不使用该选项时，下面的才有效
    * -optimizations optimization_filter  
    根据optimization_filter指定要优化的文件
    * -optimizationpasses n  
    优化数量 n  
    * -assumenosideeffects class_specification  
    优化时允许访问并修改类和类的成员的 访问修饰符，可能作用域会变大。
    * -mergeinterfacesaggressively  
    合并接口，即使它们的实现类未实现合并后接口的所有方法。
  * 混淆选项
    * -dontobfuscate  
    不混淆
    * -printmapping [filename]  
    打印 映射旧名到新名
    * -applymapping filename  
    打印相关
    * -obfuscationdictionary filename  
    指定外部模糊字典
    * -classobfuscationdictionary filename  
    指定class模糊字典
    * -packageobfuscationdictionary filename  
    指定package模糊字典
    * -overloadaggressively  
    过度加载，多个属性和方法使用相同的名字，只是参数和返回类型不同 可能各种异常
    * -useuniqueclassmembernames  
    类和类成员都使用唯一的名字
    * -dontusemixedcaseclassnames  
    不使用大小写混合类名
    * -keeppackagenames [package_filter]  
    保持packagename 不混淆
    * -flattenpackagehierarchy [package_name]  
    指定重新打包,所有包重命名,这个选项会进一步模糊包名 好东西
将包里的类混淆成n个再重新打包到一个个的package中，注：混淆是有用，但是我用的时候安装会崩溃，不知道为什么？
    * -repackageclasses [package_name]  
    将包里的类混淆成n个再重新打包到一个统一的package中  会覆盖flattenpackagehierarchy选项
    * -keepattributes [attribute_filter]  
    混淆时可能被移除下面这些东西，如果想保留，需要用该选项。“Annotation、Exceptions, Signature, Deprecated, SourceFile, SourceDir, LineNumberTable”
  * 预校验选项
    * -dontpreverify  
    不预校验，默认选项
  * 通用选项
    * -verbose  
    打印日志
    * -dontnote [class_filter]  
    不打印某些错误
    * -dontwarn [class_filter]  
    不打印警告信息
    * -ignorewarnings  
    忽略警告，继续执行
    * -printconfiguration [filename]  
    打印配置文件
    * -dump [filename]  
    指定打印类结构

### 2.Demo实例
```
##--- For:android默认 ---
-optimizationpasses 5  # 指定代码的压缩级别
-allowaccessmodification #优化时允许访问并修改有修饰符的类和类的成员
-dontusemixedcaseclassnames  # 是否使用大小写混合
-dontskipnonpubliclibraryclasses  # 是否混淆第三方jar
-dontpreverify  # 混淆时是否做预校验
-verbose    # 混淆时是否记录日志
-ignorewarnings  # 忽略警告，避免打包时某些警告出现
-optimizations !code/simplification/arithmetic,!code/simplification/cast,!field/*,!class/merging/*  # 混淆时所采用的算法

-keepattributes *Annotation*
-keep public class com.google.vending.licensing.ILicensingService
-keep public class com.android.vending.licensing.ILicensingService
-keepclasseswithmembernames class * { # 保持 native 方法不被混淆
    native <methods>;
}

-keepclassmembers public class * extends android.view.View {
   void set*(***);
   *** get*();
}

-keepclassmembers class * extends android.app.Activity {
   public void *(android.view.View);
}

-keepclassmembers enum * {  # 保持枚举 enum 类不被混淆
    public static **[] values();
    public static ** valueOf(java.lang.String);
}

-keep class * implements android.os.Parcelable { # 保持 Parcelable 不被混淆
  public static final android.os.Parcelable$Creator *;
}

-keepclassmembers class **.R$* { #不混淆R文件
    public static <fields>;
}

-dontwarn android.support.**
##--- End android默认 ---

##--- For:不能被混淆的 ---
-keep public class * extends android.app.Activity
-keep public class * extends android.app.Fragment
-keep public class * extends android.app.Application
-keep public class * extends android.app.Service
-keep public class * extends android.content.BroadcastReceiver
-keep public class * extends android.content.ContentProvider
-keep public class * extends android.app.backup.BackupAgentHelper
-keep public class * extends android.preference.Preference

##--- For:保持自定义控件类不被混淆 ---
-keepclasseswithmembers class * {
    public <init>(android.content.Context, android.util.AttributeSet);
}
-keepclasseswithmembers class * {
    public <init>(android.content.Context, android.util.AttributeSet, int);
}
##--- For:android-support-v4 ---
-dontwarn android.support.v4.**
-keep class android.support.v4.** { *; }
-keep interface android.support.v4.app.** { *; }
-keep class * extends android.support.v4.** { *; }
-keep public class * extends android.support.v4.**
-keep public class * extends android.support.v4.widget
-keep class * extends android.support.v4.app.** {*;}
-keep class * extends android.support.v4.view.** {*;}

##--- For:Serializable ---
-keep class * implements java.io.Serializable {*;}
-keepnames class * implements java.io.Serializable
-keepclassmembers class * implements java.io.Serializable {*;}

##--- For:Gson ---
-keepattributes *Annotation*
-keep class sun.misc.Unsafe { *; }
-keep class com.idea.fifaalarmclock.entity.***
-keep class com.google.gson.stream.** { *; }

##--- For:Remove log ---
-assumenosideeffects class android.util.Log {
    public static boolean isLoggable(java.lang.String, int);
    public static int v(...);
    public static int i(...);
    public static int w(...);
    public static int d(...);
    public static int e(...);
}

##--- For:attributes(未启用) ---
#-keepattributes SourceFile,LineNumberTable # 保持反编译工具能看到代码的行数，以及release包安装后出现异常信息可以知道在哪行代码出现异常，建议不启用
-keepattributes *Annotation* #使用注解
-keepattributes Signature #过滤泛型  出现类型转换错误时，启用这个
#-keepattributes *Exceptions*,EnclosingMethod  #没试过，未知效果

```

## 3.如何查看ProGuard输出文件
>混淆之后，会给我们输出一些文件，在gradle方式下是在<project_dir>/build/proguard/目录下，ant是在<project_dir>/bin/proguard目录，eclipse构建在<project_dir>/proguard目录像。
分别有以下文件：
+ dump.txt 描述apk文件中所有类文件间的内部结构。
+ mapping.txt 列出了原始的类，方法，和字段名与混淆后代码之间的映射。
+ seeds.txt 列出了未被混淆的类和成员
+ usage.txt 列出了从apk中删除的代码

>当我们发布的release版本的程序出现bug时，可以通过以上文件（特别时mapping.txt）文件找到错误原始的位置，进行bug修改。同时，可能一开始的proguard配置有错误，也可以通过错误日志，根据这些文件，找到哪些文件不应该混淆，从而修改proguard的配置。

>注意：重新release编译后，这些文件会被覆盖，所以每次发布程序，最好都保存一份配置文件。

## 4.参考资料
* http://proguard.sourceforge.net/
* http://developer.android.com/intl/zh-cn/tools/help/proguard.html
* http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0113/2304.html
* http://blog.csdn.net/jjwwmlp456/article/details/44977721
