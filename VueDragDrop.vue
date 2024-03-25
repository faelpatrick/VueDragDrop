<template>
  <v-app>
    <v-container
      class="fill-height"
      style="position: relative; cursor: pointer"
    >
      <v-card
        v-for="(card, index) in cards"
        :key="card.id"
        color="red"
        :style="cardStyle(card)"
        @mousedown="selectCard(card, $event)"
        @mouseenter="hoverCard(true)"
        @mouseleave="hoverCard(false)"
        class="pa-5 card"
      >
        Card {{ card.id }}
      </v-card>
    </v-container>
  </v-app>
</template>

<script setup>
  import { ref, reactive } from 'vue'

  const cards = ref([
    { id: 1, position: { x: 50, y: 50 }, zIndex: 1 },
    { id: 2, position: { x: 200, y: 200 }, zIndex: 2 },
    { id: 3, position: { x: 300, y: 300 }, zIndex: 3 },
    { id: 4, position: { x: 400, y: 400 }, zIndex: 4 },
  ])
  const selectedCard = ref(null)
  const startPosition = ref({ x: 0, y: 0 })
  const containerStyle = reactive({ cursor: 'auto' })

  function selectCard(card, event) {
    selectedCard.value = card
    startPosition.value = {
      x: event.clientX - card.position.x,
      y: event.clientY - card.position.y,
    }
    containerStyle.cursor = 'grabbing'

    const maxZIndex = Math.max(...cards.value.map(c => c.zIndex)) + 1
    card.zIndex = maxZIndex

    const onMouseMove = e => {
      card.position.x = e.clientX - startPosition.value.x
      card.position.y = e.clientY - startPosition.value.y
    }

    const onMouseUp = () => {
      window.removeEventListener('mousemove', onMouseMove)
      window.removeEventListener('mouseup', onMouseUp)
      selectedCard.value = null
      containerStyle.cursor = 'auto'
    }

    window.addEventListener('mousemove', onMouseMove)
    window.addEventListener('mouseup', onMouseUp)
  }

  function hoverCard(isHovering) {
    containerStyle.cursor = isHovering ? 'grab' : 'auto'
  }

  function cardStyle(card) {
    return {
      position: 'absolute',
      top: `${card.position.y}px`,
      left: `${card.position.x}px`,
      zIndex: card.zIndex,
      cursor: selectedCard.value === card ? 'grabbing' : 'grab',
    }
  }
</script>

<style>
  .v-container {
    background-color: black;
  }

  .card {
    border-radius: 60px;
    border: solid 4px;
  }
</style>
