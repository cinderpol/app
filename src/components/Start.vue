<template>
  <v-container fluid>
    <v-layout row wrap>
      <v-flex xs12 class="text-xs-center" mt-5>
        <h1>Event Listing</h1>
      </v-flex>
      <v-flex xs12 sm6 offset-sm3 mt-5>
        <form>
          <v-layout column>
            <v-flex>
              <v-text-field
                name="title"
                label="Title"
                id="title"
                type="text"
                required
                v-model="blog.title"></v-text-field>
            </v-flex>
            <v-flex>
              <v-text-field
                name="content"
                label="Content"
                id="content"
                type="text"
                required
                v-model="blog.content"></v-text-field>
            </v-flex>
            <v-flex>
                <v-select
                    :items="districts"
                    v-model="districtId"
                    @input="locale(districtId)"></v-select>
            </v-flex>
            <v-flex>
                <v-select
                    :items="locales"
                    ></v-select>
            </v-flex>
            <v-flex>
                <v-switch
                    v-model="subscription"
                    label="Subscribe"
                ></v-switch>
            </v-flex>
            <v-flex class="text-xs-center" mt-5>
              <v-btn color="primary" @click="createPost">Create</v-btn>
            </v-flex>
            <v-flex>
                <h1>{{ districtId }}</h1>
            </v-flex>
          </v-layout>
        </form>
      </v-flex>
    </v-layout>
  </v-container>
</template>

<script>
const Parse = require('parse')
export default {
  data () {
    return {
      blog: {
        title: '',
        content: ''
      },
      subscription: false,
      districts: [],
      locales: [],
      districtId: ''
    }
  },
  methods: {
    createPost () {
      const Post = Parse.Object.extend('Post')
      const post = new Post()
      post.set('title', this.blog.title)
      post.set('content', this.blog.content)
      post.set('subscription', this.subscription)
      post.save({
        success: (post) => {
          this.$swal('Successful!', post.get('title') + ' has been added', 'success')
          this.blog.title = ''
          this.blog.content = ''
        },
        error: error => console.log('Error ' + error)
      })
    },
    getDistricts () {
      let obj = []
      const District = Parse.Object.extend('District')
      const q = new Parse.Query(District)
      q.find({
        success: districts => {
          districts.forEach(district => {
            obj.push({
              text: district.get('name'),
              value: district.id
            })
          })
          this.districts = obj
        },
        error: error => console.log('Error ' + error)
      })
    },
    locale (districtId) {
      let obj = []
      const District = Parse.Object.extend('District')
      const district = new District()
      district.id = districtId
      const Locale = Parse.Object.extend('Locale')
      const q = new Parse.Query(Locale)
      q.include('district')
      q.equalTo('district', district)
      q.find({
        success: (locales) => {
          locales.forEach(locale => {
            obj.push({
              text: locale.get('name'),
              value: locale.id
            })
          })
          this.locales = obj
        },
        error: error => console.log('Error ' + error)
      })
    }
  },
  created () {
    console.log('yes')
    this.getDistricts()
  }
}
</script>