# php-httpsqs-client ver 0.2.1 #

  * 客户端作者网站：http://phprimer.com/viewtopic.php?f=3&t=1089
  * HttpSQS项目地址：http://blog.s135.com/httpsqs/


---

# 版本更新 #
0.2.1 修正：
  * 反复请求内存溢出bug修正

0.2 修改：
  * 增加对象调用方式，0.2版本同时支持函数调用与对象调用

0.1.1 修改：
  * 修正句柄连接时内存错误的bug


---

# 安装说明 #
```
mkdir php_httpsqs_client
cd php_httpsqs_client
unzip php_httpsqs_0.1.zip
/usr/local/php/bin/phpize
./configure --enable-httpsqs --with-php-config=/usr/local/php/bin/php-config
make && make install
#接着在php.ini中添加一行
extension=httpsqs.so;
```


---

# 函数说明 #

```
/**
 * 创建httpsqs连接
 * @param string $host 服务器地址，可以为空，默认为127.0.0.1
 * @param int    $port 服务器端口，可以为空，默认为1218
 * @return resource
 */
$hr = httpsqs_connect("127.0.0.1", 1218);
```

```
/**
 * 写入队列数据
 * @param resource $hr      服务器连接句柄
 * @param string   $queue   队列名称
 * @param string   $data    写入数据
 * @param string   $charset 字符集，可以为空，默认为utf-8
 * @return boolean
 */
$putRes = httpsqs_put($hr, "testQueue", "This is a test Data", "UTF-8");
```

```
/** 
 * 获取队列最后一条数据
 * @param resource $hr
 * @param string   $queue
 * @param boolean  $return_array 是否返回数组，可以为空，默认为false
          返回数组格式：array('pos'=>'队列插入点', 'data'=>'数据值')
 * @param string   $charset 可以为空
 * @return mixed
 */
$content = httpsqs_get($hr, "testQueue", true, "UTF-8");
```

```
/**
 * 获取队列状态
 * @param resource $hr
 * @param string   $queue
 * @param boolean  $return_json 是否返回状态的json格式，可以为空，默认为false
 * @return string
 */
$status = httpsqs_status($hr, "testQueue", true);
```

```
/**
 * 获取队列某个点数据
 * @param resource $hr
 * @param string   $queue
 * @param int      $pos 要获取的某条数据的位置
 * @param string   $charset 可以为空
 * @return string
 */
$posData = httpsqs_view($hr, "testQueue", 10, "UTF-8");
```

```
/**
 * 队列重置
 * @param resource $hr
 * @param string   $queue
 * @return boolean
 */
$resetRes = httpsqs_reset($hr, "testQueue");
```

```
/**
 * 设置队列最大数据条数
 * @param resource $hr
 * @param string   $queue
 * @param int      $maxqueue 队列最大数据条数
 * @return boolean
 */
$maxqueueRes = httpsqs_maxqueue($hr, "testQueue", 10000);
```

```
/**
 * 修改定时刷新内存缓冲区内容到磁盘的间隔时间
 * @param resource $hr
 * @param string   $queue
 * @param int      $synctime 间隔时间
 * @return boolean
 */
$synctimeRes = httpsqs_synctime($hr, "testQueue", 10);
```


---

# 对象调用 #
```
// 参数与httpsqs_connect对应
$hr = new HttpSQS($host, $port);

// 参数与httpsqs_get对应
$hr->get($queuename, $return_array, $charset);

// 参数与httpsqs_put对应
$hr->put($queuename, $data, $charset);

// 参数与httpsqs_status对应
$hr->status($queuename, $return_json);

// 参数与httpsqs_view对应
$hr->view($queuename, $pos);

// 参数与httpsqs_reset对应
$hr->reset($queuename);

// 参数与httpsqs_maxqueue对应
$hr->maxqueue($queuename);

// 参数与httpsqs_synctime对应
$hr->synctime($queuename);
```


---

# 示例 #
```
// 取数据Daemon
$hr = httpsqs_connect($host, $port);
while (1) {
    $data = httpsqs_get($hr, $queuename, $charset);
    if ($data === false) {
       sleep(1);
    } else {
      // do something...
    }
}

// 或者
$hr = new HttpSQS($host, $port);
while (1) {
    $data = $hr->get($queuename, $charset);
    if ($data === false) {
        sleep(1);
    } else {
      // do something...
    }
}

// 写数据
$hr = httpsqs_connect($hort, $port);
httpsqs_put($hr, $queuename, $data, $charset);

// 或者
$hr = new HttpSQS($hort, $port);
$hr->put($queuename, $data, $charset);
```