<template>
  <header>
    <h1>Todo</h1>
    <button @click="changeTheme">
      <img src="../images/icon-moon.svg" alt="" class="moon" />
      <img src="../images/icon-sun.svg" alt="" class="sun" />
    </button>
  </header>
</template>

<script>
export default {
  name: "Header",
  methods: {
    changeTheme(){
      document.body.classList.toggle("dark")
    }
  },
};
</script>

<style scoped>
header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin: 0 auto 40px;
  width: 100%;
  max-width: 540px;
}
header h1 {
  color: hsl(0, 0%, 98%);
  text-transform: uppercase;
  letter-spacing: .7rem;
  font-size: 24px;
  font-weight: 700;
}
button {
  border: none;
  background: none;
  cursor: pointer;
}
.sun, .moon {
  width: 22px;
  height: 22px;
}
.sun {
  display: none;
}
.dark .moon{
  display: none;
}
.dark .sun{
  display: inline-block;
}
</style>
