# particulas2.0

[Visitar el sitio de las partículas](https://jonnysc.github.io/particulas2.0/)


Este código en JavaScript crea un efecto de partículas que responden al movimiento del mouse y cambian su comportamiento cuando se hace clic en la ventana del navegador. A continuación, se detalla el código paso a paso:

```javascript
// Se define la variable partNum con un valor de 1000, que representa el número de partículas en la animación.
var partNum = 1000;

// Se define una función llamada requestAnimFrame para gestionar la animación utilizando el método requestAnimationFrame del navegador.
// Esta función intenta obtener el método requestAnimationFrame del navegador y, si no está disponible, utiliza alternativas para mantener una velocidad de fotogramas de aproximadamente 60 por segundo.
window.requestAnimFrame = (function(){
    return window.requestAnimationFrame ||
        window.webkitRequestAnimationFrame ||
        window.mozRequestAnimationFrame ||
        function(callback){
            window.setTimeout(callback, 1000 / 60);
        };
})();

// Se crea un objeto keysDown para realizar un seguimiento de las teclas que están siendo presionadas y se agregan eventos keydown y keyup para actualizar este objeto cuando las teclas se presionan y se sueltan.
var keysDown = {};
window.addEventListener('keydown', function(e) {
    keysDown[e.keyCode] = true;
});
window.addEventListener('keyup', function(e) {
    delete keysDown[e.keyCode];
});

// Se define una función between(min, max) que genera un número aleatorio entre los valores min y max.
function between(min, max) {
    return Math.random() * (max - min) + min;
}

// Se obtiene el elemento canvas del documento y se obtiene su contexto 2D.
// Luego, se configura el ancho y alto del canvas para que coincida con el ancho y alto de la ventana del navegador.
var c = document.getElementById('c');
var ctx = c.getContext('2d');

// Se define el ancho y alto de canvas
var w = window.innerWidth;
var h = window.innerHeight;
c.width = w;
c.height = h;

// Se crea un objeto mouse que rastrea la posición del mouse en relación con el canvas.
// Se agregan eventos para actualizar las coordenadas del mouse cuando se mueve.
var mouse = {
    x: w / 2,
    y: h / 2
};
document.addEventListener('mousemove', function(e){
    mouse.x = e.clientX || e.pageX;
    mouse.y = e.clientY || e.pageY;
}, false);

// Se definen algunas variables relacionadas con un cursor interactivo que cambia de tamaño y opacidad cuando se hace clic en la ventana del navegador.
var cursorRad = 15;
var cursorOpacity = 1;
var cursorTrue = false;
var cursorMinDest = 100;

// Se agregan eventos mousedown y mouseup para controlar el cambio de tamaño y opacidad del cursor interactivo cuando se hace clic y se suelta en la ventana del navegador.
document.addEventListener('mousedown', function(){
    cursorRad = 50;
    cursorOpacity = 0.2;
    cursorTrue = true;
    cursorMinDest = 200;
}, false);

document.addEventListener('mouseup', function(){
    cursorRad = 25;
    cursorOpacity = 1;
    cursorTrue = false;
    cursorMinDest = 100;
}, false);

// Se crea un array particles que almacenará las partículas de la animación.
for(i = 0; i < partNum; i++) {
    particles.push(new particle);
}

// La función particle() se define para crear una nueva partícula con una posición, velocidad y color aleatorios.
function particle() {
    this.x = Math.random() * c.width;
    this.y = Math.random() * c.height;

    this.vx = 0;
    this.vy = 0;

    this.r = 1;

    this.color = '#fff';
}

// Se define la función draw() que se encarga de realizar la animación en cada fotograma.
function draw() {
    requestAnimFrame(draw);

    ctx.fillStyle = 'rgba(52, 52, 53, 1)';
    ctx.fillRect(0, 0, c.width, c.height);

    ctx.beginPath();
    ctx.fillStyle = 'rgba(255, 255, 255, ' + cursorOpacity + ')';
    ctx.arc(mouse.x, mouse.y, cursorRad, Math.PI * 2, false);
    ctx.fill();

    for(t = 0; t < particles.length; t++) {
        var p = particles[t];

        ctx.beginPath();
        ctx.fillStyle = p.color;
        ctx.arc(p.x, p.y, p.r, Math.PI * 2, false);
        ctx.fill();

        p.x+=p.vx;
        p.y+=p.vy;

        p.vx*=0.95;
        p.vy*=0.95;

        if(p.y < 0) {
            p.vy *= -1;
        }

        if(p.y > c.height) {
            p.vy *= -1;
        }

        if(p.x < 0) {
            p.vx *= -1;
        }

        if(p.x > c.width) {
            p.vx *= -1;
        }

        distance(mouse, p);
    }
}

// La función distance(p1, p2) calcula la distancia entre dos puntos p1 y p2 y, si la distancia es menor o igual a cursorMinDest, aplica una fuerza a la partícula p2 dependiendo de la posición del mouse y de si el cursor está haciendo clic o no.
function distance(p1, p2) {
    var dist,
        dx = p1.x - p2.x,
        dy = p1.y - p2.y;

    dist = Math.sqrt(dx*dx + dy*dy);

    var minDist = cursorMinDest;

    if(dist <= minDist) {

        // Draw the line
        ctx.beginPath();

        var ax = dx,
            ay = dy;

        if(cursorTrue) {
            p2.vx += ax/100;
            p2.vy += ay/100;
        } else {
            p2.vx -= ax/35;
            p2.vy -= ay/35;
        }
    }
}

// Finalmente, se llama a la función draw() para iniciar la animación.
draw();
