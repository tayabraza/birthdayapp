<template>
  <div class="modal">
    <p>
      Employee already exist!
    </p>
  </div>
</template>
  
<script>
    export default {
      name: 'Modal'
    }
</script>

<style>
.modal{
  position: fixed;
  top: 300px;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 300px;
  text-align: center;
  padding: 50px;
  background: rgb(255, 255, 255) ;
  color: red;
  border-radius: 10px;
  box-shadow: 0 0 5px 0px;
}
</style>