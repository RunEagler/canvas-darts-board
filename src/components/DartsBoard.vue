<template>
    <canvas ref="canvas" class="canvas" v-bind:width="width" v-bind:height="height"
            style="background-color:antiquewhite"></canvas>
</template>


<script lang='ts'>
    interface Point {
        x: number;
        y: number;
    }

    import {Component, Vue} from "vue-property-decorator";

    @Component({
        name: "DartsBoard",
        components: {},
    })

    export default class View extends Vue {
        public ctx: any;
        private width: number;
        private height: number;


        constructor() {
            super();
            this.ctx = undefined;
            this.width = 800;
            this.height = 800;
        }

        private mounted(): void {
            this.ctx = this.$refs.canvas.getContext("2d");
            this.ctx.translate(this.width / 2, this.height / 2);
            this.drawDartBoard(this.ctx, this.width, this.height);
        }

        private drawDartBoard(ctx: any, width: number, height: number): void {

            const radius = (width / 2) * 0.95;
            this.drawFrame(ctx, radius);
            this.drawNumbers(ctx, radius);
            this.drawDoubleArc(ctx, radius * 0.80);
            this.drawSingleArc(ctx, radius * 0.755);
            this.drawTripleArc(ctx, radius * 0.4);
            this.drawBull(ctx, radius);
        }


        private drawSingleArc(ctx: any, radius: number): void {

            let i = 0;
            for (let angle = 9; angle < 360; angle += 18) {
                const startDegree = angle;
                const endDegree = startDegree + 18;
                const startOuterPoint = this.circlePoint(radius, startDegree);
                const endInnerPoint = this.circlePoint(radius * 0.9, endDegree);
                const endOuterPoint = this.circlePoint(radius, endDegree);
                const start = this.toRadian(startDegree);
                const end = this.toRadian(endDegree);

                ctx.save();
                ctx.beginPath();
                ctx.moveTo(0, 0);
                ctx.lineTo(startOuterPoint.x, startOuterPoint.y);

                ctx.arc(0, 0, radius, start, end, false);
                ctx.lineTo(endInnerPoint.x, endInnerPoint.y);

                ctx.strokeStyle = "white";
                ctx.stroke();
                if (i % 2 === 0) {
                    ctx.fillStyle = "black";
                } else {
                    ctx.fillStyle = "white";
                }
                ctx.fill();

                ctx.restore();
                i += 1;
            }
        }


        private drawDoubleArc(ctx: any, radius: number): void {

            this.drawRedAndGreen(ctx, radius);
        }

        private drawTripleArc(ctx: any, radius: number): void {

            this.drawRedAndGreen(ctx, radius);
        }

        private drawRedAndGreen(ctx: any, radius: number): void {

            let i = 0;
            for (let angle = 9; angle < 360; angle += 18) {
                const startDegree = angle;
                const endDegree = startDegree + 18;
                const startInnerPoint = this.circlePoint(radius * 0.90, startDegree);
                const startOuterPoint = this.circlePoint(radius, startDegree);
                const endInnerPoint = this.circlePoint(radius * 0.90, endDegree);
                const start = this.toRadian(startDegree);
                const end = this.toRadian(endDegree);

                ctx.save();
                ctx.beginPath();
                ctx.moveTo(startInnerPoint.x, startInnerPoint.y);
                ctx.lineTo(startOuterPoint.x, startOuterPoint.y);
                ctx.arc(0, 0, radius, start, end, false);
                ctx.lineTo(endInnerPoint.x, endInnerPoint.y);

                ctx.lineWidth = 5;
                ctx.strokeStyle = "white";
                ctx.stroke();

                if (i % 2 === 0) {
                    ctx.fillStyle = "red";
                } else {
                    ctx.fillStyle = "green";
                }
                ctx.fill();
                ctx.restore();
                i += 1;
            }

            ctx.beginPath();
            ctx.lineWidth = 3;
            ctx.arc(0, 0, radius * 0.90, 0, Math.PI * 2);
            ctx.strokeStyle = "white";
            ctx.stroke();
        }


        private drawBull(ctx: any, radius: number): void {
            ctx.beginPath();
            ctx.arc(0, 0, radius * 0.1, 0, 2 * Math.PI);
            ctx.fillStyle = "green";
            ctx.strokeStyle = "white";
            ctx.stroke();
            ctx.fill();

            ctx.beginPath();
            ctx.arc(0, 0, radius * 0.04, 0, 2 * Math.PI);
            ctx.fillStyle = "red";
            ctx.strokeStyle = "white";
            ctx.stroke();
            ctx.fill();
        }

        private drawFrame(ctx: any, radius: number): void {
            ctx.beginPath();
            ctx.arc(0, 0, radius, 0, 2 * Math.PI);
            ctx.fillStyle = "black";
            ctx.fill();
        }

        private circlePoint(radius: number, degree: number): Point {
            let p: Point = {x: 0, y: 0};
            degree = degree % 360;
            if (degree <= 90) {
                p.x = this.calcCircleX(radius, degree);
                p.y = this.calcCircleY(radius, degree);
            } else if (90 < degree && degree <= 180) {
                p.x = -this.calcCircleX(radius, 180 - degree);
                p.y = this.calcCircleY(radius, 180 - degree);
            } else if (180 < degree && degree <= 270) {
                p.x = -this.calcCircleX(radius, degree - 180);
                p.y = -this.calcCircleY(radius, degree - 180);
            } else {
                p.x = this.calcCircleX(radius, 360 - degree);
                p.y = -this.calcCircleY(radius, 360 - degree);
            }
            return p;
        }

        private calcCircleX(radius: number, degree: number): number {
            return radius * Math.cos(this.toRadian(degree));
        }

        private calcCircleY(radius: number, degree: number): number {
            return radius * Math.sin(this.toRadian(degree));
        }


        private drawNumbers(ctx: any, radius: number): void {

            ctx.save();
            ctx.textBaseline = "middle";
            ctx.textAlign = "center";

            const dartssNumbers = [20, 1, 18, 4, 13, 6, 10, 15, 2, 17, 3, 19, 7, 16, 8, 11, 14, 9, 12, 5];

            for (const i in dartssNumbers) {
                const ang = i * Math.PI / 10;
                ctx.rotate(ang);
                ctx.translate(0, -radius * 0.90);
                ctx.rotate(-ang);
                this.drawFont(ctx, dartssNumbers[i].toString());
                ctx.rotate(ang);
                ctx.translate(0, radius * 0.90);
                ctx.rotate(-ang);
            }
            ctx.restore();
        }

        private drawFont(ctx: any, text: string) {
            ctx.font = "40px verdana";
            ctx.lineWidth = 5;
            ctx.strokeText(text, 0, 0);
            ctx.shadowBlur = 0;
            ctx.fillStyle = "white";
            ctx.fillText(text, 0, 0);
        }

        private toRadian(degree: number): number {
            return degree * Math.PI / 180;
        }
    }
</script>

<style scoped>

</style>
