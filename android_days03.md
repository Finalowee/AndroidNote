#1.网络请求
###主线程阻塞
* UI停止刷新，应用无法响应用户操作
* home按键有效，home有安卓系统处理，返回键应用处理
* 耗时操作不应该在主线程进行
* ANR异常
	* 应用无响应异常
	* 主线程阻塞时间过长会抛出该异常 
	* 代码问题或者硬件问题
* 主线程又称为UI线程，只有在主线程在可以刷新UI 
* 子线程可以通过handler对象来刷新UI
###消息队列机制
* 主线程创建时，系统会创建出消息队列（message queue）和消息轮询器  （looper）
* looper不停地检测消息队列中是否有消息
* 消息队列一旦有消息，Looper就会将消息对象传给Handler对象，处理器将会调用HandlerMessage方法，该方法运行在主线程，可以修改UI
#
	package com.panhcuang.image_iewer;

	import java.io.File;
	import java.io.FileOutputStream;
	import java.io.InputStream;
	import java.net.HttpURLConnection;
	import java.net.URL;
	import java.net.URLConnection;
	
	import android.app.Activity;
	import android.graphics.Bitmap;
	import android.graphics.BitmapFactory;
	import android.os.Bundle;
	import android.os.Handler;
	import android.os.Message;
	import android.view.View;
	import android.widget.ImageView;
	import android.widget.Toast;
	
	public class MainActivity extends Activity {

		static ImageView iv;
		static MainActivity ma;
		//生成handler对象，重写handlerMEssage方法
		static Handler handler = new Handler(){
			@Override
			public void handleMessage(Message msg) {
				super.handleMessage(msg);
				switch (msg.what) {
				case 0:
					Toast.makeText(ma, "请求失败", 0).show();
					break;
				case 1:
					iv.setImageBitmap((Bitmap)msg.obj);
					break;
				default:
					break;
				}
				
			}
		};

		@Override
		protected void onCreate(Bundle savedInstanceState) {
			super.onCreate(savedInstanceState);
			setContentView(R.layout.activity_main);
			
			iv = (ImageView)findViewById(R.id.iv);
			ma = this;
		}
		
		public void bt_viewer(View v){
			//确定图片地址
			final String path = "http://img2.imgtn.bdimg.com/it/u=2162766932,244861494&fm=21&gp=0.jpg";
	
			final File file = new File(getCacheDir(), getFileName(path));
	
			if (file.exists()){
				Bitmap bm = BitmapFactory.decodeFile(file.getAbsolutePath());
				iv.setImageBitmap(bm);
			}
			else{
				Thread t = new Thread(){
					@Override
					public void run(){
										try {
							//将地址封装为url对象
							URL url = new URL(path);
							//获取连接对象并且转型
							HttpURLConnection uc = (HttpURLConnection)url.openConnection();
							//初始化连接对象
							//设置请求类型
							uc.setRequestMethod("GET");
							//连接超时
							uc.setConnectTimeout(5000);
							//读取超时
							uc.setReadTimeout(5000);
							//建立连接
							uc.connect();//该行可以不写
							//建立连接 ,获取服务器响应码，200说明成功
							if(uc.getResponseCode() == 200){
								//获取响应中的流,其中的数据即请求的数据
								InputStream is = uc.getInputStream();
								
								
								FileOutputStream os = new FileOutputStream(file);
								byte[] b = new byte[1024];
								int len = 0;
								while ((len = is.read(b)) != -1){
									os.write(b, 0, len);
								}
								os.close();
								//读取数据，构成位图
								Bitmap bm = BitmapFactory.decodeFile(file.getAbsolutePath());
								
								//创建消息对象
								Message msg = handler.obtainMessage();
								//消息对象携带数据
								msg.obj = bm;
								msg.what = 1;
								//发送出消息,传送给handlerMessage
								handler.sendMessage(msg);
							}
							else{
								Message msg = handler.obtainMessage();
								msg.what = 0;
								handler.sendMessage(msg);
							}
						
						} catch (Exception e) {
							// TODO: handle exception
							
							e.printStackTrace();
						}
					}
				};
				t.start();
			}
		}
		public String getFileName(String path){
			int index = path.lastIndexOf("/");
			return path.substring(index+ 1);
		}
	}

###get方法提交数据，将数据拼接在网址中
###post方法提交数据，将数据封装在请求头中
###HttpClient框架提交数据
###异步HttpClient数据提交
###多线程断点数据下载
#### java版本
1. 确定下载地址
2. 包装下载地址为http
3. 设置连接属性
4. 获取文件大小
5. 生成临时文件
6. 根据线程数，计算每个线程负责的下载部分
7. 创建线程并重写run方法
8. 在run方法内部进行文件的下载处理
9. 同时做好当前下载点的纪录以便断点续存
10. 下载完毕，删除断点续存的文件。
####android版本
> 该版本同java版本差不多，不过添加了进度条和进度百分比，以及清除缓存的功能代码如下
	
	package com.panchuang.muitldownloadwithreget;

	import java.io.BufferedReader;
	import java.io.File;
	import java.io.FileInputStream;
	import java.io.InputStream;
	import java.io.InputStreamReader;
	import java.io.RandomAccessFile;
	import java.net.HttpURLConnection;
	import java.net.URL;
	
	import android.app.Activity;
	import android.os.Bundle;
	import android.os.Environment;
	import android.os.Handler;
	import android.view.View;
	import android.widget.ProgressBar;
	import android.widget.TextView;
	
	public class MainActivity extends Activity {

	static int ThreadCount = 10;
	static int finishThread = 0;
	static int currentProgress;
	public String path = "http://dldir1.qq.com/music/clntupate/QQMusic_Setup_1205.exe";
	private ProgressBar pb_download;
	private TextView tv_progress;
	//TextView只能在主线程中进行刷新
	Handler handler = new Handler(){
		public void handleMessage(android.os.Message msg) {
			tv_progress.setText((long)pb_download.getProgress() * 100 / pb_download.getMax() + "%");
			if (msg.what == 2){
				tv_progress.setText("下载完毕");
			}
		}
	};

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);
		
		pb_download = (ProgressBar)findViewById(R.id.pb_download);	
		tv_progress = (TextView)findViewById(R.id.tv_progress);
		
		
	}
	//定义了一个按钮事件的清除缓存文件方法
	public void bt_delete(View v){
		for(int i =0;i < ThreadCount;i++){
			File f = new File(Environment.getExternalStorageDirectory(),i + ".txt");
			f.delete();
		}
	}
	//定义了一个按钮事件下载文件的方法
	public void bt_download(View v){
		Thread t =new Thread(){
			@Override
			public void run() {
				try {
					//封装网址，设置连接属性
					URL url = new URL(path);
					HttpURLConnection huc = (HttpURLConnection)url.openConnection();
					huc.setRequestMethod("GET");
					huc.setConnectTimeout(5000);
					huc.setReadTimeout(5000);
					//获取文件大小好生成临时文件占用空间
					if(huc.getResponseCode() == 200){
						int len = huc.getContentLength();
						//设置进度条总长
						pb_download.setMax(len);
						//生成临时文件
						File file = new File(Environment.getExternalStorageDirectory(),getFileName(path));
						RandomAccessFile raf = new RandomAccessFile(file, "rwd");
						raf.setLength(len);
						raf.close();
						//每个线程负责下载的部分的计算
						int size = len/ThreadCount;
						for(int i = 0;i < ThreadCount;i++){
							int startIndex = i * size;
							int endIndex = (i + 1) * size -1;
							if (i == ThreadCount){
								endIndex = len - 1;
							}
							//开启线程
						new DownLoadThread(startIndex, endIndex, i).start();
						}
					}
				} catch (Exception e) {
					e.printStackTrace();
				}		
			}
		};
		t.start();
	}
	//定义了从王志忠获取文件名字的方法
	public static String getFileName(String path){
		int index = path.lastIndexOf("/");
		return path.substring(index+ 1);
	}
	//定义内部类继承线程类
	class DownLoadThread extends Thread{
		int startIndex;
		int endIndex;
		int threadId;
		
		//重写构造方法，将下载文件以及线程分配信息传入
		public DownLoadThread(int startIndex, int endIndex, int threadId) {
			super();
			this.startIndex = startIndex;
			this.endIndex = endIndex;
			this.threadId = threadId;
		}


		//重写run方法
		@Override
		public void run() {		
			try {
				//下载前检测是否有下载记录
				File progressFile = new File(Environment.getExternalStorageDirectory(),threadId+".txt");
				if(progressFile.exists()){
					FileInputStream fis = new FileInputStream(progressFile);
					BufferedReader br = new BufferedReader(new InputStreamReader(fis));
					int lastProgress = Integer.parseInt(br.readLine());
					startIndex += lastProgress;
					fis.close();
					currentProgress += lastProgress;
					pb_download.setProgress(currentProgress);
					handler.sendEmptyMessage(1);
				}
				URL url = new URL(path);
				HttpURLConnection huc = (HttpURLConnection)url.openConnection();
				huc.setRequestMethod("GET");
				huc.setReadTimeout(5000);
				huc.setReadTimeout(5000);
				//请求部分数据
				huc.setRequestProperty("Range","bytes="+startIndex+"-"+endIndex);
				//请求部分数据成功响应头为206
				if(huc.getResponseCode() == 206){
					InputStream is = huc.getInputStream();
					byte[] b = new byte[1024];
					int len = 0;
					int total = 0;
					File file = new File(Environment.getExternalStorageDirectory(),getFileName(path));
					RandomAccessFile raf = new RandomAccessFile(file, "rwd");
					
					raf.seek(startIndex);
					System.out.println(threadId+","+startIndex);
					while((len = is.read(b)) != -1){
						raf.write(b, 0, len);
						total +=len;
						//生成记录下载进度的文本文件
						RandomAccessFile progressRaf = new RandomAccessFile(progressFile, "rwd");
						progressRaf.write((total+"").getBytes());
						progressRaf.close();
						//设置当前进度
						currentProgress += len;
						pb_download.setProgress(currentProgress);
						
						handler.sendEmptyMessage(1);
					}
					raf.close();
					finishThread++;
					synchronized (path) {
						if(finishThread == ThreadCount){
							for(int i =0;i < ThreadCount;i++){
								File f = new File(Environment.getExternalStorageDirectory(),i + ".txt");
								f.delete();
							}
							finishThread = 0;
							handler.sendEmptyMessage(2);
						}
					}
					
				}
			} catch (Exception e) {
				e.printStackTrace();
			}
			
		}
		}
	}