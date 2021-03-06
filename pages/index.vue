<template>
  <div class="home">
    <Hero />
    <!-- Search -->
    <div class="container search">
      <input @input="sortMovies" v-model="searchInput" type="text" placeholder="Search">
      <button class="button" v-show="searchInput !==''" @click="$fetch" >Search</button>
    </div>

    <!-- Loading -->
    <Loading v-if="$fetchState.pending"/>

  <!-- Movies -->
  <div class="container movies">
    <!-- Searched Movies -->
    <div v-if="showSearchedMovies" id="movie-grid" class="movies-grid">
      <div v-for="movie in searchedMovies"  :key="movie.id" class="movie" >
        <div class="movie-img">
          <img :src="`https://image.tmdb.org/t/p/w500/${movie.poster_path}`" alt="">
          <p class="review">{{movie.vote_average}}</p>
          <p class="overview">{{movie.overview}}</p>
        </div>
        <div class="info">
          <p class="title">{{movie.title.slice(0,25)}} <span v-if="movie.title.length > 25">...</span></p>
          <p class="release">
            Released: {{
              new Date(movie.release_date).toLocaleDateString()
            }}
          </p>
          <NuxtLink class="button button-light" :to="{name: 'movies-id', params:{id: movie.id}}">
          Get More Info</NuxtLink>
        </div>
      </div>
    </div>

    <!-- Now Streaming Movies -->
    <div v-if="showMovies" id="movie-grid" class="movies-grid">
      <div v-for="movie in movies"  :key="movie.id" class="movie" >
        <div class="movie-img">
          <img :src="`https://image.tmdb.org/t/p/w500/${movie.poster_path}`" alt="">
          <p class="review">{{movie.vote_average}}</p>
          <p class="overview">{{movie.overview}}</p>
        </div>
        <div class="info">
          <p class="title">{{movie.title.slice(0,25)}} <span v-if="movie.title.length > 25">...</span></p>
          <p class="release">
            Released: {{
              new Date(movie.release_date).toLocaleDateString()
            }}
          </p>
          <NuxtLink class="button button-light" :to="{name: 'movies-id', params:{id: movie.id}}">
          Get More Info</NuxtLink>
        </div>
      </div>
    </div>

    <!-- Dramas -->
    <h1 v-if="showMovies" style="color:#fff; padding:30px 0;">New Dramas</h1>
        <div v-if="showMovies" id="movie-grid" class="movies-grid">
          
      <div v-for="drama in dramas"  :key="drama.id" class="movie" >
        <div class="movie-img">
          <img :src="`https://image.tmdb.org/t/p/w500/${drama.poster_path}`" alt="">
          <p class="review">{{drama.vote_average}}</p>
          <p class="overview">{{drama.overview}}</p>
        </div>
        <div class="info">
          <p class="title">{{drama.title.slice(0,25)}} <span v-if="drama.title.length > 25">...</span></p>
          <p class="release">
            Released: {{
              new Date(drama.release_date).toLocaleDateString()
            }}
          </p>
          <NuxtLink class="button button-light" :to="{name: 'movies-id', params:{id: drama.id}}">
          Get More Info</NuxtLink>
        </div>
      </div>
    </div>
  </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'IndexPage',
  head(){
    return{
      title: 'Movie App - Latest Streaming Movie Info',
      meta:[
        {
          hid: 'description',
          name: 'description',
          content: 'DevAdedeji Movie App'
        }
        ]
    }
  },
  data(){
    return{
      movies:[],
      dramas: [],

      showMovies:true,
      searchInput: '',
      searchedMovies: [], 
      showSearchedMovies:false
    }
  },
  async fetch(){
    this.searchInput === '' ? await this.getMovies() : await this.searchMovie()  
    await this.getDramas() 
  },  
  methods:{
    async getMovies(){
      const data = await axios.get('https://api.themoviedb.org/3/discover/movie?sort_by=popularity.desc&api_key=804a74ac9a1ab71039c990f01ef39b23&page=1')
      this.movies = data.data.results
    },
    async getDramas(){
      const data = await axios.get('https://api.themoviedb.org/3/discover/movie?with_genres=18&sort_by=vote_average.desc&vote_count.gte=10&api_key=804a74ac9a1ab71039c990f01ef39b23&page=1')
      this.dramas = data.data.results
    },
    async searchMovie(){
      const data = await axios.get(`https://api.themoviedb.org/3/search/movie?api_key=804a74ac9a1ab71039c990f01ef39b23&query=${this.searchInput}`)
      this.searchedMovies = data.data.results
      this.showMovies = false
      this.showSearchedMovies = true
    },
    sortMovies(){
    if(this.searchInput === ''){
      this.showMovies = true
      this.showSearchedMovies = false
    }
    },
  }
}
</script>

<style lang="scss" scoped>
.home {
  .loading {
    padding-top: 120px;
    align-items: flex-start;
  }
  .search {
    display: flex;
    padding: 32px 16px;
    input {
      max-width: 350px;
      width: 100%;
      padding: 12px 6px;
      font-size: 14px;
      border: none;
      border-radius: 5px;
      &:focus {
        outline: none;
      }
    }
    .button {
      border-top-left-radius: 0;
      border-bottom-left-radius: 0;
    }
  }
  .movies {
    padding: 32px 16px;
    .movies-grid {
      display: grid;
      column-gap: 32px;
      row-gap: 64px;
      grid-template-columns: 1fr;
      @media (min-width: 500px) {
        grid-template-columns: repeat(2, 1fr);
      }
      @media (min-width: 750px) {
        grid-template-columns: repeat(2, 1fr);
      }
      @media (min-width: 1100px) {
        grid-template-columns: repeat(4, 1fr);
      }
      .movie {
        position: relative;
        display: flex;
        flex-direction: column;
        .movie-img {
          position: relative;
          overflow: hidden;
          &:hover {
            .overview {
              transform: translateY(0);
            }
          }
          img {
            display: block;
            width: 100%;
            height: 100%;
          }
          .review {
            position: absolute;
            top: 0;
            left: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            width: 40px;
            height: 40px;
            background-color: #c92502;
            color: #fff;
            border-radius: 0 0 16px 0;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1),
              0 2px 4px -1px rgba(0, 0, 0, 0.06);
          }
          .overview {
            line-height: 1.5;
            position: absolute;
            bottom: 0;
            background-color: rgba(201, 38, 2, 0.9);
            padding: 12px;
            color: #fff;
            transform: translateY(100%);
            transition: 0.3s ease-in-out all;
          }
        }
        .info {
          margin-top: auto;
          .title {
            margin-top: 8px;
            color: #fff;
            font-size: 20px;
          }
          .release {
            margin-top: 8px;
            color: #c9c9c9;
          }
          .button {
            margin-top: 8px;
          }
        }
      }
    }
  }
}
</style>