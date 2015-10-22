#android测试&测试框架
###按照岗位划分
* 黑盒测试：测试逻辑业务
* 白盒测试：测试逻辑方法（工资高，比程序员略低）
###按照测试粒度
* 方法测试	function test
* 单元测试	unit test
* 集成测试	integration
* 系统测试	system test
###按照暴力程度分
* 冒烟测试	smoke test
* 压力测试	pressure test:	一般针对服务器
***	
	
#单元测试（junit）
* 测试框架所在位置与测试项目无关,同时需在manifest中申明
* 继承AndroidTestCase类   
#
	<instrumentation   
     android:name="android.test.InstrumentationTestRunner"
     android:targetPackage="com.panchuang.junit">
	</instrumentation>
* 定义使用类库  
#
	<uses-library   
        android:name="android.test.runner" />
* 断言：检测期望值与实际值是否一致
#	
	assertEquals(wish,result);
* 若没有返回值，可测代码逻辑是否有bug


#SQLite数据库的使用
* 保存在内部存储空间
* 不检测数据类型，都已String存储,但是不会保存错误的数据类型
* SQLiteOPenHelper 
* 重写构造函数
#
	public MySQLiteOH(Context context, String name, CursorFactory factory, int version) {
		super(context, name, factory, version);
		}
	//单元测试
	public class TestCase extends AndroidTestCase {
	public void test(){
		MySQLiteOH oh = new MySQLiteOH(getContext(), "people.db", null, 1);
		//若数据库不存在，先创建，再打开可读写数据库
		SQLiteDatabase db = oh.getWritableDatabase();
		//若存储空间已满，则发挥只读数据库
		//SQLiteDatabase db = oh.getReadableDatabase();		
		}
	}
####增加
	public void insert(){
		
		db.execSQL("insert into persion(name,salary,phone)values(?,?,?)",new Object[]{"马兰","0","888666666"});

		
	}
####删除 
	public void delete(){
		oh = new MySQLiteOH(getContext(), "people.db", null, 1);
		db = oh.getWritableDatabase();
		db.execSQL("delete from persion where name = ?", new Object[]{"马"});
		
	}
####修改
	protected void setUp() throws Exception {
		//测试方法执行之前执行此方法
		super.setUp();
		oh = new MySQLiteOH(getContext(), "people.db", null, 1);
		db = oh.getWritableDatabase();
	}
####查询
	@Override
	protected void tearDown() throws Exception {
		// 测试方法执行之后调用此方法
		super.tearDown();
		db.close();
	}
####使用api实现增删改查
	MySQLiteOH oh = new MySQLiteOH(this, "people.db", null, 1);
		//若数据库不存在，先创建，再打开可读写数据库
		SQLiteDatabase db = oh.getWritableDatabase();
			ContentValues values = new ContentValues();
			for(int i=0;i<50;i++){
				values.put("name", "张"+ i);
				values.put("salary", i+"200");
				values.put("phone", "125634" + i + i);
				//注意
				db.insert("person", null, values);
				values.clear();
			}


####事物
	try{
			db.beginTransaction();
			ContentValues values = new ContentValues();
			values.put("salary","1300");
			db.update("person", values, "name = ?", new String[]{"马玉"});
			values.clear();
			values.put("salary","1500");
			db.update("person", values, "name = ?", new String[]{"马兰"});
			db.setTransactionSuccessful();
		}catch(Exception e){
			e.printStackTrace();
		}
		finally{
			//关闭事务，同时提交
			//若设置成功则生效否则数据回滚
			db.endTransaction();
		}

####ListView
* MVC架构
	>M:模型层----javaBean--personlist  
	>V:视图层----jsp-----------ListView       
	>C:控制层----servlet------adapter
>
	Listview  lv = (LIsteView)findViewById(R.id.lv);
	lv.setAdapter(adapter);
* 实现ListAdapter接口，定义继承BasAdapter的类,或者使用匿名内部类
* ListView中每一个条目都是View对象
	class MyAdapter extends BaseAdapter{

		@Override
		public int getCount() {
			// TODO Auto-generated method stub
			return personList.size();
		}
		@Override
		public View getView(int position, View convertView, ViewGroup parent) {
			// psition:该条目处于listview的序号，从0开始
			TextView tv = new TextView(MainActivity.this);
			Person p = personList.get(position);
			//能显示多少就创建多少TextView
			//先是万不立即销毁，会缓存
			tv.setText(p.toString());
			tv.setTextSize(18);
			return tv;
		}		
		@Override
		public Object getItem(int position) {
			// TODO Auto-generated method stub
			return null;
		}
		@Override
		public long getItemId(int position) {
			// TODO Auto-generated method stub
			return 0;
		}		
	}
####利用布局填充器做美化,利用缓存，优化为
	Person p = personList.get(position);
			//布局填充器填充对象
			View v = null;
			//缓存判断
			if(convertView == null){
				v = View.inflate(MainActivity.this, R.layout.item_listview, null);
			}else{
				v = convertView;
			}
			
			//信息填入
			TextView tv_name = (TextView)v.findViewById(R.id.tv_name);
			tv_name.setText(p.getName());
			TextView tv_Phone = (TextView)v.findViewById(R.id.tv_phone);
			tv_Phone.setText(p.getPhone());
			TextView tv_salary = (TextView)v.findViewById(R.id.tv_salary);
			tv_salary.setText(p.getSalary());
			return v;
####Arrayadapter&SimpleAdapter
	ListView lv = (ListView)findViewById(R.id.lv);
	//String [] objects = new String[]{"老大","老二","小名"};
	//lv.setAdapter(new ArrayAdapter<String>(this, R.layout.item_listview, R.id.tv, objects));
	// 只能处理单一数据类型
		
	List<Map<String,Object>> data = new ArrayList<Map<String,Object>>();
		
	Map<String,Object> map1 = new HashMap<String,Object>();
	map1.put("photo",R.drawable.no4);
	map1.put("name","乌尔奇奥拉");
	data.add(map1);
	
	Map<String,Object> map2 = new HashMap<String,Object>();
	map2.put("photo",R.drawable.ic_launcher);
	map2.put("name","安卓机器人");
	data.add(map2);
	lv.setAdapter(new SimpleAdapter(this,data,R.layout.item_listview, new 
	String[]{"name","photo"}, new int[]{R.id.tv_name,R.id.iv_photo}));

####弹出对话框
* 确定取消弹出框

		public void click1(View v){
			AlertDialog.Builder builder = new Builder(this);
			builder.setIcon(android.R.drawable.alert_light_frame);
			
			builder.setTitle("当心");
			builder.setMessage("危险操作");
			builder.setPositiveButton("确定", new OnClickListener() {
				
				@Override
				public void onClick(DialogInterface dialog, int which) {
					// TODO Auto-generated method stub
					Toast.makeText(MainActivity.this, "你会后悔的", 1).show();
				}
			});
			builder.setNegativeButton("取消", new OnClickListener() {
				
				@Override
				public void onClick(DialogInterface dialog, int which) {
					// TODO Auto-generated method stub
					Toast.makeText(MainActivity.this, "算你聪明", 1).show();
					
				}
			});
			AlertDialog ad = builder.create();
			ad.show();
		}

* 单选弹出框


		public void click2(View v){
			AlertDialog.Builder builder = new Builder(this);
			builder.setTitle("您喜欢的人性别是：");
			
			final String[] items = new String[]{"男","女"};
			
			builder.setSingleChoiceItems(items, -1, new OnClickListener() {
				
				@Override
				public void onClick(DialogInterface dialog, int which) {
					Toast.makeText(MainActivity.this, "您喜欢"+ items[which]+"人", 0).show();
					dialog.dismiss();
				}
			});
			builder.show();
		}
* 多选弹出框

		public void click3(View v){
			AlertDialog.Builder builder = new Builder(this);
			builder.setTitle("请选择您喜欢的动漫人物");
			
			final String[] items = new String[]{"四枫院夜一","宫小路瑞惠","立华奏","御庭摘希"};
			final boolean[] checkedItems = new boolean[]{false,false,false,false};
			
			builder.setMultiChoiceItems(items, checkedItems, new OnMultiChoiceClickListener() {
				
				@Override
				public void onClick(DialogInterface dialog, int which, boolean isChecked) {
					// TODO Auto-generated method stub
					checkedItems[which] = isChecked;
					
				}
			});
			builder.setPositiveButton("确定", new OnClickListener() {
				
				@Override
				public void onClick(DialogInterface dialog, int which) {
					// TODO Auto-generated method stub
					String text = "";
					for (int i = 0; i<4 ;i ++){
						text += checkedItems[i] ?items[i]+" " : "";
					}
					Toast.makeText(MainActivity.this, "您喜欢的动漫人物：" + text, 0).show();
					dialog.dismiss();
				}
			});
			builder.show();
		}

___
2015/10/20 13:37:46 
___
