# **mutex\(互斥\)**

* 如果获取不到锁，支持添加最大等待时间

###### 使用例子一

```php
 if ($mutex->acquire($mutexName)) {
     // 执行业务逻辑
 } else {
     // execution is blocked!
 }
```

###### 使用例子二

```php
if ($mutex->acquire($mutexName,10)) {
    //如果不能获取锁，就等待10秒
} else {
    // execution is blocked!
}
```

* ###### 存放文件的路径

```php
public $mutexPath = '@runtime/mutex';
```

* ###### 如何根据锁名称获取锁文件路径

```php
protected function getLockFilePath($name)
{
   return $this->mutexPath . DIRECTORY_SEPARATOR . md5($name) . '.lock';
}
```

* ###### 是否自动释放

```php
public $autoRelease = true;
```

```php
$mutex = new \yii\mutex\FileMutex(['autoRelease'=>false]);
if($mutex->acquire('mutex_name')){
    echo "创建的文件不会删除";
}
```



