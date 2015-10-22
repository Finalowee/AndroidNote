#1、Android项目目录结构#
##src##
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
#
	ls:
	ps:列出设备当前的所有进程
* netstat -ano:查看端口情况
***
# 3、第一个项目-电话拨号器
* system/app/phone.apk  打电话应用,上层无法访问。除非做系统级应用，才能重写。
##点击事件侦听的4种写法
1. 定义内部类
2. 匿名内部类
3. android:onClick()；需要在context中定义同名的public void menthod（），通常是在Activity中

#4、第二个项目短信发送器
* 该api可直接访问
* android:onClick("example");
>public void example(View)
> 通过对view对象判断调用example的控件

***
#5、布局
##LinearLay0out
* 组件彼此不重叠
	>dp:长度单位，字体大小推荐sp
* 从外往里写
* layout_gravity 对齐方式
	>可用 | 运算符 分隔开            
	>水平布局下水平居中左右对齐不生效             
	>竖直布局下竖直居中顶部和底部对齐不生效
* layout_width、layout_heigth  
* layout_weight 
	>权重按比例分配屏幕的剩余宽度或高度.   
	>会先计算初始宽度或高度，然后剩余再用权重计算     
	>若要按比例分配，可将宽度或高度设置为0dp

##RelativeLayout
* 没有方向和权重
* 组件会重叠
* alignParent 
	>对齐父元素
* toxxOf(@id/x) above
	>对齐控件
* align
	>对齐控件

##FrameLayout
* 少部分api会要求只能使用帧布局
* 设置对齐方式和线性布局相同
* 组建可重叠，和相对布局相同
* 组件之间独立，和相对布局不同。
* 和线性布局不同，没有方向

##TableLayout
* tableRow 书香决定布局行数
* 每个tableRow有几个控件则有几列
* tableRow中的控件宽高可不写，写了也无效
* 在TableLayout  android:stretchColums  拉伸列
* android：layout_colum 改变列号  无法提示出来
* android：layout_span 占用列数   无法提示出来
* 分割线一般用TextView来做，有默认宽度，高度设为1dp背景色灰即可 
##AbsooluteLayout
* 手机应用中绝对用不到
* 用坐标值决定控件位置，会受分辨率影响
* 智能电视开发可能会用到
* 智能电视就两种分辨率
***
#6、logcat
###等级
	选中后可看到该等级及以上信息
* 冗余
* debug
* info  正常信息
* warm	警告信息
* error 错误信息
###搜索
###Filters
	过滤器，一般用tag标签信息过滤
###Log
	专用的打印api    
	log.x （"tag","message"）;
***
#7、android系统中数据的读写
##android的存储结构
###内部存储空间
>ram    
>rom
###外部存储空间
>sd卡   
>一般手机的自带存储空间都是外部存储空间   
>sd路径：   
1. sdcard             	2.3之前    
2. mnt/sdcard			4.3之前    
3. storage/sdcard		4.3之后    
>sd卡写入信息需要权限    
>4.0之前读取不需要权限，之后可以有可以没有，在手机中设置
>sdcard有多重状态，可用api查询   
>可获取sd卡的剩余容量大小
#
	File path = Environment.getExternalStorageDirectory();
	StatFs stat = new StatFs(path.getPath());
	long blockSize = stat.getBlockSizeLong();
	long totalBlocks = stat.getBlockCountLong();
	long availableBlocks = stat.getAvailableBlocksLong(); 
###文件访问权限
#drwxrwxrwx
* 第一个字母
>d:文件夹   
>-:文件   
#
* 后面的9个字母(owner)(group)(others)
> r：读   
> w：写  
> x：执行  

###SharePreference
* 用来保存零散数据
###pull解析器
	IntputStream is = getClassLoader.getResourceAsStream(filename) 
	//获取资源文件
	XmlPullParser xp = Xml.newPullParser();//拿到pull解析器
	try{
		xp.setInput(is,"gbk");//初始化
		
		int type = xp.getEventType();//获取结点事件类型

		while(type != XmlPullParser.END_DOCUMENT){
			//根据结点类型进行操作
		City arrayList;
			switch(type){
				case XmlPullParser.START_TAG:
					if("weather".equals(xp.getName())){
						cityList = new ArrayList<City>();
					}else if("city".equals(xp.getName)){
						city = new City();			
					}else if("name".equals(xp.getName())){
						String name = xp.nextText();//这里是获取的当前结点下一结点文本信息，并将指针移到当前节点的结束节点处
						city.setName(name);
					}省略...
				case XmlPullParser.END_TAG:
					if("city".equals(xp.getName())){
						cityList.add(city);
				}
			}
		}
	}
	