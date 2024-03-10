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
<!-- 
<style>
.lessons {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-around;
}
.lesson-card {
  border: 1px solid #ddd;
  margin: 10px;
  padding: 20px;
  width: calc(33.333% - 20px);
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}
.lesson-card h2 {
  margin-top: 0;
}
.lesson-image {
  max-width: 100%;
  height: auto;
}
.addToCartButton, .soldOutButton {
  background-color: #007bff;
  color: white;
  border: none;
  padding: 10px 20px;
  cursor: pointer;
  margin-top: 10px;
}
.soldOutButton {
  background-color: #ccc;
  cursor: not-allowed;
}
</style> -->
