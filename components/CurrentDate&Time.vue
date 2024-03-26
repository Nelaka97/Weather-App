<template>
  <div>
    <p>{{ currentDate }} {{ currentTime }}</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      currentDate: '',
      currentTime: ''
    }
  },
  mounted() {
    this.updateDateTime();
    setInterval(this.updateDateTime, 1000); // Update every second
  },
  methods: {
    updateDateTime() {
      const now = new Date();
      this.currentDate = now.toDateString();
      this.currentTime = now.toLocaleTimeString();
    }
  }
}
</script>
