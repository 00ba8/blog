---
layout: post
title: Of lines and shapes project notes
---
<a href="https://www.fxhash.xyz/generative/17015" target="_blank" rel="noopener noreferrer">Of lines and shapes</a>
is a generative project published on fxhash.

The project explores an approach to create basic geometric shapes from vertical or horizontal lines.
Blending colors, as well as some texture overlay.

Original sketch leading to the project creation:

<blockquote class="twitter-tweet"><p lang="en" dir="ltr"><a href="https://twitter.com/hashtag/generative?src=hash&amp;ref_src=twsrc%5Etfw">#generative</a> <a href="https://twitter.com/hashtag/p5js?src=hash&amp;ref_src=twsrc%5Etfw">#p5js</a> <br>scribbles 📝 <a href="https://t.co/0wmXiOUcdu">pic.twitter.com/0wmXiOUcdu</a></p>&mdash; supa8 (@_supa8) <a href="https://twitter.com/_supa8/status/1547700615802851340?ref_src=twsrc%5Etfw">July 14, 2022</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script> 

##### Squares as lines:
Pretty straightforward, decide on amout of lines and draw moving points on an axis for the given amount:
```
for (let i = 0; i <= this.lineCount; i++) {
  // draw a line; x or y to determine direction (horizontal vs vertical)
  line(this.sx, this.sy, this.sx, this.sy + this.sLen);
  // step on the axis, alternate btw x or y conditionally if needed
  this.sx += this.lineStep;
}
```

##### Triangles as lines
Can be tricky, but the idea is to only deal with equilateral triangles for the project
going in differnt directions (e.g. top to bottom, left to right, etc). Given the lenght of one
side, the height of the triangle can be calculated as
```
this.th = (this.tLen * sqrt(3)) / 2; // height of triangle
```
Once the height is determined, similarly to squares the amount of lines will dictate the step to
draw the lines along an axis. With the only difference of a line getting shorter on each side
with each iteration to create the triangle look. First the starting line coordinates are
deretmined, then the remaining lines are added:
```
// set starting line coordinates
if (this.tDirection == 1) {
  this.tx1 = this.origX;
  this.ty1 = this.origY;
  this.tx2 = this.origX + this.tLen;
  this.ty2 = this.origY;
  // only used for a shadow if given, with the recular triangle() function
  this.tx3 = this.origX + this.tLen / 2;
  this.ty3 = this.origY + this.th;
}
...
for (let i = 0; i <= this.lineCount; i++) {
  // draw the first line
  line(this.tx1, this.ty1, this.tx2, this.ty2);
  // 1 - from top, 2 - bottom, 3 - left, 4 - right
  // calculate the step for the next line on the axis and reduce the line
  // lenght by (step / 2) on both ends for symmetrical look
  // since step determines amount of total lines - i.e. space btw each line
  // each line that follows needs to be reduced by the 'step' amount
  // halved, since reducing from both ends to converge to the central point
  if (this.tDirection == 1) {
    this.tx1 = this.tx1 + this.lineStep / 2;
    this.ty1 = this.ty1 + this.tSpacing;
    this.tx2 = this.tx2 - this.lineStep / 2;
    this.ty2 = this.ty2 + this.tSpacing;
  }
  ...
```

##### Circles as lines
Can be tricky as well, but follows a similar principle of the previous two drawing the lines from the
center of a circle. Given any point (x,y) on the path of the circle being `x = r * sin(angle)`, and
`y = r * cos(angle)`, lines can be drawn from one point towards the opposite point on the circumference.
The angle is then calculated based on the amount of lines to fill the circle with, thus
`angle = 180 / count` (somewhat similar as with triangles):
```
    let cAngle = 180 / this.lineCount;
    let x1, y1, x2, y2;
    for (let i = 0; i <= this.lineCount; i ++) {
      if (this.cDirection == 1) {
        x1 = this.mX + this.cd / 2 * cos(cAngle);
        y1 = this.mY + this.cd / 2 * sin(cAngle);
        x2 = this.mX + this.cd / 2 * cos(360 - cAngle);
        y2 = this.mY + this.cd / 2 * sin(360 - cAngle);
      }
      if (this.cDirection == 2) {
        x1 = this.mX + this.cd / 2 * cos(270 + cAngle);
        y1 = this.mY + this.cd / 2 * sin(270 + cAngle);
        x2 = this.mX + this.cd / 2 * cos(270 - cAngle);
        y2 = this.mY + this.cd / 2 * sin(270 - cAngle);
      }
      //line(x1, y1, x2, y2);
      cAngle += 180 / this.lineCount;
    }
```
