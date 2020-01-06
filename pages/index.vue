<template>
  <div class="container">
    <div class="flex-column">
      <h1 class="title">
        json-compare
      </h1>
        <CompareForm />
        <p class="credits text-center">
            <a href="https://github.com/simmbiote/json-compare" target="_blank" rel="noopener noreferrer">
            <font-awesome-icon :icon="['fab', 'github']" swap-opacity size="lg" />
            </a>
        </p>
    </div>
  </div>
</template>

<script>

import Logo from '~/components/Logo.vue'
import CompareForm from '~/components/CompareForm.vue'
import { library } from '@fortawesome/fontawesome-svg-core'
import {   faGithub, faGithubAlt, faGithubSquare } from '@fortawesome/free-brands-svg-icons'
import {   faUserSecret } from '@fortawesome/free-solid-svg-icons'
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome'

library.add(faGithub)
library.add(faUserSecret)
library.add(faGithubAlt)
library.add(faGithubSquare)

export default {
  components: {
    Logo,
    CompareForm,
    FontAwesomeIcon
  }
}
</script>

<style>
@media print { * { -webkit-color-adjust: exact; } }

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
  margin-bottom: 20px;
}

.links {
  padding-top: 15px;
}
.table-striped tbody tr.investigate {
    background-color: rgb(247, 189, 82) !important;
}
.table-striped tbody tr.pass {
    background-color: rgb(145, 187, 145) !important;
}
.table-striped tbody tr.fail {
    background-color: rgb(240, 133, 133) !important;
}

.credits { margin-top: 40px;}
</style>
