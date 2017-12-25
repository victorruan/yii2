# **mutex\(互斥\)**

###### 使用例子

```php
 if ($mutex->acquire($mutexName)) {
     // business logic execution
 } else {
     // execution is blocked!
 }
```

##### 文件锁

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



