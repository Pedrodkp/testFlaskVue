<template>
  <div class="container">
    <button type="button" class="btn btn-primary">{{ msg }}</button>
  </div>
</template>

<script>
export default {
  name: 'PingPong',
  data() {
    return {
      msg: 'Hello! Its Test Vue and Bootstrap',
    };
  },
};
</script>
