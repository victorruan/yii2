Yii2中实现了mysql方式的mutex。可以看看 yii\mutex\MysqlMutex 的实现。
```php
/**
     * Acquires lock by given name.
     * @param string $name of the lock to be acquired.
     * @param int $timeout time (in seconds) to wait for lock to become released.
     * @return bool acquiring result.
     * @see http://dev.mysql.com/doc/refman/5.0/en/miscellaneous-functions.html#function_get-lock
     */
    protected function acquireLock($name, $timeout = 0)
    {
        return $this->db->useMaster(function ($db) use ($name, $timeout) {
            /** @var \yii\db\Connection $db */
            return (bool) $db->createCommand(
                'SELECT GET_LOCK(:name, :timeout)',
                [':name' => $name, ':timeout' => $timeout]
            )->queryScalar();
        });
    }
```