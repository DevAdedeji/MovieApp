<template>
    <section>
        <loading v-if="$fetchState.pending"></loading>
        <div v-else class="container single-movie">
        <NuxtLink :to="{name:'index'}" class="button">
            Back
        </NuxtLink>
        <div class="movie-info">
            <div class="movie-img">
                <img :src="`https://image.tmdb.org/t/p/w500/${movie.poster_path}`" alt="">
            </div>
            <div class="movie-content">
                <h1>Title: {{movie.title}}</h1>
                <p class="movie-fact tagline">
                    <span>TagLine:</span>"{{movie.tagline}}"
                </p>
                <p class="movie-fact">
                    <span>Released:</span>
                   {{
              new Date(movie.release_date).toLocaleDateString()
            }}
                </p>

                <p class="movie-fact">
                    <span>Duration:</span>
                    {{movie.runtime}} minutes
                </p>

                <p class="movie-fact">
                    <span>Revenue:</span>
                    {{
                        movie.revenue.toLocaleString('en-us',{
                            style: 'currency',
                            currency: 'USD',
                        })
                    }}
                </p>

    <p class="movie-fact">
        <span>Overview: </span>
        {{movie.overview}}
    </p>
            </div>
        </div>
        </div>
    </section>
</template>

<script>
import axios from 'axios'
import Loading from '../../components/Loading.vue'
export default {
  components: { Loading },
    data(){
        return{
           movie: null,
        }
    },
    async fetch(){
        await this.getMovie()
    },
    methods:{
        async getMovie(){
            const data = await axios.get(`https://api.themoviedb.org/3/movie/${this.$route.params.id}?api_key=804a74ac9a1ab71039c990f01ef39b23&page=1`)
            this.movie= data.data
        }
    }
}
</script>

<style lang="scss" scoped>
.single-movie {
  color: #fff;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  padding: 32px 16px;
  .button {
    align-self: flex-start;
    margin-bottom: 32px;
  }
  .movie-info {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 32px;
    color: #fff;
    @media (min-width: 800px) {
      flex-direction: row;
      align-items: flex-start;
    }
    .movie-img {
      img {
        max-height: 500px;
        width: 100%;
        @media (min-width: 800px) {
          max-height: 700px;
          width: initial;
        }
      }
    }
    .movie-content {
      h1 {
        font-size: 56px;
        font-weight: 400;
      }
      .movie-fact {
        margin-top: 12px;
        font-size: 20px;
        line-height: 1.5;
        span {
          font-weight: 600;
          text-decoration: underline;
        }
      }
      .tagline {
        font-style: italic;
        span {
          font-style: normal;
        }
      }
    }
  }
}
</style>