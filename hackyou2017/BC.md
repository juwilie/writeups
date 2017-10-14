Link: https://hy17-bc.spb.ctf.su/

Зайдя на страницу видим калькулятор и ссылку на его исходный код (https://hy17-bc.spb.ctf.su/?source). 
Из сурцов нас интересуют несколько строк:
```python
    if (preg_match('#[g-wy-z]#si', $expr)) { 
        echo "<span class='err'>Only hex letters A-F can be used</span>"; 
    }else{
    ...
    $output = trim(shell_exec("bc 2>&1 <<<'$expr'")); 
    }
```


```php
if (preg_match('#[g-wy-z]#si', $expr))
```
Эта строка проверяет ввод на запрещенные символы.  Конкретнее - исключает символы от g до w и от y до z из введенного текста.
Больше никакой фильтрации ввода на видим.
