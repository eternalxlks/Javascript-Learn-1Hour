 Here's how to make a basic marine life example using 3 languages!

And Ignore the comments starting with this: //this is a example for a comment!

https://codepen.io

Make A Pen, and call it whatever you want!

For The HTML section, write this:

```<div class="container-bubble">
    <canvas id="fishCanvas"></canvas>
</div>
```

This acts as the sea!

And For the CSS section:
```
.container-bubble {
    position: fixed;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
    z-index: -1;
    background-color: #000; /*Dark background for marine space */
}

canvas {
    display: block;
    width: 100%;
    height: 100%;
}
```

This is used for the background, a deep marine space

For the JavaScript Section: (warning this is going to be long)

```
const canvas = document.getElementById("fishCanvas");
const ctx = canvas.getContext("2d");

canvas.width = window.innerWidth;
canvas.height = window.innerHeight;

// Bruno, stop critizising me 
const numFish = 200;
const numPredators = 3;
const fishSpeed = 2;
const predatorSpeed = 3;
const visionRadius = 70; // Pretty Good
const avoidanceRadius = 40; // Great Distance
const predatorAvoidanceRadius = 150; // The Predators are a bit too close

// Eureka!
let fishSchool = [];
let predators = [];

// Gotta Code the Fish
class Fish {
    constructor(x, y) {
        this.x = x;
        this.y = y;
        this.size = 5;
        this.color = 'rgba(0, 200, 255, 0.9)';
        this.angle = Math.random() * Math.PI * 2;
        this.speed = fishSpeed;
        this.velocityX = Math.cos(this.angle) * this.speed;
        this.velocityY = Math.sin(this.angle) * this.speed;
    }

    // A triangle's a good shape right?
    draw() {
        ctx.save();
        ctx.translate(this.x, this.y);
        ctx.rotate(this.angle);

        ctx.beginPath();
        ctx.moveTo(-this.size, -this.size / 2);
        ctx.lineTo(this.size, 0);
        ctx.lineTo(-this.size, this.size / 2);
        ctx.closePath();
        ctx.fillStyle = this.color;
        ctx.fill();
        ctx.restore();
    }

    // ngl this is tough
    flock(fishSchool, predators) {
        let alignment = { x: 0, y: 0 };
        let cohesion = { x: 0, y: 0 };
        let separation = { x: 0, y: 0 };
        let total = 0;
        
        fishSchool.forEach(otherFish => {
            let dx = otherFish.x - this.x;
            let dy = otherFish.y - this.y;
            let distance = Math.sqrt(dx * dx + dy * dy);

            // Hasta lo Proxima!
            if (distance < visionRadius && otherFish !== this) {
                alignment.x += otherFish.velocityX;
                alignment.y += otherFish.velocityY;

                cohesion.x += otherFish.x;
                cohesion.y += otherFish.y;

                if (distance < avoidanceRadius) {
                    separation.x -= dx;
                    separation.y -= dy;
                }

                total++;
            }
        });

        if (total > 0) {
            // Alignment
            alignment.x /= total;
            alignment.y /= total;
            this.velocityX += (alignment.x - this.velocityX) * 0.05;
            this.velocityY += (alignment.y - this.velocityY) * 0.05;

            // Cohesion
            cohesion.x /= total;
            cohesion.y /= total;
            this.velocityX += (cohesion.x - this.x) * 0.01;
            this.velocityY += (cohesion.y - this.y) * 0.01;

            // Separate
            this.velocityX += separation.x * 0.1;
            this.velocityY += separation.y * 0.1;
        }

        // Predators Forever!
        predators.forEach(predator => {
            let dx = predator.x - this.x;
            let dy = predator.y - this.y;
            let distance = Math.sqrt(dx * dx + dy * dy);

            if (distance < predatorAvoidanceRadius) {
                this.velocityX -= dx * 0.2;
                this.velocityY -= dy * 0.2;
            }
        });

        // The predators are a bit too fast
        const maxSpeed = fishSpeed * 2;
        const speed = Math.sqrt(this.velocityX * this.velocityX + this.velocityY * this.velocityY);
        if (speed > maxSpeed) {
            this.velocityX = (this.velocityX / speed) * maxSpeed;
            this.velocityY = (this.velocityY / speed) * maxSpeed;
        }

        // better velocity!
        this.x += this.velocityX;
        this.y += this.velocityY;
        this.angle = Math.atan2(this.velocityY, this.velocityX);

        // BLAH BLAH BLAH
        if (this.x < 0) this.x = canvas.width;
        if (this.x > canvas.width) this.x = 0;
        if (this.y < 0) this.y = canvas.height;
        if (this.y > canvas.height) this.y = 0;
    }
}

// Who wants to take predator class!
class Predator {
    constructor(x, y) {
        this.x = x;
        this.y = y;
        this.size = 15;
        this.color = 'rgba(255, 50, 50, 0.9)';
        this.angle = Math.random() * Math.PI * 2;
        this.speed = predatorSpeed;
    }

    // More Code here!
    draw() {
        ctx.save();
        ctx.translate(this.x, this.y);
        ctx.rotate(this.angle);

        ctx.beginPath();
        ctx.moveTo(-this.size, -this.size / 2);
        ctx.lineTo(this.size, 0);
        ctx.lineTo(-this.size, this.size / 2);
        ctx.closePath();
        ctx.fillStyle = this.color;
        ctx.fill();
        ctx.restore();
    }

    // Maybe just a touch
    chase(fishSchool) {
        let nearestFish = null;
        let minDist = Infinity;

        fishSchool.forEach(fish => {
            let dx = fish.x - this.x;
            let dy = fish.y - this.y;
            let dist = Math.sqrt(dx * dx + dy * dy);

            if (dist < minDist) {
                minDist = dist;
                nearestFish = fish;
            }
        });

        if (nearestFish) {
            let dx = nearestFish.x - this.x;
            let dy = nearestFish.y - this.y;
            this.angle = Math.atan2(dy, dx);
            this.x += Math.cos(this.angle) * this.speed;
            this.y += Math.sin(this.angle) * this.speed;
        }

        // dooo dooo dooo
        if (this.x < 0) this.x = canvas.width;
        if (this.x > canvas.width) this.x = 0;
        if (this.y < 0) this.y = canvas.height;
        if (this.y > canvas.height) this.y = 0;
    }
}

// Better make these!

function initFish() {
    for (let i = 0; i < numFish; i++) {
        fishSchool.push(new Fish(Math.random() * canvas.width, Math.random() * canvas.height));
    }
}

// Better
function initPredators() {
    for (let i = 0; i < numPredators; i++) {
        predators.push(new Predator(Math.random() * canvas.width, Math.random() * canvas.height));
    }
}

// Animations are kinda weird
function animate() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);

    fishSchool.forEach(fish => {
        fish.flock(fishSchool, predators);
        fish.draw();
    });

    predators.forEach(predator => {
        predator.chase(fishSchool);
        predator.draw();
    });

    requestAnimationFrame(animate);
}

// LETS GOOO!
initFish();
initPredators();
animate();

// Almost there
window.addEventListener('resize', () => {
    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;
});

// Finally done with this repository!
```





