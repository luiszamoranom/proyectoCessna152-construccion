<template>
  <div 
    id="miDiv"
    class="Tknob"
    draggable="false"
    @mousedown="startDrag"
    @mouseup="stopDrag"
    @mousemove="handleDrag">
    <span class="number">{{ this.$store.getters.getThrottleDepth }}</span>
    <img
      src="@/assets/tknob.svg"
      :style="{
        transform: `scale(${currentScale}) rotate(${currentRotation}deg)`,
      }"
      draggable="false"
    />
  </div>
</template>

<script>
  import { mapActions } from 'vuex'
  import store from '@/store'
  export default {
    data() {
      return {
        axis: 0, // valor del thlottle
        startX: 0,
        startY: 0,
        isDragging: false,
        scaleMultiplier: 0.0002, // Factor de escala
        rotationMultiplier: 0.1, // Factor de rotación
        currentScale: 1, // Tamaño actual de la imagen
        maxScale: 1, // Límite máximo de escala
        minScale: 0.75, // Límite mínimo de escala
        currentRotation: 0, // Rotación actual en grados
      }
    },

    mounted() {
      window.addEventListener('keypress', this.girarThrottle)
    },

    methods: {
      ...mapActions(['setThrottleDepth']),
      startDrag(event) {
        this.startX = event.clientX
        this.startY = event.clientY
        this.isDragging = true
        window.addEventListener('mouseup', this.stopDrag)
      },

      /*girarThrottle(event) {
        if (event.key == 'l' && this.currentRotation != 0) {
          this.currentRotation--
        } else {
          if (event.key == 'ñ') {
            this.currentRotation++
          }
        }
      },*/

      stopDrag() {
        this.isDragging = false
        this.currentScale = this.getCurrentScale()
        this.currentRotation = this.getCurrentRotation()
        window.removeEventListener('mouseup', this.stopDrag)
      },
      handleDrag(event) {
        if (this.isDragging) {
          const currentX = event.clientX
          const diffX = currentX - this.startX
          const currentY = event.clientY
          const diffY = currentY - this.startY

          const scaleFactor = 1 + diffY * this.scaleMultiplier

          let newScale = this.currentScale * scaleFactor
          newScale = Math.max(this.minScale, Math.min(this.maxScale, newScale))

          let newRotation =
            this.currentRotation + diffX * this.rotationMultiplier
          if (newRotation >= 360) {
            newRotation -= 360
          } else if (newRotation < 0) {
            newRotation += 360
          }
          this.currentScale = newScale

          // Calcular el valor del eje
          let calculo = newScale - 1
          if (calculo === 0) {
            this.axis = 0
          } else {
            this.axis = (calculo * -400).toFixed(2)
            this.setThrottleDepth(this.axis) //seteamos el valor del thlottle con el valor del axis
            store.dispatch('setThrottleDepth', this.axis)
            console.log('throttle_depth', this.$store.getters.getThrottleDepth) // para mostrarlo en consola
            
          }

          this.currentRotation = newRotation
        }
      },
      getCurrentScale() {
        const transformValue = this.$el.querySelector('img').style.transform
        const scaleRegex = /scale\(([0-9.]+)\)/
        const match = scaleRegex.exec(transformValue)

        if (match && match[1]) {
          return parseFloat(match[1])
        }

        return 1
      },
      getCurrentRotation() {
        const transformValue = this.$el.querySelector('img').style.transform
        const rotationRegex = /rotate\((-?[0-9.]+)deg\)/
        const match = rotationRegex.exec(transformValue)

        if (match && match[1]) {
          return parseFloat(match[1])
        }
        return 0
      },
    },
  }
</script>

<style>
  .Tknob {
    /* centrado generico */
    display: flex;
    justify-content: center;
    align-items: flex-end;

    background-color: rgb(11, 143, 139);
    width: 80%;
    height: 80%;
    max-height: 80px;
    border-radius: 5%;
    margin: auto;
    position: relative;
  }
  .number {
    position: absolute;
    transform: translateY(-50px);
    color: white;
  }
</style>
