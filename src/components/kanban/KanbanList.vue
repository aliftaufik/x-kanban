<template>
  <div>
    <div class="container" v-if="kanbanList.length <= 0">
      <div class="content has-text-centered">
        <h5 class="title is-5">Your Kanban list is empty...</h5>
      </div>
    </div>
    <button class="button is-primary" @click="modalCreate = true">
      Create new
    </button>
    <div class="table-container" v-if="kanbanList.length > 0">
      <table class="table is-striped is-hoverable is-fullwidth">
        <thead>
          <tr>
            <th>Kanban Name</th>
            <th>Description</th>
            <th>Members</th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="kanban in kanbanList"
            :key="kanban.id"
            @click="onKanbanClick(kanban)"
          >
            <td>{{ kanban.kanban_name }}</td>
            <td>{{ kanban.description }}</td>
            <td>{{ kanban.members.length }}</td>
          </tr>
        </tbody>
      </table>
    </div>

    <CreateKanbanModal
      v-if="modalCreate"
      @on-close-modal="modalCreate = false"
    ></CreateKanbanModal>
  </div>
</template>

<script>
import CreateKanbanModal from '@/components/kanban/CreateKanbanModal'
import db from '@/config/db'

export default {
  components: {
    CreateKanbanModal
  },
  data() {
    return {
      modalCreate: false,
      kanbanSnapshot: null
    }
  },
  methods: {
    onKanbanClick(kanban) {
      this.$router.push(`/kanban/${kanban.id}`)
    }
  },
  computed: {
    kanbanList() {
      return this.$store.state.kanbanList
    }
  },
  created() {
    this.kanbanSnapshot = db
      .collection('kanbans')
      .onSnapshot(kanbanSnapshot => {
        kanbanSnapshot = kanbanSnapshot.docs.map(kanban => {
          const { kanban_name, description, members } = kanban.data()
          return {
            id: kanban.id,
            kanban_name,
            description,
            members
          }
        })
        this.$store.commit('SET_KANBAN_LIST', kanbanSnapshot)
      })
  },
  beforeDestroy() {
    this.kanbanSnapshot()
  }
}
</script>

<style lang="scss">
tbody tr:hover {
  cursor: pointer;
}
</style>
