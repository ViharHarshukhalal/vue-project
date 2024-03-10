<template>
  <div class="checkout">
    <h2>Shopping Cart</h2>
    <!-- <div v-if="cart.length > 0"> -->
      <button class="GoBackButton">← Back to Lessons</button>
      <div v-for="item in cart" :key="item.id" class="cart-item">
        <p>{{ item.subject }} - £{{ item.price }} ({{ item.quantity }})</p>
        <button @click="removeFromCart(item.id)" class="RemoveFromCart">Remove</button>
      </div>
      <div class="total">
        Total: £{{ totalCost }}
      </div>
      
      <h2>Checkout</h2>
            <label>Name:</label>
        <input v-model="customerName" @input="validateName" type="text" placeholder="Enter your name"><br>
        <label>Phone Number:</label>
        <input v-model="phoneNumber" @input="validatePhone" type="tel" placeholder="Enter your phone number"><br>
        <button @click="checkout" :disabled="!isNameValid || !isPhoneValid" class="Checkout">Checkout</button>
    <!-- </div> -->
    <!-- <div v-else>
      <p>Your cart is empty.</p>
    </div> -->
  </div>
</template>

<script>
export default {
  name: 'Checkout',
  props: ['cart'],
  data() {
    return {
      customerName: '',
      phoneNumber: '',
      isNameValid: false,
      isPhoneValid: false,
    };
  },
  computed: {
    totalCost() {
      return this.cart.reduce((total, item) => total + item.price * item.quantity, 0);
    },
  },
  methods: {
    removeFromCart(itemId) {
      this.$emit('remove-item-from-cart', itemId);
    },
    validateName() {
                    this.isNameValid = /^[A-Za-z]+$/.test(this.customerName);
                },

                validatePhone() {
                    this.isPhoneValid = /^[0-9]+$/.test(this.phoneNumber);
                },
    checkout() {
      const order = {
        customerName: this.customerName,
        phoneNumber: this.phoneNumber,
        lessons: this.cart.map(item => ({
          lessonId: item.id,
          price: item.price,
          quantity: item.quantity,
        })),
        total: this.totalCost,
      };

      fetch('https://bookingsystemfinal-env.eba-wwtpjrbh.eu-west-2.elasticbeanstalk.com/collections/orders', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify(order),
      })
      .then(() => {
        alert('Order placed! Thank you for your purchase.');
        this.$emit('reset-cart');
        this.customerName = '';
        this.phoneNumber = '';
        this.$emit('order-completed');
      })
      .catch(error => {
        console.error('Error placing order:', error);
      });
    },
  }
}
</script>

<style>
/* Existing styles */
.checkout input {
  display: block;
  margin-top: 10px;
}
</style>
