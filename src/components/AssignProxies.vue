<template>
  <v-container>
    <v-btn @click="doAssignProxies">Assign Proxies</v-btn>
    <ul>
      <li
        v-for="[holder, assignments] in Object.entries(assignments)"
        :key="holder"
      >
        {{ holder }} holds {{ assignments.length }} proxy/ies for:
        <span v-for="(assignment, key) in assignments" :key="assignment">
          {{ assignment }}<span v-if="key + 1 != assignments.length">,</span>
        </span>
      </li>
    </ul>
  </v-container>
</template>

<script>
import { mapState } from "vuex";

export default {
  name: "AssignProxies",

  data: () => ({
    transcript: "",
    proxies: null,
  }),

  computed: {
    ...mapState({
      memberList: (state) => state.memberList,
      presentList: (state) => state.presentList,
    }),
    assignments: function () {
      let assignments = {};
      if (!this.proxies) return assignments;

      Object.entries(this.proxies).forEach(([represented, holder]) => {
          represented = represented.trim();
          holder = holder.trim();

          if (assignments[holder]) {
            assignments[holder].push(represented);
          } else {
            assignments[holder] = [represented];
          }
      });

      return assignments;
    }
  },

  methods: {
    async doAssignProxies() {
      let res = await fetch(process.env.VUE_APP_PROXY_SOLVER_API, {
        method: "POST",
          headers: {
            'Content-Type': 'application/json',
            },
        body: JSON.stringify({
          memberList: this.memberList,
          presentList: this.presentList,
        }),
      });
      this.proxies = await res.json();
    },
  },
};
</script>

  <style src="vue-d3-network/dist/vue-d3-network.css"></style>