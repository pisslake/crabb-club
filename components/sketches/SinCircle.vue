<template>
    <v-card class="ma-5">
        <div style="height: 85vh" id="canvas"></div>
    </v-card>
</template>

<script>
export default {
    mounted() {
        let sketch = function (p) {
            //let palette = tome.get("tricolor");
            let palette = ["#ec643b", "#56b7ab", "#f8cb57", "#1f1e43"];
            let bgColor = "#f7f2df"; //palette.background;

            let cx;
            let cy;

            let radSlider;
            let pointsSlider;
            let omegaSlider;
            let ampSlider;

            let canvasWidth = document.getElementById("canvas").clientWidth;
            let canvasHeight = document.getElementById("canvas").clientHeight;
            p.setup = function () {
                //p.createCanvas(p.windowWidth, p.windowHeight);
                //p.createCanvas(900, 900);
                p.createCanvas(canvasWidth, canvasHeight);

                p.background(bgColor);

                cx = canvasWidth / 2;
                cy = canvasHeight / 2;

                // make some sliders
                radSlider = p.createSlider(0, 500, 400, 1);
                radSlider.position(10, 10);
                radSlider.style("width", "150px");
                p.createDiv("Radius").style('background-color', '#000000').position(170, 10);

                pointsSlider = p.createSlider(0, 100, 20, 1);
                pointsSlider.position(10, 30);
                pointsSlider.style("width", "150px");
                p.createDiv("Points").style('background-color', '#000000').position(170, 30);

                omegaSlider = p.createSlider(0, 5, 1, 0.1);
                omegaSlider.position(10, 50);
                omegaSlider.style("width", "150px");
                p.createDiv("Omega").style('background-color', '#000000').position(170, 50);

                ampSlider = p.createSlider(0, 200, 15, 1);
                ampSlider.position(10, 70);
                ampSlider.style("width", "150px");
                p.createDiv("Amplitude").style('background-color', '#000000').position(170, 70);
            };

            p.draw = function () {
                canvasWidth = document.getElementById("canvas").clientWidth;
                canvasHeight = document.getElementById("canvas").clientHeight;
                cx = canvasWidth / 2;
                cy = canvasHeight / 2;
                p.resizeCanvas(canvasWidth, canvasHeight);

                p.background(bgColor);
                let color = p.color(palette /*.colors*/[0]);
                color.setAlpha(70);

                p.stroke(color);
                p.strokeWeight(1);

                // make a cool wobbly boy
                let seeds = genSinRing(
                    cx,
                    cy,
                    radSlider.value(),
                    pointsSlider.value(),
                    omegaSlider.value(),
                    ampSlider.value()
                );
                drawRing(seeds);

                //drawRing(genSinRing(cx, cy, 200, 20, 1.5, 50));
            };

            // draw the sin ring
            function drawRing(points) {
                for (let i = 0; i < points.length; i++) {
                    let start = points[i];
                    for (let j = 0; j < points.length; j++) {
                        let point = points[j];
                        p.line(start.x, start.y, point.x, point.y);
                    }
                    // show the points
                    //p.fill(0);
                    //p.circle(start.x, start.y, 10);
                }
            }

            // omega = 2 pi f
            // omega being angular freq
            // above 5 rad/s gets a little absurd
            function genSinRing(
                cx,
                cy,
                baseRadius,
                numPoints,
                omega,
                amplitude
            ) {
                // clear old seeds
                let seeds = [];
                for (let i = 0; i < numPoints; i++) {
                    let angle = (p.TWO_PI / numPoints) * i;
                    let time = i + p.frameCount / 10;
                    let r = p.sin(omega * time) * amplitude + baseRadius;
                    let pos = p5.Vector.fromAngle(angle, r);
                    pos.x += cx;
                    pos.y += cy;
                    seeds.push(pos);
                }

                return seeds;
            }
        };

        const p5 = require("p5");
        new p5(sketch, "canvas");
    },
};
</script>