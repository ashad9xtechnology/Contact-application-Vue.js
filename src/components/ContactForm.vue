<template>
    <form class="form-box" @submit.prevent="submitForm">
        <div class="name">
            <label for="name">Name:</label>
            <input type="text" id="name" v-model="form.name" required>
        </div>
        <div class="email">
            <label for="email">Email:</label>
            <input type="email" id="email" v-model="form.email" required>
        </div>
        <div class="message">
            <label for="message">Message:</label>
            <textarea id="message" v-model="form.message" required></textarea>
        </div>
        <button type="submit">Submit</button>
        <div v-if="successMessage" class="success-message">
            {{ successMessage }}
        </div>
        <div v-if="errorMessage" class="error-message">
            {{ errorMessage }}
        </div>
    </form>
</template>


<style scoped>
.form-box {
    display: flex;
    justify-content: center;
    flex-direction: column;
    left: 0;
    gap: 20px;
    padding: 50px;
    border-radius: 15px;
    background-color: rgb(0, 0, 0);
}
.name {
    display: flex;
    justify-content: space-between;
}
.email {
    display: flex;
    justify-content: space-between;
}
.message {
    display: flex;
    justify-content: space-between;
}
.success-message {
  color: green;
  margin-top: 10px;
}
.error-message {
  color: red;
  margin-top: 10px;
}
</style>


<script>
import { reactive, ref } from 'vue'
import axios from 'axios'

export default {
  setup() {
    const form = reactive({
      name: '',
      email: '',
      message: ''
    })

    const successMessage = ref('')
    const errorMessage = ref('')
    const isSubmitting = ref(false)

    const submitForm = async () => {
      successMessage.value = ''
      errorMessage.value = ''
      isSubmitting.value = true

      try {
        const response = await axios.post('https://contact-application-backend.onrender.com/api/contact', form)
        successMessage.value = response.data.message || 'Form submitted successfully!'
        // Reset form
        form.name = ''
        form.email = ''
        form.message = ''
      } catch (error) {
        if (error.response) {
          // The request was made and the server responded with a status code
          // that falls out of the range of 2xx
          errorMessage.value = error.response.data.error || 'An error occurred while submitting the form.'
        } else if (error.request) {
          // The request was made but no response was received
          errorMessage.value = 'No response received from the server. Please try again later.'
        } else {
          // Something happened in setting up the request that triggered an Error
          errorMessage.value = 'An error occurred while submitting the form. Please try again.'
        }
      } finally {
        isSubmitting.value = false
      }
    }

    return { 
      form, 
      submitForm, 
      successMessage, 
      errorMessage,
      isSubmitting
    }
  }
}
</script>