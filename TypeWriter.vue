<script setup lang="ts">
import { ref } from 'vue'

interface IProps {
  text:string,
  fontSize?:number
}
const props = withDefaults(defineProps<IProps>(),{
  text:"",
  fontSize:6,
  speed: 0.01
})

const dLetter = 0.02;
const lines:string[] = splitTextIntoLines(props.text)
const dLines = [0, ...lines.map((line)=> line.length * dLetter)]
const linesRef:Ref<string[]> = ref([lines]);

function splitTextIntoLines(text:string) {
  return text.split("\n");
}

function getAnimationDelay(letterIndex:number, lineIndex:number) {
  const dLetter = props.speed;

  const delay = letterIndex * dLetter + "s";
  
  const line_length = lines[letterIndex]?.length;
  if (letterIndex === line_length -1){
    linesRef._raw_value.push(lines[lineIndex])
  }

  return {
    animationDelay: delay,
  };
}
</script>
<template>
  <div class="aText" :style="(fontSize>0)? `font-size:${props.fontSize}em`:''">
    <p
      v-for="(line, lineIndex) in lines" 
      :key="lineIndex" 
      style="padding:0;margin:0"
    > 
        <span
          v-for="(letter, letterIndex) in line"
          :key="letterIndex"
          class="letter"
          :style="{animationDelay: dLines.slice(0,lineIndex+1).reduce((partialSum, a) => partialSum + a, 0) + dLetter + letterIndex*dLetter + 's'}"
        >
          {{  letter  }}
        </span>
      </p>
  </div>
</template>

<style>
.letter {
  opacity: 0;
  animation: fadeIn 0.1s ease-in-out forwards;
  transform: translateX(100px);
}

@keyframes fadeIn {
  0% {
    opacity: 0;
    transform: translateX(100px);
  }
  100% {
    opacity: 1;
    transform: translateX(0);
  }
}
.aText{
  color: rgb(0, 0, 0);
  font-size: x-large;
}
</style>
