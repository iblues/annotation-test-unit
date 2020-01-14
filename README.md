<h1 align="center"> Annotation-test-unit </h1>

<p align="center">A PhpUnit Tool Base on annotation and laravel .
一个基于注解和laravel单元测试的 自动化测试包

</p>

### 这个扩展包有啥用? 
<p>
1.改变你的开发方式. 改完代码,切换浏览器/postman 请求接口 烦不烦? 
    
2.顺带完成高覆盖测试.
    
后面配个视频在这里!~
</p>

## Installing

1.composer 安装
```shell
1.$ composer require iblues/annotation-test-unit -vvv
```
2.配置好单元测试( 相关教程请百度)

3.找一个控制器.增加注解.

```
    //请确保use以下
    use Iblues\AnnotationTestUnit\Annotation as Test;

    //请确认有匹配的路由, 程序是根据路由的映射表进行查找. 如果路由映射错误, 会无法执行. 后期会处理这种问题
    /**
     * @Test\Api(
     *     @Test\Now(),
     * )
     */
    public function index(Request $request)
    ....

    /**
     * 请确保 xx/xx/1有数据
     * @Test\Api(
     *     "path":1   
     *      @Test\Now(),
     * )
     */
    public function show(Request $request)
    ....

   /**
     * @Test\Api(
     *     @Test\Now(),
     *     @Test\Request({"title":122}),
     *     @Test\Response({
     *      "data":{"id":true,"title":122}
     *     })
     * )
     */
    public function store($id,Request $request)
    ....
```
4.创建单元测试文件,运行即可. Tips: ctrl+r / 开启toggle auto test 即可重新运行测试,加快效率!
```
<?php
namespace Tests\Api;

use Iblues\AnnotationTestUnit\Traits\ApiTest;
use Illuminate\Foundation\Testing\DatabaseTransactions;
use Tests\TestCase;

/**
 * 注解测试
 * @author Blues
 */
class AnnotationTest extends TestCase
{
    //DatabaseTransactions自动开启事务. 这样不会写入数据库. 但是注意!事务外的应用读不起到数据库的写入值.
    use ApiTest,DatabaseTransactions;

    public function setUp(): void
    {
        //阻止commit提交导致脏数据库. 配合DatabaseTransactions同时使用
        $mock = \Mockery::mock('alias:\DB');
        $mock->shouldReceive('commit')
            ->withAnyArgs()
            ->andReturn('true');

        parent::setUp();
    }

    /**
     * 测试带有@test\Api和@test\Now注解的
     */
    public function testNow(){
        $this->doNow();
    }

    /**
     * 测试带有@test\Api()注解的
     */
    public function testAll(){
       $this->doAll();
    }

}
```

    
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
 Iblues\AnnotationTestUnit\Annotation  as  Test
 
 


## Usage

### 文档说明
```
注意逗号!


@Test\Now //代表执行的测试要执行这个.避免全部执行很慢. 在Test\Api中

//@testDebug //返回的内容都打印出来
//@testTransaction true //事务默认就是开启.可以不设置

@Test\Api (代表是api的测试)
@Test\Api( path = http://baidu.com , method=GET)
@Test\Api( path = /api/test/test/1 , method=POST)
@Test\Api( path = 1) (会自动寻找匹配的路由 等于 /api/test/test/1)

@test\Request({1:21})   //json参数
//@test\Request({file:@storage(12.txt)} ) //代表文件路径的 未完成

//@testBefore(/test/test/TestBoot()); //优先调其他的, 未完成
//@testBefore(@test('expressOrder')); //会将请求和返回的结果存下来 再议

@test\Response( 200 )  //默认就是200
@Test\Response({
  "data":true,
  "data":{
    {"id":true}
   },
   @Test\TestAssert(...), //等等其他的断言 未完成. 
}),


@TestBefore() //请求前调用
@TestAssert() //请求后调用

```
### DEMO

1.简易版 请求看是不是返回200
```
@Test\Api(
   @Test\Now(),
   @Test\Request(),
   @Test\Response({
      "data":true
   }),
)
```
2.验证返回结果版本
```
@Test\Api(
   @Test\Now(),
   @Test\Request(),
   @Test\Response({
      "data":true,
      "data":{
        {"id":true}
       }
   }),
)
```
3.复杂版本.同一个控制器 多种请求.多个返回结果
```

@Test\Api(
  @Test\Now(),
  @Test\Before(/test/test::function),
  @Test\Request(),
  @Test\Response(200,{
   "code":true,
   "data":{{"id":true,"user":true}},
    @Test\Now(),@Test\Debug()
  }),
  @Test\Debug()
)




@Test\Api(
  @Test\Now(),
  @Test\Request({"title":122}),
  @Test\Response({
   "data":{"title":122}
  }),
  @Test\Debug()
),


@Test\Api(
  @Test\Now(),
  @Test\Request({"title":1}),
  @Test\Response(422,{
   "data":{"title":true}
  }),
  @Test\Debug()
)
```

4.结合larfree
```
@Test\Api //完了. 所有参数自动构建.
```

5.调用模板
```
@Test\Api(
    @Test\Larfree('tes.test')
)

//然后定义? 
```



## 其他  函数的常规测试 未完成的 待续
```
@TestNow
@assert(1,2) == 3
```

## FAQ
Q: 报错 got '@' at position
A: 注解错误,  经常是少了逗号.


## TodoList
- [ ] testApi
- [x] testNow
- [x] testParam
- [ ] testParam 文件上传支持
- [x] testResponse
- [ ] testResponse,正则和高级规则支持
- [ ] testBefore
- [x] testResponse
- [ ] testTransaction
- [ ] testDebug
- [ ] testName , testBefore

## Contributing

You can contribute in one of three ways:

1. File bug reports using the [issue tracker](https://github.com/iblues/annotation-test-unit/issues).
2. Answer questions or fix bugs on the [issue tracker](https://github.com/iblues/annotation-test-unit/issues).
3. Contribute new features or update the wiki.

_The code contribution process is not very formal. You just need to make sure that you follow the PSR-0, PSR-1, and PSR-2 coding guidelines. Any new code contributions must be accompanied by unit tests where applicable._

## License

MIT