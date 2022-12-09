# Captcha
```javascript
const width = 250;
const height = 50;
var possibleCharacters = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789";

function _generateRandomText() {
    var text = '';
    var length = Math.floor((Math.random() * 2) + 4);
    for (var i = 0; i < length; i++) {
        text += possibleCharacters.charAt(Math.floor(Math.random() * possibleCharacters.length));
    }
    return text;
}

function _generateRandomColour() {
    return "rgb(" + Math.floor((Math.random() * 255)) + ", " + Math.floor((Math.random() * 255)) + ", " + Math.floor((Math.random() * 255)) + ")";
}

function _drawRandomCurve(ctx) {

    var gradient1 = ctx.createLinearGradient(0, 0, width, 0);
    gradient1.addColorStop(0, _generateRandomColour());
    gradient1.addColorStop(1, _generateRandomColour());

    ctx.lineWidth = 1.5;
    ctx.strokeStyle = '#000';
    ctx.beginPath();
    // ctx.moveTo(Math.floor((Math.random() * width)), Math.floor((Math.random() * height)));
    // ctx.bezierCurveTo(Math.floor((Math.random() * width)), Math.floor((Math.random() * height)),
    //     Math.floor((Math.random() * width)), Math.floor((Math.random() * height)),
    //     Math.floor((Math.random() * width)), Math.floor((Math.random() * height)));
    // ctx.stroke();
    ctx.beginPath();
ctx.moveTo(Math.floor((Math.random() * width)),Math.floor((Math.random() * height)));
ctx.lineTo(Math.floor((Math.random() * height)),Math.floor((Math.random() * width)));
ctx.stroke();
}

setTimeout(() => {
    var canvas = document.getElementById("myCanvas");
    var ctx = canvas.getContext("2d");
    // var gradient1 = ctx.createLinearGradient(0, 0, width, 0);
    // gradient1.addColorStop(0, _generateRandomColour());
    // gradient1.addColorStop(1, _generateRandomColour());

    // ctx.fillStyle = gradient1;
    // ctx.fillRect(0, 0, width, height);

    // var gradient2 = ctx.createLinearGradient(0, 0, width, 0);
    // gradient2.addColorStop(0, _generateRandomColour());
    // gradient2.addColorStop(1, _generateRandomColour());
    ctx.font = 'bold 40px "Comic Sans MS", cursive, sans-serif';
    // ctx.fillStyle = gradient2;

    // ctx.setTransform((Math.random() / 10) + 0.9, //scalex
    //     0.1 - (Math.random() / 5), //skewx
    //     0.1 - (Math.random() / 5), //skewy
    //     (Math.random() / 10) + 0.9, //scaley
    //     (Math.random() * 20) + 10, //transx
    //     100); //transy
    //     ctx.setTransform(1, 0, 0, 1, 0, 0);

        ctx.fillText(_generateRandomText(), 40, 40);


    var numRandomCurves = Math.floor((Math.random() * 6) + 5);
    for (var i = 0; i < 25; i++) {
        _drawRandomCurve(ctx);
    }
    for (var i = 0; i < 50; i++) {
        ctx.fillRect(Math.floor((Math.random() * width)),Math.floor((Math.random() * height)),3,3);
    }
}, 500);
```
