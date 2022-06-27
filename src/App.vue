<template>
  <PopoverComponent />

  <div class="max-w-2xl mx-auto py-16 px-4 sm:py-10 sm:px-0 lg:max-w-7xl lg:px-8">
    <div class="bg-white px-4 py-3 flex items-center justify-between sm:px-6">
      <div class="flex-1 flex justify-between sm:hidden">
        <a href="#"
           class="relative inline-flex items-center px-4 py-2 border border-gray-300 text-sm font-medium rounded-md text-gray-700 bg-white hover:bg-gray-50">
          Previous </a>
        <a href="#"
           class="ml-3 relative inline-flex items-center px-4 py-2 border border-gray-300 text-sm font-medium rounded-md text-gray-700 bg-white hover:bg-gray-50">
          Next </a>
      </div>
      <div class="hidden sm:flex-1 sm:flex sm:items-center sm:justify-between">
        <div>
          <p class="text-sm text-gray-700">
            Showing
            {{ ' ' }}
            <span class="font-medium">{{ page }}</span>
            {{ ' ' }}
            to
            {{ ' ' }}
            <span class="font-medium">10</span>
            {{ ' ' }}
            of
            {{ ' ' }}
            <span class="font-medium">{{ posts.length }}</span>
            {{ ' ' }}
            results
          </p>
        </div>
        <div>
          <nav class="relative z-0 inline-flex rounded-md shadow-sm -space-x-px" aria-label="Pagination">
            <button
                v-if="page !== 1"
                @click="page--"
                class="relative inline-flex items-center px-2 py-2 rounded-l-md border border-gray-300 bg-white text-sm font-medium text-gray-500 hover:bg-gray-50">
              <span class="sr-only">Previous</span>
              <ChevronLeftIcon class="h-5 w-5" aria-hidden="true"/>
            </button>
            <a v-for="pageNumber in pages.slice(page-1, page+5)" @click="page = pageNumber" href="#" aria-current="page"
               class="z-10 bg-indigo-50 border-indigo-500 text-indigo-600 relative inline-flex items-center px-4 py-2 border text-sm font-medium">
              {{ pageNumber }}
            </a>
            <button
                @click="page++" v-if="page < pages.length"
                class="relative inline-flex items-center px-2 py-2 rounded-r-md border border-gray-300 bg-white text-sm font-medium text-gray-500 hover:bg-gray-50">
              <span class="sr-only">Next</span>
              <ChevronRightIcon class="h-5 w-5" aria-hidden="true"/>
            </button>
          </nav>
        </div>
      </div>
    </div>
  </div>

  <div class="bg-white">
    <div class="max-w-2xl mx-auto py-0 px-4 sm:py-0 sm:px-6 lg:max-w-7xl lg:px-8">
      <h2 class="sr-only">Posts</h2>

      <div class="grid grid-cols-1 gap-y-10 sm:grid-cols-2 gap-x-6 lg:grid-cols-3 xl:grid-cols-4 xl:gap-x-8">
        <a v-for="post in displayedPosts" :key="post.id" class="group">
          <div class="w-full aspect-w-1 aspect-h-1 bg-gray-200 rounded-lg overflow-hidden xl:aspect-w-7 xl:aspect-h-8">
            <img src="../src/assets/images/image.jpeg" alt="Travel"
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