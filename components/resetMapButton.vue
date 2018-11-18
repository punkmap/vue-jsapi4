<template>
 <button :class="type"><slot /></button>
</template>
<script>
 export default {
   name: 'resetMapButton',
   props: [ 'type' ]
 }
</script>
