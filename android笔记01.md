#Android项目目录结构
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
	string:字符串资源文件
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
		android:icon="@drawable/ic_launcher"
        android:label="@string/app_name"/>
	
		