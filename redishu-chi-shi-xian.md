Yii2中实现了redis方式的mutex。可以看看 yii\redis\Mutex 的实现。

```php
 /**
     * Acquires a lock by name.
     * @param string $name of the lock to be acquired. Must be unique.
     * @param int $timeout time (in seconds) to wait for lock to be released. Defaults to `0` meaning that method will return
     * false immediately in case lock was already acquired.
     * @return bool lock acquiring result.
     */
    protected function acquireLock($name, $timeout = 0)
    {
        $key = $this->calculateKey($name);
        $value = Yii::$app->security->generateRandomString(20);
        $waitTime = 0;
        while (!$this->redis->executeCommand('SET', [$key, $value, 'NX', 'PX', (int) ($this->expire * 1000)])) {
            $waitTime++;
            if ($waitTime > $timeout) {
                return false;
            }
            sleep(1);
        }
        $this->_lockValues[$name] = $value;
        return true;
    }

    /**
     * Releases acquired lock. This method will return `false` in case the lock was not found or Redis command failed.
     * @param string $name of the lock to be released. This lock must already exist.
     * @return bool lock release result: `false` in case named lock was not found or Redis command failed.
     */
    protected function releaseLock($name)
    {
        static $releaseLuaScript = <<<LUA
if redis.call("GET",KEYS[1])==ARGV[1] then
    return redis.call("DEL",KEYS[1])
else
    return 0
end
LUA;
        if (!isset($this->_lockValues[$name]) || !$this->redis->executeCommand('EVAL', [
                $releaseLuaScript,
                1,
                $this->calculateKey($name),
                $this->_lockValues[$name]
            ])) {
            return false;
        } else {
            unset($this->_lockValues[$name]);
            return true;
        }
    }

    /**
     * Generates a unique key used for storing the mutex in Redis.
     * @param string $name mutex name.
     * @return string a safe cache key associated with the mutex name.
     */
    protected function calculateKey($name)
    {
        return $this->keyPrefix . md5(json_encode([__CLASS__, $name]));
    }
```



