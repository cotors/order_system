<template>
  <div>

    <div class="card" style="line-height: 30px">
      <div>欢迎您，{{user.name }} 祝您今天过得开心！</div>
    </div>

  </div>
</template>

<script setup>
  const user = JSON.parse(localStorage.getItem('canteen-user') || '{}')
</script>