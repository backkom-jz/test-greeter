# test-greeter

// 代码
```
class MyGreeter
{
    // 静态变量保存类的唯一实例
    private static $instance;
    private mixed $dateTime;

    // 私有构造函数，防止外部实例化类
    private function __construct($currentTime = '')
    {
        // 初始化代码
        $this->dateTime =empty($currentTime)?(int)date('G'):$currentTime;
    }

    // 私有克隆函数，防止外部克隆类
    private function __clone()
    {
        // 禁止克隆
    }

    // 私有反序列化函数，防止外部反序列化类
    private function __wakeup()
    {
        // 禁止反序列化
    }

    // 静态方法获取类的唯一实例
    public static function getInstance(): MyGreeter
    {
        if (self::$instance === null) {
            self::$instance = new MyGreeter();
        }
        return self::$instance;
    }

    public function greeting(): string
    {
        // 获取当前小时（0-23）
        $currentHour = $this->dateTime;

        // 根据当前时间段返回不同的问候消息
        if ($currentHour >= 6 && $currentHour < 12) {
            return 'Good morning';
        } elseif ($currentHour >= 12 && $currentHour < 18) {
            return 'Good afternoon';
        } else {
            return 'Good evening';
        }
    }
}

// 单元测试修改

  protected function setUp(): void
    {
        $this->greeter = new MyGreeter();
    }

    /** @covers \Src\MyGreeter::__construct */
    public function testInitialization()
    {
        $this->assertInstanceOf(MyGreeter::class, $this->greeter);
    }
  /** @covers \Src\MyGreeter::greeting */
    public function testGreetingNotEmpty()
    {
        $greeting = $this->greeter->greeting();
        $this->assertNotEmpty($greeting, 'Greeting should not be empty');
    }

    /** @covers \Src\MyGreeter::greeting */
    public function testGreetingContent()
    {
        $expected = 'Good evening'; 
        $this->assertEquals($expected, $this->greeter->greeting());
    }
```
    
// dockerfile 优化
```
FROM php:8.3-cli-alpine

# 设置工作目录
WORKDIR /srv

# 安装 curl 和其他依赖
RUN apk add --no-cache curl

# 复制 composer 安装脚本
COPY install-composer.sh /usr/local/bin/
RUN chmod +x /usr/local/bin/install-composer.sh

# 安装 composer
RUN /usr/local/bin/install-composer.sh

# 复制其他文件（如你的应用代码）
COPY . .

# 运行 composer 更新
RUN php composer.phar update

# 运行 PHPUnit 测试
CMD ["php", "./vendor/bin/phpunit", "--testdox", "tests"]
```

// 脚本[Composer的安装过程放在一个单独的脚本中，提高可维护性]
```
#!/bin/sh
curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
```

