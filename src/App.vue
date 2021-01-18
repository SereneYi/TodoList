<template>
  <div id="app" class="container">
    
   <h1>Todos</h1>
    <todo-list></todo-list>
  </div>
</template>

<script>
import TodoList from './components/TodoList'

export default {
  name: "App",
  components: {
    TodoList,
  },
};
</script>

<style>
* {
  box-sizing: border-box;
}

.container{
  max-width: 600px;
  margin: 0 auto;
}

h1{
  font-size: 75px;
  background: -webkit-linear-gradient(rgb(39, 86, 214), rgb(205, 209, 216));
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  text-align: center;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
  font-size: 23px;
}

#element-with-background-image {
   position: relative;
   background-image: url("//spin.atomicobject.com/wp-content/uploads/20170324102432/portfolio-tips-feature-image.jpg");
}
 
#color-overlay {
   position: absolute;
   top: 0;
   left: 0;
   width: 100%;
   height: 100%;
   background-color: black;
   opacity: 0.6;
}
</style>
