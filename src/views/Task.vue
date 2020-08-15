<template>
  <div class="row">
    <div v-if="task" class="col s6 offset-s3">
      <h1>{{task.title}}</h1>

      <form @submit.prevent="submitHandler">
        <div class="chips" ref="chips"></div>

        <div class="input-field">
          <textarea style="min-height: 300px" v-model="description" id="description" class="materialize-textarea"></textarea>
          <label for="description">Textarea</label>
          <span class="character-counter" style="float: right; font-size: 12px;">{{description.length}}/2048</span>
        </div>

        <input type="text" ref="datepicker">
        <div v-if="task.status !== 'completed'">
          <button class="btn" type="submit" style="margin-right: 1rem">Update</button>
          <button class="btn blue" type="button" @click="completeTask">Complete task</button>
        </div>

      </form>
    </div>
    <p v-else>Task no found</p>
  </div>
</template>

<script>
export default {
  computed: {
    task() {
      return this.$store.getters.taskById(+this.$route.params.id)
    }
  },
  data: () => ({
    description: '',
    chips: null,
    date: null
  }),
  mounted() {
    this.description = this.task.description
    this.chips = M.Chips.init(this.$refs.chips, {
      placeholder: 'Task tags',
      data: this.task.tags
    })
    this.date = M.Datepicker.init(this.$refs.datepicker, {
      format: 'dd.mm.yyyy',
      firstDay: 1,
      defaultDate: new Date(this.task.date),
      setDefaultDate: true

    })
    setTimeout(() => {
      M.updateTextFields()
    }, 0)
  },
  methods: {
    submitHandler() {
      this.$store.dispatch('updateTask', {
        id: this.task.id,
        description: this.description,
        date: this.date.date
      })
      this.$router.push('/list')
    },
    destroyed() {
      if (this.date && this.date.destroy) {
        this.date.destroy()
      }
      if (this.chips && this.chips.destroy) {
        this.chips.destroy()
      }
    },
    completeTask() {
      this.$store.dispatch('completeTask', this.task.id)
      this.$router.push('/list')
    }
  }

}
</script>

<style>

</style>
