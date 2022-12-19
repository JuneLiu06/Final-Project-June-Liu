# Final-Project-June-Liu


let ballPos; // ball position 
let ballSpd; // ball speed
let ballSize = 20;
let gravity; 
let level; // curent level
let levelKnife; // curent knife level
let brikes;// brikes array
let knifes; // knife array

let knifeLen; // knife length
let preTime; // time flag
let downOff; // move down offset 
let downSpeed; // move down speed
let space; // space num
let score; // score
let gameOver; // if game is over
function setup() {
  createCanvas(350, 600);
  knifes = [];
  knifeLen = 60;
  preTime = 0;
  downOff = 0;
  downSpeed = 0;
  space = 21;
  score = 0;
  gameOver = false;
  ballPos = createVector(width * 0.58, height * 0.7);
  ballSpd = createVector(0, 3);
  gravity = createVector(0, 0.3);
  level = height;
  levelKnife = height;
  brikes = [];
  
  //create brikes
  for (let i = 0; i < space + 1; i += 1) {
    if (i % 2 == 0) {
      brikes.push(
        new Brike(width * 0.4, (height * i) / space, color(177, 217, 250))
      );
    } else {
      brikes.push(
        new Brike(width * 0.4, (height * i) / space, color(92, 179, 250))
      );
    }
  }
}

function draw() {
  background(212, 228, 255);
  noStroke();
  
  //show score 
  fill(0);
  textAlign(CENTER);
  text("Score:" + score, width * 0.1, height * 0.1);
  
  if (!gameOver) {
    //if brikes move
    if (levelKnife < height * 0.4) {
      downSpeed = 1.5;
    } else {
      downSpeed = 0;
    }
    downOff += downSpeed;
    ballPos.y += downSpeed;
    level += downSpeed;
    levelKnife += downSpeed;
    
    
    //update ball position
    ballPos.add(ballSpd);
    ballSpd.add(gravity);
    if (ballPos.y + ballSize > levelKnife && ballSpd.y > 0) {
      ballSpd.y = -9;
    }

    // create new knife
    if (mouseIsPressed && frameCount - preTime > 10) {
      if (level - mouseY > height / 25) {
        preTime = frameCount;
        level -= height / 25;
        knifes.push(new Knife(level));
        score += 1;
      }
      // remove knife
      if (mouseY - level > 0) {
        level = knifes[knifes.length - 2].pos.y;
        knifes.pop();
        // level+=height/40;
        levelKnife = level;
      }
    }
    
    //update and show knifes
    for (let i = 0; i < knifes.length; i++) {
      knifes[i].pos.y += downSpeed;
      if (knifes[i].pos.x < ballPos.x && knifes[i].pos.y < levelKnife) {
        levelKnife = knifes[i].pos.y;
      }
      if (knifes[i].pos.dist(ballPos) < ballSize * 0.45) {
        gameOver = true;
        knifes[i].pos.x = width * 0.5;
        ballPos.x = width * 0.5;
      }
      knifes[i].show();
    }
    
    //show ball
    fill(255, 255, 161);
    noStroke();
    ellipse(ballPos.x, ballPos.y, ballSize);

    
    //show brikes
    for (let i = 0; i < brikes.length; i++) {
      brikes[i].show();
      brikes[i].pos.y += downSpeed;
      if (brikes[i].pos.y > height) {
        brikes[i].pos.y -= height + height / space;
      }
    }
    //if win the game
    if (score > 50) {
      gameOver = true;
    }
  } else {
    
    //if game is over, game is static
    for (let i = 0; i < knifes.length; i++) {
      knifes[i].show();
    }
    fill(255, 255, 161);
    noStroke();
    ellipse(ballPos.x, ballPos.y, ballSize);
    for (let i = 0; i < brikes.length; i++) {
      brikes[i].show();
    }
    textAlign(CENTER);
    fill(255)
    textSize(15);
    text("Press space to restart the game", width * 0.5, height * 0.5);
    if (score > 50) {
      textSize(20)
          text("Congratulations, you win the game", width * 0.5, height * 0.35);
      
    }
  }
}

//press space to restart the game 
function keyPressed() {
  if (gameOver && key == " ") {
    setup();
  }
}


class Knife {
  constructor(y) {
    this.pos = createVector(width * 0.8, y);
    this.spd = createVector(-6, 0);
  }
  show() {
    this.pos.add(this.spd);
    if (this.pos.x < width * 0.5) {
      this.spd.x = 0;
    }
    stroke(0);
    triangle(
      this.pos.x,
      this.pos.y,
      this.pos.x + 5,
      this.pos.y - 2,
      this.pos.x + 5,
      this.pos.y + 2
    );
    line(this.pos.x, this.pos.y, this.pos.x + knifeLen, this.pos.y);
  }
}

class Brike {
  constructor(x, y, c) {
    this.pos = createVector(x, y);
    this.color = c;
  }
  show() {
    noStroke();
    fill(this.color);
    rect(this.pos.x, this.pos.y, width * 0.1, height / space);
  }
}

