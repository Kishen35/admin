<template>
  <div class="container">
    <template v-if="appStatus === 'loading'">
      <LoadingSpinner/>
    </template>
    <template v-else>
      <h1>{{ list.title }}</h1>
      <p>{{ list.description }}</p>
      <promises-table :promises="promises" exclude="[source_name, politician_name]"/>
    </template>
  </div>
</template>

<script>
import { getList, getPromise } from '@/api'
import PromisesTable from '@/components/PromisesTable'
import LoadingSpinner from '@/components//LoadingSpinner'

export default {
  name: 'List',
  data () {
    return {
      appStatus: 'loading',
      list: {},
      promises: []
    }
  },
  components: { LoadingSpinner, PromisesTable },
  async created () {
    try {
      this.list = await this.getListHandler(this.$route.params.id)
      const pendingPromises = this.list.promise_ids.map(promiseId =>
        getPromise(promiseId)
      )
      this.promises = await Promise.all(pendingPromises)
      this.appStatus = ''
    } catch (e) {
      console.error(e)
    }
  },
  methods: {
    async getListHandler (id) {
      return getList(id)
    },
    async getPromiseHandler (id) {
      return getPromise(id)
    }
  }
}
</script>

<style scoped>
</style>
