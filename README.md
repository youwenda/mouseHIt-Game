## [step-01]:建立APP脚手架

将代码放置于任意Web Server下,通过http访问index.html,即可看到包含"Hello Magix!"的页面.

![step-01](http://gtms03.alicdn.com/tps/i3/T1bUOvFo4XXXa_Izjg-1030-564.png)

### 1.建立单页

index.html(文件名可任意指定):

```
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8" />
		<title></title>
	</head>
	<body>
		<script type="text/javascript" src="http://a.tbcdn.cn/s/kissy/1.3.0/seed.js"></script>
		<script type="text/javascript" src="http://a.tbcdn.cn/apps/e/magix/1.0/kissy-magix.js"></script>
		<script type="text/javascript">
			Magix.start({
				appHome : 'src/',
				debug : true
			});
		</script>
	</body>
</html>
```
* 引入kissy1.3的种子文件
* 引入kissy-magix.js --基于kissy的magix版本
* 调用Magix.start()方法,传入配置对象,启动Magix.配置对象信息:
  * appHome: Magix运行需要一个名为app的物理目录,默认与当前html同级,通过appHome指定app目录相对于当前html的位置.这个目录将作为Kissy的Package:app目录.
  * debug: 开启debug模式
  * 其他配置项详见: @API[Magix.html#.start]

  

### 2.建立app/ini配置模块
Magix在启动时一定会载入app/ini模块,读取配置信息,配置信息中最重要的信息是路由规则.

app/ini(模块名固定):

```
KISSY.add('app/ini', function(S) {
    return {
        defaultView: 'app/common/views/default',
        routes: function(pathname) {
            return this.defaultView;
        }
    };
});
```
Magix基于树状层次化的结构构建View,每个逻辑页面都有且只有一个根View.所以在app/ini模块中,仅需配置每个pathname和其rootview之间的对应关系. 详见: @GUIDE[Maigx View模型]

根view往往决定页面的基础布局,多数APP的基础布局很少改变(如:头\尾\侧边\主区域模式),所以多数情况下pathname对应着相同的view,即defaultView.

* 本例中,routes方法定义了任意pathname,都采用defaultView
* defaultView的值是一个view的模块名,本例中是"app/common/views/default"

**TODO**

1.通过系列Develop Guide文档, 介绍Magix的相关设计.



### 3.建立defaultView 
在Magix中,采用模板系统辅助View的构建,默认情况下每个View包含两个同名的.js和.html文件,前者为View的模块文件,而后者为模板文件.

app/common/views/default.js : 

```
KISSY.add("app/common/views/default", function(S, View) {
	return View.extend({
		render : function() {
			this.setViewHTML(this.template);
		}
	});
}, {
	requires : ['magix/view']
});
```

app/common/views/default.html : 

```
Hello Magix!
```

* 所有view都需继承magix/view
* 重写render方法,调用setViewHTML方法,将模板内容(this.template)写入View的容器.关于View的生命周期,详见@GUIDE[View's Lifecycle]

Magix将View和View的容器做了区分,View的容器默认为一个VFrame对象,对应页面中的一个&lt;vframe&gt;节点.不同的View可以在同一个Vframe内装载和卸载.详见:@GUIDE[View VFrame VOM]

因为页面中只有一个根View,idnex.html没有指定的情况下,Magix会自动在body下追加一个id为"magix_vf_root"的节点,作为根view的容器.

**注意**

在开发模式中,模板的获取通过xhr,这要求js和模板文件与APP住HTML同域,在生产环境中,将通过编译工具,将模板直接编译到对应的js模块当中,这样就可以将js放在其他域名(CDN)下了.



### Summary
在第一阶段,我们完成了后续建立APP过程中所必须的程序脚手架的搭建:

1. index.html:单页应用的唯一物理页面
2. app.ini:初始化配置模块,记录路由配置信息
3. defaultView:第一个view,包含模块JS文件和模板HTML文件

接下来我们将在根view内划分若干子view,完成APP的主体布局框架.

