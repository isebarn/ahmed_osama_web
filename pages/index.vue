<template>
  <v-container fluid>
    <v-row>
      <v-col :cols="cols" :offset="offset">
        <v-row dense>
          <v-carousel v-if="is_mobile" class="image" hide-delimiter-background>
            <v-carousel-item
              v-for="(item, i) in items"
              :key="i"
            >
              <v-container fill-height>
                <v-card>
                  <v-img
                    contain
                    :src="item.image"
                    class="white--text align-end cover"
                    gradient="to bottom, rgba(0,0,0,.1), rgba(0,0,0,.5)"
                  >
                    <v-card-title v-text="item.category" />
                  </v-img>
                </v-card>
              </v-container>
            </v-carousel-item>
          </v-carousel>
          <v-col
            v-for="item in items"
            v-else
            :key="item.image"
            :cols="item.flex"
          >
            <v-card>
              <v-img
                contain
                :src="item.image"
                class="white--text align-end"
                gradient="to bottom, rgba(0,0,0,.1), rgba(0,0,0,.5)"
                height="200px"
              >
                <v-card-title v-text="item.category" />
              </v-img>
            </v-card>
          </v-col>
        </v-row>
        <v-row>
          <v-col cols="12">
            <v-btn block color="#A9C2C8" @click="generate">
              Generate
            </v-btn>
          </v-col>
        </v-row>
        <v-row>
          <v-col cols="6">
            <v-btn block color="#B5DEB7" @click="rate(true)">
              Yes
            </v-btn>
          </v-col>
          <v-col cols="6">
            <v-btn block color="#F7B1AC" @click="rate(false)">
              No
            </v-btn>
          </v-col>
        </v-row>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>

export default {

  fetch () {
    this.generate()
  },

  data () {
    return {
      items: null
    }
  },

  computed: {
    is_mobile () {
      return this.$vuetify.breakpoint.mdAndDown
    },

    cols () {
      return this.is_mobile ? 12 : 6
    },

    offset () {
      return this.is_mobile ? 0 : 3
    }
  },

  methods: {
    rate (rating) {
      this.$api.Data.rate({
        look_id: this.items[0].look_id,
        rating: rating ? 'Like' : 'Dislike',
        data: this.items
      })
      this.generate()
    },

    generate () {
      const self = this
      this.$api.Data.data()
        .then(function (response) {
          self.items = response.data
        })
        .catch(function (error) {
          console.log(error)
        })
        .then(function () {
        })
    }
  }
}
</script>

<style>
.image {
  background-color: gray;
}

.cover {
  width: 100vh;
}
</style>
