<template>
  <v-container fluid pa-0>
    <v-row>
      <v-layout :class="is_small ? '' : 'align-center justify-center'">
        <v-layout :class="is_small ? 'justify-start' : 'justify-end'">
          <v-progress-circular
            :rotate="360"
            :size="120"
            :width="6"
            :value="user_total_rated/100"
            color="teal"
            :class="is_small ? '' : 'px-5 ma-5'"
          >
            {{ user_total_rated }}/100
          </v-progress-circular>
        </v-layout>
        <v-layout :class="is_small ? 'justify-end' : 'justify-start'">
          <v-progress-circular
            :rotate="360"
            :size="120"
            :width="6"
            :value="total_rated/30000"
            color="teal"
            :class="is_small ? '' : 'px-5 ma-5'"
          >
            {{ total_rated }} / 30000
          </v-progress-circular>
        </v-layout>
      </v-layout>
    </v-row>
    <v-row>
      <v-col :cols="cols" :offset="offset">
        <v-row dense>
          <v-col
            v-for="item in items"
            :key="item.image"
            :cols="item.flex"
          >
            <v-card
              :elevation="item.like ? 1 : 20"
              :outlined="item.like"
              tile
              height="100%"
            >
              <v-img
                contain
                :src="item.image"
                class="align-end"
                height="200px"
              />
              <v-card-actions>
                <v-card-title class="text-caption" v-text="item.category" />
                <v-spacer />
                <v-btn icon>
                  <v-icon :color="item.like ? 'green' : 'gray'" @click="like(item)">
                    mdi-heart
                  </v-icon>
                </v-btn>
              </v-card-actions>
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
              {{ confirm_text }}
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
      items: null,
      checked: 0,
      user_rate_count: 0
    }
  },

  computed: {
    is_small () {
      return this.$vuetify.breakpoint.xs
    },

    is_mobile () {
      return this.$vuetify.breakpoint.mdAndDown
    },

    cols () {
      return this.is_mobile ? 12 : 6
    },

    offset () {
      return this.is_mobile ? 0 : 3
    },

    confirm_text () {
      return this.portion()
        ? this.items.filter(x => x.like).length + '/' + this.items.length
        : 'Yes'
    },

    total_rated () {
      return this.checked
    },

    user_total_rated () {
      return this.user_rate_count
    }
  },

  created () {
    this.user_rate_count = this.user_cookie()
  },

  methods: {
    user_cookie () {
      if (!this.$cookies.get('completed_rates')) {
        const expires = new Date()
        expires.setHours(23, 59, 59, 999)
        this.$cookies.set('completed_rates', 0, {
          expires
        })
      }

      return this.$cookies.get('completed_rates')
    },

    rate (rating) {
      let portion = this.items.length
      if (!rating) {
        portion = 0
      } else if (this.portion()) {
        portion = this.items.filter(x => x.like).length
      }

      this.$api.Data.rate({
        look_id: this.items[0].look_id,
        rating: rating ? 'Like' : 'Dislike',
        data: this.portion() ? this.items.filter(x => x.like) : this.items,
        portion: portion + '/' + this.items.length
      })

      this.user_rate_count += 1
      this.$cookies.set('completed_rates', this.user_rate_count
      )
      this.generate()
    },

    portion () {
      return !(!this.items || this.items.filter(x => x.like).length === 0)
    },

    like (item) {
      const idx = this.items.indexOf(item)
      item.like = !item.like
      this.$set(this.items, idx, item)
    },

    generate () {
      const self = this
      this.$api.Data.data()
        .then(function (response) {
          self.items = response.data.style
          self.checked = response.data.checked
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
  background-color: white;
}

.cover {
  width: 100vh;
  max-height: 50vh;
}

</style>
