<template>
  <div id="app">
    <div style="padding-top: 600px;"></div>

    <ee-block class="test-block" initialState="closed" :headerFixedOffset="0" ref="block1" :closedHeight="50">
      <template #header>
        <header>
          <button @click="$refs.block1.toggle()">expand</button>
        </header>
      </template>
      <template #content>
        <main>YO hello yo hello there Monday is great but not greater than FRIDAI!!</main>
        <main>YO hello yo hello there Monday is great but not greater than FRIDAI!!</main>
        <main>YO hello yo hello there Monday is great but not greater than FRIDAI!!</main>
        <main>YO hello yo hello there Monday is great but not greater than FRIDAI!!</main>
        <main>YO hello yo hello there Monday is great but not greater than FRIDAI!!</main>
      </template>
      <template #footer>
        <footer>FOOTA</footer>
      </template>
    </ee-block>

    <ee-block initialState="open">
      <template #header>HEAD</template>
      <template #content>CONTENT</template>
      <template #footer>FOOTER</template>
    </ee-block>

    <div style="padding-top: 600px;"></div>
  </div>
</template>

<script>
import eeBlock from "./components/eeBlock.vue";
export default {
  name: "App",
  components: {
    "ee-block": eeBlock
  }
};
</script>

<style lang="scss">
* {
  box-sizing: border-box;
  margin: 0px;
  padding: 0px;
}

body {
  background-color: #ebebeb;
  padding: 20px;
}

#app {
  font-family: sans-serif;
  font-size: 12px;
  height: 200vh;
  width: 100%;
}

.test-block {
  .ee-block__header {
    background-color: red;
    border: 1px solid #aaa;
    padding: 20px;
  }

  .ee-block__content {
    border-left: 1px solid #aaa;
    border-right: 1px solid #aaa;
    padding: 20px;
  }

  .ee-block__footer {
    background-color: red;
    border-left: 1px solid #aaa;
    border-right: 1px solid #aaa;
    border-bottom: 1px solid #aaa;
    padding: 20px;
  }

  &.is-open {
    .ee-block__header {
      border-top: 1px solid #aaa;
    }
    .ee-block__footer {
      border-top: 1px solid #aaa;
    }
  }
}
</style>
