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
                <v-card style="background-color: white">
                  <v-img
                    contain
                    :src="item.image"
                    class="align-end cover"
                  />
                  <v-card-actions>
                    <v-card-title class="black--text" v-text="item.category" />
                    <v-spacer />
                    <v-btn icon>
                      <v-icon class="outlined" :color="item.like ? 'green' : '#6D6D71'" @click="like(item)">
                        mdi-heart
                      </v-icon>
                    </v-btn>
                  </v-card-actions>
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
                height="200px"
              />
              <v-card-actions>
                <v-card-title class="black--text" v-text="item.category" />
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
    },

    confirm_text () {
      return this.portion()
        ? this.items.filter(x => x.like).length + '/' + this.items.length
        : 'Yes'
    }
  },

  methods: {
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
  background-color: white;
}

.cover {
  width: 100vh;
  max-height: 50vh;
}

</style>
