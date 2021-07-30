<template>
  <div class="card" v-bind:class="{ flip: visible , hide: matched}" @click="selectCard">
    <div  class="card-face is-front">
      <img :src="`/images/${value}.png`" :alt="value" style="height: 120px; border-radius: 10px"  />
    </div>
    <div  class="card-face is-back"></div>
  </div>
</template>
<script>
export default {
  props: {
    matched: {
      type: Boolean,
      default: false
    },
    position: {
      type: Number,
      required: true
    },
    value: {
      type: String,
      required: true
    },
    visible: {
      type: Boolean,
      default: false
    }
  },
  setup(props, context) {
    
    const selectCard = () => {
      if(props.matched) return

      context.emit('select-card', {
        position: props.position,
        faceValue: props.value
      })
    }
    return {
      selectCard
    }
  }
}
</script>

<style scoped>
  .card {
    position: relative;
    transition: 0.5s transform ease-in;
    transform-style: preserve-3d;
  }
  .card.flip{
   transform: rotateY(180deg);
 }
  .card-face {
    width: 100%;
    height: 100%;
    border-radius: 10px;
    display: flex;
    align-items: center;
    justify-content: center;
    position: absolute;
    backface-visibility: hidden;
    
  }

  .card-face.is-front {
    background-color: blueviolet;
    color: white;
    transform:rotateY(180deg);
  }
  .card-image {
    max-width: 100%;
  }
  .card-face.is-back {
    background-image: url('../../public/images/League.png');
    background-repeat: no-repeat;
    background-size: cover;
    color: white;
  }
 .hide .card-face{
   /* display: none; */
   filter: grayscale(1);
 }
</style>
