```css
/*定义滚动条高宽及背景 高宽分别对应横竖滚动条的尺寸*/
::-webkit-scrollbar{   
    width: 10px;    
    background-color: #fff;
}/*定义滚动条轨道 内阴影+圆角*/
::-webkit-scrollbar-track{  
    -webkit-box-shadow: inset 0 0 6px rgba(0,0,0,0.3);  
    border-radius: 10px;   
    background-color: lightgray;
}
/*定义滑块 内阴影+圆角*/
::-webkit-scrollbar-thumb{   
    border-radius: 10px;  
    -webkit-box-shadow: inset 0 0 6px rgba(0,0,0,.3); 
    background-color: blue;
}
/*定义最上方和最下方的按钮*/
::-webkit-scrollbar-button{ 
    background-color: #000;  
    border:1px solid yellow;
}
```

### span设置为inline-block之后不对齐

添加：设置垂直对齐方式

```css
vertical-align: bottom;
```

