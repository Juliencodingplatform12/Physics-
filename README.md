# Physics-

function setup() {
  createCanvas(400, 400);
}

function draw() {
  background(220);
}
let mover;
let liquid;

function setup() {
  createCanvas(600, 400);
  mover = new Mover();
  liquid = new Liquid(0, height/2, width, height/2, 0.1);
}

function draw() {
  background(220);

  // Draw liquid
  liquid.display();

  // If mover is in liquid, apply drag
  if (liquid.contains(mover)) {
    let drag = liquid.calculateDrag(mover);
    mover.applyForce(drag);
  }

  // Gravity
  let gravity = createVector(0, 0.2 * mover.mass);
  mover.applyForce(gravity);

  // Friction
  let friction = mover.velocity.copy();
  friction.normalize();
  friction.mult(-1);
  let c = 0.02;
  friction.mult(c);
  mover.applyForce(friction);

  mover.update();
  mover.checkEdges();
  mover.display();
}

// Keyboard control - apply forces
function keyPressed() {
  if (keyCode === LEFT_ARROW) {
    mover.applyForce(createVector(-1, 0));
  }
  if (keyCode === RIGHT_ARROW) {
    mover.applyForce(createVector(1, 0));
  }
  if (keyCode === UP_ARROW) {
    mover.applyForce(createVector(0, -2));
  }
  if (keyCode === DOWN_ARROW) {
    mover.applyForce(createVector(0, 2));
  }
}

// Mouse click - upward force ("jump")
function mousePressed() {
  mover.applyForce(createVector(0, -5));
}

// Mover class
class Mover {
  constructor() {
    this.mass = 1;
    this.radius = 24;
    this.position = createVector(width / 2, 50);
    this.velocity = createVector();
    this.acceleration = createVector();
  }

  applyForce(force) {
    let f = p5.Vector.div(force, this.mass);
    this.acceleration.add(f);
  }

  update() {
    this.velocity.add(this.acceleration);
    this.position.add(this.velocity);
    this.acceleration.mult(0);
  }

  display() {
    fill(127);
    stroke(0);
    ellipse(this.position.x, this.position.y, this.radius * 2, this.radius * 2);
  }

  checkEdges() {
    if (this.position.y > height - this.radius) {
      this.position.y = height - this.radius;
      this.velocity.y *= -0.6;
    }
    if (this.position.x > width - this.radius) {
      this.position.x = width - this.radius;
      this.velocity.x *= -0.6;
    }
    if (this.position.x < this.radius) {
      this.position.x = this.radius;
      this.velocity.x *= -0.6;
    }
  }
}

// Liquid class for drag
class Liquid {
  constructor(x, y, w, h, c) {
    this.x = x;
    this.y = y;
    this.w = w;
    this.h = h;
    this.c = c;
  }

  contains(mover) {
    let pos = mover.position;
    return pos.x > this.x && pos.x < this.x + this.w && pos.y > this.y;
  }

  calculateDrag(mover) {
    let speed = mover.velocity.mag();
    let dragMagnitude = this.c * speed * speed;
    let drag = mover.velocity.copy();
    drag.mult(-1);
    drag.normalize();
    drag.mult(dragMagnitude);
    return drag;
  }

  display() {
    noStroke();
    fill(100, 100, 200, 150);
    rect(this.x, this.y, this.w, this.h);
  }
}
