<template>
  <PopoverComponent />

  <PaginationComponent />

  <div class="bg-white">
    <div class="max-w-2xl mx-auto py-0 px-4 sm:py-0 sm:px-6 lg:max-w-7xl lg:px-8">
      <h2 class="sr-only">Posts</h2>

      <div class="grid grid-cols-1 gap-y-10 sm:grid-cols-2 gap-x-6 lg:grid-cols-3 xl:grid-cols-4 xl:gap-x-8">
        <a v-for="post in displayedPosts" :key="post.id" class="group">
          <div class="w-full aspect-w-1 aspect-h-1 bg-gray-200 rounded-lg overflow-hidden xl:aspect-w-7 xl:aspect-h-8">
            <img src="../src/assets/image.jpeg" alt="Travel"
                 class="w-full h-full object-center object-cover group-hover:opacity-75"/>
          </div>
          <p class="mt-1 text-lg font-medium text-gray-900">
            {{ post.title }}
          </p>
          <h3 class="mt-4 text-sm text-gray-700">
            {{ post.body }}
          </h3>
        </a>
      </div>
    </div>
  </div>
</template>

<script>
import PopoverComponent from "./components/PopoverComponent.vue";
import PaginationComponent from "./components/PaginationComponent.vue";
import {ChevronLeftIcon, ChevronRightIcon} from '@heroicons/vue/solid';
import axios from "axios";

export default {
  data() {
    return {
      baseUrl: 'https://jsonplaceholder.typicode.com',
      posts: [],
      users: [],
      page: 1,
      perPage: 10,
      pages: []
    }
  },
  components: {
    PopoverComponent,
    PaginationComponent,
    ChevronLeftIcon,
    ChevronRightIcon
  },
  methods: {
    setPages() {
      let numberOfPages = Math.ceil(this.posts.length / this.perPage);
      for (let index = 1; index <= numberOfPages; index++) {
        this.pages.push(index);
      }
    },
    paginate(posts) {
      let page = this.page;
      let perPage = this.perPage;
      let from = (page * perPage) - perPage;
      let to = (page * perPage);
      return posts.slice(from, to);
    }
  },
  computed: {
    displayedPosts() {
      return this.paginate(this.posts);
    }
  },
  watch: {
    posts() {
      this.setPages();
    }
  },
  filters: {
    trimWords(value) {
      return value.split(" ").splice(0, 20).join(" ") + '...';
    }
  },

  async created() {
    await axios
        .get(this.baseUrl + '/posts')
        .then(response => {
          console.log('response', response)
          this.posts = response.data;
        })
        .catch(error => {
          console.log(error);
        })

    await axios
        .get(this.baseUrl + '/users')
        .then(response => {
          console.log('response', response)
          this.users = response.data;
        })
        .catch(error => {
          console.log(error);
        })
  },
};

</script>