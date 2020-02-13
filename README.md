<h1 align="center"> Annotation-test-unit (ATU) </h1>

<p align="center">A phpunit Tool Base on annotation and laravel.一个基于注解和laravel的单元测试包. 

</p>

### 这个扩展包有啥用? 
<p>
1.改变你的开发方式. 改完代码,切换浏览器/postman 请求接口 烦不烦? 
    
2.顺带完成高覆盖测试.
    
后面配个视频在这里!~

qq交流群:814333044
</p>

## Installing

1.composer 安装
```shell
$ composer require iblues/annotation-test-unit -vvv
```
2.配置好单元测试,教程:
<https://www.w3cschool.cn/intellij_idea_doc/using_phpunit_framework.html>
PS:有效的设置测试环境可以避免脏正式数据.

3.找一个控制器.增加注解.

```php
    //请确保use以下
    use Iblues\AnnotationTestUnit\Annotation as ATU;

    //请确认有匹配的路由, 程序是根据路由的映射表进行查找. 如果路由映射错误, 会无法执行. 后期会处理这种问题
    /**
     * @ATU\Api(
     *     @ATU\Now(),
     * )
     */
    public function index(Request $request){//...}

    /**
     * 请确保 xx/xx/1有数据
     * @ATU\Api(
     *     "path":1   
     *      @ATU\Now(),
     * )
     */
    public function show(Request $request){//...}

   /**
     * @ATU\Api(
     *     @ATU\Now(),
     *     @ATU\Request({"title":122}),
     *     @ATU\Response({
     *      "data":{"id":true,"title":122}
     *     })
     * )
     */
    public function store($id,Request $request){//...}
```
4.执行以下命令,会创建tests/api/AtuTest.php. 测试该文件即可. Tips: ctrl+r / 开启toggle auto test 即可重新运行测试,加快效率!
```shell script
php artisan vendor:publish --tag ATU
```
[See File](https://github.com/iblues/annotation-test-unit/blob/master/src/Publish/tests/Api/AtuTest.php).
    
## 如何更爽快的coding?
### 怎么爽快?
<p>
 1.有完整的代码提示.
 
 2.可以注解快速跳转.方便快速查看代码和文档
</p>

### 安装插件
1.安装phpstorm插件.

 https://plugins.jetbrains.com/plugin/index?xmlId=de.espend.idea.php.annotation
 
 2.设置插件
 use Alisa:
 Iblues\AnnotationTestUnit\Annotation  as  ATU
 
 
## Usage

### 文档说明
```
注意逗号!


@ATU\Now //代表执行的测试要执行这个.避免全部执行很慢. 在Test\Api中

//@ATUDebug //返回的内容都打印出来
//@ATUTransaction true //事务默认就是开启.可以不设置

@ATU\Api (代表是api的测试)
@ATU\Api( path = http://baidu.com , method=GET)
@ATU\Api( path = /api/test/test/1 , method=POST)
@ATU\Api( path = 1) (会自动寻找匹配的路由 等于 /api/test/test/1)

@ATU\Tag("tag1")  //用于标记分类.
@ATU\Tag(["tag1","tag2"]) 

@ATU\Request({1:21})   //json参数
//@ATU\Request({file:@storage(12.txt)} ) //代表文件路径的 未完成

//@ATUBefore(/test/test/TestBoot()); //优先调其他的, 未完成
//@ATUBefore(@ATU('expressOrder')); //会将请求和返回的结果存下来 再议

@ATU\Response( 200 )  //默认就是200
@ATU\Response({
  "data":true,
  "data":{
    {"id":true}
   },
   @ATU\TestAssert(...), //等等其他的断言 未完成. 
}),


$Test\Login(false|100|0) // false的时候不登录,  100指定用户id为100的  0随意获取一个用户 

//可以传入@Response和@request 会处理成对应值返回.
@ATU\Assert("assertDatabaseHas",{"user",
    { "title" : @ATU\Response('data.title") }
}),

@ATU\Before("doSomeThing",{"tesst"});

```
### DEMO

1.简易版 请求看是不是返回200
```
@ATU\Api(
   @ATU\Now(),
   @ATU\Request(),
   @ATU\Response({
      "data":true
   }),
)
```
2.验证返回结果版本 , 并输出debug
```
@ATU\Api(
   @ATU\Now(),
   @ATU\Debug(),
   @ATU\Request(),
   @ATU\Response({
      "data":true,
      "data":{
        {"id":true}
       }
   }),
)
```
3.复杂版本.同一个控制器 多种请求.多个返回结果;assert调用其他断言(可以调用自定义函数)
```

@ATU\Api(
  @ATU\Now(),
  @ATU\Before(/test/test::function),
  @ATU\Request(),
  @ATU\Response(200,{
   "code":true,
   "data":{{"id":true,"user":true}},
    @ATU\Now(),@ATU\Debug()
  }),
  @ATU\Debug()
)

@ATU\Api(
  @ATU\Now(),
  @ATU\Request({"title":122}),
  @ATU\Response({
   "data":{"title":122}
  }),
  @ATU\Debug()
),


@ATU\Api(
  @ATU\Now(),
  @ATU\Request({"title":1}),
  @ATU\Response(422,{
   "data":{"title":true}
  }),
  @ATU\Debug()
)

复杂版本. 囊括了大部分用法

@ATU\Api(
  path = 1,
  method = "PUT",
  @ATU\Now(),
  @ATU\Request({"title":"测试","content":123}),
  @ATU\Response({
     "data":{"id":true,"title":"测试"}
    },
    @ATU\Assert("assertSee",{"测试"}),
    @ATU\Assert("assertSee",{@ATU\GetRequest("title")}),
    @ATU\Assert("assertJson", {{"data":@ATU\GetRequest}} ),
    @ATU\Assert("assertOk"),
  ),
  @ATU\Assert("assertDatabaseHas",{"test_test",{"id":1}} ),
  @ATU\Assert("assertDatabaseHas",{"test_test",@ATU\GetRequest()}),
  @ATU\Assert("assertDatabaseHas",{"test_test",
   { "title" : @ATU\GetResponse("data.title") }
  }),
),


@ATU\Api(
  @ATU\Now(),
  @ATU\Before("createTest",{"param":"not use"}),
  @ATU\Request({"title":"测试","content":@ATU\GetParam("test.title")}),
  @ATU\Response({
     "data":{"id":true,"title":@ATU\GetRequest("title")}
    },
    @ATU\Assert("assertSee",{"测试"}),
    @ATU\Assert("assertSee",{@ATU\GetRequest("title")}),
    @ATU\Assert("assertJson", {{"data":@ATU\GetRequest}} ),
    @ATU\Assert("assertOk"),
  ),
  @ATU\Assert("assertDatabaseHas",{"test_test",{"id":1}} ),
  @ATU\Assert("assertDatabaseHas",{"test_test",@ATU\GetRequest()} ),
  @ATU\Assert("assertDatabaseHas",{"test_test",
   { "title" : @ATU\GetResponse("data.title") }
  }),
),


@ATU\Api(
  @ATU\Now(),
  path={"http://baidu.com/",@ATU\GetParam("test.id"),"/otherUrl"},
  method="GET",
  @ATU\Before("createTest"),
  @ATU\Response({
   "status":1,
   "data":{"title":@ATU\GetParam("test.title")},
  }),
  @ATU\Assert("assertDatabaseHas",{"test_test",{"title":@ATU\GetParam("test.title")} }),
)
```

4.暂时忽略
```
@ATU\Api(
  @ATU\Ignore
)
```

5.调用模板 未完成
```
@ATU\Api(
    @ATU\Larfree('tes.test')
)

//然后定义? 
```



## 其他  函数的常规测试 未完成的 未完成
```
@ATUNow
@assert(1,2) == 3
```

## FAQ
Q: 报错 got '@' at position
A: 注解错误,  经常是少了逗号.


Q: 报错  got ''' 
A: 注解中请用双引号. 单引号不行. 如@ATU\Before("login");
## TodoList
@ATU\
- [x] Api
- [x] Now
- [x] Request
- [x] getRequest
- [ ] Request 文件上传,随机种子
- [x] Response
- [x] getResponse
- [ ] Response,正则和高级规则支持
- [x] Before
- [x] Degbug
- [x] Assert
- [x] Response
- [x] GetParam
- [ ] Name ,Tag
- [x] response 关于 GetRequest和GetParam
- [x] request 关于 getParam
- [x] before 关于 getParam
- [x] Assert 关于 getParam
- [x] Ignore
- [x] RouteIgnore 忽略路由检查,(第三方扩展包中:写了注释,但是不一定绑定路由的用)

## Contributing

You can contribute in one of three ways:

1. File bug reports using the [issue tracker](https://github.com/iblues/annotation-test-unit/issues).
2. Answer questions or fix bugs on the [issue tracker](https://github.com/iblues/annotation-test-unit/issues).
3. Contribute new features or update the wiki.

_The code contribution process is not very formal. You just need to make sure that you follow the PSR-0, PSR-1, and PSR-2 coding guidelines. Any new code contributions must be accompanied by unit tests where applicable._

## License

MIT