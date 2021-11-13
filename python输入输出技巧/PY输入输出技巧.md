# PY输入输出

```py
a = int(input())
#输入一个int型数
arr = input("");    
#输入一个一维数组，每个数之间使空格隔开
num = [int(n) for n in arr.split()] 
#将输入每个数以空格键隔开做成数组，忽略前导空格和多个空格
```

python格式化输出不换行
```py
print(num, end='') #end默认是'\n'
```

有多组输入数据，文件结束停止输入。
```py
while True:
    try:
        a, b = map(int, raw_input().strip().split())
        print(a+b)
    except EOFError:
        break
```

