#1、Android项目目录结构
##src
	项目java代码
##gen
####buildConfig：应用配置文件;
####R：资源文件;
##Android5.1.1
	安卓jar包，导入之后应用才可运行
####android-suport:低版本支持高版本类库包，真实位置在libs下
##assets
	资源文件夹，存放视频、音乐等较大的文件，没有资源id，使用io流读取
##bin
	存放打包后编译的文件
####res 
####AndroidManifest
##libs
	存放第三方jar包

##res
	资源文件夹，其中的文件都有资源id(id可在R中查看)
####drawable:存放图片(hdpi、ldpi、mdpi、xhdpi、xxhdpi  系统自动匹配手机屏幕)
####layout：布局文件夹，android所有布局文件都是xml文件
####menu：菜单配置文件夹
####values:
	dimens:长度资源文件
	strings:字符串资源文件,一个values文件夹下只有一个strings
	styles:样式资源文件
##AndroidManifest
	安卓清单文件
***
    package="com.panchuang.test" <!--默认包名一致，但实际并无关系,可以不相同,是应用在系统中惟一的识别-->
	android:versionCode="1" <!--系统看的版本编号，大的版本应用可以此此值小的版本-->
	android:versinName="1.0" <!--给人看的版本号-->
	<uses-sdk
		android:minSdkVersion="22"
		android:tadgetSdkVersion="22"/>
	<application
		android:icon="@drawable/ic_launcher"<!--指定应用管理界面应用图标-->
        android:label="@string/app_name"><!--应用管理界面的应用名-->
		<activity
			android:name=" "
			android:icon
			android:label> <!--默认使用<application>d的icon和label-->
	 		<intent-fiter> <!--有这个子节点，则为入口activity-->
				<action android:name = "android.intent.action.MAIN"/>
				<category android:name="android.intent.categroy.launcher"/>
			</intent-filter>
		</activity>
	</application>
##project.properties
	项目设置

#2、程序打包&安装

##打包编译
* 安卓项目
* 编译及打包
* 生成apk=.dex+未编译资源+资源索引+AndroidManifest.xml
	>dex=classes
* 生成签名
	>防止包名相同而偶然或恶意覆盖，版权保护

##安装过程
* android launch
* adb is running normally
* performing com..Activity activity launch
* Automatic target mode 
* upload apk
* install apk
* success
* starting com..Activity(系统同时会自动删除安装包)

##安卓系统目录结构
* data/app:第三方应用安装路径
* system/app:系统级应用路径
* data/data:所有应用使用过程产生的文件保存在这儿，每个应用都拥有一个专属文件夹，无法跨包访问

##eclipse常用工具
* DDMS :dalvik debug monitor service
* logcat： 设备响应获取
* File Explorer:s设备系统刻录结构查看
* terminal： 控制台
##adb
	adb debug bridge
* adb start-server 
* adbkill-server
* adb install *.apk
* adb uninstall packageName
* adb devices
* adb shell
	#######ls
	#######ps:查看当前系统运行的所有进程