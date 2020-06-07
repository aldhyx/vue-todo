<template>
  <div class="container">
    <header>
      <img src="@/assets/logo.png" alt="logo" />
      <h1>Vue Todo App</h1>
    </header>
  </div>
</template>

<script>
export default {
  name: 'Home'
};
</script>
<style lang="scss" scoped>
.container {
  width: 768px;
  margin: 0 auto;
  background-color: white;
  height: 100vh;
  overflow: auto;
  box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.1);
  padding: 16px;
  box-sizing: border-box;

  header {
    text-align: center;

    img {
      width: 100px;
      margin-bottom: 16px;
    }

    h1 {
      color: #41b883;
      text-transform: uppercase;
      letter-spacing: 2px;
    }
  }
}
</style>
