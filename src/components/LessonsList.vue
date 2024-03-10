<template>
  <div class="lesson-cards">
    <div class="lessons">
      <div v-for="lesson in lessons" :key="lesson.id" class="lesson-card">
        <h2>{{ lesson.subject }}</h2>
        <img :src="lesson.img" alt="Lesson image" class="lesson-image">
        <p><strong>Location:</strong> {{ lesson.location }}</p>
        <p><strong>Price:</strong> £{{ lesson.price }}</p>
        <p><strong>Spaces available:</strong> {{ lesson.spaces }}</p>
        <button v-if="lesson.spaces > 0" @click="addToCart(lesson.id)" class="addToCartButton">Add to Cart</button>
        <button v-else disabled class="soldOutButton">Sold Out</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Lesson',
  props: ['lessons'],
  methods: {
    addToCart(lessonId) {
      this.$emit('add-item-to-cart', lessonId);
    }
  }
}
</script>
