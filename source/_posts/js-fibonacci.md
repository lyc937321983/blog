---
title: fibonacci函数
date: 2022-07-01 09:38:22
tags: fibonacci
comment: 'valine'
categories: 
- js
---
# js 记忆函数

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>fibonacci</title>
</head>
<body>
    <script>
        var memoizer = function(memo, fundamental) {    //memo记忆数组和fundamental函数
            //管理memo存储、何时调用fundamental
            var shell = function(n) {
                var result = memo[n];                  
                if(typeof result !== 'number') {
                    result = fundamental(shell, n);
                    memo[n] = result;
                }
                return result;
            };
            return shell;
        };
        //用memoizer定义fibonacci函数
        var fibonacci = memoizer([0, 1], function(shell, n) {
            return shell(n - 1) + shell(n - 2);
        });
        
        console.log('111',fibonacci(10));
    </script>
</body>
</html>
```

