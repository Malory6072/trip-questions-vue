<template lang="pug">
.container.purple-content.top-padding
  .row
    .col-8.text-center.col-margin
      h1.mb-5 Plan List
      PlanCard.mt-2(v-for="plan in plans" :key="plan.id" :plan="plan")
      template(v-if="page != 1")
        router-link.plan-list__link(:to="{ name: 'plan-list', query: { page: page - 1 } }" rel="prev") Prev Page
      template(v-if="hasNextPage")
        span.d-inline(v-if="page != 1")  |
        router-link.plan-list__link(:to="{ name: 'plan-list', query: { page: page + 1 } }" rel="next")  Next Page

</template>

<script>
import PlanCard from "@/components/PlanCard.vue"
import { mapState } from "vuex"
import store from "@/store/store"

function getPagePlans(routeTo, next) {
  const currentPage = parseInt(routeTo.query.page || 1)
  store
    .dispatch("fetchPlans", {
      page: currentPage
    })
    .then(() => {
      // pass it into the component as a prop, so we can print next pages
      routeTo.params.page = currentPage
      next()
    })
}

export default {
  components: {
    PlanCard
  },
  beforeRouteEnter(routeTo, routeFrom, next) {
    // Before we enter the route
    getPagePlans(routeTo, next)
  },
  beforeRouteUpdate(routeTo, routeFrom, next) {
    // Before we update the route
    getPagePlans(routeTo, next)
  },
  computed: {
    page() {
      return parseInt(this.$route.query.page) || 1
    },
    hasNextPage() {
      return this.plansTotal > this.page * this.perPage
    },
    ...mapState(["plans", "plansTotal", "perPage"])
  }
}
</script>

<style lang="sass">
.top-padding
  padding-top: 70px

.col-margin
  margin: 0 auto

.plan-list__link
  color: #004085
</style>
