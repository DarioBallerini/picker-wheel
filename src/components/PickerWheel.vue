<!-- eslint-disable @typescript-eslint/no-non-null-assertion -->
<script setup lang="ts">
import { onMounted, ref, type Ref } from 'vue';

const canvas: Ref<HTMLCanvasElement | null> = ref(null)
const colors = ['#00202e','#003f5c','#2c4875','#8a508f', '#bc5090', '#ff6361', '#ff8531', '#ffa600', '#ffd380'];
const items = ['Milanesa 1' , 'Milanesa 2'];
const showAnimation = ref(false);
let ctx: CanvasRenderingContext2D | null;
let centerX = 0;
let centerY = 0;
let turns = 0;
let canSpin = true;

onMounted(() => {
  centerX = canvas.value!.width / 2;
  centerY = canvas.value!.height / 2;
  ctx = canvas.value!.getContext("2d")!;
  drawCircle();
})

const drawCircle = () => {
  let startAngle = 0;
  let endAngle = (2 / items.length);
  items.forEach((item,index) => {
    ctx!.beginPath();
    ctx!.arc(centerX, centerY, 300, startAngle * Math.PI, endAngle * Math.PI, false);
    ctx!.lineTo(centerX, centerY);
    ctx!.closePath();
    ctx!.fillStyle = colors[index];
    ctx!.fill();
    startAngle += (2 / items.length);
    endAngle += (2 / items.length);
  })
}

const addItem = () => {
  if (items.length < 9) {
    items.push('Milanesa ' + (items.length + 1));
    drawCircle();
  }
}

const spin = () => {
  if (canSpin) {
    canSpin = false;
    turns += getRandomNumber();
    canvas.value!.style.transform = `rotate(${turns}turn)`;
    showAnimation.value = true;
    setTimeout(() => {  
      const finalPosition = Number('0.' + (turns + '').split('.')[1]);
      const itemSize = 1 / items.length;
      let iterations = 0;
      for (let index = items.length - 1; index >= 0; index--) {
        if (finalPosition > itemSize * index) {
          alert(`Winner: ${items[iterations]}`);
          break;
        }
        iterations ++;
      }
      canSpin = true;
      showAnimation.value = false;
    }, 8000);
  }
}

const getRandomNumber = () => {
  const min = 20.05;
  const max = 20.95;
  return Math.random() * (max - min) + min;
}
</script>

<template>
  <div class="wheel-container">
    <canvas height="600" width="600" ref="canvas" @click="spin" :class="{ 'rotate-animation': showAnimation }"></canvas>
    <div class="arrow">&#9664;</div>
  </div>
  <button @click="addItem">Add item</button>
</template>

<style scoped>
.wheel-container {
  position: relative;
}
.arrow {
  position: absolute;
  font-size: 40px;
  height: 100px;
  display: flex;
  align-items: center;
  top: calc(50% - 51px);
  right: -24px;
  color: white;
}
canvas {
  transition: none;
}
.rotate-animation {
  transition: transform 8s cubic-bezier(0, 1, 0.5, 1);
}
button {
  padding: 1rem 2rem;
  border: 1px solid purple;
  background-color: transparent;
  color: white;
  border-radius: 5px;
  cursor: pointer;
}
</style>
