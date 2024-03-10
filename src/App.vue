<template>
  <div id="app">
    <div class="siteTitle">
      <h1>Booking System</h1>
    </div>
    <div class="controls">
      <label>Sort By:</label>
            <select v-model="sortAttribute">
                <option value="subject">Subject</option>
                <option value="location">Location</option>
                <option value="price">Price</option>
                <option value="spaces">Spaces</option>
            </select>
            <label>Sort Order:</label>
            <select v-model="sortOrder">
                <option value="asc">Ascending</option>
                <option value="desc">Descending</option>
            </select>

            <label>Search:</label>
            <input v-model="searchQuery" @input="performSearch" type="text" placeholder="Search lessons" class="SearchField">
            <button class="ShowCart" @click="toggleCartVisibility" :disabled="cartItemCount() === 0">{{ cartButtonText }}</button>
    </div>
    <div v-if="testConsole && showTestConsole">
      <strong class="test-elem">HTTPS Test: </strong>
            <a v-bind:href="serverURL" target="_blank">link</a>
            <button @click="deleteAllCaches" class="test-elem">
                <span class="fas fa-trash"></span>
                Delete All Caches
            </button>
            <button @click="unregisterAllServiceWorkers" class="test-elem">
                <span class="fab fa-uniregistry"></span>
                Unregister All ServiceWorkers
            </button>
            <button @click="reloadPage" class="test-elem">
                <span class="fas fa-sync"></span>
                Reload Page
            </button>
    </div> <br>
    
    
    

    <component :is="currentView"
           :lessons="sortedAndFilteredLessons"
           :cart="cart"
           @add-item-to-cart="addToCart"
           @remove-item-from-cart="removeFromCart"
           @toggle-view="toggleView">
           
</component>

  </div>
</template>

<script>
import Checkout from './components/Checkout.vue';
import Lesson from './components/LessonsList.vue';

export default {
	name: 'App',
	components: {
		Lesson,
		Checkout
	},
	data() {
		return {
			lessons: [],
			cart: [],
			isCartVisible: false,
			testConsole: true,
			showTestConsole: true,
			serverURL: "https://bookingsystemfinal-env.eba-wwtpjrbh.eu-west-2.elasticbeanstalk.com/collections/lessons",
			sortAttribute: 'subject',
			sortOrder: 'asc',
			searchQuery: '',
		};
	},
	computed: {
		currentView() {
			return this.isCartVisible ? 'Checkout' : 'Lesson';
		},
		cartButtonText() {
			return `Show Cart (${this.cart.reduce((total, item) => total + item.quantity, 0)})`;
		},
		sortedAndFilteredLessons() {
			// Filter lessons first based on the search query
			let result = this.lessons.filter((lesson) => {
				return lesson.subject.toLowerCase().includes(this.searchQuery.toLowerCase()) ||
					lesson.location.toLowerCase().includes(this.searchQuery.toLowerCase());
			});

			// Then sort the filtered results
			result.sort((a, b) => {
				if (this.sortOrder === 'asc') {
					return a[this.sortAttribute] > b[this.sortAttribute] ? 1 : -1;
				} else {
					return a[this.sortAttribute] < b[this.sortAttribute] ? 1 : -1;
				}
			});

			return result;
		},
	},
	methods: {
		toggleCartVisibility() {
			// Check if the cart is not empty before toggling the visibility
			if (this.cartItemCount() > 0) {
				this.isCartVisible = !this.isCartVisible;
			}
		},
		toggleView() {
			// Check if the cart is not empty before toggling the view
			if (this.cartItemCount() > 0) {
				this.isCartVisible = !this.isCartVisible;
			}
		},
		addToCart(lessonId) {
			const lesson = this.lessons.find(l => l.id === lessonId);
			const cartItem = this.cart.find(item => item.id === lessonId);
			if (lesson && lesson.spaces > 0) {
				lesson.spaces--;
				if (cartItem) {
					cartItem.quantity++;
				} else {
					this.cart.push({
						...lesson,
						quantity: 1
					});
				}
			}
		},
		removeFromCart(lessonId) {
			const index = this.cart.findIndex(item => item.id === lessonId);
			if (index !== -1) {
				const cartItem = this.cart[index];
				if (cartItem.quantity > 1) {
					cartItem.quantity--;
				} else {
					this.cart.splice(index, 1);
				}
				const lesson = this.lessons.find(l => l.id === lessonId);
				if (lesson) {
					lesson.spaces++;
				}
			}
		},
		deleteAllCaches() {
			caches.keys().then(function(names) {
				for (let name of names)
					caches.delete(name);
			});
			console.log("All Caches Deleted");
		},

		unregisterAllServiceWorkers() {
			navigator.serviceWorker.getRegistrations().then(function(registrations) {
				for (let registration of registrations) {
					registration.unregister()
				}
			});
			console.log("ServiceWorkers Unregistered");
		},
		reloadPage() {
			window.location.reload();
		},
		cartItemCount() {
			return this.cart.reduce((total, item) => total + item.quantity, 0);
		},
		performSearch() {
			// This method is invoked on input but you may not need to explicitly handle search here
			// if you're using the computed property for filtering. This method can be kept for future search enhancements.
		},
		clearCart() {

			this.cart = []; // Clears the cart
		},

	},
	created() {
		fetch("https://bookingsystemfinal-env.eba-wwtpjrbh.eu-west-2.elasticbeanstalk.com/collections/lessons")
			.then(response => response.json())
			.then(data => {
				this.lessons = data;
			})
			.catch(error => {
				console.error('Error fetching lessons:', error);
			});

		if ("serviceWorker" in navigator) {
			navigator.serviceWorker.register("service-worker.js");
		}
	}
};
</script>

<style>
/* Your styles here */
</style>
