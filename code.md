```json
// vscode 扩展文件代码高亮
"files.associations": {
    "*.art": "html"
}
```

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

### 扒的别人代码（待整理）

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style>
        .buy {
            width: 300px;
            height: 300px;
            margin: 100px auto 0;
            text-align: center;
            position: relative;
            transform: translateZ(0);
        }

        body {
            background-color: #143568;
            line-height: 1.5;
            color: #fff;
            fill: currentColor;
            font-family: 'Helvetica Neue', 'PingFang SC', 'Myriad Pro', 'Hiragino Sans GB', 'microsoft yahei';
            -ms-user-select: none;
            -moz-user-select: none;
            -webkit-user-select: none;
            user-select: none;
            margin: 0;
            position: relative;
        }

        .buy:hover .buy-btn {
            animation: jello 1s;
        }

        .trans {
            transition: background-color .2s;
        }

        .buy-btn {
            background-color: #E16132;
            width: 100px;
            line-height: 100px;
            height: 100px;
            font-size: 24px;
            position: relative;
            z-index: 1;
            margin: auto;
            cursor: pointer;
            border-radius: 50%;
            position: absolute;
            left: 0;
            top: 0;
            right: 0;
            bottom: 0;
            margin: auto;
        }

        @keyframes jello {

            from,
            11.1%,
            to {
                transform: none;
            }

            22.2% {
                transform: skewX(-12.5deg) skewY(-12.5deg);
            }

            33.3% {
                transform: skewX(6.25deg) skewY(6.25deg);
            }

            44.4% {
                transform: skewX(-3.125deg) skewY(-3.125deg);
            }

            55.5% {
                transform: skewX(1.5625deg) skewY(1.5625deg);
            }

            66.6% {
                transform: skewX(-0.78125deg) skewY(-0.78125deg);
            }

            77.7% {
                transform: skewX(0.390625deg) skewY(0.390625deg);
            }

            88.8% {
                transform: skewX(-0.1953125deg) skewY(-0.1953125deg);
            }
        }

        .buy:hover .buy-address-1,
        :checked+.buy .buy-address-1 {
            -ms-transform: translate(0, -150%);
            transform: translate3d(0, -150%, 0);
        }

        .buy:hover .buy-address,
        :checked+.buy .buy-address {
            color: #fff;
        }

        .buy-address {
            position: absolute;
            width: 80px;
            height: 80px;
            box-sizing: border-box;
            padding-top: 20px;
            line-height: 1.25;
            background-color: #E16132;
            left: 0;
            right: 0;
            top: 0;
            bottom: 0;
            margin: auto;
            transition: background-color .2s, transform .5s, color .2s;
            border-radius: 50%;
            color: transparent;
            transform: translate3d(0, 0, 0);
        }

        .buy:hover .buy-address-2,
        :checked+.buy .buy-address-2 {
            -ms-transform: translate(-130%, -75%);
            transform: translate3d(-130%, -75%, 0);
        }

        .buy:hover .buy-address-3,
        :checked+.buy .buy-address-3 {
            -ms-transform: translate(130%, -75%);
            transform: translate3d(130%, -75%, 0);
        }

        .buy-address-6,
        .buy-address-3 {
            padding-top: 30px;
        }

        .buy:hover .buy-address-4,
        :checked+.buy .buy-address-4 {
            -ms-transform: translate(0, 150%);
            transform: translate3d(0, 150%, 0);
        }

        .buy:hover .buy-address-5,
        :checked+.buy .buy-address-5 {
            -ms-transform: translate(-130%, 75%);
            transform: translate3d(-130%, 75%, 0);
        }

        .buy:hover .buy-address-6,
        :checked+.buy .buy-address-6 {
            -ms-transform: translate(130%, 75%, 0);
            transform: translate3d(130%, 75%, 0);
        }

        canvas {
            position: absolute;
            top: 0;
            left: 0;
            background-color: #143568;
        }
    </style>
</head>

<body>
    <canvas id="star" class="canvas-star" width="705" height="943"></canvas>
    <div class="buy">
        <label class="buy-btn trans" role="button" for="buyChk">购买</label>
        <!-- 购买的几个地址 -->
        <a href="#" class="buy-address buy-address-1" target="_blank">京东<br>自营</a>
        <a href="#" class="buy-address buy-address-2" target="_blank">人邮<br>官方</a>
        <a href="#" class="buy-address buy-address-3" target="_blank">电子版</a>
        <a href="#" class="buy-address buy-address-4" target="_blank">独家<br>签名版</a>
        <a href="#" class="buy-address buy-address-5" target="_blank">当当<br>自营</a>
        <a href="#" class="buy-address buy-address-6" target="_blank">亚马逊</a>
    </div>
    <script>
        var t = document.querySelector("#star");
        var i = t.getContext("2d");
        var n = {}
            , a = 0
            , r = {
                r: 1400,
                height: 260,
                density: 300,
                maxLife: 100,
                groundLevel: t.height,
                leftWall: 0,
                rightWall: t.width,
                alpha: 0,
                maxAlpha: 1
            };
        var o = function () {
            var e = Math.random();
            var t = Math.ceil(1 / (1 - e));
            var i = [];
            for (var n = 0; n < t; n++) {
                i.push(Math.random())
            }
            return Math.min.apply(null, i)
        };
        function l() {
            t.width = window.innerWidth;
            t.height = window.innerHeight;
            r.rightWall = t.width;
            r.groundLevel = t.height;
            for (var e in n) {
                n[e].y = o() * t.height
            }
            s();
            if (t.width <= 480) {
                document.body.className = "mobile"
            } else {
                document.body.className = "pc"
            }
        }
        l();
        window.addEventListener("resize", l);
        function s() {
            i.clearRect(0, 0, t.width, t.height)
        }
        function c() {
            var e = t.width / 2
                , i = t.height;
            this.x = Math.floor(Math.random() * t.width);
            this.y = o() * t.height;
            this.vx = Math.random() * .1 + .05;
            this.particleSize = .5 + (Math.random() + .1 / 4);
            a++;
            n[a] = this;
            this.alpha = 0;
            this.maxAlpha = .2 + this.y / t.height * Math.random() * .8;
            this.alphaAction = 1
        }
        c.prototype.draw = function () {
            this.x += this.vx;
            if (this.alphaAction == 1) {
                if (this.alpha < this.maxAlpha) {
                    this.alpha += .005
                } else {
                    this.alphaAction = -1
                }
            } else {
                if (this.alpha > .2) {
                    this.alpha -= .002
                } else {
                    this.alphaAction = 1
                }
            }
            if (this.x + this.particleSize * 2 >= r.rightWall) {
                this.x = this.x - r.rightWall
            }
            i.beginPath();
            i.fillStyle = "rgba(255,255,255," + this.alpha.toString() + ")";
            i.arc(this.x, this.y, this.particleSize, 0, Math.PI * 2, true);
            i.closePath();
            i.fill()
        }
            ;
        function u() {
            s();
            var e = 400;
            if (!history.pushState) {
                e = 200
            } else if (document.msHidden != undefined) {
                e = 300
            }
            if (screen.width < 1024) {
                e = 200
            }
            if (screen.width < 640) {
                e = 100
            }
            if (Object.keys(n).length > e) {
                r.density = 0
            }
            for (var t = 0; t < r.density; t++) {
                if (Math.random() > .97) {
                    new c
                }
            }
            for (var t in n) {
                n[t].draw()
            }
            requestAnimationFrame(u)
        }
        if (!window.requestAnimationFrame) {
            window.requestAnimationFrame = function (e) {
                setTimeout(e, 17)
            }
        }
        u();
    </script>
</body>

</html>
```
