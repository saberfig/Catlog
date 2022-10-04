# 目录
[Android程序结构](#一Android程序结构)


---


# 一、Android程序结构

- manifests 安卓应用的全局描述文件
    - AndroidManifest.xml
        ```xml
        <!--配置整个应用的属性-->
        <application
            android:allowBackup="true"
            android:icon="@mipmap/ic_launcher"
            android:label="@string/app_name"
            android:roundIcon="@mipmap/ic_launcher_round"
            android:supportsRtl="true"
            android:theme="@style/AppTheme">
            <!--创建活动-->
            <activity android:name=".MainActivity">
                <!--程序入口配置-->
                <intent-filter>
                    <action android:name="android.intent.action.MAIN"/>
                    <category android:name="android.intent.category.LAUNCHER"/>
                </intent-filter>
            </activity>
        </application>
        ```
- Java 安卓应用中所有的包和Java源文件
    - 源码
    - Test（单元测试）

- res 显示保存在res中的资源文件
    - drawable（位图图片，9
     patch图片）
    - layout 安卓应用的布局文件
- mipmap 程序的启动图标
- values
    - colors.xml
    - demens.xml 尺寸资源
    - strings.xml 字符串资源
    - styles.xml 样式资源


安卓中会自动创建一个R文件，每创建一个资源都会在R文件中创建一个索引，可以通过索引引用资源


## 二、控件

### 弹出框


### 数据库

### adapter


