<template>
  <div class="container">
    <div class="flex-column">
      <h1 class="title">
        json-compare
      </h1>
        <CompareForm />
    </div>
  </div>
</template>

<script>
import Logo from '~/components/Logo.vue'
import CompareForm from '~/components/CompareForm.vue'

export default {
  components: {
    Logo,
    CompareForm
  }
}
</script>

<style>
.container {
  margin: 0 auto;

  justify-content: center;
  align-items: center;
  text-align: center;
 
}
.flex-column {
    align-items: center;
  text-align: center;
  min-height: 100vh;
  padding: 2vh 0;
  display: flex;
  justify-content: center;

}
.title {
  font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  display: block;
  font-weight: 300;
  font-size: 30px;
  color: #35495e;
  letter-spacing: 1px;
}

.links {
  padding-top: 15px;
}
.table-striped tbody tr.investigate {
    background-color: rgb(247, 189, 82);
}
.table-striped tbody tr.pass {
    background-color: rgb(145, 187, 145);
}
.table-striped tbody tr.fail {
    background-color: rgb(240, 133, 133);
}
</style>
