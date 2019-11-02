[中文网](https://www.angularjs.net.cn/)

特点

- 数据绑定：Angular数据绑定是双向的
- MVVM模式：使代码解耦
- 依赖注入
- 指令
- HTML模板和拓展HTML



#### 数据绑定

```javascript
function MyController($scope){
    $scope.clock = {
        now:new Date()
    };
    var updateClock = function(){
      $scope.clock.now = new Date;  
    };
    setInterval(function(){
        $scope.$apply(updateClock);
    },1000);
    updateClock();
}
```

#### 模块

AngularJS允许我们使用angular.module()方法来声明模块，这个方法能够接受两个参数，第一个是模块名称（字符串），第二个是依赖列表，也就是可以被注入到模块中的对象列表（字符串数组，每个元素都是一个模块名称，本模块依赖于这些模块，依赖需要在本模块加载之前由注入器进行预加载）。

```javasc
angular.module('myApp',[])
```

这个方法相当于AngularJS模块的setter方法，是用来定于模块的。

调用这个方法时如果只传递一个参数，就可以用它来引用模块。例如，可以通过以下代码来引用myApp模块：

```javasc
angular.module('myApp');
```

