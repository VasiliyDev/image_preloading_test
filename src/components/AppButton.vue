<script setup>

</script>

<template>
  <button class="custom-button">
  <slot></slot>
  </button>
</template>



<style scoped>
.custom-button {
    display: inline-block;
    padding: 10px 20px;
    font-size: 16px;
    font-weight: bold;
    color: white;
    background-color: #3498db;
    border: none;
    border-radius: 20px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);


    /* Hover effect */
    transition: background-color 0.3s ease;
}

.custom-button:hover {
    background-color: #2980b9; /* Darker blue on hover */
    cursor: pointer;
}
</style>