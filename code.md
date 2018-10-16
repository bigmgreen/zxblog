```css
// css 凹角实现，transparent 0, transparent 0（第二个用来消除锯齿）
div {
    width: 200px;
    height: 200px;
    margin: 100px auto 0;
    background-color: #654545;
    background-image: -webkit-radial-gradient(left top, #fff 5%, transparent 0, transparent 0), -webkit-radial-gradient(right top, #fff 5%, transparent 0, transparent 0), -webkit-radial-gradient(right bottom, #fff 5%, transparent 0, transparent 0), -webkit-radial-gradient(left bottom, #fff 5%, transparent 0, transparent 0);
    background-size: 200px 200px;
}
```
