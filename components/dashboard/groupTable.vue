<template>
  <div class="my-challenges__table-container">
    <v-app>
      <div v-if="!hasGroups" class="my-challenges__empty">
        <br>
        <br>
        <br>
      </div>
      <v-text-field
        v-else
        v-model="search"
        append-icon="mdi-magnify"
        label="Search"
        single-line
        hide-details
        class="search-field"
      />
      <div style="
        display: flex;
        justify-content: center;
        margin-bottom: 30px;">
        <input v-model="id" placeholder="Join by code" @keyup.enter="joinGroup" style="border-bottom: solid 1px grey;width: 500px;"/>
      <button @click="this.joinGroup" style="background-color: blue;
    width: 50px;
    height: 3.5rem;
    border-radius: 0.5rem;
    ">join</button>
      </div>
      <div v-if="!hasGroups" class="my-challenges__empty">
        <p>
          the groups you join will appear here.
        </p>
      </div>
      <v-data-table v-else
        :value="selected"
        @input="$emit('input', $event)"
        :search="search"
        :headers="headers"
        :items="items"
        show-select
        class="elevation-2"
        item-key="_id"
      >
        <template v-slot:[`item.edit`]="{ item }">
          <DashboardButton type="join" @click="item.join" />
        </template>
      </v-data-table>
    </v-app>
  </div>
</template>

<script>
export default {
  model: {
    prop: "selected",
    event: "input"
  },
  props: {
    selected: Array,
    headers: Array,
    items: Array
  },
  data() {
    return {
      search: "",
      id: ''
    };
  },
  computed:{
    user() {
      return this.$store.getters.user;
    },
    hasGroups() {
      return this.user?.groups && this.user.groups.length > 0
    },
  },
  methods: { 
    async joinGroup() {
      const inviteId = this.id
          const res = await this.$axios.$post("/xapi", {
            joinGroup: inviteId
          });

          console.log(res);

      },
  }
};
</script>

<style lang="scss">
.my-challenges {
  &__table-container {
    margin-bottom: 1.5rem;
    width: 100%;
    align-self: flex-start;

    & > .button {
      margin-top: 4rem !important;

      i {
        margin-right: 0.5rem;
      }
    }

    .search-field {
      padding: 0;
      margin: 0 auto 5rem;
      width: 100%;
      max-width: 55rem;

      @include respond(mobile) {
        margin-bottom: 4rem;
      }
    }
  }

  &__actions {
    display: flex;

    & > .dashboard-button {
      margin: 0;

      &:not(:last-child) {
        margin-right: 0.75rem;
      }
    }
  }
}
</style>
